# PracticalMachineLearning
## Background
Using devices such as Jawbone Up, Nike FuelBand, and Fitbit it is now possible to collect a large amount of data about personal activity relatively inexpensively. These types of devices are part of the quantified self-movement – a group of enthusiasts who take measurements about themselves regularly to improve their health, to find patterns in their behavior, or because they are tech geeks. One thing that people regularly do is quantify how much of a particular activity they do, but they rarely quantify how well they do it. In this project, your goal will be to use data from accelerometers on the belt, forearm, arm, and dumbell of 6 participants. They were asked to perform barbell lifts correctly and incorrectly in 5 different ways. More information is available from the website here: http://web.archive.org/web/20161224072740/http:/groupware.les.inf.puc-rio.br/har (see the section on the Weight Lifting Exercise Dataset). The goal of your project is to predict the manner in which they did the exercise. This is the "classe" variable in the training set. 

## Data
The training data for this project are available here:
https://d396qusza40orc.cloudfront.net/predmachlearn/pml-training.csv
The test data are available here:
https://d396qusza40orc.cloudfront.net/predmachlearn/pml-testing.csv

## Loading data set into R
```R
training <- read.csv("pml-training.csv")
testing <- read.csv("pml-testing.csv")
```
### Check Dimensions
```R
dim(training)
[1] 19622   160
dim(testing)
[1]  20 160
```
Data set having many parameters. So, we need to cleanse data

## Data cleansing
Since testing set has less data we’ll try to cleanse testing data set select columns having complete data set

```R
names(testing[,colSums(is.na(testing)) == 0])
[1] "X"                    "user_name"            "raw_timestamp_part_1"
[4] "raw_timestamp_part_2" "cvtd_timestamp"       "new_window"          
[7] "num_window"           "roll_belt"            "pitch_belt"          
[10] "yaw_belt"             "total_accel_belt"     "gyros_belt_x"        
[13] "gyros_belt_y"         "gyros_belt_z"         "accel_belt_x"        
[16] "accel_belt_y"         "accel_belt_z"         "magnet_belt_x"       
[19] "magnet_belt_y"        "magnet_belt_z"        "roll_arm"            
[22] "pitch_arm"            "yaw_arm"              "total_accel_arm"     
[25] "gyros_arm_x"          "gyros_arm_y"          "gyros_arm_z"         
[28] "accel_arm_x"          "accel_arm_y"          "accel_arm_z"         
[31] "magnet_arm_x"         "magnet_arm_y"         "magnet_arm_z"        
[34] "roll_dumbbell"        "pitch_dumbbell"       "yaw_dumbbell"        
[37] "total_accel_dumbbell" "gyros_dumbbell_x"     "gyros_dumbbell_y"    
[40] "gyros_dumbbell_z"     "accel_dumbbell_x"     "accel_dumbbell_y"    
[43] "accel_dumbbell_z"     "magnet_dumbbell_x"    "magnet_dumbbell_y"   
[46] "magnet_dumbbell_z"    "roll_forearm"         "pitch_forearm"       
[49] "yaw_forearm"          "total_accel_forearm"  "gyros_forearm_x"     
[52] "gyros_forearm_y"      "gyros_forearm_z"      "accel_forearm_x"     
[55] "accel_forearm_y"      "accel_forearm_z"      "magnet_forearm_x"    
[58] "magnet_forearm_y"     "magnet_forearm_z"     "problem_id"    
```
