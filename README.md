# Siamese-RPN-train
Reimplement the Siamese-RPN training process
## Main framework of Siamese RPN
According to the paper High Performance Visual Tracking with Siamese Region Proposal Network on CVPR 2018,
by Bo Li, Junjie Yan, Wei Wu, Zheng Zhu, Xiaolin Hu

## Inference
Choose template
We regard the template branches’ outputs as the kernels for local detection. Both the kernels are pre-computed on the initial frame and fixed during the whole tracking period. With the current feature map convolved by the pre-computed kernels, the detection branch performs online inference as one-shot detection. 

Proposal selection
The first proposal selection strategy is discarding the bounding boxes generated by the anchors too far away from the center. Because the nearby frames always don’t have large motion. The second proposal selection strategy is that we use cosine window and scale change penalty to re-rank the proposals’ score to get the best one. After the final bounding box is selected, target size is updated by linear interpolation to keep the shape changing smoothly.

## Training
Data set
YoutubeBB : Is labeled at intervals of 1sec. About 220k videos, each video has several clips.
COCO: Greatly expand the categories of positive pairs.
VOT2017

Policy to select pairs
Positive pairs: 	----60%
Since the bounding box in the first frame is the only exemplar, and the model has to track in long term (which means object may change it’s appearence in many ways) , The generalization ability become very important. So, we select the positive pairs randomly from a random video, instead of selecting by timeline.  

Negative pairs: 	----40%
The model is expected to tell whether it really matches the tracking object or not. So that when it lost, we could expand the searching area , or re-initialize the template and position by detection method. That’s to say, if the tracking object doesn’t exist in the searching area, the model should output a much lower score. In addition, the model should be robust to distracters, which sometimes really similar to the tracking object. For the above reasons, we add some negative pairs to training set:
foreground VS background	----10%
foreground of different classes ----10%
foreground of same class but different instances ----20%

Data Augmentation
For every frame, the search region and the scale penalty is depend on the tracking result of previous frame. In this case, Error accumulation become the main problem. To fix this problem, we just make some random error to the position and scale for the initialization of the second frame in the pair. In this way, the model is able to correct the error produced by the previous frame tracking. 
There are other method of Data Augmentation used by the author, but we are not sure what exactly how it has been done. ”some data augmentations are adopted including affine transformation”, “Except the common translation, scale variations and illumination changes, We explicitly introduce motion blur in the data augmentation.” mentioned in the paper.

## Results
Not done yet.

## Architecture
/model				models are saved here
/utils	some 			utils including function get_subwindow_tracking(crop impatch)
/vot					vot dataset
/inference_SiamRPN.py	functions for inference
/train_SiamRPN.py		functions for train
/inference_demo_VOT.py	do inference on vot
/train_VOT				do training on vot
/train_COCO			do training on COCO
/train_youtubeBB_random	do training on youtubeBB
/vot.py				VOT dataset API
/net.py				define SiamRPN structure

## Requirements
Python3.6,  Opencv,  Pytorch0.4.1,  Pycocotools(COCO API)

## How to train
train_VOT.py,  train_COCO.py,  train_youtubeBB_random.py is the training scripts for relevant data sets. 

## How to inference
Run Inference_demo_VOT.py to inference on VOT

## How to evaluate
Use vottoolkit, not done yet.
