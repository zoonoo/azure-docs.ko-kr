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
ms.openlocfilehash: 1e08203076de2073e39c5b5f5eb40b66c88490d7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103417709"
---
# <a name="use-codec-compressed-audio-input-with-the-speech-sdk"></a>Speech SDK에서 코덱 압축 오디오 입력 사용

Speech Service SDK **압축 오디오 입력 스트림** API는 `PullStream` 또는 `PushStream`을 사용하여 Speech Service로 압축 오디오를 스트리밍하는 방법을 제공합니다.

플랫폼 | 언어 | 지원되는 GStreamer 버전
| :--- | ---: | :---:
Windows(UWP 제외)  | C++, C#, Java, Python | [1.18.3](https://gstreamer.freedesktop.org/data/pkg/windows/1.18.3/)
Linux  | C++, C#, Java, Python | [지원되는 Linux 배포 및 대상 아키텍처](~/articles/cognitive-services/speech-service/speech-sdk.md)
Android  | Java | [1.18.3](https://gstreamer.freedesktop.org/data/pkg/android/1.18.3/)

## <a name="speech-sdk-version-required-for-compressed-audio-input"></a>압축 오디오 입력에 필요한 Speech SDK 버전
* RHEL 8 및 CentOS 8에는 Speech SDK 버전 1.10.0 이상이 필요합니다.
* Windows의 경우 Speech SDK 버전 1.11.0 이상이 필요합니다.
* Windows 및 Android의 최신 gstreamer용 Speech SDK 버전 1.16.0 이상.

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