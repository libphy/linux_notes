## Issue with installing nvidia driver on ubuntu 16.04LTS

- Ubuntu 16.04LTS installed on Asus X99-E WS from a bootable USB. The bootable USB was created from an image file (ubuntu-16.04.1-desktop-amd64.iso) using Windows ImageWriter. Here is some [advices](https://www.pugetsystems.com/labs/hpc/Install-Ubuntu-16-04-or-14-04-and-CUDA-8-and-7-5-for-NVIDIA-Pascal-GPU-825/) to look at, but I didn't follow all the options he took. I upgraded to [GRUB2](https://help.ubuntu.com/community/Grub2/Upgrading) for no reason or for it was recommended by people online (comment out to `#GRUB_HIDDEN_TIMEOUT=0` in `/etc/defalt/grub` to see a GRUB menu on booting). Also installed tasksell, but I did not install any through tasksell yet.
- After the basic ubuntu desktop install, the default graphic card driver from Ubuntu bundle is nouveau. Nouveau did not display properly for my Dell UltraSharp UP2716D monitor (It ran on a low resolution without any option to change). By the way, the motherboard doesn't have a graphic card onboard, so I'm using NVIDIA Titan X GPU for monitor display. To configure display properly, I needed to install NVIDIA driver. I installed NVIVIA latest driver (as of Nov. 2016): NVIDIA-Linux-x86_64-375.20.run    
(cd into the folder where the .run file is)
```bash
$ chmod -x ./XXX.run
$ ./XXX.run
```
Here, chmod -x gives a right to access executable from .run so it can be run. If one does it just `sudo ./XXX.run`, it won't have access to executable, so it won't run.
After the nvidia driver installation, the monitor can display at high resolution, although the highest resolution was shaky- perhaps due to HDMI limit. I was trying to install CUDA library therefore I needed to turn off X by going into tty (ctlr+alt+F1) to stop the lightdm service. However the tty (ctlr+alt+F1-4) did not work after installing nvidia driver. After trying many things people have suggested, the following changes in GRUB made it to work.

```bash
$ sudo nano etc/default/grub
```
Then, change these in grub
`GRUB_CMDLINE_LINUX_DEFAULT="quiet splash"` to `GRUB_CMDLINE_LINUX_DEFAULT="quiet splash vga=770`. Here vga=XXX is a [VESA code](https://en.wikipedia.org/wiki/VESA_BIOS_Extensions#Linux_video_mode_numbers). To find out what options are supported by my monitor, I needed to install hwinfo, and list frame buffers.
```bash
$ sudo apt install hwinfo
$ sudo hwinfo --framebuffer
```
This outputs a list of supported resolution and corresponding colors in bits. I looked at the VESA code table and chose a low resolution one for tty console that matches supported resolution and colors. I chose 800x600 at 16 bit color, which corresponds to `vga=770`. I also needed to uncomment the line `GRUB_TERMINAL=console`, so it can run on console, and finally uncomment and change the resolution setting. `GRUB_GFXMODE=800x600`.

## CUDA installation
According to TensorFlow (as of Nov. 2016), it best works with CUDA 8.0 and CudNN 5.0.
So I downloaded those files:
```
cuda_8.0.44_linux.run
cudnn-8.0-linux-x64-v5.0-ga.tgz
```
Follow instructions on [**Nvidia CUDA installation guide**](http://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html#axzz4RLZI29yS).    
As requirements, I need kernel version > 4.4.0, and gcc > 5.3.1.
To check kernel version, `uname -r`: mine is `4.4.0-47-generic`. To check gcc version, `gcc --version`: mine is `5.4.0`.    
Now, I install CUDA.
(In tty- ctrl+alt+F1)
```
$ sudo service stop lightdm
(cd into the folder where the CUDA file is)
$ sudo sh cuda_8.0.44_linux.run
```
After accepting the term, it asks to install a different version of Nvidia graphics driver (Linux-x86_64 367.48) which seems older than what I have already. In the CUDA installation guide, it seems I don't need to install a new one (375.20) as long as the file types are the same (.run) for both old and new versions.
```
Toolkit location: [default] /usr/localcuda-8.0
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
I could not find a way how to add LD_LIBRARY_PATH correcly- I had many .conf files in /etc/ld.so.conf.d, so I didn't know which one I should be modifying. This [guide to setup environment variables](https://help.ubuntu.com/community/EnvironmentVariables) has good explanations how to setup different types of environment variables. I ignored it for now.

Checked file permission of /dev/nvidia*. I have `/dev/nvidia0` and `/dev/nvidia1`.    
`stat -c "%a %n" nvidia0` shows I have a correct permission (666).
I ignore the drvier wanring for now, and will see if TensorFlow can run. First I check if my two gpus are recognized correctly:
```
$ nvidia-smi
```
Now, I test CUDA sample files by going into the folder where sample files  `~/.../NVIDIA_CUDA-8.0_Samples/` and typing `make`. It takes awhile to build the binary files. Then, a `bin` folder appears. Inside the bin folder and navigate into subfolders, there are samples I can test- e.g. deviceQuery. Run `./deviceQuery` and`./bandwidthTest`. My result came out ok.
I test in a new bash window to see if LD_LIBRARY and PATH environment variables are issue. It seems it still works well.

Other people's experiences:    
[Ubuntu 16.04 + CUDA 8.0 + CudNN v5.0 on Pascal GTX 1080](http://textminingonline.com/dive-into-tensorflow-part-iii-gtx-1080-ubuntu16-04-cuda8-0-cudnn5-0-tensorflow)     
[Ubuntu 16.04 + CURA 8.0 + CudNN v5.1 on older GPUs (970 & 780)](https://alliseesolutions.wordpress.com/2016/09/08/install-gpu-tensorflow-from-sources-w-ubuntu-16-04-and-cuda-8-0-rc/)
