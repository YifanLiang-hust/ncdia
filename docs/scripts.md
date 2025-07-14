# Scripts

The OpenHAIV framework provides a comprehensive set of shell scripts that help users quickly execute various experiments. These scripts are organized by task type and provide a convenient way to run experiments with pre-configured settings. This document explains the script directory structure, naming conventions, and how to use and customize these scripts.

## Script Directory Structure

The scripts are organized in the following directory structure:

```
scripts/
├── sl/           # Supervised Learning scripts
├── inc/          # Incremental Learning scripts
├── ood/          # Out-of-Distribution Detection scripts
└── ncdia_*.sh    # Neural Component Decoupling scripts
```

### Naming Conventions

Scripts follow a consistent naming pattern that indicates:
1. The task type (e.g., `sl_`, `inc_`, `det_`)
2. The dataset (e.g., `oes_`, `cifar10_`, `cub_`)
3. The model architecture (e.g., `rn18`, `coop-b16`)
4. The algorithm or method (e.g., `msp`, `lwf`, `savc`)

Example: `sl_oes_coop-b16.sh` indicates a Supervised Learning task on the OES dataset using CoOp-CLIP-B/16 model.

## Script Types

### Supervised Learning Scripts (`sl/`)

These scripts train models in a standard supervised learning setting. They typically use the `PreTrainer` trainer and standard classification losses.

Example:
```bash
# Benchmark: OES
# Model: ResNet18
# Method: Cross-Entropy
# Task: Supervised Learning
python ncdia/train.py \
    --cfg configs/pipeline/supervised_learning/sl_oes_rn18.yaml \
    --opts device='cuda:0'
```

Common supervised learning scripts include:
- `sl_oes_rn18.sh`: Training a ResNet18 model on OES dataset
- `sl_cifar10_rn18.sh`: Training a ResNet18 model on CIFAR10 dataset
- `sl_oes_coop-b16.sh`: Training a CoOp-CLIP-B/16 model on OES dataset

### Incremental Learning Scripts (`inc/`)

These scripts train models in an incremental learning setting, where new classes are introduced over time. There are two main subtypes:

#### Class-incremental Learning
Example:
```bash
# Benchmark: CUB200
# Model: ResNet18
# Method: LwF
# Task: Class-incremental Learning
python ncdia/train.py \
    --cfg configs/pipeline/incremental_learning/inc_cub_lwf.yaml \
    --opts device='cuda:0'
```

#### Few-shot Class-incremental Learning
Example:
```bash
# Benchmark: BM200
# Model: ResNet18
# Method: SAVC
# Task: Few-shot Class-incremental Learning
python ncdia/train.py \
    --cfg configs/pipeline/incremental_learning/inc_BM200_savc.yaml \
    --opts device='cuda:0'
```

Common incremental learning scripts include:
- `inc_cub_lwf.sh`: Learning without Forgetting on CUB200 dataset
- `inc_cub_icarl.sh`: iCaRL method on CUB200 dataset
- `inc_BM200_savc.sh`: SAVC method on BM200 dataset (few-shot setting)

### Out-of-Distribution Detection Scripts (`ood/`)

These scripts train and evaluate models for out-of-distribution detection, identifying samples that don't belong to the training distribution.

Example:
```bash
# Benchmark: OES
# Model: ResNet50
# Method: MSP
# Task: Out-of-Distribution Detection
python ncdia/train.py \
    --cfg configs/pipeline/ood_detection/det_oes_rn50_msp.yaml \
    --opts device='cuda:0'
```

Common OOD detection scripts include:
- `det_oes_rn50_msp.sh`: Maximum Softmax Probability method with ResNet50 on OES dataset
- `det_oes_rn50_mls.sh`: Maximum Logit Score method with ResNet50 on OES dataset
- `det_oes_clip-b16_glmcm.sh`: GL-MCM method with CLIP-B/16 on OES dataset

### Batch Processing Scripts

Some scripts are designed to run multiple related experiments in sequence:

Example:
```bash
# Benchmark: OES
# Model: Multiple CLIP-based Models
# Method: Multiple Detection Methods
# Task: Out-of-Distribution Detection (With Training)

bash scripts/ood/det_oes_coop-b16_mcm.sh
bash scripts/ood/det_oes_locoop-b16_glmcm.sh
bash scripts/ood/det_oes_sct-b16_glmcm.sh
bash scripts/ood/det_oes_dpm-b16_dpm.sh
# More scripts...
```

## Customizing Scripts

You can customize existing scripts or create new ones by following these steps:

1. **Copy an Existing Script**:
   ```bash
   cp scripts/sl/sl_oes_rn18.sh scripts/sl/sl_oes_mymodel.sh
   ```

2. **Modify the Configuration Path**:
   ```bash
   # Change this line
   --cfg configs/pipeline/supervised_learning/sl_oes_rn18.yaml \
   # To point to your custom configuration
   --cfg configs/pipeline/supervised_learning/sl_oes_mymodel.yaml \
   ```

3. **Update the Script Comments** to reflect your experiment:
   ```bash
   # Benchmark: OES
   # Model: MyModel
   # Method: MyMethod
   # Task: Supervised Learning
   ```

## Running Scripts

To run a script, simply execute it with bash:

```bash
bash scripts/sl/sl_oes_rn18.sh
```

You can also override configuration parameters directly from the command line:

```bash
bash scripts/sl/sl_oes_rn18.sh --opts device='cuda:1' trainer.max_epochs=20
```

## Advanced Usage

### GPU Selection

Most scripts allow specifying the GPU device using the `device` parameter:

```bash
python ncdia/train.py \
    --cfg configs/pipeline/supervised_learning/sl_oes_rn18.yaml \
    --opts device='cuda:0'  # Use the first GPU
```

To use a different GPU, change `cuda:0` to the desired GPU index (e.g., `cuda:1`).

### Multi-stage Training

Some scripts implement multi-stage training, where different phases (e.g., training, testing) are executed sequentially:

```bash
# Training phase
python ncdia/train.py \
    --cfg configs/pipeline/ood_detection/msp/det_oes_rn50_msp_train.yaml \
    --opts device='cuda:0'

# Testing phase
python ncdia/train.py \
    --cfg configs/pipeline/ood_detection/msp/det_oes_rn50_msp_test.yaml \
    --opts device='cuda:0'
```

### Running on Multiple GPUs

For multi-GPU training, you can use the `CUDA_VISIBLE_DEVICES` environment variable:

```bash
CUDA_VISIBLE_DEVICES=0,1,2,3 python ncdia/train.py \
    --cfg configs/pipeline/supervised_learning/sl_oes_rn18.yaml \
    --opts device='cuda' trainer.distributed=True
```