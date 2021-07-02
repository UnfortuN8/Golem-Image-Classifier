# Image Classification on Golem

This is my Image Classifier that is built to run on the Golem Network. The Classifier itself utilizes python's Scikit library, more specifically Sklearn. The image classification script itself was based off this amazing article by [Gogul09](https://gogul.dev/software/image-classification-python) and the accompanying repo, although several modifications were made to dockerize the script, run with modern libararies, accept args, as well as some other differences. Thanks to the power of sklearn, this script while it utilizes the RandomForestClassifier, can just as easily be swapped out for LogisticRegression, LinearDiscriminantAnalysis, KNeighborsClassifier, DecisionTreeClassifier, and many others.

The dataset is moved into the task at the time of building the docker image. This is done because Machine Learning datasets can be quite long, and since Golem has a 3 hour task limit having the dataset start in the image is faster than sending it all over socket. The Service works in 2 main parts

----------------

## Service Breakdown

**Start** - Upon the start of the service imageclassifier.py is called with the --trainmodel argument. It looks through the /dataset/ folder, and gets the label names from the folder, and from the images inside. The images are first moved into a .h5 format. This data is then used to train a model based off of the many classifiers avaliable. This model currently uses the RandomForestClassifier as it gives the highest accuracy at around 60% given this small dataset and 13 labels (an improvement from around 7.7% guessing). Once the model is trained using this classifier it is pickled and stored on disk.

**Predict** - The Predict model takes in a path as an argument and predicts based off of that image and using the pickled model. The prediction is then made based off prediction labels, and printed out which is captured by the requestor and printed out.

----

## Modifying for Personal/Business Use

Want to run image classification for yourself on Golem?

- Setup yagna daemon as shown [here](https://handbook.golem.network/requestor-tutorials/flash-tutorial-of-requestor-development)

- Install docker on your system (varies based on OS)

- Clone this repo

- Move your dataset into the dataset folder of the service folder. Data should be in /train/label format. Move Test data into /test/ or don't (you can send it over a socket connection, not ideal for large datasets though)

- Install gvmkit-build (```$pip install gvmkit-build ```)

- Then run ```$docker build -t MyGolemTask .``` (From within services folder)

- Then ```$gvmkit-build MyGolemTask ```

- and ```$gvmkit-build MyGolemTask --push```

- Replace image hash in requestor.py with the one that comes from the above command

- If Daemon is running, then just run ```$python3 requestor.py ``` (modify with a for loop for multiple predictions to loop through an array of filepaths, use absolute path for each)

- Predictions will be printed out

Issues? Find Nebula on the [Golem discord](https://discord.com/invite/y29dtcM) 

Improvements? Submit a PR!