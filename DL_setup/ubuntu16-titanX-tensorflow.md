# Installing TensorFlow on Ubuntu 16.04 + TitanX Pascal GPUs
## Requirements
According to TensorFlow (as of Nov. 2016), it best works with CUDA 8.0 and cuDNN 5.0.
So I downloaded those files:
```
cuda_8.0.44_linux.run
cudnn-8.0-linux-x64-v5.0-ga.tgz
```

## CUDA installation

Follow instructions on [**Nvidia CUDA installation guide**](http://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html#axzz4RLZI29yS).    
As requirements, I need kernel version > 4.4.0, and gcc > 5.3.1.
To check kernel version, `uname -r`: mine is `4.4.0-47-generic`. To check gcc version, `gcc --version`: mine is `5.4.0`.    
Now, I install CUDA.
(In tty- ctrl+alt+F1)
```
$ sudo service lightdm stop
(cd into the folder where the CUDA file is)
$ sudo sh cuda_8.0.44_linux.run
```
After accepting the term, it asks to install a different version of Nvidia graphics driver (Linux-x86_64 367.48) which seems older than what I have already. In the CUDA installation guide, it seems I don't need to install a new one (375.20) as long as the file types are the same (.run) for both old and new versions. **Note:** *Official TensorFlow installation guide says to use /usr/local/cuda as installation location, but I followed NVIDIA's installation guide to use /usr/local/cuda-8.0 - Later it messes up with cuDNN installation when I followed /usr/local/cuda as cudNN installation location. However I can add cudNN files easily to /cuda-8.0 folder as well.*
```
Toolkit location: [default] /usr/local/cuda-8.0
Install symbolic link: yes
Install samples: yes
CUDA Samples Location: /home/[my_username]/projects/CUDA_samples
```
Installation began, and finished with an warning.
- Incomplete Installation- driver

As suggested, I added these paths:
```bash
$ export PATH=$PATH:/usr/local/cuda-8.0
$ export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/cuda-8.0/lib64
```
This won't persist if bash session is end. Permanent (system-wide) changes in PATH can be made by changing /etc/environment file.
```bash
$ sudo nano /etc/enviroment
```
Add `/usr/local/cuda-8.0/bin:` in front for the PATH value. So it now looks like
```
PATH="/usr/local/cuda-8.0/bin:/usr/local/sbin:/usr/local/bin:...."
```
This [guide to setup environment variables](https://help.ubuntu.com/community/EnvironmentVariables) has good explanations how to setup different types of environment variables. In addition to /etc/environment, I added these in .bashrc
```
export CUDA_HOME=/usr/local/cuda-8.0
export PATH=/usr/local/cuda-8.0/bin${PATH:+:${PATH}}
export LD_LIBRARY_PATH=/usr/local/cuda-8.0/lib64${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}
```

Checked file permission of /dev/nvidia*. I have `/dev/nvidia0` and `/dev/nvidia1`.    
`stat -c "%a %n" nvidia0` shows I have a correct permission (666).
I ignore the drvier wanring for now, and will see if TensorFlow can run. First I check if my two gpus are recognized correctly:
```
$ nvidia-smi
```
Now, I test CUDA sample files by going into the folder where sample files  `~/.../NVIDIA_CUDA-8.0_Samples/` and typing `make`. It takes awhile to build the binary files. Then, a `bin` folder appears. Inside the bin folder and navigate into subfolders, there are samples I can test- e.g. deviceQuery. Run `./deviceQuery` and`./bandwidthTest`. My result came out ok.
I test in a new bash window to see if LD_LIBRARY and PATH environment variables are issue. It seems it still works well.

## cuDNN Installation
cd into the folder where the .tgz file is.
```bash
tar -zxvf cudnn-8.0-linux-x64-v5.0-ga.tgz
sudo cp cuda/include/cudnn.h /usr/local/cuda/include/
sudo cp cuda/lib64/libcudnn* /usr/local/cuda/lib64/
sudo chmod a+r /usr/local/cuda/include/cudnn.h
sudo chmod a+r /usr/local/cuda/lib64/libcudnn*
```
## TensorFlow Installation
Follow [the TensorFlow installation guide](https://www.tensorflow.org/versions/r0.12/get_started/os_setup.html#installing-from-sources)
Install python dependencies.
```bash
sudo apt-get install python-pip
sudo apt-get install python-numpy swig python-dev python-wheel
```
Install the Java JDK in Ubuntu 16.04 by apt-get:
```bash
$ sudo apt-get update
$ sudo apt-get install default-jre
$ sudo apt-get install default-jdk
```
Get the Bazel install script from Bazel github：
``` bash
# cd into Download or desired folder to download
$ wget https://github.com/bazelbuild/bazel/releases/download/0.4.0/bazel-0.4.0-installer-linux-x86_64.sh
```
Then run (make sure to use the correct/ latest version. It gave me an error when I used an old version 0.3.0)：
```bash
$ chmod +x bazel-0.4.0-installer-linux-x86_64.sh
./bazel-0.4.0-installer-linux-x86_64.sh --user
```
Now, Bazel is installed, set the environment variables by adding following code in ~/.bashrc：
```
source /home/[username]/.bazel/bin/bazel-complete.bash
export PATH=$PATH:/home/[username]/.bazel/bin
```
and run:
```bash
$ source ~/.bashrc
```
## Build and install TensorFlow GPU version from the source code
```bash
$ git clone https://github.com/tensorflow/tensorflow
$ cd tensorflow
$ ./configure
```
[WARNING] Not all is in the default folder location! So check the correct location.
To determine location of python:
```bash
$ which python
/usr/bin/python
```
It's the same as default.
```python
import numpy
print numpy.__file__
/usr/lib/python2.7/dist-packages/numpy/__init__.pyc
```
To determine gcc install location and version:
```bash
$ which gcc
/usr/bin/gcc
$ gcc -v
```
Althogh it displays the location as /gcc, use the latest one (`/usr/bin/gcc-5` in my case).

To determine where CUDA toolkits are installed, check PATH and find cuda in the values.
```bash
$ echo $PATH
# OR
$ which nvcc
```
Mine was not in default `/usr/local/cuda` folder, but was in `/usr/loacl/cuda-8.0`.      
To answer which cuDNN to use and the location,
```bash
cat /usr/local/cuda/include/cudnn.h | grep CUDNN_MAJOR -A 2
```
It gives a result: '5'.    
**Warning:** *Be sure to enter correct locations for CUDA and cuDNN. Even both /cuda and /cuda-8.0 folders contained seemingly identical files, switching around the location info makes errors after TensorFlow is installed.*
Compute capability for Titan X is '5.2'.    
There are many other options to choose such as google cloud platform, hadoop, openCL, etc- which may ask a bunch of additional questions on all kinds of dependencies and their locations. To save time, I'll skip those for now, and go for plain configuration  with GPU and CUDA support enabled.
 Now, tensorflow is configured successfully
. To finish installation, need to build a pip package using bazel.
```bash
($ cd tensorflow)
# To build with GPU support:
$ bazel build -c opt --config=cuda //tensorflow/tools/pip_package:build_pip_package
# The build takes long... be patient.

$ bazel-bin/tensorflow/tools/pip_package/build_pip_package /tmp/tensorflow_pkg
```
Then find the correct name of the file after cd into the destination folder above.
```bash
# The name of the .whl file will depend on your platform.
$ sudo pip install /tmp/tensorflow_pkg/tensorflow-0.12.0rc0-py2-none-any.whl
```
## Test TensorFlow
Following will printout whether it uses GPUs.
```python
import tensorflow as tf
sess = tf.Session(config=tf.ConfigProto(log_device_placement=True))
```
My two GPUs are displyed in the log correctly.
## Resources
[The official TensorFlow installation guide](https://www.tensorflow.org/versions/r0.12/get_started/os_setup.html) : This link can be broken when they replace it to a newer version in the future. If link doesn't work, just search for their official website.
Other people's experiences:    
[Ubuntu 16.04 + CUDA 8.0RC + cuDNN 5.1 on GTX1080](https://marcnu.github.io/2016-08-17/Tensorflow-v0.10-installed-from-scratch-Ubuntu-16.04-CUDA8.0RC-cuDNN5.1-1080GTX/)
[Ubuntu 16.04 + CUDA 8.0 +  cuDNN v5.0 on Pascal GTX 1080](http://textminingonline.com/dive-into-tensorflow-part-iii-gtx-1080-ubuntu16-04-cuda8-0-cudnn5-0-tensorflow)     
[Ubuntu 16.04 + CUDA 8.0 + cuDNN v5.1 on older GPUs (970 & 780)](https://alliseesolutions.wordpress.com/2016/09/08/install-gpu-tensorflow-from-sources-w-ubuntu-16-04-and-cuda-8-0-rc/)
