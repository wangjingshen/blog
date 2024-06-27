## 前言

这段时间在学习 INVADEseq，其中16S 数据的分析主要基于 gatk PathSeq，现整理 PathSeq 相关内容如下。


## 背景

许多人类疾病是由病原体引起的，受感染的组织同时含有人类和微生物的核酸，PathSeq可以排除宿主序列后再检测微生物的序列。由于其不需要从头开始了解微生物的序列信息，所以这种无偏倚的微生物检测方法比靶向 PCR 或全微生物阵列方法有所进步 [1]。

![image](https://github.com/wangjingshen/blog/blob/master/image/202406_PathSeq/PathSeq_pipeline.png)


## 宿主和微生物的参考

运行 PathSeq 需要提前准备好宿主和微生物的参考。目前，gatk官方提供了人类宿主和病毒的参考[2]，这部分可以用gsutil进行下载。
```
gsutil -m cp -r "gs://gatk-best-practices/pathseq/resources" .
```
其中，人类宿主参考为pathseq_host，相应的文件如下：

| file | description |
| :--- | :--- |
| pathseq_host.fa | human reference nucleotide sequence FASTA file | 
| pathseq_host.fa.fai | reference index | 
| pathseq_host.dict | reference dictionary |
| pathseq_host.fa.img | BWA-MEM index image of the reference |
| pathseq_host.bfi | Bloom filter of host reference k-mers |

病毒参考为pathseq_microbe，相应的文件如下：

| file | description |
| :--- | :--- |
| pathseq_microbe.fa | microbe reference sequences |
| pathseq_microbe.fa.fai | reference index |
| pathseq_microbe.dict | reference dictionary |
| pathseq_microbe.fa.img | reference BWA-MEM index image file |
| pathseq_taxonomy.db | PathSeq taxonomy file corresponding to the microbe reference |

小鼠的宿主参考需要自行构建，示例代码如下：

```
wget -c http://igenomes.illumina.com.s3-website-us-east-1.amazonaws.com/Mus_musculus/UCSC/mm10/Mus_musculus_UCSC_mm10.tar.gz &
tar -zxvf Mus_musculus_UCSC_mm10.tar.gz

cd Mus_musculus/UCSC/mm10/Sequence/Chromosomes/
cat chr1.fa chr2.fa chr3.fa chr4.fa chr5.fa chr6.fa chr7.fa chr8.fa chr9.fa chr10.fa chr11.fa chr12.fa chr13.fa chr14.fa chr15.fa chr16.fa chr17.fa chr18.fa chr19.fa chrM.fa chrX.fa chrY.fa > mm10.fa

samtools faidx mm10.fa

gatk CreateSequenceDictionary -R mm10.fa &
gatk BwaMemIndexImageCreator -I mm10.fa &
gatk PathSeqBuildKmers --reference mm10.fa --bloom-false-positive-probability 0.001 --kmer-size 31 --kmer-mask 15 -O mm10.bfi --java-options "-Xmx48G" &
```


## 测试

#### 运行

考虑到后续的流程开发，这里基于celescope进行PathSeq的测试，用到的conda环境有 js_invadeseq 和 celescope2.0.7。

step1：先对下机的16S富集样本跑celescope rna流程，参考基因组设置为对应转录组的物种（宿主），运行需要添加参数 --STAR_param "--outSAMunmapped Within" 使输出的bam里包含未比对到宿主的reads。

```
source activate celescope2.0.7

multi_rna \
      --mapfile test.mapfile \
      --genomeDir /SGRNJ06/randd/public/genome/rna/celescope_v2/mmu/ \
      --STAR_param "--outSAMunmapped Within"
```

step2：对生成的 bam 进行格式修复，添加 read group (@RG) [3]。

```
picard AddOrReplaceReadGroups \
    INPUT=test/outs/test_Aligned.sortedByCoord.out.bam \
    OUTPUT=test_addRG.bam \
    RGID=test \
    RGLB=test_library \
    RGPL=illumina \
    RGPU=test20 \
    RGSM=test_sample \
```
注：如果不进行这一步，PathSeq可以跑通并且不报错，但是生成的是空白结果。

step3：运行PathSeqPipelineSpark[4]。

```
gatk PathSeqPipelineSpark \
    --input test_addRG.bam \
    --filter-bwa-image /SGRNJ06/randd/USER/wangjingshen/rd_project/invadeseq/reference/pathseq_mm10/mm10.fa.img \
    --kmer-file /SGRNJ06/randd/USER/wangjingshen/rd_project/invadeseq/reference/pathseq_mm10/mm10.bfi \
    --min-clipped-read-length 70 \
    --microbe-bwa-image /SGRNJ06/randd/USER/wangjingshen/rd_project/invadeseq/reference/pathseq_microbe/pathseq_microbe.fa.img \
    --microbe-dict  /SGRNJ06/randd/USER/wangjingshen/rd_project/invadeseq/reference/pathseq_microbe/pathseq_microbe.dict \
    --taxonomy-file /SGRNJ06/randd/USER/wangjingshen/rd_project/invadeseq/reference/pathseq_microbe/pathseq_taxonomy.db \
    --divide-by-genome-length true \
    --java-options "-Xmx48G" \
    --tmp-dir /SGRNJ06/randd/USER/wangjingshen/tmp/ \
    --output pathseq.bam \
    --scores-output pathseq_score.txt \
```

注：该流程可以拆开分为三步 PathSeqFilterSpark、PathSeqBwaSpark、PathSeqScoreSpark [5-7]，一般情况下，跑 PathSeqPipelineSpark 即可。


#### 结果解读
1.pathseq.bam

比对到微生物参考序列的高质量非宿主 reads，其中 YP 标签列出了该条 reads 比对上物种的 NCBI 分类 ID。示例如下：

```
test_seq1 16      NZ_CP013119.1   2067089 0       150M    *       0       0       TGCGGGTCCCCGTCAATTCCTTTGAGTTTTAATCTTGCGACCGTACTCCCCAGGCGGTCAACTTCACGCGTTAGCTGCGCTACTAAGGCCTAACGGCCCCAACAGCTAGTTGACATCGTTTAGGGCGTGGACTCCCCGGGTATCTAATCA FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF:FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF:FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF:FFFFFFFFFFFFF:FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF XA:Z:NZ_CP013119.1,-1062893,150M,2;NZ_CP013119.1,+3368485,150M,2;NZ_CYTB01000006.1,+988,150M,2;NZ_AVOG02000015.1,+1074,150M,2;NZ_CP019697.1,-1212093,150M,4;NZ_CP019697.1,-1217843,150M,4;NZ_CP019697.1,+2594601,150M,4;      RG:Z:test   YP:Z:643674,134375,1386079,511
```

2.pathseq_score.txt

物种分类得分表，示例如下：

| tax_id | taxonomy | type | name | kingdom | score | score_normalized | reads | unambiguous | reference_length |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| 1386079 | root\|cellular_organisms\|Bacteria\|Proteobacteria\|Betaproteobacteria\|Burkholderiales\|Alcaligenaceae\|Alcaligenes\|\Alcaligenes_sp._EGD-AK7 | species | Alcaligenes_sp._EGD-AK7 | Bacteria | 318.42560350276494 | 0.9995708564749053 | 5745 | 0 | 4276451 |

上表各列的含义：

| 列 | 含义 |
| :--- | :--- |
| taxonomy |物种分类 |
| type | 物种分类的等级，主要为界、门、纲、目、科、属、种（kingdom，phylum，class，order，family，genus，species）|
| name | 物种名 |
| kingdom | 界名 |
| score | 该分类存在的可信程度 |
| score_normalized | score的标准化形式，每个kingdom的总和标准化为 100 |
| reads | 比对上的reads数量（模糊或明确）|
| unambiguous | 明确比对上的reads数量 |
| reference_length | 参考序列的长度（以碱基为单位）；如果没有直接对应的参考序列，则为 0 |


## 参考材料

1.Kostic AD, Ojesina AI, Pedamallu CS, Jung J, Verhaak RG, Getz G, Meyerson M. PathSeq: software to identify or discover microbes by deep sequencing of human tissue. Nat Biotechnol. 2011 May;29(5):393-6. doi: 10.1038/nbt.1868. PMID: 21552235; PMCID: PMC3523678.

2.https://console.cloud.google.com/storage/browser/gatk-best-practices

3.https://gatk.broadinstitute.org/hc/en-us/articles/360037872491--How-to-Fix-a-badly-formatted-BAM

4.https://gatk.broadinstitute.org/hc/en-us/articles/13832627644443-PathSeqPipelineSpark

5.https://gatk.broadinstitute.org/hc/en-us/articles/360036485592-PathSeqFilterSpark

6.https://gatk.broadinstitute.org/hc/en-us/articles/13832688099483-PathSeqBwaSpark

7.https://gatk.broadinstitute.org/hc/en-us/articles/13832764304539-PathSeqScoreSpark