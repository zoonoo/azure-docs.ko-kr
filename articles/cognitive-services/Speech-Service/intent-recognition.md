---
title: 의도 인식 개요 - Speech Service
titleSuffix: Azure Cognitive Services
description: 의도 인식을 사용하여 미리 정의된 사용자 목표를 인식할 수 있습니다. 이 문서는 의도 인식 서비스의 이점과 기능을 개략적으로 설명합니다.
services: cognitive-services
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: v-demjoh
keywords: 의도 인식
ms.openlocfilehash: 532101c8cc307e6a5bb65022702b516c492a51fe
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107210374"
---
# <a name="what-is-intent-recognition"></a>의도 인식이란?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

이 개요에서는 의도 인식의 이점과 기능에 대해 알아봅니다. Cognitive Services Speech SDK는 LUIS(Language Understanding) 서비스와 통합되어 의도를 인식합니다. 의도란 항공권 예약, 날씨 확인, 호출 등 사용자가 수행하려는 것을 말합니다.
의도 인식을 사용하여 애플리케이션, 도구 및 디바이스는 LUIS에서 정의한 옵션에 따라 사용자가 시작하거나 수행할 작업을 결정할 수 있습니다.

## <a name="luis-key-required"></a>LUIS 키 필요

* LUIS는 음성 서비스와 통합되어 음성에서 의도를 인식합니다. 음성 서비스 구독은 필요 없고 LUIS만 있으면 됩니다.
* 음성 의도 인식은 SDK와 통합됩니다. Speech Service에 LUIS 키를 사용할 수 있습니다.
* Speech SDK를 통한 의도 인식은 [LUIS에서 지원하는 지역의 하위 집합](./regions.md#intent-recognition)에서 제공됩니다.

## <a name="get-started"></a>시작

의도 인식을 시작하려면 [빠른 시작](get-started-intent-recognition.md)을 참조하세요.

## <a name="sample-code"></a>예제 코드

의도 인식용 샘플 코드:

* [빠른 시작: 미리 빌드된 홈 자동화 앱 사용](../luis/luis-get-started-create-app.md)
* [C#용 Speech SDK를 사용하여 음성에서 의도 인식](./how-to-recognize-intents-from-speech-csharp.md)
* [C#의 Unity를 사용하는 의도 인식 및 기타 Speech Service](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/unity/speechrecognizer)
* [Python용 Speech SDK를 사용하여 의도 인식](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/python/console)
* [Windows에서 C++용 Speech SDK를 사용하는 의도 인식 및 기타 Speech Service](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/cpp/windows/console)
* [Windows 또는 Linux에서 Java용 Speech SDK를 사용하는 의도 인식 및 기타 Speech Service](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/java/jre/console)
* [웹 브라우저에서 JavaScript용 Speech SDK를 사용하는 의도 인식 및 기타 Speech Service](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/js/browser)

## <a name="reference-docs"></a>참조 문서

* [Speech SDK](./speech-sdk.md)

## <a name="next-steps"></a>다음 단계

* 의도 인식 [빠른 시작](get-started-intent-recognition.md) 완료
* [평가판 Speech Service 구독 키 받기](overview.md#try-the-speech-service-for-free)
* [Speech SDK 가져오기](speech-sdk.md)