# Setting up basic python packages for deep learning & data science in Windows    
This page is for beginners using python in Windows. The information is as of March 2017- it can be outdated whenever libraries such as tensorflow update in the future. If that's the case, use these examples with caution- check with the libraries/packages website and change versions appropriately.  
There are other ways to install stuffs, but I'll be using Anaconda mostly, since it's very convenient and works almost the same way regardless the OS.

#### STEP 1. Install Anaconda
- https://www.continuum.io/downloads   
- Choose Windows Python 3.X 64 bit installer (You may choose to use python 2, but I recommend python 3- it makes easier to install tensorflow)
- Download the .exe file and install
- Choose "yes" (default) to add environment variable to PATH

#### STEP 2. Create a conda environment
Conda is a convenient package manager. You will use it for python packages installing mostly, but it also works with non-python stuffs. For example, you can use for installing git (`conda install git`).    

In cmd, type `conda list` to check which packages you have. There are many basic python packages that comes with defalut Anaconda installation.     
Now, we'll create a new environment which is completely separate from the default. The '(cmd)' sign in the box below means I'm typing something in the cmd. The '$' sign is just my notation for the prompt end, and you can ignore it- Do not type $ in the command.
```
(cmd)
$ conda create -n MYENV python=3.5
```
In the example above, I've created an conda environment named 'MYENV' (you can give an arbitrary name, and the letters don't have to be capitals- but make sure to name something easy to remember what it is.), with python 3.5 version. You can set `python=3`, then it will be the latest python 3.X. You can even make a python 2 environment by having `python=2.7` instead. I chose version 3.5 since it works best with installing the current tensorflow version (It will change in the future- so check it out on the tenforflow webpage).    

Now, activate the conda environment. Note that it's `activate` for Windows cmd and `source activate` if it's Mac or linux bash.
```
(cmd)
$ activate MYENV
```
It will change your prompt to
```
(cmd)
(MYENV) $
```
This means you're now using the MYENV environment. Let's see which packages are in the environment by typing `conda list` while in the environment. The newly created environment is pretty empty.    
Let's install some essential python packages.
```
(cmd)
(MYENV) $ conda update conda

....It will update some stuffs....

(MYENV) $ conda install ipython numpy scipy pandas matplotlib scikit-learn jupyter
```
The conda package manager installs bunch of other packages along with those specified packages. You can install other packages (e.g. seaborn) if you want. The `conda install` can install multiple packages in one command line as shown above.     
Now, get into ipython and check if those packages are installed correctly.
```
(cmd)
(MYENV) $ ipython
```
You'll be in the ipython consol
```
(ipython)
Python 3.5.3 |Continuum Analytics, Inc.| (default, Feb 22 2017, 21:28:42) [MSC v.1900 64 bit (AMD64)]
Type "copyright", "credits" or "license" for more information.

IPython 5.3.0 -- An enhanced Interactive Python.
?         -> Introduction and overview of IPython's features.
%quickref -> Quick reference.
help      -> Python's own help system.
object?   -> Details about 'object', use 'object??' for extra details.

[1]
```
