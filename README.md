# proj4

#  Overview

During this project, I created mosaics of various images by selecting correspondence points, and computing the corresponding homographies between the matrices.

#  Part 1. Taking Pictures

I started by taking several images of various environments, keeping the center of projection as close as possible, but rotating the camera just slightly to capture new information, with the eventual goal of stitching these images together to create a mosaic / panoramic.

| Mother Image 1 | Me Image 2 | 
|:-------------------------:|:-------------------------:|
|<img width="400" src="ma_delaunay.png"> |  <img width="400" src="cam_delaunay.png"> |

| Mother Image 1 | Me Image 2 | 
|:-------------------------:|:-------------------------:|
|<img width="400" src="ma_delaunay.png"> |  <img width="400" src="cam_delaunay.png"> |

| Mother Image 1 | Me Image 2 | 
|:-------------------------:|:-------------------------:|
|<img width="400" src="ma_delaunay.png"> |  <img width="400" src="cam_delaunay.png"> |

#  Part 2. Defining Homographies

After shooting these pairs of images, I selected 8 correspondence points for each image.
I then wrote a function to compute the homography matrix, given a set of 4+ points for two images. 
Here's an example of correspondence points I picked for the nature images:

| Nature Image 1 Correspondence Points | Nature Image 2 Correspondence Points | 
|:-------------------------:|:-------------------------:|
|<img width="400" src="ma_delaunay.png"> |  <img width="400" src="cam_delaunay.png"> |

Due to having 4+ correspondences, we can set up an overdetermined systems of equations, where we can solve for H, which is a 3x3 matrix composed of 8 unknown values and a 1 as the 9th value, using least squares.

# Part 3. Rectification

Once I figure out homography, I tested to see if it could work by 'rectifying' objects within an image, almost undoing the warping that taking a picture does to an object. I took two images containing rectangular objects that are warped within the image so that the direct shape is not rectangular, and then 

# Part 3. Rectification


