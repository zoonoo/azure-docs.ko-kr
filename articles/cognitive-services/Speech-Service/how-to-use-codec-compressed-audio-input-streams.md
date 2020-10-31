---
title: 음성 SDK-음성 서비스를 사용 하 여 코덱 압축 오디오 스트리밍
titleSuffix: Azure Cognitive Services
description: '음성 SDK를 사용 하 여 음성 서비스에 압축 된 오디오를 스트리밍하는 방법에 대해 알아봅니다. C + +, c # 및 Linux 용 Java, Android의 Java 및 iOS의 목적-C에 사용할 수 있습니다.'
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
ms.openlocfilehash: 7d6a0388507d836050550a3a98255deabb826cde
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93128340"
---
# <a name="use-codec-compressed-audio-input-with-the-speech-sdk"></a>음성 SDK를 사용 하 여 코덱 압축 오디오 입력 사용

음성 서비스 SDK **압축 오디오 입력 스트림** API는 또는을 사용 하 여 음성 서비스에 압축 오디오를 스트리밍하는 방법을 제공 합니다 `PullStream` `PushStream` .

플랫폼 | 언어 | 지원 되는 GStreamer 버전
| :--- | ---: | :---:
Windows (UWP 제외)  | C + +, c #, Java, Python | [1.15.1](https://gstreamer.freedesktop.org/data/pkg/windows/1.15.1/)
Linux  | C + +, c #, Java, Python | [지원 되는 Linux 배포판 및 대상 아키텍처](~/articles/cognitive-services/speech-service/speech-sdk.md)
Android  | Java | [1.14.4](https://gstreamer.freedesktop.org/data/pkg/android/1.14.4/)

## <a name="speech-sdk-version-required-for-compressed-audio-input"></a>압축 오디오 입력에 필요한 Speech SDK 버전
* RHEL 8 및 CentOS 8에는 Speech SDK 버전 1.10.0 이상이 필요 합니다.
* Windows의 경우 Speech SDK 버전 1.11.0 이상이 필요 합니다.

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

## <a name="prerequisites"></a>필수 구성 요소

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

## <a name="example-code-using-codec-compressed-audio-input"></a>코덱 압축 오디오 입력을 사용 하는 예제 코드

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
> [음성 인식 방법 알아보기](quickstarts/speech-to-text-from-microphone.md)
