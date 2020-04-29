---
title: 콜 센터 기록-음성 서비스
titleSuffix: Azure Cognitive Services
description: 음성 텍스트에 대 한 일반적인 시나리오는 IVR (대화형 음성 응답)과 같은 다양 한 시스템에서 제공 되는 많은 수의 전화 통신 데이터를 찍으면 되므로 간편는 것입니다. 비즈니스는 음성 서비스와 통합 된 음성 모델을 사용 하 여 오디오 캡처 시스템으로 고품질의 고급 기능을 얻을 수 있습니다.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: d959f4948d6b848f3b399c1310add06991d72012
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2020
ms.locfileid: "74806323"
---
# <a name="speech-service-for-telephony-data"></a>전화 통신 데이터를 위한 음성 서비스

전화 통신 데이터는 유선전화, 휴대폰 및 라디오를 통해 생성되며, 일반적으로 품질이 낮고 대역이 8 kHz로 좁아서 음성 텍스트 변환 시 어려움이 있습니다. 찍으면 되므로 간편의 음성 서비스 excel에서의 최신 음성 인식 모델은 사용자가 데이터를 이해 하기 어려운 경우에도이 전화 통신 데이터를 확인 합니다. 이러한 모델은 많은 양의 전화 통신 데이터를 사용 하 여 학습 되며, 잡음이 많은 환경 에서도 시장 시장 인식 정확도가 우수 합니다.

음성 텍스트 변환의 일반적인 시나리오는 IVR(대화형 음성 응답)과 같은 다양한 시스템에서 발생할 수 있는 대량의 전화 통신을 전사하는 것입니다. 해당 시스템이 제공하는 오디오는 스테레오 또는 모노 모두 가능하며, 신호에 대한 후처리를 거의 또는 전혀 수행하지 않은 원시 데이터일 수 있습니다. 음성 서비스와 통합 된 음성 모델을 사용 하 여 비즈니스는 오디오를 캡처하는 데 사용 되는 시스템에 상관 없이 고품질의 고급 기능을 얻을 수 있습니다.

전화 통신을 사용하면 고객의 요구를 더 잘 이해하거나 새로운 시장 기회를 파악하거나 콜 센터 상담원의 수행능력을 평가할 수 있습니다. 데이터를 transcribed 한 후 비즈니스는 향상 된 원격 분석, 핵심 문구 식별 또는 고객 감정 분석 등의 목적으로 출력을 사용할 수 있습니다.

이 페이지에 요약한 기술은 Microsoft가 실시간 모드와 일괄 처리 모드에서 모두 다양한 지원 통화 처리 서비스를 위해 내부적으로 사용하는 기술입니다.

음성 서비스에서 제공 하는 기술 및 관련 기능 중 일부를 검토해 보겠습니다.

> [!IMPORTANT]
> 음성 서비스 통합 모델은 다양 한 데이터를 사용 하 여 학습 되며 받아쓰기에서 전화 통신 분석까지 다양 한 시나리오에 대 한 단일 모델 솔루션을 제공 합니다.

## <a name="azure-technology-for-call-centers"></a>Azure의 콜 센터용 기술

음성 서비스 기능의 기능 측면 외에도 기본 목적 – 콜 센터에 적용 되는 경우는 고객 환경을 개선 하는 것입니다. 이와 관련하여 세 가지 분명한 영역이 존재합니다.

- 호출 후 분석은 기본적으로 호출 후 호출 기록을 일괄 처리 합니다.
- 호출이 수행 될 때 다양 한 정보를 추출 하기 위해 오디오 신호를 처리 하는 실시간 분석 (감정가 중요 한 사용 사례가 됨)
- 음성 도우미 (봇)-에이전트 참여 없이 고객의 문제를 해결 하기 위해 고객과 봇 사이의 대화를 추진 하거나, 에이전트를 지원 하기 위해 AI (인공 지능) 프로토콜을 적용 하는 것입니다.

일괄 처리 시나리오 구현의 일반적인 아키텍처 개요도는 ![콜 센터 전사 아키텍처](media/scenarios/call-center-transcription-architecture.png) 아래의 그림을 참조하세요.

## <a name="speech-analytics-technology-components"></a>Speech Analytics 기술 구성 요소

Azure는 도메인에 대 한 호출이 post 인지 실시간 인지에 상관 없이 고객 환경을 개선 하기 위한 성숙 하 고 새로운 기술 집합을 제공 합니다.

### <a name="speech-to-text-stt"></a>STT(음성 텍스트 변환)

[음성 텍스트](speech-to-text.md) 는 모든 콜 센터 솔루션에서 가장 많이 검색 된 기능입니다. 많은 다운스트림 분석 프로세스에서 transcribed 텍스트를 사용 하므로_WER_(단어 오류 요금)는 매우 중요 합니다. 콜 센터 전사의 핵심 과제 중 하나는 콜 센터에서 흔히 발생하는 소음(예 - 배경에서 말하는 다른 상담원), 매우 다양한 언어 로캘과 방언 및 실제 전화 신호의 낮은 품질입니다. WER은 지정 된 로캘에 대해 음향 및 언어 모델이 얼마나 잘 학습 되는지와 밀접 하 게 연관 되어 있으므로 로캘에 맞게 모델을 사용자 지정 하는 기능이 중요 합니다. 당사의 최신 통합 버전 4.x 모델은 전사 정확도와 대기 시간 둘 다에 대한 솔루션입니다. 수십 시간 동안의 어쿠스틱 데이터와 수십억 개의 어휘 정보를 사용 하 여 학습 하 고, 통합 모델은 콜 센터 데이터를 높여줄 시장에서 가장 정확한 모델입니다.

### <a name="sentiment"></a>데이터

고객의 환경이 좋은지 여부를 측정하는 것은 콜 센터 영역에 적용할 때 음성 분석의 매우 중요한 영역 중 하나입니다. 당사의 [일괄 처리 전사 API](batch-transcription.md)는 발화별로 감정 분석을 제공합니다. 통화 음성 텍스트의 일부로 얻은 값 세트를 집계하여 상담원과 고객 모두에 대한 통화의 감정을 결정할 수 있습니다.

### <a name="silence-non-talk"></a>무음(말하지 않음)

지원 통화의 35%가 말하지 않는 시간(일명 무음)이 되는 것이 보통입니다. 비 대화가 발생 하는 몇 가지 시나리오는 다음과 같습니다. 에이전트는 고객과 함께 이전 케이스 기록을 조회 하는 에이전트, 고객의 데스크톱에 액세스 하 여 기능을 수행할 수 있는 도구를 사용 하는 에이전트, 전송 대기 중인 고객에 대 한 대기 등을 포함 합니다. 이러한 유형의 시나리오와 이러한 시나리오에서 발생 하는 중요 한 고객 구분의 수와 통화에서 발생 하는 위치에 따라 침묵에서 소리가 발생 하는 경우를 측정 하는 것이 매우 중요 합니다.

### <a name="translation"></a>Translation

일부 회사에서는 배달 관리자가 고객의 세계 전체 환경을 이해할 수 있도록 외국어 지원 호출에서 번역 된 기록을 제공 하는 방법을 시험해 볼 수 있습니다. 당사의 [번역](translation.md) 기능은 탁월합니다. 많은 로캘에 대해 오디오-오디오 또는 오디오-텍스트를 번역할 수 있습니다.

### <a name="text-to-speech"></a>텍스트에서 음성 변환

[텍스트 음성 변환](text-to-speech.md)은 고객과 상호 작용하는 봇을 구현하는 데 있어서 또 하나의 중요한 영역입니다. 일반적인 경로는 고객이 말하고, 해당 음성이 텍스트로 전사되고, 텍스트의 의도를 분석하고, 인식된 의도를 기반으로 응답을 합성한 다음, 자산을 고객에게 표시하거나 합성된 음성 응답을 생성하는 것입니다. 물론이 모든 것이 신속 하 게 발생 하므로 이러한 시스템이 성공 하면 대기 시간이 짧고 중요 한 구성 요소가 됩니다.

[음성-텍스트](speech-to-text.md), [LUIS](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/), [봇 프레임 워크](https://dev.botframework.com/), [텍스트 음성 변환](text-to-speech.md)등과 같은 다양 한 기술에 대 한 종단 간 대기 시간은 매우 낮습니다.

당사의 새 음성도 사람의 음성과 구분할 수 없습니다. 음성을 사용 하 여 자신의 고유한 개성을 제공할 수 있습니다.

### <a name="search"></a>검색

분석의 또 다른 요소는 특정 이벤트 또는 환경이 발생한 상호 작용을 식별하는 작업입니다. 이는 일반적으로 다음 두 가지 방법 중 하나를 사용 하 여 수행 됩니다. 사용자가 문구를 입력 하 고 시스템에 응답 하는 임시 검색 또는 분석가가 호출에서 시나리오를 식별 하는 논리 문 집합을 만든 다음 각 호출을 해당 쿼리 집합에 대해 인덱싱할 수 있는 보다 구조화 된 쿼리를 사용할 수 있습니다. 좋은 검색 예제는 "이 호출은 품질을 위해 기록 되어야 합니다. ". 대부분의 회사에서는 전화를 실제로 기록 하기 전에 해당 에이전트가 고객에 게 이러한 부인를 제공 하도록 합니다. 대부분의 분석 시스템은 쿼리/검색 알고리즘을 통해 발견 된 동작을 추세 할 수 있으며, 이러한 추세 보고는 궁극적으로 분석 시스템의 가장 중요 한 기능 중 하나입니다. [Cognitive Services 디렉터리](https://azure.microsoft.com/services/cognitive-services/directory/search/)를 통해 인덱싱 및 검색 기능을 사용하여 사용자의 엔드투엔드 솔루션을 크게 향상할 수 있습니다.

### <a name="key-phrase-extraction"></a>핵심 문구 추출

이 영역은 더 까다로운 분석 응용 프로그램 중 하나 이며 AI와 기계 학습의 응용 프로그램에서 기능과 합니다. 이 경우 주요 시나리오는 고객 의도를 유추 하는 것입니다. 고객이 전화를 건 이유가 무엇입니까? 고객의 문제가 무엇입니까? 고객이 부정적 경험을 한 이유가 무엇입니까? [텍스트 분석 서비스](https://azure.microsoft.com/services/cognitive-services/text-analytics/) 는 이러한 중요 한 키워드 또는 구를 추출 하기 위해 종단 간 솔루션을 신속 하 게 업그레이드 하는 데 사용할 수 있는 분석 집합을 제공 합니다.

이제 음성 인식용 일괄 처리 및 실시간 파이프라인을 좀 더 자세히 살펴보겠습니다.

## <a name="batch-transcription-of-call-center-data"></a>콜 센터 데이터의 일괄 전사

찍으면 되므로 간편 대량 오디오의 경우 Batch 기록 [API](batch-transcription.md)를 개발 했습니다. Batch Transcription API는 대량의 오디오 데이터를 비동기 방식으로 전사하기 위해 개발되었습니다. 찍으면 되므로 간편 콜 센터 데이터와 관련 하 여이 솔루션은 다음 핵심 요소를 기반으로 합니다.

- **정확도** -4 세대 통합 모델에서는 unsurpassed 기록 품질을 제공 합니다.
- **대기 시간** -대량으로 작업을 수행 하는 경우 신속 하 게 신속 하 게 수행 해야 합니다. [Batch Transcription API](batch-transcription.md)를 통해 시작된 전사 작업은 즉시 큐에 저장되며 작업이 실행되기 시작한 후 실시간 전사보다 더 빠르게 수행됩니다.
- **보안** -호출에 중요 한 데이터가 포함 될 수 있다는 것을 이해 합니다. 나머지 요소에서는 보안이 매우 높은 우선 순위 중 하나가 되도록 했습니다. 당사의 서비스는 ISO, SOC, HIPAA, PCI 인증을 획득했습니다.

콜 센터는 매일 대량의 오디오 데이터를 생성 합니다. 전화 통신 데이터를 Azure Storage와 같은 중앙 위치에 저장하는 업무의 경우 [Batch Transcription API](batch-transcription.md)를 사용하여 비동기 방식으로 전사를 요청하고 수신할 수 있습니다.

일반적인 솔루션은 다음과 같은 서비스를 사용합니다.

- 음성 서비스는 음성 텍스트를 높여줄 하는 데 사용 됩니다. 일괄 처리 기록 API를 사용 하려면 음성 서비스에 대 한 표준 구독 (S0)이 필요 합니다. 체험 구독(F0) 서비스는 제공하지 않습니다.
- [Azure Storage](https://azure.microsoft.com/services/storage/)는 전화 통신 데이터 및 Batch Transcription API에서 반환된 음성 텍스트를 저장하기 위해 사용됩니다. 이 스토리지 계정은 특히 새 파일이 추가될 때 알림을 사용해야 합니다. 해당 알림은 전사 프로세스를 트리거하기 위해 사용됩니다.
- [Azure Functions](https://docs.microsoft.com/azure/azure-functions/)는 각 기록에 대한 SAS(공유 액세스 디지털 서명) URI를 만들고 HTTP POST 요청을 트리거하여 전사를 시작하기 위해 사용됩니다. 또한 Azure Functions는 Batch Transcription API를 사용하여 전사를 검색하고 삭제하는 요청을 만들기 위해 사용됩니다.

내부적으로는 위의 기술을 사용하여 일괄 처리 모드에서 Microsoft 고객 통화를 지원하고 있습니다.
![일괄 처리 아키텍처](media/scenarios/call-center-batch-pipeline.png)

## <a name="real-time-transcription-for-call-center-data"></a>콜 센터 데이터에 대한 실시간 전사

일부 비즈니스는 대화 내용을 실시간으로 전사해야 합니다. 실시간 전사를 사용하면 키워드 식별, 대화와 관련된 콘텐츠와 리소스 검색 트리거, 감정을 모니터링하기 위한 접근성 개선 또는 원어민 화자가 아닌 고객과 상담원에 대한 번역 제공을 수행할 수 있습니다.

실시간 전사가 필요한 시나리오의 경우 [Speech SDK](speech-sdk.md) 사용을 권장합니다. 현재 음성 텍스트 변환은 [20가지가 넘는 언어](language-support.md)로 사용할 수 있으며, C++, C#, Java, Python, Node.js, Objective-C 및 JavaScript의 SDK를 사용할 수 있습니다. 샘플은 [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk)에서 각 언어로 사용할 수 있습니다. 최신 뉴스 및 업데이트는 [릴리스 정보](releasenotes.md)를 참조하세요.

내부적으로는 위의 기술을 사용 하 여 다음 다이어그램에 나와 있는 것 처럼 실시간 Microsoft 고객 호출을 분석 합니다.

![일괄 처리 아키텍처](media/scenarios/call-center-reatime-pipeline.png)

## <a name="a-word-on-ivrs"></a>IVR 참고사항

음성 서비스는 [음성 SDK](speech-sdk.md) 또는 [REST API](rest-apis.md)를 사용 하 여 모든 솔루션에 쉽게 통합 될 수 있습니다. 그러나 콜 센터 전사를 위해서는 추가 기술이 필요할 수 있습니다. 일반적으로 IVR 시스템과 Azure 간의 연결이 필요합니다. 이러한 구성 요소는 제공 하지 않지만, 다음은 IVR의 연결에 대 한 설명입니다.

여러 IVR 또는 전화 통신 서비스 제품 (예: Genesys 또는 오디오 코드)은 Azure 서비스에 대 한 인바운드 및 아웃 바운드 오디오 통과를 활성화 하는 데 활용할 수 있는 통합 기능을 제공 합니다. 기본적으로 사용자 지정 Azure 서비스는 전화 통화 세션 (예: Call Start 또는 Call End)을 정의 하는 특정 인터페이스를 제공 하 고, 음성 서비스에 사용 되는 인바운드 스트림 오디오를 수신 하는 WebSocket API를 노출할 수 있습니다. 대화 전사 또는 Bot Framework와 연결과 같은 아웃바운드 응답은 Microsoft의 텍스트 음성 변환 서비스와 동기화하고 재생을 위해 IVR로 돌아갈 수 있습니다.

또 다른 시나리오는 SIP (세션 시작 프로토콜)와 직접 통합 하는 것입니다. Azure 서비스는 SIP Server에 연결하므로 음성 텍스트 변환 및 텍스트 음성 변환 단계에 사용되는 인바운드 스트림 및 아웃바운드 스트림을 가져옵니다. SIP 서버에 연결하려면 Ozeki SDK와 같은 상용 소프트웨어 제품 또는 이 유형의 오디오 통화 시나리오를 지원하기 위한 [팀 통화 및 회의 API](/graph/api/resources/communications-api-overview)(현재 베타판)를 사용할 수 있습니다.

## <a name="customize-existing-experiences"></a>기존 환경 사용자 지정

 음성 서비스는 기본 제공 모델에서 잘 작동 합니다. 그러나 제품 또는 환경에 대 한 환경을 추가로 사용자 지정 하 고 조정할 수 있습니다. 사용자 지정 옵션은 음향 모델 조정부터 브랜드를 위한 고유한 음성 글꼴까지 다양합니다. 사용자 지정 모델을 작성 한 후에는 실시간 또는 일괄 처리 모드에서 음성 서비스 기능에 사용할 수 있습니다.

| Speech Service | 모델 | Description |
| -------------- | ----- | ----------- |
| 음성 텍스트 변환 | [음향 모델](how-to-customize-acoustic-models.md) | 자동차 또는 공장 작업장과 같은 고유한 녹음 조건을 갖는 특수한 환경에서 사용되는 애플리케이션, 도구 또는 디바이스에 대해 사용자 지정 음향 모델을 만듭니다. 액센트가 있는 음성, 특정 배경 소음, 레코딩에 특정 마이크 사용 등을 예로 들 수 있습니다. |
|                | [언어 모델](how-to-customize-language-model.md) | 산업별 어휘 및 문법(예: 의료 용어 또는 IT 전문 용어)의 전사를 개선하기 위해 사용자 지정 언어 모델을 만듭니다. |
|                | [발음 모델](how-to-customize-pronunciation.md) | 사용자 지정 음성 모델을 사용 하 여 윗주 형태 및 단어 또는 용어에 대 한 표시를 정의할 수 있습니다. 제품 이름 또는 머리글자어와 같은 사용자 지정된 용어를 처리하는 데 유용합니다. 시작 해야 하는 모든 작업은 간단한 `.txt` 파일인 음성 파일입니다. |
| 텍스트 음성 변환 | [음성 글꼴](how-to-customize-voice-font.md) | 사용자 지정 글꼴을 사용하여 브랜드에 대해 인식 가능한 한 가지 종류의 음성을 만들 수 있습니다. 처음에는 소량의 데이터로 시작하세요. 더 많은 데이터를 제공할수록 더 자연스럽고 인간과 유사한 소리가 나는 음성 글꼴이 됩니다. |

## <a name="sample-code"></a>예제 코드

샘플 코드는 각 Speech service 기능에 대해 GitHub에서 사용할 수 있습니다. 이러한 샘플은 파일 또는 스트림에서 오디오 읽기, 연속 및 1단계 인식 및 사용자 지정 모델 사용과 같은 일반적인 시나리오를 다룹니다. 다음 링크를 사용하여 SDK 및 REST 샘플을 확인하세요.

- [음성 텍스트 변환 및 음성 번역 샘플(SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [일괄 처리 기록 샘플(REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
- [텍스트 음성 변환 샘플(REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="reference-docs"></a>참조 문서

- [Speech SDK](speech-sdk-reference.md)
- [Speech Devices SDK](speech-devices-sdk.md)
- [REST API: 음성 텍스트 변환](rest-speech-to-text.md)
- [REST API: 텍스트 음성 변환](rest-text-to-speech.md)
- [REST API: 일괄 처리 기록 및 사용자 지정](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [평가판 Speech Service 구독 키 받기](get-started.md)
