---
title: 기술 집합에 인지 서비스 연결
titleSuffix: Azure Cognitive Search
description: Azure 인지 검색의 AI 보강 파이프라인에 코그너티브 서비스 올인원 구독을 첨부하기 위한 지침입니다.
manager: nitinme
author: LuisCabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: 254c912114e3f1c7a495f389bc6a6416cbde7e11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77472454"
---
# <a name="attach-a-cognitive-services-resource-to-a-skillset-in-azure-cognitive-search"></a>Azure 인지 검색의 기술 집합에 인지 서비스 리소스 연결 

Azure Cognitive Search에서 보강 파이프라인을 구성할 때 제한된 수의 문서를 무료로 보강할 수 있습니다. 더 크고 빈번한 워크로드의 경우 청구 가능한 인지 서비스 리소스를 첨부해야 합니다.

이 문서에서는 보강 파이프라인을 정의하는 기술 집합에 키를 할당하여 리소스를 연결하는 방법을 알아봅니다.

## <a name="resources-used-during-enrichment"></a>농축 중에 사용되는 리소스

Azure Cognitive Search는 이미지 분석 및 광학 문자 인식(OCR), 자연어 처리를 위한 [텍스트 분석](https://azure.microsoft.com/services/cognitive-services/text-analytics/) 및 [텍스트 번역과](https://azure.microsoft.com/services/cognitive-services/translator-text-api/)같은 기타 보강을 위한 [컴퓨터 비전을](https://azure.microsoft.com/services/cognitive-services/computer-vision/) 비롯한 코그너티브 서비스에 대한 의존성을 가지고 있습니다. Azure Cognitive Search의 보강 컨텍스트에서 이러한 AI 알고리즘은 *기술*내에 래핑되고 *기술 집합에*배치되며 인덱싱 중에 *인덱서에서* 참조합니다.

## <a name="how-billing-works"></a>청구 방법

+ Azure Cognitive Search는 기술 집합에서 제공하는 인지 서비스 리소스 키를 사용하여 이미지 및 텍스트 보강에 대한 요금을 청구합니다. 청구 가능한 기술의 실행은 [인지 서비스 종량제 가격입니다.](https://azure.microsoft.com/pricing/details/cognitive-services/)

+ 이미지 추출은 보강 전에 문서가 해독될 때 발생하는 Azure 인지 검색 작업입니다. 이미지 추출은 청구할 수 있습니다. 이미지 추출 가격 책정은 [Azure 인지 검색 가격 페이지를](https://go.microsoft.com/fwlink/?linkid=2042400)참조하십시오.

+ 문서 크래킹 구 중에 텍스트 추출도 발생합니다. 청구할 수 없습니다.

+ 조건부, 셰이퍼, 텍스트 병합 및 텍스트 분할 기술을 포함하여 인지 서비스를 호출하지 않는 기술은 청구할 수 없습니다.

## <a name="same-region-requirement"></a>동일한 지역 요구 사항

Azure 인지 검색 및 Azure 인지 서비스가 동일한 리전 내에 존재해야 합니다. 그렇지 않으면 런타임에 이 메시지가 표시됩니다.`"Provided key is not a valid CognitiveServices type key for the region of your search service."` 

여러 리전간에 서비스를 이동할 수 있는 방법은 없습니다. 이 오류가 발생하면 Azure Cognitive Search와 동일한 지역에 새 코그너티브 서비스 리소스를 만들어야 합니다.

> [!NOTE]
> 일부 기본 제공 기술은 비지역 인지 서비스(예: [텍스트 번역 기술)를](cognitive-search-skill-text-translation.md)기반으로 합니다. 비지역 기술을 사용하면 Azure 인지 검색 영역이 아닌 다른 지역에서 요청이 서비스될 수 있습니다. 비지역 서비스에 대한 자세한 내용은 [지역별 코그너티브 서비스 제품을](https://aka.ms/allinoneregioninfo) 참조하세요.

## <a name="use-free-resources"></a>무료 리소스 사용

제한된 무료 처리 옵션을 사용하여 AI 강화 자습서 및 빠른 시작 연습을 완료할 수 있습니다.

무료(제한된 보강) 리소스는 인덱서당 하루에 20개의 문서로 제한됩니다. 인덱서를 삭제하고 다시 만들어 카운터를 재설정할 수 있습니다.

1. 데이터 가져오기 마법사 열기:

   ![데이터 가져오기 마법사 열기](media/search-get-started-portal/import-data-cmd.png "데이터 가져오기 마법사 열기")

1. 데이터 원본을 선택하고 **AI 보강(선택 사항)을**계속 추가합니다. 이 마법사의 단계별 연습은 [Azure 포털의 인덱스 만들기를](search-get-started-portal.md)참조하십시오.

1. **인지 서비스를 연결** 확장 한 다음 무료 **(제한된 농축)를 선택하십시오**.

   ![확장 된 연결 인지 서비스 섹션](./media/cognitive-search-attach-cognitive-services/attach1.png "확장 된 연결 인지 서비스 섹션")

1. 이제 **인지 능력 추가를**포함하여 다음 단계로 계속할 수 있습니다.

## <a name="use-billable-resources"></a>유료 리소스 사용

하루에 20개 이상의 보강을 생성하는 워크로드의 경우 청구 가능한 인지 서비스 리소스를 첨부해야 합니다. 인지 서비스 API를 호출하지 않더라도 항상 청구 가능한 인지 서비스 리소스를 첨부하는 것이 좋습니다. 리소스를 연결하면 일일 제한이 재정의됩니다.

코그너티브 서비스 API를 호출하는 기술에 대해서만 요금이 부과됩니다. API 기반이 아닌 [사용자 지정 기술](cognitive-search-create-custom-skill-example.md)또는 [텍스트 분할,](cognitive-search-skill-textmerger.md) [텍스트 분할 및](cognitive-search-skill-textsplit.md) [셰이퍼와](cognitive-search-skill-shaper.md)같은 기술에 대한 요금이 청구되지 않습니다.

1. 데이터 가져오기 마법사를 열고 데이터 원본을 선택한 다음 **AI 보강(선택 사항)을**계속 추가합니다.

1. **인지 서비스 연결 확장한** 다음 새 인지 서비스 리소스 **만들기를**선택합니다. 리소스를 만들 수 있도록 새 탭이 열립니다.

   ![Cognitive Services 리소스 만들기](./media/cognitive-search-attach-cognitive-services/cog-services-create.png "Cognitive Services 리소스 만들기")

1. **위치** 목록에서 Azure 인지 검색 서비스가 있는 지역을 선택합니다. 성능상의 이유로 이 리전을 사용해야 합니다. 또한 이 리전을 사용하면 리전 전체에서 아웃바운드 대역폭 요금이 청구됩니다.

1. 가격 **책정 계층** 목록에서 **S0을** 선택하여 Azure Cognitive Search에서 제공하는 기본 제공 기술을 뒷받침하는 비전 및 언어 기능을 비롯한 코그너티브 서비스 기능의 올인원 컬렉션을 가져옵니다.

   S0 계층의 경우 [코그너티브 서비스 가격 페이지에서](https://azure.microsoft.com/pricing/details/cognitive-services/)특정 워크로드에 대한 요금을 찾을 수 있습니다.
  
   + 제안 **선택** 목록에서 인지 **서비스가** 선택되어 있는지 확인합니다.
   + **언어** 기능에서 텍스트 **분석 표준의** 요금은 AI 인덱싱에 적용됩니다.
   + **비전** 기능에서 컴퓨터 **비전 S1** 요금이 적용됩니다.

1. 새 코그너티브 서비스 리소스를 프로비전하려면 **만들기를** 선택합니다.

1. 데이터 가져오기 마법사가 포함된 이전 탭으로 돌아갑니다. **새로 고침을** 선택하여 인지 서비스 리소스를 표시한 다음 리소스를 선택합니다.

   ![코그너티브 서비스 리소스 선택](./media/cognitive-search-attach-cognitive-services/attach2.png "코그너티브 서비스 리소스 선택")

1. 인지 **능력 추가** 섹션을 확장하여 데이터에서 실행하려는 특정 인지 능력을 선택합니다. 마법사의 나머지 부분을 완료합니다.

## <a name="attach-an-existing-skillset-to-a-cognitive-services-resource"></a>Cognitive Services 리소스에 기존 기술 연결

기존 기술이 있으면 새 Cognitive Services 리소스나 다른 Cognitive Services 리소스에 연결할 수 있습니다.

1. 서비스 **개요** 페이지에서 기술 **집합을**선택합니다.

   ![기술 세트 탭](./media/cognitive-search-attach-cognitive-services/attach-existing1.png "기술 세트 탭")

1. 기술 집합의 이름을 선택한 다음 기존 리소스를 선택하거나 새 리소스를 만듭니다. **확인**을 선택하여 변경 내용을 확인합니다.

   ![기술 집합 리소스 목록](./media/cognitive-search-attach-cognitive-services/attach-existing2.png "기술 집합 리소스 목록")

   **무료(제한된 보강)** 옵션은 매일 20개의 문서로 제한되며 **새 인지 서비스 만들기 리소스를** 사용하여 새 청구 가능한 리소스를 프로비저닝할 수 있습니다. 새 리소스를 만든 경우 **새로 고침**을 선택하여 Cognitive Services 리소스 목록을 새로 고친 다음, 리소스를 선택합니다.

## <a name="attach-cognitive-services-programmatically"></a>프로그래밍 방식으로 Cognitive Services 연결

프로그래밍 방식으로 기술을 정의하는 경우 `cognitiveServices` 섹션을 기술에 추가합니다. 이 섹션에는 기술 집합과 연결하려는 인지 서비스 리소스의 키를 포함합니다. 리소스는 Azure 인지 검색 리소스와 동일한 지역에 있어야 합니다. 또한 `@odata.type`을 포함하고, 이를 `#Microsoft.Azure.Search.CognitiveServicesByKey`로 설정하세요.

다음 예제는 이러한 패턴을 보여줍니다. 정의 `cognitiveServices` 끝에 있는 섹션을 확인합니다.

```http
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2019-05-06
api-key: [admin key]
Content-Type: application/json
```
```json
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

## <a name="example-estimate-costs"></a>예: 예상 비용

인지 검색 인덱싱과 관련된 비용을 추정하려면 일부 숫자를 실행할 수 있도록 평균 문서의 모양에 대한 아이디어로 시작합니다. 예를 들어 다음과 같은 대략적인 경우를 확인할 수 있습니다.

+ 1,000PDF.
+ 각각 6페이지.
+ 페이지당 하나의 이미지(6,000개 이미지)
+ 페이지당 3,000자.

각 PDF의 문서 크래킹, 이미지 및 텍스트 추출, 이미지의 광학 문자 인식(OCR) 및 조직의 엔터티 인식으로 구성된 파이프라인을 가정합니다.

이 문서에 표시된 가격은 가설입니다. 추정 프로세스를 설명하는 데 사용됩니다. 비용은 더 낮을 수 있습니다. 실제 거래 가격은 [코그너티브 서비스 가격](https://azure.microsoft.com/pricing/details/cognitive-services)을 참조하십시오.

1. 텍스트 및 이미지 콘텐츠로 문서 해독의 경우, 텍스트 추출이 현재 무료입니다. 6,000개의 이미지의 경우 추출된 이미지 1,000개당 1달러를 가정합니다. 이 단계의 비용은 $6.00입니다.

2. 영어로 된 6,000개의 이미지 OCR의 경우, OCR 인지 기술은 최적의 알고리즘(DescribeText)을 사용합니다. 분석할 1,000개 이미지당 비용이 $2.50라고 가정할 경우 이 단계를 위해 $15.00를 지불합니다.

3. 엔터티 추출의 경우 페이지당 총 3개의 텍스트 레코드가 있어야 합니다. 각 레코드는 1,000자입니다. 페이지당 3개의 텍스트 레코드에 6,000페이지를 곱하면 18,000개의 텍스트 레코드와 같습니다. 1,000개의 텍스트 레코드당 $2.00라고 가정할 경우 이 단계의 비용은 $36.00입니다.

이 모든 것을 종합하면 설명된 기술 집합으로 이 유형의 PDF 문서 1,000건을 섭취하기 위해 약 57.00달러를 지불하게 됩니다.

## <a name="next-steps"></a>다음 단계
+ [Azure 인지 검색 가격 책정 페이지](https://azure.microsoft.com/pricing/details/search/)
+ [기술 집합을 정의하는 방법](cognitive-search-defining-skillset.md)
+ [기술 집합 만들기(REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [보강 필드를 매핑하는 방법](cognitive-search-output-field-mapping.md)
