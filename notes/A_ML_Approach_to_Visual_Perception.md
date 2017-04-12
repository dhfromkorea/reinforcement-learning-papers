# Paper

* **Title**: A Machine Learning Approach to Visual Perception
of Forest Trails for Mobile Robots
* **Authors**: Alessandro Giusti et al
* **Link**: http://rpg.ifi.uzh.ch/docs/RAL16_Giusti.pdf
* **Tags**: Visual-Based Navigation; Aerial Robotics; Machine Learning; Deep Learning
* **Year**: 2015

## TL;DR
* Training an MAV (=small flying robots) so it learns to perceive trails (e.g. forest/dessert) is hard. Existing research approached this as a segmentation problem (figuring out trail boundaries) using a solution that relied on handcrafted features (e.g. image saliency). 
* The authors made the trail perception problem easier to solve by reframing it into an image classification problem (the model decides whether to turn left or right, or go straight) and by using Deep Neural Network that obviated the need for handcrafted features.
* Essentially the authors applied an Imitation/Apperenticeship Learning, where the hiker (human expert) is an optimal(target) policy that supervises MAVs with ground truth labels the hiker created himself. I think this was a good decision because the reward function would be hard to formulate and the typical trial and error approach would be too costly to perform physically; a failure case may destory a vehicle.  
* Tricks: 
    * (1): used three cameras to remedy non-iidness. 
    * (2): used human instead of a robot so data are collected cheaply.
* Model Architectures: CNN-based Deep Neural Network.
* Experiments/Results: With the test data, the authors' model achieved a classification accuracy comparable to humans' and better than previous systems. The authors have also done real-world field tests with two Quadrotors in which they navigated pretty well. During the field tests, the authors identified a few practical concerns that may deteriorate the performance. 
* This [demo video](http://people.idsia.ch/~giusti/forest/web/) sums it all up.


## Chapterwise notes
### Introduction
* In order to successfully follow a forest trail, a robot has to perceive where the trail (the dominant direction of the trail from the robot's current position) is and react (steer in a certain direction) in order to stay on the trail.
* Previous works solved a trail "segmentation" problem. To do this, one needs to explicitly define which visual features characterize a trail. Rasmussen et al. relied on appearance contrast, whereas Santana et al. adopted image conspicuity. Both center around the idea of image saliency which measures how much a pixel stands out from the rest. 
* The segmentation/saliency approach can easily become a weak solution because unpaved roads are normally much less structured than paved ones: their appearance is very variable and often boundaries are not well defined.
* Instead, the authors reframed this into an image classification problem and used DNN without using manually crafted features.
    
### Problem formulation

![alt text][problem_formulation]


* The intuition behind the formulation below is simple. For example, if you (robot) are heading towards the left too much compared to where the trail heads, you need to turn right.
* ~v represents the direction of a camera's optical axis (=the direction in which the robot's camera is facing)
* ~t represent the dominant direction of the trail. (=the ground truth direction of the trail from the robot's current position.)
* Let α be the signed angle between ~v and ~t.
* Let β be some arbitrary margin padded to ~t.
* Each input frame (with a ~v) will be classfied according to:
    * Turn Left (TL) if −90◦ < α < −β; 
    * Go Straight (GS) if −β ≤ α < +β;
    * Turn Right (TR) if +β ≤ α < +90◦;


### Visual Perception of Forest Trails
![alt text][data_collection]

* Data: 17,119 training frames and testing (7,355 frames) (augmented by mirroring, translation, scaling etc.) The split was defined by avoiding that the same trail section appears in both the training and testing set. The three classes are evenly represented within each set. (stratified?)


* Data-collection Trick: Deep Learning models are known to demand lots of data for training. To make data acquisition easier/cheaper, the authors had a human hiker collect data instead of an MAV. This is unlike Ross et al. used the controller previously trained by manually piloting the robot for a short time.

* Three-camera Trick: The data collected are non-iid and non-iidness is known to destabilize learning with compounding errors. To alleviate this risk, although not explicitly mentioned in the literature, the authors had the hiker have three cameras on his/her head facing left, center and right. Images from left and right cameras represent errors/mistakes and the output labels from them corrections. (e.g. an image from the left camera (=when steering to left too much) would be associated with Turn Right label.) 

![alt text][cnn]

* input: 3 × 101 × 101 (RGB-valued frames)
* num_layers: 9 (CNN)
* output: softmax probabilities for 3 classes
* The DNN is trained using backpropagationfor 90 epochs. The learning rate started at 0.005 and decayed by 5% per epoch. Weights were initialized by sampling from uniform distribution. Activations used were tanh and the final activation function used was softmax.

### Experimental results
![alt text][result]

* benchmark models: two models that solves a segmentation problem (saliency-based). two Human classifiers.
    * (1) Simple Saliency-based Model: The input image is computed on its hue dimension only to a saliency map which then discretized into blocks. Each block forms a 144-dimensional feature vector. The feature vectors runs with an RBF Kernel SVM model.
    * (2) A more sophisticated algorithm than (1) was applied to the input image and the output was the segmentations of trails. The authors computed a representative coordinate of such segment (centroid of the largest component in the binarized trail probability map) and mapped the point to class. If the coordinate of the centroid (=trail), x, is larger(=to the right) than (0.5 + k)*image_width (the center of the image), x is mapped to Turn Right. (k is a hyperparameter)
    * (3) each of which is asked to classify 200 randomly sampled images from the testing set in one of the three classes
* Failure Cases and Qualitative Results: the authors found the instances which are easy for our system are also trivial for human observers, whereas instances that the DNN failed (third and fourth row) are in fact difficult and ambiguous cases also for humans.

* Field test: The authors tested on  1) A Parrot ARDrone controlled by a laptop (Figure 8, left). 2) A standalone quadrotor. The MAVs tested were controlled by two variables: yaw(steering)and speed. Yaw was defined proportional to P(TR) - P(TL). Speed to P(GS). The main problem they faced was due to poor quality of the actual test camera images vs. the GoPro images acquired by the human hiker. The authors also observed the MAVs often crash to obstacles when trails are narrow which is natural as the system was not trained for obstacle detection. 



[data_collection]: ../img/mobile_robot_hiker_data_acquisition.png ""
[cnn]: ../img/mobile_robot_cnn.png ""
[problem_formulation]: ../img/mobile_robot_ground_truth.png ""
[result]: ../img/mobile_robot_result.png ""