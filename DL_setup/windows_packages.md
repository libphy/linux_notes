# Image processing packages install for Windows
### OpenCV
Open source computer vision library, written in C++. This library has many useful computer vision tools and they are generally faster than other image processing tools.   
Go to http://opencv.org/ to download .exe for Windows
If you want to install through command line, there is an [instruction](https://www.solarianprogrammer.com/2016/09/17/install-opencv-3-with-python-3-on-windows/) through [unofficial wheel files](http://www.lfd.uci.edu/~gohlke/pythonlibs/#opencv). I did not try .exe, but installed opencv_python‑3.2.0‑cp35‑cp35m‑win_amd64.whl and it worked for my system (64 bit Windows + conda environment with python 3.5).
```
(cmd)
$ pip install opencv_python‑3.2.0‑cp35‑cp35m‑win_amd64.whl
```
### scikit-image
Straightforward to install (use `pip install scikit-image` or `conda install scikit-image`). See the [official website](http://scikit-image.org/docs/dev/install.html).

### SimpleITK
SimpleITK is a python package which is a mini version of ITK which is written in C++. It is useful when dealing with medical images in various formats such as .mha, .mhd, .nii, .dcm, etc. Installing is straightforward. `pip install SimpleITK`  
