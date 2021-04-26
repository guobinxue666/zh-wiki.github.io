---
title: MTK Camera 架构介绍
toc: true
date: 2020-00-00 00:00:01
tags: MTK Camera
---

# MTK Camera 简介

------

Android Camera 代码架构大概分为3层。

- Camera Service
- Camera HAL
- Kernel

Camera Service 谷歌已经帮我们做好了，并给我们提供了 **Interface**  供 **Vendor** 进行客制化。用来与 service 进行通信。

Camera Hal 层去实做了这些 Interface。实做部分就是：Camera Provider Hal 和 Camera Device Hal3 。

- ICameraProvider
- ICameraDevice
- ICameraDeviceSession
- ICameraDeviceCallback

## Camera Provider HAL

ICameraProvider 的实做。对应的类名： CameraProviderImpl。

包在 camera device manager 外围，只是一个 adapter, 适配不同版本的 camera device interface。 

Camera Service(指的是camera android层的进程: cameraserver ) 可以通过 ICameraProvider 去拿到 ICameraDevice 。

## Camera Device HAL3

ICameraDevice 和 ICameraDeviceSession 的实做。

对应的类名： CameraDevice3Impl, CameraDevice3SessionImpl 。

用于Camera Service 去操作每一个 camera。 比如: open, close, configureStreams, processCaptureRequest 。

还包含了 IVirtualDevice 的实做。用于 camera device manager 去跟camera device 交互。

# MTK Debug 指南

------

## 如何开启各个模块的Log

开各模块log前，建议先关闭selinux权限，并确定camera logD是已经有打印的，如果没有打印可以用如下命令开启：

```bash
adb shell setenforce 0
adb shell setprop persist.vendor.mtk.camera.log_level 3 
adb shell pkill camera*
```

### MTK Camera2 APP Log

```bash
adb root
adb shell setprop vendor.debug.mtkcam.loglevel 3  
```

### Camera Device Hal3 Log

```bash
# log tag: mtkcam-dev3
adb root
adb shell setprop debug.camera.log.CameraDevice3 2
ð  dump session param in configure stage
ð  log requests from framework
ð  log results from pipeline
```

### AppStreamMgr 的 Log

```bash
# log tag:  mtkcam-AppStreamMgr
adb root
adb shell setprop vendor.debug.camera.log.AppStreamMgr X
ð  X>=1, dump per-frame callback image/meta/shutter/error message
ð  X>=2, dump per-frame control metadata
ð  X>=3, dump per-frame result metadata  
打开后，会有关键 log:   mtkcam-AppStreamMgr: [x-CallbackHandler::performCallback]
```

### Pipeline Log 

```bash
# log tag: mtkcam-PipelineFrameBuilder
adb root
adb shell setprop persist.vendor.debug.camera.log X
adb reboot
ð  X>=2, Log every IPipelineFrame settings
 ð  X>=3, Log every IPipelineFrame settings and its PipelineContext
打开后，会有关键 log:   mtkcam-PipelineFrameBuilder: App image stream buffers=
```




> 参考博客
>
> https://blog.csdn.net/taylorpotter/article/details/105707181
>
> https://www.freesion.com/article/9752721066/
>
> https://blog.csdn.net/coppa/article/details/108067432
>
> https://blog.csdn.net/Coppa/article/details/108553449?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-2.control&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-2.control
>
> 

