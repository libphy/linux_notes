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
