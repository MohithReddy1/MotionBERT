# MotionBERT: A Unified Perspective on Learning Human Motion Representations

This is the official PyTorch implementation of the paper "<a href="https://arxiv.org/pdf/2210.06551.pdf" rel="nofollow">MotionBERT: A Unified Perspective on Learning Human Motion Representations</a>" (ICCV 2023).

<img src = "./Outputs/gifs/MotionBERT.gif"><br>

## Inference

- Please refer to [docs/inference.md](docs/inference.md) for 3D Pose Estimation and Human Mesh Recovery.

# Inference implementation in Google Colab

## Cloning GitHub Repositories 
1. Clone [MotionBERT](https://huggingface.co/walterzhu/MotionBERT) repository which contains Pretrained models and checkpoints.

2. Clone [AlphaPose](https://github.com/MVIG-SJTU/AlphaPose.git) repository.

3. Install required libraries. And import required libraries.

4. Use T4 GPU for runtime in colab Check if a CUDA-compatible GPU is available and set device.

### Setup AlphaPose

5. Install additional dependencies for AlphaPose.

6. Setup the Environment to run AlphaPose.

7. Authenticate and Download Pre-trained Models to Google Drive and create directories in AlphaPose folder and store that Pre-trained Models in colab.

8. Upload the custom video for further processing in order to execute AlphaPose on it.

9. Copy the path of the video

```
For example: copy this -->  /content/pose.mp4
```
### Generate the 2D key-points from AlphaPose

10. Change directory to AlphaPose and generate the 2D key-points from AlphaPose for custom video using below code:

```
!python scripts/demo_inference.py --cfg configs/halpe_26/resnet/256x192_res50_lr1e-3_1x.yaml --checkpoint pretrained_models/halpe26_fast_res50_256x192.pth --indir examples/res/vis --video {vid_ff} --save_video
```

11. Save the video with Alphapose Keypoints. Example Output:
<br>

 <img src = "./Outputs/gifs/2d Pose.gif"><br>

### Run 3D Pose Estimation Inference

12. Run MotionBERT Inference of 3D Pose Estimation using below code:
    <br>

```
%cd /content/MotionBERT
!python /content/MotionBERT/infer_wild.py \
--vid_path {vid_ff} \
--json_path /content/AlphaPose/examples/res/alphapose-results.json \
--out_path /content/MHFormer_out
```

13. Example Output of 3D Pose Estimation for custom video.

    <img src = "./Outputs/gifs/3DPose_output.gif"><br>
<br>

14. Install additional dependencies for MotionBERT to run the Human Mesh Recovery inference. 

15. Download the datasets [here](https://1drv.ms/f/s!AvAdh0LSjEOlfy-hqlHxdVMZxWM) and put them to  `data/mesh/`. We use Human3.6M, COCO, and PW3D for training and testing. Descriptions of the joint regressors could be found in [SPIN](https://github.com/nkolot/SPIN/tree/master/data).

16. Download the SMPL model(`basicModel_neutral_lbs_10_207_0_v1.0.0.pkl`) from [SMPLify](https://smplify.is.tue.mpg.de/), put it  to `data/mesh/`, and rename it as `SMPL_NEUTRAL.pkl`

### Run Human Mesh Recovery Inference

17. Run MotionBERT Inference of Human Mesh Recovery using below code:
    <br>

```
%cd /content/MotionBERT
!python /content/MotionBERT/infer_wild_mesh.py --vid_path {vid_ff} --json_path /content/AlphaPose/examples/res/alphapose-results.json --out_path /content/MeshOut --ref_3d_motion_path /content/MHFormer_out/X3D.npy
```

18. Example Output of Human Mesh Recovery for custom video.
<br>

  <img src = "./Outputs/gifs/mesh_output.gif"><br>


## MotionBERT Testing 

19. Install necessary packages.

20. Download H36M dataset and Extract the H36M pkl data in ```/content/``` directory in colab.

21. Split the dataset to get it ready for evaluation.

22. Perform testing
```%cd /content/MotionBERT
!python train.py \
--config configs/pose3d/MB_train_h36m.yaml \
--evaluate checkpoint/pose3d/MB_train_h36m/best_epoch.bin
```
```
Testing output:
Protocol #1 Error (MPJPE): 39.21385534671374 mm
Protocol #2 Error (P-MPJPE): 32.93466426644884 mm
```

# Implementation in Local Environment

## Installation 

1. Download and install [Anaconda](https://docs.anaconda.com/free/anaconda/install/index.html) latest version.
2. Download and install [Python](https://www.python.org/downloads/) latest version.

3. Download and install [CUDA](https://developer.nvidia.com/cuda-downloads) ToolKit as per your machine and GPU

<br>

Now create MotionBERT environment using Anaconda and Python and install PyTorch in it.

Clone the [MotionBERT](https://github.com/Walter0807/MotionBERT) github repo in that environment.

```bash
conda create -n motionbert python=3.7 anaconda
conda activate motionbert
# Please install PyTorch according to your CUDA version.
conda install pytorch torchvision torchaudio pytorch-cuda=12.1 -c pytorch -c nvidia
pip install -r requirements.txt
```

## Getting Started

| Task                              | Document                             |
| --------------------------------- | ------------------------------------ |
| Pretrain                          | [docs/pretrain.md](docs/pretrain.md) |
| 3D human pose estimation          | [docs/pose3d.md](docs/pose3d.md)     |
| Skeleton-based action recognition | [docs/action.md](docs/action.md)     |
| Mesh recovery                     | [docs/mesh.md](docs/mesh.md)         |

## Download Models

| Model                      | Download Link                                                         | Config                                                         | Performance    |
| -------------------------- | --------------------------------------------------------------------- | -------------------------------------------------------------- | -------------- |
| MotionBERT (162MB)         | [OneDrive](https://1drv.ms/f/s!AvAdh0LSjEOlgS425shtVi9e5reN?e=6UeBa2) | [pretrain/MB_pretrain.yaml](configs/pretrain/MB_pretrain.yaml) | -              |
| MotionBERT-Lite (61MB)     | [OneDrive](https://1drv.ms/f/s!AvAdh0LSjEOlgS27Ydcbpxlkl0ng?e=rq2Btn) | [pretrain/MB_lite.yaml](configs/pretrain/MB_lite.yaml)         | -              |
| 3D Pose (H36M-SH, scratch) | [OneDrive](https://1drv.ms/f/s!AvAdh0LSjEOlgSvNejMQ0OHxMGZC?e=KcwBk1) | [pose3d/MB_train_h36m.yaml](configs/pose3d/MB_train_h36m.yaml) | 39.2mm (MPJPE) |
| 3D Pose (H36M-SH, ft)      | [OneDrive](https://1drv.ms/f/s!AvAdh0LSjEOlgSoTqtyR5Zsgi8_Z?e=rn4VJf) | [pose3d/MB_ft_h36m.yaml](configs/pose3d/MB_ft_h36m.yaml)       | 37.2mm (MPJPE) |
| Mesh (with 3DPW, ft)       | [OneDrive](https://1drv.ms/f/s!AvAdh0LSjEOlgTmgYNslCDWMNQi9?e=WjcB1F) | [mesh/MB_ft_pw3d.yaml](configs/mesh/MB_ft_pw3d.yaml)           | 88.1mm (MPVE)  |

<br>

# Inference implementation in Local Environment

1. Install PyTorch version 2.1.0

    ```
    !pip install torch==2.1.0 torchvision==0.16.0 torchaudio==2.1.0 --index-url https://download.pytorch.org/whl/cu121
    ```

2. Install required packages openmim and mmengine

3. Install mmcv version 2.1.0

    ```
    !pip install mmcv==2.1.0 -f https://download.openmmlab.com/mmcv/dist/cu121/torch2.1/index.html
    ```
4. Clone MMPose Repository. It is based on MotionBERT.

    ```
    ! git clone https://github.com/open-mmlab/mmpose.git
    ```

5. Clone MMDetection Repository

    ```
    !git clone https://github.com/open-mmlab/mmdetection.git
    ```
6. Import required libraries.

7. Run the MMPose for 3D Pose Estimation

    ```
    !python demo/body3d_pose_lifter_demo.py \
    demo/mmdetection_cfg/rtmdet_m_640-8xb32_coco-person.py \
    https://download.openmmlab.com/mmpose/v1/projects/rtmpose/rtmdet_m_8xb32-100e_coco-obj365-person-235e8209.pth \
    configs/body_2d_keypoint/rtmpose/body8/rtmpose-m_8xb256-420e_body8-256x192.py \
    https://download.openmmlab.com/mmpose/v1/projects/rtmposev1/rtmpose-m_simcc-body7_pt-body7_420e-256x192-e48f03d0_20230504.pth \
    configs/body_3d_keypoint/motionbert/h36m/motionbert_dstformer-243frm_8xb32-240e_h36m-original.py \
    https://download.openmmlab.com/mmpose/v1/body_3d_keypoint/pose_lift/h36m/motionbert_ft_h36m-d80af323_20230531.pth \
    --input /pose.mp4 \
    --output-root  vis_results
    ```
8. Example Output of 3D Pose Estimation for custom video.<br>

    <img src = "./Outputs/gifs/3D Pose_local.gif"><br>

    <br>

## Hints

1. The model could handle different input lengths (no more than 243 frames). No need to explicitly specify the input length elsewhere.

2. The model uses 17 body keypoints ([H36M format](https://github.com/JimmySuen/integral-human-pose/blob/master/pytorch_projects/common_pytorch/dataset/hm36.py#L32)). If you are using other formats, please convert them before feeding to MotionBERT.

# Benchmarking Results

Benchmarking results of AlphaPose and MotionBERT 3D Pose Inference for 5 iterations in Google Colab. 


<img src = "./Outputs/alphapose_performance.png">

                Fig: AlphaPose Benchmark Performance 

<img src = "./Outputs/motionbert_performance.png">

                Fig: MotionBERT Benchmark Performance 


