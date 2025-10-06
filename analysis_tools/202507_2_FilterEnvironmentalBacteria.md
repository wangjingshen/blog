## 前言
16S rRNA 基因测序不可避免会引入实验污染，这里根据已发表文章[1]中的环境菌对 celescope pathseq 的输出进行过滤，并重新生成结果和报告。因为本地和云端跑 celescope pathseq 的输出不一致，这里针对两种分别写了一个脚本。


### 本地版本
代码：/SGRNJ06/randd/USER/wangjingshen/script/sc16S_environment_filter/

示例：/SGRNJ06/randd/USER/wangjingshen/script/sc16S_environment_filter/test/
```
source activate celescope2.2.0

python /SGRNJ06/randd/USER/wangjingshen/script/sc16S_environment_filter/script/pipeline.py \
    --mapfile mapfile
```
参数：

--mapfile   四列，依次为富集分析目录,环境菌文件（一列文件，环境菌genus）, 样本名, 对应转录组的分析目录

--step      要跑的分析模块，默认为 ln_mkdir,filter,report

```
pathseq_path /SGRNJ06/randd/USER/wangjingshen/script/sc16S_environment_filter/data/environment_list.tsv sample rna_path

```

### 云端版本
云端需要先把远端分析RNA转成本地目录格式，其中path.txt 为一列文件，云端 RNA 分析路径。
```
source activate /SGRNJ/Public/Software/conda_env/celescope2.2.0
cloud-convert paths.txt
```

代码：/SGRNJ06/randd/USER/wangjingshen/script/sc16S_environment_filter_cloud/

示例：/SGRNJ06/randd/USER/wangjingshen/script/sc16S_environment_filter_cloud/test/
```
source activate celescope2.2.0

python /SGRNJ06/randd/USER/wangjingshen/script/sc16S_environment_filter_cloud/script/environment_filter.py \
    --mapfile mapfile
```
参数：

--mapfile   三列，依次为富集云端分析目录,环境菌文件（一列文件，环境菌genus）, 样本名, 对应转录组的分析目录

```
celescope_pathseq_cloud_path sample rna_cloud2local_path

```


## 小结
后续考虑把本地和云端整合成一个脚本，到时候更新在这里。此外，目前主要是根据文章中的环境菌对细菌矩阵进行过滤，后续有新文章提供环境菌或者过滤环境菌算法，也会进行更新。


## reference

1.Salter, S.J., Cox, M.J., Turek, E.M. et al. Reagent and laboratory contamination can critically impact sequence-based microbiome analyses. BMC Biol 12, 87 (2014).