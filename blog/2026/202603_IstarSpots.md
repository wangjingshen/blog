## 前言
istar目前输出的 cluster 是基于连续图像的，无法提供spot信息，现写脚本实现 istar 分群映射到 spot 的功能。


## 解决思路
步骤1：坐标对齐与缩放

读取spots位置文件（包含barcode、tissue标志、行列、像素坐标），只保留组织内spots（in_tissue == 1），将spots坐标线性缩放到[0, 1008]范围，匹配 istar 图像尺寸;

步骤2：构建iStar索引

提取iStar中非背景的像素坐标，用这些像素构建KDTree，用于快速最近邻搜索

步骤3：Spot到Cluster的映射

对每个spot，在iStar有效像素中查找k个最近邻，只保留距离小于阈值（distance_thresh）的邻居，取这些邻居中出现频率最高的 cluster 作为该 spot 的归属；

步骤4：结果输出

可视化：生成两种散点图，spots图（istar2spots.png），叠加 istar 分群图的 spots 图（istar2spots_bg.png）；

数据表：保存每个barcode对应的iStar cluster编号的 csv 表；


## 使用

分析环境：istar_env

分析示例
```
python /SGRNJ06/randd/USER/wangjingshen/script_dev/istarSpots/script/pipeline.py \
    --istar_labels /SGRNJ06/randd/USER/wangjingshen/rd_project/2026/istar/r5_raw/YWL_NJU_ST_Lib/outs/clusters-gene/labels.pickle \
    --dir /SGRNJ06/randd/PROJECT/R25030501_Spatial_FFPE_tgx/20260318_tumor/YWL_NJU_ST_Lib/ \
    --spname YWL_NJU_ST_Lib \
    --k 5 \
    --distance_thresh 200 \
```

参数
```
--istar_labels         istar 的 分群 pickle 文件
--dir                  空转 celescope 分析目录 
--spname               样本名
--k                    k 个 istar 像素, 默认为 3
--distance_thresh      spot 和 istar 像素的距离阈值, 默认为 200
--clip                 是否去除超出istar图区域的spot
```

结果

istar_labels.png （istar 流程结果，用于验证）
<div align='left'>
      <img src="https://github.com/wangjingshen/blog/blob/master/image/2026/202603_IstarSpots/istar_labels.png"
      alt="Editor" width = "300">
</div>


istar2spots.png （istar 映射到 spot 的图）
<div align='left'>
      <img src="https://github.com/wangjingshen/blog/blob/master/image/2026/202603_IstarSpots/istar2spots.png"
      alt="Editor" width = "300">
</div>


istar2spots_bg.png （istar 映射到 spot 的图，并叠加在 istar 的分群结果上）
<div align='left'>
      <img src="https://github.com/wangjingshen/blog/blob/master/image/2026/202603_IstarSpots/istar2spots_bg.png"
      alt="Editor" width = "300">
</div>


istar_clusters_seurat.png （seurat 展示 istar 映射到 spot 的图）
<div align='left'>
      <img src="https://github.com/wangjingshen/blog/blob/master/image/2026/202603_IstarSpots/istar_clusters_seurat.png"
      alt="Editor" width = "400">
</div>


记录 barcode 的 istar分群的 csv 文件
<div align='left'>
      <img src="https://github.com/wangjingshen/blog/blob/master/image/2026/202603_IstarSpots/istar_cluster_df.png"
      alt="Editor" width = "200">
</div>


## 小结
空转样本切片图的大小不一，脚本效果还不太稳定，后续会进行更新。