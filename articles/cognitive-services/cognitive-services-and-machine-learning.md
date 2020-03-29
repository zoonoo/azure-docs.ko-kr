---
title: 코그너티브 서비스 및 머신 러닝
titleSuffix: Azure Cognitive Services
description: Azure Cognitive Services가 기계 학습을 위한 다른 Azure 제품과 어디에 적합한지 알아봅니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: diberry
ms.openlocfilehash: cde505e4c95de9b9693a0e9d260d7fa84f3e905b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "75531482"
---
# <a name="cognitive-services-and-machine-learning"></a>Cognitive Services 및 기계 학습

코그너티브 서비스는 감성에 대한 텍스트 분석이나 물체 나 얼굴을 인식하는 이미지를 분석하는 것과 같은 일반적인 문제를 해결하는 기계 학습 기능을 제공합니다. 이러한 서비스를 사용하기 위해 특별한 기계 학습 또는 데이터 과학 지식이 필요하지 않습니다. 

[코그너티브 서비스는](welcome.md) 각각 서로 다른 일반화된 예측 기능을 지원하는 서비스 그룹입니다. 서비스는 올바른 서비스를 찾을 수 있도록 서로 다른 범주로 나뉩니다. 

|서비스 카테고리|목적|
|--|--|
|[의사 결정](https://azure.microsoft.com/services/cognitive-services/directory/decision/)|합리적이고 효율적인 의사 결정을 위해 권장 사항을 표시하는 앱을 빌드하세요.|
|[언어](https://azure.microsoft.com/services/cognitive-services/directory/lang/)|앱에서 사전 구축된 스크립트를 통해 자연어를 처리하고, 감성을 평가하며, 사용자가 원하는 것을 인식하는 방법을 학습하도록 지원하세요.|
|[검색](https://azure.microsoft.com/services/cognitive-services/directory/search/)|Bing Search API를 앱에 추가하고, 단일 API 호출을 통해 수십억 개의 웹 페이지, 이미지, 비디오 및 뉴스를 검색하세요.|
|[Speech](https://azure.microsoft.com/services/cognitive-services/directory/speech/)|음성을 텍스트로 변환하고 텍스트를 자연스러운 음성으로 변환합니다. 한 언어를 다른 언어로 번역하고 화자 검증 및 인식 기능을 사용하도록 설정할 수 있습니다.|
|[시각](https://azure.microsoft.com/services/cognitive-services/directory/vision/)|사진, 동영상 및 디지털 잉크 콘텐츠를 인식 및 식별하고 캡션을 추가하고 인덱싱 및 조정합니다.|
||||

다음과 같은 경우 코그너티브 서비스 사용

* 일반화된 솔루션을 사용할 수 있습니다.
* 프로그래밍 REST API 또는 SDK에서 솔루션에 액세스합니다. 

다음을 수행할 때 다른 기계 학습 솔루션을 사용합니다.

* 알고리즘을 선택해야 하며 매우 구체적인 데이터를 학습해야 합니다.

## <a name="what-is-machine-learning"></a>머신 러닝이란 무엇인가요?

기계 학습은 특정 요구 사항을 해결하기 위해 데이터와 알고리즘을 결합하는 개념입니다. 데이터와 알고리즘이 학습되면 출력은 다른 데이터와 함께 다시 사용할 수 있는 모델입니다. 학습된 모델은 새 데이터를 기반으로 통찰력을 제공합니다. 

기계 학습 시스템을 구축하는 프로세스에는 기계 학습 또는 데이터 과학에 대한 지식이 필요합니다.

기계 학습은 [Azure 기계 학습(AML) 제품 및 서비스를](https://docs.microsoft.com/azure/architecture/data-guide/technology-choices/data-science-and-machine-learning?context=azure/machine-learning/studio/context/ml-context)사용하여 제공됩니다.

## <a name="what-is-a-cognitive-service"></a>코그너티브 서비스란?

코그너티브 서비스는 기계 학습 솔루션의 구성 요소 중 일부 또는 전부인 데이터, 알고리즘 및 학습된 모델을 제공합니다. 이러한 서비스는 기계 학습 또는 데이터 과학에 대한 경험이 필요 없이 데이터에 대한 일반적인 지식을 요구하기 위한 것입니다. 이러한 서비스는 REST API와 언어 기반 SDK를 모두 제공합니다. 따라서 서비스를 사용하려면 프로그래밍 언어 지식이 있어야 합니다.

## <a name="how-are-cognitive-services-and-azure-machine-learning-aml-similar"></a>코그너티브 서비스 및 Azure 기계 학습(AML)은 어떻게 유사합니까?

둘 다 비즈니스 운영을 향상시키기 위해 인공 지능 (AI)을 적용하는 최종 목표를 가지고 있지만 각 제품에서 이를 제공하는 방법은 다릅니다. 

일반적으로 청중은 다릅니다.

* 코그너티브 서비스는 기계 학습 경험이 없는 개발자를 위한 것입니다.
* Azure 기계 학습은 데이터 과학자를 위해 맞춤화됩니다. 

## <a name="how-is-a-cognitive-service-different-from-machine-learning"></a>코그너티브 서비스는 기계 학습과 어떻게 다른가요?

코그너티브 서비스는 학습된 모델을 제공합니다. 이렇게 하면 REST API 또는 SDK에서 사용할 수 있는 데이터와 알고리즘이 함께 제공됩니다. 시나리오에 따라 몇 분 내에 이 서비스를 구현할 수 있습니다.  코그너티브 서비스는 텍스트의 핵심 구또는 이미지의 항목 식별과 같은 일반적인 문제에 대한 답변을 제공합니다. 

기계 학습은 일반적으로 성공적으로 구현하는 데 더 오랜 시간이 필요한 프로세스입니다. 이 시간은 데이터 수집, 정리, 변환, 알고리즘 선택, 모델 학습 및 배포에 소요되어 코그너티브 서비스에서 제공하는 것과 동일한 수준의 기능을 제공합니다. 머신 러닝을 사용하면 고도로 전문화된 및/또는 특정 문제에 대한 답변을 제공할 수 있습니다. 기계 학습 문제는 고려 중인 문제의 특정 주제와 데이터에 익숙해지고 데이터 과학에 대한 전문 지식이 필요합니다.

## <a name="what-kind-of-data-do-you-have"></a>어떤 종류의 데이터가 있습니까?

서비스 그룹인 Cognitive Services는 학습된 모델에 대한 사용자 지정 데이터가 없음, 일부 또는 모든 사용자 지정 데이터를 요구할 수 있습니다. 

### <a name="no-additional-training-data-required"></a>추가 교육 데이터가 필요하지 않습니다.

완전히 학습된 모델을 제공하는 서비스는 _블랙박스로_취급될 수 있습니다. 어떻게 작동하는지 또는 어떤 데이터가 학습에 사용되었는지 알 필요가 없습니다. 예측을 얻기 위해 데이터를 완전히 학습된 모델로 가져옵니다. 

### <a name="some-or-all-training-data-required"></a>일부 또는 모든 교육 데이터 필요

일부 서비스를 사용하면 자신의 데이터를 가져온 다음 모델을 학습할 수 있습니다. 이렇게 하면 자체 데이터로 서비스의 데이터 및 알고리즘을 사용하여 모델을 확장할 수 있습니다. 출력은 사용자의 요구와 일치합니다. 사용자 고유의 데이터를 가져올 때 서비스에 특정한 방식으로 데이터에 태그를 연결해야 할 수 있습니다. 예를 들어 꽃을 식별하기 위해 모델을 학습하는 경우 각 이미지의 꽃 위치와 함께 꽃 이미지 카탈로그를 제공하여 모델을 학습할 수 있습니다. 

서비스를 _통해_ 자체 데이터를 향상시키기 위해 데이터를 제공할 수 있습니다. 서비스는 데이터를 제공해야 할 _수_ 있습니다. 

### <a name="real-time-or-near-real-time-data-required"></a>실시간 또는 거의 실시간 데이터 필요

서비스는 효과적인 모델을 구축하기 위해 실시간 또는 거의 실시간 데이터가 필요할 수 있습니다. 이러한 서비스는 상당한 양의 모델 데이터를 처리합니다. 

## <a name="service-requirements-for-the-data-model"></a>데이터 모델에 대한 서비스 요구 사항

다음 데이터는 허용하거나 요구하는 데이터의 종류에 의해 각 서비스를 분류합니다.

|코그너티브 서비스|교육 데이터가 필요하지 않습니다.|일부 또는 모든 교육 데이터를 제공합니다.|실시간 또는 거의 실시간 데이터 수집|
|--|--|--|--|
|[Anomaly Detector](./Anomaly-Detector/overview.md)|x|x|x|
|Bing Search |x|||
|[컴퓨터 비전](./Computer-vision/Home.md)|x|||
|[콘텐츠 진행자](./Content-Moderator/overview.md)|x||x|
|[맞춤형 비전](./Custom-Vision-Service/home.md)||x||
|[얼굴](./Face/Overview.md)|x|x||
|[Form Recognizer](./form-recognizer/overview.md)||x||
|[몰입형 판독기](./immersive-reader/overview.md)|x|||
|[Ink Recognizer](./Ink-recognizer/overview.md)|x|x||
|[LUIS(Language Understanding)](./LUIS/what-is-luis.md)||x||
|[개인화](./personalizer/what-is-personalizer.md)|x*|x*|x|
|[QnA 메이커](./QnAMaker/Overview/overview.md)||x||
|[스피커 인식기](./speaker-recognition/home.md)||x||
|[음성 텍스트 음성 변환(TTS)](speech-service/text-to-speech.md)|x|x||
|[음성 음성-텍스트(STT)](speech-service/speech-to-text.md)|x|x||
|[음성 번역](speech-service/speech-translation.md)|x|||
|[텍스트 분석](./text-analytics/overview.md)|x|||
|[번역기 텍스트](./translator/translator-info-overview.md)|x|||
|[번역기 텍스트 - 사용자 지정 번역기](./translator/custom-translator/overview.md)||x||

*Personalizer는 귀하의 정책 및 데이터를 평가하기 위해 서비스에서 수집한 교육 데이터만 필요합니다(실시간으로 작동). 개인 설정 또는 일괄 학습을 위해 대규모 기록 데이터 집합이 필요하지 않습니다. 

## <a name="where-can-you-use-cognitive-services"></a>코그너티브 서비스는 어디에서 사용할 수 있습니까?
 
이 서비스는 REST API(들) 또는 SDK 호출을 할 수 있는 모든 응용 프로그램에서 사용됩니다. 응용 프로그램의 예로는 웹 사이트, 봇, 가상 또는 혼합 현실, 데스크톱 및 모바일 응용 프로그램이 있습니다. 

## <a name="how-is-azure-cognitive-search-related-to-cognitive-services"></a>Azure 인지 검색은 코그너티브 서비스와 어떻게 관련이 있습니까?

[Azure Cognitive Search는](../search/search-what-is-azure-search.md) 선택적으로 Cognitive 서비스를 사용하여 이미지 및 자연어 처리를 인덱싱 워크로드에 추가하는 별도의 클라우드 검색 서비스입니다. 코그너티브 서비스는 개별 API를 래핑하는 [기본 제공 기술을](../search/cognitive-search-predefined-skills.md) 통해 Azure 인지 검색에 노출됩니다. 연습에 사용 가능한 리소스를 사용할 수 있지만 더 큰 볼륨에 대해 [청구 가능한 리소스를](../search/cognitive-search-attach-cognitive-services.md) 만들고 첨부할 계획입니다.

## <a name="how-can-you-use-cognitive-services"></a>코그너티브 서비스는 어떻게 사용할 수 있습니까?

각 서비스는 데이터에 대한 정보를 제공합니다. 서비스를 함께 결합하여 음성(오디오)을 텍스트로 변환하고, 텍스트를 여러 언어로 번역한 다음, 번역된 언어를 사용하여 기술 자료에서 답변을 얻을 수 있습니다. 코그너티브 서비스는 자체적으로 지능형 솔루션을 만드는 데 사용할 수 있지만 기존 기계 학습 프로젝트와 결합하여 모델을 보완하거나 개발 프로세스를 가속화할 수도 있습니다. 

다른 기계 학습 도구에 대한 내보낸 모델을 제공하는 코그너티브 서비스:

|코그너티브 서비스|모델 정보|
|--|--|
|[맞춤형 비전](./custom-vision-service/home.md)|[Export](./Custom-Vision-Service/export-model-python.md) 안드로이드용 텐서플로우, iOS11용 코어ML, 윈도우 ML용 ONNX 용 수출|

## <a name="learn-more"></a>자세한 정보

* [아키텍처 가이드 - Microsoft의 기계 학습 제품은 무엇입니까?](https://docs.microsoft.com/azure/architecture/data-guide/technology-choices/data-science-and-machine-learning)
* [머신 러닝 - 딥 러닝 과 머신 러닝 소개](../machine-learning/concept-deep-learning-vs-machine-learning.md)

## <a name="next-steps"></a>다음 단계

* [Azure 포털](cognitive-services-apis-create-account.md) 또는 [Azure CLI를](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli)사용하여 인지 서비스 계정을 만듭니다.
* 코그너티브 서비스를 [인증하는](authentication.md) 방법에 대해 알아봅니다.
* 문제 식별 및 디버깅에 [진단 로깅을](diagnostic-logging.md) 사용합니다. 
* 도커 [컨테이너에](cognitive-services-container-support.md)인지 서비스를 배포합니다.
* 서비스 업데이트를 최신 상태로 [유지합니다.](https://azure.microsoft.com/updates/?product=cognitive-services)
