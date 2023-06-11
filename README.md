# pool-detection-from-satellite
detect pools from satellite image

Since polls are blue and, it is better to work with blue band chanell so we can more easly detect polls.


    with rasterio.open('poll.png') as src:
    
    visible_blue = src.read(3)

![](C:\Users\selmank\Desktop\işte.png)

Then,I graph the histogram values to select the threshold value for the bianry image so we can easly detect polls.


    plt.hist(visible_blue.ravel(), bins=256, range=(0, 256), color='blue')



From the histogram I selected the threshold as 240 and plotted a binary image.



    thresh_value = 240
    ret, thresh = cv2.threshold(visible_blue, thresh_value, 255, cv2.THRESH_BINARY)



NOW,  I will use morphological operations to expand white color, so  first I will use dilation operation which is used to expand the boundaries of regions of foreground pixels or to join neighboring regions of foreground pixels.

We can choose optimal kernel for dilation ,i tried different one and dedicated to use this one :


    # Define the kernel for dilation
    kernel = np.ones((15,15), np.uint8)
    # Perform dilation operation
    dilation = cv2.dilate(thresh, kernel, iterations = 1)



We can also use closing ,that is used to fill gaps and smooth boundaries of objects in binary images 

We can also change kernel but i choose to use this:




    # Define the kernel for closing
    kernel = np.ones((15,15), np.uint8)
    # Perform closing operation
    closing = cv2.morphologyEx(thresh, cv2.MORPH_CLOSE, kernel)


For this part ,I used closing operation. I labeled regions and if they are greater than specific area(I decided to use area=13 after empirical results ) i drawed a dot at the centroid of the pools.


This algorith works better but still there are some polls undetected because of the shadows.


    # Apply connected-component labeling to identify regions
    labels = measure.label(closing)


    # Loop over each labeled region and draw a dot at its centroid
    for region in measure.regionprops(labels):
        for region in measure.regionprops(labels) :
         area = region.area
         if area > 13:
         # Get centroid coordinates
            y, x = region.centroid
        # Draw dot at centroid on original image
            cv2.circle(img, (int(x), int(y)), 5, (0, 0, 255), -1)
    # Display the original image with centroids marked
    cv2_imshow( img)




