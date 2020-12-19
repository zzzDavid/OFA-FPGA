# A Guide to deploy Once-For-All networks on Xilinx DPU

## Introduction
This documentation is a detailed guide to quantize, compile, deploy, and measure latency of Once-For-All networks on Xilinx ZCU102 board running DPU. The models that are successfully deployed on Xilinx DPU are listed in the following table. 

| Model | Top-1 Accuracy | FLOPs | #Param | Latency |
|:-----:|:--------------:|:-----:|:------:|:--------|
| Proxyless-Mobile 0.5|  |    |   |     |   | 


## Table of Content

[- Install Dependencies](#dependency)

[- Steps to deploy OFA networks on FPGA](#steps-to-deploy-ofa-networks-on-fpga)

[- References](#references)

## Pre-requisites 

<details>
<summary>DPU Configuration</summary>
<code>
[DPU IP Spec]
IP  Timestamp            : 2019-11-18 18:45:00 <br>
DPU Core Count           : 3 <br>

[DPU Core Configuration List]<br>
DPU Core                 : #0<br>
DPU Enabled              : Yes<br>
DPU Arch                 : B4096<br>
DPU Target Version       : v1.4.0<br>
DPU Freqency             : 300 MHz<br>
Ram Usage                : Low<br>
DepthwiseConv            : Enabled<br>
DepthwiseConv+Relu6      : Enabled<br>
Conv+Leakyrelu           : Enabled<br>
Conv+Relu6               : Enabled<br>
Channel Augmentation     : Enabled<br>
Average Pool             : Enabled<br>
<br>
DPU Core                 : #1<br>
DPU Enabled              : Yes<br>
DPU Arch                 : B4096<br>
DPU Target Version       : v1.4.0<br>
DPU Freqency             : 300 MHz<br>
Ram Usage                : Low<br>
DepthwiseConv            : Enabled<br>
DepthwiseConv+Relu6      : Enabled<br>
Conv+Leakyrelu           : Enabled<br>
Conv+Relu6               : Enabled<br>
Channel Augmentation     : Enabled<br>
Average Pool             : Enabled<br>
<br>
DPU Core                 : #2<br>
DPU Enabled              : Yes<br>
DPU Arch                 : B4096<br>
DPU Target Version       : v1.4.0<br>
DPU Freqency             : 300 MHz<br>
Ram Usage                : Low<br>
DepthwiseConv            : Enabled<br>
DepthwiseConv+Relu6      : Enabled<br>
Conv+Leakyrelu           : Enabled<br>
Conv+Relu6               : Enabled<br>
Channel Augmentation     : Enabled<br>
Average Pool             : Enabled<br>
<br>

[DPU Extension List]<br>
Extension Softmax<br>
Enabled                  : Yes<br>
</code>
</details>

## Install Dependencies

[Vitis-AI](https://www.xilinx.com/html_docs/vitis_ai/1_2/zkj1576857115470.html) compiler supports Caffe and TensorFlow models for edge device. We choose Caffe compilation workflow because it is well-supported.  

### `pytorch2caffe`: PyTorch-to-Caffe Conversion Tool

To convert original OFA models implemented in PyTorch to Caffe framework, we provide a conversion tool: [pytorch2caffe](https://github.com/zzzDavid/pytorch2caffe). It converts PyTorch models to BVLC Caffe models and performs checking during conversion. 

To install this tool, we recommend using `conda` for Python environment management.


### `Vitis-AI`: Quantization and Compilation Tool


### Measure Latency


## Steps to deploy OFA networks on FPGA

### Export Caffe model

### Quantization

1. Prepare calibration dataset

Calibration dataset: [imagenet_calib.zip (Google Drive)](https://drive.google.com/file/d/1KZE10LXRQCSJuK9d7xErDOjTjUj4fHyi/view?usp=sharing)



### Compilation

### Deploy model on FPGA


## References

[Vitis-AI User Documentation](https://www.xilinx.com/html_docs/vitis_ai/1_2/zkj1576857115470.html)


