
##Video Summary

Watch this video for the final outcome of this project! 

[![IMAGE ALT TEXT HERE](https://img.youtube.com/vi/6uKrz9iPvY4/0.jpg)](https://www.youtube.com/embed/6uKrz9iPvY4)

<iframe width="854" height="480" src="https://www.youtube.com/embed/6uKrz9iPvY4" frameborder="0" allowfullscreen></iframe>

##The Project

The goals / steps of this project are the following:
* Use the simulator to collect data of good driving behavior
* Build, a convolution neural network in Keras that predicts steering angles from images
* Train and validate the model with a training and validation set
* Test that the model successfully drives around track one without leaving the road
* Summarize the results with a written report

##Files

The main files included for this submission are 

```sh
model.py
model.h5
run2.mp4
writeup_report.md
```

Using the drive.py and the beta_simulator provided by Udacity, the car can be driven autonomously by executing
```sh
python drive.py model.h5
```
##Model Architecture 

I used the nVidia End-to-End Deep Learning Network for my model. However, I did not change the input image sizes to match with what NVidia used, and kept it the same as what came out of the simulator (160x320)

My Keras model looks like this:

```sh
   model = Sequential()
   model.add(Lambda(lambda x: x/255.0 - 0.5, input_shape=(160,320,3)))
   model.add(Cropping2D(cropping=((70,25),(0,0)))) # 65 x 320

   model.add(Convolution2D(24,5,5,name='Conv1',subsample=(2,2),activation='relu'))
   model.add(Convolution2D(36,5,5,name='Conv2',subsample=(2,2),activation='relu'))
   model.add(Convolution2D(48,5,5,name='Conv3',subsample=(2,2),activation='relu'))
   model.add(Convolution2D(64,3,3,name='Conv4',subsample=(1,1),activation='relu'))
   model.add(Convolution2D(64,3,3,name='Conv5',subsample=(1,1),activation='relu'))
   
   model.add(Flatten())
   model.add(Dense(1164))
   model.add(Dropout(DROPOUT))
   model.add(Dense(100))
   model.add(Dropout(DROPOUT))
   model.add(Dense(50))
   model.add(Dropout(DROPOUT))
   model.add(Dense(10))
   model.add(Dense(1))
```

##Optimization

The model uses Adam optimizer (so no manual tuning of the learning rate was necessary) and MSE loss functions. 

The dropout layers were set to 10% to control overfitting. 

##Preprocessing - Images

I focused initially on building the simplest model possible that can handle Track1.

The training data was taken from the Udacity provided dataset. I also created my own custom dataset and tried separate runs with it. One thing I learned from this exercise was that your driving style can result in significantly different training sets (do you consistently do small steering adjustments while driving, or do you create infrequent, larger steering adjustments etc)

The images were cropped 70 pixels from the top, and 25 pixels from the bottom to keep just the main features of the road. While I have studied several other steps that one could do at this stage (color/RGB processing, grayscaling, brightness/contrast adjustments, resizing, rotations) for the simple model I left the pictures as is. Only the center camera images were used for the first pass.

##Preprocessing - Steering Angles

I felt that a steering angle of 0.5 from the training run doesnt mean that a value of 0.49 or 0.52 is "incorrect" - probably they all can still do the required job done. So I added a random variation of +/- 10% to the steering angles. 

This makes the model more general and helps to reduce overfitting to precise values from the training set. 

##Data Augmentation - Images

The training data is biased to left turns - as the track has a lot more left turns than right turns. 

Inorder to reduce the bias to left turns, all images were flipped (as well as their steering angles reversed) and added to the database. This doubles the number of data points in the training set and makes the data also more generalized to both left and right turns.

##Data Normalization

After the above mentioned steps, the distribution of data looks like this

![Image](https://github.com/kiranganesh/CarND-Behavioral-Cloning-P3/blob/master/images/pic1.JPG)

Clearly, there is an over-concentration of values around the 0 angle of steering. This needs to be addressed and the data needs to be made more balanced. After reducing the overrepresented class the balanced data set looks like this:

![Image](https://github.com/kiranganesh/CarND-Behavioral-Cloning-P3/blob/master/images/pic2.JPG)

##Results

The model was relatively quick to train on AWS g2 GPU insances.

![Image](https://github.com/kiranganesh/CarND-Behavioral-Cloning-P3/blob/master/images/pic3.JPG)

With this simple model, the car did a decent job of going around Track1 for multiple laps. Occasionally the driving around sharp curves were a bit "too close" for comfort but the car stayed on the road. One improvement to make in the future would be to make the driving more centered even around curves.

The model however did not work well on Track2. One obvious enhancement that I need to make in the training is to account for the sloping roads and uneven horizons. I plan to tackle this as a separate challenge aside from the submission.

##Commentary

It was immensely satisfying to build a simple deep learning model that could drive the simulator car on its own. This example shows the power of convolutional neural networks and how effective they can be. This new paradigm of teaching cars to drive (compared to the robotic model) really showcases the power of deep learning. 

After having gone through the Traffic Sign recognition exercise with Tensorflow, Keras was a real pleasure to work with. Keras makes the construction of the model extremely simple, putting the programmer focus on engineering the solution and working with the data - versus getting lost in the weeds of building the correct model. 

And lastly, the biggest learning is that its all about the data. Garbage in, garbage out. I suspect that multiple models can get a reasonable level of accuracy, but without the right data (right content, right normalization, right randomization) etc the models wont be very useful.

##Acknowledgements 

I learned a ton from David Silver's hands on session on youtube - they were super useful to get me started quickly.

Multiple comments on Slack were extremely insightful as well. Its great to have a community like this to accelerate your learning!

