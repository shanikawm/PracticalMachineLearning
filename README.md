# Practical Machine Learning
# Peer-graded Assignment : Prediction Assignment Writeup
### Shanika Amarasoma [2018-07-27]
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
Data set having many parameters. So, we need to cleanse data set and select a set of features. 

## Data cleansing
Since testing set has less data we’ll try to cleanse the testing data set by selectins columns having complete data set

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
So, we have 52 clean parameters.
Now we'll make sure training data set will also have complete data for the same set of columns
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
Training set having even more complete parameter set including above selected 52 parameters.
```R
train_data <- training[,c(features,"classe")]
dim(train_data)
[1] 19622    53
```
Now our feature set is OK.

## Partitioning the Data Set
Now we'll partition the data set for model training and cross validation testing. We'll need to load caret package and set seed for reproduce the same result if needed. 
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
## Random Forest Algorithm
First we'll try the Random forest algorithm since it is often very accurate . 
```R
modFit<-train(classe ~ ., data = model_train, method = "rf",prox=TRUE)
modFit
Random Forest 

13737 samples
   52 predictor
    5 classes: 'A', 'B', 'C', 'D', 'E' 

No pre-processing
Resampling: Bootstrapped (25 reps) 
Summary of sample sizes: 13737, 13737, 13737, 13737, 13737, 13737, ... 
Resampling results across tuning parameters:

  mtry  Accuracy   Kappa    
   2    0.9883830  0.9853103
  27    0.9884621  0.9854106
  52    0.9780830  0.9722868

Accuracy was used to select the optimal model using the largest value.
The final value used for the model was mtry = 27.
save(modFit,file='modfit.rda')
```
This command actually took 5-6 Hours to complete. Now will use the confusionMatrix function described in https://www.rdocumentation.org/packages/caret/versions/6.0-80/topics/confusionMatrix to see the accuracy with our test data set prediction cross validation

```R
confusionMatrix(modFit, newdata = predict(modFit, newdata = model_test))
Bootstrapped (25 reps) Confusion Matrix 

(entries are percentual average cell counts across resamples)
 
          Reference
Prediction    A    B    C    D    E
         A 28.1  0.2  0.0  0.0  0.0
         B  0.1 19.0  0.2  0.0  0.0
         C  0.0  0.1 17.2  0.3  0.0
         D  0.0  0.0  0.1 16.3  0.1
         E  0.0  0.0  0.0  0.0 18.3
                            
 Accuracy (average) : 0.9885
 ```
 Accuracy is 0.9885
 
After little bit of futher research trainControl described in https://www.rdocumentation.org/packages/caret/versions/6.0-80/topics/trainControl is added to the function and did the same.
```R
modFit<-train(classe ~ ., data = model_train, method = "rf", trControl=trainControl(method="cv",number=10),prox=TRUE,verbose=TRUE,allowParallel=TRUE)
save(modFit,file="modfit2.rda")
modFit
Random Forest 

13737 samples
   52 predictor
    5 classes: 'A', 'B', 'C', 'D', 'E' 

No pre-processing
Resampling: Cross-Validated (10 fold) 
Summary of sample sizes: 12363, 12363, 12361, 12365, 12364, 12364, ... 
Resampling results across tuning parameters:

  mtry  Accuracy   Kappa    
   2    0.9917749  0.9895943
  27    0.9918476  0.9896860
  52    0.9884265  0.9853568

Accuracy was used to select the optimal model using the largest value.
The final value used for the model was mtry = 27.

confusionMatrix(modFit, newdata = predict(modFit, newdata = model_test))
Cross-Validated (10 fold) Confusion Matrix 

(entries are percentual average cell counts across resamples)
 
          Reference
Prediction    A    B    C    D    E
         A 28.4  0.2  0.0  0.0  0.0
         B  0.0 19.1  0.1  0.0  0.0
         C  0.0  0.1 17.2  0.2  0.0
         D  0.0  0.0  0.1 16.2  0.1
         E  0.0  0.0  0.0  0.0 18.3
                            
 Accuracy (average) : 0.9918
```
Accuracy is even better with that change. 
Now we'll apply this model to new data set and see the result.
```R
predict(modFit,testing)
 [1] B A B A A E D B A A B C B A E E A B B B
Levels: A B C D E
```
Note: caret, randomForest and e1071 R packages had to install for this model building. RStudio automatically handled any other dependancy package. 
