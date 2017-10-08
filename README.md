# Behavioural Cloning
[![Udacity - Self-Driving Car NanoDegree](https://s3.amazonaws.com/udacity-sdc/github/shield-carnd.svg)](http://www.udacity.com/drive)

The goal of this project was to automate the driving of a simulated car around a track. This is done by emulating the behaviour of a human driver.

Initially the car was driven around the track once and the steering angles along with the front view of the car was recorded. The task when broken down in terms of Deep Learning was to learn the optimal steering angle given the front view of the car.

The initial attempt was a two layer Deep Neural network where the two layers were CNNs. Each layer consisted of 4 5x5 kernels followed by a Relu activation. This was connected to the output layer as a simple Dense layer with one output node with no activation. The network was trained for one Epoch and proved to be quite jittery during testing of the car. It consistently went out of bounds of a road.

Subsequently the number of kernels was increased to 32 5x5 kernels and trained for 4 Epochs. The driving was much smoother after this training but continued to move outside the road limits. This was mainly due to the fact that there was an imbalance of left turns compared to right turns.

The final dataset was gathered by driving the simulated car around the track four times. The final two iterations of the track was done by taking the car in the reverse direction, ensuring that it had enough right turns to train on. Furthermore for each batch of training the image was flipped left to right followed by taking the negative of the steering angle. This ensured a balanced dataset in terms of left vs right steering angles. An example can be seen in Cell 5 of the Jupyter Notebook.

## Final Model:
My final model consisted of the following layers:

| Layer         		|     Description	        					|
|:---------------------:|:---------------------------------------------:|
| Input         		| 320x160x3 RGB image   							|
| Cropping | Crop 70 pixels from top and 25 from bottom image |
| Normalise | Divide by 255 and minus 0.5 from image |
| Convolution Block     	| 8 Kernels 	|
| Convolution Block     	| 16 Kernels 	|
| Convolution Block     	| 32 Kernels 	|
| Convolution Block     	| 64 Kernels 	|
| Flatten					|												|
| Dense	| Connect to one node with `tanh` activation 				|

The Convolution Block consisted of the following:
- A 3x3 kernel model.
- Relu
- A 3x3 kernel model.
- Relu
- A 2x2 Max Pooling with a stride of 2.

The convolution followed by activation was repeated twice as shown in the dot points above. This structure was **inspired by the VGG16 model**.

The image was cropped as shown above by the `Cropping` layer since the top line mainly consisted of the sky while the bottom pixels contained the bonnet of the car. Both of which would have been distracting for the model.

The Dense layer contained a `tanh` function to ensure that the output was between -1 and 1. The code did employ a Dropout layer (with `p=0.5`) between the Flatten layer and Dense layer. However, in hindsight this could have been used between other layers as well.

See `model.ipynb` for code.

## Model Training
In order to train the model, we also used the left-right flipped images. In doing so we doubled the number of training instances that we had. The steering angle of the flipped image was simply the negative of the existing steering angle.

A python generator function was used to load data into memory (one batch at a time). This was due to the fact that we could not fit all data into memory given that there were ~32000 images (including the flipped images).

5% of the data was taken to be a validation set using scikit learn's `train_test_split` function (~1600 images). After three epochs of training the train MSE was 0.049 while the validation set MSE was 0.055.

## Result:
https://youtu.be/vflrrUSm4Js

<video src="./video.mp4" width="320" height="160" controls preload></video>
