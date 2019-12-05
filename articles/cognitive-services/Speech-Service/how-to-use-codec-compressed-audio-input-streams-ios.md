---
title: IOS에서 음성 SDK를 사용 하 여 코덱 압축 오디오 스트리밍
titleSuffix: Azure Cognitive Services
description: IOS의 음성 SDK를 사용 하 여 압축 된 오디오를 음성 서비스로 스트리밍하는 방법에 대해 알아봅니다.
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: chlandsi
ms.openlocfilehash: 2089f4191ddd57fa8dc19862bd195756c166f2d4
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74805861"
---
# <a name="how-to-use-codec-compressed-audio-input-with-the-speech-sdk-on-ios"></a>방법: iOS에서 음성 SDK를 사용 하 여 코덱 압축 오디오 입력 사용

Speech SDK의 **압축 오디오 입력 스트림** API는 끌어오기 또는 푸시 스트림을 사용 하 여 압축 오디오를 음성 서비스로 스트리밍하는 방법을 제공 합니다.

> [!IMPORTANT]
> IOS에서 압축 오디오를 스트리밍하는 경우 Speech SDK 버전 1.7.0 이상이 필요 합니다. [Android의](how-to-use-codec-compressed-audio-input-streams-android.md) [ C++Linux (ubuntu 16.04 C#, ubuntu 18.04, Debian 9) 및 java에서, 및 java](how-to-use-codec-compressed-audio-input-streams.md) 에 대해서도 지원 됩니다.

Wav/PCM에 대해서는 메인 라인 음성 설명서를 참조하십시오. Wav/PCM을 제외하고, 다음과 같은 압축 코덱 입력 형식이 지원됩니다.

- MP3
- OPUS/OGG
- FLAC
- Wav 컨테이너의 형식이 ALAW
- Wav 컨테이너의 MULAW

## <a name="prerequisites"></a>전제 조건

압축 된 오디오 처리는 [GStreamer](https://gstreamer.freedesktop.org)을 사용 하 여 구현 됩니다. 라이선스 상의 이유로 이러한 함수는 SDK와 함께 제공 될 수 없지만 이러한 함수를 포함 하는 래퍼 라이브러리는 응용 프로그램 개발자가 작성 하 고 SDK를 사용 하 여 앱과 함께 제공 해야 합니다.

이 래퍼 라이브러리를 빌드하려면 먼저 [GSTREAMER SDK](https://gstreamer.freedesktop.org/data/pkg/ios/1.16.0/gstreamer-1.0-devel-1.16.0-ios-universal.pkg)를 다운로드 하 여 설치 합니다. 그런 다음 [래퍼 라이브러리](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/objective-c/ios/compressed-streams/GStreamerWrapper)의 Xcode 프로젝트를 다운로드 합니다.

Xcode에서 프로젝트를 열고 **일반 IOS 장치** 대상에 대해 빌드합니다. 특정 대상에 대해 빌드하는 것은 작동 하지 않습니다.

빌드 단계에서는 `GStreamerWrapper.framework`이름이 인 모든 필수 아키텍처에 대해 동적 라이브러리를 사용 하 여 동적 프레임 워크 번들을 생성 합니다.

이 프레임 워크는 음성 서비스 SDK로 압축 된 오디오 스트림을 사용 하는 모든 앱에 포함 되어야 합니다.

Xcode 프로젝트에서 다음 설정을 적용 하 여이 작업을 수행 합니다.

1. 방금 빌드한 `GStreamerWrapper.framework`와 [여기](https://aka.ms/csspeech/iosbinary)에서 다운로드할 수 있는 COGNITIVE SERVICES Speech SDK의 프레임 워크를 모두 샘플 프로젝트가 포함 된 디렉터리로 복사 합니다.
1. _프로젝트 설정_의 프레임 워크에 대 한 경로를 조정 합니다.
   1. **포함 된 이진 파일** 헤더의 **일반** 탭에서 SDK 라이브러리를 프레임 워크로 추가 합니다. 추가 ...를 추가 > **다른** **파일** 추가 ... > 선택한 디렉터리로 이동 하 고 두 프레임 워크를 모두 선택 합니다.
   1. **빌드 설정** 탭으로 이동하고 **모든** 설정을 활성화합니다.
1. `$(SRCROOT)/..` 디렉터리를 **검색 경로** 제목 아래의 _프레임워크 검색 경로_에 추가합니다.

## <a name="example-code-using-codec-compressed-audio-input"></a>코덱 압축 오디오 입력을 사용하는 예제 코드

압축 오디오 형식을 음성 서비스로 스트리밍하려면 `SPXPullAudioInputStream` 또는 `SPXPushAudioInputStream`를 만듭니다.

다음 코드 조각은 스트림의 압축 형식으로 mp3를 지정 하 여 `SPXPushAudioInputStream`인스턴스에서 `SPXAudioConfiguration`을 만드는 방법을 보여 줍니다.

[!code-objectivec[Set up the input stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=66-77&highlight=2-11)]

다음 코드 조각에서는 압축 된 오디오 데이터를 파일에서 읽고 `SPXPushAudioInputStream`펌프 하는 방법을 보여 줍니다.

[!code-objectivec[Push compressed audio data into the stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=105-151&highlight=19-44)]

## <a name="next-steps"></a>다음 단계

- [음성 평가판 구독 가져오기](https://azure.microsoft.com/try/cognitive-services/)
- [Java에서 음성을 인식 하는 방법을 참조 하세요.](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java)
