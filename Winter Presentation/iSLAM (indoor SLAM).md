# iSLAM (indoor SLAM)


Computer vision is everywhere today, and this is far from an overestimation. There are a lot of apps using this technology in all industries: from medical treatment to traffic monitoring. Such products make our lives much easier, which is why they are so popular. 

One of the most popular CV challenge is a SLAM. **SLAM (simultaneous localization and mapping)** is a method of map constructing of the area by processing video captured by a moving camera. 

We can divide SLAMs eit her by the area of usage: Outdoor and Indoor, or by the main algorithms: sparse, dense or semi-dense.

- OUTDOOR for creating maps of entire streets, regions, and even cities. **Sparse SLAM** is the most convenient for the outdoor usage. In sparse SLAM we can ignore a lot of information from image and only extract main features.

> **Feature** is an component of the picture with eye-catching geometry. They are convenient for detecting movement between frames.
> 
- INDOOR for creating the most accurate plan of a room. In this case we need a lot of information from every frame so we use **dense SLAM** or **semi-dense SLAM** methods. Usually these algorithms assume the whole frame as a feature and find a lot of significant elements (i.e. surfaces or borders, depth map for the whole frame). The algorithms are more robust but at the same time they’re heavy in terms of computational requirements.

The goal of our project is to develop software that constructs 3D map of the room based on the video from a mono camera. This goal can be reached using Indoor SLAM.

The challenge of mapping is relevant in many industries, especially in the industry of autonomous driving systems for navigation and collision avoidance. 3D models of rooms and streets allow you to create virtual tours and models for education, entertainment, marketing, and make it possible to analyze spatial geometry in construction.

As a result we want to develop software that gets video and returns generated map in some popular map format or in our own specified format if it will be necessary. For image processing and computing algorithms we will use the latest version of the open source OpenCV computer vision algorithm library (4.8).

## **iSLAM team:**

> **Kozlov Kirill, Shalygin Igor, Tsoy Anton**
> 

[**Vision**](https://docs.google.com/document/d/1eW_hydVhcLtVoHmuoEjj_d-RU1TVpqTfTVsQzkhEK6M/edit)