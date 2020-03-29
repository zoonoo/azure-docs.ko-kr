---
title: 음성 SDK - 음성 서비스로 코드화 압축 오디오 스트리밍
titleSuffix: Azure Cognitive Services
description: '음성 SDK를 사용하여 압축된 오디오를 음성 서비스로 스트리밍하는 방법을 알아봅니다. C ++, C #, 리눅스에 대 한 자바에 사용할 수, 안 드 로이드와 iOS에서 목표-C에서 자바.'
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/09/2020
ms.author: amishu
zone_pivot_groups: programming-languages-set-twelve
ms.openlocfilehash: 3fab02d3dc567a2c54edad5bfb05abe7d99f7b7c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78943722"
---
# <a name="use-codec-compressed-audio-input-with-the-speech-sdk"></a>음성 SDK와 코덱 압축 오디오 입력 사용

음성 서비스 SDK **압축 오디오 입력 스트림** API는 `PullStream` 또는 을 `PushStream`사용하여 압축된 오디오를 음성 서비스로 스트리밍하는 방법을 제공합니다.

> [!IMPORTANT]
> 스트리밍 압축 입력 오디오는 현재 C #, C ++, 리눅스에 자바 (우분투 16.04, 우분투 18.04, 데비안 9, RHEL 8, CentOS 8)에 대한 지원됩니다. 또한 안드로이드에서 자바와 iOS 플랫폼에서 목표-C에 대한 지원됩니다.
> 음성 SDK 버전 1.7.0 이상이 필요합니다(RHEL 8, CentOS 8의 경우 버전 1.10.0 이상).

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

## <a name="prerequisites"></a>사전 요구 사항

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-tos/compressed-audio-input/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [prerequisites](includes/how-tos/compressed-audio-input/cpp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-tos/compressed-audio-input/java/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-objectivec"
[!INCLUDE [prerequisites](includes/how-tos/compressed-audio-input/objectivec/prerequisites.md)]
::: zone-end

## <a name="example-code-using-codec-compressed-audio-input"></a>코덱 압축 오디오 입력을 사용한 예제 코드

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-tos/compressed-audio-input/csharp/examples.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [prerequisites](includes/how-tos/compressed-audio-input/cpp/examples.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-tos/compressed-audio-input/java/examples.md)]
::: zone-end

::: zone pivot="programming-language-objectivec"
[!INCLUDE [prerequisites](includes/how-tos/compressed-audio-input/objectivec/examples.md)]
::: zone-end

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [음성 인식 방법 알아보기](quickstarts/speech-to-text-from-microphone.md)
