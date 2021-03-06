# K\-Nearest Neighbors<a name="k-nearest-neighbors"></a>

Amazon SageMaker k\-nearest neighbors \(k\-NN\) algorithm is an index\-based algorithm\. It uses a non\-parametric method for classification or regression\. For classification problems, the algorithm queries the *k* points that are closest to the sample point and returns the most frequently used label of their class as the predicted label\. For regression problems, the algorithm queries the *k* closest points to the sample point and returns the average of their feature values as the predicted value\. 

Training with the k\-NN algorithm has three steps: sampling, dimension reduction, and index building\. Sampling reduces the size of the initial dataset so that it fits into memory\. For dimension reduction, the algorithm decreases the feature dimension of the data to reduce the footprint of the k\-NN model in memory and inference latency\. We provide two methods of dimension reduction methods: random projection and the fast Johnson\-Lindenstrauss transform\. Typically, you use dimension reduction for high\-dimensional \(d >1000\) datasets to avoid the “curse of dimensionality” that troubles the statistical analysis of data that becomes sparse as dimensionality increases\. The main objective of k\-NN's training is to construct the index\. The index enables efficient lookups of distances between points whose values or class labels have not yet been determined and the k nearest points to use for inference\.

**Topics**
+ [Input/Output Interface](#kNN-input_output)
+ [kNN Sample Notebooks](#kNN-sample-notebooks)
+ [How It Works](kNN_how-it-works.md)
+ [EC2 Instance Recommendation](#kNN-instances)
+ [K\-Nearest Neighbors Hyperparameters](kNN_hyperparameters.md)
+ [Tuning a K\-Nearest Neighbors Model](kNN-tuning.md)
+ [Data Formats for K\-Nearest Neighbors Training Input](kNN-in-formats.md)
+ [K\-NN Request and Response Formats](kNN-inference-formats.md)

## Input/Output Interface<a name="kNN-input_output"></a>

Amazon SageMaker k\-NN supports train and test data channels\.
+ Use a *train channel* for data that you want to sample and construct into the k\-NN index\.
+ Use a *test channel* to emit scores in log files\. Scores are listed as one line per mini\-batch: accuracy for `classifier`, mean\-squared error \(mse\) for `regressor` for score\.

For training inputs, k\-NN supports `text/csv` and `application/x-recordio-protobuf` data formats\. For input type `text/csv`, the first `label_size` columns are interpreted as the label vector for that row\. You can use either File mode or Pipe mode to train models on data that is formatted as `recordIO-wrapped-protobuf` or as `CSV`\.

For inference inputs, k\-NN supports the `application/json`, `application/x-recordio-protobuf`, and `text/csv` data formats\. The `text/csv` format accepts a `label_size` and encoding parameter\. It assumes a `label_size` of 0 and a UTF\-8 encoding\.

For inference outputs, k\-NN supports the `application/json` and `application/x-recordio-protobuf` data formats\. These two data formats also support a verbose output mode\. In verbose output mode, the API provides the search results with the distances vector sorted from smallest to largest, and corresponding elements in the labels vector\.

For batch transform, k\-NN supports the `application/jsonlines` data format for both input and output\. An example input is as follows:

```
content-type: application/jsonlines

{"features": [1.5, 16.0, 14.0, 23.0]}
{"data": {"features": {"values": [1.5, 16.0, 14.0, 23.0]}}
```

An example output is as follows:

```
accept: application/jsonlines

{"predicted_label": 0.0}
{"predicted_label": 2.0}
```

For more information on input and output file formats, see [Data Formats for K\-Nearest Neighbors Training Input](kNN-in-formats.md) for training, [K\-NN Request and Response Formats](kNN-inference-formats.md) for inference, and the [kNN Sample Notebooks](#kNN-sample-notebooks)\.

## kNN Sample Notebooks<a name="kNN-sample-notebooks"></a>

For a sample notebook that uses the Amazon SageMaker k\-nearest neighbor algorithm to predict wilderness cover types from geological and forest service data, see the [K\-Nearest Neighbor Covertype ](https://github.com/awslabs/amazon-sagemaker-examples/blob/master/introduction_to_amazon_algorithms/k_nearest_neighbors_covtype/k_nearest_neighbors_covtype.ipynb)\. For instructions how to create and access Jupyter notebook instances that you can use to run the example in Amazon SageMaker, see [Using Notebook Instances](nbi.md)\. Once you have created a notebook instance and opened it, select the **SageMaker Examples** tab to see a list of all the Amazon SageMaker samples\. The topic modeling example notebooks using the NTM algorithms are located in the **Introduction to Amazon algorithms** section\. To open a notebook, click on its **Use** tab and select **Create copy**\.

## EC2 Instance Recommendation<a name="kNN-instances"></a>

### Training<a name="kNN-instances-training"></a>

To start, try running training on a CPU, using, for example, an ml\.m5\.2xlarge instance, or on a GPU using, for example, an ml\.p2\.xlarge instance\.

### Inference<a name="kNN-instances-inference"></a>

Inference requests from CPUs generally have a lower average latency than requests from GPUs because there is a tax on CPU\-to\-GPU communication when you use GPU hardware\. However, GPUs generally have higher throughput for larger batches\.