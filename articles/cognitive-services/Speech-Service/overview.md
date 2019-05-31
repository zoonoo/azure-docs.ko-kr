---
title: Azure Speech Services란?
titleSuffix: Azure Cognitive Services
description: Azure Speech Services는 음성-텍스트 변환, 텍스트-음성 변환 및 음성 번역을 단일 Azure 구독에 통합한 것입니다. Speech SDK, Speech Devices SDK 또는 REST API를 사용하여 애플리케이션, 도구 및 디바이스에 음성을 쉽게 추가할 수 있습니다. 기존 채팅 봇에 음성 기능을 추가하거나, 번역 애플리케이션에서 텍스트-음성 변환을 수행하거나, 대량의 콜 센터 데이터를 기록할 수 있습니다.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: overview
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: dc0871c0b16246fa178072e9d9f266d4c02ba1c4
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/22/2019
ms.locfileid: "66002998"
---
# <a name="what-are-the-speech-services"></a>Speech Services란?

Azure Speech Services는 음성-텍스트 변환, 텍스트-음성 변환 및 음성 번역을 단일 Azure 구독에 통합한 것입니다. [Speech SDK](speech-sdk-reference.md), [Speech Devices SDK](https://aka.ms/sdsdk-quickstart) 또는 [REST API](rest-apis.md)를 사용하여 애플리케이션, 도구 및 디바이스에 음성을 쉽게 추가할 수 있습니다.

> [!IMPORTANT]
> Speech Services는 Bing Speech API, Translator Speech 및 Custom Speech 대신 사용되었습니다. 마이그레이션 지침에 대해서는 *방법 가이드 > 마이그레이션*을 참조하세요.

다음과 같은 기능이 Azure Speech Services를 구성합니다. 이 표의 링크를 사용하여 각 기능의 일반적인 사용 사례를 알아보거나 API 참조를 검색할 수 있습니다.

| 서비스 | 기능 | 설명 | SDK) | REST (영문) |
|---------|---------|-------------|-----|------|
| [음성 텍스트 변환](speech-to-text.md) | 음성 텍스트 변환 | 음성 텍스트 변환은 오디오 스트림을 애플리케이션, 도구 또는 디바이스가 사용하거나 표시할 수 있는 텍스트로 실시간으로 기록합니다. [LUIS(Language Understanding)](https://docs.microsoft.com/azure/cognitive-services/luis/)에서 음성 텍스트 변환을 사용하여 기록된 음성에서 사용자 의도를 파생시키고 음성 명령 작업에 따라 행동합니다. | [예](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [예](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [일괄 처리 기록](batch-transcription.md) | 일괄 처리 기록을 사용하면 대량의 비동기 음성 텍스트 변환 기록을 수행할 수 있습니다. 이 서비스는 사용자 지정 및 모델 관리와 같은 엔드포인트를 사용하는 REST 기반 서비스입니다. | 아니요 | [예](https://westus.cris.ai/swagger/ui/index) |
| | [대화 전사](conversation-transcription-service.md) | 실시간 음성 인식, 화자 식별 및 다이어리화를 가능하게 해줍니다. 화자를 구별하는 기능을 사용하여 직접 만남을 전사하는 데 이상적입니다. | 예 | 아니요 |
| | [Custom Speech 모델 만들기](#customize-your-speech-experience) | 고유한 환경에서 인식 및 기록을 위해 음성 텍스트 변환을 사용하는 경우 사용자 지정 음향, 언어 및 발음 모델을 만들고 학습하여 주변 소음 또는 산업용 어휘를 처리할 수 있습니다. | 아니요 | [예](https://westus.cris.ai/swagger/ui/index) |
| [텍스트 음성 변환](text-to-speech.md) | 텍스트 음성 변환 | 텍스트 음성 변환은 입력 텍스트를 인간과 유사한 합성 음성으로 변환합니다. 표준 음성 및 인공신경망 음성 중에서 선택합니다([언어 지원](language-support.md) 참조). | [예](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [예](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [사용자 지정 음성 만들기](#customize-your-speech-experience) | 브랜드 또는 제품에 고유한 사용자 지정 음성 글꼴을 만듭니다. | 아니요 | [예](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Speech Translation](speech-translation.md) | 음성 번역 | 음성 번역을 사용하면 음성에 대한 실시간 종단 간 다중 언어 번역을 애플리케이션, 도구 및 장치에 추가할 수 있습니다. 이 서비스는 음성을 음성으로 변환 및 음성을 텍스트로 변환을 위해 사용합니다. | [예](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | 아니요 |
| [음성 우선 가상 도우미](voice-first-virtual-assistants.md) | 음성 우선 가상 도우미 | Azure Speech Services를 사용하는 사용자 지정 가상 도우미는 개발자가 자신의 애플리케이션과 환경을 위해 자연스럽고 인간과 유사한 대화형 인터페이스를 만들 수 있도록 해줍니다. Bot Framework의 Direct Line Speech 채널은 짧은 대기 시간 및 높은 안정성으로 음성 입력, 음성 출력 상호 작용을 가능하게 하는 호환 가능 봇에 조정되고 조율된 진입점을 제공함으로써 이러한 기능을 향상시킵니다. | [예](voice-first-virtual-assistants.md) | 아니요 |

## <a name="news-and-updates"></a>새로운 기능 및 업데이트 사항

Azure Speech Services의 새로운 기능에 대해 알아봅니다.

* 2019년 5월 - 이제 [대화 기록](conversation-transcription-service.md), [호출 센터 전사](call-center-transcription.md) 및 [음성 우선 가상 도우미](voice-first-virtual-assistants.md)에 대한 설명서를 사용할 수 있습니다.
* 2019년 5월
    * Speech SDK 1.5.1이 릴리스되었습니다. 업데이트, 향상된 기능 및 알려진 문제의 전체 목록은 [릴리스 정보](releasenotes.md)를 참조하세요.
    * Speech SDK 1.5.0이 릴리스되었습니다. 업데이트, 향상된 기능 및 알려진 문제의 전체 목록은 [릴리스 정보](releasenotes.md)를 참조하세요.
* 2019년 4월 - Windows 및 Linux에서 C++, C# 및 Java용 텍스트 음성 변환(베타)을 지원하는 Speech SDK 1.4.0을 출시했습니다. 또한 SDK는 이제 Linux에서 C++ 및 C#용 MP3 및 Opus/Ogg 오디오 형식을 지원합니다. 업데이트, 향상된 기능 및 알려진 문제의 전체 목록은 [릴리스 정보](releasenotes.md)를 참조하세요.
* 2019년 3월 - 특정 지역에서 사용할 수 있는 전체 음성 목록을 반환하는 새로운 TTS(텍스트 음성 변환) 엔드포인트가 제공됩니다. 또한 새로운 지역에서 TTS가 지원됩니다. 자세한 내용은 [Text-to-speech API 참조(REST)](rest-text-to-speech.md)를 참조하세요.

## <a name="try-speech-services"></a>Speech Services 사용해 보기

각각이 10분 이내에 코드를 실행할 수 있게 디자인된 가장 인기 있는 프로그래밍 언어로 빠른 시작을 제공합니다. 이 표에는 각 기능에 대해 가장 인기 있는 빠른 시작이 포함되어 있습니다. 왼쪽 탐색 영역을 사용하여 추가 언어 및 플랫폼을 확인하세요.

| 음성 텍스트 변환(SDK) | 텍스트 음성 변환(SDK) | 번역(SDK) |
|----------------------|----------------------|-------------------|
| [C#, .NET Core(Windows)](quickstart-csharp-dotnet-windows.md) | [C#, .NET Framework(Windows)](quickstart-text-to-speech-dotnet-windows.md) | [Java(Windows, Linux)](quickstart-translate-speech-java-jre.md) |
| [JavaScript(브라우저)](quickstart-js-browser.md) | [C++(Windows)](quickstart-text-to-speech-cpp-windows.md) | [C#, .NET Core(Windows)](quickstart-translate-speech-dotnetcore-windows.md) |
| [Python(Windows, Linux, macOS)](quickstart-python.md) | [C++(Linux)](quickstart-text-to-speech-cpp-linux.md) | [C#, .NET Framework(Windows)](quickstart-translate-speech-dotnetframework-windows.md) |
| [Java(Windows, Linux)](quickstart-java-jre.md) | | [C++(Windows)](quickstart-translate-speech-cpp-windows.md) |

> [!NOTE]
> 음성 텍스트 변환 및 텍스트 음성 변환은 REST 엔드포인트와 연결된 빠른 시작일 수도 있습니다.

Speech Services를 사용해본 경우 Speech SDK 및 LUIS를 사용하여 음성에서 의도를 인식하는 방법을 설명하는 자습서를 사용해 보세요.

* [자습서: Speech SDK 및 LUIS, C#을 사용하여 음성에서 의도 인식](how-to-recognize-intents-from-speech-csharp.md)
* [자습서: 텍스트 번역, 감정 분석 및 번역된 텍스트를 음성, REST로 합성하는 Flask 앱 빌드](https://docs.microsoft.com/azure/cognitive-services/translator/tutorial-build-flask-app-translation-synthesis?toc=%2fazure%2fcognitive-services%2fspeech-service%2ftoc.json&bc=%2fazure%2fcognitive-services%2fspeech-service%2fbreadcrumb%2ftoc.json&toc=%2Fen-us%2Fazure%2Fcognitive-services%2Fspeech-service%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)

## <a name="get-sample-code"></a>샘플 코드 가져오기

샘플 코드는 각 Azure Speech Services에 대한 GitHub에서 사용할 수 있습니다. 이러한 샘플은 파일 또는 스트림에서 오디오 읽기, 연속 및 1단계 인식 및 사용자 지정 모델 사용과 같은 일반적인 시나리오를 다룹니다. 다음 링크를 사용하여 SDK 및 REST 샘플을 확인하세요.

* [음성 텍스트 변환, 텍스트 음성 변환 및 음성 번역 샘플(SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
* [일괄 처리 기록 샘플(REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
* [텍스트 음성 변환 샘플(REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="customize-your-speech-experience"></a>음성 환경 사용자 지정

Azure Speech Services는 기본 제공 모델에 잘 작동하지만, 제품 또는 환경에 대한 경험을 추가로 사용자 지정하고 조정하려고 할 수 있습니다. 사용자 지정 옵션은 음향 모델 조정부터 브랜드를 위한 고유한 음성 글꼴까지 다양합니다. 사용자 지정 모델을 작성한 후 Azure Speech Services와 함께 사용할 수 있습니다.

| Speech Service | 모델 | 설명 |
|----------------|-------|-------------|
| 음성 텍스트 변환 | [음향 모델](how-to-customize-acoustic-models.md) | 자동차 또는 공장 작업장과 같은 고유한 녹음 조건을 갖는 특수한 환경에서 사용되는 애플리케이션, 도구 또는 디바이스에 대해 사용자 지정 음향 모델을 만듭니다. 액센트가 있는 음성, 특정 배경 소음, 레코딩에 특정 마이크 사용 등을 예로 들 수 있습니다. |
| | [언어 모델](how-to-customize-language-model.md) | 필드별 어휘 및 문법(예: 의료 용어 또는 IT 전문 용어)으로 기록을 향상시키는 사용자 지정 언어 모델을 만듭니다. |
| | [발음 모델](how-to-customize-pronunciation.md) | 사용자 지정 발음 모델을 사용하여 사용자가 발음 유형과 단어 또는 용어의 표시를 정의할 수 있습니다. 제품 이름 또는 머리글자어와 같은 사용자 지정된 용어를 처리하는 데 유용합니다. 발음 파일만 있으면 시작 가능 - 간단한 .txt 파일 |
| 텍스트 음성 변환 | [음성 글꼴](how-to-customize-voice-font.md) | 사용자 지정 글꼴을 사용하여 브랜드에 대해 인식 가능한 한 가지 종류의 음성을 만들 수 있습니다. 처음에는 소량의 데이터로 시작하세요. 더 많은 데이터를 제공할수록 더 자연스럽고 인간과 유사한 소리가 나는 음성 글꼴이 됩니다. |

## <a name="reference-docs"></a>참조 문서

* [Speech SDK](speech-sdk-reference.md)
* [Speech Devices SDK](speech-devices-sdk.md)
* [REST API: 음성 텍스트 변환](rest-speech-to-text.md)
* [REST API: 텍스트 음성 변환](rest-text-to-speech.md)
* [REST API: 일괄 처리 기록 및 사용자 지정](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [무료로 Speech Service 구독 키 받기](get-started.md)
