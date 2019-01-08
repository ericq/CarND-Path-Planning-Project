This article describes the reflection of the code that has been implemented. 

The core planning module has the following aspects:

1. Keep lane and maintain optimal speed. If there is no front car in short range, the ego-car will accelerate in a steady fashion till hit the speed limit; if there is a front car with a short distance, a "too_close" flag is set and the ego-car will decelerate. 

2. change lanes. If the ego-car is getting too close to a car in the front, the ego-car will plan for a possible lane change. It will prioritize to change to the left lane if not in the left most lane. The ego-car will rely on sensor_fusion to make sure there are no cars too close in the target lane. If there is no safe gap available in the left lane, the ego-car will consider to change to the right lane 

3. Prefer to drive in the middle lane. while the ego-car is driving in normal condition - no front car is too close and drive at speed limit, it will try to move to the middle lane if it's safe to do so. The reason for this is that the ego-car will have more choices if the front-car is too close, it can either goes to the left or to the right lane. It improves overall driving efficiency. It's also safer to stay in the middle lane from a practical driving experience - safer than right most lane because less cars in/out the ramp; also the left most lane is usually reserved for other cars to pass. 

4. smooth trajectory generation. Spline.h based implementation is used to generate smooth trajectories. It's important to use the last two points from the previous path and also sparse to-be waypoints in the spline generator. 

5. It's vital to use the previous_path variable returned by the simulator. It's not only used to generate a smooth trajectory, but also to estimate correctly the ego-car and other cars position. 

6. avoid swirling. I have observed swirling driving due the lane change. This is caused by the ego-car adopted new lane changing while it's executing a lane change. This caused jerk on the "d" dimension.  This issue can be mitigated by tuning the safe buffer distance between cars. But to avoid this completely, I have implemented a lightweight transaction system to make sure the car will first finish a lane changing if it's within 0.3m range from the target lane. 

7. I'm aware several places can be improved: 
a. a FSM based implementation that supports ready,keep-lane,change left lane,change right lane, prepare to change left and prepare to change right. This will lead to high reliability and cleaner code to maintain.

b. project the trajectories of other moving vehicles. This is not implemented in this project for two reasons: 1) high way traffic has an inherent simpler trajectories. i.e, other cars won't be too crazy. 2) use converative buffer zone while making lange change decisions -- the flip side is that it reduces overall driving efficiency.  

c. a different way to calcuate trajectories for the ego-car and use cost functions to choose based on safety, comfortness, efficiency and etc.
