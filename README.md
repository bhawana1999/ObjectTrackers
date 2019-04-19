# ObjectTrackers

In this repo, several openCV trackers are studied- different trackers available in OpenCV 3.4.1 — BOOSTING, MIL, KCF, TLD, MEDIANFLOW, GOTURN, MOSSE and CSRT.

Locating an object in successive frames of a video is called tracking.

There are several terms related to object tracking :-

<b>Dense Optical flow</b>: These algorithms help estimate the motion vector of every pixel in a video frame.

<b>Sparse optical flow</b>: These algorithms, like the Kanade-Lucas-Tomashi (KLT) feature tracker, track the location of a few feature points in an image.
    
    
<b>Kalman Filtering</b>: A very popular signal processing algorithm used to predict the location of a moving object based on prior motion information. One of the early applications of this algorithm was missile guidance! Also as mentioned here, “the on-board computer that guided the descent of the Apollo 11 lunar module to the moon had a Kalman filter”.
    
    
<b>Meanshift and Camshift</b>: These are algorithms for locating the maxima of a density function. They are also used for tracking.
    
    
<b>Single object trackers</b>: In this class of trackers, the first frame is marked using a rectangle to indicate the location of the object we want to track. The object is then tracked in subsequent frames using the tracking algorithm. In most real life applications, these trackers are used in conjunction with an object detector.
    
    
<b>Multiple object track finding algorithms</b>: In cases when we have a fast object detector, it makes sense to detect multiple objects in each frame and then run a track finding algorithm that identifies which rectangle in one frame corresponds to a rectangle in the next frame.

<b>Centroid Tracking</b>:  It relies on the Euclidean distance between (1) existing object centroids (i.e., objects the centroid tracker has already seen before) and (2) new object centroids between subsequent frames in a video.

<b>Why is tracking better than detection?</b>

<b>1. Tracking is faster than detection </b>- The reason is simple. When you are tracking an object that was detected in the previous frame, you know a lot about the appearance of the object. You also know the location in the previous frame and the direction and speed of its motion. So in the next frame, you can use all this information to predict the location of the object in the next frame and do a small search around the expected location of the object to accurately locate the object. A good tracking algorithm will use all information it has about the object up to that point while a detection algorithm always starts from scratch. A great example to understand this approach is whycon markers versus aruco markers. 
Whycon marker is faster than aruco marker because whycon marker are tracked while aruco are detected.


![alt text](https://github.com/sona-19/ObjectTrackers/blob/master/aruco.png)
Aruco Marker


![alt text](https://github.com/sona-19/ObjectTrackers/blob/master/whycon.png)
Whycon marker


<b>2. Tracking can help when detection fails:</b> If you are running a face detector on a video and the person’s face get’s occluded by an object, the face detector will most likely fail. A good tracking algorithm, on the other hand, will handle some level of occlusion.

<b>3. Tracking preserves identity:</b> The output of object detection is an array of rectangles that contain the object. However, there is no identity attached to the object. For example, in the video below, a detector that detects red dots will output rectangles corresponding to all the dots it has detected in a frame. In the next frame, it will output another array of rectangles.
