---
title: "YOLO horse detection"
date: 2019-11-11
date_human: "End of 2019"
draft: false
summary: "Horse detection as part of an event information system."
web: "https://techforfuture.nl/project/event-information-system-military-boekelo"
---

# Description
<!--- Situation --->
The Military in Boekelo, The Netherlands is one of the largest outdoor equine events. The course is spread out over a large area with several jumping obstacles along the way. Due to the large area it is impossible for spectators to watch their favourite rider jump over all obstacles. The goal of the project was to create a real-time, tailored, app-based  event information system. Part of this system were intelligent cameras. A key element of the intelligent camera was the automatic detection of horses with riders.

<!--- Task/Action --->
To enable this, I fine-tuned the YOLO object detection model to detect horses with riders.

<!--- Result/Impact --->
Results from the object detection model are used to create automated panning shots of riders passing by the obstacles. These shots can be streamed to the mobile devices of interested end users. Using the realised event information system spectators are able to watch their favourite rider jump all obstacles around the course.

# Video
The below video shows the YOLOv3 object detection model in action. Notice the large scale range at which the model is still able to accurately detect the horse with rider.

{{< youtube VLf1wmP04Xw >}}
