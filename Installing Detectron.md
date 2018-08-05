## Installing Detectron

Requirements:

- NVIDIA GPU, Linux, Python2
- GPU,CUDA8.0,CUDNN6.0.21

Caffe2:

### Install Dependencies

```
sudo apt-get update
sudo apt-get install -y --no-install-recommends \
      build-essential \
      cmake \
      git \
      libgoogle-glog-dev \
      libgtest-dev \
      libiomp-dev \
      libleveldb-dev \
      liblmdb-dev \
      libopencv-dev \
      libopenmpi-dev \
      libsnappy-dev \
      libprotobuf-dev \
      openmpi-bin \
      openmpi-doc \
      protobuf-compiler \
      python-dev \
      python-pip                          
sudo pip install \
      future \
      numpy \
      protobuf
      
# for Ubuntu 14.04
sudo apt-get install -y --no-install-recommends libgflags2
# for Ubuntu 16.04
sudo apt-get install -y --no-install-recommends libgflags-dev

```





******************************************************************************************************

### Install with GPU Support

**For Ubuntu 14.04**

```
sudo apt-get update && sudo apt-get install wget -y --no-install-recommends
wget "http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1404/x86_64/cuda-repo-ubuntu1404_8.0.61-1_amd64.deb"
sudo dpkg -i cuda-repo-ubuntu1404_8.0.61-1_amd64.deb
sudo apt-get update
sudo apt-get install cuda
```

**For Ubuntu 16.04**

```
sudo apt-get update && sudo apt-get install wget -y --no-install-recommends
wget "http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/cuda-repo-ubuntu1604_8.0.61-1_amd64.deb"
sudo dpkg -i cuda-repo-ubuntu1604_8.0.61-1_amd64.deb
sudo apt-get update
sudo apt-get install cuda
```

#### Install cuDNN (all Ubuntu versions)

**Version 5.1**

```
CUDNN_URL="http://developer.download.nvidia.com/compute/redist/cudnn/v5.1/cudnn-8.0-linux-x64-v5.1.tgz"
wget ${CUDNN_URL}
sudo tar -xzf cudnn-8.0-linux-x64-v5.1.tgz -C /usr/local
rm cudnn-8.0-linux-x64-v5.1.tgz && sudo ldconfig
```

**Version 6.0** Visit [NVIDIA’s cuDNN download](https://developer.nvidia.com/rdp/cudnn-download) to register and download the archive. Follow the same instructions above switching out for the updated library.

Be warned that installing CUDA and CuDNN will increase the size of your build by about 4GB, **so plan to have at least 12GB for your Ubuntu disk size**.

*************************************************************************************************************************************************************************************

### Clone & Build

```
# Clone Caffe2's source code from our Github repository
git clone --recursive https://github.com/caffe2/caffe2.git && cd caffe2
# 下载到了本地，copy到/etc，#/etc/caffe2/.....

git submodule update --init

# Create a directory to put Caffe2's build files in
mkdir build && cd build

# Configure Caffe2's build
# This looks for packages on your machine and figures out which functionality
# to include in the Caffe2 installation. The output of this command is very
# useful in debugging.
cmake ..

# Compile, link, and install Caffe2
sudo make install
```

### Test the Caffe2 Installation

```
cd ~ && python -c 'from caffe2.python import core' 2>/dev/null && echo "Success" || echo "Failure"
```

If this fails, then get a better error message by **running a Python interpreter in the `caffe2/build` directory and then trying `from caffe2.python import core`.**

maybe you will get "no modules named ..."

用户主目录下

```
vi ~/.bashrc

export PYTHONPATH=/etc/caffe2/caffe2/build:$PYTHONPATH

source ~/.bashrc

```

## Other Dependencies

```
pip install numpy>=1.13 pyyaml>=3.12 matplotlib opencv-python>=3.2 setuptools Cython mock scipy
```

```
install cocoapi:

cocoapi拷贝到/root下

cd $cocoapi/PythonAPI         #$cocoapi为cocoapi的路径

make install

```

## Detectron

copy detectron to /root

cd  $detectron/lib && make

测试一下

```
python2 $DETECTRON/tests/test_spatial_narrow_as_op.py
```
