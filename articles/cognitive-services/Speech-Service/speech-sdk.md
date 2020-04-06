---
title: 음성 SDK 소개 - 음성 서비스
titleSuffix: Azure Cognitive Services
description: 음성 소프트웨어 개발 키트(SDK)는 많은 음성 서비스 기능을 노출하므로 음성 지원 응용 프로그램을 보다 쉽게 개발할 수 있습니다.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: dapine
ms.openlocfilehash: 440944033e4b6a8fb29cf9ace6ad784b91316526
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668759"
---
# <a name="about-the-speech-sdk"></a>Speech SDK 정보

음성 소프트웨어 개발 키트(SDK)는 음성 지원 응용 프로그램을 개발할 수 있도록 많은 음성 서비스 기능을 노출합니다. Speech SDK는 다양한 프로그래밍 언어와 모든 플랫폼에서 사용할 수 있습니다.

[!INCLUDE [Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

## <a name="scenario-capabilities"></a>시나리오 기능

음성 SDK는 음성 서비스의 많은 기능을 노출하지만 모든 기능을 노출하지는 않습니다. 음성 SDK의 기능은 종종 시나리오와 관련이 있습니다. Speech SDK는 로컬 장치, 파일, Azure Blob 저장소, 심지어 입력 및 출력 스트림을 사용하여 실시간 및 비실시간 시나리오모두에 이상적입니다. 스피치 SDK로 시나리오를 달성할 수 없는 경우 REST API 대안을 찾습니다.

### <a name="speech-to-text"></a>음성 텍스트 변환

[음성-텍스트(음성](speech-to-text.md) *인식이라고도*함)는 오디오 스트림을 응용 프로그램, 도구 또는 장치가 사용하거나 표시할 수 있는 텍스트로 전사합니다. [LUIS(Language Understanding)](../luis/index.yml)에서 음성 텍스트 변환을 사용하여 기록된 음성에서 사용자 의도를 파생시키고 음성 명령 작업에 따라 행동합니다. [음성 번역을](speech-translation.md) 사용하여 음성 입력을 단일 통화로 다른 언어로 번역할 수 있습니다. 자세한 내용은 [음성-텍스트 기본 을](speech-to-text-basics.md)참조하십시오.

### <a name="text-to-speech"></a>텍스트 음성 변환

[텍스트 음성](text-to-speech.md) *변환(음성 합성이라고도*함)은 텍스트를 인간과 같은 합성 음성으로 변환합니다. 입력 텍스트는 문자열 리터럴이거나 [SSML(음성 합성 태그 언어)을](speech-synthesis-markup.md)사용하는 것입니다. 표준 또는 신경 음성에 대한 자세한 내용은 [텍스트 음성 변환 언어 및 음성 지원을](language-support.md#text-to-speech)참조하십시오.

### <a name="voice-assistants"></a>음성 도우미

음성 도우미는 음성 SDK를 사용하여 개발자가 응용 프로그램 및 환경을 위해 인간과 같은 자연스러운 대화형 인터페이스를 만들 수 있도록 합니다. 음성 도우미 서비스는 장치와 도우미 간의 빠르고 안정적인 상호 작용을 제공합니다. 구현은 작업 완료를 위해 봇 프레임워크의 직접 라인 음성 채널 또는 통합 사용자 지정 명령(미리 보기) 서비스를 사용합니다. 또한 [사용자 지정 음성 포털을](https://aka.ms/customvoice) 사용하여 음성 도우미를 만들어 고유한 음성 환경을 만들 수 있습니다.

#### <a name="keyword-spotting"></a>키워드 스포팅

[키워드 스포팅의](speech-devices-sdk-create-kws.md) 개념은 음성 SDK에서 지원됩니다. 키워드 스포팅은 음성에서 키워드를 식별하는 행위이며 키워드를 듣는 즉시 동작합니다. 예를 들어 "안녕 마이크로 소프트 코타나"는 마이크로 소프트 코타나 도우미를 활성화합니다.

### <a name="meeting-scenarios"></a>회의 시나리오

Speech SDK는 단일 장치 또는 다중 장치 대화에서 회의 시나리오를 전사하는 데 적합합니다.

#### <a name="conversation-transcription"></a>대화 전사

[대화 전사를](conversation-transcription.md) 사용하면 각 스피커에 대한 실시간(및 비동기) 음성 인식, 스피커 식별 및 문장 *귀속(이층화라고도*함)을 사용할 수 있습니다. 화자를 구별하는 기능을 사용하여 직접 만남을 전사하는 데 이상적입니다.

#### <a name="multi-device-conversation"></a>다중 디바이스 대화

[멀티 디바이스 대화를](multi-device-conversation.md)통해 대화에서 여러 장치 또는 클라이언트를 연결하여 음성 기반 또는 텍스트 기반 메시지를 전송하고 전사 및 번역을 쉽게 지원합니다.

### <a name="custom--agent-scenarios"></a>사용자 지정/에이전트 시나리오

음성 SDK는 전화 통신 데이터가 생성되는 콜 센터 시나리오를 전사하는 데 사용할 수 있습니다.

#### <a name="call-center-transcription"></a>호출 센터 전사

[콜 센터 전사는](call-center-transcription.md) 대화형 음성 응답(IVR)과 같은 다양한 시스템에서 발생할 수 있는 대량의 전화 통신 데이터를 전사하기 위한 음성-텍스트의 일반적인 시나리오입니다. 음성 서비스의 최신 음성 인식 모델은 사람이 이해하기 어려운 경우에도 이 전화 통신 데이터를 전사하는 데 탁월합니다.

### <a name="codec-compressed-audio-input"></a>코덱 압축 오디오 입력

음성 SDK 프로그래밍 언어 중 일부는 코덱 압축 오디오 입력 스트림을 지원합니다. 자세한 내용은 <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-use-codec-compressed-audio-input-streams" target="_blank">압축 오디오 입력 형식 <span class="docon docon-navigate-external x-hidden-focus"> </span>사용을 </a>참조하십시오.

## <a name="rest-api"></a>REST API

Speech SDK는 음성 서비스의 많은 기능 기능을 다루지만 일부 시나리오에서는 REST API를 사용할 수 있습니다.

### <a name="batch-transcription"></a>일괄 처리 기록

[일괄 전사를](batch-transcription.md) 사용하면 대용량 데이터의 비동기 음성-텍스트 전사를 사용할 수 있습니다. 일괄 전사는 REST API에서만 가능합니다. 음성 오디오를 텍스트로 변환하는 것 외에도 배치 음성-텍스트는 diarization 및 감정 분석을 가능하게 합니다.

## <a name="customization"></a>사용자 지정

음성 서비스는 음성-텍스트, 텍스트 음성 변환 및 음성 변환 전반에 걸쳐 기본 모델로 뛰어난 기능을 제공합니다. 경우에 따라 고유한 사용 사례에서 더 잘 작동하도록 기준 성능을 높일 수 있습니다. Speech Service에는 다양한 코드 없는 사용자 지정 도구가 있어 데이터를 기반으로 하는 사용자 지정 모델과 경쟁 우위를 확보할 수 있습니다. 이러한 모델은 사용자 및 조직에서만 사용할 수 있습니다.

### <a name="custom-speech-to-text"></a>사용자 지정 음성-텍스트

고유한 환경에서 음성-텍스트 변환을 사용하여 사용자 지정 음향, 언어 및 발음 모델을 만들고 학습하여 주변 소음 또는 산업별 어휘를 해결할 수 있습니다. 코드 없음 사용자 지정 음성 모델의 생성 및 관리는 [사용자 지정 음성 포털을](https://aka.ms/customspeech)통해 사용할 수 있습니다. 사용자 지정 음성 모델이 게시되면 음성 SDK에서 사용할 수 있습니다.

### <a name="custom-text-to-speech"></a>사용자 지정 텍스트 음성 변환

맞춤 음성으로도 알려진 맞춤 텍스트 음성은 브랜드에 대해 인식할 수 있는 고유한 음성을 만들 수 있는 온라인 도구 세트입니다. 사용자 정의 음성 모델의 생성 및 관리는 [사용자 정의 음성 포털을](https://aka.ms/customvoice)통해 사용할 수 있습니다. 사용자 지정 음성 모델이 게시되면 음성 SDK에서 사용할 수 있습니다.

## <a name="get-the-speech-sdk"></a>Speech SDK 가져오기

# <a name="windows"></a>[Windows](#tab/windows)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-windows.md)]

# <a name="linux"></a>[Linux](#tab/linux)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-linux.md)]

# <a name="ios"></a>[iOS](#tab/ios)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-ios.md)]

# <a name="macos"></a>[macOS](#tab/macos)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-macos.md)]

# <a name="android"></a>[Android](#tab/android)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-android.md)]

# <a name="nodejs"></a>[Node.JS](#tab/nodejs)

[!INCLUDE [Get the Node.js Speech SDK](includes/get-speech-sdk-nodejs.md)]

# <a name="browser"></a>[브라우저](#tab/browser)

[!INCLUDE [Get the Browser Speech SDK](includes/get-speech-sdk-browser.md)]

---

[!INCLUDE [License notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

[!INCLUDE [Sample source code](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>다음 단계

* [음성 평가판 구독 가져오기](https://azure.microsoft.com/try/cognitive-services/)
* [C#에서 음성을 인식하는 방법 참조](quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
