---
title: 기술과 Cognitive Services 리소스 연결 - Azure Search
description: Azure Search에서 인지 보강 파이프라인에 Cognitive Services 올인원 구독을 연결하는 방법에 대한 지침입니다.
manager: cgronlun
author: LuisCabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: bad64f439d45581f8f4b55ea1ac849db1e27cb76
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65024594"
---
# <a name="attach-a-cognitive-services-resource-with-a-skillset-in-azure-search"></a>Azure Search에서 기술과 Cognitive Services 리소스 연결 

AI 알고리즘 드라이브는 [cognitive 인덱싱 파이프라인](cognitive-search-concept-intro.md) Azure Search의 구조화 되지 않은 데이터 처리에 사용 합니다. 이러한 알고리즘은 이미지 분석 및 OCR(광학 문자 인식)을 위한 [Computer Vision](https://azure.microsoft.com/services/cognitive-services/computer-vision/)과 엔터티 인식, 핵심 구 추출 및 기타 강화를 위한 [Text Analytics](https://azure.microsoft.com/services/cognitive-services/text-analytics/)를 비롯한 [Cognitive Services 리소스](https://azure.microsoft.com/services/cognitive-services/)를 기준으로 합니다.

제한된 수의 문서를 보강하거나 크고 빈번한 워크로드에 대해 유료 Cognitive Services 리소스를 연결할 수 있습니다. 이 문서에서는 Cognitive Services 리소스를 인지 기술에 연결하여 [Azure Search 인덱싱](search-what-is-an-index.md) 중에 데이터를 보강하는 방법을 알아봅니다.

파이프라인이 Cognitive Services API와 관련이 없는 기술로 구성된 경우 여전히 Cognitive Services 리소스를 연결해야 합니다. 이렇게 하면 **무료** 리소스가 재정의되어 일별 보강 횟수가 낮게 제한됩니다. Cognitive Services API에 바인딩되지 않은 기술의 경우 요금이 부과되지 않습니다. 이러한 기술에는 [사용자 지정 기술](cognitive-search-create-custom-skill-example.md), [텍스트 병합기](cognitive-search-skill-textmerger.md), [텍스트 분할자](cognitive-search-skill-textsplit.md) 및 [쉐이퍼](cognitive-search-skill-shaper.md)가 포함됩니다.

> [!NOTE]
> 처리 빈도를 늘리거나 문서를 추가하거나 AI 알고리즘을 추가하여 범위를 확장할 때는 청구 가능 Cognitive Services 리소스를 연결해야 합니다. Cognitive Services에서 API를 호출할 때와 Azure Search에서 문서 해독 단계의 일부로 이미지를 추출할 때는 요금이 누적됩니다. 문서에서 텍스트 추출할 때는 요금이 발생하지 않습니다.
>
> 기본 제공 기술 실행은 기존 부과 [종 량 Cognitive Services 가격 이동](https://azure.microsoft.com/pricing/details/cognitive-services/)합니다. 에 설명 된 대로 이미지 추출 가격을 [Azure Search 가격 책정 페이지](https://go.microsoft.com/fwlink/?linkid=2042400)합니다.

## <a name="use-free-resources"></a>무료 리소스 사용

제한된 무료 처리 옵션을 사용하여 인지 검색 자습서 및 빠른 시작 연습을 완료할 수 있습니다. 

**(제한 된 강화) 무료** 구독 당 하루 20 문서로 제한 됩니다.

1. **데이터 가져오기** 마법사를 엽니다.

   ![데이터 가져오기 명령](media/search-get-started-portal/import-data-cmd2.png "데이터 가져오기 명령")

1. 데이터 원본을 선택하고 **인지 검색 추가(선택 사항)** 를 선택합니다. 이 마법사에 대한 단계별 연습은 [포털 도구를 사용하여 가져오기, 인덱싱 및 쿼리](search-get-started-portal.md)를 참조하세요.

1. **Cognitive Services 연결**을 확장하고 **무료(제한적 보강)** 을 선택합니다.

   ![확장된 Cognitive Services 연결 섹션](./media/cognitive-search-attach-cognitive-services/attach1.png "확장된 Cognitive Services 연결")

다음 단계, **보강 추가**를 진행합니다. 포털에서 사용할 수 있는 기술에 대한 설명은 인지 검색 빠른 시작의 ["2단계: 인지 기술 추가"](cognitive-search-quickstart-blob.md#create-the-enrichment-pipeline)를 참조하세요.

## <a name="use-billable-resources"></a>유료 리소스 사용

매일 20개를 초과하는 보강에 번호를 매기는 워크로드에는 유료 Cognitive Services 리소스를 연결해야 필요합니다. 

Cognitive Services API를 호출하는 기술에 대해서만 요금이 청구됩니다. 와 같은 비 API 기반 기술을 [사용자 지정 기술](cognitive-search-create-custom-skill-example.md), [텍스트 병합기](cognitive-search-skill-textmerger.md), [텍스트 분할자](cognitive-search-skill-textsplit.md) 및 [쉐이퍼](cognitive-search-skill-shaper.md) 기술과 같은 비 API 기반 기술에는 요금이 청구되지 않습니다.

1. 엽니다는 **데이터 가져오기** 마법사는 데이터 원본을 선택 하 고 계속 해 서 **cognitive search 추가 (선택 사항)** 합니다. 

1. 확장 **Cognitive Services 연결** 선택한 후 **새 Cognitive Services 리소스 만들기**합니다. 리소스를 만들 수 있도록 새 탭이 열립니다. 

   ![Cognitive Services 리소스 만들기](./media/cognitive-search-attach-cognitive-services/cog-services-create.png "Cognitive Services 리소스 만들기")

1. 위치에 지역 간 아웃 바운드 대역폭 요금을 방지 하려면 Azure Search와 같은 영역을 선택 합니다.

1. 가격 책정 계층 선택 **S0** Cognitive Services 기능을 Azure Search에서 사용 하는 미리 정의 된 기술을 다시 비전 및 언어 기능을 포함 하 여 모든 통합 된 단일 컬렉션을 가져옵니다. 

   S0 계층의 경우에서 찾을 수 있습니다 속도 특정 워크 로드에 대 한 합니다 [Cognitive Services 가격 페이지](https://azure.microsoft.com/pricing/details/cognitive-services/)합니다.
  
   + **제공 선택**, 했는지 *Cognitive Services* 을 선택 합니다.
   + 언어 기능에 대 한 요금 아래의 *텍스트 분석 표준* AI 인덱싱에 적용 합니다. 
   + 비전 기능에 대 한 요금 아래의 *컴퓨터 비전 S1* 적용 됩니다.

1. **만들기**를 클릭하여 새 Cognitive Services 리소스를 프로비전합니다. 

1. **데이터 가져오기** 마법사가 포함된 이전 탭으로 돌아갑니다. **새로 고침**을 클릭하여 Cognitive Services 리소스를 표시한 다음, 리소스를 선택합니다.

   ![선택한 Cognitive Service 리소스](./media/cognitive-search-attach-cognitive-services/attach2.png "선택한 Cognitive Service 리소스")

1. **보강 추가** 섹션을 확장하여 데이터에서 실행할 특정 인식 기술을 선택하고 나머지 흐름을 계속 진행할 수 있습니다. 포털에서 사용할 수 있는 기술에 대한 설명은 인지 검색 빠른 시작의 ["2단계: 인지 기술 추가"](cognitive-search-quickstart-blob.md#create-the-enrichment-pipeline)를 참조하세요.

## <a name="attach-an-existing-skillset-to-a-cognitive-services-resource"></a>Cognitive Services 리소스에 기존 기술 연결

기존 기술이 있으면 새 Cognitive Services 리소스나 다른 Cognitive Services 리소스에 연결할 수 있습니다.

1. **서비스 개요** 페이지에서 **기술**을 선택합니다.

   ![기술 탭](./media/cognitive-search-attach-cognitive-services/attach-existing1.png "기술 탭")

1. 기술 이름을 클릭한 다음, 기존 리소스를 선택하거나 새 리소스를 만듭니다. **확인**을 클릭하여 변경 내용을 확인합니다. 

   ![기술 리소스 목록](./media/cognitive-search-attach-cognitive-services/attach-existing2.png "기술 리소스 목록")

**무료(제한적 보강)** 는 매일 20개 문서로 제한되며 **새 Cognitive Services 리소스 만들기**는 새로운 유료 리소스를 프로비전하는 데 사용된다는 것을 기억하십시오. 새 리소스를 만든 경우 **새로 고침**을 선택하여 Cognitive Services 리소스 목록을 새로 고친 다음, 리소스를 선택합니다.

## <a name="attach-cognitive-services-programmatically"></a>프로그래밍 방식으로 Cognitive Services 연결

프로그래밍 방식으로 기술을 정의하는 경우 `cognitiveServices` 섹션을 기술에 추가합니다. 섹션에는 기술과 연결할 Cognitive Services 리소스의 키가 포함됩니다. 리소스는 Azure Search와 동일한 지역에 있어야 한다는 것을 기억하십시오. 또한 `@odata.type`을 포함하고, 이를 `#Microsoft.Azure.Search.CognitiveServicesByKey`로 설정하세요. 

다음 예제는 이러한 패턴을 보여줍니다. 정의 맨 아래에 있는 cognitiveServices 섹션에 유의하세요.

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

## <a name="example-estimate-costs"></a>예제: 비용 추정

인지 검색 인덱싱과 관련된 비용을 추정하려면, 평균 문서가 어떤 모양인지부터 생각하여 몇 가지 숫자를 제공할 수 있도록 합니다. 예를 들어, 예측을 위해 아래와 같은 근사치를 사용할 수 있습니다.

+ PDF 파일 1000개
+ 각 6페이지
+ 페이지당 이미지 하나(이미지 6000개)
+ 페이지당 3000자

이미지 및 텍스트 추출, OCR (광학 문자 인식) 이미지를 사용 하 여 각 PDF 문서 해독 및 조직의 엔터티 인식으로 구성 된 파이프라인을 가정 합니다. 

이 연습에서는 트랜잭션당 가장 비싼 가격을 사용합니다. 실제 가격은 누진 가격 책정으로 인해 낮아질 수 있습니다. [Cognitive Services 가격 책정](https://azure.microsoft.com/pricing/details/cognitive-services)을 참조하세요.

1. 텍스트 및 이미지 콘텐츠로 문서 해독의 경우, 텍스트 추출이 현재 무료입니다. 6,000개의 이미지는 1,000개의 이미지를 추출할 때마다 $1라고 추정하면 이 단계에 $6.00의 비용이 소요됩니다.

2. 영어로 된 6,000개의 이미지 OCR의 경우, OCR 인지 기술은 최적의 알고리즘(DescribeText)을 사용합니다. 분석할 1,000개 이미지당 비용이 $2.50라고 가정할 경우 이 단계를 위해 $15.00를 지불합니다.

3. 엔터티 추출의 경우 페이지당 총 3개의 텍스트 레코드가 있습니다. 각 레코드는 1,000자입니다. 페이지당 3개의 텍스트 레코드* 6,000장의 페이지 = 18,000개의 텍스트 레코드입니다. 1,000개의 텍스트 레코드당 $2.00라고 가정할 경우 이 단계의 비용은 $36.00입니다.

비용을 모두 합하면, 설명한 기술을 통해 이러한 특성의 PDF 문서 1,000개를 수집하는 데 $57.00 정도를 지불합니다. 

## <a name="next-steps"></a>다음 단계
+ [Azure Search 가격 책정 페이지](https://azure.microsoft.com/pricing/details/search/)
+ [기술 집합을 정의하는 방법](cognitive-search-defining-skillset.md)
+ [기능 만들기(REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [보강 필드를 매핑하는 방법](cognitive-search-output-field-mapping.md)
