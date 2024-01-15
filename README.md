# How to use CUDA and PyTorch on WSL2: cheatsheet

## Introduction

Welcome to this simple guide designed to help you navigate the utilization of __CUDA with PyTorch in a WSL2 (Windows Subsystem for Linux 2) environment__. This project aims to bridge the gap between Windows-based development and the powerful capabilities of Linux-driven, GPU-accelerated deep learning.

This guide has been tested on Windows 11 but should also work on Windows 10.

**This guide is work in progress :fire:**

## Table of Contents

- [Introduction](#introduction)
- [Install Nvidia GPU drive on Windows](#install-nvidia-gpu-drive-on-windows)
- [Find Compatible PyTorch Version](#find-compatible-pytorch-version)
- [Install Ubuntu through WSL2](#install-ubuntu-through-wsl2)
- [Install CUDA toolkit on Ubuntu](#install-cuda-toolkit-on-ubuntu)
- [Install OpenGL on Ubuntu](#install-opengl-on-ubuntu)
- [License](#license)

## Install Nvidia GPU drive on Windows

- Install the latest Nvidia GPU driver on Windows. Download [here](https://www.nvidia.co.uk/Download/index.aspx?lang=en-uk).

- Confirm your Nvidia GPU driver installation by opening a PowerShell session and use command.
    ```
    nvidia-smi
    ```

## Install Ubuntu through WSL2

- Windows 11 has __Windows Subsystem for Linux (WSL) 2__ pre-installed.

- In a PowerShell session (run as Admin), first update WSL 2. (This guide is tested on WSL 2 version 2.0.14.)
    ```
    wsl --update
    ```

- Then, install Ubuntu 22.04.
    ```
    wsl --install -d Ubuntu-22.04
    ```
    After downloading and installing the distribution, you will be prompted to choose your UNIX username and password.

- Confirm your WSL2 Ubuntu installation by using this command in a PowerShell terminal:
    ```
    wsl -l -v
    ```

- If you encounter this error `The stub received bad data` with error code `Wsl/Service/0x800706f7`, first uninstall Ubuntu 22.04.

    ```
    wsl --unregister Ubuntu-22.04
    ```

    Next, use this command to reset the Winsock Catalog ([reference](https://github.com/microsoft/WSL/issues/9413)).
    ```
    netsh winsock reset
    ```
    And then install Ubuntu 22.04 again.
    ```
    wsl --install -d Ubuntu-22.04
    ```

- [Reference](https://learn.microsoft.com/en-us/windows/wsl/install).

## Install CUDA toolkit on Ubuntu

### Find compatible CUDA and PyTorch version

- Reference [this](https://developer.nvidia.com/cuda-gpus) page to find your GPU's __Compute Capability__.

- Then, use [this](https://arnon.dk/matching-sm-architectures-arch-and-gencode-for-various-nvidia-cards/) page to find a __CUDA toolkit version__ compatible with your GPU's Compute Capability.

- Lastly, reference [this](https://pytorch.org/get-started/previous-versions/) page to find a __PyTorch version__ compatible with your intended CUDA toolkit.


### Install CUDA toolkit

<!-- ## Install OpenGL on Ubuntu -->

- Since WSL 2 relies on Nvidia Windows driver and GPU paravirtualisation, it is vital to install CUDA toolkit inside WSL 2 __without__ installing Nvidia Linux driver.

- Run WSL 2 Ubuntu 22.04's terminal from the Start menu.

- Remove the old GPG key.

    ```
    sudo apt-key del 7fa2af80
    sudo apt-get update
    ```

- Use [this](https://developer.nvidia.com/cuda-toolkit-archive) CUDA toolkit archive page and choose the version you want. I will use version 11.8 as an example.

- Select __Linux__ as Operating System, __x86_64__ as Architecture, __Ubuntu__ as Distribution, and select your WSL2's Ubuntu version (__22.04 in my case__). Select __deb (local)__ as Installer Type. Installation instructions will appear.

- Use their Installation Instructions __EXCEPT THE LAST LINE__, like below:
    ```
    wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2204/x86_64/cuda-ubuntu2204.pin
    sudo mv cuda-ubuntu2204.pin /etc/apt/preferences.d/cuda-repository-pin-600
    wget https://developer.download.nvidia.com/compute/cuda/11.8.0/local_installers/cuda-repo-ubuntu2204-11-8-local_11.8.0-520.61.05-1_amd64.deb
    sudo dpkg -i cuda-repo-ubuntu2204-11-8-local_11.8.0-520.61.05-1_amd64.deb
    sudo cp /var/cuda-repo-ubuntu2204-11-8-local/cuda-*-keyring.gpg /usr/share/keyrings/
    sudo apt-get update
    ```
- Then, install only CUDA Toolkit. You may need to change the version number from the command.
    ```
    sudo apt-get -y install cuda-toolkit-11-8
    ```

- Add the following lines to the end of your `$HOME/.bashrc` file.
    ```
    export PATH=/usr/local/cuda/bin${PATH:+:${PATH}}
    export LD_LIBRARY_PATH=/usr/local/cuda/lib64${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}
    ```

- Start a new WSL 2 Ubuntu terminal, and test that CUDA toolkit is successfully installed.

    ```
    nvcc --version
    ```

- [Reference](https://docs.nvidia.com/cuda/wsl-user-guide/index.html).

## License

This project is licensed under the Creative Commons Attribution-NonCommercial 4.0 International License - see the [LICENSE.md](LICENSE.md) file for details.

