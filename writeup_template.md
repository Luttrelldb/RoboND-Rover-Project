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
[movie1]: ./output/test_mapping.mp4

## [Rubric](https://review.udacity.com/#!/rubrics/916/view) Points
### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  

You're reading it!

### Notebook Analysis
#### 1. Run the functions provided in the notebook on test images (first with the test data provided, next on data you have recorded). Add/modify functions to allow for color selection of obstacles and rock samples.

There are three manin steps to processing the image so that the rover can use it to navagte its environment: perform a prespective transform, apply a color threshold and determin a path.  The perspective transform is used to turn the 2d image that the rover collects with its camara sensor into a map of the environment that accuratly stores the location of objects in the environment.  It is accomplished using refference points corrliating to a perfect square on the ground, in the raw image this square will be distorted by the perspcetive, but if the four points of the square's corrners are provided (should alwasy be consistant) then the angle of distortion can be determined and corrected for.  Using this method it is possible to create a top down world view of the rovers environment, however this process has its limitations in that the ground must be flat and the rover must also be sitting flat, no pitch or roll, else the map will not be accurate.  
  The second process is to apply a color thershold to the map image, or the raw image the order does not matter.  This will allow the rover to

![alt text][image1]

#### 1. Populate the `process_image()` function with the appropriate analysis steps to map pixels identifying navigable terrain, obstacles and rock samples into a worldmap.  Run `process_image()` on your test data using the `moviepy` functions provided to create video output of your result. 

This was done.
![alt text][movie1]
![alt text][image2]
### Autonomous Navigation and Mapping

#### 1. Fill in the `perception_step()` (at the bottom of the `perception.py` script) and `decision_step()` (in `decision.py`) functions in the autonomous mapping scripts and an explanation is provided in the writeup of how and why these functions were modified as they were.

The only major modification that was made to the test scripts was to add in two additional copies of the rover camara that are thresholded differiently, one for obstacles and one for rock sample identification. To do this the "color_thresh" function was changed to return two images, one that will test the same for red, green and blue, and one that will select pixels that are above threshold for red and green but below threshold for blue.  This modification allows the two different binary images to be uesed for the light colored sand as naviagable terrian, selceted on all three above threshold, and for the yellow rock samples, which is identified by the missing the blue channle but otherwise is the same as the first.  The third image is created by taking the inverse of the navagable terrain image useing "1-path_threshold_image" to invert the binary image. This is possible because if it was not identified as the light colored path it must be un-navagable terriane.  The code presented in the "perception_step()" function is other wise the same as in the test notebook.

The "decision_step()" funciton...

#### 2. Launching in autonomous mode your rover can navigate and map autonomously.  Explain your results and how you might improve them in your writeup.  



**Note: running the simulator with different choices of resolution and graphics quality may produce different results, particularly on different machines!  Make a note of your simulator settings (resolution and graphics quality set on launch) and frames per second (FPS output to terminal by `drive_rover.py`) in your writeup when you submit the project so your reviewer can reproduce your results.**

Here I'll talk about the approach I took, what techniques I used, what worked and why, where the pipeline might fail and how I might improve it if I were going to pursue this project further.  



![alt text][image3]


