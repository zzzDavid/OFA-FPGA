# A Guide to deploy Once-For-All networks on Xilinx DPU

## Introduction
This documentation is a detailed guide to quantize, compile, deploy, and measure latency of Once-For-All networks on Xilinx ZCU102 board running DPU. The models that are successfully deployed on Xilinx DPU are listed in the following table. 

| Model | Top-1 Accuracy | FLOPs | #Param | Latency |
|:-----:|:--------------:|:-----:|:------:|:--------|
| Proxyless-Mobile 0.5|  |    |   |     |   | 


## Table of Content

[1. Pre-requisites](#pre-requisites)

[2. Install Dependencies](#dependency)

[3. Steps to deploy OFA networks on FPGA](#steps-to-deploy-ofa-networks-on-fpga)

[4. References](#references)

## Pre-requisites 

1. Zynq UltraScale+ MPSoC DPU Target Reference Design (TRD)
 
As the hardware pre-requisite, the DPU TRD needs to be set up first. We recommend DPU B4096 version `v1.4.0` for evaluation. The following links are the official guides to set up DPU TRD on ZCU102 board.  

- [DPU TRD Vitis Flow](https://github.com/Xilinx/Vitis-AI/blob/master/dsa/DPU-TRD/prj/Vitis/README.md)
- [DPU TRD Vivado Flow](https://github.com/Xilinx/Vitis-AI/blob/master/dsa/DPU-TRD/prj/Vivado/README.md)

The DPU configuration used in this documentation (checked with `dexplorer -w`): 
<details>
<summary>DPU Configuration</summary>

```text
[DPU IP Spec]
IP  Timestamp            : 2019-11-18 18:45:00
DPU Core Count           : 3

[DPU Core Configuration List]
DPU Core                 : #0
DPU Enabled              : Yes
DPU Arch                 : B4096
DPU Target Version       : v1.4.0
DPU Freqency             : 300 MHz
Ram Usage                : Low
DepthwiseConv            : Enabled
DepthwiseConv+Relu6      : Enabled
Conv+Leakyrelu           : Enabled
Conv+Relu6               : Enabled
Channel Augmentation     : Enabled
Average Pool             : Enabled

DPU Core                 : #1
DPU Enabled              : Yes
DPU Arch                 : B4096
DPU Target Version       : v1.4.0
DPU Freqency             : 300 MHz
Ram Usage                : Low
DepthwiseConv            : Enabled
DepthwiseConv+Relu6      : Enabled
Conv+Leakyrelu           : Enabled
Conv+Relu6               : Enabled
Channel Augmentation     : Enabled
Average Pool             : Enabled

DPU Core                 : #2
DPU Enabled              : Yes
DPU Arch                 : B4096
DPU Target Version       : v1.4.0
DPU Freqency             : 300 MHz
Ram Usage                : Low
DepthwiseConv            : Enabled
DepthwiseConv+Relu6      : Enabled
Conv+Leakyrelu           : Enabled
Conv+Relu6               : Enabled
Channel Augmentation     : Enabled
Average Pool             : Enabled


[DPU Extension List]
Extension Softmax
Enabled                  : Yes
```

</details>

2. Docker installation

We will use Xilinx's Vitis-AI docker container (Linux) to quantize and compile models. Therefore, a valid docker installation is required. The official guide to install docker on a Linux machine: [Install Docker Engine](https://docs.docker.com/engine/install/).

## Install Dependencies

[Vitis-AI](https://www.xilinx.com/html_docs/vitis_ai/1_2/zkj1576857115470.html) compiler supports Caffe and TensorFlow models for edge device. We choose Caffe compilation workflow because it is well-supported. To compile OFA models, we first export the original model to Caffe version, then we run quantization and compilation with Vitis-AI tools.  

### `pytorch2caffe`: PyTorch-to-Caffe Conversion Tool

To convert original OFA models implemented in PyTorch to Caffe framework, we provide a conversion tool: [pytorch2caffe](https://github.com/zzzDavid/pytorch2caffe). It converts PyTorch models to BVLC Caffe models and performs checking during conversion. 

To install this tool, we recommend using `conda` for Python environment management.


### Vitis-AI: Quantization and Compilation Tool


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


