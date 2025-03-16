# Alzhimers-Disease-Prediction-Using-Deep-learning
# AD-Prediction  Convolutional Neural Networks for Alzheimer's Disease Prediction Using Brain MRI Image 
Alzheimers disease (AD) is characterized by severe memory loss and cognitive impairment. It associates with significant brain structure changes, which can be measured by magnetic resonance imaging (MRI) scan. The observable preclinical structure changes provides an opportunity for AD early detection using image classification tools, like convolutional neural network (CNN). However, currently most AD related studies were limited by sample size. Finding an efficient way to train image classifier on limited data is critical. In our project, we explored different transfer-learning methods based on CNN for AD prediction brain structure MRI image. We find that both pretrained 2D AlexNet with 2D-representation method and simple neural network with pretrained 3D autoencoder improved the prediction performance comparing to a deep CNN trained from scratch. The pretrained 2D AlexNet performed even better (**86%**) than the 3D CNN with autoencoder (**77%**).  ## Method 
1. Data In this project, we used public brain MRI data from **Alzheimers Disease Neuroimaging Initiative (ADNI)** Study. ADNI is an ongoing, multicenter cohort study, started from 2004. It focuses on understanding the diagnostic and predictive value of Alzheimers disease specific biomarkers. The ADNI study has three phases: ADNI1, ADNI-GO, and ADNI2. Both ADNI1 and ADNI2 recruited new AD patients and normal control as research participants. Our data included a total of 686 structure MRI scans from both ADNI1 and ADNI2 phases, with 310 AD cases and 376 normal controls. We randomly derived the total sample into training dataset (n = 519), validation dataset (n = 100), and testing dataset (n = 67).  #### 2. Image preprocessing Image preprocessing were conducted using Statistical Parametric Mapping (SPM) software, version 12. The original MRI scans were first skull-stripped and segmented using segmentation algorithm based on 6-tissue probability mapping and then normalized to the International Consortium for Brain Mapping template of European brains using affine registration. Other configuration includes: bias, noise, and global intensity normalization. The standard preprocessing process output 3D image files with an uniform size of 121x145x121. Skull-stripping and normalization ensured the comparability between images by transforming the original brain image into a standard image space, so that same brain substructures can be aligned at same image coordinates for different participants. Diluted or enhanced intensity was used to compensate the structure changes. the In our project, we used both whole brain (including both grey matter and white matter) and grey matter only.  
3. AlexNet and Transfer Learning Convolutional Neural Networks (CNN) are very similar to ordinary Neural Networks. A CNN consists of an input and an output layer, as well as multiple hidden layers. The hidden layers are either convolutional, pooling or fully connected. ConvNet architectures make the explicit assumption that the inputs are images, which allows us to encode certain properties into the architecture. These then make the forward function more efficient to implement and vastly reduce the amount of parameters in the network.  #### 3.1. AlexNet The net contains eight layers with weights; the first five are convolutional and the remaining three are fully connected. The overall architecture is shown in Figure 1. The output of the last fully-connected layer is fed to a 1000-way softmax which produces a distribution over the 1000 class labels. AlexNet maximizes the multinomial logistic regression objective, which is equivalent to maximizing the average across training cases of the log-probability of the correct label under the prediction distribution. The kernels of the second, fourth, and fifth convolutional layers are connected only to those kernel maps in the previous layer which reside on the same GPU (as shown in Figure1). The kernels of the third convolutional layer are connected to all kernel maps in the second layer. The neurons in the fully connected layers are connected to all neurons in the previous layer. Response-normalization layers follow the first and second convolutional layers. Max-pooling layers follow both response-normalization layers as well as the fifth convolutional layer. The ReLU non-linearity is applied to the output of every convolutional and fully-connected layer. ![](images/f1.png)  The first convolutional layer filters the 224x224x3 input image with 96 kernels of size 11x11x3 with a stride of 4 pixels (this is the distance between the receptive field centers of neighboring neurons in a kernel map). The second convolutional layer takes as input the (response-normalized and pooled) output of the first convolutional layer and filters it with 256 kernels of size 5x5x48. The third, fourth, and fifth convolutional layers are connected to one another without any intervening pooling or normalization layers. The third convolutional layer has 384 kernels of size 3x3x256 connected to the (normalized, pooled) outputs of the second convolutional layer. The fourth convolutional layer has 384 kernels of size 3x3x192 , and the fifth convolutional layer has 256 kernels of size 3x3x192. The fully-connected layers have 4096 neurons each.  #### 3.2. Transfer Learning Training an entire Convolutional Network from scratch (with random initialization) is impractical[14] because it is relatively rare to have a dataset of sufficient size. An alternative is to pretrain a Conv-Net on a very large dataset (e.g. ImageNet), and then use the ConvNet either as an initialization or a fixed feature extractor for the task of interest. Typically, there are three major transfer learning scenarios:   **ConvNet as fixed feature extractor:** We can take a ConvNet pretrained on ImageNet, and remove the last fully-connected layer, then treat the rest structure as a fixed feature extractor for the target dataset. In AlexNet, this would be a 4096-D vector. Usually, we call these features as CNN codes. Once we get these features, we can train a linear classifier (e.g. linear SVM or Softmax classifier) for our target dataset.   **Fine-tuning the ConvNet:** Another idea is not only replace the last fully-connected layer in the classifier, but to also fine-tune the parameters of the pretrained network. Due to overfitting concerns, we can only fine-tune some higher-level part of the network. This suggestion is motivated by the observation that earlier features in a ConvNet contains more generic features (e.g. edge detectors or color blob detectors) that can be useful for many kind of tasks. But the later layer of the network becomes progressively more specific to the details of the classes contained in the original dataset.   **Pretrained models:** The released pretrained model is usually the final ConvNet checkpoint. So it is common to see people use the network for fine-tuning.  
4. 3D Autoencoder and Convolutional Neural Network We take a two-stage approach where we first train a 3D sparse autoencoder to learn filters for convolution operations, and then build a convolutional neural network whose first layer uses the filters learned with the autoencoder. ![](images/f2.png)  #### 4.1. Sparse Autoencoder An autoencoder is a 3-layer neural network that is used to extract features from an input such as an image. Sparse representations can provide a simple interpretation of the input data in terms of a small number of \parts by extracting the structure hidden in the data. The autoencoder has an input layer, a hidden layer and an output layer, and the input and output layers have same number of units, while the hidden layer contains more units for a sparse and overcomplete representation. The encoder function maps input x to representation h, and the decoder function maps the representation h to the output x. In our problem, we extract 3D patches from scans as the input to the network. The decoder function aims to reconstruct the input form the hidden representation h.  #### 4.2. 3D Convolutional Neural Network Training the 3D convolutional neural network(CNN) is the second stage. The CNN we use in this project has one convolutional layer, one pooling layer, two linear layers, and finally a log softmax layer. After training the sparse autoencoder, we take the weights and biases of the encoder from trained model, and use them a 3D filter of a 3D convolutional layer of the 1-layer convolutional neural network. Figure 2 shows the architecture of the network.  
5. Tools In this project, we used Nibabel for MRI image processing and PyTorch Neural Networks implementation.
