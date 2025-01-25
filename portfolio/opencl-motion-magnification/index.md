---
title: "OpenCL motion magnification"
date: 2021-04-19
date_human: "April 2021"
draft: false
git: "https://github.com/bramton/opencl-motion-magnification"
summary: "Accelerating the magnification of tiny motions in video data."
---

# Description
Tiny motions which are hardly visible to the human eye can be visually amplified using the phase-based motion magnification algorithm[1]. The initial idea was to use this algorithm to analyse videos from infrastructure assets such as bridges to estimate the structural health. An accelerated open-source implementation of this algorithm was not available yet. I implemented this algorithm using OpenCL to accelerate the computations.

# Video
A small experiment I did was to compare raw video against compressed video.

{{< youtube g7Sy2e0N7lw >}}

# References
1. N. Wadhwa, M. Rubinstein, F. Durand and W. T. Freeman, [Riesz pyramids for fast phase-based video magnification](https://doi.org/10.1109/ICCPHOT.2014.6831820) *2014 IEEE International Conference on Computational Photography (ICCP)*, Santa Clara, CA, USA, 2014, pp. 1-10
2. [Website belonging to publication with some cool result videos](https://people.csail.mit.edu/nwadhwa/riesz-pyramid/)
