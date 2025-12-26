## 前言
最近测试了 pixelgen 的 PNA 格式数据分析，现进行整理。

## 使用
代码：/SGRNJ06/randd/USER/wangjingshen/script_dev/pixelgen_PNA/script/analysis.R

测试示例：/SGRNJ06/randd/USER/wangjingshen/script_dev/pixelgen_PNA/test

运行示例
```
Rscript /SGRNJ06/randd/USER/wangjingshen/script_dev/pixelgen_PNA/script/analysis.R \
    --pxl /SGRNJ06/randd/USER/wangjingshen/rd_project/2025/mpx/r1/P25112507/S01_pixelgen-singleron-P25112507.layout.pxl,/SGRNJ06/randd/USER/wangjingshen/rd_project/2025/mpx/r1/P25112507/S02_pixelgen-singleron-P25112507.layout.pxl \
    --spname S01,S02 \
    --gname S01,S02 \
    --nUMI_cutoff 10000 \
    --isotype_fraction_cutoff 0.001 \
    --rm_batch no \
    --rm_batch_var spname \
    --ndims 10 \
    --resolution 0.8 \
    --outdir outdir \
```

#### 参数
```
--pxl pxl文件，以逗号分隔
--spname 样本名，以逗号分隔
--gname 组名，以逗号分隔
--nUMI_cutoff 细胞最少UMI的阈值，默认为 10000
--isotype_fraction_cutoff 同型对照抗体的最大丰度阈值，默认为 0.001
--rm_batch 是否进行去批次
--rm_batch_var 去批次的变量
--ndims 10 用于分群的主成分数
--resolution 0.8  分群的分辨率
--outdir 结果路径
```

#### 结果
<div align='left'>
      <img src="https://github.com/wangjingshen/blog/blob/master/image/202511_PixelgenPNA/pixelgen_PNA_outdir.png"
      alt="Editor" width = "300">
</div>

1.data.rds   整合 PNA 数据的 seurat对象

2.qc/molecule_rank_plot.png

使用分子排秩图对细胞calling进行质量控制，Pixelator 默认阈值为10000。如果要在样本中找到小细胞，例如血小板，则需要调整 nUMI_cutoff。

<div align='left'>
      <img src="https://github.com/wangjingshen/blog/blob/master/image/202511_PixelgenPNA/molecule_rank_plot.png"
      alt="Editor" width = "400">
</div>

3.qc/TauPlot.png

Tau测量蛋白质组成的异质性，可用于检测各种类型的异常值。Pixelator对Tau的分类为低、正常和高。一般而言，低 Tau 值的细胞可能是抗体聚集体；高 Tau 值的细胞特异性较低，可能包含比期望正常细胞更异质的蛋白质。异常值可能代表人为的技术扰动，在后续分析中会移除异常值。

<div align='left'>
      <img src="https://github.com/wangjingshen/blog/blob/master/image/202511_PixelgenPNA/TauPlot.png"
      alt="Editor" width = "400">
</div>

4.qc/isotype_fraction.png

PNA数据包括三种用于测量背景信号的同型对照抗体，与其他抗体相比，这些同型对照的丰度水平应该较低。因此，异常升高的同型对照丰度一般是低质量细胞。Pixelator 的推荐阈值为 0.1%。

<div align='left'>
      <img src="https://github.com/wangjingshen/blog/blob/master/image/202511_PixelgenPNA/isotype_fraction.png"
      alt="Editor" width = "400">
</div>

5.cluster/spname.png   样本的降维图（内部样本，不进行公开展示，测试目录可见）

6.cluster/gname.png   组的降维图（内部样本，不进行公开展示，测试目录可见）

7.cluster/seurat_clusters.png   机械分群图

<div align='left'>
      <img src="https://github.com/wangjingshen/blog/blob/master/image/202511_PixelgenPNA/seurat_clusters.png"
      alt="Editor" width = "400">
</div>