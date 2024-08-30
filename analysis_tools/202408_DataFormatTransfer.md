## 前言
项目分析中有时会遇到不同数据格式的转换需求，现整理相关内容如下。


## 背景
### SeuratDisk

针对此类需求，过往经常使用 SeuratDisk 包进行处理[1]，测试结果主要如下：

1）seurat 和 SingleCellExperiment 可以相互转换；

2）seurat 可以 转成 anndata，anndata 转 seurat 可能会报如下的错，这可能的原因之一是 anndata 中 .obs 和 .var 中的列名存在空格或者符号；

<div align='left'>
      <img src="https://github.com/wangjingshen/blog/blob/master/image/202408_DataFormatTransfer/anndata_to_seurat_bug.png" alt="Editor" width = "700">
</div>

3）seurat 和 loom 可以相互转换，但是 loom 转 seurat 的降维数据丢失。

<div align='left'>
      <img src="https://github.com/wangjingshen/blog/blob/master/image/202408_DataFormatTransfer/loom_to_seurat_bug.png" alt="Editor" width = "800">
</div>

### sceasy

sceasy 包是另一个专门用于处理数据转换的包[3]。sceasy 接受的输入数据类型有 anndata，seurat，sce，loom；转换输出的数据类型有 anndata，seurat，sce，loom，cds。不同数据类型的转换测试结果：

| 转换后的类型 | input：anndata | input：seurat | input：sce | input：loom |
| :--- | :--- | :--- | :--- | :--- |
| anndata | - | yes | yes | yes |
| seurat | yes | - | no | no |
| loom | no | no | yes | - |
| sce | no | yes | - | yes |
| cds | yes | no | no | no |

表1：sceasy数据转换情况表。yes：表示该种输入类型可以转换为目标类型； no：表示该种输入类型无法直接转换为目标类型；-： 表示输入和输出类型一致，不进行操作。


## 脚本

尽管 sceasy 比 SeuratDisk 使用体验相对好一些，但是 sceasy 部分数据类型之间不能转换，此外，部分类型转换后存在不能作图的 bug。针对上述问题，写脚本 /SGRNJ06/randd/USER/wangjingshen/script/data_format_trans/script/data_format_trans.R 进行优化。

#### 主要参数

--input  输入数据

--from_type  输入数据的数据类型，anndata，seurat，sce，loom

--to_type  转换后的数据类型，anndata，loom，sce，seurat，cds

--outdir  输出目录，默认为 outdir/

#### 输出

转换后的数据类型 

#### 测试示例

/SGRNJ06/randd/USER/wangjingshen/script/data_format_trans/test/，以表1中无法相互转换的格式为例，转换结果如下：

![image](https://github.com/wangjingshen/blog/blob/master/image/202408_DataFormatTransfer/data_fromat_transfer_update_results.png)


## 参考

1.https://github.com/mojaveazure/seurat-disk

2.https://github.com/cellgeni/sceasy