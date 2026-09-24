## 前言
最近研发有多例空转样本需要注释，现针对该需求进行整理。

## 解决方案
分析主要基于 seurat 包[1]

#### 方法1 seurat 标签转移

step1 CCA 对齐：将参考数据集（有标签）和查询数据集（无标签）投影到共享低维空间，消除批次差异，让同源细胞重合；

step2 MNN 找锚点：在共享空间中，寻找互相最近邻细胞对，作为跨数据集的可信匹配锚点；

step3 KNN 加权投票预测标签：对 query 每个细胞，根据邻近 ref 锚点的细胞类型、距离权重投票，得出最优细胞类型和置信分数。


#### 方法2 seurat RCTD 解卷积

step1 构建参考谱：从注释好的单细胞 ref，计算每种细胞类型的平均基因表达特征谱；

step2 平台效应校正：校正 scRNA 与空间 ST 不同测序平台的捕获效率差异；

step3 泊松混合模型：假设每个 spot 的 UMI 计数，是多种细胞类型表达谱按未知比例线性叠加；用最大似然估计求解每个细胞类型占比PMC；

step4 状态判定：根据拟合结果区分：singlet：主要一种细胞；doublet：两种细胞混合；reject：模型拟合很差，不可信 spot。


#### 方法3 当作单转注释，适用于没有单细胞参考数据集的备选方案。


## 使用
```
source activate r4.1_env

python /SGRNJ06/randd/USER/wangjingshen/bioinfo_tools/projects/space/space_anno/scripts/pipeline.py \
    --space_dir /SGRNJ06/randd/PROJECT/IR_group/SC_Spatial-transcriptome/0604_Mus_OCT_Nlib/kidney/JC_1/0604_Mus_Kidney_OCT_Nlib/ \
    --sc /SGRNJ07/Standard_Analysis/celelens2local/202606291726_RD24012902_B1/majordataset/RD24012902_B1_major_dataset.rds \
    --score_filter 0 \
    --name Mus_Kidney \
```

## 参数
--space_dir      空转 celescope 目录

--sc             单细胞参考 rds

--score_filter   预测置信度阈值，预测得分低于这个阈值的 spot 标记成 Unassigned，不分配细胞类型, 默认为0

--image_alpha    HE 图的透明度, 默认为 0.5

--resolution     分辨率, 默认为 0.3

--name           样本名


## 结果

以小鼠肠道数据为例:

1 机械分群图
<div align='left'>
      <img src="https://github.com/wangjingshen/blog/blob/master/image/2026/202607_spaceAnno/space_seurat_clusters.png"
      alt="Editor" width = "500">
</div>

2 单细胞转录组注释
<div align='left'>
      <img src="https://github.com/wangjingshen/blog/blob/master/image/2026/202607_spaceAnno/sc_clusters.png"
      alt="Editor" width = "500">
</div>

3 seurat 标签转移注释图
<div align='left'>
      <img src="https://github.com/wangjingshen/blog/blob/master/image/2026/202607_spaceAnno/space_clusters.png"
      alt="Editor" width = "500">
</div>

4 seurat RCTD 解卷积注释图
<div align='left'>
      <img src="https://github.com/wangjingshen/blog/blob/master/image/2026/202607_spaceAnno/space_RCTD_clusters.png"
      alt="Editor" width = "500">
</div>


## reference
1.https://satijalab.org/seurat/articles/spatial_vignette