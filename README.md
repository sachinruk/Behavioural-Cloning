<script src="http://api.html5media.info/1.1.8/html5media.min.js"></script>
# Behavioural Cloning
[![Udacity - Self-Driving Car NanoDegree](https://s3.amazonaws.com/udacity-sdc/github/shield-carnd.svg)](http://www.udacity.com/drive)

## Data Gathering and Training Methods
The data was gathered by driving the simulated car around the track three times. The first time around I attempted to keep the car towards the right of the lane (as is done under the American lane system). The trained system from this dataset had some undesired affects such as a tendency to go outside the marked lanes since it was close to the edge. During the second run it was attempted to keep the car more towards the centre of the lane. The final iteration of the track was done by taking the car in the reverse direction, ensuring that it had enough right turns to train on.

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

The Dense layer contained a `tanh` function to ensure that the output was between -1 and 1. The code did employ a Dropout layer (with `p=0.5`) between the Flatten layer and Dense layer. However, in hindsight this could have been used between other layers as well.

See `model.ipynb` for code.

## Model Training
In order to train the model, we also used the left-right flipped images. In doing so we doubled the number of training instances that we had. The steering angle of the flipped image was simply the negative of the existing steering angle.

A python generator function was used to load data into memory (one batch at a time). This was due to the fact that we could not fit all data into memory given that there were ~27000 images (including the flipped images).

5% of the data was taken to be a validation set using scikit learn's `train_test_split` function (~1200 images). After two epochs of training the train MSE was 0.0573 while the validation set MSE was 0.0570.

## Result:
https://youtu.be/fHwPvo-PEi4

<video src="./video.mp4" width="320" height="160" controls preload></video>
