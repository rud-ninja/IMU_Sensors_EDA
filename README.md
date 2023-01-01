# Title: Predicting heart rate time-evolution for a given task from IMU Sensor data 

### Objectives:
1. Preprocess the data.
2. Plot relevant features to visualise their relationships.
4. Create a prediction model for time evolution of heart rates for specific tasks.

#### Language used:
Python

#### Libraries used:
Pandas, NumPy, Matplotlib, statsmodel, sklearn

#### Imputation and Prediction algorithm:
Linear regression and Iterative imputer for imputations of IMU sensor imputations. Forward fill for heart rate imputations. The reason behind heart rate missing values was a difference in frequency of measurement between the heart monitor and the IMUs.

Random forest regressor for heart rate predictions.

#### The Dataset:
*PAMAP2* dataset that contains data tracked by a heart monitor and 3 IMU sensors for 9 different subjects while performing various tasks. For information about the subjects, please click here. For information about the experiment and tasks performed, please click here.

## Figures and discussions

For an extensive report and EDA, please check older versions of the code. [Version 1](https://github.com/rud-ninja/IMU_Sensors_EDA/blob/main/IMU_sensor_analysis_code.ipynb), [Version 2](https://github.com/rud-ninja/IMU_Sensors_EDA/blob/main/IMU_new_2.ipynb)

The following discussion is from the latest version with an improved prediction model and error visualisations. The code can be found [here](https://github.com/rud-ninja/IMU_Sensors_EDA/blob/main/IMU4.ipynb).

### Some stats from the dataset

We start off with a sorted (by median) boxplot of the heart rate values versus activity and subjects.

![](https://github.com/rud-ninja/IMU_Sensors_EDA/blob/main/imgs/boxplots_hr.png)

**Fig 1: (Left) Activity vs Heart rate. The values above the median line on each boxplot refers to the MET values of the respective activities. The MET value is an indication of physical energy consumption and therefore has no fixed upper limit. (Right) Subject versus heart rate. The values above the median line are ages and the values below the median line denote the BMI of each subject. BMI = weight/height^2.

Intuitively, the first figure shows positive correlation between heart rate and activity MET. An activity requiring higher consumption of energy results in higher heart rates. But the same cannot be said for the other figure. There is no visible correlation between heart rates and physical attributes such as height, weight or age. We can visualise these claims from heatmaps as well.

![](https://github.com/rud-ninja/IMU_Sensors_EDA/blob/main/imgs/corr_imu_hr.png) ![](https://github.com/rud-ninja/IMU_Sensors_EDA/blob/main/imgs/corr_pa_hr.png)
![](https://github.com/rud-ninja/IMU_Sensors_EDA/blob/main/imgs/corr_imu_pa.png)

**Fig 2: Correlation between features


### Normalising sensor data

![](https://github.com/rud-ninja/IMU_Sensors_EDA/blob/main/new_imgs/hr%20vs%20time%20per%20age.png)
![](https://github.com/rud-ninja/IMU_Sensors_EDA/blob/main/new_imgs/hr%20vs%20time%20per%20bmi.png)

**Fig 3: Stratified plots of the time evolutions of heart rate over the whole length of the track. The gaps in between time evolutions denote transient phases of rest and a change in activity.

The above plots demonstrate that the time durations and heart rates are not uniform for all activities across all the subjects. For better training, it is advantageous to scale the features appropriately. In this task, the timestamps for each combination of subject and activity have been scaled down to a fraction of the whole duration so essentially between 0 and 1. The same has been done for heat rates which have been scaled to a fraction of their respective heart rate spectrum.

The reason why the heart rate time evolution seems possible is due to the fact that there appears to be distinct trend in heart rate associating with some of the activities.

![]

**Fig 4: Trends in heart rate time evolution across different subjects for different activities.

### Predictions

The choice of activity clearly influences both the inputs and the outputs. For example, the duration may depend on the kind of activity. Other IMU measurements depend on it as well. An activity of sitting will record lower motion values than that for running. A choice of activity may also affect the heart rate in a different way other than physical motion, i.e. mentally. To avoid confounding in the logic, this task has concentrated on heart rate predictions for a specific activity at a time. Which means the model has to be trained everytime for a new activity.

A new attribute has been added to the dataset that denotes the heart rate carried forward from the previous activity performed by a subject which sets the base for the evolution of the heart rate for the next activity. If starting from rest, the value can easily be set to the resting heart rate. This helps to limit the predictions and minimise errors. Features have been chosen based on correlation values and an absolute threshold value of 0.2 has been considered.

![](https://github.com/rud-ninja/IMU_Sensors_EDA/blob/main/imgs/cycling_prediction.png)
![](https://github.com/rud-ninja/IMU_Sensors_EDA/blob/main/imgs/lying_prediction.png)
![](https://github.com/rud-ninja/IMU_Sensors_EDA/blob/main/imgs/rope_jumping_prediction.png)
![](https://github.com/rud-ninja/IMU_Sensors_EDA/blob/main/imgs/running_prediction.png)

**Fig 5: Some of the predictions by the model.

![](https://github.com/rud-ninja/IMU_Sensors_EDA/blob/main/new_imgs/pie.png)
