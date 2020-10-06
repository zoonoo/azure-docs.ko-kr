---
title: Text Analytics API를 사용한 텍스트 마이닝 및 분석 - Azure Cognitive Services
titleSuffix: Azure Cognitive Services
description: Text Analytics API를 사용한 텍스트 마이닝에 대해 알아봅니다. 감정 분석, 언어 감지 및 기타 형태의 자연어 처리에 사용합니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 09/09/2020
ms.author: aahi
keywords: 텍스트 마이닝, 감정 분석, 텍스트 분석
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: 544de4adb1891c3d558a524466a076daefb42aa4
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/10/2020
ms.locfileid: "89647469"
---
# <a name="what-is-the-text-analytics-api"></a>텍스트 분석 API란?

Text Analytics API는 감성 분석, 오피니언 마이닝, 핵심 구 추출, 언어 감지 및 명명된 엔터티 인식을 포함하는 텍스트 마이닝 및 텍스트 분석을 위한 NLP(자연어 처리) 기능을 제공하는 클라우드 기반 서비스입니다.

이 API는 개발 프로젝트를 위한 클라우드의 기계 학습 및 AI 알고리즘 모음인 [Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/)의 일부입니다. [REST API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/) 또는 [클라이언트 라이브러리](quickstarts/text-analytics-sdk.md)에서 이러한 기능을 사용할 수 있습니다.

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Understanding-Text-using-Cognitive-Services/player]

## <a name="sentiment-analysis"></a>정서 분석

[감정 분석](how-tos/text-analytics-how-to-sentiment-analysis.md)을 사용하면 텍스트에서 긍정적 또는 부정적 감정에 대한 단서를 찾아서 사람들이 브랜드 또는 주제에 대해 어떻게 생각하는지 알아볼 수 있습니다. 이 API 기능은 각 문서에 대해 0과 1 사이의 감점 점수를 반환합니다. 여기서 1이 가장 긍정적인 것입니다.

v3.1 미리 보기부터 오피니언 마이닝은 감정 분석의 기능입니다. NLP(자연어 처리)의 양상 기반 감정 분석이라고도 하는 이 기능은 양상과 관련된 의견에 대한 더 세부적인 정보(예: 제품 또는 서비스의 특성)를 텍스트 형식으로 제공합니다.

## <a name="key-phrase-extraction"></a>핵심 문구 추출

[핵심 구 추출](how-tos/text-analytics-how-to-keyword-extraction.md)을 사용하여 텍스트의 주요 개념을 빠르게 식별합니다. 예를 들어, "음식이 맛있었고 훌륭한 직원이 있었습니다"라는 텍스트에서 핵심 구 추출은 "음식"과 "훌륭한 직원"이라는 주요 논점을 반환합니다.

## <a name="language-detection"></a>언어 검색

언어 감지는 [입력 텍스트가 작성된 언어를 감지](how-tos/text-analytics-how-to-language-detection.md)하고 광범위한 언어, 변형, 방언 및 일부 지역/문화 언어로 요청 시 제출된 모든 문서에 대해 단일 언어 코드를 보고할 수 있습니다. 언어 코드는 신뢰도 점수와 쌍을 이룹니다.

## <a name="named-entity-recognition"></a>명명된 엔터티 인식

NER(명명된 엔터티 인식)은 사람, 장소, 조직, 수량으로 텍스트의 [엔터티를 식별하고 분류](how-tos/text-analytics-how-to-entity-linking.md)할 수 있습니다. 잘 알려진 엔터티도 인식되고 웹에서 더 많은 정보에 연결됩니다.

## <a name="use-containers"></a>컨테이너 사용

[Text Analytics 컨테이너](how-tos/text-analytics-how-to-install-containers.md)를 텍스트 마이닝 및 API 사용을 위한 온-프레미스 솔루션으로 사용합니다. 이러한 Docker 컨테이너를 사용하면 핵심 구를 추출하고, 언어를 감지하고, 데이터에 더 가깝게 감정을 분석할 수 있습니다.

## <a name="typical-workflow"></a>일반적인 워크플로

간단한 워크플로: 분석을 위해 데이터를 제출하고 코드의 출력을 처리합니다. 분석기는 추가 구성 또는 사용자 지정 없이 있는 그대로 사용됩니다.

1. Text Analytics용 [Azure 리소스를 만듭니다](../cognitive-services-apis-create-account.md). 그런 다음, 요청을 인증할 수 있도록 생성된 [키를 가져옵니다](../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource).

2. 데이터를 구조화되지 않은 원시 텍스트로 포함하는 [요청을 JSON으로 구성](how-tos/text-analytics-how-to-call-api.md#json-schema)합니다.

3. 등록 동안 설정된 엔드포인트에 요청을 게시하고, 감정 분석, 핵심 구 추출, 언어 감지 또는 명명된 엔터티 인식 등, 원하는 리소스를 추가합니다.

4. 응답을 스트리밍하거나 로컬로 저장합니다. 요청에 따라, 결과는 감정 점수, 추출된 핵심 구 모음 또는 언어 코드가 됩니다.

출력은 ID를 기준으로 게시한 각 텍스트 문서에 대한 결과를 포함하는 단일 JSON 문서로 반환됩니다. 추후에 결과를 실행 가능한 정보로 분석하거나, 시각화하거나, 분류할 수 있습니다.

데이터는 계정에 저장되지 않습니다. Text Analytics API에서 수행하는 작업은 상태 비저장 작업입니다. 즉, 제공하는 텍스트가 처리된 후 결과가 즉시 반환됩니다.

## <a name="text-analytics-for-multiple-programming-experience-levels"></a>여러 프로그래밍 경험 수준을 위한 Text Analytics

프로그래밍 경험이 많지 않더라도 프로세스에서 Text Analytics API 사용을 시작할 수 있습니다. 이러한 자습서를 통해 API를 사용하여 자신의 경험 수준에 맞는 다양한 방법으로 텍스트를 분석하는 방법을 알아봅니다. 

* 필요한 최소한의 프로그래밍:
    * [Text Analytics 및 Power Automate를 사용하여 Excel에서 정보 추출](tutorials/extract-excel-information.md)
    * [Text Analytics API 및 MS Flow를 사용하여 Yammer 그룹의 코멘트에 대한 감정 식별](https://docs.microsoft.com/Yammer/integrate-yammer-with-other-apps/sentiment-analysis-flow-azure?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)
    * [Power BI를 Text Analytics API와 통합하여 고객 피드백 분석](tutorials/tutorial-power-bi-key-phrases.md)
* 권장되는 프로그래밍 환경:
    * [Azure Databricks를 사용하여 스트리밍 데이터에 대한 감정 분석](https://docs.microsoft.com/azure/azure-databricks/databricks-sentiment-analysis-cognitive-services?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)
    * [Flask를 빌드하여 텍스트 번역, 감정 분석 및 음성 합성](https://docs.microsoft.com/azure/cognitive-services/translator/tutorial-build-flask-app-translation-synthesis?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)


<a name="supported-languages"></a>

## <a name="supported-languages"></a>지원되는 언어

이 섹션은 검색 가능성을 높이기 위해 별도 문서로 이동되었습니다. 이 콘텐츠에 대해서는 [Text Analytics API의 지원되는 언어](text-analytics-supported-languages.md)를 참조하세요.

<a name="data-limits"></a>

## <a name="data-limits"></a>데이터 제한

모든 Text Analytics API 엔드포인트는 원시 텍스트 데이터를 수락합니다. 자세한 내용은 [데이터 제한](concepts/data-limits.md) 문서를 참조하세요.

## <a name="unicode-encoding"></a>유니코드 인코딩

Text Analytics API는 텍스트 표현 및 문자 수 계산에 유니코드 인코딩을 사용합니다. 측정 가능한 문자 수 차이 없이 UTF-8 및 UTF-16으로 요청을 제출할 수 있습니다. 유니코드 코드 포인트는 문자 길이에 대한 추론으로 사용되며, 텍스트 분석 데이터 제한을 위해 동일한 것으로 간주됩니다. [`StringInfo.LengthInTextElements`](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo.lengthintextelements)를 사용하여 문자 수를 확인하는 경우 데이터 크기 측정에 사용하는 것과 동일한 방법을 사용합니다.

## <a name="next-steps"></a>다음 단계

+ Text Analytics에 대한 [Azure 리소스를 생성](../cognitive-services-apis-create-account.md)하여 애플리케이션의 키와 엔드포인트를 가져옵니다.

+ [빠른 시작](quickstarts/text-analytics-sdk.md)를 사용하여 API 호출 보내기를 시작합니다. 최소의 코드로 텍스트를 제출하고, 분석을 선택하고, 결과를 보는 방법을 알아봅니다.

+ 새 릴리스 및 기능에 대한 자세한 내용은 [Text Analytics API의 새로운 기능](whats-new.md)을 참조하세요.

+ Azure Databricks를 사용하여 이 [감정 분석 자습서](https://docs.microsoft.com/azure/azure-databricks/databricks-sentiment-analysis-cognitive-services)를 좀 더 면밀히 살펴보세요.

+ [외부 및 커뮤니티 콘텐츠 페이지](text-analytics-resource-external-community.md)에서 다른 도구 및 기술과 함께 Text Analytics API를 사용하는 방법을 보여 주는 블로그 게시물 및 비디오 목록을 확인하세요.
