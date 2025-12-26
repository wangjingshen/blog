## 前言
常有释放数据的需求，主要是原始数据和矩阵，不同 assay 释放的矩阵各不相同，现针对此类需求进行整理。

## 使用
代码：/SGRNJ06/randd/USER/wangjingshen/script_dev/data_release/script/release.py

测试示例：/SGRNJ06/randd/USER/wangjingshen/script_dev/data_release/test/

运行示例
```
python /SGRNJ06/randd/USER/wangjingshen/script_dev/data_release/script/release.py \
 --mapfile_json mapfile.json \
 --outdir data \
 --data_type fastq,rna_matrix,ebv_matrix \
 --rna_celescope test/RNA/ \
 --ebv_celescope test/EBV/ \
```

#### 参数：
```
    --mapfile_json   要释放数据的相关 mapfile 的 json 文件
    --outdir    结果路径
    --data_type   要释放的数据格式，当前支持 fastq, rna_matrix, tag_matrix, ebv_matrix, bcr_matrix, tcr_matrix, snp_matrix
    --rna_celescope  rna assay 的 celescope 分析路径
    --tag_celescope  tag assay 的 celescope 分析路径
    --ebv_celescope  ebv assay 的 celescope 分析路径
    --bcr_celescope  bcr assay 的 celescope 分析路径
    --tcr_celescope  tcr assay 的 celescope 分析路径
    --snp_celescope  rna assay 的 celescope 分析路径
```

json文件示例
```
{
 "rna":"rna_mapfile",
 "ebv":"ebv_mapfile",
}
```

mapfile文件示例
```
# RNA
prefix,library,sample
prefix1,fq_path1,sample1
prefix2,fq_path2,sample2

# 其余assay
prefix,library,sample,match_rna
prefix1,fq_path1,sample1,match_rna1
prefix2,fq_path2,sample2,match_rna2
```

#### 结果
<div align='left'>
      <img src="https://github.com/wangjingshen/blog/blob/master/image/202510_ReleaseData/release.png"
      alt="Editor" width = "400">
</div>