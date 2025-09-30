![Effects SDK logo](assets/Logo.png "a title")

# Android Audio Effects SDK

## Real-time AI-Powered Audio Noise Suppression SDK

Experience flawless audio with our real-time AI-powered noise suppression solution.
Enjoy super easy integration, allowing you to enhance your application’s audio quality
quickly and efficiently.

**Perfect for**:

* Video Conferencing: Ensure crystal-clear communication without background distractions.
* Live Streaming: Deliver professional-grade audio for live broadcasts and streams.
* Recording Applications: Capture high-quality audio by eliminating unwanted noise.

---

## Table of Contents

1. [Features](#features)
1. [Requirements](#requirements)
2. [Setup](#setup)
3. [Usage](#usage)
4. [Documentation](#Documentation)

---

## Features

* Real-time AI-powered noise suppression
* Supports sample rates: 16K, 32K, 44.1K, 48K
* Supports formats: PCM_FLOAT, PCM_16BIT
* Simple and seamless integration

## Trial evaluation

A Customer ID is required for the Effects SDK.
To receive a new trial Customer ID, please fill out the contact form on the [effectssdk.ai](https://effectssdk.ai/cp/registration) website.

## Requirements

1. Android 8.1 (API Level 27)
2. Supported Architectures: armeabi-v7a, arm64-v8a

## Setup

Add this line to your build.gradle file (please check the actual version [here](https://github.com/EffectsSDK/audio-effects-sdk-android/releases))
```kotlin
implementation("ai.effectssdk:audio:+")
```
or include it as local aar if you use some specific version 
(you can find archive with aar and demo app [here](https://github.com/EffectsSDK/audio-effects-sdk-android/releases)).

## Usage

### Add permissions

```xml

<manifest>
	<uses-permission android:name = "android.permission.RECORD_AUDIO" />
	<uses-permission android:name = "android.permission.INTERNET" />

	<android>
		...
	</android>
</manifest>
```

### Create SDK factory instance

```kotlin
val audioSdkFactory = AudioEffectsSDK.getAudioSdkFactory()
```

### Call auth method for sdk object creation access

You can check license status [here](reference/audio-sdk/com.effectssdk.audio/-license-status/index.md)

```kotlin
audioSdkFactory.auth(yourCustomerId) { licenseStatus: LicenseStatus ->
	when (licenseStatus) {
		LicenseStatus.ACTIVE -> {
			//create pipeline instance gere
		}

		LicenseStatus.INACTIVE -> {}
		LicenseStatus.EXPIRED -> {}
		LicenseStatus.ERROR -> {}
	}
}
```

### Create pipeline instance and enable noise suppression

You can check available options [here](reference/audio-sdk/com.effectssdk.audio.pipeline/-pipeline-config/index.md)

```kotlin
val defaultPipelineConfig = PipelineConfig()
val audioPipeline = audioSdkFactory.createAudioPipeline(defaultPipelineConfig)
audioPipeline.enableNoiseSuppression(true)
```

### Call process() method

```kotlin
val result = audioPipeline.process(yourAudioData)
```

### Call release() co free allocated resources

```kotlin
audioPipeline.release()
audioSdkFactory.release()
```

## Documentation

[Code sample](examples/SampleFragment.kt)

[API reference](reference/index.md)

[Technical Details](reference/Technical%20details.md)