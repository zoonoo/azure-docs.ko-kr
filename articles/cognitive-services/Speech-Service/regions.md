---
title: Speech 서비스 지역
description: Speech 서비스의 지역에 대한 참조입니다.
services: cognitive-services
author: mahilleb-msft
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 06/28/2018
ms.author: mahilleb
ms.openlocfilehash: 1cb00035dc8f1cdeabd1beb22ca69f47bf4bd89e
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/11/2018
ms.locfileid: "44379573"
---
# <a name="regions-of-the-speech-service"></a>Speech 서비스의 지역

Speech 서비스는 여러 다른 지역에서 사용할 수 있습니다.
구독을 만들 때 필요에 따라 사용 가능한 영역을 선택할 수 있습니다.

구독을 사용하는 경우 선택한 지역을 고려해야 합니다.

## <a name="rest-api"></a>REST API

REST API를 사용하여 적절한 지역별 끝점을 선택합니다.
자세한 내용은 [REST API](rest-apis.md)를 참조하세요.

## <a name="speech-sdk"></a>음성 SDK

[Speech SDK](speech-sdk.md)에서 지역을 문자열로 지정합니다(예를 들어, C#용 Speech SDK에서 [SpeechFactory.FromSubscription](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechfactory.fromsubscription)에 대한 매개 변수로 지정).

### <a name="regions-for-speech-recognition-and-translation"></a>음성 인식 및 번역 지역

아래 표에는 **음성 인식** 및 **번역** 서비스를 사용할 수 있는 지역이 나와 있습니다.

지역| Speech SDK의 지역 매개 변수 값| 포털
-|-
미국 서부| `westus`| https://westus.cris.ai
미국 서부2| `westus2`| https://westus2.cris.ai
미국 동부| `eastus`| https://eastus.cris.ai
미국 동부2| `eastus2`| https://eastus2.cris.ai
동아시아| `eastasia`| https://eastasia.cris.ai
동남아시아| `southeastasia`| https://southeastasia.cris.ai
북유럽| `northeurope`| https://northeurope.cris.ai
서유럽|  `westeurope`| https://westeurope.cris.ai

### <a name="regions-for-intent-recognition"></a>의도 인식 지역

Speech SDK를 통해 **의도 인식**에 사용할 수 있는 지역은 [Language Understanding 서비스 지역 페이지](/azure/cognitive-services/luis/luis-reference-regions)에 표시됩니다.
나열된 각 게시 지역에서 해당 Speech SDK 지역 매개 변수는 끝점 도메인 이름의 첫 번째 부분으로 결정됩니다.
예를 들어, `westus`를 사용하여 미국 서부 게시 지역을 지정합니다.
