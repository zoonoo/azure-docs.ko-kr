---
title: Azure Cognitive Services란?
titleSuffix: Azure Cognitive Services
description: Azure Cognitive Services는 REST API가 있는 클라우드 서비스이며, Microsoft Azure와 함께 사용하여 지능형 애플리케이션을 빌드할 수 있는 클라이언트 라이브러리 SDK입니다.
services: cognitive-services
author: nitinme
manager: nitinme
keywords: 인지적 서비스, 인지적 인텔리전스, 인지적 솔루션, AI 서비스, 인지적 이해, 인지적 기능
ms.service: cognitive-services
ms.subservice: ''
ms.topic: overview
ms.date: 08/28/2020
ms.author: nitinme
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: bcb3fdbe12a2704e585a9b13484c2528c9cdc559
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90894448"
---
# <a name="what-are-azure-cognitive-services"></a>Azure Cognitive Services란?

Azure Cognitive Services는 REST API가 있는 클라우드 기반 서비스이며, 개발자가 직접적인 AI(인공 지능) 또는 데이터 과학 기술이나 지식 없이도 인지적 인텔리전스를 애플리케이션에 빌드하도록 지원하는 데 사용할 수 있는 클라이언트 라이브러리 SDK입니다. Azure Cognitive Services를 사용하는 개발자는 보고, 듣고, 말하고, 이해하고, 추론할 수도 있는 인지적 솔루션을 통해 애플리케이션에 인지적 기능을 손쉽게 추가할 수 있습니다.

인지적 이해를 제공하는 AI 서비스 카탈로그는 크게 5가지로 분류됩니다.

* 시각
* 음성
* 언어
* Web Search
* 의사 결정

새 설명서의 현재 목록은 [Cognitive Services 문서의 새로운 기능](whats-new-docs.md)에서 확인할 수 있습니다.

## <a name="vision-apis"></a>Vision API

|서비스 이름|서비스 설명|
|:-----------|:------------------|
|[Computer Vision](https://docs.microsoft.com/azure/cognitive-services/computer-vision/ "Computer Vision")|Computer Vision 서비스는 이미지를 처리하고 정보를 반환하는 고급 인지 알고리즘에 대한 액세스를 제공합니다.|
|[Custom Vision Service](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/home "사용자 지정 시각 서비스")|Custom Vision Service를 사용하면 사용자 지정 이미지 분류자를 빌드할 수 있습니다.|
|[Face](https://docs.microsoft.com/azure/cognitive-services/face/ "Face")| Face 서비스는 개선된 얼굴 인식 알고리즘에 액세스할 수 있도록 하여 얼굴 특성 감지 및 인식을 지원합니다.|
|[Form Recognizer](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/ "Form Recognizer")(미리 보기)|Form Recognizer는 양식 문서에서 키-값 쌍 및 테이블 데이터를 식별하고 추출합니다. 그런 다음, 원본 파일의 관계를 포함한 정형 데이터를 출력합니다.|
|[Ink Recognizer](https://docs.microsoft.com/azure/cognitive-services/ink-recognizer/ "Ink Recognizer")(사용 중지)|Ink Recognizer를 사용하면 디지털 잉크 스트로크 데이터, 모양 및 필기 내용을 인식하여 분석하고, 모든 인식된 엔터티를 사용하여 문서 구조를 출력할 수 있습니다.|
|[Video Indexer](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview "비디오 인덱서")|Video Indexer를 사용하여 비디오에서 인사이트를 추출할 수 있습니다.|

## <a name="speech-apis"></a>Speech API

|서비스 이름|서비스 설명|
|:-----------|:------------------|
|[Speech Service](https://docs.microsoft.com/azure/cognitive-services/speech-service/ "Speech Service")|Speech Service는 애플리케이션에 음성 지원 기능을 추가합니다.|
|[Speaker Recognition API](https://docs.microsoft.com/azure/cognitive-services/speaker-recognition/home "Speaker Recognition API")(미리 보기)|Speaker Recognition API는 화자 식별 및 인증을 위한 알고리즘을 제공합니다.|
|[Bing Speech](https://docs.microsoft.com/azure/cognitive-services/speech/home "Bing Speech")(사용 중지)|Bing Speech API는 애플리케이션에서 음성 지원 기능을 만드는 편리한 방법을 제공합니다.|
|[Translator Speech](https://docs.microsoft.com/azure/cognitive-services/translator-speech/ "Translator Speech")(사용 중지)|Translator Speech는 기계 번역 서비스입니다.|

> [!NOTE]
> [Azure Cognitive Search](https://docs.microsoft.com/azure/search/)를 찾고 있나요? 일부 작업에는 Cognitive Services를 사용하지만 다른 시나리오를 지원하는 다른 검색 기술입니다.


## <a name="language-apis"></a>Language API

|서비스 이름|서비스 설명|
|:-----------|:------------------|
|[Language Understanding LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/ "언어 이해")|LUIS(Language Understanding) 서비스를 사용하면 애플리케이션에서 사람의 말을 통해 무엇을 원하는지 이해할 수 있습니다.|
|[QnA Maker](https://docs.microsoft.com/azure/cognitive-services/qnamaker/index "QnA Maker")|QnA Maker를 사용하여 반구조화된 콘텐츠로부터 질문 및 답변 서비스를 작성할 수 있습니다.|
|[텍스트 분석](https://docs.microsoft.com/azure/cognitive-services/text-analytics/ "텍스트 분석")|텍스트 분석은 감정 분석, 핵심 구 추출 및 언어 감지를 위한 원시 텍스트의 자연어 처리를 제공합니다.|
|[Translator](https://docs.microsoft.com/azure/cognitive-services/translator/ "변환기")|Translator는 거의 실시간으로 머신 기반 텍스트 번역을 제공합니다.|


## <a name="search-apis"></a>Search API

|서비스 이름|서비스 설명|
|:-----------|:------------------|
|[Bing News Search](https://docs.microsoft.com/azure/cognitive-services/bing-news-search/ "Bing 뉴스 검색")|Bing News Search는 사용자의 쿼리와 관련된 것으로 확인된 뉴스 기사 목록을 반환합니다.|
|[Bing Video Search](https://docs.microsoft.com/azure/cognitive-services/Bing-Video-Search/ "Bing 비디오 검색")|Bing Video Search는 사용자의 쿼리와 관련된 것으로 확인된 비디오 목록을 반환합니다.|
|[Bing Web Search](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/ "Bing 웹 검색")|Bing Web Search는 사용자의 쿼리와 관련된 것으로 확인된 검색 결과 목록을 반환합니다.|
|[Bing Autosuggest](https://docs.microsoft.com/azure/cognitive-services/Bing-Autosuggest "Bing Autosuggest")|Bing Autosuggest를 통해 부분 검색 쿼리 용어를 Bing에 전송하고 제안된 쿼리 목록을 다시 가져올 수 있습니다.|
|[Bing Custom Search](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search "Bing 사용자 지정 검색")|Bing Custom Search를 사용하면 관심 있는 토픽에 대한 맞춤형 검색 경험을 만들 수 있습니다.|
|[Bing Entity Search](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/ "Bing Entity Search")|Bing Entity Search는 Bing이 사용자의 쿼리와 관련되었다고 결정하는 엔터티에 대한 정보를 반환합니다.|
|[Bing Image Search](https://docs.microsoft.com/azure/cognitive-services/bing-image-search "Bing 이미지 검색")|Bing Image Search는 사용자의 쿼리와 관련된 것으로 확인된 이미지 표시를 반환합니다.|
|[Bing Visual Search](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search "Bing Visual Search")|Bing Visual Search는 시각적으로 유사한 이미지, 이미지에서 찾은 제품의 쇼핑 원본과 관련 검색 등 이미지에 대한 인사이트를 반환합니다.|
|[Bing Local Business Search](https://docs.microsoft.com/azure/cognitive-services/bing-local-business-search/ "Bing Local Business Search")| Bing Local Business Search API를 사용하면 애플리케이션에서 검색 쿼리를 기반으로 로컬 비즈니스에 대한 연락처 및 위치 정보를 찾을 수 있습니다.|
|[Bing Spell Check](https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/ "Bing 맞춤법 검사")|Bing Spell Check API를 사용하여 상황별 문법 및 맞춤법 검사를 수행할 수 있습니다.|

## <a name="decision-apis"></a>Decision API

|서비스 이름|서비스 설명|
|:-----------|:------------------|
|[Anomaly Detector](https://docs.microsoft.com/azure/cognitive-services/anomaly-detector/ "Anomaly Detector")(미리 보기)|Anomaly Detector를 사용하면 시계열 데이터에서 변칙을 모니터링하고 감지할 수 있습니다.|
|[Content Moderator](https://docs.microsoft.com/azure/cognitive-services/content-moderator/overview "Content Moderator")|Content Moderator는 불쾌감을 주거나, 원치 않거나, 위험한 콘텐츠를 모니터링하는 프로세스를 제공합니다.|
|[Metrics Advisor](https://docs.microsoft.com/azure/cognitive-services/metrics-advisor)(미리 보기) | Metrics Advisor는 다변형 시계열 데이터에 대한 사용자 지정 변칙 검색과 서비스를 사용하는 데 도움이 되는 완전한 기능을 갖춘 웹 포털을 제공합니다.
|[Personalizer](https://docs.microsoft.com/azure/cognitive-services/personalizer/ "Personalizer")|Personalizer를 사용하면 사용자의 실시간 동작을 통해 학습함으로써 사용자에게 표시할 최상의 환경을 선택할 수 있습니다.|

## <a name="learn-with-the-quickstarts"></a>빠른 시작으로 학습

다음을 사용하는 실습 빠른 시작으로 Cognitive Services 리소스를 만드는 방법에 대해 알아보세요.

* [Azure Portal](cognitive-services-apis-create-account.md?tabs=multiservice%2Cwindows "Azure portal")
* [Azure CLI](cognitive-services-apis-create-account-cli.md?tabs=windows "Azure CLI")
* [Azure SDK 클라이언트 라이브러리](cognitive-services-apis-create-account-cli.md?tabs=windows "cognitive-services-apis-create-account-client-library?pivots=programming-language-csharp")
* [ARM(Azure Resource Manager) 템플릿](resource-manager-template.md?tabs=portal "ARM(Azure Resource Manager) 템플릿")

## <a name="subscription-management"></a>구독 관리

Microsoft 계정으로 로그인하면 [내 구독](https://www.microsoft.com/cognitive-services/subscriptions "내 구독")에 액세스하여 사용하는 제품, 나머지 할당량 및 구독에 추가 제품을 추가하는 기능을 보여줄 수 있습니다.

## <a name="upgrade-to-unlock-higher-limits"></a>더 높은 제한을 잠금 해제하려면 업그레이드

모든 API에는 사용량 및 처리량 제한이 포함된 체험 계층이 있습니다.  유료 제품을 사용하고 Azure Portal에서 서비스를 배포할 때 적절한 가격 책정 계층 옵션을 선택하면 이러한 제한을 늘릴 수 있습니다. [제품 및 가격 책정에 대해 자세히 알아봅니다](https://azure.microsoft.com/pricing/details/cognitive-services/ "제품 및 가격 책정"). 신용 카드 및 전화번호로 Azure 구독자 계정을 설정해야 합니다. 특별한 요구 사항이 있거나 단지 판매에 관해 상담하려는 경우 가격 책정 페이지 상단에 있는 “문의” 단추를 클릭하세요.

## <a name="regional-availability"></a>국가별 가용성

Cognitive Services에서 API는 증가하는 Microsoft 운영 데이터 센터의 네트워크에서 호스팅됩니다. [Azure 지역 목록](https://azure.microsoft.com/regions "Azure 지역 목록")에서 각 API에 대한 지역별 가용성을 확인할 수 있습니다.

아직 지원하지 않는 지역을 찾습니까? [UserVoice 포럼](https://cognitive.uservoice.com/ "UserVoice 포럼")에서 기능 요청을 제출하여 Microsoft에 알려주세요.

## <a name="supported-cultural-languages"></a>지원되는 문화 언어

 Cognitive Services는 서비스 수준에서 광범위한 문화 언어를 지원합니다. [지원되는 언어 목록](language-support.md "지원되는 언어 목록")에서 각 API에서 사용할 수 있는 언어를 찾을 수 있습니다.

## <a name="securing-resources"></a>리소스 보안

Azure Cognitive Services는 Azure Active Directory 자격 증명을 통한 [인증](authentication.md "인증"), 유효한 리소스 키 및 [Azure Virtual Networks](cognitive-services-virtual-networks.md "Azure Virtual Networks")를 비롯한 계층화된 보안 모델을 제공합니다.

## <a name="container-support"></a>컨테이너 지원

 Cognitive Services는 Azure 클라우드 또는 온-프레미스에 배포하는 데 사용할 컨테이너를 제공합니다. [Cognitive Services 컨테이너](cognitive-services-container-support.md "Cognitive Services 컨테이너")에 대해 자세히 알아봅니다.

## <a name="certifications-and-compliance"></a>인증 및 규정 준수

Cognitive Services은 CSA STAR 인증, FedRAMP Moderate 및 HIPAA BAA와 같은 인증을 받았습니다.

해당하는 감사 및 보안 검토에 대한 인증을 [다운로드](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942 "다운로드로 사용 가능한 제품 설명서에서 데이터 공급자 설치 섹션을 참조하세요")할 수 있습니다.

개인 정보 및 데이터 관리를 이해하려면 [보안 센터](https://servicetrust.microsoft.com/ "보안 센터")로 이동하세요.

## <a name="support"></a>지원

Cognitive Services는 여러 [지원 옵션](cognitive-services-support-options.md "지원 옵션")을 제공합니다.




## <a name="next-steps"></a>다음 단계

* [Cognitive Services 계정 만들기](cognitive-services-apis-create-account.md "Cognitive Services 계정 만들기")
