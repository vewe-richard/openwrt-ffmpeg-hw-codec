# Patch details mentioned in 
https://lwn.net/Articles/964323/


```commandline
$ git clone https://github.com/alien999999999/linux.git
$ cd linux
$ git checkout rpi4-codec/media_stage/v1
$ git diff af6b40a6419db0d49692ebac84efac421942aed8 efc6d748cb59797d07572309b167339cba038c7f --name-status
A       Documentation/userspace-api/media/v4l/pixfmt-nv12-col128.rst                        
M       Documentation/userspace-api/media/v4l/pixfmt-yuv-planar.rst                         
M       Documentation/userspace-api/media/v4l/yuv-formats.rst                               
M       drivers/media/v4l2-core/v4l2-ioctl.c                                                
M       drivers/staging/vc04_services/Kconfig                                               
M       drivers/staging/vc04_services/Makefile                                              
A       drivers/staging/vc04_services/bcm2835-codec/Kconfig                                 
A       drivers/staging/vc04_services/bcm2835-codec/Makefile                                
A       drivers/staging/vc04_services/bcm2835-codec/bcm2835-v4l2-codec.c                    
M       drivers/staging/vc04_services/interface/vchiq_arm/vchiq_arm.c                       
M       drivers/staging/vc04_services/vchiq-mmal/mmal-msg-format.h                          
M       drivers/staging/vc04_services/vchiq-mmal/mmal-msg.h                                 
M       drivers/staging/vc04_services/vchiq-mmal/mmal-parameters.h                          
M       drivers/staging/vc04_services/vchiq-mmal/mmal-vchiq.c                               
M       include/uapi/linux/videodev2.h                                                      

```
You see, the patch added new files
```commandline
A       drivers/staging/vc04_services/bcm2835-codec/Kconfig                                 
A       drivers/staging/vc04_services/bcm2835-codec/Makefile                                
A       drivers/staging/vc04_services/bcm2835-codec/bcm2835-v4l2-codec.c
```

```commandline
git diff af6b40a6419db0d49692ebac84efac421942aed8 efc6d748cb59797d07572309b167339cba038c7f -- drivers/staging/vc04_services/Makefile             
diff --git a/drivers/staging/vc04_services/Makefile b/drivers/staging/vc04_services/Makefile
index 2cebf92525a0..903c949a1ee5 100644                                                     
--- a/drivers/staging/vc04_services/Makefile                                                
+++ b/drivers/staging/vc04_services/Makefile                                                
@@ -14,6 +14,8 @@ endif                                                                     
                                                                                            
 obj-$(CONFIG_SND_BCM2835)              += bcm2835-audio/                                   
 obj-$(CONFIG_VIDEO_BCM2835)            += bcm2835-camera/                                  
+obj-$(CONFIG_VIDEO_CODEC_BCM2835)      += bcm2835-codec/                                   
 obj-$(CONFIG_BCM2835_VCHIQ_MMAL)       += vchiq-mmal/                                      
 obj-$(CONFIG_BCM_VC_SM_CMA)            += vc-sm-cma/                                       
 obj-$(CONFIG_VIDEO_ISP_BCM2835)        += bcm2835-isp/                                     

```

It's clear, the patch added the bcm2835 codec for v4l2 framework.

# Code in openwrt  `24.10.0-rc5`
Kernel source is in  
`/root/openwrt-imagebuilder/openwrt/build_dir/target-aarch64_cortex-a53_musl/linux-bcm27xx_bc
m2710/linux-6.6.69                                                                          
`
You will see codec is in the source tree,
```commandline
root@pve:~/openwrt-imagebuilder/openwrt/build_dir/target-aarch64_cortex-a53_musl/linux-bcm27xx_bcm2710/linux-6.6.69# ls drivers/staging/vc04_services/bcm2835-codec/                    
bcm2835-v4l2-codec.c  Kconfig  Makefile  TODO                                               
```

And same Kconfig
```commandline
root@pve:~/openwrt-imagebuilder/openwrt/build_dir/target-aarch64_cortex-a53_musl/linux-bcm27xx_bcm2710/linux-6.6.69# diff drivers/staging/vc04_services/bcm2835-codec/Kconfig ~/openwrt-
imagebuilder/linux/drivers/staging/vc04_services/bcm2835-codec/Kconfig                      
root@pve:~/openwrt-imagebuilder/openwrt/build_dir/target-aarch64_cortex-a53_musl/linux-bcm27
```

A little difference in Makefile
```commandline
root@pve:~/openwrt-imagebuilder/openwrt/build_dir/target-aarch64_cortex-a53_musl/linux-bcm27
xx_bcm2710/linux-6.6.69# diff drivers/staging/vc04_services/bcm2835-codec/Makefile ~/openwrt
-imagebuilder/linux/drivers/staging/vc04_services/bcm2835-codec/Makefile                    
6,8d5                                                                                       
< ccflags-y += \                                                                            
<       -I$(srctree)/drivers/staging/vc04_services \                                        
<       -D__VCCOREVER__=0x04000000                                                          

```
And it's obvious that openwrt `24.10.0-rc5` kernel `6.6.69` has more updates then the kernel mentioned in the patch.

























