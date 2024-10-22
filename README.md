#  Project 4: Stitching Photo Mosaics

#  Overview

During this project, I created mosaics of various images by selecting correspondence points, and computing the corresponding homographies between the matrices.

#  Part 1. Taking Pictures

I started by taking several images of various environments, keeping the center of projection as close as possible, but rotating the camera just slightly to capture new information, with the eventual goal of stitching these images together to create a mosaic / panoramic. Here are the pairs of images I chose to create photo mosaics, all of which are places I find myself quite often.

| BAIR Image 1 | BAIR Image 2 | 
|:-------------------------:|:-------------------------:|
|<img width="400" src="bair1.jpg"> |  <img width="400" src="bair2.jpg"> |

| Grove Image 1 | Grove Image 2 | 
|:-------------------------:|:-------------------------:|
|<img width="400" src="grove1.jpg"> |  <img width="400" src="grove2.jpg"> |

| VLSB Hallway Image 1 | VLSB Hallway Image 2 | 
|:-------------------------:|:-------------------------:|
|<img width="400" src="vlsb1.jpg"> |  <img width="400" src="vlsb2.jpg"> |

#  Part 2. Defining Homographies

After shooting these pairs of images, I selected 8 correspondence points for each image.
I then wrote a function to compute the homography matrix, given a set of 4+ points for two images. 
Here's an example of correspondence points I picked for the nature images:

| Nature Image 1 Correspondence Points | Nature Image 2 Correspondence Points | 
|:-------------------------:|:-------------------------:|
|<img width="400" src="vlsb1_pts.png"> |  <img width="400" src="vlsb2_pts.png"> |

Due to having 4+ correspondences, we can set up an overdetermined systems of equations, where we can solve for H, which is a 3x3 matrix composed of 8 unknown values and a 1 as the 9th value, using least squares. H is a 3x3 matrix such that multiplying by the correspondence points of one image result in the correspondence points of the second image (scaled by a factor of w). In more explicit terms, the correspondence points of one image are a 3 x n matrix, where n represents the number of points and each row is respectively the x_coordinates, y_coordinates, and a row of 1s. It outputs a 3 x n matrix, where the first and second row are the x and y_coordinates of the points in the new image, scaled by a factor of w, found in the third row of the matrix.

# Part 3. Rectification

Once I defined homographies, I tested homography on 'rectifying' objects within an image, essentially undoing the warping that taking a picture does to an object. I took two images containing rectangular objects that are warped within the image so that the direct shape is not rectangular, and then warping that object into a rectangular shape, therefore recovering the original shape of an object from an image.

In this first example, I selected 4 correspondence points surrounding this dentist billboard I pass by occassionally on Channing Street. I then estimated the shape of the rectified billboard. Using `skimage.draw.polygon`, I selected all the points within the rectified billboard space, and then inverse warped (by using the inverse of the homography matrix calculated on the 4 correspondence points and 4 new rectified corners) to find the corresponding un-rectified points. I then used `scipy.interpolate.
griddata` to interpolate the original colors onto the new rectified grid.

| Original Billboard Image | Rectified Billboard | 
|:-------------------------:|:-------------------------:|
|<img width="400" src="dentist.jpg"> |  <img width="400" src="dentistres.jpg"> |

I performed the same rectification on this painting from an art gallery in Seattle I went to during the summer. 

| Original Artpiece Image | Rectified Artpiece | 
|:-------------------------:|:-------------------------:|
|<img width="400" src="art.jpg"> |  <img width="400" src="artres.jpg"> |

As seen above for these two examples, homography worked successfully in order to warp an image into a desired shape.

# Part 4. Warping

After performing rectification and confirming successful homography, I extended this application to warp images into each other to create a mosaic. Similarly to the above logic, I warped one image to the other like such:
1. Retrieve the correspondence points I chose in Part 2 for each pair of images.
2. Compute the homography matrix between the correspondence points of the two images.
3. Multiply the homography matrix by the **corner points** in Image 1 to get the estimated dimension of the warped image.
4. Calculate the *translatation* needed to get these corner points to a positive domain and store this as the translated corners of warped image 1.
5. Use these translated corners to estimate dimension of mosaic (warped Image 1 + Image 2)
6. Use `skimage.draw.polygon` to select all the positive points from the new warped image 1 and use inverse warping on the **non-shifted** points to retrieve the original points.
7. Use interpolation to place corresponding colors from the original image into the positive warped positions.

Following these steps, I warped all the first images in the 3 pairs to the corresponding second images. Here were the results:

| Original BAIR Image 1 | Warped BAIR Image 1 |
|:-------------------------:|:-------------------------:|
|<img width="400" src="bair1.jpg"> |  <img width="400" src="estimated.jpg"> |

| Original Grove Image 1 | Warped Grove Image 1 | 
|:-------------------------:|:-------------------------:|
|<img width="400" src="grove1.jpg"> |  <img width="400" src="grovetry.jpg"> |

| Original VLSB Image 1 | Warped VLSB Image 1 | 
|:-------------------------:|:-------------------------:|
|<img width="400" src="vlsb1.jpg"> |  <img width="400" src="vlsbtry.jpg"> |

# Part 5. Mosaic Blending

With these warped images, it was time to blend the warped image 1 with the corresponding image 2 to create a mosaic!
For each warped image, I mapped the corresponding image 2 into the correct position onto an empty image in the dimensions of the two images combined. *To do this, I shifted Image 2 by the same x and y shift from Part 4 to get the warped Image 1 to the positive domain.*

I then used 2-band blending to blend the Warped Image 1 and Image 2 together.
I first computed the distance transform for both images (`scipy.ndimage.distance_transform_edt`), using the Euclidean distance as measurement. 

Here is an example of the distance transform of the warped VLSB image 1 and VLSB image 2:

| Warped Grove Image 1 Distance Transform | Grove Image 2 Distance Transform | 
|:-------------------------:|:-------------------------:|
|<img width="400" src="grove1_dist.jpg"> |  <img width="400" src="grove2_dist.jpg"> |

Using the distance transform, I blended the low frequency components of the two images by using the distance transform as the weights in a linear combination of warped Image 1 and Image 2. I then blended the high frequency components of the two images by taking the corresponding value of an image based on which distance transform had a higher value for that point. Lastly, I combined the blended low frequency components and the blended high frequency components to end up with a smoothly blended mosaic image. For these three examples, I actually had a pretty good result for just blending the lower frequency components (even though it did blur some details), so I tended to weigh it higher when combining the low frequency and high frequency components, since the high frequency components added some strong edges / artifacts.

Here is an example:

| Combined Grove Low Frequency | Combined Grove High Frequency | 
|:-------------------------:|:-------------------------:|
|<img width="400" src="grove1_dist.jpg"> |  <img width="400" src="grove2_dist.jpg"> |

And here are the results of all 3 mosaics! 

| Warped BAIR 1 Image | BAIR Image 2 | BAIR Mosaic | 
|:-------------------------:|:-------------------------:|:-------------------------:|
|<img width="400" src="grove1_dist.jpg"> |  <img width="400" src="grove2_dist.jpg"> | <img width="400" src="grove2_dist.jpg"> |

| Warped Grove 1 Image | Grove Image 2 | Grove Mosaic | 
|:-------------------------:|:-------------------------:|:-------------------------:|
|<img width="400" src="grove1_dist.jpg"> |  <img width="400" src="grove2_dist.jpg"> | <img width="400" src="grove2_dist.jpg"> |

| Warped VLSB 1 Image | VLSB Image 2 | VLSB Mosaic | 
|:-------------------------:|:-------------------------:|:-------------------------:|
|<img width="400" src="grove1_dist.jpg"> |  <img width="400" src="grove2_dist.jpg"> | <img width="400" src="grove2_dist.jpg"> |







