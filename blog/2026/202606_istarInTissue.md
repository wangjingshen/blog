## 前言
istar 基于 H&E 染色切片图进行背景识别，受限于切片图质量，背景识别效果不稳定。此外，由于切片图和有效spots并不完全吻合，又会出现 istar 结果和降维图有差别。现针对这些问题对 istar 流程进行优化。

## 解决方案
基于有效 spots 进行背景识别。

## 使用
在原流程基础上添加 foreground_method 参数，其中 in_tissue 基于 spatial 中 positions_list.csv 有效spots 进行背景识别；istar 则使用 istar 内置背景识别方法。
```
source activate istar_env

python /SGRNJ06/randd/USER/wangjingshen/bioinfo_tools/projects/2026/istar/scripts/istar.py \
    --dir /SGRNJ06/randd/USER/wangjingshen/rd_project/2026/istar/r6/celescope/PXL_NJU_ST_Lib/ \
    --image /SGRNJ06/randd/USER/wangjingshen/rd_project/2026/istar/r6/celescope/PXL_NJU_ST_Lib/outs/spatial/figure/Subcutaneous_tumor_0.05.jpg \
    --spname PXL_NJU_ST_Lib \
    --swap_pos T \
    --foreground_method in_tissue \
    --foreground_cluster_method max \
    --cluster_method km \
    --n_cluster 10 \
```


## 结果

这里以小鼠肠道数据为例

机械分群图
<div align='left'>
      <img src="https://github.com/wangjingshen/blog/blob/master/image/2026/202606_istarBackground/cluster_Intestine.jpg"
      alt="Editor" width = "300">
</div>

istar 原流程判定的背景
<div align='left'>
      <img src="https://github.com/wangjingshen/blog/blob/master/image/2026/202606_istarBackground/istar_mask_Intestine_raw.png"
      alt="Editor" width = "300">
</div>

istar 新流程判定的背景
<div align='left'>
      <img src="https://github.com/wangjingshen/blog/blob/master/image/2026/202605_IstarSpotsUpdate/istar_mask_Intestine_update.png"
      alt="Editor" width = "300">
</div>

istar 原流程分群
<div align='left'>
      <img src="https://github.com/wangjingshen/blog/blob/master/image/2026/202605_IstarSpotsUpdate/istar_cluster_Intestine_raw.png"
      alt="Editor" width = "300">
</div>

istar 新流程分群
<div align='left'>
      <img src="https://github.com/wangjingshen/blog/blob/master/image/2026/202605_IstarSpotsUpdate/istar_cluster_intestine_update.png"
      alt="Editor" width = "300">
</div>


这里以小鼠膀胱癌原位瘤数据为例，效果会更明显。

机械分群图
<div align='left'>
      <img src="https://github.com/wangjingshen/blog/blob/master/image/2026/202606_istarBackground/cluster_Subcutaneous_tumor.png"
      alt="Editor" width = "300">
</div>

istar 原流程判定的背景
<div align='left'>
      <img src="https://github.com/wangjingshen/blog/blob/master/image/2026/202606_istarBackground/istar_mask_Subcutaneous_tumor_raw.png"
      alt="Editor" width = "300">
</div>

istar 新流程判定的背景
<div align='left'>
      <img src="https://github.com/wangjingshen/blog/blob/master/image/2026/202605_IstarSpotsUpdate/istar_mask_Subcutaneous_tumor_update.png"
      alt="Editor" width = "300">
</div>

istar 原流程分群
<div align='left'>
      <img src="https://github.com/wangjingshen/blog/blob/master/image/2026/202605_IstarSpotsUpdate/istar_cluster_Subcutaneous_tumor_raw.png"
      alt="Editor" width = "300">
</div>

istar 新流程分群
<div align='left'>
      <img src="https://github.com/wangjingshen/blog/blob/master/image/2026/202605_IstarSpotsUpdate/istar_cluster_Subcutaneous_tumor_update.png"
      alt="Editor" width = "300">
</div>