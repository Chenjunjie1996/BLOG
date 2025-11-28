## LSI vs. PCA：单细胞分析中两大降维方法的核心区别
单细胞数据维度极高，降维是分析流程中极为关键的一步。但不同类型的数据需要不同的降维方法——这就是 LSI（Latent Semantic Indexing） 和 PCA（Principal Component Analysis） 在单细胞分析中扮演的两个角色。

## PCA：连续表达数据的“几何压缩”
PCA 是最经典、最常见的降维方法，通过线性变换找出数据中方差最大的方向（主成分）。

PCA 的逻辑非常适合:
- 连续数值;
- 近似服从高斯分布;
- 变量间具有线性相关性.

### 为什么 PCA 适合 scRNA-seq 的表达矩阵（log-normalized counts）:
- 连续数值;
- 存在生物学驱动的协同表达;
- 主成分能很好捕捉主要生物学信号（细胞类型、状态、周期等）.

## LSI：稀疏、二值化数据的“语义挖掘者”
LSI 原本来自文本挖掘

在自然语言处理（NLP）中，LSI 用于处理“词频矩阵”：
- 大量零值;
- 高频词与低频词差异巨大;
- 不同文档的复杂语义关系隐藏在稀疏矩阵的结构中.

### 为什么 scATAC 不能直接用 PCA？
ATAC 的 PEAK 矩阵有几个特点：
- 极度稀疏（95%+ 是 0）
- 类似二分类（可及/不可及）
- 高频 peak（如 housekeeping）会淹没真实差异
- 技术噪声远大于生物信号
这样的数据直接 PCA 会让结果被“高频垃圾峰”主导，无法反映细胞差异。LSI 本质是：TF-IDF 标准化 + SVD 分解（类似 PCA but more robust）。TF-IDF会降低垃圾高频peak的权重，放大特异peak的信息，更适合稀疏、离散数据。因此 LSI 是 scATAC-seq 的标准降维方法。

## 实际使用场景
### 用 PCA 的典型场景
- scRNA-seq
- 多模态 RNA + ATAC 中的 RNA 部分
- 基因表达矩阵分析（bulk 或 pseudo-bulk）
### 用 LSI 的典型场景
- scATAC-seq
- chromatin accessibility 数据
- peak × cell 极度稀疏的矩阵
- 单细胞多模态（RNA + ATAC）中的 ATAC 模块
