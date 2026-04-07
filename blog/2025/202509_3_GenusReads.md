## 前言
16S项目目前输出的是细菌属 UMI 矩阵，有些客户需要分析特定细菌 reads 的分布情况，这里对这个需求进行整理, 以备后续参考。


## 使用说明
代码：/SGRNJ06/randd/USER/wangjingshen/script_dev/pathseq_genus_reads/script/

示例：/SGRNJ06/randd/USER/wangjingshen/script_dev/pathseq_genus_reads/test/

```
source activate r4.1_env

python /SGRNJ06/randd/USER/wangjingshen/script_dev/pathseq_genus_reads/script/pipeline.py \
  --mapfile mapfile
```
mapfile是一个6列文件，依次是celescope pathseq 分析目录，样本名，taxonomy_id和taxonomy对应文件，特定genus，匹配的RNA 分析路径， 输出目录

```
dir sample tax_id tax_name match_dir outdir
celescope_pathseq_path sample taxonomy_id.tsv Klebsiella match_rna_path outdir
```

taxonomy_id.tsv是一个两列文件，依次是tax_id，taxonomy，从 02.pathseq/{sample_name}_pathseq_score.txt 中获取。
<div align='left'>
      <img src="https://github.com/wangjingshen/blog/blob/master/image/2025/202509_3_GenusReads/Klebsiella_tax.png"
      alt="Editor" width = "400">
</div>

输出文件是一个三列文件，依次为barcode，UMI数，reads数
<div align='left'>
      <img src="https://github.com/wangjingshen/blog/blob/master/image/2025/202509_3_GenusReads/genus_UMI_reads.png"
      alt="Editor" width = "400">
</div>