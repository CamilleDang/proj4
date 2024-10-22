#  Project 4: Stitching Photo Mosaics

#  Overview

During this project, I created mosaics of various images by selecting correspondence points, and computing the corresponding homographies between the matrices.

#  Part 1. Taking Pictures

I started by taking several images of various environments, keeping the center of projection as close as possible, but rotating the camera just slightly to capture new information, with the eventual goal of stitching these images together to create a mosaic / panoramic. Here are the pairs of images I chose to create photo mosaics, all of which are places I find myself quite often.

| BAIR Image 1 | BAIR Image 2 | 
|:-------------------------:|:-------------------------:|
|<img width="400" src="ma_delaunay.png"> |  <img width="400" src="cam_delaunay.png"> |

| Grove Image 1 | Grove Image 2 | 
|:-------------------------:|:-------------------------:|
|<img width="400" src="ma_delaunay.png"> |  <img width="400" src="cam_delaunay.png"> |

| Nature Image 1 | Nature Image 2 | 
|:-------------------------:|:-------------------------:|
|<img width="400" src="ma_delaunay.png"> |  <img width="400" src="cam_delaunay.png"> |

#  Part 2. Defining Homographies

After shooting these pairs of images, I selected 8 correspondence points for each image.
I then wrote a function to compute the homography matrix, given a set of 4+ points for two images. 
Here's an example of correspondence points I picked for the nature images:

| Nature Image 1 Correspondence Points | Nature Image 2 Correspondence Points | 
|:-------------------------:|:-------------------------:|
|<img width="400" src="ma_delaunay.png"> |  <img width="400" src="cam_delaunay.png"> |

Due to having 4+ correspondences, we can set up an overdetermined systems of equations, where we can solve for H, which is a 3x3 matrix composed of 8 unknown values and a 1 as the 9th value, using least squares. H is a 3x3 matrix such that multiplying by the correspondence points of one image result in the correspondence points of the second image (scaled by a factor of w). In more explicit terms, the correspondence points of one image are a 3 x n matrix, where n represents the number of points and each row is respectively the x_coordinates, y_coordinates, and a row of 1s. It outputs a 3 x n matrix, where the first and second row are the x and y_coordinates of the points in the new image, scaled by a factor of w, found in the third row of the matrix.

# Part 3. Rectification

Once I defined homographies, I tested homography on 'rectifying' objects within an image, essentially undoing the warping that taking a picture does to an object. I took two images containing rectangular objects that are warped within the image so that the direct shape is not rectangular, and then warping that object into a rectangular shape, therefore recovering the original shape of an object from an image.

In this first example, I selected 4 correspondence points surrounding this dentist billboard I pass by occassionally on Channing Street. I then estimated the shape of the rectified billboard. Using `skimage.draw.polygon`, I selected all the points within the rectified billboard space, and then inverse warped (by using the inverse of the homography matrix calculated on the 4 correspondence points and 4 new rectified corners) to find the corresponding un-rectified points. I then used `scipy.interpolate.
griddata` to interpolate the original colors onto the new rectified grid.

| Original Billboard Image | Rectified Billboard | 
|:-------------------------:|:-------------------------:|
|<img width="400" src="ma_delaunay.png"> |  <img width="400" src="cam_delaunay.png"> |

I performed the same rectification on this painting from an art gallery in Seattle I went to during the summer. 

| Original Artpiece Image | Rectified Artpiece | 
|:-------------------------:|:-------------------------:|
|<img width="400" src="ma_delaunay.png"> |  <img width="400" src="cam_delaunay.png"> |

# Part 4. Warping



# Part 5. Mosaic Blending




