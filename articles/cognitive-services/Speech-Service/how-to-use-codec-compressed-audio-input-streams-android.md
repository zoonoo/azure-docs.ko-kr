---
title: Android에서 음성 SDK를 사용 하 여 코덱 압축 오디오 스트리밍
titleSuffix: Azure Cognitive Services
description: Android에서 음성 SDK를 사용 하 여 압축 된 오디오를 음성 서비스로 스트리밍하는 방법에 대해 알아봅니다.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: amishu
ms.openlocfilehash: 01135229167dde3784137ab1b06dfc931766a2e0
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74805844"
---
# <a name="how-to-use-codec-compressed-audio-input-with-the-speech-sdk-on-android"></a>방법: Android에서 음성 SDK를 사용 하 여 코덱 압축 오디오 입력 사용

Speech SDK의 **압축 오디오 입력 스트림** API는 PullStream 또는 pushstream을 사용 하 여 압축 오디오를 음성 서비스로 스트리밍하는 방법을 제공 합니다.

> [!IMPORTANT]
> 스트리밍 압축 입력 오디오는 현재 [ C++Linux의, C#및 Java (ubuntu 16.04, ubuntu 18.04, Debian 9)에서](how-to-use-codec-compressed-audio-input-streams.md)지원 됩니다. Android의 Java 및 [iOS 플랫폼의 목적-C](how-to-use-codec-compressed-audio-input-streams-ios.md) 에서도 지원 됩니다.
> Speech SDK 버전 1.7.0 이상이 필요 합니다.

Wav/PCM에 대해서는 메인 라인 음성 설명서를 참조하십시오. Wav/PCM을 제외하고, 다음과 같은 압축 코덱 입력 형식이 지원됩니다.

- MP3
- OPUS/OGG
- FLAC
- Wav 컨테이너의 형식이 ALAW
- Wav 컨테이너의 MULAW

## <a name="prerequisites-to-using-codec-compressed-audio-input-on-android"></a>Android에서 코덱 압축 오디오 입력을 사용 하기 위한 필수 구성 요소

코덱 압축 오디오는 [GStreamer](https://gstreamer.freedesktop.org)을 사용 하 여 구현 됩니다. 라이선스의 이유로 Gstreamer 이진 파일은 SDK를 사용 하 여 컴파일되지 않습니다. Android 용으로 미리 빌드된 이진 파일을 사용 해야 합니다. 미리 빌드된 라이브러리를 다운로드 하려면 [Android 개발용 설치](https://gstreamer.freedesktop.org/documentation/installing/for-android-development.html?gi-language=c)를 참조 하세요.

`libgstreamer_android.so`는 필수입니다. GStreamer 플러그 인이 `libgstreamer_android.so`에 연결 되어 있는지 확인 합니다.

```make
GSTREAMER_PLUGINS := coreelements app audioconvert mpg123 audioresample audioparsers ogg opusparse opus wavparse alaw mulaw flac
```

아래에 `Android.mk` 및 `Application.mk` 파일 예제가 나와 있습니다. Gstreamer 공유 개체 `libgstreamer_android.so`만들려면 다음 단계를 따르세요.

```make
# Android.mk
LOCAL_PATH := $(call my-dir)

include $(CLEAR_VARS)

LOCAL_MODULE    := dummy
LOCAL_SHARED_LIBRARIES := gstreamer_android
LOCAL_LDLIBS := -llog
include $(BUILD_SHARED_LIBRARY)


ifndef GSTREAMER_ROOT_ANDROID
$(error GSTREAMER_ROOT_ANDROID is not defined!)
endif

ifndef APP_BUILD_SCRIPT
$(error APP_BUILD_SCRIPT is not defined!)
endif

ifndef TARGET_ARCH_ABI
$(error TARGET_ARCH_ABI is not defined!)
endif

ifeq ($(TARGET_ARCH_ABI),armeabi)
GSTREAMER_ROOT        := $(GSTREAMER_ROOT_ANDROID)/arm
else ifeq ($(TARGET_ARCH_ABI),armeabi-v7a)
GSTREAMER_ROOT        := $(GSTREAMER_ROOT_ANDROID)/armv7
else ifeq ($(TARGET_ARCH_ABI),arm64-v8a)
GSTREAMER_ROOT        := $(GSTREAMER_ROOT_ANDROID)/arm64
else ifeq ($(TARGET_ARCH_ABI),x86)
GSTREAMER_ROOT        := $(GSTREAMER_ROOT_ANDROID)/x86
else ifeq ($(TARGET_ARCH_ABI),x86_64)
GSTREAMER_ROOT        := $(GSTREAMER_ROOT_ANDROID)/x86_64
else
$(error Target arch ABI not supported: $(TARGET_ARCH_ABI))
endif

GSTREAMER_NDK_BUILD_PATH  := $(GSTREAMER_ROOT)/share/gst-android/ndk-build/
include $(GSTREAMER_NDK_BUILD_PATH)/plugins.mk
GSTREAMER_PLUGINS         :=  coreelements app audioconvert mpg123 audioresample audioparsers ogg opusparse opus wavparse alaw mulaw flac
GSTREAMER_EXTRA_LIBS      := -liconv
include $(GSTREAMER_NDK_BUILD_PATH)/gstreamer-1.0.mk
```

```make
# Application.mk
APP_STL = c++_shared
APP_PLATFORM = android-21
APP_BUILD_SCRIPT = Android.mk
```

Ubuntu 16.04 또는 18.04에서 다음 명령을 사용 하 여 `libgstreamer_android.so`을 빌드할 수 있습니다. 다음 명령줄은 [ANDROID NDK b16b](https://dl.google.com/android/repository/android-ndk-r16b-linux-x86_64.zip) 의 [Gstreamer android version 1.14.4](https://gstreamer.freedesktop.org/data/pkg/android/1.14.4/gstreamer-1.0-android-universal-1.14.4.tar.bz2) 에 대해서만 테스트 되었습니다.

```sh
# assuming wget and unzip already installed on the system
mkdir buildLibGstreamer
cd buildLibGstreamer
wget https://dl.google.com/android/repository/android-ndk-r16b-linux-x86_64.zip
unzip -q -o android-ndk-r16b-linux-x86_64.zip
export PATH=$PATH:$(pwd)/android-ndk-r16b
export NDK_PROJECT_PATH=$(pwd)/android-ndk-r16b
wget https://gstreamer.freedesktop.org/data/pkg/android/1.14.4/gstreamer-1.0-android-universal-1.14.4.tar.bz2
mkdir gstreamer_android
tar -xjf gstreamer-1.0-android-universal-1.14.4.tar.bz2 -C $(pwd)/gstreamer_android/
export GSTREAMER_ROOT_ANDROID=$(pwd)/gstreamer_android

mkdir gstreamer
# Copy the Application.mk and Android.mk from the documentation above and put it inside $(pwd)/gstreamer

# Enable only one of the following at one time to create the shared object for the targeted ABI
echo "building for armeabi-v7a. libgstreamer_android.so will be placed in $(pwd)/armeabi-v7a"
ndk-build -C $(pwd)/gstreamer "NDK_APPLICATION_MK=Application.mk" APP_ABI=armeabi-v7a NDK_LIBS_OUT=$(pwd)

#echo "building for arm64-v8a. libgstreamer_android.so will be placed in $(pwd)/arm64-v8a"
#ndk-build -C $(pwd)/gstreamer "NDK_APPLICATION_MK=Application.mk" APP_ABI=arm64-v8a NDK_LIBS_OUT=$(pwd)

#echo "building for x86_64. libgstreamer_android.so will be placed in $(pwd)/x86_64"
#ndk-build -C $(pwd)/gstreamer "NDK_APPLICATION_MK=Application.mk" APP_ABI=x86_64 NDK_LIBS_OUT=$(pwd)

#echo "building for x86. libgstreamer_android.so will be placed in $(pwd)/x86"
#ndk-build -C $(pwd)/gstreamer "NDK_APPLICATION_MK=Application.mk" APP_ABI=x86 NDK_LIBS_OUT=$(pwd)
```

공유 개체 (libgstreamer_android)가 빌드된 후에는 응용 프로그램 개발자가 Android 앱에 공유 개체를 두어야 합니다. 그러면 음성 SDK를 통해 해당 개체를 로드할 수 있습니다.

## <a name="example-code-using-codec-compressed-audio-input"></a>코덱 압축 오디오 입력을 사용하는 예제 코드

압축 오디오 형식을 음성 서비스로 스트리밍하려면 `PullAudioInputStream` 또는 `PushAudioInputStream`를 만듭니다. 그런 다음, 스트림 클래스의 인스턴스에서 `AudioConfig`를 생성하여 스트림의 압축 형식을 지정합니다.

`myPullStream`이라고 하는 입력 스트림 클래스가 있고 OPUS/OGG를 사용하고 있다고 가정하겠습니다. 코드는 다음과 비슷할 수 있습니다.

```java
import com.microsoft.cognitiveservices.speech.audio.AudioConfig;
import com.microsoft.cognitiveservices.speech.audio.AudioInputStream;
import com.microsoft.cognitiveservices.speech.audio.AudioStreamFormat;
import com.microsoft.cognitiveservices.speech.audio.PullAudioInputStream;
import com.microsoft.cognitiveservices.speech.internal.AudioStreamContainerFormat;

SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");

// Create an audio config specifying the compressed audio format and the instance of your input stream class.
AudioStreamFormat audioFormat = AudioStreamFormat.getCompressedFormat(AudioStreamContainerFormat.OGG_OPUS);
AudioConfig audioConfig = AudioConfig.fromStreamInput(myPullStream, audioFormat);

SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, audioConfig);

SpeechRecognitionResult result = recognizer.recognizeOnceAsync().get()

String text = result.getText();
```

## <a name="next-steps"></a>다음 단계

- [음성 평가판 구독 가져오기](https://azure.microsoft.com/try/cognitive-services/)
- [Java에서 음성을 인식 하는 방법을 참조 하세요.](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java)
