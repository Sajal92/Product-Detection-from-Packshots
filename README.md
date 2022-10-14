# Product-Detection-from-Packshots
**Overview of the solution provided for Problem 1:**

*Firstly the products are cropped from the product_images and stored in a different
folder named ‘cropped product images’. The classical algorithm was used to detect
the object in the image. The algorithm works as follows. Firstly, the product image is
blurred using a gaussian blur and following which the edges are detected using
canny edge detector. After this the contours are detected on the image using an
inbuilt function in OpenCV. These contours are sorted and the largest contour
,corresponding to the largest area, represents the product inside the image. In this
way the product is segmented from all the images in product_images. The result for
this step are shown in the image below for the product image qr281.jpg
![alt text](https://github.com/Sajal92/Product-Detection-from-Packshots/blob/main/sample_crop.png)

* After obtaining the product segment from each of the product images individual
product is matched with each and every image in the shelf_images folder. For this
also I used classical computer vision algorithm called feature matching. It works as
follows, firstly we find keypoints and descriptors for both the product image and the
shelf image and then use FLANN based matcher to look for matching product
descriptors inside the shelf image, if there’s any. In this way if there’s any match
found then the corresponding IDs of product and shelf image, including the max min
va;ues of the bounding box, are dumped into solutions.txt file. The result for this step
are shown in the image below for product image qr281.jpg and shelf image db1662.jpg
![alt text](https://github.com/Sajal92/Product-Detection-from-Packshots/blob/main/sample_output.png)

Pros:
* Can handle different scales of product and shelf image. The product inside the shelf
image need not be of the same size as inside the product image.
* Can handle rotated and skewed images of the product.
* More robust when compared to template matching.
* Unsupervised method therefore no need of data annotation.

Cons:
* Since the algorithm uses knn matching it is very slow.

**Further improvements to the above algorithm:**
* In the step 1 of the above algorithm rather than using the contour method we can use
foreground and background technique to segment product from the product_image.
These algorithm will perform better since the product inside the product_image is in
clear foreground compared to te relatively white background. This accurate segment
can then be used in matching algorithm proposed above.

**The best solution which comes to my mind that can solve both problem 1 and
problem 2:**
* Firstly annotate the product images by drawing the bounding boxes for each product
using an object detection labeling software i.e labelImg. This operation will be very
fast since each product image contains only one product.

* Once you have annotated each product image, then comes the process of
generating training images for our deep learning model (preferably fine tuning an
already available model from tensorflow zoo to our specdific problem). It mainly
involves following steps:
  * Take a blank image of a particular dimension say 500 X 500●
Divide it into 100(10 X 10) sections of size 50 X 50 each or divide it into
400(20 X 20) sections of size 25 X 25 each. This division could further vary
depending on the variations we want in our training images
  * Now take the cropped annotated product and resize it to the the section size
mentioned in the above step. There is one thing to be remembered here, this
cropped annotated part should also be padded with some pixels(these pixels
act as a background and simulate the space between products in real
scenario) before placing it into above mentioned sections. Also, the cropped
annotated part can be augmented(i.e rotated, skewed, blurred etc.) before
being padded and placed into the sections. In this way we can simulate the
real world scenario where product might not be placed upright. It also solves
the problem of product being placed tilted inside the product image.
  * Following the above steps we can generate a lot of training images.

* Fine tune a state of the art object detection model to our training images and
continuously monitoring the model performance on test images. Once the desired
metric score is achieved we can deploy the model on server or on edge devices.

Pros of the above proposed method:
* Very fast to generate a lot of annotated training images when compared to annotating
whole of the shelf images for individual product.
* Very easy to push the model accuracy higher once the training and test loss starts to
decrease.

Cons:
* Not an end to end model
