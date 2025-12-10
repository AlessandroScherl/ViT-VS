# ViT-VS: On the Applicability of Pretrained Vision Transformer Features for Generalizable Visual Servoing [IROS 2025]

<div align="center">
  <img src="docs/static/videos/sorting.gif" alt="ViT-VS Teaser" width="800px">
  <p>
    <a href="https://alessandroscherl.github.io/ViT-VS/">
      <img src="https://img.shields.io/badge/Project-Page-blue?style=flat-square" alt="Project Page">
    </a>
    <a href="https://arxiv.org/abs/2503.04545">
      <img src="https://img.shields.io/badge/Paper-arXiv-red?style=flat-square" alt="arXiv">
    </a>
  </p>
</div>

## Overview

ViT-VS is a visual servoing approach that leverages pretrained vision transformers for semantic feature extraction. Our framework combines the advantages of classical and learning-based visual servoing methods:

- **Universal Applicability**: No task-specific training required
- **Semantic Robustness**: High convergence rates even with image perturbations
- **Category-level Generalization**: Works with unseen objects from same category


## Prerequisites for Running Vit-VS in Simulation (Docker:ROS1+GAZEBO)

1. **Install Docker**
   - Follow the official guide: [Docker Installation](https://docs.docker.com/engine/install/)

2. **Install NVIDIA Container Toolkit (for GPU support)**
   - Follow the official guide: [NVIDIA Container Toolkit Installation](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/latest/install-guide.html)

## Getting Started

1. **Clone the repository**
   ```bash
   git clone https://github.com/AlessandroScherl/ViT-VS.git
   ```
   
2. **Configure GPU support (optional)**
   - To disable GPU support, comment out the GPU version and enable the non-GPU in buildandrun.sh

3. **Build and run Docker container**
   ```bash
   ./buildandrun.sh
   ```

4. **Inside the Docker container:**
   ```bash
   catkin_make
   cd catkin_ws/src/ibvs/src
   ```

## Running Visual Servoing

The visual servoing system can be run with different feature detection methods using a single unified script:

```bash
./run_ibvs.sh --method [--config] [--perturbation]
```

### Arguments:

- `--method`: Specify the feature detection method (required)
  - Options: `sift`, `orb`, `akaze`, `dino`
- `--config`: Specify a custom configuration file (optional)
  - Default: `config.yaml`
- `--perturbation`: Enable perturbation mode (optional)
  - Adds image perturbation during visual servoing

### Examples:

```bash
# Run with SIFT feature detection
./run_ibvs.sh --method sift

# Run with ORB and perturbation
./run_ibvs.sh --method orb --perturbation

# Run DINOv2 with a custom config
./run_ibvs.sh --method dino --config custom_config.yaml

# Run AKAZE with all options
./run_ibvs.sh --method akaze --config custom_config.yaml --perturbation
```
   
At this point:
- Gazebo and RViz should start
- Visual Servoing code will begin running
- Simulation runs according to the config file in catkin_ws/ibvs/config

## Real-World Deployment

For real-world use, you'll need:

- A robot capable of utilizing a velocity controller for the end-effector (servoing)
- A RGBD camera mounted on the endeffector
- Preferably a Nvidia GPU

We utilized:
- An Intel RealSense D435i
- ROS `ur5_twist_controller` from [Universal Robots ROS Driver](https://github.com/UniversalRobots/Universal_Robots_ROS_Driver)
- TF2 for calculation of the camera transformation to the TCP

It is recommended to utilize a ROS framework since then it is straightforward to reutilize the ViT-VS code by just turning off the simulation part. Instead of subscribing to the simulated camera topics and sending the velocities to the simulated environment, you would gather data from the real camera and send the calculated velocities to the real robot.


## Additional Configuration

### Alternative Feature Matching
To run the Visual Servoing with SIFT+BF corresponding matching:
```bash
catkin_make
cd catkin_ws/src/ibvs/src
./run_sift.sh
```

### Enabling Gazebo GUI
- Open catkin_ws/ibvs/launch/ibvs.launch
- Set the argument "gui" to true

### Working with Perturbed Models
Create the perturbed models:
```bash
cd ~/catkin_ws/src/ibvs
python3 generate_perturbed_models.py
```

### Viewing Results
To inspect a .npz file run:
```bash
python3 eval_conv_pose.py.py results_config_sift_standard.npz
```

## Acknowledgments

We would like to express our gratitude to the [DINOv2](https://github.com/facebookresearch/dinov2) team for their excellent work on self-supervised vision transformers.

## Citation

If you find this work useful for your research, please cite our paper:

```bibtex
@article{scherl2025vit-vs,
  title={ViT-VS: On the Applicability of Pretrained Vision Transformer Features for Generalizable Visual Servoing},
  author={Scherl, Alessandro and Thalhammer, Stefan and Neuberger, Bernhard and W\"{o}ber, Wilfried and Garc\'ia-Rodr\'iguez, Jos\'e},
  journal={arXiv preprint arXiv:2503.04545},
  year={2025}
}
```
