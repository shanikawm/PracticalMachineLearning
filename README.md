# Practical Machine Learning
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
 [1] "X"                    "user_name"            "raw_timestamp_part_1" "raw_timestamp_part_2" "cvtd_timestamp"      
 [6] "new_window"           "num_window"           "roll_belt"            "pitch_belt"           "yaw_belt"            
[11] "total_accel_belt"     "gyros_belt_x"         "gyros_belt_y"         "gyros_belt_z"         "accel_belt_x"        
[16] "accel_belt_y"         "accel_belt_z"         "magnet_belt_x"        "magnet_belt_y"        "magnet_belt_z"       
[21] "roll_arm"             "pitch_arm"            "yaw_arm"              "total_accel_arm"      "gyros_arm_x"         
[26] "gyros_arm_y"          "gyros_arm_z"          "accel_arm_x"          "accel_arm_y"          "accel_arm_z"         
[31] "magnet_arm_x"         "magnet_arm_y"         "magnet_arm_z"         "roll_dumbbell"        "pitch_dumbbell"      
[36] "yaw_dumbbell"         "total_accel_dumbbell" "gyros_dumbbell_x"     "gyros_dumbbell_y"     "gyros_dumbbell_z"    
[41] "accel_dumbbell_x"     "accel_dumbbell_y"     "accel_dumbbell_z"     "magnet_dumbbell_x"    "magnet_dumbbell_y"   
[46] "magnet_dumbbell_z"    "roll_forearm"         "pitch_forearm"        "yaw_forearm"          "total_accel_forearm" 
[51] "gyros_forearm_x"      "gyros_forearm_y"      "gyros_forearm_z"      "accel_forearm_x"      "accel_forearm_y"     
[56] "accel_forearm_z"      "magnet_forearm_x"     "magnet_forearm_y"     "magnet_forearm_z"     "problem_id"   
```
First Seven Parameters cannot be considered as features. So, we’ll remove them and get all other. Last parameter is the one which should be predicted.

```R
features <- names(testing[,colSums(is.na(testing)) == 0])[8:59]
test_data <- testing[,c(features,"problem_id")]
dim(test_data)
[1] 20 53
```
So, we have 53 clean parameters.
No we'll make sure training set will also have complete data for the same set of columns
```R
names(training[,colSums(is.na(training)) == 0])
 [1] "X"                       "user_name"               "raw_timestamp_part_1"    "raw_timestamp_part_2"    "cvtd_timestamp"         
 [6] "new_window"              "num_window"              "roll_belt"               "pitch_belt"              "yaw_belt"               
[11] "total_accel_belt"        "kurtosis_roll_belt"      "kurtosis_picth_belt"     "kurtosis_yaw_belt"       "skewness_roll_belt"     
[16] "skewness_roll_belt.1"    "skewness_yaw_belt"       "max_yaw_belt"            "min_yaw_belt"            "amplitude_yaw_belt"     
[21] "gyros_belt_x"            "gyros_belt_y"            "gyros_belt_z"            "accel_belt_x"            "accel_belt_y"           
[26] "accel_belt_z"            "magnet_belt_x"           "magnet_belt_y"           "magnet_belt_z"           "roll_arm"               
[31] "pitch_arm"               "yaw_arm"                 "total_accel_arm"         "gyros_arm_x"             "gyros_arm_y"            
[36] "gyros_arm_z"             "accel_arm_x"             "accel_arm_y"             "accel_arm_z"             "magnet_arm_x"           
[41] "magnet_arm_y"            "magnet_arm_z"            "kurtosis_roll_arm"       "kurtosis_picth_arm"      "kurtosis_yaw_arm"       
[46] "skewness_roll_arm"       "skewness_pitch_arm"      "skewness_yaw_arm"        "roll_dumbbell"           "pitch_dumbbell"         
[51] "yaw_dumbbell"            "kurtosis_roll_dumbbell"  "kurtosis_picth_dumbbell" "kurtosis_yaw_dumbbell"   "skewness_roll_dumbbell" 
[56] "skewness_pitch_dumbbell" "skewness_yaw_dumbbell"   "max_yaw_dumbbell"        "min_yaw_dumbbell"        "amplitude_yaw_dumbbell" 
[61] "total_accel_dumbbell"    "gyros_dumbbell_x"        "gyros_dumbbell_y"        "gyros_dumbbell_z"        "accel_dumbbell_x"       
[66] "accel_dumbbell_y"        "accel_dumbbell_z"        "magnet_dumbbell_x"       "magnet_dumbbell_y"       "magnet_dumbbell_z"      
[71] "roll_forearm"            "pitch_forearm"           "yaw_forearm"             "kurtosis_roll_forearm"   "kurtosis_picth_forearm" 
[76] "kurtosis_yaw_forearm"    "skewness_roll_forearm"   "skewness_pitch_forearm"  "skewness_yaw_forearm"    "max_yaw_forearm"        
[81] "min_yaw_forearm"         "amplitude_yaw_forearm"   "total_accel_forearm"     "gyros_forearm_x"         "gyros_forearm_y"        
[86] "gyros_forearm_z"         "accel_forearm_x"         "accel_forearm_y"         "accel_forearm_z"         "magnet_forearm_x"       
[91] "magnet_forearm_y"        "magnet_forearm_z"        "classe"  
```
Training set having move complete parameter set including above selected 53 parameters.
```R
train_data <- training[,c(features,"classe")]
dim(train_data)
[1] 19622    53
```
No our feature set is OK.

## Partitioning the Data Set
Now we'll partition the data set for model training and cross validation. We'll need to load caret package and set seed for reproduce the same result if needed. 
```R
library(caret)
set.seed(1981)
inTrain <- createDataPartition(train_data$classe, p=0.7, list=FALSE)
model_train <- train_data[inTrain,]
model_test <- train_data[-inTrain,]
dim(model_test)
[1] 5885   53
dim(model_train)
[1] 13737    53
```
## Trying Random Forest Algorithm
We'll just try the Random forest algorithm and see the accuracy. 


