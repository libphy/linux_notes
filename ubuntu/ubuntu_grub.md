# Boot
### Messed up grub
Symptom: Boot did not work and had a dark screen with flashing texts (something to do with 'sane', but even when I fix sane issue, it persisted).
Reinstall using bootable usb wasn't an option- (different from what people say that I can preserve data) it did not offer reinstall or repair without wiping out data.
Using advanced booting option (manually choosing Grub kernels) I identified which kernels (in my case, there were many booting image versions and each is called 'kernel') works and which doesn't.
To get rid of a messed-up kernel, use terminal.
```bash
dpkg -l linux-image-\* | grep ^ii
```
Then, the result looks like this.  
```
...
ii  linux-image-3.13.0-53-generic                         3.13.0-53.89                                        amd64        Linux kernel image for version 3.13.0 on 64 bit x86 SMP
ii  linux-image-3.13.0-57-generic                         3.13.0-57.95                                        amd64        Linux kernel image for version 3.13.0 on 64 bit x86 SMP
ii  linux-image-3.13.0-61-generic                         3.13.0-61.100                                       amd64        Linux kernel image for version 3.13.0 on 64 bit x86 SMP
ii  linux-image-3.13.0-62-generic                         3.13.0-62.102                                       amd64        Linux kernel image for version 3.13.0 on 64 bit x86 SMP
ii  linux-image-3.13.0-63-generic                         3.13.0-63.103                                       amd64        Linux kernel image for version 3.13.0 on 64 bit x86 SMP
ii  linux-image-3.13.0-65-generic                         3.13.0-65.106                                       amd64        Linux kernel image for version 3.13.0 on 64 bit x86 SMP
ii  linux-image-3.13.0-68-generic                         3.13.0-68.111                                       amd64        Linux kernel image for version 3.13.0 on 64 bit x86 SMP
ii  linux-image-3.13.0-74-generic                         3.13.0-74.118                                       amd64        Linux kernel image for version 3.13.0 on 64 bit x86 SMP
ii  linux-image-3.13.0-92-generic                         3.13.0-92.139                                       amd64        Linux kernel image for version 3.13.0 on 64 bit x86 SMP
ii  linux-image-3.13.0-93-generic                         3.13.0-93.140                                       amd64        Linux kernel image for version 3.13.0 on 64 bit x86 SMP
...
```
I got rid of an image (the latest one that something got messed up during update).
```bash
sudo apt-get purge linux-image-3.13.0-101-generic
```
It erased the problematic kernel (with 101 tag) and its derivatives.
