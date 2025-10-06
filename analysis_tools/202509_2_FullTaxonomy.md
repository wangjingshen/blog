## 前言
16S项目目前输出的是属 UMI 矩阵，有些客户需要完整界门纲目科信息的矩阵，这里对这个需求进行整理, 以备后续参考。

## 输出文件补充界门纲目科属种信息
代码：/SGRNJ06/randd/USER/wangjingshen/script_dev/pathseq_full_tax/script/

示例：/SGRNJ06/randd/USER/wangjingshen/script_dev/pathseq_full_tax/test/

参数：

--pathseq_score    pathseq输出的score

--df_genus         属UMI矩阵

--name             样本名

--outdir           输出目录

原始矩阵：
<div align='left'>
      <img src="https://github.com/wangjingshen/blog/blob/master/image/202509_2_FullTaxonomy/raw_UMI_matrix.png"
      alt="Editor" width = "500">
</div>

完整信息的矩阵：
<div align='left'>
      <img src="https://github.com/wangjingshen/blog/blob/master/image/202509_2_FullTaxonomy/full_taxonomy_UMI_matrix.png"
      alt="Editor" width = "800">
</div>