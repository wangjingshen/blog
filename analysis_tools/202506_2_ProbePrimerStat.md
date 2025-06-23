## 前言
最近在统计探针和引物时，发现此前的脚本不适配 V3 beads，因此基于 celescope2.6.0 更新了探针和引物脚本，以适配 V3 beads.
<div align='left'>
      <img src="https://github.com/wangjingshen/blog/blob/master/image/202506_2_ProbePrimerStat/celescope_version.png"
      alt="Editor" width = "350">
</div>

## 测试
分析环境为 CeleScope2.6.0_probe。

1.转录组统计探针示例
```
multi_rna \
    --mapfile mapfile \
    --genomeDir /SGRNJ06/randd/public/genome/rna/celescope_v2/hs/ \
    --mod sjm \
    --probe_file /SGRNJ06/randd/PROJECT/RD20102301_DZH/P24062604_BaoNeiJun/20240716_probe/MYCO_3_probe.fasta \
    --probe_file_mode 16S
```
比常规跑转录组多提供两个参数，probe_file 为探针文件；probe_file_mode 为探针文件模式，16S（按 16S 探针名进行整合） or others。

2.富集文件统计引物示例
目前，研发同事只需要富集文库的引物检出，因此只需要跑sample，barcode两步。
```
multi_snp \
    --mapfile mapfile \
    --genomeDir /SGRNJ06/randd/public/genome/rna/celescope_v2/hs/ \
    --thread 10 \
    --mod sjm \
    --gene_list /SGRNJ06/randd/PROJECT/RD20102301_DZH/dzh_test/tsq/20231026_JMML_FJ/gene_list.tsv \
    --overlap 8 \
    --outFilterMatchNmin 80 \
    --not_consensus \
    --amp_file /SGRNJ06/randd/PROJECT/RD20102301_DZH/16S_display/primer/fasta_file/16S_primer_degenerate.fasta \
    --probe_file_mode 16S \
    --steps_run sample,barcode
```

## 结果
<div align='left'>
      <img src="https://github.com/wangjingshen/blog/blob/master/image/202506_2_ProbePrimerStat/RNA_probe_stat.png"
      alt="Editor" width = "350">
</div>

<div align='left'>
      <img src="https://github.com/wangjingshen/blog/blob/master/image/202506_2_ProbePrimerStat/fj_primer_stat.png"
      alt="Editor" width = "350">
</div>