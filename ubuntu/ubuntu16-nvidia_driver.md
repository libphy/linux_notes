# Ubuntu and Drivers Installation on a New-build multi-GPU machine
I built a multi-GPU desktop computer on my own. My hardware setup is:

- **Motherboard: ASUS X99-E WS USB3.1**    
   40-lane PCIe, supports RAM up to 128 MB, has 7 double-slot PCIe sockets which are both SLI and CrossFireX compatible, and can support 4 GPUs at x8.
- **CPU: Intel i7-5930K**    
  3.5 GHz, 6-Core
- **RAM: G.SKILL Ripjaws V Series 64GB**    
  (4 x 16GB) 288-Pin DDR4 SDRAM DDR4 3200  
- **GPU: Two NVIDIA Titan X Pascal**  
  3584 CUDA cores @ 1.5 GHz, 12GB GDDR5X memory per GPU (10 Gbps memory speed), Pascal (GP102) architecture, 11 TFLOPs for FP32, 1:128-1:64 FP16, 4:1 native INT8.

## Issue with installing nvidia driver on ubuntu 16.04LTS

- Ubuntu 16.04LTS installed on Asus X99-E WS from a bootable USB. The bootable USB was created from an image file (ubuntu-16.04.1-desktop-amd64.iso) using Windows ImageWriter. Here is some [advices](https://www.pugetsystems.com/labs/hpc/Install-Ubuntu-16-04-or-14-04-and-CUDA-8-and-7-5-for-NVIDIA-Pascal-GPU-825/) to look at, but I didn't follow all the options he took. I upgraded to [GRUB2](https://help.ubuntu.com/community/Grub2/Upgrading) for no reason or for it was recommended by people online (comment out to `#GRUB_HIDDEN_TIMEOUT=0` in `/etc/defalt/grub` to see a GRUB menu on booting). Also installed tasksell, but I did not install any through tasksell yet.
- After the basic ubuntu desktop install, the default graphic card driver from Ubuntu bundle is nouveau. Nouveau did not display properly for my Dell UltraSharp UP2716D monitor (It ran on a low resolution without any option to change). By the way, the motherboard doesn't have a graphic card onboard, so I'm using NVIDIA Titan X GPU for monitor display. To configure display properly, I needed to install NVIDIA driver. I installed NVIDIA latest driver (as of Nov. 2016): NVIDIA-Linux-x86_64-375.20.run    
(cd into the folder where the .run file is)
```bash
$ chmod +x ./XXX.run
$ ./XXX.run
```
Here, chmod +x gives a right to access executable from .run so it can be run. If one does it just `sudo ./XXX.run`, it won't have access to executable, so it won't run.
After the nvidia driver installation, the monitor can display at high resolution, although the highest resolution was shaky- perhaps due to HDMI limit. I was trying to install CUDA library therefore I needed to turn off X by going into tty (ctlr+alt+F1) to stop the lightdm service. However the tty (ctlr+alt+F1-4) did not work after installing nvidia driver. After trying many things people have suggested, the following changes in GRUB made it to work.

```bash
$ sudo nano etc/default/grub
```
Then, change these in grub
`GRUB_CMDLINE_LINUX_DEFAULT="quiet splash"` to `GRUB_CMDLINE_LINUX_DEFAULT="quiet splash vga=788`. Here vga=XXX is a [VESA code](https://en.wikipedia.org/wiki/VESA_BIOS_Extensions#Linux_video_mode_numbers). To find out what options are supported by my monitor, I needed to install hwinfo, and list frame buffers.
```bash
$ sudo apt install hwinfo
$ sudo hwinfo --framebuffer
```
This outputs a list of supported resolution and corresponding colors in bits. I looked at the VESA code table and chose a low resolution one for tty console that matches supported resolution and colors. I chose 800x600 at 16 bit color, which corresponds to `vga=788`. I also needed to uncomment the line `GRUB_TERMINAL=console`, so it can run on console, and finally uncomment and change the resolution setting. `GRUB_GFXMODE=800x600`. Then it works well.

[update] when I buit a second machine with different specs, the `hwinfo --framebuffer` did not work initially. Booting in the recovery mode and updating stuffs fixed this issue. Once I got the correct list of supported resolution settings, I was able to choose VESA code correctly. 
