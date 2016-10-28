# Predict Car Sale Prices with ML Studio
This demo will create a very simple Azure ML experiment which predicts car sale prices based on features.

Technologies: **Azure Machine Learning**

Technical Depth: **Developers, Data Scientists, Architects**

Time: **10 minutes**

Software: **Browser**

Hardware: **None**

Video: **To Do**
 
Supporting files: **[Automobile price data.csv](https://github.com/dxuk/Future-Decoded-2016-Stand-Demos/blob/master/Car%20Sales%20Data%20with%20Azure%20Machine%20Learning/Automobile%20price%20data.csv)**

Accounts: **FDStand2016@outlook.com** (password is **FutureDecoded2016**)

## Create an experiment and load data
Sign into https://studio.azureml.net
* Use **FDStand2016@outlook.com** (password is **FutureDecoded2016**)

Datasets > New > From Local File > `[Automobile price data.csv](https://github.com/dxuk/Future-Decoded-2016-Stand-Demos/blob/master/Car%20Sales%20Data%20with%20Azure%20Machine%20Learning/Automobile%20price%20data.csv)`

Experiments > New > Blank experiment

Drag `Car price data.csv` to the canvas (Saved Datasets > My Datasets)

Visualize the dataset (Right-click the output port > Visualise)

## Preproces data

Remove the `normalized-losses` column
* Drag the Data Transformation > `Select Columns in Dataset` module
* Connect to the `Car price data.csv`
* `Launch column selector`
* With Rules > All Columns > Exclude `normalized-losses`

Clean data by removing rows with missing values
* Drag the Transforms > `Clean missing data` module
* Connect to the `Select Columns in Dataset` module
* Cleaning mode = "Remove entire row"

Run the experiment and observe green ticks

## Define features
Defining features is a way of filtering the data to focus on the features (the attributes of a car in our case) that are of interest.

Exclude weight
* Drag the Data Transformation > `Select Columns in Dataset` module
* Connect to the left port of `Clean missing data`
* `Launch column selector`
* With Rules > No Columns > Include > `make fuel doors body drive engine-size bhp mpg price` (everything apart from weight)

## Choose an ML algorithm
Now we'll apply an ML algorithm to our cleaned, filtered data

Split training and testing data into two sections
* Drag the Data Transformation > Sample & Split > Split Data module
* Connect to output of `Select Columns in Dataset`
* `Fraction of rows in the first output dataset` to 0.75

Run the experiment (this allows the cleaning modules to pass definitions down the workflow)

Add Linear Regression algorithm
* Drag the Machine Learning > Initialize Model > Regression > `Linear Regression` module
* PLace next to the `split data` module

Train the model
* Drag the Machine Learning > Train > `Train Model` module
* Connect the left input to `Linear regression`
* Connect the right input to the left output of `Split Data`
* `Launch column selector`
* Add `price` as a selected column

Run the experiment. This will use 75% of our data to train the model

## Predict prices
We now need to score out model by comparing the model we've train against the remaining 25% of data to see how accurate the price prediction is

Score the model
* Drag the Machine Learning > Score > `Score Model` module
* Connect the left input to `Train Model`
* Connect the right input to right output of `Split data`

Run the experiment

Visualise the output of `Score Model`
* Compare `price` to `scored label` (the predicted price given other data)

Evaluate the model
* Drag the Machine Learning > Evaluate > `Evaluate Model` module
* Connect the left input to `Score model`
* Run the experiment
* Visualise the output and observe error rates

NOTE
* Mean Absolute Error (MAE): The average of absolute errors (an error is the difference between the predicted value and the actual value).
* Root Mean Squared Error (RMSE): The square root of the average of squared errors of predictions made on the test dataset.
* Relative Absolute Error: The average of absolute errors relative to the absolute difference between actual values and the average of all actual values.
* Relative Squared Error: The average of squared errors relative to the squared difference between the actual values and the average of all actual values.
* Coefficient of Determination: Also known as the R squared value, this is a statistical metric indicating how well a model fits the data.

## Convert to Predictive Experiment
So far the experiment has just been a 'trainig experiment'. We now need to convert it to a model that can be used to score new data.

Run the experiment

Setup Web Service > Predictive Web Service

Run the new predictive experiment (takes approx 30 seconds)

Deploy Web Service

## Test the Web Service
Web Services > Car Price Prediction [Predictive Exp]

Point out API key

Click `Request/response` to see API help page with endpoint inputs/outputs and sample code

Request/Response > Test (preview)
* make = `audi`
* fuel = `diesel`
* doors =  `four`
* body = `hatchback`
* drive = `fwd`
* weight = `1900`
* engine-size = `150`
* bhp = `150`
* mpg = `55`
* price = `21500`
* Observe scored label is £20,914
