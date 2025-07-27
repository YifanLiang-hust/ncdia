
# 🚀 Quick Start

## 📦 Installation

It is recommended to use anaconda3 to manage and maintain the python library environment.

1. Download the .sh file from the anaconda3 website
2. Install anaconda3 with .sh file

```bash
bash Anaconda3-2023.03-Linux-x86_64.sh
```

## 🔧 Environment Setup

Create and activate a virtual environment:
```bash
conda create -n openhaiv python=3.10 -y
conda activate openhaiv
pip install -r requirements.txt
python setup.py install
```

Required packages:

* pytorch>=1.12.0 torchvision>=0.13.0 (recommend official torch command)
* numpy>=1.26.4
* scipy>=1.14.0
* scikit-learn>=1.5.1

## 🏃‍♂️ Running Examples

### **🚨 Out-of-Distribution Detection**

```bash
python ncdia/train.py --cfg configs/pipeline/ood_detection/msp/det_oes_rn50_msp_train.yaml --opts device='cuda:0'
```

### **🌱 Class-incremental Learning**

```bash
bash ./scripts/inc_BM200_lwf.sh  
```

### **🔍 Novel Class Discovery**

```bash
# Set required parameters
# - model weight in weight_path
# - id_txt_file and ood_txt_file
# - output_dir

python ncd.py
```
