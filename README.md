# *DocFace*: Matching ID Document Photos to Selfies

By Yichun Shi and Anil K. Jain

![DocFace](https://raw.githubusercontent.com/seasonSH/DocFace/master/figs/docface.png)

### Contents
0. [Introduction](#introduction)
0. [Citation](#citation)
0. [Requirements](#requirements)
0. [Usage](#usage)
0. [Models](#models)
0. [Results](#results)


### Introduction

This repository includes the tensorflow implementation of **DocFace**, which is a system proposed for Matching ID photos and live face photos. DocFace is shown to siginificantly outperformn general face matchers on the ID-Selfie matching problem. We here give the example training code and models of the methods in the paper. For the preprocessing part, we follow the repository of [SphereFace](http://openaccess.thecvf.com/content_cvpr_2017/papers/Liu_SphereFace_Deep_Hypersphere_CVPR_2017_paper.pdf) to align the face images using [MTCNN](https://github.com/kpzhang93/MTCNN_face_detection_alignment). The user can also use other methods for face alignment. Because the dataset used in the paper is private, we cannot publish it here. One can use the system to test on their own dataset.


### Citation

If you find **DocFace** helpful to your research, please cite:

	@InProceedings{shi_2018_docface,
	  title = {DocFace: Matching ID Document Photos to Selfies},
	  author = {Shi, Yichun and Jain, Anil K.},
	  booktitle = {arXiv:1805.02283},
	  year = {2018}
	}

### Requirements
1. Requirements for `Python3`
2. Requirements for `Tensorflow 1.2r` or newer versions.
3. Run `pip install -r requirements.txt` for other dependencies.

### Usage

#### Part 1: Preprocessing
##### Dataset Structure
Download the [Ms-Celeb-1M](https://www.msceleb.org/download/cropped) and [LFW](http://vis-www.cs.umass.edu/lfw/lfw.tgz) dataset for training and testing the base model. Other dataset such as CASIA-Webface can also be used for training. Because MsCeleb is known to be a very noisy dataset, we use the [clean list](https://github.com/AlfredXiangWu/face_verification_experiment) provided by Wu et al. Arrange MsCelebV1 dataset and LFW dataset as the following structure, where each subfolder represents a subject:

    Aaron_Eckhart
        Aaron_Eckhart_0001.jpg
    Aaron_Guiel
        Aaron_Guiel_0001.jpg
    Aaron_Patterson
        Aaron_Patterson_0001.jpg
    Aaron_Peirsol
        Aaron_Peirsol_0001.jpg
        Aaron_Peirsol_0002.jpg
        Aaron_Peirsol_0003.jpg
        Aaron_Peirsol_0004.jpg
    ...

For the ID-Selfie dataset, make sure each folder has only two images, and is in such a structure:

    Subject1
        1.jpg
        2.jpg
    Subject2
        1.jpg
        2.jpg
    ...
Here "1.jpg" are the ID photos and "2.jpg" are the selfies.

##### Face Alignment
We align all the face images following the [SphereFace](http://openaccess.thecvf.com/content_cvpr_2017/papers/Liu_SphereFace_Deep_Hypersphere_CVPR_2017_paper.pdf). The User is recommended to use their code for face alignment. It is also okay to use other face alignment methods, make sure all the images are resized to 96 x 112. Users can also use an input size of 112 x 112 by changing the "image_size" in the configuration files.

#### Part 2: Train
**Note:** In this part, we assume you are in the directory **`$SDOCFACE_ROOT/`**

##### Training the base model

1. Set up the dataset paths in `config/basemodel.py`:

	```Python
	# Training dataset path
	train_dataset_path = '/path/to/msceleb1m/dataset/folder'
	
	# Testing dataset path
	train_dataset_path = '/path/to/lfw/dataset/folder'
	```

2. Due to the memory cost, the user may need more than one GPUs to use a batch size of `256` on Ms-Celeb-1M. In particular, we used four GTX 1080 Ti GPUs. In such cases, change the following entry in `config/basemodel.py`: 

    ```Python
    # Number of GPUs
    num_gpus = 1
    ```
 
    The user can also use the pre-trained [base model](#Models) we provide.
 
3. Run the following command in the terminal:

	```Shell
	python train_base.py config/basemodel.py
	```
    After training, a model folder will appear under`log/faceres/`. We will use it for fine-tuning. If training code is run more than once, multiple folders will appear with time stamps as their names.
    
##### Fine-tuning on the ID-Selfie datasets

1. Set up the dataset paths and the pre-trained model path in `config/finetune.py`

	```Python
	# Training dataset path
	train_dataset_path = '/path/to/training/dataset/folder'
	
	# Testing dataset path
	train_dataset_path = '/path/to/testing/dataset/folder'
	
	...
	
	# The model folder from which to retore the parameters
    restore_model = '/path/to/the/pretrained/model/folder'
	```

2. Run the following command in the terminal:

	```Shell
	python train_sibling.py config/finetune.py
	```

### Models

- BaseModel: [Google Drive](https://drive.google.com/file/d/11yMyj_qPo9RuSQyGxya1xa6ilesrNpS6/view?usp=sharing)

- Fine-tuned DocFace model: [Google Drive](https://drive.google.com/file/d/1ZhJXK_TkWhTo2hxjg35cMRex9k7NTOpp/view?usp=sharing)


### Results
- Using our pre-trained base model, one should be able to achieve 99.67% on the standard LFW verificatio protocol and 99.60% on the [BLUFR](http://www.cbsr.ia.ac.cn/users/scliao/projects/blufr/) protocol. Similar results should be achieved by using our code to train the Face-ResNet on MsCelebV1.

- Using our private dataset, we see a significant improvement of performance on the ID-Selfie matching problem:
    ![Table1](https://raw.githubusercontent.com/seasonSH/DocFace/master/figs/table1.png)
    ![Table2](https://raw.githubusercontent.com/seasonSH/DocFace/master/figs/table1.png)

### Contact

  Yichun Shi: shiyichu **at** msu **dot** edu
