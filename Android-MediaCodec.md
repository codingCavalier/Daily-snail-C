1、视频时长3069毫秒，使用MediaExtractor读取视频sample数据，sample数据的时间戳通过getSampleTime获取，注意：此时间戳标记的是当前sample数据的起始时间，即：

每两个竖线间表示一个sample数据包，那么拿到最后一个数据包的时间戳是3031，不管最后一个时间戳是多少，一定是小于3069这个值的

|-----|---|------|-----|-------- 省略 -------|------|-----|-------|<br>
0                                                        3031  3069

2、要想 MediaCodec.getOutputImage 或者 MediaCodec.getOutputBuffer 拿到数据，那么不能给 MediaCodec 配置 surface，即：MediaCodec.configure(format, null, null, 0) 第二个参数要传null

3、MediaCodec 解码后保存每一帧图片：

方法1：
```java
Image image = mVideoDecoder.getOutputImage(decoderOutIndex);
if(image != null) {
    Bitmap bitmap = Bitmap.createBitmap(image.getWidth(), image.getHeight(), Bitmap.Config.ARGB_8888);
    YuvToRgbConverter converter = new YuvToRgbConverter(context);
    converter.yuvToRgb(image, bitmap);
    // 保存bitmap
}
```
用到的工具类（可以去网上找）：
![image](https://github.com/codingCavalier/Daily-snail/assets/26496772/064cf460-7e19-4c7b-bda1-5cdb24e8ee0c)

方法2：
```java
ByteBuffer byteBuffer = mVideoDecoder.getOutputBuffer(decoderOutIndex);
if (byteBuffer != null) {
    byteBuffer.position(bufferInfo.offset);
    byteBuffer.limit(bufferInfo.offset + bufferInfo.size);
    byte[] data = new byte[byteBuffer.remaining()];
    byteBuffer.get(data);
    ByteArrayOutputStream out = new ByteArrayOutputStream();
    YuvImage yuv = new YuvImage(data, ImageFormat.NV21, mInputContext.getSourceSize().getWidth(), mInputContext.getSourceSize().getHeight(), null);
    yuv.compressToJpeg(new Rect(0, 0, mInputContext.getSourceSize().getWidth(), mInputContext.getSourceSize().getHeight()), 100, out);
    byte[] bytes = out.toByteArray();
    Bitmap bitmap = BitmapFactory.decodeByteArray(bytes, 0, bytes.length);
    // 保存bitmap
    BitmapExtensionKt.saveToFile(bitmap, CacheUtil.createTemporaryFile("" + bufferInfo.presentationTimeUs * 1000));
}
```
