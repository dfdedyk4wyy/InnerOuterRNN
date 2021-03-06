The Inner Approach in Chemoinformatics (Undirected Graph - Recursive Neural Networks)
=================================================================

This is a Tensorflow implementation based on the models proposed in "Deep Architectures and Deep Learning in Chemoinformatics: The Prediction of Aqueous Solubility for Drug-Like Molecules" (A. Lusci et al., Journal of Chem. Inf. Modeling, 2013) [1]. It implements recursive neural networks operating on molecular graphs (SMILES) of arbitrary size for chemical property prediction (e.g. solubility).

### Short summary of the method/paper: 

Properties of a molecule with N atoms are predicted by averaging the predictions of an inner-recursive neural network on N different views of the molecule. In each of the N views, the undirected graph representing the molecule is transformed into a N directed acyclic graphs where all eges are oriented towards one atom - a different atom each time. A small multi-layer perceptron (MLP) crawls along these directed edges operating on atom features (i.e. it crawls "inside" the molecule's graph, thus called "Inner" approach), and when reaching the 'terminal' atom it emits a vector. There are N of these vectors which are averaged and then used to compute the overall prediction for the molecule by a different MLP. The same crawling MLP is used for each of the N views of the molecule - i.e. the weights are shared.

![Alt text](references/UGRNN.png?raw=true "Excerpt from [1] demonstrating how a UG-RNN operates on a molecule with eigth atoms. ")






## Requirements:


Python 2 or 3, Numpy -- preferrably using [Anaconda](https://www.continuum.io/downloads)

[Tensorflow](https://www.tensorflow.org/get_started/os_setup) -- tested with version 1.2.1 

[RDkit](http://www.rdkit.org/docs/Install.html) -- the easiest way to install it (if using Anaconda) is "conda install -c https://conda.anaconda.org/rdkit rdkit"

networkx
scipy
scikit-learn (sklearn)












# Usage:

The examples/ directory contains python code examples for various benchmark data sets. Their default settings will perform a ten-fold cross validation on the data. These can be used as a starting point for similar problems or datasets.

Alternatively, one can call "python InnerModel/train.py [ARGUMENTS]" directly, with appropriate arguments as detailed below:


### run InnerModel/train.py to train models


  --model_name 
  
      Name of the model
                        
  --max_epochs 
  
      Number of epochs to run the training for.
                        
  --batch_size 
  
      Batch size (choose a small number on the order of ~ 5)
                        
  --model_params 
  
      Model Parameters: a tuple of three nubers (e.g. 14,13,14)
      The first entry is the size of the hidden layer of the network that crawls along the directed 
      edges of a view on the molecule (Mg hidden units in the paper).
      The second entry is the size of the internal output layer of the network that transfers the 
      assembled information from of a view on the molecule to the averaging step (Mg output units in the paper).
      The third entry is the size of the hidden layer of the 'external' output network that makes the 
      final prediction for the molecule after the averaging step (Mo hidden units in the paper).
  
  --learning_rate 
  
      Initial learning rate (defaults to 1e-3)

  --output_dir 
  
      Directory for storing the trained models (default: model_training_output)

  --training_file 
  
      Path to the csv file containing training data set (e.g. data/delaney/train_delaney.csv)

  --validation_file 
  
      Path to the csv file containing validation data set (e.g. data/delaney/validate_delaney.csv)
      Leave empty to run a cross-validation on the data set specified by <training_file> instead.

  --smile_col 
  
      Column in the csv file that represents the inputs for the UG-RNN -- i.e. the SMILES of the molecules
  
  --target_col 
  
      Column in the csv file that represents the prediction target for the RNN
      
  --contract_rings
      
      Boolean (contract cycles in molecules into a single node; see paper for details) [default False]

  --clip_gradient
  
      Boolean, specify whether to clip gradients during training, by default False as it is 
      not needed with careful setting of other hyperparameters.

  --crossval_total_num_splits

      If <validation_file> is left empty then this defines the number of cross-validation runs that will be performed (default:10)



#### Examples:

To train one model on one train/validation split of the ESOL/Delaney data set (run within the Inner/ directory):

    python InnerModel/train.py --training_file=data/delaney/train_delaney.csv --validation_file=data/delaney/validate_delaney.csv --model_params=14,13,12


To perform a 5-fold cross-validation on the Lipophilicity (LIPO) data set (run within the Inner/ directory):

    python InnerModel/train.py --training_file=data/Lipophilicity.csv --model_params=14,13,12 --crossval_total_num_splits=5 --smile_col=smiles --target_col=exp




### run InnerModel/predict.py to make predictions



  --model_names [can be a list]
  
      Name of the models used for prediction (one or more)
                        
  --model_params  [can be a list]
  
      Model Parameters (same as the ones used for training. 3-tuples, one for each model)

  --output_dir 
  
      Root Directory where the model parameters are stored

  --test_file 
  
      Path to the csv file containing test data set

  --validation_file 
  
      Path to the csv file containing validation data set

  --smile_col 
  
    Column in the csv file that represents the inputs for the RNN, i.e. the SMILES of the molecules

  --target_col 
  
    Column in the csv file that represents the prediction target for the RNN

  --contract_rings

    Boolean (contract cycles in molecules into a single node; see paper for details) [default False]





## Authors:

Gregor Urban <gurban@uci.edu>
Akshay Jain <akshaybetala@gmail.com>





