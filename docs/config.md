# Configuration System

The OpenHAIV framework uses a flexible YAML-based configuration system that allows users to easily define and modify various experimental parameters without changing the code. This document provides detailed information about the structure, usage, and major components of the configuration files.

## Configuration File Structure

OpenHAIV configuration files follow a hierarchical structure, mainly divided into the following sections:

1. **Base Configuration**: Defines basic parameters for experiments, such as random seed, device, output directory, etc.
2. **Trainer Configuration**: Defines parameters for the training process
3. **Model Configuration**: Defines model architecture and related parameters
4. **Algorithm Configuration**: Defines specific algorithms and methods
5. **Data Loader Configuration**: Defines datasets and data loading parameters
6. **Optimizer Configuration**: Defines optimization algorithms and related parameters
7. **Scheduler Configuration**: Defines learning rate scheduling strategies

Configuration files can inherit from other configuration files through the `_base_` field, enabling modular and reusable configurations.

## Configuration Modules in Detail

### config.algorithms

Algorithm configurations define the specific algorithms and methods used in experiments, including:

#### Supervised Learning Algorithms
```yaml
algorithm:
  type: StandardSL  # Standard supervised learning
```

#### Class-incremental Learning Algorithms
```yaml
algorithm:
  type: LwF  # Learning without Forgetting
  # Algorithm-specific parameters
  temperature: 2.0
  alpha: 1.0
```

Other supported class-incremental learning algorithms include: `Finetune`, `Joint`, `iCaRL`, `EWC`, `BiC`, `WA`, `DER`, `Coil`, `FOSTER`, `SSRE`, `FeTrIL`, `MEMO`, etc.

#### Few-shot Class-incremental Learning Algorithms
```yaml
algorithm:
  type: SAVC  # Few-shot class-incremental learning algorithm
  # Algorithm-specific parameters
  alpha: 1.0
  beta: 0.1
```

#### Out-of-Distribution Detection Algorithms
```yaml
algorithm:
  type: MSP  # Maximum Softmax Probability
  # or
  type: MLS  # Maximum Logit Score
  # or
  type: MCM  # Maximum Concept Matching
```

Other supported out-of-distribution detection algorithms include: `ODIN`, `VIM`, `MDS`, `DML`, `FDBD`, `GODIN`, `MCM`, `GL-MCM`, etc.

### config.dataloader

Data loader configurations define the datasets and data loading parameters used in experiments:

```yaml
_base_: configs/dataloader/OES/oes.yaml  # Inherit base dataset configuration

dataloader:
  train:
    dataset:
      type: OES  # Dataset type
      root: /path/to/dataset  # Dataset path
      split: train  # Dataset split
    batch_size: 128  # Batch size
    num_workers: 4  # Number of data loading worker threads
    shuffle: True  # Whether to shuffle data
  
  val:  # Validation set configuration
    dataset:
      type: OES
      root: /path/to/dataset
      split: val
    batch_size: 128
    num_workers: 4
    shuffle: False
```

Supported datasets include: `OES`, `CIFAR10`, `CIFAR100`, `ImageNet`, `ImageNetR`, `CUB200`, `Remote`, `Food101`, `Caltech101`, etc.

### config.model

Model configurations define the model architecture and related parameters used in experiments:

```yaml
model:
  type: ResNet18  # Model type
  # Or use a more complex definition
  type: ResNet_Base
  network:
    type: ResNet18
    num_classes: 94
  checkpoint: "/path/to/checkpoint.pth"  # Pre-trained model path
  loss: CrossEntropyLoss  # Loss function type
```

Supported models include:
- Standard CNN models: `ResNet18`, `ResNet34`, `ResNet50`, `ResNet101`
- Vision-language models: `CLIP-B/16`, `CLIP-B/32`, `RSCLIP-B/16`, `RSCLIP-B/32`
- Algorithm-specific models: `AliceNET`, `CoOp`, `LoCoOp`, `SCT`, `DPM`

### config.pipeline

Pipeline configuration files integrate all of the above components into a complete experiment configuration, for example, a supervised learning pipeline configuration:

```yaml
_base_: configs/dataloader/OES/oes.yaml  # Inherit data loader configuration

trainer:
  type: PreTrainer  # Trainer type
  max_epochs: 10  # Maximum training epochs

algorithm:
  type: StandardSL  # Algorithm type

model:
  type: ResNet18  # Model type

criterion:
  type: CrossEntropyLoss  # Loss function

optimizer:
  type: Adam  # Optimizer
  lr: 0.0003  # Learning rate
  weight_decay: 0.0001  # Weight decay

scheduler:
  type: Constant  # Scheduler type

seed: 0  # Random seed
device: cuda  # Device

exp_name: exp  # Experiment name
work_dir: ./output/supervised/sl_oes_rn18  # Output directory
```

## How to Use Configurations

Configuration files can be passed to the training script via command line:

```bash
python ncdia/train.py --cfg configs/pipeline/supervised_learning/sl_oes_rn18.yaml --opts device='cuda:0'
```

The `--opts` parameter allows overriding any parameter in the configuration file.

## Configuration Inheritance Mechanism

OpenHAIV's configuration system supports composition based on inheritance, making configurations more modular and reusable:

```yaml
_base_: [
  configs/dataloader/OES/oesfull.yaml,  # Inherit data loader configuration
  configs/algorithms/ood_detection/dml.yaml,  # Inherit algorithm configuration
]

# Override or add specific parameters
trainer:
  type: DetTrainer
  max_epochs: 10

model:
  type: ResNet_Base
  network:
    type: ResNet18
    num_classes: 94
```

Through the inheritance mechanism, repetitive configurations can be avoided, improving the maintainability of configuration files.

## Configuration Best Practices

1. **Modular Configuration**: Extract common configurations as base configuration files, and combine them through inheritance
2. **Use Relative Paths**: Paths in configuration files should use paths relative to the project root directory when possible
3. **Comment Key Parameters**: Add comments to important parameters to improve readability
4. **Parameter Tuning**: For new tasks, start with existing configurations and gradually adjust parameters
5. **Version Control**: Configuration files for important experiments should be version controlled to reproduce results
