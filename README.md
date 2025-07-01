# CodeFilling(CVPR2024)
Communication-Efficient Collaborative Perception via
Information Filling with Codebook


## Installation
Please refer to OpenCOOD's [data introduction](https://opencood.readthedocs.io/en/latest/md_files/data_intro.html) to prepare
data.

1. Clone this repository and navigate to the root folder.
2. Create a new environment:
    ```bash
    conda create -n codefilling python=3.10
    conda activate codefilling
    ```
3. Install PyTorch (Torch 2.7.1 with CUDA 12.6):
    ```bash
    pip install torch torchvision
    ```
4. Install dependencies:
    ```bash
    pip install -r requirements.txt
    ```
5. Install `spconv`:
    ```bash
    pip install spconv-cu126
    ```

6. Build custom extensions:
    ```bash
    python setup.py develop
    python opencood/utils/setup.py build_ext --inplace
    # Optional:
    python opencood/pcdet_utils/setup.py build_ext --inplace
    ```

7. Prepare logs and modality assignment:
    ```bash
    mkdir -p opencood/logs
    cp -r opencood/modality_assign opencood/logs/heter_modality_assign
    ```

## Quick Start

### Train your model
We adopt the same setting as OpenCOOD which uses yaml file to configure all the parameters for training. To train your own model from scratch or a continued checkpoint, run the following commonds:
```python
python opencood/tools/train.py --hypes_yaml ${CONFIG_FILE} [--model_dir  ${CHECKPOINT_FOLDER}]
```
Arguments Explanation:
- `hypes_yaml`: the path of the training configuration file, e.g. `opencood/hypes_yaml/second_early_fusion.yaml`, meaning you want to train
an early fusion model which utilizes SECOND as the backbone. See [Tutorial 1: Config System](https://opencood.readthedocs.io/en/latest/md_files/config_tutorial.html) to learn more about the rules of the yaml files.
- `model_dir` (optional) : the path of the checkpoints. This is used to fine-tune the trained models. When the `model_dir` is
given, the trainer will discard the `hypes_yaml` and load the `config.yaml` in the checkpoint folder.

### Test the model (benchmark setting)



```python
python opencood/tools/inference_comm_bp_plus.py --model_dir ${CHECKPOINT_FOLDER} --fusion_method ${FUSION_STRATEGY} --modal ${modality_id} --comm_thre ${communication_threshold} [--result_name] [--note] [--w_solver] [--solver_thre] [--solver_method]

```
Arguments Explanation:
- `model_dir`: the path to your saved model.
- `fusion_method`: indicate the fusion strategy, currently support 'early', 'late', and 'intermediate'. CodeFilling is a kind of intermediate strategy.
- `modal`: indicate the modality type, currently support 'lidar_only: 0', 'camera_only: 1', 'ego_lidar_other_camera: 2', 'ego_camera_other_lidar: 3', 'random modality: 4' .
- `comm_thre`: indicate the threshold for selecting transmitted area.
- `w_solver`: whether use information filling based solver
- `solver_thre`: the desired maximum information demand
- `solver_method`: choose from max and sum

More detailed arguments can be found in the file. The evaluation results will be dumped in the model directory.

### Test the model (extra setting)

#### latency robustness 

```python
python opencood/tools/inference_comm_bp_plus.py --delay_time ${delay_time} --model_dir ${CHECKPOINT_FOLDER} --fusion_method ${FUSION_STRATEGY} --modal ${modality_id} --comm_thre ${communication_threshold} [--result_name] [--note]

```
Extra Arguments Explanation:
- `delay_time`: emulated delayed time


#### pose error robustness 

```python
python opencood/tools/inference_comm_w_noise.py --model_dir ${CHECKPOINT_FOLDER} --fusion_method ${FUSION_STRATEGY} --modal ${modality_id} --comm_thre ${communication_threshold} [--result_name] [--note]

No extra argument is required. Noise setting needs to be added to the configuration yaml file.

The evaluation results will be dumped in the model directory.


### Train your model
OpenCOOD uses yaml file to configure all the parameters for training. To train your own model
from scratch or a continued checkpoint, run the following commonds:
```python
python opencood/tools/train.py --hypes_yaml ${CONFIG_FILE} [--model_dir  ${CHECKPOINT_FOLDER}]
```
Arguments Explanation:
- `hypes_yaml` or `-y`: the path of the training configuration file, e.g. `opencood/hypes_yaml/second_early_fusion.yaml`, meaning you want to train
an early fusion model which utilizes SECOND as the backbone. See [Tutorial 1: Config System](https://opencood.readthedocs.io/en/latest/md_files/config_tutorial.html) to learn more about the rules of the yaml files.
- `model_dir` (optional) : the path of the checkpoints. This is used to fine-tune the trained models. When the `model_dir` is
given, the trainer will discard the `hypes_yaml` and load the `config.yaml` in the checkpoint folder.

If you train DiscoNet with Knowledge Distillation, use opencood/tools/train_kd.py

## Citation
 If you are using our OpenCOOD framework or OPV2V dataset for your research, please cite the following paper:
 ```bibtex
@inproceedings{YueCodeFilling:CVPR2024,
  author = {Yue Hu, Juntong Peng, Sifei Liu, Junhao Ge, Si Liu, Siheng Chen},
  title = {Communication-Efficient Collaborative Perception via Information Filling with Codebook},
  booktitle = {2024 IEEE / CVF Computer Vision and Pattern Recognition Conference (CVPR)},
  year = {2024}}
```

Thank for the cooperative perception codebases [OpenCOOD](https://github.com/DerrickXuNu/OpenCOOD) and [HEAL](https://github.com/yifanlu0227/HEAL).
