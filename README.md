Phone Screen Recorder
=====
这是个DEMO APP 主要是实现了屏幕录制功能（可同时录制来自麦克风的声音）。

<img alt="screenshot" src="screenshot.png" width="50%" />



说明：使用了 [MediaProjectionManager][1], [VirtualDisplay][2], [AudioRecord][3], [MediaCodec][4] 以及 [MediaMuxer][5] 等API，故而这个项目最低支持Android 5.0。





录屏原理
=====


- `Display` 可以“投影”到一个 `VirtualDisplay`
- 通过 `MediaProjectionManager` 取得的 `MediaProjection`创建`VirtualDisplay` 
- `VirtualDisplay` 会将图像渲染到 `Surface`中，而这个`Surface`是由`MediaCodec`所创建的

```
mEncoder = MediaCodec.createEncoderByType(MIME_TYPE);
...
mSurface = mEncoder.createInputSurface();
...
mVirtualDisplay = mMediaProjection.createVirtualDisplay(name, mWidth, mHeight, mDpi, DisplayManager.VIRTUAL_DISPLAY_FLAG_PUBLIC, mSurface, null, null);
```

- `MediaMuxer` 将从 `MediaCodec` 得到的图像元数据封装并输出到MP4文件中

```
int index = mEncoder.dequeueOutputBuffer(mBufferInfo, TIMEOUT_US);
...
ByteBuffer encodedData = mEncoder.getOutputBuffer(index);
...
mMuxer.writeSampleData(mVideoTrackIndex, encodedData, mBufferInfo);
```
所以其实在**Android 4.4**上可以通过`DisplayManager`来创建`VirtualDisplay`也是可以实现录屏，但因为权限限制需要**ROOT**。 (see [DisplayManager.createVirtualDisplay()][6])

[1]: https://developer.android.com/reference/android/media/projection/MediaProjectionManager.html
[2]: https://developer.android.com/reference/android/hardware/display/VirtualDisplay.html
[3]: https://developer.android.com/reference/android/media/AudioRecord.html
[4]: https://developer.android.com/reference/android/media/MediaCodec.html
[5]: https://developer.android.com/reference/android/media/MediaMuxer.html
[6]: https://developer.android.com/reference/android/hardware/display/DisplayManager.html
[7]: https://github.com/yrom/ScreenRecorder/releases/latest
[8]: https://play.google.com/store/apps/details?id=net.yrom.screenrecorder.demo
