---
title: 의도 인식 개요-음성 서비스
titleSuffix: Azure Cognitive Services
description: 의도 인식을 사용 하면 미리 정의 된 사용자 목표를 인식할 수 있습니다. 이 문서는 의도 인식 서비스의 이점과 기능을 개략적으로 설명 합니다.
services: cognitive-services
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: v-demjoh
keywords: 의도 인식
ms.openlocfilehash: 0d718459e0fd0ea410232d3a165b560aa8c59cd1
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2020
ms.locfileid: "92174641"
---
# <a name="what-is-intent-recognition"></a>의도 인식 이란?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

이 개요에서는 의도 인식의 이점과 기능에 대해 알아봅니다. Cognitive Services Speech SDK는 LUIS(Language Understanding) 서비스와 통합되어 의도를 인식합니다. 의도란 항공권 예약, 날씨 확인, 호출 등 사용자가 수행하려는 것을 말합니다.
응용 프로그램, 도구 및 장치에서 의도 인식 기능을 사용 하 여 사용자가 LUIS에서 정의 하는 옵션에 따라 시작 하거나 수행할 작업을 결정할 수 있습니다.

## <a name="luis-key-required"></a>LUIS key 필요

* LUIS는 음성 서비스와 통합되어 음성에서 의도를 인식합니다. 음성 서비스 구독은 필요 없고 LUIS만 있으면 됩니다.
* 음성 의도 인식은 SDK와 통합 됩니다. 음성 서비스에 LUIS 키를 사용할 수 있습니다.
* Speech SDK를 통한 의도 인식은 [LUIS에서 지 원하는 지역의 하위 집합에 제공](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#intent-recognition)됩니다.

## <a name="get-started"></a>시작하기

의도 인식을 시작 하려면 [빠른](quickstarts/intent-recognition.md) 시작을 참조 하세요.

## <a name="sample-code"></a>예제 코드

의도 인식에 대 한 샘플 코드:

* [빠른 시작: 미리 빌드된 홈 자동화 앱 사용](https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app)
* [C#용 Speech SDK를 사용하여 음성에서 의도 인식](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-recognize-intents-from-speech-csharp)
* [C에서 Unity를 사용 하 여 의도 인식 및 기타 음성 서비스 #](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/unity/speechrecognizer)
* [Python 용 Speech SDK를 사용 하 여 의도 인식](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/python/console)
* [Windows에서 c + + 용 Speech SDK를 사용 하 여 의도 인식 및 기타 음성 서비스](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/cpp/windows/console)
* [Windows 또는 Linux에서 Java 용 Speech SDK를 사용 하는 의도 인식 및 기타 음성 서비스](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/java/jre/console)
* [웹 브라우저에서 JavaScript 용 Speech SDK를 사용 하 여 의도 인식 및 기타 음성 서비스](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/js/browser)

## <a name="reference-docs"></a>참조 문서

* [Speech SDK](speech-sdk-reference.md)

## <a name="next-steps"></a>다음 단계

* 의도 인식 [빠른](quickstarts/intent-recognition.md) 시작 완료
* [평가판 Speech Service 구독 키 받기](overview.md#try-the-speech-service-for-free)
* [Speech SDK 가져오기](speech-sdk.md)
