## 前言
此前写了一个脚本用于 istar 分群映射到 spots，测试中会出现 spots 图和 istar 分群图偏移的问题，现针对该问题进行修复。


## 解决方案
spots 图和 istar 分群图偏移（见下图），这个问题是分析时默认使用在组织中的 spots 导致的。

<div align='left'>
      <img src="https://github.com/wangjingshen/blog/blob/master/image/2026/202605_IstarSpotsUpdate/istar2spots_bg_raw.png"
      alt="Editor" width = "300">
</div>

正确的是使用整张切片图（包含不在组织中的 spots）进行缩放，再进行映射。

<div align='left'>
      <img src="https://github.com/wangjingshen/blog/blob/master/image/2026/202605_IstarSpotsUpdate/istar2spots_bg_update.png"
      alt="Editor" width = "300">
</div>

使用还是参见 https://github.com/wangjingshen/blog/blob/master/blog/2026/202603_istarSpots.md