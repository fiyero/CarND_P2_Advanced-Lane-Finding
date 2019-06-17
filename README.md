# Advanced Lane Finding
## https://medium.com/@patrickhk/self-driving-car-advanced-lane-lines-detection-524e1be6ccff

Previously we build a basic car lane line detection model under the Udacity Self Driving Car Engineer Nano Degree Program. Now is time to apply more advanced skills to improve the model. Below is the result:<br/>

### My result
https://www.youtube.com/watch?v=WzHN9ZeElho


### Correct the image distortion
When we capture the 3D object into 2D image by camera, distortion due to lens usually occurs and makes the object look either further or closer than it is. Therefore the first step is to deal with the distortion. To get the camera calibration matrix and distortion coefficients, we can make use of the cv2.findChessboardCorners and cv2.calibrateCamera function. Then we can correct the distortion by cv2.undistort.<br/>
![p1](https://cdn-images-1.medium.com/max/800/1*5WWTySsolWnkDpnne5t-gw.png)<br/>
![p2](https://cdn-images-1.medium.com/max/800/1*U0frqP0MLVJknL2WDesC7g.png)<br/>

### Perspective transform to get image in the birds eyes view
Again when a camera captures 3D object into 2D image, parallel lines seem to converge to a point. It makes the parallel lane lines look like converging in the middle of the road. We can select certain src image points and project into dst points to get the perspective matrix M by cv2.getPerspectiveTransform (or given by manufacture). Then we can get the warped image(after perspective transform) by cv2.warpPerspective function<br/>
![p3](https://cdn-images-1.medium.com/max/800/1*j8ijxi7L5SsuIVcgNCjSRg.png)<br/>

### Define threshold function to get the lane line only
Maybe there are many noise in the image, such as mountain, other car, tree…etc. We want to make use of the fact that lane line are mostly white or yellow color to define some threshold function.<br/>

For example we can make use of the HLS color space to get<br/>
![p4](https://cdn-images-1.medium.com/max/800/1*y65LwzpXVIwsKbe4gar0fA.png)<br/>
![p5](https://cdn-images-1.medium.com/max/800/1*CmQUeRl2uyzHov2Tkbjayg.png)<br/>
By combining sobel filter, HLS space, LAB space, direction of gradient and perspective transform, we can get<br/>
![p6](https://cdn-images-1.medium.com/max/800/1*P697lKPUTzp08V0L8yUvWQ.png)<br/>
![p7](https://cdn-images-1.medium.com/max/800/1*rnOluQR-btCbWWZ3dGXg0Q.png)<br/>
### Get the lane line location
Since the warped img is binary image, we can make a histogram of pixel value and get the location of left, right lane by identifying the peak<br/>

![p8](https://cdn-images-1.medium.com/max/800/1*1fmTuP7Yw37riQ6Mo-f_tQ.png)<br/>
Then we use the sliding window approach to get the nonzero pixel and iterate through nwindows to track curvature. I use nwindows = 9, margin = 100 and minpix = 50. Draw box by cv2.rectangle and get all the x,y coordinate position of the lane line point. Feed the point to np.polyfit get the best fit curve then extrapolate/predict the x-coordinate by proving y-coordinate.<br/>


![p9](https://cdn-images-1.medium.com/max/800/1*24iZQIYTJ8POKeDKKXkvSw.png)<br/>
### Draw the lane line on the original img
Once we have the best fit curve of the lane line we can draw the area by cv2.fillPoly. Since this is in the birds eyes view we have to reverse perspective transform back to normal view, then apply to our original image.<br/>
![p10](https://cdn-images-1.medium.com/max/800/1*tz1Rj1VCx4jbo3oncNUkaQ.png)<br/>
![p11](https://cdn-images-1.medium.com/max/800/1*5dk9h1klWddQWv_D6gHYtg.png)<br/>
![p12](https://cdn-images-1.medium.com/max/800/1*kd4MaWyv89S--3XvGZ4Xmw.png)<br/>
### Ouput numerical estimation of the lane curvature and vehicle position
Since all the operation are done in pixel space, is time to map it back to real world space with the help of some conversion. I adopt the conversion ym_per_pix = 30/720, which means there are (30/720) meters per pixel in y dimension. We can adjust the ratio base on actual measurement to get better result. Then all we do is the following equation and apply cv2.putText to visualize the number<br/>

![p13](https://cdn-images-1.medium.com/max/800/1*ejulHRRgmxnOaCe9bmWdHg.png)<br/>
![p14](https://cdn-images-1.medium.com/max/800/1*qrGHm-TfKLkuOS65AeqoTQ.png)<br/>
![p15](https://cdn-images-1.medium.com/max/800/1*pP9kCuLWnCWM2ScAzVApaA.png)<br/>

### Apply into video
Wrap every above step into a function. Then use the VideoFileClip function from moviepy.editor. Boom, done.<br/>

-------------------------------------------------------------------------------------------------------------------------------------
### More about me
[[:pencil:My Medium]](https://medium.com/@patrickhk)<br/>
[[:house_with_garden:My Website]](https://www.fiyeroleung.com/)<br/>
[[:space_invader:	My Github]](https://github.com/fiyero)<br/>

