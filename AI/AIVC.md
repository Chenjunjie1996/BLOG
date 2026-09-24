# AIVC：人工智能虚拟细胞（AI Virtual Cell）

> 从分子到细胞的多尺度、多模态 AI 模拟器  
> 目标：在计算机中构建可预测、可干预的“数字细胞”

**AIVC**（Artificial Intelligence Virtual Cell，人工智能虚拟细胞）是 2024 年由斯坦福、哈佛、微软研究院等 40 余位学者在 *Cell* 杂志提出的愿景框架。它旨在用大规模神经网络，从海量多组学数据中学习细胞的通用表征，从而模拟细胞在基因扰动、药物作用、环境变化下的行为，实现真正意义上的 *in silico* 实验。

核心论文：  
[How to Build the Virtual Cell with Artificial Intelligence: Priorities and Opportunities](https://doi.org/10.1016/j.cell.2024.11.015)  
（Bunne, Roohani, Leskovec, Quake, Regev 等，*Cell* 2024）

---

## 为什么需要 AIVC？

传统生物学研究高度依赖湿实验：一次基因敲除、一次药物筛选、一次成像，周期长、成本高、通量有限。

AIVC 的目标是把“细胞”变成可计算的对象：

- 观察细胞在未见过的扰动下会如何响应
- 预测疾病进展、药物副作用、分化轨迹
- 在虚拟空间中大规模探索假设，再指导真实实验

业界常把 AIVC 比作“生物学领域的下一个 AlphaFold 时刻”——从“设计零件（蛋白质）”迈向“模拟整台机器（细胞）”。

---

## 核心愿景与能力

根据 *Cell* 路线图，一个理想的 AIVC 应具备三大能力：

### 1. 通用表征（Universal Representations, URs）
将不同个体、不同条件、不同模态（转录组、表观遗传、蛋白、空间位置等）的数据映射到统一的计算空间，形成跨细胞类型、跨物种的通用细胞状态参考。

### 2. 细胞行为预测
在给定初始状态和扰动（基因编辑、小分子、环境变化）后，预测细胞的后续状态迁移，包括分化、衰老、疾病进展等动态过程，甚至外推到训练数据中未出现的状态。

### 3. 虚拟仪器（Virtual Instruments）
通过可解释的神经网络模块，在表征空间中执行类似湿实验的操作（虚拟敲除、虚拟加药、虚拟过表达），并输出可验证的预测结果。

---

## 技术架构愿景

AIVC 不是单一模型，而是一个**分层、可扩展的基础模型体系**：



关键技术要素：
- **多模态融合**：scRNA-seq、scATAC-seq、蛋白组、成像、空间转录组等
- **多尺度建模**：从分子相互作用到全细胞表型
- **状态与动态**：不仅编码静态状态，还能模拟时间演化与扰动累积效应
- **可解释性与因果**：通过虚拟仪器探索机制，而非纯黑箱预测

当前实现路径通常采用“通用表征 + 任务专用虚拟仪器”的组合，并强调开放协作与统一评测。

---

## 当前进展（截至 2026）

| 方向 | 代表性工作 / 资源 | 说明 |
|------|-------------------|------|
| **路线图** | Bunne et al., *Cell* 2024 | 正式提出 AIVC 概念与优先事项 |
| **大规模数据** | Arc Virtual Cell Atlas、Tahoe-100M | 数亿级细胞、药物扰动图谱 |
| **挑战赛** | Arc Institute Virtual Cell Challenge (2025/2026) | 零样本扰动预测评测，推动诚实评估 |
| **模型尝试** | GenBio AI AIDO Cell 等 | 多尺度、有状态的虚拟细胞原型 |
| **评测反思** | 多项 2025 基准测试 | 指出当前大模型在零样本扰动预测上仍未稳定超越简单基线 |

社区共识：数据规模与多样性、跨上下文泛化能力、以及严格的零样本评测，是当前最大瓶颈。

---

## 数据与评测的重要性

AIVC 对数据的要求远超传统单细胞分析：
- **多尺度**：分子 → 细胞 → 组织
- **多模态**：序列、结构、空间、动态
- **扰动响应**：基因敲低/敲除、药物处理、环境变化的 paired 数据
- **跨上下文**：不同细胞系、供体、物种的泛化

Arc Institute 等机构推动的 Virtual Cell Challenge 正是为了建立“虚拟细胞的图灵测试”——模型必须在从未见过的细胞背景下做出可靠预测。

---

## 快速了解资源

- **核心论文**  
  [How to Build the Virtual Cell with Artificial Intelligence](https://doi.org/10.1016/j.cell.2024.11.015)

- **社区资源汇总**  
  [Awesome Virtual Cell](https://github.com/Boom5426/Awesome-Virtual-Cell)（论文、数据集、挑战赛、讲座）

- **数据与挑战**  
  Arc Institute Virtual Cell Atlas & Challenge  
  CELLxGENE Census（CZI）

- **近期动态**  
  GenBio AI AIDO Cell（2026）  
  Virtual Cell Challenge 2026（零样本多上下文泛化）

---

## 写在最后
AIVC仍处于早期阶段。它不是一个已经“可用”的成品模型，而是一个正在被整个生命科学与 AI 社区共同定义的基础设施愿景。它的价值在于：把假设探索从湿实验的线性节奏，变成可大规模并行的计算过程；把“我猜这个基因很重要”变成“我先在虚拟细胞里跑完一万种可能，再决定做哪几个实验”。如果 AlphaFold 解决了“蛋白质怎么折”，那么 AIVC 试图回答的是——细胞作为系统，如何响应世界。这可能是未来十年计算生物学最激动人心的方向之一。

## 引用

```bibtex
@article{bunne2024virtual,
  title={How to build the virtual cell with artificial intelligence: Priorities and opportunities},
  author={Bunne, Charlotte and Roohani, Yusuf and others},
  journal={Cell},
  volume={187},
  number={25},
  pages={7045--7063},
  year={2024},
  publisher={Elsevier}
}