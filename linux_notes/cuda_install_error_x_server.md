###During the CUDA 7.5 installation on Ubuntu 14. for setting up TensorFlow for GPU (NVIDIA GeForce 970), the error message said installation failed because X server is running.
``` This appeared to work
```1) Hit CTRL+ALT+F1 and login using your credentials.
2) kill your current X server session by typing sudo service lightdm stop or sudo stop lightdm
3) I didn't have to do this (Enter runlevel 3 by typing sudo init 3 and install your *.run file.)

When x server is stopped, all the graphic interface is gone (since x server is managing graphical interface) and only a blank terminal is available. Then I went into a directory where cuda installation execution file is, then followed their instruction (https://developer.nvidia.com/cuda-downloadsRun : `sudo sh cuda_7.5.18_linux.run`) to install cuda, and it went successfully.
4) Then I started X server: (You might be required to reboot when the installation finishes. If not, run sudo service lightdm start or sudo start lightdm to start your X server again.)
```
