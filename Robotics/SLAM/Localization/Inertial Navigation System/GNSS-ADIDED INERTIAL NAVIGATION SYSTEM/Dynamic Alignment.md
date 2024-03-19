# [[DYNAMIC ALIGNMENT]]
Under sufficient dynamic motion, a GNSS/INS determines heading through a process known as dynamic alignment. The system correlates the acceleration measurements from the accelerometer with the position and velocity measurements from the GNSS receiver and is able to accurately derive the heading through this comparison.  
  
For example, consider an accelerometer that measures that a system is accelerating in the negative y-axis of the vehicle, while the GNSS reports the system is accelerating West, as shown in Figure 1.20. Correlating these two measurements together yields that the negative y-axis must be aligned to West, and so the system must be pointing North.  
  
Some systems -- primarily legacy systems -- require a specific pattern of motion to achieve dynamic alignment. But all that is required for most modern systems is horizontal acceleration of any type, such as accelerating down a runway at takeoff, driving around the block, or flying a figure-8. In fact, most smaller vehicles simply need to get up to a decent speed to trigger dynamic alignment; the small fluctuations of a car at highway speed or a Cessna in light turbulence is enough for the Kalman filter to observe heading.  
  
Note that the process of dynamic alignment is not the same as assuming heading is in the same direction as the velocity vector. It is a measure of the true heading of the vehicle, completely independent from course over ground (COG).

![](https://www.vectornav.com/inertialprimer/support-library/gpsins_head.jpg)
