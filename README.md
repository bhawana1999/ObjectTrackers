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


<b> Object Tracking Algorithms </b>

Since we have tracked the object up until the current frame, we know how it has been moving. In other words, we know the parameters of the motion model. The motion model is just a fancy way of saying that you know the location and the velocity ( speed + direction of motion ) of the object in previous frames. If you knew nothing else about the object, you could predict the new location based on the current motion model, and you would be pretty close to where the new location of the object is.

But we have more information that just the motion of the object. We know how the object looks in each of the previous frames. In other words, we can build an appearance model that encodes what the object looks like. This appearance model can be used to search in a small neighborhood of the location predicted by the motion model to more accurately predict the location of the object.

   <i> The motion model predicts the approximate location of the object. The appearance model fine tunes this estimate to provide a more accurate estimate based on appearance.</i>


Based on the online training method, various algorithms are classified as:-

<b>BOOSTING Tracker</b>

This tracker is based on an online version of AdaBoost — the algorithm that the HAAR cascade based face detector uses internally. This classifier needs to be trained at runtime with positive and negative examples of the object. The initial bounding box supplied by the user ( or by another object detection algorithm ) is taken as the positive example for the object, and many image patches outside the bounding box are treated as the background. Given a new frame, the classifier is run on every pixel in the neighborhood of the previous location and the score of the classifier is recorded. The new location of the object is the one where the score is maximum. So now we have one more positive example for the classifier. As more frames come in, the classifier is updated with this additional data.

<b>MIL Tracker</b>

This tracker is similar in idea to the BOOSTING tracker described above. The big difference is that instead of considering only the current location of the object as a positive example, it looks in a small neighborhood around the current location to generate several potential positive examples. You may be thinking that it is a bad idea because in most of these “positive” examples the object is not centered.

This is where Multiple Instance Learning ( MIL ) comes to rescue. In MIL, you do not specify positive and negative examples, but positive and negative “bags”. The collection of images in the positive bag are not all positive examples. Instead, only one image in the positive bag needs to be a positive example! In our example, a positive bag contains the patch centered on the current location of the object and also patches in a small neighborhood around it. Even if the current location of the tracked object is not accurate, when samples from the neighborhood of the current location are put in the positive bag, there is a good chance that this bag contains at least one image in which the object is nicely centered. 

<b>KCF Tracker</b>

KFC stands for Kernelized Correlation Filters. This tracker builds on the ideas presented in the previous two trackers. This tracker utilizes that fact that the multiple positive samples used in the MIL tracker have large overlapping regions. This overlapping data leads to some nice mathematical properties that is exploited by this tracker to make tracking faster and more accurate at the same time.

<b>TLD Tracker</b>

TLD stands for Tracking, learning and detection. As the name suggests, this tracker decomposes the long term tracking task into three components — (short term) tracking, learning, and detection. From the author’s paper, “The tracker follows the object from frame to frame. The detector localizes all appearances that have been observed so far and corrects the tracker if necessary. The learning estimates detector’s errors and updates it to avoid these errors in the future.” This output of this tracker tends to jump around a bit. For example, if you are tracking a pedestrian and there are other pedestrians in the scene, this tracker can sometimes temporarily track a different pedestrian than the one you intended to track. On the positive side, this track appears to track an object over a larger scale, motion, and occlusion. If you have a video sequence where the object is hidden behind another object, this tracker may be a good choice.


<b>MEDIANFLOW Tracker</b>

Internally, this tracker tracks the object in both forward and backward directions in time and measures the discrepancies between these two trajectories. Minimizing this ForwardBackward error enables them to reliably detect tracking failures and select reliable trajectories in video sequences.


<b>GOTURN tracker</b>

Out of all the tracking algorithms in the tracker class, this is the only one based on Convolutional Neural Network (CNN). From OpenCV documentation, we know it is “robust to viewpoint changes, lighting changes, and deformations”. But it does not handle occlusion very well.

<b>MOSSE tracker</b>

Minimum Output Sum of Squared Error (MOSSE) uses adaptive correlation for object tracking which produces stable correlation filters when initialized using a single frame. MOSSE tracker is robust to variations in lighting, scale, pose, and non-rigid deformations. It also detects occlusion based upon the peak-to-sidelobe ratio, which enables the tracker to pause and resume where it left off when the object reappears. MOSSE tracker also operates at a higher fps (450 fps and even more). To add to the positives, it is also very easy to implement, is as accurate as other complex trackers and much faster. But, on a performance scale, it lags behind the deep learning based trackers.

<b>CSRT tracker</b>

In the Discriminative Correlation Filter with Channel and Spatial Reliability (DCF-CSR), we use the spatial reliability map for adjusting the filter support to the part of the selected region from the frame for tracking. This ensures enlarging and localization of the selected region and improved tracking of the non-rectangular regions or objects. It uses only 2 standard features (HoGs and Colornames). It also operates at a comparatively lower fps (25 fps) but gives higher accuracy for object tracking.
