

## **HEART-MET Assistive Robot Challenge with VideoMae Transformers**

Olmer Garcia-Bedoya, Jose Tomas Lorente , Sebastian Murcia

Ekumens Labs

## Introduction

This report presents the methodology and some conclusions learned during the[icsr-2022 HEART-MET Assistive Robot Challenge](https://www.icsr2022.it/icsr-2022-metrics-heart-met-assistive-robot-challenge/). The task in the challenge is to recognize human activities from videos. The videos are recorded from robots operating in a domestic environment and include activities such as reading a book, drinking water, falling on the floor, etc. HEART-MET is one of the competitions in the METRICS project, which has received funding from the European Union's Horizon 2020 research and innovation program under grant agreement No 871252. The competition aims to benchmark assistive robots performing healthcare-related tasks in unstructured domestic environments.

[Ekumen Labs](https://www.ekumenlabs.com/) is an international engineering boutique, provider of advanced software development services and technology. We specialize in bridging the gap between scientific research and deployable software products, with experience in open source projects like ROS. We specialize in the following areas: Robotics Software Applications, Web and Mobile Technology, Embedded Systems and augmented reality applications.

After getting a lot of data successfully in natural language processing (NLP) by self-supervised learning. The solutions, based on autoregressive language modeling in GPT and masked autoencoding in BERT , are conceptually simple:they remove a portion of the data and learn to predict the removed content. Application of transformer in images start with [An Image is Worth 16x16 Words: Transformers for Image Recognition at Scale](https://arxiv.org/pdf/2010.11929.pdf), which outperform the current state-of-the-art (CNN) by almost x4 in terms of computational efficiency and accuracy, where Encoding: key,value(position in image of subimage, subimage). After this concept Facebook created [Masked Autoencoders Are Scalable Vision Learners](https://arxiv.org/pdf/2111.06377.pdf) which is the base of [VideoMAE: Masked Autoencoders are Data-Efficient Learners for Self-Supervised Video Pre-Training](https://arxiv.org/pdf/2203.12602.pdf) the model used during this challenge. In the next section is presented some insight about the methodology.

## Methodology

[videoMAE](https://huggingface.co/docs/transformers/model_doc/videomae) is a Model transformer with video classification, like other transformers it is trained in two stages. The first stages called pre-taining is created by an encoder and a decoder which require a lot computational power. The input receives a pipeline of masking random cubes take from video composed by 16 frames by 16 fixed-size patches of each image frame over a video of resolution 224x224. The output of the decoder receives the complete video with the idea of "challenging self-supervisory tasks that require holistic understanding". The second stage, called fine tuning, consists in a dense layer with input the output of the encoder and the output the number of classification classes for the video.

In huggingFace, videoMAE is a PyTorch[torch.nn.Module](https://pytorch.org/docs/stable/nn.html#torch.nn.Module) subclass. The hugging face data and model come from the [authors repository](https://github.com/MCG-NJU/VideoMAE), but in our case we start from the fine tuning model present in [HuggingFace of Multimedia Computing Group-Nanjing University](https://huggingface.co/MCG-NJU). Specifically, we start making the fine tuning process of the last layer of the model [_videomae-base-finetuned-kinetics_](https://huggingface.co/MCG-NJU/videomae-base-finetuned-kinetics)_. 


## Conclusion

The most interesting characteristic of VideoMAE approach was the speed in the fine tuning part, because with less than 30 epoch (which takes around 50 minutes in a NVIDIA 3090TI) we get an 0.68 over the validation dataset. This contrasts with the results that we obtain with [the base model](https://github.com/HEART-MET/pytorch-i3d) which take around this time by each epoch. Although we tested different approaches to improve the results we did not find any solution, we think that the principal problem comes from the data which is unbalanced , and many videos could be classified in different classes. Adding some sort of memory neuron to the model, or some sort of information about like optical flow, to convey more information about the video than only 16 frames could have also improved the result.

We also tested X-CLIP ([**X-CLIP HuggingFace**](https://huggingface.co/docs/transformers/model_doc/xclip) **,** [**X-CLIP Github**](https://github.com/microsoft/VideoX/tree/master/X-CLIP)**)** but gave 36% accuracy over the training dataset without any training process. We think this could be an interesting approach increasing the description of the categories taking into account classes of the kinetics, because it can give an initial classification for labeling the dataset.

Next steps, after try balance the data , include start the pretraining from the large model of kinetics-400 or from another pre-trained model available (Something-Something V2 or AVA 2.2), change from half precision to double precision.

[1](#sdfootnote1anc) https://viso.ai/deep-learning/vision-transformer-vit/

![](RackMultipart20221202-1-uapuef_html_e7f3c6764ad67b57.jpg)
