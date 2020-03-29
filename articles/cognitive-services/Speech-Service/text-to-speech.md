---
title: 텍스트 음성 변환 - 음성 서비스
titleSuffix: Azure Cognitive Services
description: Speech 서비스의 텍스트 음성 변환 기능을 사용하면 응용 프로그램, 도구 또는 장치에서 텍스트를 인간과 같은 자연스러운 합성 음성으로 변환할 수 있습니다. 미리 설정된 음성을 선택하거나 사용자 지정 음성을 만들 수 있습니다.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: dapine
ms.openlocfilehash: 7c7574ee4e9f75b9b650ff63a10666c5e379fca8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131574"
---
# <a name="what-is-text-to-speech"></a>텍스트 음성 변환이란?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

음성 변환 서비스의 텍스트 음성 변환서비스를 사용하면 응용 프로그램, 도구 또는 장치에서 텍스트를 인간과 같은 합성 음성으로 변환할 수 있습니다. 표준 및 신경 음성 중에서 선택하거나 제품 또는 브랜드에 고유한 사용자 지정 음성을 만들 수 있습니다. 75+ 표준 음성은 45개 이상의 언어와 로캘에서 사용할 수 있으며, 5개의 신경 음성은 선택된 수의 언어와 로캘에서 사용할 수 있습니다. 지원되는 음성, 언어 및 로캘의 전체 목록은 [지원되는 언어를](language-support.md#text-to-speech)참조하십시오.

> [!NOTE]
> 빙 연설은 2019년 10월 15일에 폐기되었습니다. 응용 프로그램, 도구 또는 제품이 Bing 음성 API 또는 사용자 지정 음성을 사용하는 경우 음성 서비스로 마이그레이션하는 데 도움이 되는 가이드를 만들었습니다.
> - [Bing 음성에서 음성 서비스로 마이그레이션](how-to-migrate-from-bing-speech.md)

## <a name="core-features"></a>핵심 기능

* 음성 합성 - [음성 SDK](quickstarts/text-to-speech-audio-file.md) 또는 [REST API를](rest-text-to-speech.md) 사용하여 표준, 신경 또는 사용자 지정 음성을 사용하여 텍스트 음성변환

* 긴 오디오의 비동기 합성 - [Long Audio API를](long-audio-api.md) 사용하여 텍스트 음성 변환 파일을 10분 이상 비동기적으로 합성합니다(예: 오디오 북 또는 강의). 음성 SDK 또는 음성-텍스트 REST API를 사용하여 수행된 합성과 달리 응답은 실시간으로 반환되지 않습니다. 요청이 비동기적으로 전송되고 응답이 폴링되며 서비스에서 사용할 수 있게 되면 합성된 오디오가 다운로드될 것으로 예상됩니다. 사용자 지정 신경 음성만 지원됩니다.

* 표준 음성 - 통계 적 파라메트릭 합성 및 / 또는 연결 합성 기술을 사용하여 생성. 이 목소리는 매우 이해하기 쉽고 자연스러운 소리입니다. 다양한 음성 옵션을 통해 애플리케이션이 45개 이상의 언어로 쉽게 말할 수 있습니다. 이러한 음성은 약어 지원, 약어 확장, 날짜/시간 해석, 폴리폰 등을 포함하여 높은 발음 정확도를 제공합니다. 표준 음성의 전체 목록은 [지원되는 언어를](language-support.md#text-to-speech)참조하십시오.

* 신경 음성 - 심층 신경망은 음성 언어의 스트레스와 억양에 관한 전통적인 음성 합성의 한계를 극복하는 데 사용됩니다. 프로소디 예측과 음성 합성이 동시에 수행되어 더 유연하고 자연스러운 출력을 생성합니다. 신경 음성은 챗봇 및 음성 비서와의 상호 작용을 보다 자연스럽고 매력적인 수준으로 만들고, 전자책과 같은 디지털 텍스트를 오디오북으로 변환하고, 차량 내비게이션 시스템을 향상시키는 데 사용할 수 있습니다. 인간과 같은 자연스러움과 명확한 단어 조음으로, 신경 음성은 AI 시스템과 상호 작용할 때 청취 피로를 크게 줄여줄것입니다. 신경 음성의 전체 목록은 [지원되는 언어를](language-support.md#text-to-speech)참조하십시오.

* 음성 합성 태그 언어(SSML) - 음성-텍스트 출력을 사용자 지정하는 데 사용되는 XML 기반 태그 언어입니다. SSML을 사용하면 피치를 조정하고, 일시 중지를 추가하고, 발음을 향상시키고, 말하기 속도를 높이거나 느리게 하고, 볼륨을 늘리거나 줄이며, 여러 음성을 단일 문서에 특성화할 수 있습니다. [SSML](speech-synthesis-markup.md)을 참조하십시오.

## <a name="get-started"></a>시작

텍스트 음성 변환 서비스는 [음성 SDK를](speech-sdk.md)통해 사용할 수 있습니다. 퀵스타트로 사용할 수 있는 몇 가지 일반적인 시나리오가 있으며, 다양한 언어와 플랫폼에서 제공됩니다.

* [오디오 파일로 음성 합성](quickstarts/text-to-speech-audio-file.md)
* [스피커로 음성 합성](quickstarts/text-to-speech.md)
* [비동기적으로 긴 형식 오디오 합성](quickstarts/text-to-speech/async-synthesis-long-form-audio.md)

원하는 경우 [REST를](rest-text-to-speech.md)통해 텍스트 음성 변환 서비스에 액세스할 수 있습니다.

## <a name="sample-code"></a>예제 코드

텍스트 음성 변환에 대한 샘플 코드는 GitHub에서 사용할 수 있습니다. 이 샘플은 가장 인기 있는 프로그래밍 언어의 텍스트-음성 변환을 다룹니다.

- [텍스트 음성 변환 샘플(SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [텍스트 음성 변환 샘플(REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="customization"></a>사용자 지정

표준 음성 및 신경 음성 외에도 제품 또는 브랜드고유의 사용자 지정 음성을 만들고 미세 조정할 수 있습니다. 시작하기 위해 필요한 것은 소수의 오디오 파일과 관련 전사뿐입니다. 자세한 내용은 [사용자 지정 음성 으로 시작하기](how-to-custom-voice.md) 를 참조하십시오.

## <a name="pricing-note"></a>가격 표

텍스트 음성 변환 서비스를 사용하는 경우 구두점을 포함하여 음성으로 변환된 각 문자에 대한 요금이 청구됩니다. SSML 문서 자체는 청구할 수 없지만 음소 및 피치와 같이 텍스트가 음성으로 변환되는 방식을 조정하는 데 사용되는 선택적 요소는 청구 가능한 문자로 계산됩니다. 청구 가능한 목록은 다음과 같습니다.

- 요청의 SSML 본문에서 텍스트 음성 변환 서비스에 전달된 텍스트
- SSML 형식의 요청 본문의 텍스트 필드 내의 모든 `<speak>` `<voice>` 태그(및 태그 제외)
- 문자, 구두점, 공백, 탭, 마크업 및 모든 공백 문자
- 유니코드에 정의된 모든 코드 포인트

자세한 내용은 [가격 책정](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)을 참조하십시오.

> [!IMPORTANT]
> 각 중국어, 일본어 및 한국어 문자는 청구시 두 문자로 계산됩니다.

## <a name="reference-docs"></a>참조 문서

- [음성 SDK](speech-sdk.md)
- [REST API: 텍스트 음성 변환](rest-text-to-speech.md)

## <a name="next-steps"></a>다음 단계

- [무료 음성 서비스 구독 받기](get-started.md)
- [Speech SDK 가져오기](speech-sdk.md)
