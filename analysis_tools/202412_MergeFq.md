## 前言
目前转录组和富集文库有小测、加测数据，释放数据和部分自己写的流程需要使用merge之后的数据，现整理相关内容如下。

## 解决方案
根据mapfile里的样本名分别进行fq的merge，然后生成merge后的mapfile。

## 脚本
/SGRNJ06/randd/USER/wangjingshen/script/merge_fq/script/merge_fq.py

#### 参数

--mapfile ： 原始的 mapfile

--merge_outdir ： merge 后的 fq 输出路径

--mapfile_outdir ： merge 后的 mapfile 输出路径


#### 输出

1.merge 后的 fq 

2.merge 后的 mapfile

#### 测试示例

测试路径： /SGRNJ06/randd/USER/wangjingshen/script/merge_fq/test/

原始 mapfile
```
s1    raw_fq/test1_1/    test1
s1    raw_fq/test1_2/    test1
s2    raw_fq/test2_1/    test2
s2    raw_fq/test2_2/    test2
```

merge 后的 fq, tree outdir/
```
outdir/
├── test1_fq
│   ├── s1_R1.fastq.gz
│   └── s1_R2.fastq.gz
└── test2_fq
    ├── s2_R1.fastq.gz
    └── s2_R2.fastq.gz

2 directories, 4 files
```

merge 后的 mapfile
```
s1   outdir/test1_fq/    test1
s2   outdir/test2_fq/    test2
```