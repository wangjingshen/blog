## 前言

前段时间阅读了 GeneTrajectory，并做了一些测试，现整理如下，作为在 github 上写的第一篇 blog 吧。


## 文章主要内容：

基因表达的动态变化往往决定细胞的状态和功能，严格调控的基因级联是生物学过程的基础。当前，单细胞 RNA 测序已广泛用于研究细胞状态转变的基因动力学，一般我们可以通过细胞轨迹分析来研究此类问题。然而，当细胞同时经历多个生物学过程且每个过程由不同的基因集参与时，基于整体基因表达谱识别到的伪时间将不再可靠，因为它混合了多个过程的影响。举例而言，一生物体内分化（线性过程）和细胞周期（循环过程）同时发生，当这两个过程严格相互依赖时，它们可以通过共同的潜在变量进行参数化从而得到一维细胞曲线，沿着曲线对细胞进行排序从而为细胞分配有意义的伪时间；而当这两个过程相互独立时，细胞会落入流形（这两个过程的笛卡尔积），这两个过程不共享共同的潜在变量，此时基于一维细胞曲线的方法将不再适用。

![image](https://github.com/wangjingshen/blog/blob/master/image/202405_GeneTrajectory/GeneTrajectory_Fig_1A.png)

为了应对这一挑战，作者提出了构建基因轨迹的 GeneTrajectory，其可以将多个独立过程与连续基因动力学进行反卷积，从而提取在给定生物学过程有顺序贡献的基因并构建相应的基因轨迹，从而揭示基因活动的连续顺序。


## GeneTrajectory主要工作流程：

步骤 1：构建细胞的k最近邻 （kNN） 图，其中每个细胞与其 k 个最近邻的细胞相连;

步骤 2：根据基因在细胞图上的表达分布计算 Wasserstein 距离从而量化给定基因分布转移到另一个基因分布的最小成本;

步骤 3：识别基因轨迹，具体而言，搜索与扩散图嵌入原点距离最大的基因作为第一条基因轨迹的终点，然后在图上使用随机游走来检索在终点1处终止的其他基因。第一条轨迹的基因检索完成后，在剩余基因中确定后续基因轨迹的终点并重复上述步骤，直到提取出所有可检测的轨迹;

步骤 4：确定每条基因轨迹上的基因顺序。

![image](https://github.com/wangjingshen/blog/blob/master/image/202405_GeneTrajectory/GeneTrajectory_workflow.png)


## 测试：
conda环境：jsr4.1

#### 测试1：人的髓系细胞数据集
生物学背景：CD14+ 单核细胞先转变为中间型单核细胞，然后分化为具有不同功能的 CD16+ 单核细胞。细胞分群注释如下：

<div align='left'>
      <img src="https://github.com/wangjingshen/blog/blob/master/image/202405_GeneTrajectory/human_myeloid_cluster.png" alt="Editor" width = "400">
</div>

对该数据集进行 GeneTrajectory 分析，得到3条基因轨迹，其中红色、绿色、蓝色依次代表基因轨迹 1、2、3。


<div align='left'>
      <img src="https://github.com/wangjingshen/blog/blob/master/image/202405_GeneTrajectory/human_myeloid_trajectory.png" alt="Editor" width = "450">
</div>

为了将基因轨迹和细胞注释信息结合起来，可以按轨迹上的基因顺序将基因分成 5 个连续的基因集，再计算细胞中每个基因集中的基因表达比例作为该细胞的对应基因集分数，然后将基因集分数映射到细胞分群上，红色区域（高比例表达部分）在细胞分群图上的分布提示每条基因轨迹参与的细胞类型转变。如测试数据所示，基因轨迹 1 与中间型单核细胞向 2 型树突状细胞分化相关；基因轨迹 2 与中间型单核细胞向 CD16+ 单核细胞分化相关；基因轨迹 3 与 CD14+ 单核细胞经由中间型单核细胞向 CD16+ 单核细胞分化相关。

<img src="https://github.com/wangjingshen/blog/blob/master/image/202405_GeneTrajectory/human_myeloid_trajectory_gene_bins.png" alt="Editor" width = 720>

此外，可以在基因轨迹和细胞分群上展示一些关键基因，例如与初始 CD14+ 单核细胞细胞状态相关的CLEC5A、RETN；在完全分化的 CD16+ 单核细胞中广泛表达的 C1QB 和 FCGR3A；在 2 型树突状细胞中表达的 CD72、CD1C、PKIB。

<img src ="https://github.com/wangjingshen/blog/blob/master/image/202405_GeneTrajectory/human_myeloid_trajectory_key_genes.png" alt="Editor" width = 450>

<img src ="https://github.com/wangjingshen/blog/blob/master/image/202405_GeneTrajectory/human_myeloid_featureplot_key_genes.png" alt="Editor" width = 650>

注：另外可以通过一维图直接展示关键基因的顺序，示例如下：
<img src="https://github.com/wangjingshen/blog/blob/master/image/202405_GeneTrajectory/GeneTrajectory_Fig_6D.png" alt="Editor" width = 500>

接下来，使用细胞轨迹分析中常用的 monocle2 对测试数据进行分析，和上述 GeneTrajectory 的结果进行对比。如下图所示，monocle2 也可以识别出 CD14+ 单核细胞经由中间型单核细胞向 CD16+ 单核细胞和 2 型树突状细胞分化的轨迹。

<div align='left'>
      <img src="https://github.com/wangjingshen/blog/blob/master/image/202405_GeneTrajectory/human_myeloid_monolce_trajectories_cluster.png" alt="Editor" width = "470">
</div>

<div align='left'>
      <img src="https://github.com/wangjingshen/blog/blob/master/image/202405_GeneTrajectory/human_myeloid_monocle_trajectories_pseudotime.png" alt="Editor" width = "420">
</div>

通过分支分析来探索不同分化命运中参与的基因，如测试数据所示，热图从中间向左是 CD14+ 单核细胞向 2 型树突状细胞分化， 热图从中间向右是 CD14+ 单核细胞向 CD16+ 单核细胞分化（热图中的 Pre-branch、Cell fate1、Cell fate2 依次对应 state1、state2、state3），通过聚类找出谱系依赖性表达模式的基因模块。

<div align='left'>
      <img src="https://github.com/wangjingshen/blog/blob/master/image/202405_GeneTrajectory/human_myeloid_monocle_trajectories_state.png" alt="Editor" width = "270">
</div>

<div align='left'>
      <img src="https://github.com/wangjingshen/blog/blob/master/image/202405_GeneTrajectory/human_myeloid_monocle_trajectories_branches.png" alt="Editor" width = "400">
</div>


#### 测试2：小鼠的脑数据集
生物学背景：放射状胶质细胞分化为兴奋性神经元和抑制性神经元。细胞分群注释如下：

<div align='left'>
      <img src="https://github.com/wangjingshen/blog/blob/master/image/202405_GeneTrajectory/mouse_brain_cluster.png" alt="Editor" width = "400">
</div>

对该数据集进行 GeneTrajectory 分析, 得到基因轨迹如下：

<div align='left'>
      <img src="https://github.com/wangjingshen/blog/blob/master/image/202405_GeneTrajectory/mouse_brain_trajectory.png" alt="Editor" width = "450">
</div>

同样可以将基因轨迹分成 5 个连续的基因集，再映射到细胞分群图上。其中，基因轨迹 1 与放射状胶质细胞向抑制性神经元分化相关；基因轨迹 2 与放射状胶质细胞向兴奋性神经元分化相关。

<div align='left'>
      <img src="https://github.com/wangjingshen/blog/blob/master/image/202405_GeneTrajectory/mouse_brain_trajectory_gene_bins.png" alt="Editor" width = "700">
</div>

同样在基因轨迹和细胞分群上展示一些关键基因，包括在放射状胶质细胞中表达的 Id3，在抑制性神经元中表达的 Dlx2，在兴奋性神经元中表达的 
Satb2。

<div align='left'>
      <img src="https://github.com/wangjingshen/blog/blob/master/image/202405_GeneTrajectory/mouse_brain_trajectory_key_genes.png" alt="Editor" width = "450">
</div>

<img src ="https://github.com/wangjingshen/blog/blob/master/image/202405_GeneTrajectory/mouse_brain_featureplot_key_genes.png" alt="Editor" width = 700>


## 总结

作者开发了一种构建基因轨迹的方法 —— GeneTrajectory，轨迹中的基因顺序表征特定生物学过程的基因动力学。

GeneTrajectory也存在着一些不足，当一个基因参与多个生物学过程时，理论上它应该在基因轨迹的 "hub" 节点处，然而如果该基因在许多细胞中均有表达，其与无信息基因（homogeneously expressed gene）的 Wasserstein 距离较小，这导致 GeneTrajectory 难以区分这两类基因。此外，GeneTrajectory 不能自动推断每条轨迹的方向性，需要人工进行判定；但从另一方面来说，GeneTrajectory 突破了部分轨迹分析工具需要指定起始或终止细胞的分析限制。

最后，讨论一下在项目分析时的轨迹分析工具选择，目前建议继续使用相对更成熟的 monocle，因为 monocle 有着丰富的下游分析以及可视化，并且已有较多文章引用，而GeneTrajectory的可视化形式还比较少；如果对轨迹中的基因顺序感兴趣，可以进行 GeneTrajectory 分析作为补充。


## 参考

Qu, R., Cheng, X., Sefik, E. et al. Gene trajectory inference for single-cell data by optimal transport metrics. Nat Biotechnol (2024). https://doi.org/10.1038/s41587-024-02186-3
