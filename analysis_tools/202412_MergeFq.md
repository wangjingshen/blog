## 前言
目前转录组和富集文库有小测、加测数据，释放数据和部分自己写的流程需要使用merge之后的数据。

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
test1    raw_fq/test1_1/    test1
test1    raw_fq/test1_2/    test1
test2    raw_fq/test2_1/    test2
test2    raw_fq/test2_2/    test2
```

merge 后的 fq
```
tree test/outdir/

test/outdir/
├── test1_fq
│   ├── test1_R1.fastq.gz
│   └── test1_R2.fastq.gz
└── test2_fq
    ├── test2_R1.fastq.gz
    └── test2_R2.fastq.gz

2 directories, 4 files
```

merge 后的 mapfile
```
test1   outdir/test1_fq/    test1
test2   outdir/test2_fq/    test2
```