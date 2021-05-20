---
title: 기술 세트에 Cognitive Services 연결
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search에서 AI 보강 파이프라인에 Cognitive Services 올인원 구독을 연결하는 방법에 대해 알아봅니다.
author: LuisCabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/16/2021
ms.openlocfilehash: 77735166fafe9d39dff483baa89a4b31db31275d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100577930"
---
# <a name="attach-a-cognitive-services-resource-to-a-skillset-in-azure-cognitive-search"></a>Azure Cognitive Search에서 기술 세트에 Cognitive Services 리소스 연결

Azure Cognitive Search에서 [AI 보강 파이프라인](cognitive-search-concept-intro.md)을 구성하는 경우 제한된 수의 문서를 무료로 보강할 수 있습니다. 더 크고 더 빈번한 워크로드의 경우 청구 가능한 "올인원" Cognitive Services 리소스를 연결해야 합니다. "올인원" 구독은 단일 API 키를 통해 액세스 권한이 부여된 개별 서비스 대신 제품으로 "Cognitive Services"를 참조합니다.

"올인원" Cognitive Services 리소스는 기술 세트에 포함할 수 있는 [미리 정의된 기술](cognitive-search-predefined-skills.md)을 구동합니다.

+ 이미지 분석 및 OCR(광학 인식)을 위한 [Computer Vision](https://azure.microsoft.com/services/cognitive-services/computer-vision/)
+ 언어 검색, 엔터티 인식, 감정 분석 및 핵심 구 추출을 위한 [Text Analytics](https://azure.microsoft.com/services/cognitive-services/text-analytics/)
+ [텍스트 번역](https://azure.microsoft.com/services/cognitive-services/translator-text-api/)

기술 세트 정의에서 "올인원" Cognitive Services 키는 선택 사항입니다. 일일 트랜잭션 수가 하루에 20개 미만이면 비용이 흡수됩니다. 그러나 트랜잭션이 이 수를 초과하는 경우 처리를 계속하려면 유효한 리소스 키가 필요합니다.

모든 "올인원" 리소스 키가 유효합니다. 내부적으로 검색 서비스는 "올인원" 키가 다른 지역의 리소스에 대한 키인 경우에도 동일한 물리적 지역에 함께 배치된 리소스를 사용합니다. [제품 가용성](https://azure.microsoft.com/global-infrastructure/services/?products=search) 페이지에는 국가별 가용성이 나란히 표시됩니다.

> [!NOTE]
> 기술 세트에서 미리 정의된 기술을 생략하면 Cognitive Services에 액세스할 수 없으며 기술 세트가 키를 지정하는 경우에도 요금이 부과되지 않습니다.

## <a name="how-billing-works"></a>청구 방법

+ Azure Cognitive Search는 기술 세트에서 제공하는 Cognitive Services 리소스 키를 사용하여 이미지 및 텍스트 보강에 대한 요금을 청구합니다. 청구 가능 기술 세트를 실행하는 요금은 [Cognitive Services 종량제 가격](https://azure.microsoft.com/pricing/details/cognitive-services/)으로 청구됩니다.

+ 이미지 추출은 보강 전에 문서가 깨진 경우 발생하는 Azure Cognitive Search 작업입니다. 이미지 추출은 청구 가능합니다. 이미지 추출 가격 책정은 [Azure Cognitive Search 가격 책정 페이지](https://azure.microsoft.com/pricing/details/search/)를 참조하세요.

+ 텍스트 추출은 문서 크래킹 구문 중에도 발생합니다. 청구되지 않습니다.

+ Conditional, Shaper, Text Merge 및 Text Split 기술 세트를 포함하여 Cognitive Services를 호출하지 않는 기술은 청구되지 않습니다.

## <a name="same-region-requirement"></a>동일한 지역 요구 사항

Cognitive Search와 Cognitive Services는 모두 [제품 가용성](https://azure.microsoft.com/global-infrastructure/services/?products=search) 페이지에 표시된 것처럼 동일한 물리적 지역 내에 있어야 합니다. Cognitive Search를 제공하는 대부분의 지역은 Cognitive Services를 제공합니다.

두 서비스가 없는 지역에서 AI 보강을 시도하면 "제공된 키가 검색 서비스 지역에 대한 올바른 CognitiveServices 유형 키가 아닙니다."라는 메시지가 표시됩니다.

> [!NOTE]
> 일부 기본 제공 기술은 비 지역별 Cognitive Services(예: [텍스트 번역 기술](cognitive-search-skill-text-translation.md))를 기반으로 합니다. 비 지역별 기술을 사용하는 것은 Azure Cognitive Search 지역이 아닌 다른 지역에서 요청을 처리할 수 있음을 의미합니다. 비 지역별 서비스에 대한 자세한 내용은 [지역별 Cognitive Services 제품](https://aka.ms/allinoneregioninfo) 페이지를 참조하세요.

## <a name="use-free-resources"></a>무료 리소스 사용

제한된 무료 처리 옵션을 사용하여 AI 보강 자습서 및 빠른 시작 연습을 완료할 수 있습니다.

무료(제한적 보강) 리소스는 인덱서당 하루 20개의 문서로 제한됩니다. [인덱서를 초기화](search-howto-run-reset-indexers.md)하여 카운터를 초기화할 수 있습니다.

AI 보강에 대해 **데이터 가져오기** 마법사를 사용하는 경우 **AI 보강 추가(선택사항)** 페이지에서 "Cognitive Services 연결" 옵션을 찾을 수 있습니다.

![확장된 Cognitive Services 연결 섹션](./media/cognitive-search-attach-cognitive-services/attach1.png "확장된 Cognitive Services 연결 섹션")

## <a name="use-billable-resources"></a>유료 리소스 사용

하루에 20개보다 많은 보강을 만드는 작업의 경우 청구 가능 Cognitive Services 리소스를 연결해야 합니다. Cognitive Services API를 호출하지 않으려는 경우에도 항상 청구 가능 Cognitive Services 리소스를 연결하는 것이 좋습니다. 리소스를 연결하면 일일 한도가 재정의됩니다.

Cognitive Services API를 호출하는 기술에 대해서만 요금이 청구됩니다. [사용자 지정 기술](cognitive-search-create-custom-skill-example.md) 또는 API 기반이 아닌 [텍스트 병합기](cognitive-search-skill-textmerger.md), [텍스트 분할자](cognitive-search-skill-textsplit.md) 및 [쉐이퍼](cognitive-search-skill-shaper.md)와 같은 기술에 대한 요금이 청구 되지 않습니다.

**데이터 가져오기** 마법사를 사용하는 경우 **AI 보강 추가(선택 사항)** 페이지에서 청구 가능 리소스를 구성할 수 있습니다.

1. **Cognitive Services 연결** 을 확장한 다음, **새 Cognitive Services 리소스 만들기** 를 선택합니다. 리소스를 만들 수 있도록 새 탭이 열립니다.

   ![Cognitive Services 리소스 만들기](./media/cognitive-search-attach-cognitive-services/cog-services-create.png "Cognitive Services 리소스 만들기")

1. **위치** 목록에서 검색 서비스가 있는 동일한 지역을 선택합니다.

1. **가격 책정 계층** 목록에서 **S0** 를 선택하여 Azure Cognitive Search에서 제공하는 기본 제공 기술을 지원하는 비전 및 언어 기능을 포함하여 Cognitive Services 기능의 올인원 컬렉션을 가져옵니다.

   S0 계층의 경우 [Cognitive Services 가격 책정 페이지](https://azure.microsoft.com/pricing/details/cognitive-services/)에서 특정 작업에 대한 요금을 확인할 수 있습니다.
  
   + **제품 선택** 목록에서 **Cognitive Services** 가 선택되어 있는지 확인합니다.
   + **언어** 기능에서 **Text Analytics 표준** 에 대한 요금은 AI 인덱싱에 적용됩니다.
   + **비전** 기능에서 **Computer Vision S1** 에 대한 요금이 적용됩니다.

1. **만들기** 를 선택하여 새 Cognitive Services 리소스를 프로비전합니다.

1. 이전 탭으로 돌아갑니다. **새로 고침** 을 선택하여 Cognitive Services 리소스를 표시한 다음, 리소스를 선택합니다.

   ![Cognitive Services 리소스 선택](./media/cognitive-search-attach-cognitive-services/attach2.png "Cognitive Services 리소스 선택")

1. **인식 기술 추가** 섹션을 확장하여 데이터를 실행하려는 특정 인식 기술을 선택합니다. 마법사의 나머지를 완료합니다.

## <a name="attach-an-existing-skillset-to-a-cognitive-services-resource"></a>Cognitive Services 리소스에 기존 기술 연결

기존 기술이 있으면 새 Cognitive Services 리소스나 다른 Cognitive Services 리소스에 연결할 수 있습니다.

1. 검색 서비스 개요 페이지에서 **기술 세트** 를 선택합니다.

   ![기술 세트 탭](./media/cognitive-search-attach-cognitive-services/attach-existing1.png "기술 세트 탭")

1. 기술 세트의 이름을 선택한 다음, 기존 리소스를 선택하거나 새 리소스를 만듭니다. **확인** 을 선택하여 변경 내용을 확인합니다.

   ![기술 세트 리소스 목록](./media/cognitive-search-attach-cognitive-services/attach-existing2.png "기술 세트 리소스 목록")

   **무료(제한적 보강)** 옵션은 매일 20개의 문서를 제한하고, **새 Cognitive Services 리소스 만들기** 를 사용하여 새로운 청구 가능 리소스를 프로비전할 수 있습니다. 새 리소스를 만든 경우 **새로 고침** 을 선택하여 Cognitive Services 리소스 목록을 새로 고친 다음, 리소스를 선택합니다.

## <a name="attach-cognitive-services-programmatically"></a>프로그래밍 방식으로 Cognitive Services 연결

프로그래밍 방식으로 기술을 정의하는 경우 `cognitiveServices` 섹션을 기술에 추가합니다. 해당 세션에서 기술 세트와 연결할 Cognitive Services 리소스의 키를 포함합니다. 리소스는 Azure Cognitive Search 리소스와 동일한 지역에 있어야 합니다. 또한 `@odata.type`을 포함하고, 이를 `#Microsoft.Azure.Search.CognitiveServicesByKey`로 설정하세요.

다음 예제는 이러한 패턴을 보여줍니다. 정의의 맨 끝에 있는 `cognitiveServices` 섹션을 참조하세요.

```http
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2020-06-30
api-key: [admin key]
Content-Type: application/json
{
    "name": "skillset name",
    "skills": 
    [
      {
        "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
        "categories": [ "Organization" ],
        "defaultLanguageCode": "en",
        "inputs": [
          {
            "name": "text", "source": "/document/content"
          }
        ],
        "outputs": [
          {
            "name": "organizations", "targetName": "organizations"
          }
        ]
      }
    ],
    "cognitiveServices": {
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
        "description": "mycogsvcs",
        "key": "<your key goes here>"
    }
}
```

## <a name="example-estimate-costs"></a>예제: 비용 추정

인식 검색 인덱싱과 관련된 비용을 추정하려면, 평균 문서가 어떤 모양인지부터 생각하여 몇 가지 숫자를 실행할 수 있도록 합니다. 예를 들어 다음과 같을 수 있습니다.

+ PDF 파일 1,000개
+ 각 6페이지
+ 페이지당 이미지 하나(이미지 6,000개)
+ 페이지당 3,000자

각 PDF의 문서 크래킹, 이미지 및 텍스트 추출, 이미지의 OCR(광학 문자 인식) 및 조직의 엔터티 인식으로 구성된 파이프라인이 있다고 가정합니다.

이 문서에 표시된 가격은 가상입니다. 예측 프로세스를 설명하는 데 사용됩니다. 비용은 낮아질 수 있습니다. 트랜잭션의 실제 가격은 [Cognitive Services 가격 책정](https://azure.microsoft.com/pricing/details/cognitive-services)을 참조하세요.

1. 텍스트 및 이미지 콘텐츠로 문서 해독의 경우, 텍스트 추출이 현재 무료입니다. 6,000개 이미지의 경우 추출된 모든 1,000개 이미지에 대해 $1를 가정합니다. 이 단계의 비용은 $6.00입니다.

2. 영어로 된 6,000개의 이미지 OCR의 경우, OCR 인지 기술은 최적의 알고리즘(DescribeText)을 사용합니다. 분석할 1,000개 이미지당 비용이 $2.50라고 가정할 경우 이 단계를 위해 $15.00를 지불합니다.

3. 엔터티 추출의 경우 페이지당 총 3개의 텍스트 레코드가 있습니다. 각 레코드는 1,000자입니다. 페이지당 3개의 텍스트 레코드* 6,000장의 페이지 = 18,000개의 텍스트 레코드입니다. 1,000개의 텍스트 레코드당 $2.00라고 가정할 경우 이 단계의 비용은 $36.00입니다.

비용을 모두 합하면, 설명한 기술 세트를 통해 이러한 형식의 PDF 문서 1,000개를 수집하는 데 $57.00 정도를 지불합니다.

## <a name="next-steps"></a>다음 단계

+ [Azure Cognitive Search 가격 책정 페이지](https://azure.microsoft.com/pricing/details/search/)
+ [기술 집합을 정의하는 방법](cognitive-search-defining-skillset.md)
+ [기술 집합 만들기(REST)](/rest/api/searchservice/create-skillset)
+ [보강 필드를 매핑하는 방법](cognitive-search-output-field-mapping.md)