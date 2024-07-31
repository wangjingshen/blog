## 前言
最近整合分析、自动注释的需求比较多，现整理相关内容如下。


## 整合分析
多样本整合跑seurat流程 [1]。

#### 测试路径
/SGRNJ06/randd/USER/wangjingshen/script/seurat/test/

#### 主要参数
--matrix_10X：  表达矩阵路径（内含3个文件），多个数据用逗号进行分隔

--spname：      样本名，多个样本名用逗号进行分隔（需要与 matrix_10X 一一对应）

--outdir：      输出路径，默认为 outdir

#### 输出
1.seurat.rds ：           Seurat机械分群rds

2.seurat_cluster.pdf：    Seurat机械分群图

![image](https://github.com/wangjingshen/blog/blob/master/image/202407_Annotation/seurat_cluster.png)


## 自动注释 —— SingleR

SingleR 利用纯细胞类型的参考转录组来独立推断每个细胞或每个细胞群的身份[2]。

#### 测试路径

/SGRNJ06/randd/USER/wangjingshen/script/singleR/test

#### 主要参数
--rds：      Seurat 机械分群 rds

--species：  物种，人（hs），鼠（mm）

--outdir：   结果路径，默认为outdir

#### 输出
1.seurat_singleR.rds：                  包含singleR自动注释结果的rds

2.seurat_cluster_singleR.png（pdf）：   singleR自动注释图

3.cellular_composition.png（pdf）：     细胞组分图

![image](https://github.com/wangjingshen/blog/blob/master/image/202407_Annotation/seurat_cluster_singleR.png)


## 自动注释 —— auto_assign
auto_assign 是 CeleScope [3] 中的自动注释流程，这里总结一下使用方法，并增加一些输出文件。

#### 测试路径
/SGRNJ06/randd/USER/wangjingshen/script/auto_assign/test/

#### 主要参数
--rds ：               Seurat机械分群rds

--outdir ：            输出目录，可选参数，默认为当前路径

--sample ：            样本名

--type_marker_tsv：    细胞marker表，具体见 /SGRNJ06/randd/USER/wangjingshen/script/auto_assign/data/

#### 输出
1.seurat_anno.rds：                          包含auto_assign自动注释结果的rds

2.seurat_cluster_auto_assign.png（pdf）：    auto_assign自动注释图

3.cellular_composition.png（pdf）：          细胞组分图

![image](https://github.com/wangjingshen/blog/blob/master/image/202407_Annotation/seurat_cluster_auto_assign.png)


## 自动注释 —— CelliD
CelliD 基于多重对应分析 (Multiple Correspondence Analysis)，可在低维空间中同时表示细胞和基因。然后根据基因与每个细胞的距离对基因进行排序，从而提供每个细胞无偏的基因特征 [4]。

#### 测试示例
/SGRNJ06/randd/USER/wangjingshen/script/CelliD/test/

#### 主要参数
--rds ：        Seurat机械分群rds

--species ：    可选参数，物种，默认为hs（人）， mm（鼠）

--mode ：       注释模式： all（所有组织的 cell marker），single（单个组织的 cell marker），common（单个组织 + 常见细胞类型的 cell marker）

--organ ：      组织名， 具体见下面的'目前支持的器官组织'

--ref ：        参考，raw（Default，PanglaoDB 数据库的 cell markers），update（项目中更新的 cell marker）

--nfeatures：   可选参数，用于超几何检验的前 n 个 feature，默认为 200

--outdir ：     可选参数，输出目录，默认为 outdir

#### 输出
1.seurat_CelliD.rds：                 包含 CelliD 自动注释结果的rds

2.seurat_cluster_CelliD.png（pdf）：  CelliD 自动注释图

3.cellular_composition.png（pdf）：   细胞组分图

![image](https://github.com/wangjingshen/blog/blob/master/image/202407_Annotation/seurat_cluster_CelliD.png)

#### 目前支持的器官组织
| name | 中文名称 |
| :--- | :--- |
| Adrenal glands | 肾上腺 |
| Blood | 血液 |
| Bone | 骨骼 |
| Brain | 脑 |
| Connective tissue | 结缔组织 |
| Embryo | 胚胎 |
| Epithelium | 上皮 |
| Eye | 眼 |
| GI tract | 胃肠道 |
| Heart | 心脏 |
| Immune system | 免疫系统 |
| Kidney | 肾脏 |
| Liver | 肝脏 |
| Lungs | 肺 |
| Mammary gland | 乳腺 |
| Olfactory system | 嗅觉系统 |
| Oral cavity | 口腔 |
| Pancreas | 胰腺 |
| Placenta | 胎盘 |
| Reproductive | 生殖系统 |
| Skeletal muscle | 骨骼肌 |
| Skin | 皮肤 |
| Smooth muscle | 平滑肌 |
| Thymus | 胸腺 |
| Thyroid | 甲状腺 |
| Urinary bladder | 膀胱 |
| Vasculature | 脉管系统 |
| Zygote | 受精卵 |


## 小结
目前自动注释主要用的 singleR 流程，auto_assign 和 CelliD 流程可以备用；如果有更高的注释要求（例如客户样本），可以申请人工注释。

## 参考
1.Hao Y, Hao S, Andersen-Nissen E, Mauck WM 3rd, Zheng S, Butler A, Lee MJ, Wilk AJ, Darby C, Zager M, Hoffman P, Stoeckius M, Papalexi E, Mimitou EP, Jain J, Srivastava A, Stuart T, Fleming LM, Yeung B, Rogers AJ, McElrath JM, Blish CA, Gottardo R, Smibert P, Satija R. Integrated analysis of multimodal single-cell data. Cell. 2021 Jun 24;184(13):3573-3587.e29. doi：10.1016/j.cell.2021.04.048. Epub 2021 May 31. PMID：34062119; PMCID：PMC8238499.

2.Aran, D., Looney, A.P., Liu, L. et al. Reference-based analysis of lung single-cell sequencing reveals a transitional profibrotic macrophage. Nat Immunol 20, 163–172 (2019). https://doi.org/10.1038/s41590-018-0276-y

3.https://github.com/singleron-RD/CeleScope

4.Cortal, A., Martignetti, L., Six, E. et al. Gene signature extraction and cell identity recognition at the single-cell level with Cell-ID. Nat Biotechnol 39, 1095–1102 (2021). https://doi.org/10.1038/s41587-021-00896-6