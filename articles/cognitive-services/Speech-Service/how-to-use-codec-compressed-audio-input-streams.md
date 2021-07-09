---
title: Speech SDK를 사용하여 코덱 압축 오디오 스트리밍 - Speech Service
titleSuffix: Azure Cognitive Services
description: Speech SDK를 사용하여 압축된 오디오를 Speech Service로 스트리밍하는 방법에 대해 알아봅니다. C++, C# 및 Linux용 Java, Android의 Java 및 iOS의 Objective-C에서 사용할 수 있습니다.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: amishu
ms.custom: devx-track-csharp
zone_pivot_groups: programming-languages-set-twenty-two
ms.openlocfilehash: 183679eb9c4eb2480736dfb4cec8a343c67d0ed7
ms.sourcegitcommit: 67cdbe905eb67e969d7d0e211d87bc174b9b8dc0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111854498"
---
# <a name="use-codec-compressed-audio-input"></a>코덱 압축 오디오 입력 사용

Speech SDK 및 Speech CLI는 GStreamer를 사용하여 압축된 오디오 형식을 수락할 수 있습니다. GStreamer는 원시 PCM으로 유선을 통해 Speech Service에 전송되기 전에 오디오의 압축을 해제합니다.

플랫폼 | 언어 | 지원되는 GStreamer 버전
| :--- | ---: | :---:
Windows(UWP 제외)  | C++, C#, Java, Python | [1.18.3](https://gstreamer.freedesktop.org/data/pkg/windows/1.18.3/)
Linux  | C++, C#, Java, Python | [지원되는 Linux 배포 및 대상 아키텍처](~/articles/cognitive-services/speech-service/speech-sdk.md)
Android  | Java | [1.18.3](https://gstreamer.freedesktop.org/data/pkg/android/1.18.3/)

## <a name="speech-sdk-version-required-for-compressed-audio-input"></a>압축 오디오 입력에 필요한 Speech SDK 버전
* RHEL 8 및 CentOS 8에는 Speech SDK 버전 1.10.0 이상이 필요합니다.
* Windows의 경우 Speech SDK 버전 1.11.0 이상이 필요합니다.
* Windows 및 Android의 최신 GStreamer용 Speech SDK 버전 1.16.0 이상.

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

## <a name="gstreamer-required-to-handle-compressed-audio"></a>압축된 오디오를 처리하는 데 필요한 GStreamer

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/cpp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/java/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/python/prerequisites.md)]
::: zone-end

## <a name="example-code-using-codec-compressed-audio-input"></a>코덱 압축 오디오 입력을 사용하는 예제 코드

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/csharp/examples.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/cpp/examples.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/java/examples.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/python/examples.md)]
::: zone-end

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [음성 인식 방법 알아보기](./get-started-speech-to-text.md)
