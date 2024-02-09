---
title: "一种引力波探测卫星动态姿态规划方法"
authors:
- admin
date: "2022-08-01T00:00:00Z"
doi: "10.16804/j.cnki.issn1006-3242.2022.04.007"

# Schedule page publish date (NOT publication's date).
publishDate: "2022-08-01T00:00:00Z"

# Publication type.
# Accepts a single type but formatted as a YAML list (for Hugo requirements).
# Enter a publication type from the CSL standard.
publication_types: ["中文期刊"]

# Publication name and optional abbreviated publication name.
publication: ""
publication_short: ""

abstract: 在引力波探测任务中，针对大尺度激光建链时目标姿态动态变化以及姿态机动约束复杂的问题，提出了引力波探测卫星动态姿态规划方法。首先给出引力波探测卫星的最优目标姿态求解方法。然后，设计了随机节点启发式扩展方法和目标姿态实时更新方法，对力矩轨迹进行平滑处理，实现了动态目标姿态规划。仿真结果表明，与经典RRT姿态规划方法相比，本文提出的方法具有更高的指向精度，并且消耗能量少，可满足引力波探测卫星大尺度建链保持的任务需求。

# Summary. An optional shortened abstract.
summary: 在引力波探测任务中，针对大尺度激光建链时目标姿态动态变化以及姿态机动约束复杂的问题，提出了引力波探测卫星动态姿态规划方法。

tags:
- Source Themes
featured: false

links:
- name: Custom Link
  url: 
url_pdf: 10.16804/j.cnki.issn1006-3242.2022.04.007
url_code: ''
url_dataset: '#'
url_poster: '#'
url_project: ''
url_slides: ''
url_source: '#'
url_video: '#'

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder. 
image:
  caption: 'Header'
  focal_point: ""
  preview_only: false

# Associated Projects (optional).
#   Associate this publication with one or more of your projects.
#   Simply enter your project's folder or file name without extension.
#   E.g. `internal-project` references `content/project/internal-project/index.md`.
#   Otherwise, set `projects: []`.
projects:
- internal-project

# Slides (optional).
#   Associate this publication with Markdown slides.
#   Simply enter your slide deck's filename without extension.
#   E.g. `slides: "example"` references `content/slides/example/index.md`.
#   Otherwise, set `slides: ""`.
slides: example
categories:
    - Work
tags:
    - Thesis
---



## 仿真结果

使用经典RRT算法和改进RRT算法进行姿态规划，仿真结果如下：

![路径对比图](path_compare.png)

![力矩对比图](u_compare.png)


{{% callout note %}}
Create your slides in Markdown - click the *Slides* button to check out the example.
{{% /callout %}}

Add the publication's **full text** or **supplementary notes** here. You can use rich formatting such as including [code, math, and images](https://docs.hugoblox.com/content/writing-markdown-latex/).
