---
title: 지역 - 음성 서비스
titlesuffix: Azure Cognitive Services
description: Speech 서비스의 지역에 대한 참조입니다.
services: cognitive-services
author: mahilleb-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/14/2019
ms.author: mahilleb
ms.custom: seodec18
ms.openlocfilehash: 32a8b4cbfd0d8b43d294155432a0feaf29036c4c
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55218141"
---
# <a name="speech-service-supported-regions"></a>Speech Service 지원 지역

음성 서비스를 통해 애플리케이션에서 오디오를 텍스트로 변환하고, 음성 번역을 수행하고, 텍스트를 음성으로 변환할 수 있습니다. 음성 SDK 및 REST API에 대한 고유한 엔드포인트를 사용하여 여러 지역에서 서비스를 사용할 수 있습니다.

구독에 대한 영역과 일치하는 엔드포인트를 사용하는지 확인합니다.

## <a name="speech-sdk"></a>음성 SDK

[Speech Service SDK](speech-sdk.md)에서 지역은 문자열로 지정됩니다(예: C#용 음성 SDK에서 `SpeechConfig.FromSubscription`에 대한 매개 변수로 지정).

### <a name="speech-recognition-and-translation"></a>음성 인식 및 번역

**음성 인식** 및 **번역**에 대해 이러한 지역에서 음성 SDK를 사용할 수 있습니다.

  지역 | 음성 SDK 매개 변수 | 음성 사용자 지정 포털
 ------|-------|--------
 미국 서부 | `westus` | https://westus.cris.ai
 미국 서부2 | `westus2` | https://westus2.cris.ai
 미국 동부 | `eastus` | https://eastus.cris.ai
 미국 동부2 | `eastus2` | https://eastus2.cris.ai
 동아시아 | `eastasia` | https://eastasia.cris.ai
 동남아시아 | `southeastasia` | https://southeastasia.cris.ai
 북유럽 | `northeurope` | https://northeurope.cris.ai
 서유럽 | `westeurope` | https://westeurope.cris.ai


### <a name="intent-recognition"></a>의도 인식

Speech SDK를 통해 **의도 인식**을 사용할 수 있는 지역은 다음과 같습니다.

 글로벌 지역 | 지역 | 음성 SDK 매개 변수
 ------|-------|--------
 아시아 | 동아시아 | `eastasia`
 아시아 | 동남아시아 | `southeastasia`
 오스트레일리아 | 오스트레일리아 동부 | `australiaeast`
 유럽 | 북유럽 | `northeurope`
 유럽 | 서유럽 | `westeurope`
 북아메리카 | 미국 동부 | `eastus`
 북아메리카 | 미국 동부 2 | `eastus2`
 북아메리카 | 미국 중남부 | `southcentralus`
 북아메리카 | 미국 중서부 | `westcentralus`
 북아메리카 | 미국 서부 | `westus`
 북아메리카 | 미국 서부 2 | `westus2`
 남미 | 브라질 남부 | `brazilsouth`

[LUIS(Language Understanding Service)](/azure/cognitive-services/luis/luis-reference-regions)에서 지원하는 게시 지역의 하위 집합입니다.

## <a name="rest-apis"></a>REST API

음성 서비스는 또한 음성-텍스트 및 텍스트-음성 변환 요청에 대한 REST 엔드포인트를 노출합니다.

### <a name="speech-to-text"></a>음성 텍스트 변환

음성-텍스트 변환 참조 설명서는 [REST API](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)를 참조하세요.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

### <a name="text-to-speech"></a>텍스트 음성 변환

텍스트-음성 변환 참조 설명서는 [REST API](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)를 참조하세요.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]
