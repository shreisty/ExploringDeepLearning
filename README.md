# Exploring Deep Learning in Python
This repository contains code I used for learning and exploring Deep Learning using Python. 

## TrainCNN.py
I wrote `TrainCNN.py` so that I can easily experiment with some of the important hyperparameters involved in training Deep Convolutional Neural Networks. Fixing hyperparameters in the code makes it difficult to try out different values of a parameter as well as to keep track of the results. `TrainCNN.py` accepts values of the hyperparameters on the command line and write the results to CSV files. One CSV file is generated with detailed information about each run of the program. It contains the values of all hyperparameters and the status of each epoch. Some of the hyperparameters, training loss and error, and validation loss and error is appended to a common CSV file. This CSV file allows easy comparison of the effect of different values of hyperparameters in a single place. Additionally, a chart illustrating training and validation loss, and training and validation error is generated using Matplotlib.

`TrainCNN.py` accepts the following command line arguments:

**General Parameters:**

`--cnnArch {Custom,VGG16}`

&nbsp;  &nbsp;  &nbsp; The CNN architecture (default: Custom)

`--classMode {Categorical,Binary}`

&nbsp;  &nbsp;  &nbsp; The class mode of the data (default: Categorical)

**Hyper Parameters:**

`--optimizer {Adam,SGD,RMSProp}`

&nbsp;  &nbsp;  &nbsp; The optimization function (default: Adam)

`--learningRate LEARNINGRATE`

&nbsp;  &nbsp;  &nbsp; The learning rate (default: 0.0001)

`--imageSize IMAGESIZE`

&nbsp;  &nbsp;  &nbsp; The image size (default: 224)

`--numEpochs NUMEPOCHS`

&nbsp;  &nbsp;  &nbsp; The maximum number of epochs to train (default: 30)

`--batchSize BATCHSIZE`

&nbsp;  &nbsp;  &nbsp; The batch size to use for training (default: 25)

**Output Parameters:**

`--outputFileNamePrefix OUTPUTFILENAMEPREFIX`

&nbsp;  &nbsp;  &nbsp; The prefix for all output files (default: Foo)

`--resultsFileName RESULTSFILENAME`

&nbsp;  &nbsp;  &nbsp; File name of the common output CSV (default: Results.csv)

**Regularization Parameters:**

`--dropout/--no-dropout`

&nbsp;  &nbsp;  &nbsp; Enable/disable dropout regularization. (default: False)

`--augmentation/--no-augmentation`

&nbsp;  &nbsp;  &nbsp; Enable/disable image augmentations. (default: False)

`--augMultiplier AUGMULTIPLIER`

&nbsp;  &nbsp;  &nbsp; With image augmentation, number of images in the dataset times this multiplier is used for training. (default: 3)

**Other Parameters:**

`--debug/--no-debug`

 &nbsp;  &nbsp;  &nbsp; Enable/disable debugging. Debugging mode uses a smaller dataset for faster execution. (default: False)

Most of the parameters are self explanatory, here I explain only some of the more obscure of them:
* `-–cnnArch`: This parameter defines the architecture of the CNN. Two support values are `Custom` and `VGG16`. `Custom` defines a CNN model with four blocks of 2D convolutional and max pooling layers and ends with two fully dense layers. `VGG16` uses imagenet weights with VGG16 model and without the top layer. I added a single dense layer at the end. Note that when using `VGG16` model, image size is fixed at 224 x 244 and `--imageSize` is ignored.
* `--augMultiplier`: When image augmentation is used, number of training steps is scaled with `–augMultiplier` . This essentially increased the number of images with which the CNN is trained.

### Dataset
`TrainCNN.py` expects data to be in the following layout:
* There should be four folder: data/train. data/train_debug, data/validate, and data/validate_debug in the same folder as `TrainCNN.py`.
* For each of these folders, there should be a sub-folder for each class in the dataset.
*_debug* folders are optional and are used with `–debug` switch. The idea is to test the entire code using a smaller dataset which will take less time to execute to wrinkle out any issues or bugs.

### Planned Updates
* Support for different initializers for weights. Currently `he_uniform` is used.
* Support for other pretrained models available in tf.keras.
* Support for early stopping.
* Support for training on multiple GPUs.

### Using TrainCNN.py
While `TrainCNN.py` can be used by itself, it is primarily designed to be used through Windows batch file or Linux shell scripts. For example, to study the effect of batch size on validation loss we can run the following commands sequentially:

Train.cnn.py -–batchSize 16 –outputFileNamePrefix Model_BS16

Train.cnn.py -–batchSize 32 –outputFileNamePrefix Model_BS32

Train.cnn.py -–batchSize 64 –outputFileNamePrefix Model_BS64

Train.cnn.py -–batchSize 128 –outputFileNamePrefix Model_BS128

Running these four command will generate the following files:
* Model_BS16.h5, Model_BS16.csv, and Model_BS16.png.
* Similarly for other three commands.
* Results.csv

## LogisticRegression.py
`LogisticRegression.py` trains a classifier for Kaggle’s dogs vs. cats dataset. It accepts the following command line arguments:

`--imageSize`

&nbsp;&nbsp;&nbsp;Logistic regression expects all samples to have the same number of features. imageSize is used to specify the size to which all images in a dataset will be rescaled to (default: 100)

`--solver {lbfgs,sag,saga}`

&nbsp;&nbsp;&nbsp;Choose the solver used to minimize the cost function for logistic regression (default: lbfgs)

`--rescale / –-no-rescale`

&nbsp;&nbsp;&nbsp;Enable rescaling pixel values in images to [0.0, 1.0] and standardize all images so that each pixel has a zero mean and 1.0 standard deviation (default: --no-rescale)

`--debug / --no-debug`

&nbsp;&nbsp;&nbsp;Enable debugging. In debugging mode, only first 100 images are used

On first run, `LogisticRegression.py` stores the entire dataset as numpy arrays after rescaling (if specified) to a npz array. On subsequent runs, it loads this file to save time on resizing images and rescaling pixel values. The fitted model is saved to a file with prefix LRModel. Finally, `LogisticRegression.py` appends its results to `LogisticRegression.csv` with the following columns: solver, image size, rescaled, training accuracy, validation accuracy, time taken to fit model, and total time. This makes it easy to compare results from different parameters.

### Using LogisticRegression.py

`LogisticRegression.py` can be used either itself on a console or it can be used from a Windows batch file or Linux shell script with different combination of arguments so that they can be compared easily. For example, to compare various images sized with and without rescaling we can use the following command in a batch file to shell script:

./LogisticRegression.py –-imageSize 50 --rescale

./LogisticRegression.py –-imageSize 100 --rescale

./LogisticRegression.py –-imageSize 150 --rescale

./LogisticRegression.py –-imageSize 200 --rescale

./LogisticRegression.py –-imageSize 50

./LogisticRegression.py –-imageSize 100

./LogisticRegression.py –-imageSize 150

./LogisticRegression.py –-imageSize 200

### Planned Updated
* Generalize script to support datasets other than dogs vs. cats.
* Support for specifying range of values for Cs, the parameter to specify the weight for L2 regularization.