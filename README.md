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


## Install Dependencies

[Vitis-AI](https://www.xilinx.com/html_docs/vitis_ai/1_2/zkj1576857115470.html) compiler supports Caffe and TensorFlow models for edge device. We choose Caffe compilation workflow because it is well-supported.  

### `pytorch2caffe`: PyTorch-to-Caffe Conversion Tool

To convert original OFA models implemented in PyTorch to Caffe framework, we provide a conversion tool: [pytorch2caffe](https://github.com/zzzDavid/pytorch2caffe). It converts PyTorch models to BVLC Caffe models and performs checking during conversion. 

To install this tool, we recommend using `conda` for Python environment management.


### `Vitis-AI`: Quantization and Compilation Tool


### Measure Latency


## Steps to deploy OFA networks on FPGA




## References

[Vitis-AI User Documentation](https://www.xilinx.com/html_docs/vitis_ai/1_2/zkj1576857115470.html)


