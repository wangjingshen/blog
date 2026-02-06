## 前言
最近测试使用 napari 选取空转结果感兴趣的 spot，现整理相关内容如下。

## 使用

分析环境：napari_env

### step1 celscope space 结果 转成 napari 的输入数据（zarr）
```
python /SGRNJ06/randd/USER/wangjingshen/script_dev/space_napari/script/h5_to_zarr.py \
    --space_dir /SGRNJ06/randd/PROJECT/R25030501_Spatial_FFPE_tgx/20251127/Mint_FFPE_96_96_1119/ \
    --sample Mint_FFPE_96_96_1119 \
    --outdir napari_input 
```

输出的 zarr 目录
dataset.zarr

├── images/hires        高分辨率 H&E 或荧光图，这里取空转数据里的高分辨图（tissue_hires_image.png）

├── shapes/spots        parquet格式，存储每个 spot 的坐标

├── tables/adata        AnnData，表达矩阵（X）、obs、var、obsm、uns...

└── zmetadata           元数据 JSON 文件，记录所有数组的 chunk 大小、压缩方式、维度名

注：
Parquet 是一种专为大数据处理系统优化的列式存储文件格式，由 Twitter 和 Cloudera 于 2013 年共同创建。与传统的基于行存储的格式（如 CSV 和 JSON）相比，Parquet 文件格式具有一系列优势：1）通过列式存储数据，Parquet 可以提高查询性能，尤其是对涉及汇总或过滤大量数据的分析工作负载；2）Parquet 的先进压缩和编码技术有助于降低存储成本同时保持高读写性能。可以使用 hexdump 查看数据文件。

<div align='left'>
      <img src="https://github.com/wangjingshen/blog/blob/master/image/202601_SpaceNapari/parquet.png"
      alt="Editor" width = "400">
</div>

当数据量较小时，Parquet 会大于 CSV 和 JSON，因为 Parquet 会存储额外的信息，包括特定行组内特定列中的最小值和最大值等信息。此时，查询性能可能会降低，因此一般建议生成的 Parquet 文件在上百 MB 时使用。

<div align='left'>
      <img src="https://github.com/wangjingshen/blog/blob/master/image/202601_SpaceNapari/data_size.png"
      alt="Editor" width = "300">
</div>


### step2 使用 napari 选取感兴趣的区域
服务器打开 napari 有点问题，这一步在本地进行，准备步骤代码参照 https://github.com/wangjingshen/script_dev/blob/main/space_napari/script/napari.py

napari操作步骤如下图所示
<div align='left'>
      <img src="https://github.com/wangjingshen/blog/blob/master/image/202601_SpaceNapari/napari_step.png"
      alt="Editor" width = "400">
</div>

最终结果如下图
<div align='left'>
      <img src="https://github.com/wangjingshen/blog/blob/master/image/202601_SpaceNapari/napari_select.png"
      alt="Editor" width = "400">
</div>

## reference
1.https://spatialdata.scverse.org/en/stable/tutorials/notebooks/notebooks/examples/napari_rois.html