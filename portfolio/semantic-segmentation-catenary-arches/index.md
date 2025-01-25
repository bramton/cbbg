---
title: "Semantic segmentation of point cloud catenary arches"
date: 2022-12-26
date_human: "End of 2022"
draft: false
video: "https://youtu.be/nU4kkb3pz0Y"
article: "https://doi.org/10.3390/s23010222"
web: "https://www.saxion.edu/business-and-research/research/smart-industry/ambient-intelligence/digitalisatie-bovenleidingen-en-draagconstructies"
dataset: "https://doi.org/10.4121/17048816.v1"
summary: "Digitalisation of catenary arches using point cloud data."
---

# Description
<!--- Situation --->
Accurate and up-to-date maps of exising rail infrastructure is often not available. Currently, information from the railway scene is surveyed by human operators when it is needed. This is a costly and time consuming operations, furthermore the rail cannot be used during surveying for safety reasons.

<!--- Task/Action --->
Goal of this research was to automate the survey process and deliver fast, accurate maps without disturbing the availability of the rail. This is done by using a train mounted laser scanner and using a modified PointNet++ network to perform the semantic segmentation task. Semantic segmentation assigns a meaningful label to each point within the point cloud.

<!--- Result/Impact --->
Based on a leave-one-out-cross-validation using a modified PointNet++ network an mIoU of 71% is obtained. This is a promising result. If repeated measurements over time are taken, this work can also pave the way towards predictive maintenance.


# Video
{{< youtube nU4kkb3pz0Y >}}


# References
1. Ton, B.; Ahmed, F.; Linssen, J. [Semantic Segmentation of Terrestrial Laser Scans of Railway Catenary Arches: A Use Case Perspective](https://doi.org/10.3390/s23010222). *Sensors* 2023, 23, 222
2. [Dataset] Ton,B.; Strukton Rail [Labelled high resolution point cloud dataset of 15 catenary arches in the Netherlands](https://doi.org/10.4121/17048816.v1). *4TU.ResearchData* 2022
