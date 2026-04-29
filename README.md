# LUMI
Tips on how to setup and use LUMI clusters.

## Storage
| Storage Type | Quota | Max Files | Expandable | Retention | Billing Rate |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **User Home** | 20 GB | 100k | No | User lifetime | NA |
| **Project Space** | 50 GB | 100k | Yes, up to 500GB | Project lifetime | 1x |
| **Project Scratch** | 50 TB | 2000k | Yes, up to 500TB | Project lifetime* | 1x |
| **Project Flash** | 2 TB | 1000k | Yes, up to 100TB | Project lifetime* | 3x |

Currently, I put everything (singularities, data, codes) under Scratch: ```/project/project_465002703/jiahe```.

## Running on Slurm
GPU Running: ```srun --partition=standard-g --account=project_465002703 --<other specifications> ```
- Use ```sinfo -s``` to view available partitions.

## Python Environment
Directly installing Anaconda is inapplicable on LUMI. Instead, I use singularity.

### Singularity
- Start by pulling an existing singularity from [docker hub](https://hub.docker.com/r/rocm/pytorch):
```
# Choose your own version
singularity pull docker://rocm/pytorch:rocm7.2_ubuntu24.04_py3.12_pytorch_release_2.7.1
```
- If you want to modify this environment, build a sandbox of it:
```
singularity build --sandbox ./torch271 pytorch_rocm7.2_ubuntu24.04_py3.12_pytorch_release_2.7.1.sif
```
- To install stuff in the sandbox:
```shell
# Load a GPU node, if needed
sigularity shell --writable $sandbox_path

pip install <your stuff>

# If everything's fine, build a .sif file for faster running
singularity build $sif_name $sandbox_path
```

- Use a singularity container to run stuff:
```
# (Recommended) use the .sif file
srun --gpus=1 --partition=standard-g --account=project_465002703 singularity exec \
  <your_name>.sif python script.py

# Use the sandbox (slower)
srun --gpus=1 --partition=standard-g --account=project_465002703 singularity exec \
  <sandbox_path> python script.py
```

- The storage amount of a singularity (take ```pytorch_rocm6.4.4_ubuntu22.04_py3.10_pytorch_release_2.4.1.sif``` as example):
```
.sif: 20G
sandbox folder: 55G, 285k files # !!files might be too much
```

## Environment Installation Logs
### Depth-Anything-3
git repo: [depth-anything-3](https://github.com/ByteDance-Seed/depth-anything-3)

1. Pull singularity: ```singularity pull docker://rocm/pytorch:rocm7.2.1_ubuntu22.04_py3.10_pytorch_release_2.7.1```
