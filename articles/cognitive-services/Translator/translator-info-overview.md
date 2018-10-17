---
title: Translator Text API란?
titlesuffix: Azure Cognitive Services
description: Translator Text API를 응용 프로그램, 웹 사이트, 도구 및 기타 솔루션에 통합하여 다국어 사용자 환경을 제공합니다.
services: cognitive-services
author: noellelacharite
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: overview
ms.date: 05/10/2018
ms.author: nolachar
ms.openlocfilehash: 47ac3c81b85424a5cacab00863cd88fab9463fb9
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/18/2018
ms.locfileid: "46124663"
---
# <a name="what-is-translator-text-api"></a>Translator Text API란?

Translator Text API는 응용 프로그램, 웹 사이트, 도구 또는 기타 솔루션에 원활하게 통합되어 [60개 언어가 넘는](languages.md) 다국어 사용자 환경을 제공할 수 있습니다. 모든 하드웨어 플랫폼 및 모든 운영 체제에서 텍스트-텍스트 언어 번역을 수행하는 데 사용할 수 있습니다.

Translator Text API는 사용자 개발 프로젝트에서 쉽게 사용할 수 있는 클라우드의 AI 알고리즘 및 기계 학습의 Azure [Cognitive Services API](https://docs.microsoft.com/azure/#pivot=products&panel=ai) 컬렉션에 포함됩니다.

## <a name="about-microsoft-translator"></a>Microsoft Translator 정보

Microsoft Translator는 클라우드 기반 기계 번역 서비스입니다. 이 서비스의 핵심에는 다양한 Microsoft 제품 및 서비스를 제공하고 전세계 수천 개 비즈니스가 해당 응용 프로그램 및 워크플로에서 사용하면서 해당 콘텐츠가 전세계 고객에게 도달하게 하는 Translator Text API 및 [Translator Speech API](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-translation)가 있습니다.

음성 번역은 기존 Translator Speech API, Bing Speech API 및 Custom Speech Service(미리 보기)를 완전히 사용자 지정 가능한 통합 서비스에 결합한 [Cognitive Services Speech 미리 보기](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/)를 통해 사용할 수도 있습니다.  

[Microsoft Translator 서비스](https://www.microsoft.com/en-us/translator/home.aspx)를 자세히 알아보기

## <a name="language-customization"></a>언어 사용자 지정

핵심 Microsoft Translator 서비스의 확장인 Custom Translator는 Translator Text API와 함께 사용하여 신경망 번역 시스템을 사용자 지정하고 특정 용어 및 스타일에 맞게 번역을 향상시킬 수 있습니다.

Custom Translator를 사용하면 고유한 비즈니스 및 업계에서 사용되는 용어를 처리하는 번역 시스템을 구축할 수 있습니다. 사용자 지정 번역 시스템은 범주 매개 변수를 사용하여 일반 Microsoft Translator Text API를 통해 여러 유형의 장치에서 기존 응용 프로그램, 워크플로 및 웹 사이트에 쉽게 통합됩니다. 

[언어 사용자 지정](customization.md)에 대한 자세한 정보

## <a name="microsoft-translator-neural-machine-translation"></a>Microsoft Translator 신경망 기계 번역

NMT(신경망 기계 번역)는 고품질 AI 기반 기계 번역에 대한 새로운 표준으로, 2010년 중반에 안정적인 품질 수준에 도달한 레거시 SMT(통계 기계 번역) 기술을 대체합니다.

NMT는 원시 번역 품질 채점 관점에서 SMT보다 더 나은 번역을 제공할 뿐만 아니라 더 유창하고 인간적으로 들릴 수도 있습니다. 이렇게 유연한 번역이 제공될 수 있는 핵심 이유는 NMT가 단어를 번역하는 데 문장의 전체 컨텍스트를 사용한다는 것입니다. SMT는 각 단어 앞뒤의 몇 개 단어로 된 컨텍스트만 사용합니다.

NMT 모델은 API의 핵심이며, 최종 사용자에게는 보이지 않습니다. 눈에 띄는 유일한 차이점은 특히 중국어, 일본어 및 아랍어와 같은 언어에 대한 번역 품질이 향상되었다는 것입니다. 

[NMT 작동 방법](https://www.microsoft.com/en-us/translator/mt.aspx#nnt)에 대해 자세히 알아보기

## <a name="next-steps"></a>다음 단계

- [가격 정보](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)를 참조합니다.

- 액세스 키를 [등록](translator-text-how-to-signup.md)합니다.

- [빠른 시작](quickstarts/csharp.md)에서 C#으로 작성된 REST API를 연습할 수 있습니다. 최소한의 코드를 사용하여 텍스트를 한 언어에서 다른 언어로 번역하는 방법을 알아봅니다.

- [API 참조 설명서](https://docs.microsoft.com/azure/cognitive-services/Translator/reference/v3-0-reference)는 API에 대한 기술 설명서를 제공합니다.

## <a name="see-also"></a>참고 항목

- [Cognitive Services 설명서 페이지](https://docs.microsoft.com/azure/#pivot=products&panel=ai)
- [Cognitive Services 제품 페이지](https://azure.microsoft.com/services/cognitive-services/)
- [솔루션 및 가격 책정 정보](https://www.microsoft.com/en-us/translator/default.aspx)
