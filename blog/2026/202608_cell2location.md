## 前言
最近在测试 cell2location，现对相关内容进行整理。

## 原理
step1.基于注释的单细胞转录组数据通过 RegressionModel 负二项回归学习各细胞类型的表达特征，校正单细胞批次效应。
step2.将细胞类型特征导入 Cell2location 分层贝叶斯模型，基于负二项分布对空间转录组每个 spot 的基因计数进行分解，估计每个空间位点各类细胞的绝对丰度；模型同时校正位点间 RNA 捕获效率差异以及游离 mRNA 背景噪声。

## 使用
```
source activate cell2loc_env2

python /SGRNJ06/randd/USER/wangjingshen/bioinfo_tools/projects/space/cell2location/scripts/pipeline.py \
     --sc_ref /SGRNJ06/randd/USER/wangjingshen/bioinfo_tools/projects/space/cell2location/test/test/tutorial/data/sc/sc.h5ad \
     --space_dir /SGRNJ06/randd/USER/wangjingshen/bioinfo_tools/projects/space/cell2location/test/test/tutorial/data/space/ \
     --spname Human_Lymph_Node \
     --sc_max_epochs 250 \
     --N_cells_per_location 30 \
     --detection_alpha 20 \
     --space_max_epochs 30000 \
     --labels_key Subset \
     --batch_key Sample \
     --categorical_covariate_keys 'Method' \
     --cell_count_cutoff 5 \
     --cell_percentage_cutoff2 0.03 \
     --nonz_mean_cutoff 1.12 \
     --step sc_train,space_train,plot
```

## 参数
--sc_ref                        单细胞参考数据的h5ad

--space_dir                     空转 celescope 分析路径

--spname                        样本名

--labels_key                    单细胞参考 h5ad 中细胞变量名, 默认为 cluster

--sc_max_epochs                 单细胞参考回归模型的最大迭代轮数, 依据损失收敛情况执行早停策略, 默认为 250

--batch_key                     单细胞参考数据中用于校正批次效应的元数据列名, 默认为 None

--categorical_covariate_keys    单细胞参考里分类协变量名, 默认为 None

--cell_count_cutoff             细胞计数过滤阈值, 默认为 5

--cell_percentage_cutoff2       细胞占比过滤阈值, 默认为 0.03

--nonz_mean_cutoff              非零表达均值阈值, 默认为 1.12

--N_cells_per_location          每个 spot 的细胞数, 根据新格元空转产品实验经验, 默认为 3

--detection_alpha               RNA 捕获效率正则化系数, 值越大正则越强, 默认为 20

--space_max_epochs              空间反卷积主模型最大迭代轮数, 默认为 30000

--step                          分析执行步骤, 默认为 sc_train,space_train,plot



## 结果

1.结果目录
<div align='left'>
      <img src="https://github.com/wangjingshen/blog/blob/master/image/2026/202608_cell2location/cell2location_outs.png"
      alt="Editor" width = "300">
</div>


2.每个 spot 里面该细胞类型的估计绝对细胞数量

spot 是混合位置, 数值可以小于 1, 小数是模型估计期望细胞数, 不是真实物理计数。

<div align='left'>
      <img src="https://github.com/wangjingshen/blog/blob/master/image/2026/202608_cell2location/cell2location_cell_abundance.png"
      alt="Editor" width = "600">
</div>


3.分细胞类型展示估计绝对细胞数量
<div align='left'>
      <img src="https://github.com/wangjingshen/blog/blob/master/image/2026/202608_cell2location/cell2location_multi_panel.png"
      alt="Editor" width = "800">
</div>


4.每个 spot 取最高绝对细胞数量的细胞类型
<div align='left'>
      <img src="https://github.com/wangjingshen/blog/blob/master/image/2026/202608_cell2location/cell2location_max_cell_type.png"
      alt="Editor" width = "500">
</div>


## reference
1.https://cell2location.readthedocs.io/en/latest/index.html

2.https://github.com/BayraktarLab/cell2location

3.Kleshchevnikov V, et al. Cell2location maps fine-grained cell types in spatial transcriptomics. Nat. Biotechnol. 2022;40:661–671. doi: 10.1038/s41587-021-01139-4. 