---
title: 'Time-optimal attitude planning for spacecraft with movable parts using second order cone programming'

# Authors
# If you created a profile for a user (e.g. the default `admin` user), write the username (folder name) here
# and it will be replaced with their full name and linked to their profile.
authors:
  - admin


# Author notes (optional)
author_notes:
  - 'Major contribution'


date: '2023-08-28T00:00:00Z'
doi: '10.1016/j.ast.2023.108589'

# Schedule page publish date (NOT publication's date).
publishDate: '2023-08-28T00:00:00Z'

# Publication type.
# Accepts a single type but formatted as a YAML list (for Hugo requirements).
# Enter a publication type from the CSL standard.
publication_types: ['article-journal']

# Publication name and optional abbreviated publication name.
publication: Aerospace Science and Technology
publication_short: AST

abstract: The objective of this paper is to plan a time-optimal attitude trajectory in a relatively short time for spacecraft with movable parts under complex pointing constraints. The constrained attitude planning problem is ﬁrst established, then the constraints on attitude maneuvers are described. The time-optimal attitude planning for rigid spacecraft body is decoupled into geometric level and dynamic level through the addition of the path scalar. To obtain a given safe path in quaternion space, RRT*-Smart path planning and spherical and quadrangle (SQUAD) interpolation are carried out at the geometric level. The timeoptimal parameterization for a given path is reformulated as a second order cone programming (SOCP) problem at the dynamic level through a nonlinear change in variables and the addition of various convexity-preserving extensions. In order to overcome the issue of high system dimension caused by the movable parts, a decoupled attitude planning method is designed to generate the quaternion trajectory of the spacecraft body and the rotation angle trajectory of the movable parts respectively. In comparison to GPOPS, the simulation results demonstrate that the proposed method can plan a suboptimal solution in milliseconds. Furthermore, the performance indicator for energy consumption is better.

# Summary. An optional shortened abstract.
summary: The objective of this paper is to plan a time-optimal attitude trajectory in a relatively short time for spacecraft with movable parts under complex pointing constraints.

tags: []

# Display this page in the Featured widget?
featured: true

# Custom links (uncomment lines below)
# links:
# - name: Custom Link
#   url: http://example.org

url_pdf: 'https://doi.org/10.1016/j.ast.2023.108589'
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
  focal_point: ''
  preview_only: false

# Associated Projects (optional).
#   Associate this publication with one or more of your projects.
#   Simply enter your project's folder or file name without extension.
#   E.g. `internal-project` references `content/project/internal-project/index.md`.
#   Otherwise, set `projects: []`.
projects:
  - example

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

## Simulation Results
![Alt text](traj.png)

![Alt text](path_compare.png)

{{% callout note %}}
Click the _Cite_ button above to demo the feature to enable visitors to import publication metadata into their reference management software.
{{% /callout %}}

{{% callout note %}}
Create your slides in Markdown - click the _Slides_ button to check out the example.
{{% /callout %}}

Add the publication's **full text** or **supplementary notes** here. You can use rich formatting such as including [code, math, and images](https://docs.hugoblox.com/content/writing-markdown-latex/).
