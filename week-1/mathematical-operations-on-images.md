# Mathematical operations on images
## Datatype conversion
 For most Arithmetic operations like multiplication or division, we need to convert the array type to floating point with each pixel having 16, 32 or 64 bits of data, meaning each color channel could have 2^n possibilities of value. This helps prevent overflow while working with the images.

Most of the time we don't need to work using float64 and float32 is enough for most image processing operations ( even float16 ).

Once we convert an image from a uint8 to a float32 data type we need to scale the values of the image accordingly. 
- The scale or the scaling factor reduces the range of 0-255 to 0-1, hence all the floating point numbers now range between 0-1. 
- When we convert back to unsigned integer we use the reciprocal of the scaling factor and the range is converted from 0-1 to 0-255.

```
# Read image
image = cv2.imread(os.path.join(DATA_PATH,"images/boy.jpg"))

scalingFactor = 1/255.0

# Convert unsigned int to float
image = np.float32(image)
# Scale the values so that they lie between [0,1]
image = image * scalingFactor

#Convert back to unsigned int
image = image * (1.0/scalingFactor)
image = np.uint8(image)
```

## Contrast Enhancement
**Contrast** is approximately the difference in intensity between the brightest and darkest regions of a given image. The higher the difference, the higher the contrast. 

The maximum contrast of an image is also known as **Dynamic Range**. 

One of the easiest ways to improve contrast of an image is Intensity Scaling:

$I_0 = alpha * I$

```
contrastPercentage = 20

# Multiply with scaling factor to increase contrast
contrastHigh = image * (1+contrastPercentage/100)

# Display the outputs
plt.figure(figsize=[20,20])
plt.subplot(121);plt.imshow(image[...,::-1]);plt.title("original Image");
plt.subplot(122);plt.imshow(contrastHigh[...,::-1]);plt.title("High Contrast");
```

**Note:**
We will have an error due to the maximum value of a uint8 data type image. We are overflowing and loosing information.

![High Contrast overflow error due to using wrong data type](./data/high-contrast-overflow.png)

### Solutions is to normalize intensity values 

1. Clip or Normalize the intensity values to 0 ~ 255 and change the data type to uint8.
2. If you want to keep the image in float format, then Normalize the intensity values so that it lies in [0,1].

```
contrastPercentage = 30

# Clip the values to [0,255] and change it back to uint8 for display
contrastImage = image * (1+contrastPercentage/100)
clippedContrastImage = np.clip(contrastImage, 0, 255)
contrastHighClippedUint8 = np.uint8(clippedContrastImage)

# Convert the range to [0,1] and keep it in float format
contrastHighFloat = image * (1+contrastPercentage/100.0)
maxValue = image.max()
contrastHighNormalized01 = contrastHighFloat/maxValue

plt.figure(figsize=[20,20])
plt.subplot(131);plt.imshow(image[...,::-1]);plt.title("original Image");
plt.subplot(132);plt.imshow(contrastHighClippedUint8[...,::-1]);plt.title("converted back to uint8");
plt.subplot(133);plt.imshow(contrastHighNormalized01[...,::-1]);plt.title("Normalized float to [0, 1]");

```


![High Contrast solution with normalizing](./data/high-contrast-solution.png)

