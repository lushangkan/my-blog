---
title: v4l2loopback在腾讯会议黑屏的问题
published: "2025-08-19 21:18:39"
draft: false
description: "解决:"
author: 路上看见
tags:
  - v4l2loopback
  - obs
  - 腾讯会议
  - 虚拟摄像头
  - linux
  - 编程
toc: true
---


解决:
使用老版本1.13.1
6.16内核可以使用[https://github.com/ticpu/v4l2loopback-obs][1]

在腾讯会议黑屏，但是使用ffplay等其他软件正常读取
猜测是新版本的v4l2loopback加强了参数校验

以下是错误日志，巨佬可以看看是什么问题:

    [  +0.016545] v4l2-loopback[2338], pid(136620):  open() -> dev@00000000593d5742 with image@000000003fd57957
    [  +0.000018] videodev: v4l2_open: video2: open (0)
    [  +0.000018] video2: VIDIOC_ENUM_FMT: index=0, type=vid-cap, flags=0x0, pixelformat=YUYV little-endian (0x56595559), mbus_code=0x0000, description='YUYV 4:2:2'
    [  +0.000023] video2: VIDIOC_ENUM_FRAMESIZES: index=0, pixelformat=YUYV little-endian (0x56595559), type=1, wxh=1280x720
    [  +0.000583] video2: VIDIOC_ENUM_FRAMESIZES: error -22: index=1, pixelformat=YUYV little-endian (0x56595559), type=0
    [  +0.000022] video2: VIDIOC_ENUM_FMT: error -22: index=1, type=vid-cap, flags=0x0, pixelformat=.... little-endian (0x00000000), mbus_code=0x0000, description=''
    [  +0.000030] v4l2-loopback[1135], pid(136620):  S_FMT[CAPTURE] YUYV:1280x720 size=1843200
    [  +0.000007] video2: VIDIOC_S_FMT: type=vid-cap, width=1280, height=720, pixelformat=YUYV little-endian (0x56595559), field=none, bytesperline=2560, sizeimage=1843200, colorspace=8, flags=0x0, ycbcr_enc=0, quantization=0, xfer_func=0
    [  +0.000027] video2: VIDIOC_G_PARM: type=vid-cap, capability=0x1000, capturemode=0x0, timeperframe=1/30, extendedmode=0, readbuffers=2
    [  +0.000127] v4l2-loopback[2348], pid(136620):  close() -> dev@00000000593d5742 with image@000000003fd57957
    [  +0.000008] v4l2-loopback[1638], pid(136620):  REQBUFS(memory=1, req_count=0) and device-bufs=2/2 [used/max]
    [  +0.000008] videodev: v4l2_release: video2: release
    [  +0.000151] v4l2-loopback[2338], pid(136620):  open() -> dev@00000000593d5742 with image@000000003fd57957
    [  +0.000007] videodev: v4l2_open: video2: open (0)
    [  +0.000011] video2: VIDIOC_QUERYCAP: driver=v4l2 loopback, card=VirtualCam, bus=platform:v4l2loopback-002, version=0x00061001, capabilities=0x85200001, device_caps=0x05200001
    [  +0.001447] video2: VIDIOC_CROPCAP: error -25: type=vid-cap, bounds (0,0)/0x0, defrect (0,0)/0x0, pixelaspect 0/0
    [  +0.000028] v4l2-loopback[1135], pid(136620):  S_FMT[CAPTURE] YUYV:1280x720 size=1843200
    [  +0.000007] video2: VIDIOC_S_FMT: type=vid-cap, width=1280, height=720, pixelformat=YUYV little-endian (0x56595559), field=none, bytesperline=2560, sizeimage=1843200, colorspace=8, flags=0x0, ycbcr_enc=0, quantization=0, xfer_func=0
    [  +0.000026] video2: VIDIOC_G_PARM: type=vid-cap, capability=0x1000, capturemode=0x0, timeperframe=1/30, extendedmode=0, readbuffers=2
    [  +0.000081] video2: VIDIOC_G_FMT: type=vid-cap, width=1280, height=720, pixelformat=YUYV little-endian (0x56595559), field=none, bytesperline=2560, sizeimage=1843200, colorspace=8, flags=0x0, ycbcr_enc=0, quantization=0, xfer_func=0
    [  +0.000024] video2: VIDIOC_G_PARM: type=vid-cap, capability=0x1000, capturemode=0x0, timeperframe=1/30, extendedmode=0, readbuffers=2
    [  +0.000061] v4l2-loopback[1638], pid(136620):  REQBUFS(memory=1, req_count=4) and device-bufs=2/2 [used/max]
    [  +0.000007] video2: VIDIOC_REQBUFS: count=2, type=vid-cap, memory=mmap
    [  +0.000017] video2: VIDIOC_QUERYBUF: 00:00:00.000000 index=0, type=vid-cap, request_fd=0, flags=0x00002000, field=none, sequence=0, memory=mmap, bytesused=1843200, offset/userptr=0x0, length=1843200
    [  +0.000021] timecode=00:00:00 type=0, flags=0x00000000, frames=0, userbits=0x00000000
    [  +0.000605] videodev: v4l2_mmap: video2: mmap (0)
    [  +0.000028] video2: VIDIOC_QUERYBUF: 00:00:00.000000 index=1, type=vid-cap, request_fd=0, flags=0x00002000, field=none, sequence=0, memory=mmap, bytesused=1843200, offset/userptr=0x1c2000, length=1843200
    [  +0.000019] timecode=00:00:00 type=0, flags=0x00000000, frames=0, userbits=0x00000000
    [  +0.000385] videodev: v4l2_mmap: video2: mmap (0)
    [  +0.000012] video2: VIDIOC_QBUF: 00:00:00.000000 index=0, type=vid-cap, request_fd=0, flags=0x00000002, field=any, sequence=0, memory=mmap, bytesused=0, offset/userptr=0x0, length=0
    [  +0.000020] timecode=00:00:00 type=0, flags=0x00000000, frames=0, userbits=0x00000000
    [  +0.000019] video2: VIDIOC_QBUF: 00:00:00.000000 index=1, type=vid-cap, request_fd=0, flags=0x00000002, field=any, sequence=0, memory=mmap, bytesused=0, offset/userptr=0x0, length=0
    [  +0.000019] timecode=00:00:00 type=0, flags=0x00000000, frames=0, userbits=0x00000000
    [  +0.000016] video2: VIDIOC_STREAMON: type=vid-cap
    [  +0.004001] video2: VIDIOC_DQBUF: error -22: 00:00:00.000000 index=0, type=vid-cap, request_fd=0, flags=0x00000000, field=any, sequence=0, memory=(null), bytesused=0, offset/userptr=0x0, length=0
    [  +0.000032] timecode=00:00:00 type=0, flags=0x00000000, frames=0, userbits=0x00000000
    [  +0.000041] video2: VIDIOC_QBUF: error -22: 00:00:00.000000 index=0, type=vid-cap, request_fd=0, flags=0x00000000, field=any, sequence=0, memory=(null), bytesused=0, offset/userptr=0x0, length=0
    [  +0.000024] timecode=00:00:00 type=0, flags=0x00000000, frames=0, userbits=0x00000000
    [  +0.011862] videodev: v4l2_write: video2: write: 1843200 


  [1]: https://github.com/ticpu/v4l2loopback-obs