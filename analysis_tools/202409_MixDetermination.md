## 前言
实验同事经常使用人、鼠混合细胞系进行测试，因此产生对下机数据进行物种拆分及分别注释的需求，现整理相关内容如下。

## 解决方案
step1：基于矩阵中不同物种的基因计算各物种的UMI比例，特定物种 UMI 比例高于预设的阈值（例如 0.9），判定该细胞属于特定物种；

step2：根据物种判定结果，对矩阵进行拆分并去除不属于对应物种的基因，再根据此前写的 [Annotation](202407_Annotation.md) 完成注释需求。

## 脚本
/SGRNJ06/randd/USER/wangjingshen/script/mix_determination/script/mix_determination.R

#### 主要参数
--matrix_10X ： 矩阵位置

--matrix_10X_gene ： 矩阵中基因名文件位置

--percent_threshold ： UMI比例判定阈值，默认为0.9

--outdir ： 输出目录，默认为outdir

--name ： 样本名

#### 输出
1.{name}_species.tsv, 物种判定文件；
<div align='left'>
      <img src="https://github.com/wangjingshen/blog_dev/blob/main/image/202409_MixDetermination/test_species.png"
      alt="Editor" width = "350">
</div>

2.{name}_human, 人的矩阵；

3.{name}_mouse, 鼠的矩阵。

<div align='left'>
      <img src="https://github.com/wangjingshen/blog_dev/blob/main/image/202409_MixDetermination/test_outdir.png" 
      alt="Editor" width = "220">
</div>

#### 测试示例
/SGRNJ06/randd/USER/wangjingshen/script/mix_determination/test/