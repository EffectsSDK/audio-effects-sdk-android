# WebRTC integration

For tests you can use any demo webRTC project, like [this](https://github.com/maitrungduc1410/WebRTC-Demo)

## Integration

#### Create external audio processing

```java
    ExternalAudioProcessingFactory audioProcessingFactory = new ExternalAudioProcessingFactory();
```

#### Create AudioProcessing implementation

```java

ExternalAudioProcessingFactory.AudioProcessing audioProcessingInstance = new ExternalAudioProcessingFactory.AudioProcessing() {

    @Override
    public void initialize(final int sampleRate, final int numChannels) {

    }

    @Override
    public void reset(final int newRate) {
    }

    @Override
    public void process(final int numBands, final int numFrames, final ByteBuffer byteBuffer) {
        // process audio here
        
    }
};
```
#### Use array syntax

Process implementation for arrays
```java
    @Override
public void process(final int numBands, final int numFrames, final ByteBuffer byteBuffer) {
    final int size = byteBuffer.remaining();
    //This array can be created once (outside the method)
    final byte[] array = new byte[size];
    byteBuffer.get(array, 0, size);
    final byte[] result = pipeline.process(webRtcFloatToPcm16(array));
    byteBuffer.clear();
    byteBuffer.put(pcm16ToWebRTCFloat(result));
}
```
 Convert webRTC data to SDK pcm16 format, if need
```kotlin
fun pcm16ToWebRTCFloat(data: ByteArray): ByteArray { 
    val byteBufferSrc = ByteBuffer.wrap(data).order(ByteOrder.LITTLE_ENDIAN)
    val byteBufferDst = ByteBuffer.allocate(data.size * 2).order(ByteOrder.LITTLE_ENDIAN)
    for (index: Int in 0 until (data.size / 2)) {
        val value = byteBufferSrc.getShort().toInt().toFloat()
        byteBufferDst.putFloat(value)
    }
    return byteBufferDst.array()
}

fun webRtcFloatToPcm16(data: ByteArray): ByteArray {
    val byteBufferDst = ByteBuffer.allocate(data.size / 2).order(ByteOrder.LITTLE_ENDIAN)
    val byteBufferSrc = ByteBuffer.wrap(data).order(ByteOrder.LITTLE_ENDIAN)
    for (index: Int in 0 until (data.size / 4)) {
        val value = byteBufferSrc.getFloat().toInt().toShort()
        byteBufferDst.putShort(value)
    }
    return byteBufferDst.array()
}
```

#### Use byte buffer directly 

You must guarantee correct byte order for input if you use this syntax.

```java
 @Override
public void process(final int numBands, final int numFrames, final ByteBuffer byteBuffer) {
	//for PCM_FLOAT mono input number of frames = syzeInBytes/4
	pipeline.process(byteBuffer, byteBuffer.capacity() / 4, buf, byteBuffer.capacity() / 4);

}

```

### How to turn off webRTC effects

#### Set MediaConstraints

```kotlin
private fun audioSourceConstraints(): MediaConstraints {
    //bool to string, WEBRTC_AUDIO_PROCESSING_ENABLED must be false
    val enabled = WEBRTC_AUDIO_PROCESSING_ENABLED.toString()
    return MediaConstraints().apply {
        mandatory.add(MediaConstraints.KeyValuePair("googEchoCancellation", enabled))
        mandatory.add(MediaConstraints.KeyValuePair("googAutoGainControl", enabled))
        mandatory.add(MediaConstraints.KeyValuePair("googNoiseSuppression", enabled))
        mandatory.add(MediaConstraints.KeyValuePair("googHighpassFilter", enabled))
        mandatory.add(MediaConstraints.KeyValuePair("googTypingNoiseDetection", enabled))
    }
}
```

#### Create and use AudioSource

```kotlin
audioSource = peerConnectionFactory?.createAudioSource(audioSourceConstraints())
yourMediaStream?.addTrack(peerConnectionFactory?.createAudioTrack("LOCAL_MS_AT", audioSource))
```
