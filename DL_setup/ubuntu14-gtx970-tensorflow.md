# Tensotflow (GPU enabled) installation on Linux Ubuntu

[The official tensorflow installation guide](https://www.tensorflow.org/versions/r0.9/get_started/os_setup.html#pip-installation) sometime has obscure instruction thus I got errors due to setup & version differences. Following is my note on what problems occurred during my installation and how I solved it.

```
My machine specifications:
 - type: Desktop (linux 64)
 - OS: ubuntu 14.04 LTS
 - graphic card: NVIDIA GeForce GTX 970

```
### Installation Overview

Tensorflow instruction page gives a few options to install on linux machines. I used pip install simply because I didn't have anaconda or other stuff installed previously and didn't want to install too many additional stuffs. But, it might be a good idea to use those since they have their own environment so less chance to interact with existing settings of python. The instruction says I need Cuda Installation before Tensorflow installation. (In reality, in my poor execution of those instructions, the order got messed up, but it worked ok generally- if something is not ok, the error message will tell you to go back to certain step.)

### CUDA libraries installation
During the CUDA 7.5 installation on Ubuntu 14. for setting up TensorFlow for GPU (NVIDIA GeForce 970), the error message said installation failed because X server is running.
This appeared to work:
1) Hit `CTRL+ALT+F1` and login using your credentials.
2) kill your current X server session by typing sudo service lightdm stop or sudo stop lightdm
3) I didn't have to do this (Enter runlevel 3 by typing `sudo init 3` and install your ``*.run file``.)

When x server is stopped, all the graphic interface is gone (since x server is managing graphical interface) and only a blank terminal is available. Then I went into a directory where cuda installation execution file was downloaded, then followed their [cuda install instruction](https://developer.nvidia.com/cuda-downloadsRun): `sudo sh cuda_7.5.18_linux.run` to install cuda, and it went successfully.
4) Then I started X server: (You might be required to reboot when the installation finishes. If not, run sudo service lightdm start or sudo start lightdm to start your X server again.)

### Cudnn
When installling cudnn, the directories of files (folder name *cudnn-7.5-linux-x64-v4* below) were slightly different (my folder was called just cuda when I opened tgz file), but it is easy to figure out and change the command accordingly. Also it assumes cuda was installed the default directory `/usr/local/cuda`. If someone had different location for cuda, need to change accordingly (I used all default locations).

```bash
$ tar xvzf cudnn-7.5-linux-x64-v4.tgz
$ sudo cp cudnn-7.5-linux-x64-v4/cudnn.h /usr/local/cuda/include
$ sudo cp cudnn-7.5-linux-x64-v4/libcudnn* /usr/local/cuda/lib64
$ sudo chmod a+r /usr/local/cuda/include/cudnn.h /usr/local/cuda/lib64/libcudnn*
```
Update: Unfortunately, in some case, weight multiplication using gpu didn't work in tensorflow using cudnn-7.5. (Tensorflow would import and run a simple NN example, but did not run for conv2D model training in keras.)
As of July 2016, it seems cudnn-v4 + cuda 7.0/7.5 are supported by tensorflow, and not higher version of cudnn.
I re-installed [cudnn v4](https://developer.nvidia.com/rdp/cudnn-download).
```
geena@ubu:~/Downloads$ tar xvzf cudnn-7.0-linux-x64-v4.0-prod.tgz
geena@ubu:~/Downloads$ cd cuda
geena@ubu:~/Downloads/cuda$ ls -a
.  ..  include  lib64
geena@ubu:~/Downloads/cuda$ sudo cp include/cudnn.h /usr/local/cuda/includegeena@ubu:~/Downloads/cuda$ sudo cp lib64/libcudnn* /usr/local/cuda/lib64
geena@ubu:~/Downloads/cuda$ sudo chmod a+r /usr/local/cuda/include/cudnn.h /usr/local/cuda/lib64/libcudnn*

```
Then ran the configuration and input as below.
Previously I set up installed location as /usr/local/cuda/ (also different versions of library files all together)
If it's different, one needs to change the directory accordingly.
I checked my cudnn library file version was 4.0.7 unlike 4.0.4 in web examples. After running below, things work great.
```
geena@ubu:~/Downloads/tensorflow-master$ ./configure
Please specify the location of python. [Default is /usr/bin/python]:
Do you wish to build TensorFlow with Google Cloud Platform support? [y/N] 
No Google Cloud Platform support will be enabled for TensorFlow
Do you wish to build TensorFlow with GPU support? [y/N] y
GPU support will be enabled for TensorFlow
Please specify which gcc nvcc should use as the host compiler. [Default is /usr/bin/gcc]: /usr/bin/gcc-4.9
Please specify the Cuda SDK version you want to use, e.g. 7.0. [Leave empty to use system default]: 7.5
Please specify the location where CUDA 7.5 toolkit is installed. Refer to README.md for more details. [Default is /usr/local/cuda]:
Please specify the Cudnn version you want to use. [Leave empty to use system default]: 4.0.7
Please specify the location where cuDNN 4.0.7 library is installed. Refer to README.md for more details. [Default is /usr/local/cuda]:
Please specify a list of comma-separated Cuda compute capabilities you want to build with.
You can find the compute capability of your device at: https://developer.nvidia.com/cuda-gpus.
Please note that each additional compute capability significantly increases your build time and binary size.
[Default is: "3.5,5.2"]: 3.5
Setting up Cuda include
Setting up Cuda lib64
Setting up Cuda bin
Setting up Cuda nvvm
Setting up CUPTI include
Setting up CUPTI lib64
Configuration finished
```



### Configuring CUDA
After installing cuda toolkit 7.5 and cudnn 7.0 (just default latest version when I installed), I followed [configure instruction](https://www.tensorflow.org/versions/r0.9/get_started/os_setup.html#optional-install-cuda-gpus-on-linux), where a lot of confusion happens.

First of all, in the paragraph Configure TensorFlow's canonical view of Cuda libraries, I was puzzled I couldn't figure out where the "configure script" was, and what "the root of your source tree" means.
I found the __source__ [github repo](https://github.com/tensorflow/tensorflow) from stackoverflow, but it is also mentioned in the official guide in the section __Installing from sources__. Then I git cloned those to a temporary directory (e.g. download folder) and ran a bash command below (in the downloaded directory).

```bash
$ ./configure
```

Then, it asks bunch of questions. Most can be answered as written in the tensorflow instruction. There were a couple of things I stumbled on:
1. No SWIG_PATH error on the question 'Do you wish to build TensorFlow with GPU support?'
-> It turns out I didn't have swig installed. Installing the latest version from swig.org solved the problem.
2. Mine had old version of gcc (4.8), so I had to install/upgrade it to 4.9. It seems that currently there are newer version available.
3. Cudnn version: Mine is not 4.0.4. I checked version by a bash command "nvcc -V" (do it as root.) and it was 7.0(?), but the configure would not accept it, so I left it as default. Same applies to the location of cudnn library.

### Building target with GPU support

I didn't have bazel, the [tensorflow setup page](https://www.tensorflow.org/versions/r0.9/get_started/os_setup.html) has links to pre-requisite setups before pip installation. Bazel can be found [here](http://www.bazel.io/docs/install.html). Bazel needs JDK 8 or later, and my Ubuntu (14.04) needed a different JDK installation (oracle JDK instead of open JDK) which is well written in the bazel installation page.
Somehow the stuff below didn't work- but bazel ran ok.

```bash
$ chmod +x PATH_TO_INSTALL.SH
$ ./PATH_TO_INSTALL.SH --user
```
### Testing tensorflow
Open python or ipython and run some codes.

```python
import tensorflow as tf
hello = tf.constant('Hello, TensorFlow!')
print(sess.run(hello))
```

When I accidentally ran ipython in the tensorflow source directory, I got error:
__"ImportError: cannot import name pywrap_tensorflow"__, but it turned out that python needs to run outside the source directory, otherwise python will load library from a wrong place in side the source directory.

### Testing if Tensorflow is running on GPU

I followed typing some code [here](https://www.tensorflow.org/versions/r0.9/how_tos/using_gpu/index.html).
And it works beautifully!

## Bonus - Creating a conda environment with GPU Tensorflow
Found [this article](https://devtalk.nvidia.com/default/topic/936429/-solved-tensorflow-with-gpu-in-anaconda-env-ubuntu-16-04-cuda-7-5-cudnn-/) about installing ubuntu 16 + CUDA 7.5 + anaconda tensorflow gpu. It worked on my computer with CUDA 7.5 installed. I haven't tried with other CUDA version.

Create a tensorflow environment using Python 3.5:
$ conda create -n tensorflow python=3.5

Download tensorflow & rename the whl:
$ wget https://storage.googleapis.com/tensorflow/linux/gpu/tensorflow-0.8.0-cp34-cp34m-linux_x86_64.whl
$ mv tensorflow-0.8.0-cp34-cp34m-linux_x86_64.whl tensorflow-0.8.0-cp35-none-linux_x86_64.whl

Start the "tensorflow" env
$ source activate tensorflow
(tensorflow) $ pip install --ignore-installed --upgrade tensorflow-0.8.0-cp35-none-linux_x86_64.whl
To exit the env
(tensorflow) $ source deactivate 

