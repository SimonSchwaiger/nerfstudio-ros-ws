# Nerfstudio ROS Noetic Workspace

This repository includes tools to quickly get started with semantic mapping in Nerfstudio and ROS Noetic. As outlined in the [nerf_bridge repository (link)](https://github.com/javieryu/nerf_bridge), getting ROS and Nerfstudio installed in the same environment can be painful. This workspace takes care of that.

The workspace is based on my [ros-ml-container (link)](https://github.com/SimonSchwaiger/ros-ml-container) in order to prebuild images with ROS and the correct CUDA dependencies in GitHub's cloud. This repository modifies *Dockerfile.remote* to install Nerfstudio, Tiny Cuda NN, Lerf and COLMAP. The container is not loaded as a submodule, as I am unlikely to change the ROS1 configuration in the original repository.

## Installation ML Container with ROS and Nerfstudio (Linux)

This setup is intended to be used on Linux and requires an Nvidia GPU with the proprietary drivers installed. Modern Ubuntu versions nowadays package the Nvidia driver. On Ubuntu 22.04 and later use [Ubuntu's official guide (link)](https://ubuntu.com/server/docs/nvidia-drivers-installation).

### Install Docker

Uses a convenience script to install Docker on Ubuntu. See [the official Docker install instructions (link)](https://docs.docker.com/engine/install/ubuntu/#install-using-the-convenience-script).

```bash
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh ./get-docker.sh --dry-run
```

$\to$ If the dry run was successful, proceed with the installation:

```bash
sudo sh ./get-docker.sh
```

### Install Nvidia Container Toolkit

Nvidia Container Toolkit allows the GPU to be passed to the Docker container. Follow [Nvidia's official documentation (link)](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/1.16.2/install-guide.html).

Use the following command to test whether or not your GPU can be passed to a container.

```bash
docker run --gpus all hello-world
```

### Configure Sudoless Docker

Ddd you user to the Docker group to enable `docker` to run without `sudo`

```bash
sudo groupadd docker
sudo usermod -aG docker $USER
```

Now restart the PC.

The following command queries the Docker Daemon to test if Docker can be run.

```bash
docker run hello-world
```

## Workspace

### Directory Structure

* **requirements.txt**
   Defines python packages that are installed at container startup. The build script installs these Python packages in the same Python environment as the ROS Python interface.

* **src**
   Includes ROS packages locally build upon container startup. ROS dependencies are automatically installed using rosdep. This directory is mounted to `/catkin_ws/src` in the container.

* **app**
   Includes files that exist outside of ROS packages (e.g., Jupyter notebooks). This directory is moutned to `/app` in the container.

* **Dockerfile.remote**
   Final Dockerfile used to locally install required packages missing from the image prebuilt in the cloud. Modify this Dockerfile to install further software in the container.

### Run Workspace

Unzip the workspace and navigate to the root of ros_ml_container. Run the following command to start the container.

```bash
GRAPHICS_PLATFORM=nvidia ROS_DISTRO=noetic DOCKER_RUN_ARGS="--net host" bash buildandrun.sh
```

Open the [link starting with 127.0...](http://127.0.0.1:8888) shown in the terminal. On the login page, write `ros_ml_container` as the password and login. The following section describes how to use the development environment. If you prefer to develop using VSCode, you can install the [Remote Development (link)](https://code.visualstudio.com/docs/remote/remote-overview) and [Docker (link)](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-docker) extensions and connect to the running container.

### Manual: How to Use JupyterLab

#### 1. **Navigating the Interface**

1. **File Browser**: Located on the left sidebar, it lets you browse, open, and manage files and directories.
   
2. **Launcher**: The Launcher tab, accessible from the left sidebar or by clicking the "+" icon in the file browser, allows you to create new notebooks, text files, terminals, and other documents.

3. **Tabs and Panels**: JupyterLab uses a tabbed interface. Opened files and terminals appear as tabs in the main work area. You can arrange these tabs side-by-side or in a grid.

#### 2. **Using Terminals**

1. **Open a Terminal**:
   - From the Launcher, click the Terminal icon under the Other section.
   - A terminal window will open in a new tab, allowing you to run shell commands.

#### 3. **Saving and Exporting Work**

1. **Save**: JupyterLab automatically saves your work, but you can manually save it by clicking the save icon or pressing `Ctrl + S`.

2. **Synced Folders**: JupyterLab runs in a Docker container and therefore, files saved in JupyterLab will **not be persistent**. Only save files in **/app/** and **/catkin_ws/src**, since these folders are synchronised with *./app* and *./src* in the workspace folder.

#### 4. **Shutting Down JupyterLab**

1. **Ctrl+D** in the terminal where the container has originally been started exits the workspace.