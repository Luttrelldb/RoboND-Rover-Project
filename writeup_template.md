## Project: Search and Sample Return
### Writeup Template: You can use this file as a template for your writeup if you want to submit it as a markdown file, but feel free to use some other method and submit a pdf if you prefer.

---


**The goals / steps of this project are the following:**  

**Training / Calibration**  

* Download the simulator and take data in "Training Mode"
* Test out the functions in the Jupyter Notebook provided
* Add functions to detect obstacles and samples of interest (golden rocks)
* Fill in the `process_image()` function with the appropriate image processing steps (perspective transform, color threshold etc.) to get from raw images to a map.  The `output_image` you create in this step should demonstrate that your mapping pipeline works.
* Use `moviepy` to process the images in your saved dataset with the `process_image()` function.  Include the video you produce as part of your submission.

**Autonomous Navigation / Mapping**

* Fill in the `perception_step()` function within the `perception.py` script with the appropriate image processing functions to create a map and update `Rover()` data (similar to what you did with `process_image()` in the notebook). 
* Fill in the `decision_step()` function within the `decision.py` script with conditional statements that take into consideration the outputs of the `perception_step()` in deciding how to issue throttle, brake and steering commands. 
* Iterate on your perception and decision function until your rover does a reasonable (need to define metric) job of navigating and mapping.  

[//]: # (Image References)

[image1]: ./misc/rover_image.jpg
[image2]: ./calibration_images/example_grid1.jpg
[image3]: ./calibration_images/example_rock1.jpg 

## [Rubric](https://review.udacity.com/#!/rubrics/916/view) Points
### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  

You're reading it!

### Notebook Analysis
#### 1. Run the functions provided in the notebook on test images (first with the test data provided, next on data you have recorded). Add/modify functions to allow for color selection of obstacles and rock samples.

There are three manin steps to processing the image so that the rover can use it to navagte its environment: perform a prespective transform, apply a color threshold and determin a path.  
  The perspective transform is used to turn the 2d image that the rover collects with its camara sensor(1) into a map of the environment that accuratly stores the location of objects in the environment(3).  It is accomplished using refference points corrliating to a perfect square on the ground(6), in the raw image this square will be distorted by the perspcetive, but if the four points of the square's corrners are provided (should alwasy be consistant) then the angle of distortion can be determined and corrected for.  Using this method it is possible to create a top down world view of the rovers environment(2), however this process has its limitations in that the ground must be flat and the rover must also be sitting flat, no pitch or roll, else the map will not be accurate.  
    The second process is to apply a color thershold to the map image, or the raw image the order does not matter.  This will allow the rover to find ground it can path over vs obsitcles in its environment and identify rock samples, with slight modifications(4).
      The third step is to take the thresholded image of navagiable terrain and find the average angle of all the pixels to the robot location.  This will provide a direction for the robot to drive, if the angles are to the right of the robot it will turn to the right and vice versa for the left(5).


1-4 ![alt text][image2]
5 ![alt text][image2]
6 ![alt text][image3]

#### 1. Populate the `process_image()` function with the appropriate analysis steps to map pixels identifying navigable terrain, obstacles and rock samples into a worldmap.  Run `process_image()` on your test data using the `moviepy` functions provided to create video output of your result.

The only major changes made was to the color_threshold function.  It was changed to return two binary images that were thresholded differently on the same values.  Specificaly the blue channel in the image was screended in one output image to as less than the threshold to identify rock samples in the image. This is because the yellow rocks should be zero in the blue channenl idealy.

See video and notebook file.

### Autonomous Navigation and Mapping

#### 1. Fill in the `perception_step()` (at the bottom of the `perception.py` script) and `decision_step()` (in `decision.py`) functions in the autonomous mapping scripts and an explanation is provided in the writeup of how and why these functions were modified as they were.

In the perception_step() the color thresholding function was altered to retun two binary images, one for light colored terrain and one for the rock samples that do not have a significant blue channel value.  Further, to get the map of obsitcale in the environemnt the inverse of the light colored terrain binary image was used.  
    For the wold mapping part of the preception step, a thershold was applied baised on the current roll and pitch of the rover.  This was done because if the rover has significant pitch or roll the image created uesing the methods will not be accurate to the real environment due to the simplistic transformation used to create the top down image.  
    All other functions were only modified to functinally work baised on the test notebook examples.
    
The decision_step() was modified by reducing the rate of turn for the rover while driving forward.  This was done by multiplying the previous value by 0.50, resulting in a reduciton of 50% in its turning radius.  The reason for the reduction was two fold: the rover had a perpencity for getting stuck in open spaces constantly turning in a circle as there was never a major change in the navagable path due to the tight turn radius and the rover tended to swerve constantly back and forth along its path.  The change reduced the effect of the swerving, vertiualy eliminating it, and it helped the rover constantly move to new areas and not get stuck spinning in circles.
    The turning was fruther modified to use the median value for the angles instead of the mean so the rover was less likely to get stuck on the small rocks in the envrionment.  Using the mean the rover tened to not make a decision as to a direction to avoid the obsticle.  
     The other modifications to the rovers navagation were made to the default values in the Rover class, chaning key threshold values to alter the rovers behavior.  The values changed were: stop_forward, go_forward and  max_vel.  the stop_forward and go_forward were altered to be significantly higher, 10x for stop_forward (50 to 500) and 2x for go_forward (500 to 1000).  This was done to keep the rover form crahsing into walls and then not turning enought for it to find a clear path forward.  The max_vel was changed to 1.5 down from 2.0 and was done to increas the fidelity of the map as it limitis the roll and pitch of the rover in motion.


#### 2. Launching in autonomous mode your rover can navigate and map autonomously.  Explain your results and how you might improve them in your writeup.  

   The simualation was running in .... with quality set to Good the FPS was averaged 18.5

   The results of any given run are passiable, the fidelity is between 80-90 percent and the rover always findes the rocks in the environment that it encounters. Further the rover maps about 90 percent of the environment when it does not get stuck somewhere.  
   The issues with the rovers performances is that it still sways slightly when it moves, it cannot pick up the rocks and it gets stuck on a rock, corrner or in open spaces occasionaly.  All of these problems come from the way the rover makes movement dicison by raw calcualtions from the area of navagiable terrain.  If this project were to be persued further the rover should be provided with more goal oreianted pathing, meaning the rover would be driving toward a goal like the area of the wold map that has the least mapping or a new rock sample it found.  This would allow the rover to compleate the maping and handle sample pick up as well as potentialy user given commands and goals.  The rover would however need the current pathing methods as a low level handleing of navagating through the environment but if it had a destiantion it was headed towrad it could avoid many of the problems observed in its behavior.
