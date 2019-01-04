---
title: Speech to Text 사용 | Microsoft Docs
description: Speech Service에서 Speech to Text를 사용하는 방법 알아보기
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/16/2018
ms.author: v-jerkin
ms.openlocfilehash: 26cecedfc3ad2d472b9686e25054fe08253cee77
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/17/2018
ms.locfileid: "39068525"
---
# <a name="use-speech-to-text-in-the-speech-service"></a>Speech Service에서 “Speech to Text” 사용

애플리케이션에서 두 가지 방법으로 **Speech to Text**를 사용할 수 있습니다.

| 방법 | 설명 |
|-|-|
| [SDK](speech-sdk.md) | C/C++, C# 및 Java 개발자를 위한 가장 간단한 방법 |
| [REST](rest-apis.md) | HTTP POST 요청을 사용하여 짧은 발화 인식 | 

## <a name="using-the-sdk"></a>SDK 사용

[Speech SDK](speech-sdk.md)는 전체 기능과 함께 응용 프로그램에서 **Speech to Text**를 사용하는 가장 간단한 방법을 제공합니다.

1. 음성 팩터리를 만들어 Speech Service 구독 키와 [지역](regions.md) 또는 권한 부여 토큰을 제공합니다. 이때 인식 언어 또는 고유한 음성 인식 모델의 사용자 지정 끝점과 같은 옵션을 구성할 수도 있습니다.

2. 팩터리에서 인식기를 가져옵니다. 세 가지 유형의 인식기를 사용할 수 있습니다. 각 인식기 유형은 디바이스의 기본 마이크, 오디오 스트림 또는 파일의 오디오를 사용할 수 있습니다.

    인식기 | 함수
    -|-
    음성 인식기|음성의 텍스트 전사 제공
    의도 인식기|인식 후에 [LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/)를 통해 화자 의도 유추\*
    번역 인식기|전사된 텍스트를 다른 언어로 번역([Speech Translation](how-to-translate-speech.md) 참조)

    \* ‘의도 인식을 위해 의도 인식기에 대한 음성 팩터리를 만들 때 별도의 LUIS 구독 키를 사용해야 합니다.’
    
4. 필요한 경우, 비동기 작업에 대한 이벤트를 연결합니다. 중간 및 최종 결과가 있으면 인식기가 이벤트 처리기를 호출합니다. 결과가 없으면 애플리케이션이 최종 전사 결과를 받습니다.

5. 인식을 시작합니다.
   명령 또는 쿼리 인식과 같은 1단계 인식의 경우 인식되는 첫 번째 발언을 반환하는 `RecognizeAsync()`를 사용합니다.
   전사와 같은 장기 실행 인식의 경우 `StartContinuousRecognitionAsync()`를 사용하고 비동기 인식 결과 대한 이벤트를 연결합니다.

### <a name="sdk-samples"></a>SDK 샘플

샘플의 최신 집합은 [Cognitive Services 음성 SDK 샘플 GitHub 리포지토리](https://aka.ms/csspeech/samples)를 참조하세요.

## <a name="using-the-rest-api"></a>REST API 사용

REST API는 SDK에서 지원하는 언어를 사용하지 않을 경우, 음성을 인식하는 가장 간단한 방법입니다. 서비스 끝점에 대한 HTTP POST 요청을 만들어 요청 본문으로 전체 발화를 전달합니다. 인식된 텍스트가 포함된 응답을 받습니다.

> [!NOTE]
> 발화는 REST API를 사용할 경우, 15초 이하로 제한됩니다.

**Speech to Text** REST API에 대한 자세한 내용은 [REST API](rest-apis.md#speech-to-text)를 참조하세요. 이 기능이 작동하는지 확인하려면 GitHub에서 [REST API 샘플](https://github.com/Azure-Samples/SpeechToText-REST)을 다운로드합니다.

## <a name="next-steps"></a>다음 단계

- [음성 평가판 구독 가져오기](https://azure.microsoft.com/try/cognitive-services/)
- [C++에서 음성을 인식하는 방법](quickstart-cpp-windows.md)
- [C#에서 음성을 인식하는 방법](quickstart-csharp-dotnet-windows.md)
- [Java에서 음성을 인식하는 방법](quickstart-java-android.md)
