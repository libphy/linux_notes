# Recommended useful python packages for Data Scientist    
[ref](https://www.upwork.com/hiring/data/15-python-libraries-data-science/)
So far, I've installed:
- numpy (installed through apt-get when I install tensorflow)  
- scipy
- pandas
- Ipython
- matplotlib
- scikit-learn
- tensorflow (from the source + bazel build)

And realized that there are many locations that python store packages.
1. /usr/lib/python2.7/dist-packages/
  In my case, this contains some pip, easy-install, wheel
2. /usr/local/lib/python2.7/dist-packages/
  This has numpy, sklearn, and tensorflow
3. /usr/local/lib/python2.7/site-packages/
  This folder is empty
4. /home/username/.local/lib/python2.7/site-packages/
  This folder has all kinds of libraries including IPython, jupyter, matplotlib, pandas, and pip.

I was puzzled why there are so many different locations exist for python libraries. Some people online say that it depends on installation route, such as through apt-get or pip install.

# Using Anaconda
Managing environments with conda is useful.
For example, creating an environment with python version 3.5 with name TEST can be done by 
```bash
conda create -n TEST python=3.5 ipython
```
To remove an environment:    
```bash
conda remove --name TEST --all
```
Note that if ipython is not included, ipython in the conda environment will pull system ipython which maybe on a different python version. It seems a bug, and is explained why [here](http://conda.pydata.org/docs/troubleshooting.html#shell-command-location)    
To activate/deactivate:
```bash
$ source activate TEST
(TEST) user@PCname:~$
(TEST) ....:~$ source deactivate TEST
$
```

