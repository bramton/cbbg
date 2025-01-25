---
title: "Railway point cloud object detection"
date: 2024-07-01
date_human: "July 2024"
draft: false
video: "https://youtu.be/xPIfndWJO04"
dataset: "https://doi.org/10.4121/fa259c52-a585-420c-8a0c-af5e91518e29.v1"
web: "https://techforfuture.nl/project/tff2207/"
summary: "Railway point cloud object detection."
---

# Description
<!--- Situation --->
Maintaining, renovation and construction of railway projects require accurate and up-to-date maps of the current rail situation. Obtaining these maps currently is an expensive and time consuming endeavour.

<!--- Task/Action --->
Therefore, a joint project between our research group [Ambient Intelligence](https://www.saxion.edu/business-and-research/research/smart-industry/ambient-intelligence) and [Strukton Rail](https://struktonrail.nl) was conceived to automate the process of mapping. To do so, a dedicated measurement train is used which is equipped with a LiDAR. This LiDAR accurately captures the rail environment as a point cloud. Using deep learning a model was trained to detect objects within the point cloud.

<!--- Result/Impact --->
As a result, digitalisation of large segments of rail can be done in a short amount of time.

# Video
This video shows the results from the PointPillar object detection model. For this proof-of-concept, the following object classes are detected: masts, signals, tension rods, and relay cabinets. The PointPillar implementation from the [MMDetection3D](https://github.com/open-mmlab/mmdetection3d) framework was used.

{{< youtube xPIfndWJO04 >}}

# References
1. [Dataset] B. Ton, and Strukton Rail. [Annotated Mobile Laser Scans of the Dutch Railway Environment](https://doi.org/10.4121/fa259c52-a585-420c-8a0c-af5e91518e29.v1), *4TU.ResearchData*, 2024
