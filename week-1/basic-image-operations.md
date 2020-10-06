# Basic Image Operations

## Import Necessary Libraries and Setup 
```
# Import libraries
import cv2
import numpy as np
from dataPath import DATA_PATH
import matplotlib.pyplot as plt
%matplotlib inline
import matplotlib
matplotlib.rcParams['figure.figsize'] = (6.0, 6.0)
matplotlib.rcParams['image.cmap'] = 'gray'
```

## Copying and creating new images
### Creating a Copy Of an Image
```
imageCopy = image.copy()
```

### Creating a Completely Black Image

```
blackImage = np.zeros((100,200,3),dtype='uint8')
plt.imshow(blackImage)
```

### Creating a Completely White Image
```
whiteImage = 255*np.ones((100,200,3), dtype='uint8')
plt.imshow(whiteImage)
```

### Create a empty matrix of the same size as original image
```
greyImage = 150 * np.ones_like(original_image)
plt.imshow(greyImage)
plt.colorbar()
```

<br>

## Cropping an image section
```
startRow = 10
stopRow = 100
startCol = 10
endCol = 100
crop = image[startRow:stopRow,startCol:endCol]
plt.imshow(crop[:,:,::-1])
```
<br>

## Resizing an Image
```
resizedImage =   cv2.resize( src, dsize[, dst[, fx[, fy[, interpolation]]]]  )
```

### Parameters for *cv2.resize*
- `src` - input image
- `dsize` - output image size  (tuple *(resizedWidth, resizedHeight)*)
- `fx` - scale factor along the horizontal axis;
- `fy` - scale factor along the vertical axis; **Either dsize or both fx and fy must be non-zero.**
- `interpolation` - interpolation method ( Bilinear / Bicubic etc ).

[Interpolation values](https://docs.opencv.org/4.1.0/da/d54/group__imgproc__transform.html#ga5bb5a1fea74ea38e1a5445ca803ff121)


#### dsize example:
```
# Set rows and columns
resizeDownWidth = 300
resizeDownHeight = 200
resizedDown = cv2.resize(image, (resizeDownWidth, resizeDownHeight), interpolation= cv2.INTER_LINEAR)

# Note aspect ratio is not going to be preserved in this example
resizeUpWidth = 700
resizeUpHeight = 1000
resizedUp = cv2.resize(image, (resizeUpWidth, resizeUpHeight), interpolation= cv2.INTER_LINEAR)


#Displaying images
plt.figure(figsize=[15,15])
plt.subplot(131);plt.imshow(image[:,:,::-1]);plt.title("Original Image")
plt.subplot(132);plt.imshow(resizedUp[:,:,::-1]);plt.title("Scaled Up Image")
plt.subplot(133);plt.imshow(resizedDown[:,:,::-1]);plt.title("Scaled Down Image")

```
**Note:**
The scaled down image has less information in it so because we are displaying both images on the same figure size the scaled down image will be less sharp. 

<br>

#### Scaling example
```
# Scaling Down the image 1.5 times by specifying both scaling factors
scaleUpX = 2
scaleUpY = 2

# Scaling Down the image 0.6 times specifying a single scale factor.
scaleDown = 0.5

scaledDown = cv2.resize(image, None, fx= scaleDown, fy= scaleDown, interpolation= cv2.INTER_LINEAR)

scaledUp = cv2.resize(image, None, fx= scaleUpX, fy= scaleUpY, interpolation= cv2.INTER_LINEAR)

# Display images
plt.figure(figsize=[15,15])
plt.subplot(121);plt.imshow(scaledDown[...,::-1]);plt.title("Scaled Down Image, size = {}".format(scaledDown.shape[:2]));
plt.subplot(122);plt.imshow(scaledUp[...,::-1]);plt.title("Scaled Up Image, size = {}".format(scaledUp.shape[:2]));
```

<br>

## Creating binary masks for images
Typically with masks white pixels identify pixels we are interested in and black pixels identify pixels that we are not interested in.

### Create a mask specifying coordinates
```
# Create an empty image of same size as the original
mask1 = np.zeros_like(image)
plt.imshow(mask1)

# Set appropriate pixels as white to signify importance
mask1[50:200,170:320] = 255

# Display mask alongside image
plt.figure(figsize=[15,15])
plt.subplot(121);plt.imshow(image[:,:,::-1]);plt.title("Original Image")
plt.subplot(122);plt.imshow(mask1[:,:,::-1]);plt.title("Mask")
```

### Create a mask based on pixel intensities and color 

If we want to focus on *red pixels*, the simplest logic that does the trick is setting bounds using the `cv2.inRange` function:
- The red channel should have high intensity ( keep the range of pixel values from 100 to 255 )
- The other 2 channels should have low intensity ( keep the range of pixel values in Blue and Green channels between 0 to 100)

mask    =    cv2.inRange(    src, lowerb, upperb[, dst]    )

#### Parameters
- `src` - first input array.
- `lowerb` - inclusive lower boundary array or a scalar.
- `upperb` - inclusive upper boundary array or a scalar.

It produces a output array/binary image ( pixels are either black or white ), of the same size as src and CV_8U type. 

```
mask2 = cv2.inRange(image, (0,0,150), (100,100,255))
plt.figure(figsize=[15,15])
plt.subplot(121);plt.imshow(image[...,::-1]);plt.title("Original Image")
plt.subplot(122);plt.imshow(mask2);plt.title("Masked Image")
```

