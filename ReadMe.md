# kalibr_allan

This has some nice utility scripts and packages that allow for calculation of the noise values for use in both [kalibr](https://github.com/ethz-asl/kalibr) and IMU filters.
The dataset of the manufacture can find the "white noise" values for the system, but the bias noises need to be found through experimental tests.
The `gyroscope_random_walk` and `accelerometer_random_walk` values can normally be found on the IMU datasheet as either angular random walk or velocity random walk, respectively.



## IMU Noise Values

Parameter | YAML element | Symbol | Units
--- | --- | --- | ---
Gyroscope "white noise" | `gyroscope_noise_density` | <img src="https://latex.codecogs.com/svg.latex?{%5Csigma_g}"> | <img src="https://latex.codecogs.com/svg.latex?{%5Cfrac%7Brad%7D%7Bs%7D%5Cfrac%7B1%7D%7B%5Csqrt%7BHz%7D%7D}">
Accelerometer "white noise" | `accelerometer_noise_density` | <img src="https://latex.codecogs.com/svg.latex?{%5Csigma_a}"> | <img src="https://latex.codecogs.com/svg.latex?{%5Cfrac%7Bm%7D%7Bs^2%7D%5Cfrac%7B1%7D%7B%5Csqrt%7BHz%7D%7D}">
Gyroscope "random walk" | `gyroscope_random_walk` | <img src="https://latex.codecogs.com/svg.latex?{%5Csigma_b_g}"> | <img src="https://latex.codecogs.com/svg.latex?{%5Cfrac%7Brad%7D%7Bs^2%7D%5Cfrac%7B1%7D%7B%5Csqrt%7BHz%7D%7D}">
Accelerometer "random walk" | `accelerometer_random_walk` | <img src="https://latex.codecogs.com/svg.latex?{%5Csigma_b_a}"> | <img src="https://latex.codecogs.com/svg.latex?{%5Cfrac%7Bm%7D%7Bs^3%7D%5Cfrac%7B1%7D%7B%5Csqrt%7BHz%7D%7D}">




## Experiment Steps

1. With the IMU remaining still, record a ROS bag of the readings (we collected a bag for about 4 hours)
2. Convert the ROS bag into a matlab mat file.
    * Use the included `bagconvert` ROS package to do this
    * Example: `rosrun bagconvert bagconvert imu.bag /imu/data`
    * imu.bag is your bag name; /imu/data is your imu topic
3. Run the included matlab scripts to generate an allan deviation plot for the readings
    * If using the parallel version, it uses the matlab parallel toolbox  
    * Need to specify the mat file that the bagconverter made, and the rate of IMU messages
    * first run SCRIPT_allan_matparallel.m  to get the allan deviation result; second run SCRIPT_process_results.m to plot it 
    * the path in two m file need to change 
4. Interpret the generated charts to find noise values
    * Run the process results script
    * Will fit a -1/2 line to the left side of the allan plot
    * White noise is at tau=1 (according to [kalibr wiki](https://github.com/ethz-asl/kalibr/wiki/IMU-Noise-Model#from-the-allan-standard-deviation-ad))
    * Will fit a 1/2 line to the right side of the allan plot
    * Random walk is at tau=3 (according to [kalibr wiki](https://github.com/ethz-asl/kalibr/wiki/IMU-Noise-Model#from-the-allan-standard-deviation-ad))
5. Some example data can be found **[HERE](https://drive.google.com/drive/folders/1a3Es85JDKl7tSpVWEUZryOwtsXB8793o?usp=sharing)**:
    * XSENS MTI-G-700
    * Tango Yellowstone Tablet
    * ASL-ETH VI-Sensor

## Tips(Something need to be paied attention to)
1. Use Matlab2018

(1) The first version of Matlab I use is 2017, although I complied the bagconvert successfully, the rosrun of bagconvert failed. Then, I changed to 2018, Just ok

(2) [Guide](https://www.cnblogs.com/iwuqing/p/9833292.html) to install Matlab2018 in ubuntu 

2. Find Matlab when compile the bagconvert

change one line in FindMatlab.cmake

`find_program(MATLAB_EXE_PATH matlab PATHS /usr/local/MATLAB/R2018a/bin)`

then

`rm -rf build/ && catkin_make`

3. Running time of allan 

4h data need more than 1 hour

4. Sometimes the line fitting is not correct, you need draw the line on your own and read the parameter

5. The spike problem I met like in this issue https://github.com/rpng/kalibr_allan/issues/12 

   My solution is recollect the data and put IMU in more stable position.

### Example Plot - XSENS MTI-G-700
![allan chart acceleration](data/results_20170908T182715_accel.png)

![allan chart angular velocity](data/results_20170908T182715_gyro.png)

### Example Plot - Tango Yellowstone Tablet
![allan chart acceleration](data/results_20171031T115123_accel.png)

![allan chart angular velocity](data/results_20171031T115123_gyro.png)

### Example Plot - ASL-ETH VI-Sensor
![allan chart acceleration](data/results_20180206T140217_accel.png)

![allan chart angular velocity](data/results_20180206T140217_gyro.png)
