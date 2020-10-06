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