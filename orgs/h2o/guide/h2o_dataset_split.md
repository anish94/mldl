# Split Dataset in H2O #

Once data is loaded into H2O memory, as data scientist you may want to split it into various subsets of datasets so you can use them in various ways.

The most common methods of spliting dataset is following 2 ways:
- Type A - Most splits are design around 80/20 or 75/25 size where higher % is for training data and lower % is for validation.
  - Training dataset
  - Validation
- Type B - In this split user choose 80/10/10 or 70/15/15 where higher % is for training data and lower % is for validation.
  - Training 
  - Validation
  - Test Dataset

In the following example we will load the following dataset into H2O then split it into both types (A and B)
Dataset: https://s3.amazonaws.com/h2o-airlines-unpacked/allyears2k.csv (2000 Rows)

As you can see below the dataset is alrady loaded into H2O:
![](https://github.com/Avkash/mldl/blob/master/images/flow-split-main.png?raw=true)
## Spliting dataset in FLOW ##
You just need to select your dataset in flow and then select "Split" button to start the split process. Once Split action screen appears input your percentage (%) of split as required and then final perform the split. 

The following screenshot shows how to create 2 splits from one dataset:
![](https://github.com/Avkash/mldl/blob/master/images/flow-split-2sets.png?raw=true)

The following screenshot shows how to create 3 splits from one dataset:
![](https://github.com/Avkash/mldl/blob/master/images/flow-split-3sets.png?raw=true)

## Spliting dataset in Python ##
Loading dataset:
```
df = h2o.import_file("https://s3.amazonaws.com/h2o-airlines-unpacked/allyears2k.csv")
print(df.shape)
```
Spliting dataset into 2 sets of training and validation sub datasets of 80/20 % distribution:
```
train, valid = df.split_frame(ratios=[.8])
print(train.shape)
print(valid.shape)
```
Spliting dataset into 3 sets of training, validation and test sub datasets of 70/15/15 % distribution:
```
train, valid, test = df.split_frame(ratios=[.75, .15])
print(train.shape)
print(valid.shape)
print(test.shape)
```

## Spliting dataset in R ##
Loading dataset:
```
df = h2o.importFile("https://s3.amazonaws.com/h2o-airlines-unpacked/allyears2k.csv")
h2o.nrows(df)
h2o.ncols(df)
```
Spliting dataset into 2 sets of training and validation sub datasets of 80/20 % distribution:
```
h2o.splitFrame(df, ratios = c(0.80), destination_frames = c("train_frame", "valid_frame"))
train = df[[1]]
valid = df[[2]]
```
Spliting dataset into 3 sets of training, validation and test sub datasets of 70/15/15 % distribution:
```
h2o.splitFrame(df, ratios = c(0.75,0.15), destination_frames = c("train_frame", "valid_frame", "test_frame"))
train = df[[1]]
valid = df[[2]]
test = df[[3]]
```
Note: If you run the following listing command you will see the split frames with the frame keys as below:
```
> h2o.ls()
                         key
1                train_frame
2                valid_frame
3                test_frame
```

## Spliting dataset in Spark/Scala ##
Importing required libraries and creating H2O Context:
```
import org.apache.spark.h2o._
val h2oContext = H2OContext.getOrCreate(sc)
```
Loading dataset from the local file system as below:
```
val airline_df = new H2OFrame(new File("/Users/avkashchauhan/Downloads/allyears2k.csv"))
airline_df.numRows
airline_df.numCols
```
Spliting dataset into 2 sets of training and validation sub datasets of 80/20 % distribution:
```
# Importing required libraries
import water.{Key, MRTask}
import _root_.hex.splitframe.ShuffleSplitFrame

val keys = Array[String]("train.hex", "valid.hex").map(Key.make[Frame](_))
val ratios = Array[Double](0.8, 0.2)
val frames = ShuffleSplitFrame.shuffleSplitFrame(airline_df, keys, ratios, 1234567689L)

val train = frames(0)
val valid = frames(1)
```
Spliting dataset into 3 sets of training, validation and test sub datasets of 70/15/15 % distribution:
```
import water.{Key, MRTask}
import _root_.hex.splitframe.ShuffleSplitFrame

val keys = Array[String]("train.hex", "valid.hex", "test.hex").map(Key.make[Frame](_))
val ratios = Array[Double](0.7, 0.15, 0.15)
val frames = ShuffleSplitFrame.shuffleSplitFrame(airline_df, keys, ratios, 1234567689L)

val train = frames(0)
val valid = frames(1)
val test = frames(2)
```

