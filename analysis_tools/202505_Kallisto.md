## 前言
地球上估计有 10^31 个病毒，其中超过 3*10^5 种病毒可能引起人类疾病，但目前在人类中只有 261 种被检测到。现有的病毒检测方法依赖于参考基因组，但目前NCBI RefSeq仅提供 5,970 种核糖体病毒参考基因组，远低于实际存在的病毒种类。因此，作者扩展了 RNA 测序数据预处理工具 kallisto，在单细胞分辨率下通过将核苷酸序列与氨基酸参考序列进行翻译比对，其中，PalmDB包含296,623个独特的RdRP（RNA依赖的RNA聚合酶）氨基酸序列，代表着估计的10^8到10^12种病毒。
<div align='left'>
      <img src="https://github.com/wangjingshen/blog/blob/master/image/202505_Kallisto/kallisto.png"
      alt="Editor" width = "350">
</div>

## 测试示例
测试基于 conda 环境 sdev，测试路径为 /SGRNJ06/randd/USER/wangjingshen/rd_project/kallisto/data/r3/。测试分为两块内容，1）基于PalmDB数据库，2）基于PalmDB之外的病毒参考序列。


#### 1.PalmDB
1.1 下载 PalmDB reference files
```
wget https://raw.githubusercontent.com/pachterlab/LSCHWCP_2023/master/PalmDB/ID_to_taxonomy_mapping.csv
wget https://raw.githubusercontent.com/pachterlab/LSCHWCP_2023/master/PalmDB/palmdb_clustered_t2g.txt
wget https://raw.githubusercontent.com/pachterlab/LSCHWCP_2023/master/PalmDB/palmdb_rdrp_seqs.fa
wget https://raw.githubusercontent.com/pachterlab/LSCHWCP_2023/master/PalmDB/README.md
```

1.2 基于 PalmDB 构建 reference
```
kb ref \
    --aa \
    --d-list $(gget ref --ftp -w dna mus_musculus) \
    -i index.idx \
    --workflow custom \
    /SGRNJ06/randd/USER/wangjingshen/rd_project/kallisto/reference/PalmDB/palmdb_rdrp_seqs.fa
```
注：--d-list用于屏蔽宿主序列。在鉴定微生物序列过程中出现的一个常见问题是参考基因组数据库的跨物种污染，例如细菌基因组普遍受到人类 DNA 的污染，这可能导致将宿主读段错误地归类为细菌或病毒。因此，可以在与病毒参考比对之前删除宿主读段，以防止将宿主读段错误地归类为病毒。

1.3 比对
```
kb count \
    -i index.idx \
    -g /SGRNJ06/randd/USER/wangjingshen/rd_project/kallisto/reference/PalmDB/palmdb_clustered_t2g.txt \
    -x 0,0,9,0,25,34,0,50,59:0,60,72:1,0,0 \
    -w /SGRNJ06/randd/USER/zhouyiqi/work/analysis/kb_python/test/1769k-GEXSCOPE-V2.txt \
    --parity single \
    -o mouse790_1_FJ \
    -t 8 \
    --h5ad \
    /SGRNJ06/DATA04/23_03/2023_03_20/PN23030805/mouse790_1_FJ/2023-03-20-238/R230314015_R1.fastq.gz \
    /SGRNJ06/DATA04/23_03/2023_03_20/PN23030805/mouse790_1_FJ/2023-03-20-238/R230314015_R2.fastq.gz
```

### 2.自定义基因组
有些病毒不在PalmDB数据库中，此处，以乙肝病毒基因组为例。因为提供的是核苷酸序列，因此在构建参考基因组和比对均需要去掉--aa参数，该参数表示提供的基因组文件包含氨基酸序列。

2.1 构建 reference
```
kb ref \
    --verbose \
    -t 4 \
    -k 31 \
    --d-list $(gget ref --ftp -w dna mus_musculus) \
    -i hbv_ref.idx \
    -g hbv_t2g.txt \
    -f1 hbv_transcripts.fa \
    /OLDSGRNJ03/randd/test_rd/dxh/data/HBV_genome/HBV_NC_003977.2.fasta \
    /SGRNJ06/randd/USER/wangjingshen/project/huashan_HBV/data/2022-10-18_gtf/HBV.gtf

2.2 比对
```
kb count \
    -i /SGRNJ06/randd/USER/wangjingshen/rd_project/kallisto/data/r1/hbv_kb_ref/hbv_ref.idx \
    -g /SGRNJ06/randd/USER/wangjingshen/rd_project/kallisto/data/r1/hbv_kb_ref/hbv_t2g.txt \
    -x 0,0,9,0,25,34,0,50,59:0,60,72:1,0,0 \
    -w /SGRNJ06/randd/USER/zhouyiqi/work/analysis/kb_python/test/1769k-GEXSCOPE-V2.txt \
    --parity single \
    -o mouse790_1_ZL \
    -t 8 \
    --h5ad \
    /SGRNJ06/DATA04/23_03/2023_03_20/PN23030805/mouse790_1_ZL/2023-03-20-232/R230314009_R1.fastq.gz \
    /SGRNJ06/DATA04/23_03/2023_03_20/PN23030805/mouse790_1_ZL/2023-03-20-232/R230314009_R2.fastq.gz
```

## 流程
为了方便运行，写了一个小流程。需要提供三个参数，1）mapfile, 4列文件,依次为 library_id fq_dir sample match_dir； 2）workflow，sgr(目前仅支持) or others； 3）host_species, huamn or mouse。

```
python /SGRNJ06/randd/USER/wangjingshen/script_dev/kallisto/script/kallisto.py \
    --mapfile mapfile \
    --workflow sgr \
    --host_species human 
```

## 结果
主要是一个匹配转录组细胞的病毒UMI表，行为barcode，列为病毒（ID,rep_ID,phylum,class,order,family,genus,species,strandedness）
<div align='left'>
      <img src="https://github.com/wangjingshen/blog/blob/master/image/202505_Kallisto/kallisto_out.png"
      alt="Editor" width = "350">
</div>

## reference
1.https://www.biorxiv.org/content/10.1101/2023.12.11.571168v1.full

2.https://github.com/pachterlab/LSCHWCP_2023