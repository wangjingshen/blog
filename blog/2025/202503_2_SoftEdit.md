## 背景
最近研发同事提了一个统计 16S 富集文库的引物序列的需求，现基于 celescope1.15.0 进行可编辑安装，这样改动 celescope1.15.0 里的代码，可以实时生效，便于调试，现整理相关内容如下。

## 方法
下载 celescope1.15.0 安装包，解压进入包的目录，创建环境，建议是把 celescope 移到环境中 lib/python3.9/site-packages/（便于管理）

```
conda create -p /SGRNJ06/randd/USER/wangjingshen/soft/miniforge3/envs/celescope1.15.0_probe -y --file conda_pkgs.txt

conda activate /SGRNJ06/randd/USER/wangjingshen/soft/miniforge3/envs/celescope1.15.0_probe

pip install -e . # 可编辑安装
```