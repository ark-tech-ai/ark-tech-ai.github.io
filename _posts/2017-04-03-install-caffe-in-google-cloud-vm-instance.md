---
layout: post
title: Install caffe in Google Cloud vm instance
date: 2017-04-03
---

### General dependencies
```bash
sudo apt-get install libprotobuf-dev libleveldb-dev libsnappy-dev libopencv-dev libhdf5-serial-dev protobuf-compiler
sudo apt-get install libgflags-dev libgoogle-glog-dev liblmdb-dev
sudo apt-get install --no-install-recommends libboost-all-dev
```
other suggested packages
```bash
sudo apt-get install build-essential
sudo apt-get install vim cmake git
sudo apt-get install libprotobuf-dev libleveldb-dev libsnappy-dev libopencv-dev libboost-all-dev libhdf5-serial-dev
```

glog
```bash
wget https://google-glog.googlecode.com/files/glog-0.3.3.tar.gz
tar zxvf glog-0.3.3.tar.gz
cd glog-0.3.3
./configure
make && make install
```

gflags
```bash
wget https://github.com/schuhschuh/gflags/archive/master.zip
unzip master.zip
cd gflags-master
mkdir build && cd build
export CXXFLAGS="-fPIC" && cmake .. && make VERBOSE=1
make && make install
```

lmdb
```bash
git clone git://gitorious.org/mdb/mdb.git
cd mdb/libraries/liblmdb
make && make install
```

### CUDA: 
Install by apt-get or the NVIDIA .run package. The NVIDIA package tends to follow more recent library and driver versions, but the installation is more manual. If installing from packages, install the library and latest driver separately; the driver bundled with the library is usually out-of-date. This can be skipped for CPU-only installation.

Go to [CUDA download](https://developer.nvidia.com/cuda-downloads), select the right OS, architecture, distribution, version, installer type. Download the installer (2.6 KB).

```bash
sudo dpkg -i cuda-repo-ubuntu1404_8.0.61-1_amd64.deb
sudo apt-get update
sudo apt-get install cuda
sudo reboot
```

### BLAS: 
Install ATLAS by 
```
sudo apt-get install libatlas-base-dev 
```
or install OpenBLAS or MKL for better CPU performance.

### cuDNN
First, download cuDNN
```bash
tar -zxvf cudnn-6.5-linux-x64-v2.tgz
cd cudnn-6.5-linux-x64-v2
sudo cp lib* /usr/local/cuda/lib64/
sudo cp cudnn.h /usr/local/cuda/include/
```
Then update the soft link
```bash
cd /usr/local/cuda/lib64/
sudo rm -rf libcudnn.so libcudnn.so.6.5
sudo ln -s libcudnn.so.6.5.48 libcudnn.so.6.5
sudo ln -s libcudnn.so.6.5 libcudnn.so
```

### setup enviroment parameters
open file /etc/profile and add following at the end of the file
```bash
PATH=/usr/local/cuda/bin:$PATH
export PATH
```
run following command to enable it
```bash
source /etc/profile
```
Meanwhile, add lib path, in folder /etc/ld.so.conf.d/, add new file cuda.conf with content:
```bash
/usr/local/cuda/lib64
```
and run by 
```bash
sudo ldconfig
```

### install Intel MKL or Atlas
```bash
sudo apt-get install libatlas-base-dev
```

### install OpenCV
Recommend to install version 2.4.10. 
```bash
git clone https://github.com/bearpaw/Install-OpenCV.git
cd Install-OpenCV/Ubuntu/2.4
sudo ./opencv2_4_10.sh
```


### Compilation
Caffe can be compiled with either Make or CMake. Make is officially supported while CMake is supported by the community.

#### Compilation with Make
Configure the build by copying and modifying the example Makefile.config for your setup. The defaults should work, but uncomment the relevant lines if using Anaconda Python.
```bash
cp Makefile.config.example Makefile.config
# Adjust Makefile.config (for example, if using Anaconda Python, or if cuDNN is desired)
make all -j 4
make test -j 4
make runtest -j 4
make pycaffe -j 4
```

### References
01 [Install Caffe in Ubuntu 14.04 from Scratch](coldmooon.github.io/2015/08/03/caffe_install/)
