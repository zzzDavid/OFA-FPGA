# A Guide to deploy Once-For-All networks on Xilinx DPU

## Introduction
This documentation is a detailed guide to quantize, compile, deploy, and measure the latency of Once-For-All Proxyless search space networks on Xilinx ZCU102 board running DPU. The models that are successfully deployed on Xilinx DPU are listed in the following table. 

| Model | Top-1 Accuracy | Top-5 Accuracy | FLOPs | #Param | Latency (DPU) |
|:-----:|:--------------:|:--------------:|:-----:|:------:|:-------------:|
| Proxyless-Mobile 0.5|  58.848|  81.664  | 103.6M  |  2.2M   |  3.279 ms | 


## Table of Content

[1. Pre-requisites](#pre-requisites)

[2. Install Dependencies](#install-dependencies)

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

```sh
$ conda create -n deploy python=3.7
```

Install `pytorch2caffe` python package:
```sh
$ git clone https://github.com/zzzDavid/pytorch2caffe
$ cd pytorch2caffe
$ pip install .
```

### Vitis-AI: Quantization and Compilation Tool

Vitis-AI version needs to be compatible with DPU TRD.

## Steps to deploy OFA networks on FPGA

### Export Caffe model

1. Prepare calibration dataset

Calibration dataset: [imagenet_calib.zip (Google Drive)](https://drive.google.com/file/d/1KZE10LXRQCSJuK9d7xErDOjTjUj4fHyi/view?usp=sharing)

2. Export ProxylessNAS net to Caffe model
```sh
$ git clone https://github.com/mit-han-lab/proxylessnas.git
$ cd proxylessnas/deploy
$ ./run.sh
```

```sh
SOURCE="/home/user/imagenet_calib/calibration.txt"
ROOT="/home/user/imagenet_calib/img/"
INPUT_SIZE=224
IMAGENET="/home/user/imagenet"
GPU=0
ARCH="proxyless_mobile_05"
```

### Quantization

```sh
$ vai_q_caffe quantize                                     \
    -model ./caffe_nets/proxylessmobile05.prototxt         \
    -weights ./caffe_nets/proxylessmobile05.caffemodel     \
    --calib_iter 100                                       \
    --gpu 0                                                \
    --output_dir ./caffe_nets/proxylessmobile05/quantize_results
```

### Compilation

```sh
$ vai_c_caffe                                                             \
    -p ./caffe_nets/proxylessmobile05/quantize_results/deploy.prototxt  \
    -c ./quantize_results/deploy.caffemodel                             \
    -a /opt/vitis_ai/compiler/arch/dpuv2/ZCU102/ZCU102.json             \
    -e  "{'mode': 'debug'}"                                             \
    --output_dir ./compiled                                             \
    -n proxylessmobile05
```

**Notice**:
`quantize_results/deploy.prototxt` might contain a redundant input layer. In that case, please remove the redundant input layer before compilation.


<details>
<summary>Compiler Output (kernel list)</summary>

```text
kernel list info for network "proxylessmobile05"
                               Kernel ID : Name
                                       0 : proxylessmobile05

                             Kernel Name : proxylessmobile05
--------------------------------------------------------------------------------
                             Kernel Type : DPUKernel
                               Code Size : 0.17MB
                              Param Size : 2.06MB
                           Workload MACs : 207.17MOPS
                         IO Memory Space : 0.34MB
                              Mean Value : 0, 0, 0, 
                      Total Tensor Count : 43
                Boundary Input Tensor(s)   (H*W*C)
               default_name_input_1:0(0) : 224*224*3

               Boundary Output Tensor(s)   (H*W*C)
             proxylessmobile05_fc_1:0(0) : 1*1*1000

                        Total Node Count : 42
                           Input Node(s)   (H*W*C)
             proxylessmobile05_conv_1(0) : 224*224*3

                          Output Node(s)   (H*W*C)
               proxylessmobile05_fc_1(0) : 1*1*1000




**************************************************
* VITIS_AI Compilation - Xilinx Inc.
**************************************************
```

</details>

#### Quantize and Compile models in batch

Alternatively, we provide a batch compilation tool to automate the quantization and compilation process.
To run the auto-compilation tool:

```sh
$ git clone https://github.com/zzzDavid/vitis-compile
$ cd vitis-compile
$ ./run.sh
```

```sh
SOURCE="/home/user/proxylessnas/deploy/caffe_nets"
RESULT="/home/user/proxylessnas/deploy/compiled"
GPU=0
```


### Deploy model on FPGA

```sh
$ git clone https://github.com/zzzDavid/DPU-Profiling
```

```sh
$ python to_so.py
$ cp ./so/* /usr/lib
```

```sh
$ python3 latency.py
```

<details>
<summary>Latency Profiling Result</summary>

```text
=====================================================================================================
[DNNDK] Performance profile - DPU Kernel "proxylessmobile05" DPU Task "proxylessmobile05-0"
=====================================================================================================
  ID                       NodeName Workload(MOP) Mem(MB) RunTime(ms) Perf(GOPS) Utilization    MB/S
   1       proxylessmobile05_conv_1        14.451    0.34       0.134      107.8         8.8%  2550.7
   2       proxylessmobile05_conv_3         3.211    0.29       0.063       51.0         4.1%  4620.3
   3       proxylessmobile05_conv_4         8.580    0.17       0.173       49.6         4.0%   994.7
   4       proxylessmobile05_conv_6         2.408    0.12       0.037       65.1         5.3%  3300.8
   5       proxylessmobile05_conv_7         7.526    0.20       0.106       71.0         5.8%  1847.4
   6       proxylessmobile05_conv_9         4.817    0.24       0.060       80.3         6.5%  4071.0
   7      proxylessmobile05_conv_10         8.505    0.09       0.133       63.9         5.2%   672.4
   8      proxylessmobile05_conv_12         1.806    0.06       0.025       72.3         5.9%  2255.1
   9      proxylessmobile05_conv_13         3.726    0.08       0.061       61.1         5.0%  1258.0
  10      proxylessmobile05_conv_15         2.710    0.09       0.036       75.3         6.1%  2607.9
  11      proxylessmobile05_conv_16         5.532    0.08       0.114       48.5         3.9%   682.8
  12      proxylessmobile05_conv_18         2.710    0.09       0.037       73.2         6.0%  2537.4
  13      proxylessmobile05_conv_19         5.532    0.08       0.114       48.5         3.9%   682.8
  14      proxylessmobile05_conv_21         2.710    0.09       0.037       73.2         6.0%  2537.4
  15      proxylessmobile05_conv_22         8.185    0.06       0.131       62.5         5.1%   449.3
  16      proxylessmobile05_conv_24         2.258    0.04       0.024       94.1         7.7%  1713.6
  17      proxylessmobile05_conv_25         3.058    0.04       0.068       45.0         3.7%   592.9
  18      proxylessmobile05_conv_27         1.882    0.04       0.028       67.2         5.5%  1562.0
  19      proxylessmobile05_conv_28         3.058    0.04       0.068       45.0         3.7%   592.9
  20      proxylessmobile05_conv_30         1.882    0.04       0.028       67.2         5.5%  1562.0
  21      proxylessmobile05_conv_31         3.058    0.04       0.069       44.3         3.6%   584.3
  22      proxylessmobile05_conv_33         1.882    0.04       0.027       69.7         5.7%  1619.8
  23      proxylessmobile05_conv_34         6.115    0.07       0.118       51.8         4.2%   614.5
  24      proxylessmobile05_conv_36         4.516    0.07       0.028      161.3        13.1%  2363.9
  25      proxylessmobile05_conv_37         4.121    0.05       0.077       53.5         4.4%   640.7
  26      proxylessmobile05_conv_39         2.710    0.05       0.028       96.8         7.9%  1905.6
  27      proxylessmobile05_conv_40         4.121    0.05       0.079       52.2         4.2%   624.5
  28      proxylessmobile05_conv_42         2.710    0.05       0.029       93.4         7.6%  1839.9
  29      proxylessmobile05_conv_43         4.121    0.05       0.078       52.8         4.3%   632.5
  30      proxylessmobile05_conv_45         2.710    0.05       0.029       93.4         7.6%  1839.9
  31      proxylessmobile05_conv_46         6.802    0.06       0.107       63.6         5.2%   515.6
  32      proxylessmobile05_conv_48         2.710    0.05       0.028       96.8         7.9%  1651.1
  33      proxylessmobile05_conv_49         8.185    0.12       0.183       44.7         3.6%   666.5
  34      proxylessmobile05_conv_51         5.419    0.09       0.035      154.8        12.6%  2594.5
  35      proxylessmobile05_conv_52         4.092    0.06       0.101       40.5         3.3%   627.7
  36      proxylessmobile05_conv_54         2.710    0.05       0.032       84.7         6.9%  1593.2
  37      proxylessmobile05_conv_55         4.092    0.06       0.099       41.3         3.4%   640.4
  38      proxylessmobile05_conv_57         2.710    0.05       0.030       90.3         7.3%  1699.4
  39      proxylessmobile05_conv_58         8.185    0.12       0.182       45.0         3.7%   670.2
  40      proxylessmobile05_conv_60         9.032    0.13       0.038      237.7        19.3%  3297.8
  41      proxylessmobile05_conv_61        20.070    0.23       0.170      118.1         9.6%  1332.2
  42         proxylessmobile05_fc_1         2.560    1.24       0.235       10.9         0.9%  5278.4

                Total Nodes In Avg:
                                All       207.171    5.33       3.279       63.2         5.1%  1625.5
=====================================================================================================
```
</details>

## References

[Vitis-AI User Documentation](https://www.xilinx.com/html_docs/vitis_ai/1_2/zkj1576857115470.html)


