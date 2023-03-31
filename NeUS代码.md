# NeUS代码

## models

### renderer.py

#### ==func== sample_pdf

首先给一个均匀分布u（0.05, 0.15, ..., 0.95），然后寻找u在cdf分布的对应区间，根据cdf区间找到对应的z值区间，结果是获得一些靠近cdf等值分布点的采样点

