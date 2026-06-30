# Scanpy 手动构建 Spatial AnnData（ATAC Peak Matrix）及常见问题排查

## 背景

对于 Spatial Transcriptomics 数据，可以直接使用 `scanpy.read_visium()` 读取 Space Ranger 的输出。

但是对于 Spatial ATAC 或其他非标准 Visium 数据，通常只有如下目录结构：

```text
project/
├── peak_count.mtx
├── barcodes.tsv
├── peaks.bed
└── spatial/
    ├── tissue_positions_list.csv
    ├── tissue_hires_image.png
    ├── tissue_lowres_image.png
    └── scalefactors_json.json
```

由于不存在标准的 Gene Expression Matrix，`scanpy.read_visium()` 会报错，因此需要手动构建带有空间信息的 `AnnData`。

---

## 一、读取 ATAC Peak Matrix

```python
import scanpy as sc
import pandas as pd

adata = sc.read_mtx("peak_count.mtx").T

adata.obs_names = pd.read_csv(
    "barcodes.tsv",
    header=None
)[0].values

adata.var_names = pd.read_csv(
    "peaks.bed",
    sep="\t",
    header=None
).apply(
    lambda x: f"{x[0]}:{x[1]}-{x[2]}",
    axis=1
)
```

得到的数据维度为：

```text
(n_spots, n_peaks)
```

---

## 二、读取 Spatial 坐标

读取 `tissue_positions_list.csv`：

```python
coords = pd.read_csv(
    "tissue_positions_list.csv",
    header=None
)

coords.columns = [
    "barcode",
    "in_tissue",
    "array_row",
    "array_col",
    "pxl_row",
    "pxl_col",
]

coords = coords.set_index("barcode")
```

仅保留共同 barcode：

```python
common = adata.obs_names.intersection(coords.index)

adata = adata[common]
coords = coords.loc[common]
```

---

## 三、写入空间坐标

Scanpy 要求空间坐标保存在：

```python
adata.obsm["spatial"]
```

二维坐标写入方式如下：

```python
adata.obsm["spatial"] = coords[
    ["pxl_col", "pxl_row"]
].values
```

注意坐标顺序必须为：

```text
[x, y] = [col, row]
```

不要写成：

```text
[row, col]
```

否则图像会旋转或镜像。

---

## 四、加载图片

```python
from PIL import Image
import numpy as np

hires = np.array(
    Image.open("tissue_hires_image.png")
)

lowres = np.array(
    Image.open("tissue_lowres_image.png")
)
```

---

## 五、加载 scalefactor

```python
import json

with open("scalefactors_json.json") as f:
    scalefactors = json.load(f)
```

例如：

```json
{
  "tissue_hires_scalef": 0.170111,
  "tissue_lowres_scalef": 0.051033,
  "spot_diameter_fullres": 89.4
}
```

---

## 六、构建 `adata.uns["spatial"]`

```python
library_id = "sample1"

adata.uns["spatial"] = {
    library_id: {
        "images": {
            "hires": hires,
            "lowres": lowres
        },
        "scalefactors": scalefactors,
        "metadata": {}
    }
}
```

至此即可使用：

```python
sc.pl.spatial(...)
```

或：

```python
sq.pl.spatial_scatter(...)
```

---

## 七、常见问题

### 1. 背景图显示为绿色

#### 现象

整个组织背景变成绿色，而不是正常的 HE 图。

#### 原因

检查：

```python
print(hires.shape)
```

如果输出：

```text
(2000, 2000)
```

说明图片是单通道灰度图，而不是标准 RGB 图片。

标准 Visium 图片应为：

```text
(2000, 2000, 3)
```

或：

```text
(2000, 2000, 4)
```

Matplotlib 对二维数组默认使用 `viridis` colormap，因此灰度图会被渲染成紫—绿—黄渐变，看起来背景呈绿色。

#### 解决方法一：转换为 RGB（推荐）

```python
hires = Image.open(
    "tissue_hires_image.png"
).convert("RGB")

lowres = Image.open(
    "tissue_lowres_image.png"
).convert("RGB")

hires = np.array(hires)
lowres = np.array(lowres)
```

这是最推荐的方法。

#### 解决方法二：灰度复制为 RGB

```python
if hires.ndim == 2:
    hires = np.stack([hires] * 3, axis=-1)

if lowres.ndim == 2:
    lowres = np.stack([lowres] * 3, axis=-1)
```

#### 如何判断图片类型

```python
img = Image.open("tissue_hires_image.png")

print(img.mode)
```

常见结果如下：

| mode | 含义      |
| ---- | ------- |
| RGB  | 彩色图     |
| RGBA | 带透明通道   |
| L    | 灰度图     |
| 1    | 二值 Mask |

如果：

```text
mode = L
```

说明不是标准 Visium HE 图。

---

### 2. Spot 落在组织外面

#### 现象

大量 spot 明显位于组织边界之外。

通常不是绘图问题，而是空间坐标与背景图不在同一坐标系。

#### 原因一：坐标未使用正确的缩放比例（最常见）

Space Ranger 提供的：

```text
pxl_row
pxl_col
```

属于 Full Resolution Pixel Coordinate。

如果图像经过缩放，而坐标未同步处理，就会出现偏移。

应确认 `tissue_hires_scalef` 的使用方式与所采用的坐标一致，并保证图像与坐标使用相同的坐标体系。

#### 原因二：背景图不是原始 HE 图

如果：

```text
tissue_hires_image.png
```

实际上是：

* tissue mask
* segmentation mask
* binary mask

那么组织边界本身就与 HE 图不同，视觉上容易误认为 Spot 偏移。

可通过以下方式判断：

```python
print(img.mode)
print(np.unique(np.array(img))[:20])
```

例如：

```text
L
[0 255]
```

通常表示二值组织掩膜。

#### 原因三：Barcode 顺序未正确对应

应保证：

```python
coords = coords.loc[adata.obs_names]
```

而不是仅依赖集合交集，否则坐标可能与 Spot 顺序不一致。

#### 原因四：手动设置 crop 导致坐标错位

例如：

```python
crop_coord=(0, w/scale, 0, h/scale)
```

若与 `adata.obsm["spatial"]` 所采用的坐标体系不一致，也会导致 Spot 漂移。

建议在确认坐标正确之前，不要自行修改 `crop_coord`。

---

## 八、建议

对于 Spatial ATAC，建议尽量保持与 `scanpy.read_visium()` 相同的数据结构：

* `adata.obsm["spatial"]`
* `adata.uns["spatial"]`
* `scalefactors_json.json`
* RGB 格式的组织图像
* 坐标与图像使用一致的坐标体系

这样几乎所有基于 Scanpy 或 Squidpy 的空间分析函数都可以直接使用，而无需额外修改绘图代码。

---

## 总结

手动构建 Spatial AnnData 的核心在于三个部分：

1. **空间坐标**：正确写入 `adata.obsm["spatial"]`，保证坐标顺序为 `[pxl_col, pxl_row]`。
2. **空间元数据**：构建符合 `scanpy.read_visium()` 规范的 `adata.uns["spatial"]`。
3. **图像与坐标一致性**：确保使用 RGB 图像、正确的 `scalefactors`，并保证图像和坐标属于同一坐标系。

在实际开发中，最常见的问题包括绿色背景（灰度图被自动应用 colormap）、Spot 偏移（坐标与图像缩放不一致）、以及使用组织 Mask 替代 HE 图导致的显示异常。按照上述方法逐项检查，通常可以快速定位并解决问题。
