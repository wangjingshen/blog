## 背景

此前跑 pathseq 需要调整服务器的资源限制，其中服务器和SGE集群的资源配置不太一样，记录如下。


## 配置

#### 服务器

服务器需要修改 /etc/security/limits.conf, 适当提高 nproc（每个用户进程数限制）和nofile（每个进程可以打开的文件数限制）[1,2]。

#### SGE集群

SGE集群需要额外配置 /opt/sge/default/common/configuration，在 execd_params 里设置 S_DESCRIPTORS 和 H_DESCRIPTORS（同样的，适当提高）。


## 使用
在跑需要提高文件数的程序时，在前面加上如下命令行:

```
ulimit -n max_open_files
```

## reference

1. https://blog.csdn.net/zxljsbk/article/details/89153690
2. https://github.com/broadinstitute/gatk/issues/5316