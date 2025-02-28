## 背景

鉴于anaconda是商业公司，常用的 anaconda 及 miniconda 存在商业风险；而开源软件社区驱动的 conda-forge 提供了可免费使用、无商业风险且稳定高效的的miniforge，可作为anaconda、miniconda的替代品。本篇 blog 记录把 conda  环境迁移到 miniforge。

## 步骤

#### 下载安装命令

下载参见 https://github.com/conda-forge/miniforge 的 Install 部分，类 Unix 的系统可以通过 uname -a 查看系统架构以获取对应版本的下载命令。

示例如下：

```
wget -c "https://github.com/conda-forge/miniforge/releases/latest/download/Miniforge3-Linux-x86_64.sh"
```

#### 安装

运行下载好的安装命令，需要注意指定下安装目录。
```
bash Miniforge3-Linux-x86_64.sh
```

#### 迁移环境

复制原来的虚拟环境到 miniforge 的 env 目录下即可，命令如下：
```
cp -r /your_raw_path/envs/raw_env/ /your_miniforge_path/envs/
```

#### 使用

使用 miniforge bin 下的conda进行初始化

```
/your_miniforge_path/bin/conda init
```

其他人激活环境时需要填写全路径，例如 /your_miniforge_path/envs/env_A；

为了方便其他人使用自己的环境，可以让他们在 .condarc 里添加如下内容，

```
envs_dirs:
  - /your_miniforge_path/envs
```


## reference

1. https://github.com/conda-forge/miniforge

2. https://blog.csdn.net/qq_37424778/article/details/138313158