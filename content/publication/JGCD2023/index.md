---
title: "Polynomial Attitude Trajectory Planning for Spacecraft with Movable Parts Using Decoupled Strategy"
authors:
- admin
author_notes:
- "Main contribution"

date: "2023-11-14T00:00:00Z"
doi: "10.2514/1.G007645"

# Schedule page publish date (NOT publication's date).
publishDate: "2023-11-14T00:00:00Z"

# Publication type.
# Accepts a single type but formatted as a YAML list (for Hugo requirements).
# Enter a publication type from the CSL standard.
publication_types: ["article-journal"]

# Publication name and optional abbreviated publication name.
publication: "Journal of Guidance, Control, and Dynamics"
publication_short: "JGCD"

abstract: The problem of attitude planning for spacecraft with movable parts under complex pointing constraints is addressed in this paper. To avoid tackling the complex pointing constraints in the high-dimensional system, a novel decoupled strategy is proposed to map the pointing space to the attitude space. Path-planning algorithm Rapidly-exploring Random Tree (RRT)*-Smart is employed in pointing space to generate a collision-free pointing path, and piecewise quintic polynomials are used to represent the attitude path. By proving the differential flatness of spacecraft with movable parts, the inverse dynamics is used to acquire attitude state trajectory in state space. Benefiting from the decoupled strategy, the attitude state trajectory is optimized by a new heuristic time allocation algorithm using binary search with no gradient information needed. The simulation results are discussed, and the performance during each scenario is analyzed.

# Summary. An optional shortened abstract.
summary: The problem of attitude planning for spacecraft with movable parts under complex pointing constraints is addressed in this paper.

tags:
- Source Themes
featured: true

# links:
# - name: ""
#   url: ""
url_pdf: https://arc.aiaa.org/doi/10.2514/1.G007645
url_code: ''
url_dataset: ''
url_poster: ''
url_project: ''
url_slides: ''
url_source: ''
url_video: ''

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
projects: []

# Slides (optional).
#   Associate this publication with Markdown slides.
#   Simply enter your slide deck's filename without extension.
#   E.g. `slides: "example"` references `content/slides/example/index.md`.
#   Otherwise, set `slides: ""`.
slides: ""
categories:
    - Work
tags:
    - Thesis
---

![Alt text](traj.png)

![Alt text](path_compare.png)

{{% callout note %}}
Click the *Cite* button above to demo the feature to enable visitors to import publication metadata into their reference management software.
{{% /callout %}}

{{% callout note %}}
Create your slides in Markdown - click the *Slides* button to check out the example.
{{% /callout %}}

Add the publication's **full text** or **supplementary notes** here. You can use rich formatting such as including [code, math, and images](https://docs.hugoblox.com/content/writing-markdown-latex/).
