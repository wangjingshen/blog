## 前言
这里针对16S项目的分析需求和分析代码进行整理,以备后续参考。

## 分析代码
分析环境：r4.1_env

代码：/SGRNJ06/randd/USER/wangjingshen/script_dev/sc16S_downstream_analysis/script/

示例：/SGRNJ06/randd/USER/wangjingshen/script_dev/sc16S_downstream_analysis/test/

#### step0 数据预处理
代码：/SGRNJ06/randd/USER/wangjingshen/script_dev/sc16S_downstream_analysis/script/prepdata.R

参数：

--rds           注释好的rds

--df_genus      16S的UMI矩阵, 多个样本用逗号分隔

--rna_spname    转录组的样本名, 多个样本用逗号分隔

--outdir        输出目录, default: 00.data


输出：
1）data_seurat.rds              整合16S genus丰度的rds

添加的信息如下:
<div align='left'>
      <img src="https://github.com/wangjingshen/blog/blob/master/image/2025/202507_1_sc16SDownstreamAnalysis/00.data/00_data_genus.png"
      alt="Editor" width = "500">
</div>

#### step1 基础展示
代码：/SGRNJ06/randd/USER/wangjingshen/script_dev/sc16S_downstream_analysis/script/basic_plot.R

参数：

--rds               数据预处理生成的rds

--subcluster        用于作图的cluster, default: all

--specific_genus    用于作图的特定genus, 没有就不用填这个参数

--splitgroup        按group分开作图, default: T

--outdir            输出目录, default: 01.basic_plot

结果：

featureplot_total_genus：genus 的总 UMI 的 UMAP 图；

<div align='left'>
      <img src="https://github.com/wangjingshen/blog/blob/master/image/2025/202507_1_sc16SDownstreamAnalysis/01.basic_plot/featureplot_total_genus.png"
      alt="Editor" width = "400">
</div>

featureplot_total_genus_group：genus 的总 UMI 按组拆分的 UMAP 图；

<div align='left'>
      <img src="https://github.com/wangjingshen/blog/blob/master/image/2025/202507_1_sc16SDownstreamAnalysis/01.basic_plot/featureplot_total_genus_group.png"
      alt="Editor" width = "600">
</div>

featureplot_genus_detect：检测到 genus 的 UMAP 图，其中，检测到 genus 的总 UMI 数 > 2 定义为 genus+；反之，定义为 genus-；

<div align='left'>
      <img src="https://github.com/wangjingshen/blog/blob/master/image/2025/202507_1_sc16SDownstreamAnalysis/01.basic_plot/featureplot_genus_detect.png"
      alt="Editor" width = "500">
</div>

featureplot_genus_detect_group：检测到 genus 按组拆分的 UMAP 图；

<div align='left'>
      <img src="https://github.com/wangjingshen/blog/blob/master/image/2025/202507_1_sc16SDownstreamAnalysis/01.basic_plot/featureplot_genus_detect_group.png"
      alt="Editor" width = "500">
</div>

barplot_total_genus_umi: 各细胞类型各组检测到 genus 的总UMI数的 条形图；

<div align='left'>
      <img src="https://github.com/wangjingshen/blog/blob/master/image/2025/202507_1_sc16SDownstreamAnalysis/01.basic_plot/barplot_total_genus_umi.png"
      alt="Editor" width = "500">
</div>

barplot_total_genus_umi_group: 各组检测到 genus 的总UMI数的 条形图；

<div align='left'>
      <img src="https://github.com/wangjingshen/blog/blob/master/image/2025/202507_1_sc16SDownstreamAnalysis/01.basic_plot/barplot_total_genus_umi_group.png"
      alt="Editor" width = "300">
</div>

featureplot_top10_genus_*: *数据中检出细胞数前 10 的 genus 的 UMAP 图（*为 Main：整体数据 or groupX：X组数据）；

<div align='left'>
      <img src="https://github.com/wangjingshen/blog/blob/master/image/2025/202507_1_sc16SDownstreamAnalysis/01.basic_plot/featureplot_top10_genus_N.png"
      alt="Editor" width = "800">
</div>

top10_genus_split：*数据中检出细胞数前 10 的 genus 的 UMAP 图拆分版，其中数字后缀为检出细胞数的降序排序号；

<div align='left'>
      <img src="https://github.com/wangjingshen/blog/blob/master/image/2025/202507_1_sc16SDownstreamAnalysis/01.basic_plot/top10_genus_split/featureplot_top10_genus_N_1.png"
      alt="Editor" width = "400">
</div>

barplot_genus_count_sample_cluster：横坐标为样本+细胞类型，纵坐标为检测到的细菌属个数；

<div align='left'>
      <img src="https://github.com/wangjingshen/blog/blob/master/image/2025/202507_1_sc16SDownstreamAnalysis/01.basic_plot/barplot_genus_count_sample_cluster.png"
      alt="Editor" width = "700">
</div>

barplot_genus_umi_sample_cluster：横坐标为样本+细胞类型，纵坐标为检测到的细菌属 UMI 总数；

<div align='left'>
      <img src="https://github.com/wangjingshen/blog/blob/master/image/2025/202507_1_sc16SDownstreamAnalysis/01.basic_plot/barplot_genus_umi_sample_cluster.png"
      alt="Editor" width = "700">
</div>

barplot_plot_data_cluster：作图用到的数据；


#### step2 genus的差异分析
代码：/SGRNJ06/randd/USER/wangjingshen/script_dev/sc16S_downstream_analysis/script/diff.R

参数：

--rds               注释好的rds

--mode              差异分析模式, 按cluster分析或按group分析, default: cluster

--split             拆分进行差异分析, 例如按cluster分析时, 设置split为T时,每个group分别按cluster分析。 default: F

--outdir            输出目录, default: 02.diff

输出：
<div align='left'>
      <img src="https://github.com/wangjingshen/blog/blob/master/image/2025/202507_1_sc16SDownstreamAnalysis/02.diff/02_diff_res.png"
      alt="Editor" width = "300">
</div>


#### step2 根据genus的差异分析结果画 circos 图
代码：/SGRNJ06/randd/USER/wangjingshen/script_dev/sc16S_downstream_analysis/script/circos.R

参数：

--stat_df           差异分析的表

--obj               用于作图的对象, clusterA or groupA or all

--top_n             按照genus的均值取 top_n 个, dafault: 10

--outdir            输出目录, default: 02.diff/circos

输出：
<div align='left'>
      <img src="https://github.com/wangjingshen/blog/blob/master/image/2025/202507_1_sc16SDownstreamAnalysis/02.diff/circos/cluster/circos_MPs.png"
      alt="Editor" width = "500">
</div>

#### step3 genus检出的转录多样性 vs genus未检出的转录多样性 (一般不输出，暂无项目分析该需求)
代码：/SGRNJ06/randd/USER/wangjingshen/script_dev/sc16S_downstream_analysis/script/transcriptome_diversity.R

参数：

--rds               rds

--split_group       default: F

--analysis_genus    用于分组的 genus, default: total_genus, 即根据所有genus的检出进行分组

--nFeatures         用于计算转录多样性的基因个数, default:500

--outdir            输出目录, default: 03.transcriptome_diversity

输出：
<div align='left'>
      <img src="https://github.com/wangjingshen/blog/blob/master/image/2025/202507_1_sc16SDownstreamAnalysis/03.diversity/total/shannon_diversity_barplot.png"
      alt="Editor" width = "500">
</div>