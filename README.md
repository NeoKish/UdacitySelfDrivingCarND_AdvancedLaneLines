# Advanced Lane Finding Project


The following project is a part of Udacity Self Driving Nanodegree program. It involves application of techniques like camera calibration, perspective transform, color and gradient thresholding, sliding window and polynomial fitting taught during the lectures

Goal of the project is to correctly identify lane lines, measure the curvature and offset of the camera from lane centre throughout a 50 second video where in the car follows a curved path and goes through different lighting conditions.

The lane detection algorithm is first tested on a set of test images and then applied to the video.


Below are files references to images, videos and folder consisting of the output images.

[//]: # (File References)

[image1]: ./output_images/undist_camera_cal_image_output.png
[image2]: ./output_images/undistort_test_image_output.png
[folder1]: ./output_images/Binary_thresholded_images
[folder2]: ./output_images/Warped_test_images_with_source_points_drawn(without color threshold)
[folder3]: ./output_images/warped_binary_image_fit_polynomial
[folder4]: ./output_images/Final_output_images
[video1]: ./test_videos_output/project_video.mp4 "Video"


### Camera Calibration 

In the camera calibration code, with a set of chessboard images, we first try to get the objectpoints and image points(corners) which we then use to do camera calibration using open CV calibrateCamera function.The calibrateCamera function returns camera matrix and distortion coefficients which we then use for undistortion of chessboard images. An example of the application is seen in [image1]

### Pipeline (single images)

The pipeline involves series of algorithms applied to reached the final output stage. Code for pipeline is written under test_images_pipeline() function

1) In the first step, we try to undistort the image using the undistort() 
First we read the image then convert it to grayscale.
We ran a camera calibration function on the grayscale image shape as this changes with shape.The camera matrix and undistortion coefficients obtained from camera calibration function is used to undistort the image. The output result can be found in [image2]

2) In the second step, color and gradient threshold is applied to get the binary image using threshold() function
For color threshold, we use saturation channel as it is more efficient in identifying lane lines in differnt lighting conditions
For gradient threshold, sobel operator on grayscale image is applied along x direction since the results are less noisier then combined xy.
A combination of both the threshold gives us the final binary threshold. The output images with application of threshold() function is stored in [folder1]

3) In the third step, a perspective tranform is performed over binary thresholded images using warp() function. The source and destination points were drawn on the image to verify that the lines appear parallel in the warped image. For the output images, the warp transformation is shown for non thresholded images but in the pipeline it is done on the binary thresholded image. The examples of transformation on the test images can be found in [folder2] 

Below are the hard-coded source and destination points

        #source points from the undistorted image
        src=np.float32([[580,460],
                    [700,460],
                    [1100,720],
                    [200,720]])
    
        #destination points required for perspective transform
        dst= np.float32([[370,0],
                    [980,0],
                    [980,720],
                    [370,720]])


4) In the fourth step, a check is made to see if the lane lines are detected. 

If the lane lines pixels are not detected, then we first find the lane pixels using the function find_lane_pixels() by passing on the binary warped image

In the find_lane_pixels() , we first take a histogram of the bottom half image to get peaks which indicate starting point of lane lines. Then we use the technique of sliding windows moving upwards to track the path of the lane lines from bottom to top. This gives us the left and right lane pixel postions. The we use np.polyfit() function to get left and right polynomials. This is returned by the function.
The left and right fit obtained is stored in line class variables so that we can keep track of polynomials fitted to the positions in each frame.The polynomials fitted to the left and right lane positions on binary warped images can be found in [folder3]

If the lane lines are detected, then to avoid longer computation time we have used search_around_poly() function to search in margin around the previous lane line positions and compute new lane positions with new polynomial fitting to them.

Once we have found out the polynomials for the left and right lane positions in the search_around_poly() functions, they are recasted back to get the left and right lane lines which is drawn on blank image and then warped back to original undistorted image so that we can see that the lane area is accurately identified. The application can be found in the set of images in [folder4]

5) In the fifth step, we calculate the radius of curvature using measure_curvature_ real function for left and right lane in world space by using conversion factor(pixel to world space) and then fitting new polynomials and using the equation of radius of curvature presented in lectures to get radius of curvature.

We also calculate the camera offset by taking difference between camera centre (midpoint of lane centre obtained from first frame) and lane centre(average of bottom left and right lane x positions obtained in subsequent frames). Both the radius of curvature and camera offset are mentioned in the set of images stored in [folder4]


### Video
The project video is stored in [video1]. 

### Project challenges
The pipeline seems to be working for majority of the video but there is little wobbliness when there the car goes up and down during the change in road conditions. I think to improve on this, I might consider averaging over few frames and then outputting the result




