# proj4

#  Overview

During this project, I morphed faces together by selecting correspondence points of important facial features, computing the Delauney triangulation of the points, and corresponding affine transformations, in order to successfuly warp the face structure and used cross-dissolving to interpolate corresponding colors. This allowed me to morph between images (ex: images of my mother and me, and me at different ages) as well as calculate the 'average face' of two images or of a population and exaggerate key features of an individual.

#  Part 1. Defining Correspondences

I chose to morph between my mother and me.
In order to do this, I first selected two images of us in similar positions and cropped them to be the same size. I then selected 60 correspondence points at key feature points (facial features such as eyes, nose, mouth, rim of face, rim of hair + 4 points at each corner of the image), using matplotlib and ginput, and stored each mouse click in an array of points, with a button press indicating the end.
I then computed the Delauney triangulation of the corresponding points (using `scipy.spatial.Delaunay`), which enabled triangle-to-triangle correspondences for both images.

| Mother Triangulation | Me Triangulation | 
|:-------------------------:|:-------------------------:|
|<img width="400" src="ma_delaunay.png"> |  <img width="400" src="cam_delaunay.png"> |
