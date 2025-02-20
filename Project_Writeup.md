# **Behavioral Cloning** 

**Behavioral Cloning Project**

The goals / steps of this project are the following:
* Use the simulator to collect data of good driving behavior
* Build, a convolution neural network in Keras that predicts steering angles from images
* Train and validate the model with a training and validation set
* Test that the model successfully drives around track one without leaving the road
* Summarize the results with a written report


## Rubric Points
### Here I will consider the [rubric points](https://review.udacity.com/#!/rubrics/432/view) individually and describe how I addressed each point in my implementation.  

---
### Files Submitted & Code Quality

#### 1. Submission includes all required files and can be used to run the simulator in autonomous mode

My project includes the following files:
* model.py containing the script to create and train the model
* drive.py for driving the car in autonomous mode
* model.h5 containing a trained convolution neural network 
* Project_Writeup.md summarizing the results (you're reading it)

#### 2. Submission includes functional code
Using the Udacity provided simulator and my drive.py file, the car can be driven autonomously around the track by executing 
```sh
python drive.py model.h5
```
Link to my project video: [here](https://github.com/aakashkardam/Behavioral_Cloning_Udacity_Project_4/blob/master/video.mp4)

#### 3. Submission code is usable and readable

The model.py file contains the code for training and saving the convolution neural network. The file shows the pipeline I used for training and validating the model, and it contains comments to explain how the code works.

#### Approach

The project requires one to collect the data by maually driving a car in the simulator. The project depends heavily on collecting good amounts of data which is typical of deep learning problems. Training the model for the straight paths is relatively easier than the sharp turns which require more data. 

As such I focussed on collecting 2 laps of data where I tried to keep the car in the middle of the road. Later on, I ended up generating more data for sharper turns on the road.

After collecting all the data, I used data augmenting techniques like cropping the upper part of the image which basically had sky, trees, hills and the other objects in the scene which aren't relevant and more of a distraction for the model. I tried flipping images left to right along with changing signs of the corresponding measurements. I also experimented with using images from different cameras on the car, the left and the right one by adding a correction factor (0.05) to the steering measurement, adding it to the left camera image and subtracting from the right camera image.

After the collection process (including augmenting), I had 39138 number of data points (samples). I finally randomly shuffled the data set and put 20% of the data into a validation set. So, finally I trained on 31310 samples and validated on 7828 samples.

I used this training data for training the model. The validation set helped determine if the model was over or under fitting. I started with default 10 epochs that keras use and reduced it to 5 which was good enough to produce a model which drove the car autonomously on track 1. I used an adam optimizer so that manually training the learning rate wasn't necessary.

Once I had the data preprocessing/augmenting part done, I implemented the neural network with the architecture described below to train the model. I trained 5 models with different performances. The performance got better from the first model to the last one.

### Random Images from the Dataset
![LeftCameraImage](LeftCameraImage.png)
![CenterCameraImage](CenterCameraImage.png)
![RightCameraImage](RightCameraImage.png)
Random images from the data set captured by recording in the Udacity simulator. Images from left camera(top), center camera (middle) and right camera (bottom).
### Model Architecture and Training Strategy

#### 1. An appropriate model architecture has been employed

My model consists of a convolution neural network with  5 convolutional layers, 3 of them using 5x5 filter and the remaning 2 using 3x3 filter. I also use 4 fully connected layers with 100, 50, 10 and 1 units in them respectively. The model is summarized below in more detail.

The model includes RELU layers to introduce nonlinearity, and the data is normalized in the model using a Keras lambda layer (code line 82). 

My final model consisted of the following layers:

| Layer                         |     Description                                                                       |
|:---------------------:|:-----------------------------------------------------------------:|
| Input                         | 160x320x3 RGB image                                                                     |
| Lambda Layer          | to normalize the data |
| Cropping 2D layer     | crops the image from the top, upto 70 pixels and 25 pixels from the bottom |
| Convolutional Layer-1 | 5x5 filter, 24 units, relu activation, 2x2 subsampling |
| Convolutional Layer-2 | 5x5 filter, 36 units, relu activation, 2x2 subsampling | 
| Convolutional Layer-3 | 5x5 filter, 48 units, relu activation, 2x2 subsampling |
| Convolutional Layer-4 | 3x3 filter, 64 units, relu activation | 
| Convolutional Layer-5 | 3x3 filter, 64 units, relu activation |
| Flatten Layer         | Flattens the output from the Convolutional Layer-5 |
| Fully Connected Layer-1 | 100 units, relu activation |
| Fully Connected Layer-2 | 50 units, relu activation  |
| Fully Connected Layer-3 | 10 units, relu activation  |
| Fully Connected Layer-4 | 1 unit as output           |

the sequential model architecture described above is compiled with a mean squared error and the adam optimizer. The network is trained with 5 epochs and using default values for other parameters like learning rate. This number was reduced from 10 epochs which were initially used. Reducing epochs trained the model faster. The model used an adam optimizer, so the learning rate was not tuned manually. 

#### 2. Attempts to reduce overfitting in the model

The model was trained and validated on different data sets to ensure that the model was not overfitting. The model was tested by running it through the simulator and ensuring that the vehicle could stay on the track. It was thoroughly tested in the autonomous mode for a couple of laps on the track 1.


#### 3. Appropriate training data

Training data was chosen to keep the vehicle driving on the road. I used a combination of center lane driving, recovering from the left and right sides of the road. I used the left and the right camera images also for training. I used a correction factor of 0.05 and arrived at it after experimenting with a number of factors so that the car drives smoothly.


#### 4. Solution Design Approach

The overall strategy for deriving a model architecture was to initially see if a basic model runs and the car can move in the autonomous mode and not to worry about how badly it performs but to get the pipeline sorted and working correctly. Then the next task is to improve the model in order to make it drive smoothy.

My first step was to use a convolution neural network model similar to the LeNet as implemented in the previous project [Traffic Sign CLassifier](https://github.com/aakashkardam/Traffic_Sign_Classifier_Udacity_Project_3) and taking the hint from there, I also looked in to the model Nvidia uses for their self driving car and worked from there.

In order to gauge how well the model was working, I split my image and steering angle data into a training and validation set. I found that my first model had a low mean squared error on the training set but a high mean squared error on the validation set. This implied that the model was overfitting. 

To combat the overfitting, I modified the model so that the loss and the validation loss both decreased up untill the last epoch.

The final step was to run the simulator to see how well the car was driving around track one. There were a few spots where the vehicle fell off the track and went off road. This happened quite a lot at the sharp turns. This suggested that I need to collect more general data for the sharper turns/ curves on the track so I recorded more samples of data with the sharp turns both by keeping the car in the middle and near the edge of the road.

At the end of the process, the vehicle is able to drive autonomously around the track without leaving the road. The images below show some of the snapshots from the video created in autonomous mode.

![AutonomousMode](AutonomousModeImages.png)
The images are snapshots taken from the video created in the fully autonomous mode on the track1.

#### 5. Conclusion

I learnt quite a lot on this project, especially how important it is to get good data for training. Despite having all the elements of your Neural Network architecture right in place, if you don't have a good enough data to compliment it the results are going to be far from perfect. In essence I learnt how each component of the workflow in a typical deep learning problem is extermely crucial an has to be in good coherence with each other. 

I have also included the samplerun.log file in case anyone wants to look at the output. The corresponding model file is also added to this repository titled `model_sample.h5`.

I also faced a lot of difficulties like memory issues when the size of dataset increased to more than 50000 images, some of these issues are outlined and discussed [here](https://stackoverflow.com/questions/43147983/could-not-create-cudnn-handle-cudnn-status-internal-error). As such I reduced the training data to only 2 laps and additional data for the sharp turns. I ended up training my model on 31310 samples and validate on 7828 samples as discussed before.

When using the images from the multiple cameras : left and right. It needs a little bit of experimentation to tune the steering correction factor and requires lot of patience.

In general the sharp turns on the track was a challenge. I have tested my model on the track 2 but it doesn't perform as well and needs further improvement and better data collection. This is one of the near future goals to achieve fully autonomous level on the track 2.
