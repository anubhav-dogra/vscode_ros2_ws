FROM osrf/ros:humble-desktop

# Add vscode user with same UID and GID as your host system
# (copied from https://code.visualstudio.com/remote/advancedcontainers/add-nonroot-user#_creating-a-nonroot-user)
ARG USERNAME=ros
ARG USER_UID=1000
ARG USER_GID=$USER_UID
RUN groupadd --gid $USER_GID $USERNAME \
    && useradd -s /bin/bash --uid $USER_UID --gid $USER_GID -m $USERNAME \
    && apt-get update \
    && apt-get install -y sudo \
    && echo $USERNAME ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/$USERNAME \
    && chmod 0440 /etc/sudoers.d/$USERNAME

# Switch from root to user
USER $USERNAME

# Optional: Install additional packages
# either add sudo or switch to USER root and reswitch to USER ros
ENV DEBIAN_FRONTEND=noninteractive
RUN sudo apt-get update \
    && sudo apt install -y libeigen3-dev \
    # Install Git and other utilities
    && sudo apt install -y git wget curl \
    && sudo apt-get install -y libglfw3-dev \
    # clean up
    && sudo apt-get clean \
    && sudo rm -rf /var/lib/apt/lists/*

# Create a workspace directory for colcon builds within the container
WORKDIR /home/$USERNAME/ros2_ws
# Create workspace and clone the repository
# RUN mkdir -p /home/$USERNAME/ros2_ws/src
    # && cd /home/$USERNAME/ros2_ws/src \
    # && git clone https://github.com/anubhav-dogra/software_robot_library.git

# Set up environment for sourcing the workspace automatically
RUN echo "if [ -f /opt/ros/humble/setup.bash ]; then source /opt/ros/humble/setup.bash; fi" >> /home/$USERNAME/.bashrc
RUN echo "if [ -f /home/$USERNAME/ros2_ws/install/setup.bash ]; then source /home/$USERNAME/ros2_ws/install/setup.bash; fi" >> /home/$USERNAME/.bashrc

# Install MuJoCo in /opt
ENV MUJOCO_VERSION="3.2.6"
ENV MUJOCO_URL="https://github.com/google-deepmind/mujoco/releases/download/${MUJOCO_VERSION}/mujoco-3.2.6-linux-x86_64.tar.gz"
ENV MUJOCO_DIR="/opt/mujoco"
RUN sudo apt-get update && sudo apt-get install -y \
    curl \
    tar \
    && sudo apt-get clean \
    && sudo rm -rf /var/lib/apt/lists/*

# Download and extract MuJoCo
RUN sudo mkdir -p ${MUJOCO_DIR} \
    && curl -L ${MUJOCO_URL} | sudo tar -xz -C ${MUJOCO_DIR} \
    && sudo rm -rf /var/lib/apt/lists/*

# Optional: Set up any additional configuration for MuJoCo (e.g., licensing, etc.)
# COPY ./mjkey.txt /opt/mujoco/

# Set environment variables for MuJoCo
ENV MUJOCO_PY_MUJOCO_PATH=${MUJOCO_DIR}/mujoco-${MUJOCO_VERSION}
ENV LD_LIBRARY_PATH="${MUJOCO_PY_MUJOCO_PATH}/bin:${LD_LIBRARY_PATH}"

# Set the shell to bash
SHELL ["/bin/bash", "-c"]

# Entry point: Switch to workspace
ENTRYPOINT ["/bin/bash"]
