## Project: Search and Sample Return  

[//]: # (Image References)

[image1]: ./misc/Rover_image_manipulation.jpg
[image2]: ./calibration_images/example_grid1.jpg
[image3]: ./misc/rover_direction.jpg

### Notebook Analysis
#### 1. Run the functions provided in the notebook on test images (first with the test data provided, next on data you have recorded). Add/modify functions to allow for color selection of obstacles and rock samples.

There are three main steps to processing the image so that the rover can use it to navigate its environment: perform a perspective transform, apply a color threshold and determine a path.  
  The perspective transform is used to turn the 2d image that the rover collects with its camera sensor(1) into a map of the environment that accurately stores the location of objects in the environment(3).  It is accomplished using reference points correlating to a perfect square on the ground(6), in the raw image this square will be distorted by the perspective, but if the four points of the square's corners are provided (should always be consistent) then the angle of distortion can be determined and corrected for.  Using this method it is possible to create a top down world view of the rovers environment(2), however this process has its limitations in that the ground must be flat and the rover must also be sitting flat, no pitch or roll, else the map will not be accurate.  
    The second process is to apply a color threshold to the map image, or the raw image the order does not matter.  This will allow the rover to find ground it can path over vs obstacles in its environment and identify rock samples, with slight modifications(4).
      The third step is to take the thresholded image of navigable terrain and find the average angle of all the pixels to the robot location.  This will provide a direction for the robot to drive, if the angles are to the right of the robot it will turn to the right and vice versa for the left(5).


1-4 
![alt text][image1]
5 
![alt text][image3]
6 
![alt text][image2]

#### 1. Populate the `process_image()` function with the appropriate analysis steps to map pixels identifying navigable terrain, obstacles and rock samples into a worldmap.  Run `process_image()` on your test data using the `moviepy` functions provided to create video output of your result.

The only major changes made was to the color_threshold function.  It was changed to return two binary images that were thresholded differently on the same values.  Specifically, the blue channel in the image was screened in one output image to as less than the threshold to identify rock samples in the image. This is because the yellow rocks should be zero in the blue channel ideally.

See video and notebook file.

### Autonomous Navigation and Mapping

#### 1. Fill in the `perception_step()` (at the bottom of the `perception.py` script) and `decision_step()` (in `decision.py`) functions in the autonomous mapping scripts and an explanation is provided in the writeup of how and why these functions were modified as they were.

In the perception_step() the color thresholding function was altered to return two binary images, one for light colored terrain and one for the rock samples that do not have a significant blue channel value.  Further, to get the map of obstacle in the environment the inverse of the light-colored terrain binary image was used.  
    For the world mapping part of the perception step, a threshold was applied based on the current roll and pitch of the rover.  This was done because if the rover has significant pitch or roll the image created using the methods will not be accurate to the real environment due to the simplistic transformation used to create the top down image.  
    All other functions were only modified to functionally work based on the test notebook examples.
    
The decision_step() was modified by reducing the rate of turn for the rover while driving forward.  This was done by multiplying the previous value by 0.50, resulting in a reduction of 50% in its turning radius.  The reason for the reduction was twofold: the rover had a propensity for getting stuck in open spaces constantly turning in a circle as there was never a major change in the navigable path due to the tight turn radius and the rover tended to swerve constantly back and forth along its path.  The change reduced the effect of the swerving, virtually eliminating it, and it helped the rover constantly move to new areas and not get stuck spinning in circles.
    The turning was further modified to use the median value for the angles instead of the mean so the rover was less likely to get stuck on the small rocks in the environment.  Using the mean the rover tended to not make a decision as to a direction to avoid the obstacle.  
     The other modifications to the rovers navigation were made to the default values in the Rover class, changing key threshold values to alter the rovers behavior.  The values changed were: stop_forward, go_forward and  max_vel.  the stop_forward and go_forward were altered to be significantly higher, 10x for stop_forward (50 to 500) and 2x for go_forward (500 to 1000).  This was done to keep the rover form crashing into walls and then not turning enough for it to find a clear path forward.  The max_vel was changed to 1.5 down from 2.0 and was done to increase the fidelity of the map as it limits the roll and pitch of the rover in motion.


#### 2. Launching in autonomous mode your rover can navigate and map autonomously.  Explain your results and how you might improve them in your writeup.  

   The simulation was running in .... with quality set to Good the FPS was averaged 18.5

   The results of any given run are passable, the fidelity is between 80-90 percent and the rover always finds the rocks in the environment that it encounters. Further the rover maps about 90 percent of the environment when it does not get stuck somewhere.  
   The issues with the rover’s performances is that it still sways slightly when it moves, it cannot pick up the rocks and it gets stuck on a rock, corner or in open spaces occasionally.  All of these problems come from the way the rover makes movement decision by raw calculations from the area of navigable terrain.  If this project were to be pursued further the rover should be provided with more goal oriented pathing, meaning the rover would be driving toward a goal like the area of the world map that has the least mapping or a new rock sample it found.  This would allow the rover to complete the mapping and handle sample pick up as well as potentially user given commands and goals.  The rover would however need the current pathing methods as a low level handling of navigating through the environment but if it had a destination it was headed toward it could avoid many of the problems observed in its behavior.

