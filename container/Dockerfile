# SAM3 Docker Environment for GeForce RTX 3060 Ti
# Base image with CUDA 12.6 support on Ubuntu 24.04
FROM nvidia/cuda:12.6.0-cudnn-devel-ubuntu24.04

# Set environment variables
ENV DEBIAN_FRONTEND=noninteractive
ENV PYTHONUNBUFFERED=1
ENV CUDA_HOME=/usr/local/cuda
ENV PATH=${CUDA_HOME}/bin:${PATH}
ENV LD_LIBRARY_PATH=${CUDA_HOME}/lib64:${LD_LIBRARY_PATH}
# Disable PEP 668 (externally-managed-environment) for Docker container
ENV PIP_BREAK_SYSTEM_PACKAGES=1

# Install system dependencies
# Ubuntu 24.04 comes with Python 3.12 by default
RUN apt-get update && apt-get install -y \
    python3 \
    python3-dev \
    python3-pip \
    git \
    wget \
    curl \
    vim \
    build-essential \
    libgl1 \
    libglib2.0-0 \
    libsm6 \
    libxext6 \
    libxrender-dev \
    libgomp1 \
    && rm -rf /var/lib/apt/lists/*

# Create symbolic link for python
RUN update-alternatives --install /usr/bin/python python /usr/bin/python3 1

# Upgrade pip (ignore system packages to avoid conflicts)
RUN python -m pip install --upgrade --ignore-installed pip setuptools wheel

# Set working directory
WORKDIR /workspace/sam3

# Copy requirements files for better caching
COPY container/requirements.txt /tmp/requirements.txt
COPY pyproject.toml ./

# Install PyTorch with CUDA 12.6 support
RUN pip install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu126

# Install all dependencies from requirements.txt
RUN pip install -r /tmp/requirements.txt

# Copy the rest of the application
COPY . .

# Copy helper scripts
COPY container/run_jupyter.sh /workspace/sam3/run_jupyter.sh
RUN chmod +x /workspace/sam3/run_jupyter.sh

# Install SAM3 in editable mode
RUN pip install -e .

# Create directories for models and data
RUN mkdir -p /workspace/models /workspace/data

# Expose Jupyter port
EXPOSE 8888

# Set environment variable for model cache
ENV TORCH_HOME=/workspace/models
ENV HF_HOME=/workspace/models/huggingface

# Default command: start bash
CMD ["/bin/bash"]
