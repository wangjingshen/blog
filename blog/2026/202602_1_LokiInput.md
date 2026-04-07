## 前言
最近在测试loki，loki会对空间转录组的矩阵和图像分别进行编码，官方各模块的文档未提供具体的输入文件构建代码。现根据作者提供的通用输入文件构建示例进行测试，矩阵编码没有太大问题，而图像编码存在问题，该步骤需要先按 spot 进行切割然后再进行编码，默认结果会按 spot 名称字母排序，需要根据 h5 文件里的排序进行修正。这里把 loki 输入文件构建整理成脚本，以便后续样本进行 loki 各模块的测试。


## 解决思路
步骤1：将空转 celescope 的分析目录转成 loki 的输入目录；

步骤2：对矩阵和图像分别进行编码；

步骤3：在空转数据上对 OmiCLIP 模型进行微调，再对矩阵和图像分别进行编码。


## 使用
分析环境：loki_env

测试示例
```
python /SGRNJ06/randd/USER/wangjingshen/script/loki/script/preprocess.py \
    --dir /SGRNJ06/randd/PROJECT/R25030501_Spatial_FFPE_tgx/20251127/Mint_FFPE_96_96_1119/ \
    --spname Mint_FFPE_96_96_1119 \
    --hk_genes /SGRNJ06/randd/USER/wangjingshen/script/loki/data/housekeeping_genes_mus.csv \
    --sc /SGRNJ06/randd/USER/wangjingshen/rd_project/2026/loki/r2/decompose/input/musculus_intestine_subset.h5ad

```

参数
```
--dir       空转 celescope 分析目录   
--spname    样本名
--hk_genes  管家基因
--sc        相同物种组织已注释的单转数据
--step      要分析的步骤, 默认为 input,loki_encode,loki_finetune_encode
```

## 结果
1.space_input 为 input 步骤生成的文件夹，相比 celescope 分析目录新增了 image_coord.csv 和 valid_spots.h5ad 文件，h5ad 文件由 h5 文件转换而来并强制要求在切片图上（部分 10X 公开数据存在切片图外的 spot，见下图）；

<div align='left'>
      <img src="https://github.com/wangjingshen/blog/blob/master/image/2026/202602_LokiInput/10X_brain_spots.png"
      alt="Editor" width = "500">
</div>

2.loki_input 为 loki_encode 步骤生成的文件夹，同一前缀的是针对不同 loki 模块的输入文件，存储的都是编码文件，带header的首行为barcode；

3.loki_input_finetune 为 loki_finetune_encode 步骤生成的文件夹；

<div align='left'>
      <img src="https://github.com/wangjingshen/blog/blob/master/image/2026/202602_LokiInput/loki_out.png"
      alt="Editor" width = "300">
</div>

## reference
1.https://github.com/GuangyuWangLab2021/Loki
