# LaneDetection


<b>Methodology:</b>

<b>A) Selection of Region of Interest:</b>
 We exploited the mounted camera's property which is so placed that nearly bottom half of the image is the road.

By doing so we don’t need to find the vanishing point and the bottom half of the image is taken into account for further processing. It gives a nice approximation considering the computational overhead required for calculating vanishing point.


<b>B) Pre-processing of the ROI:</b>

Unlike day time images, night vision barely provides with any color property of the image which would have otherwise provided us with unique colour set for lane. Along with only meagre hue variation and low saturation levels even the brightness variation of such images is very delicate and highly dependent on external lighting (like street lamps, vehicle’s tail/head lamp, signals and the glare because of all these).

To get the intensity value of each pixel, we change the color space from RGB to Black and white.

This helps in faster computation without rejecting any valuable information. Bad lighting conditions can make the image harder to process and might result in false results. To avoid this, median filtering was applied. A square kernel of window size 5 was taken and passed over the entire image. 

<b>C) Probable lane marking segmentation: </b>
Primary step in the lane marking detection is to identify probable lane marking segments. 
![Eq 1: ](https://github.com/yashchandak/LaneDetection/blob/master/Documents/equation1.png)


The possible lane markings were selected based on the above parameters. δ Represents the lane width. The lane being brighter in intensity relative to its sides, only if both sides are darker and the sum of the intensity value difference on either side is between a given range then only the pixel is considered as a part of lane segment. The range was calculated using numerous sample point and plotting them. It was found to vary between the 0.25 and 0.75 based on output image’s lighting conditions, which after pre-processing the image for better contrast and brightness can be ideally chosen to be around 0.5.  Any difference less than that come under noise or non-lane parts. The lane width varies based on distance because of the perspective. Near the base it’s maximum whereas near the vanishing point it’s minimal. Lane width at any row(r) of the image is calculated using the following formula:
		

█![Eq 2:](https://github.com/yashchandak/LaneDetection/blob/master/Documents/equation2.png)


max and min represent the maximum and minimal lane width possible in the given image.
Keeping ε value 5 helps in avoiding noises. Max value is dependent on the image size and the mounted camera's position. If the camera is kept very low then because of high perspective and being closer to lane, lane width will be larger near the base as compared to the time when camera is mounted on the top. Minimum by default always remains 0 at vanishing point; otherwise it can be adjusted if required. Once max is set, the above formula can be used to get lane width at varying distances dynamically. Dynamically changing the lane width helps in accurate selection of lanes.


<b>D) Selecting only possible lane segments from above processed image:</b>

The above segmentation process often selects other unwanted noise or regions similar to lane (eg, milestones, edges of vehicles, railings, trees, lampposts, headlight glare etc.).

We exploited the geometric features of a lane segment and based on its property we selected only valid segments. First of all contours were selected from the above binary image using the 
[Suzuki85] algorithm1.Then a minimum area rectangle was drawn around it to get its orientation, length and breadth properties.


<b>Lane segment properties taken into consideration were:</b>

Segment area. Area of segments below minArea threshold denote unwanted object  and hence were rejected.
	
Ratio of sides. Being a line segment, the ratio of its length to breadth should be greater than 4:1 at least. Only segments with higher ratio were taken into account. Segments having area less than certain threshold but more than minArea can possibly represent small broken centre lane markings and hence ratio for them was brought down to 2:1.
	
Orientation. Lane segments by the virtue of their nature are never close to horizontal (unless extremely steep turn is encountered). This property helped us in removing highlighted vehicle bumpers and other segments which were otherwise being treated as false positives.
	
Vertical lane segments are possible only if the vehicle is on the lane, in such cases the lane segment will be near the bottom centre region of the image only. Every other vertical segment detected cannot be lanes hence are discarded.

A min area rectangle was bounded to the detected segment. Lane being very close to rectangles, if the segment area was not close to area of bounding rectangle then the segment was rejected.

<b>Results</b>

![Results](https://github.com/yashchandak/LaneDetection/blob/master/Documents/results.png)

