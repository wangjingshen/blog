## 背景
最近在处理研发同事的去批次整合分析需求时，发现转录组(/SGRNJ06/randd/USER/wangjingshen/script/seurat/script/seurat.R)和ATAC数据(/SGRNJ06/randd/USER/wangjingshen/script/Signac_singleR/script/analysis.R) 此前正常使用的 harmony 包（conda环境r4.1_env）报如下错，现整理相关内容如下。

<div align='left'>
      <img src="https://github.com/wangjingshen/blog/blob/master/image/202509_1_HarmonyFix/harmony_error.png"
      alt="Editor" width = "500">
</div>


## 修复方法1
方法1是一个临时方法，在相关流程中注释掉 RunHarmony，自行计算 harmony_embeddings， 再添加进 seurat 对象中。
```
# data_seurat <- RunHarmony(data_seurat,group.by="sample", verbose = F)

harmony_embeddings <- HarmonyMatrix(
      data_mat  = as.matrix(Embeddings(data_seurat)),
      meta_data = data_seurat@meta.data,
      vars_use  = "sample",
      do_pca = FALSE)

rownames(my_harmony_embeddings) <- rownames(Embeddings(data_seurat))
data_seurat[["harmony"]] <- CreateDimReducObject(embeddings = harmony_embeddings, key = "harmony_", assay = DefaultAssa(data_seurat))
```

## 修复方法2
方法2对 harmony 源代码进行修改，再基于修改好的harmony包重新安装，推荐使用。

step1, 下载harmony包
```
git clone https://github.com/immunogenomics/harmony.git
```

step2, 修改报错的部分(harmony/R/RunHarmony.R)
```
# reduction.key <- Seurat::Key(reduction.save, quiet = TRUE) 
reduction.key = reduction.save"
```

step3, 重新安装
```
devtools::install_local('/SGRNJ06/randd/USER/wangjingshen/script_dev/harmony/harmony-master/')
```

## reference
1.https://github.com/immunogenomics/harmony/issues/159