---
title: 기술에 Cognitive Services 연결 - Azure Search
description: 인식 기술에 Cognitive Services All-in-One 구독 연결에 대한 지침
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 12/05/2018
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 7f604d1b94e51db11e6d6992b7f070d64ae869dd
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53317239"
---
# <a name="associate-cognitive-services-resource-with-a-skillset"></a>기술과 Cognitive Services 리소스 연결 

> [!NOTE]
> 2018년 12월 21일부터 Cognitive Services 리소스를 Azure Search 기술과 연결할 수 있습니다. 이를 통해 Microsoft는 기술 실행 요금을 부과할 수 있습니다. 또한 문서 해독 단계의 일부로 이미지 추출에 대한 요금 청구가 이 날짜부터 시작됩니다. 문서에서의 텍스트 추출은 추가 비용 없이 계속 제공됩니다.
>
> 기본 제공 기술의 실행에 대한 요금은 기존 [Cognitive Services 종량제 가격](https://azure.microsoft.com/pricing/details/cognitive-services/)으로 청구됩니다. 이미지 추출 가격은 미리 보기 가격으로 책정되며 [Azure Search 가격 페이지](https://go.microsoft.com/fwlink/?linkid=2042400)에 설명되어 있습니다.


인식 검색은 데이터를 추출하고 보강하여 Azure Search에서 검색 가능하게 합니다. 추출 및 강화 단계를 ‘인식 기술’이라고 합니다. 콘텐츠 인덱싱 중에 호출되는 기술 집합은 ‘기술’에서 정의됩니다. 기술 집합은 [미리 정의된 기술](cognitive-search-predefined-skills.md) 또는 사용자 정의 기술을 사용할 수 있습니다(자세한 내용은 [예제: 사용자 지정 기술 만들기](cognitive-search-create-custom-skill-example.md)를 참조합니다).

이 문서에서는 [Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) 리소스를 인식 기술에 연결하는 방법을 설명합니다.

선택한 Cognitive Services 리소스는 기본 제공 인식 기술을 지원합니다. 이 리소스는 청구 용도로도 사용됩니다. 기본 제공 인식 기술을 사용하여 수행하는 모든 보강은 선택한 Cognitive Services 리소스에 대해 청구됩니다. Cognitive Services 리소스를 사용하여 동일한 작업을 수행한 경우와 동일한 요금이 청구됩니다. [Cognitive Service 가격 책정](https://azure.microsoft.com/pricing/details/cognitive-services/)을 참조하세요.

## <a name="limits-when-no-cognitive-services-resource-is-selected"></a>Cognitive Services 리소스가 선택되지 않은 경우 제한
2019년 2월 1일부터 Cognitive Services 구독을 기술과 연결하지 않을 경우 몇 개의 문서만 무료로 보강할 수 있습니다(1일당 20개 문서). 

## <a name="associating-a-cognitive-services-resource-with-a-new-skillset"></a>새 기술과 Cognitive Services 리소스 연결

1. ‘데이터 가져오기’ 환경의 일부로, 데이터 원본에 연결한 후 ‘인식 검색 추가’ 선택적 단계로 이동합니다. 

1. ‘Cognitive Services 연결’ 섹션을 확장합니다. Search Service와 동일한 지역에 있는 모든 Cognitive 서비스 리소스가 표시됩니다. 
![확장된 Cognitive Services 연결](./media/cognitive-search-attach-cognitive-services/attach1.png "확장된 Cognitive Services 연결")

1. 기존 Cognitive Services 리소스를 선택하거나 ‘새 Cognitive Services 리소스를 만듭니다’. ‘무료(제한적 보강) 리소스’를 선택하는 경우 몇 개의 문서만 무료로 보강할 수 있습니다(1일당 20개 문서). ‘새 Cognitive Services 리소스 만들기’를 클릭한 경우 Cognitive Services 리소스를 만들 수 있는 새 탭이 열립니다. 

1. 새 리소스를 만든 경우 ‘새로 고침’을 클릭하여 Cognitive Services 리소스 목록을 새로 고치고 리소스를 선택합니다. 
![선택한 Cognitive Service 리소스](./media/cognitive-search-attach-cognitive-services/attach2.png "선택한 Cognitive Service 리소스")

1. 이 작업을 완료했으면 *보강 추가* 섹션을 펼쳐 데이터에 대해 실행할 특정 인식 기술을 선택하고 나머지 흐름을 계속 진행할 수 있습니다.

## <a name="associating-a-cognitive-services-resource-with-an-existing-skillset"></a>기존 기술과 Cognitive Services 리소스 연결

1. 서비스 개요 페이지에서 ‘기술’ 탭을 선택합니다. ![기술 탭](./media/cognitive-search-attach-cognitive-services/attach-existing1.png "기술 탭")

1. 수정하려는 기술을 ‘클릭’합니다. 기술을 편집할 수 있는 블레이드가 열립니다.

1. 기존 Cognitive Services 리소스를 선택하거나 ‘새 Cognitive Services 리소스를 만듭니다’. ‘무료(제한적 보강) 리소스’를 선택하는 경우 몇 개의 문서만 무료로 보강할 수 있습니다(1일당 20개 문서). ‘새 Cognitive Services 리소스 만들기’를 클릭한 경우 Cognitive Services 리소스를 만들 수 있는 새 탭이 열립니다. <n/> 
<img src="./media/cognitive-search-attach-cognitive-services/attach-existing2.png" width="350">

1. 새 리소스를 만든 경우 ‘새로 고침’을 클릭하여 Cognitive Services 리소스 목록을 새로 고치고 리소스를 선택합니다.
1. ‘확인’을 클릭하여 변경 내용을 확인합니다.

## <a name="associating-a-cognitive-services-resource-programmatically"></a>프로그래밍 방식으로 Cognitive Services 리소스 연결

프로그래밍 방식으로 기술을 정의하는 경우 `cognitiveServices` 섹션을 추가합니다. 이 섹션에는 기술과 연결할 Cognitive Services 리소스의 키와 “#Microsoft.Azure.Search.CognitiveServicesByKey”로 설정되어야 하는 @odata.type이 포함되어야 합니다. 이 패턴은 아래 예제에 나와 있습니다.

```http
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2017-11-11-Preview
api-key: [admin key]
Content-Type: application/json
```
```json
{
    "name": "skillset name",
    "skills": 
    [
      {
        "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
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
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey"
        "description": "mycogsvcs",
        "key": "your key goes here"
    }
}
```
## <a name="example-estimating-the-cost-of-document-cracking-and-enrichment"></a>예제: 문서 크래킹 및 보강 비용 예상
지정된 유형의 문서를 보강하는 비용을 예상하는 것이 좋습니다. 아래 연습은 예제일 뿐이지만 도움이 될 수 있습니다.

1000개 PDF가 있으며, 각 문서마다 평균 6페이지가 있다고 예상합니다. 이 연습을 위해 각 문서마다 페이지당 하나의 이미지가 있다고 가정합니다. 또한 페이지당 평균 약 3,000자가 있다고 가정합니다. 

이제 보강 파이프라인의 일부로 다음 단계를 수행한다고 가정해 보겠습니다.
1. 문서 크래킹의 일부로, 문서에서 콘텐츠 및 이미지를 추출합니다.
1. 보강의 일부로, 추출한 각 페이지에 대해 OCR을 수행하고 모든 페이지의 텍스트를 결합한 다음, 모든 이미지의 결합된 텍스트에서 각 조직을 추출합니다.

이러한 문서를 수집하는 데 드는 비용을 단계별로 예상해 보겠습니다.

1000개 문서의 경우 다음과 같습니다.

1. 문서 크래킹을 통해 총 6,000개의 이미지를 추출합니다. 추출된 1000개 이미지마다 $1라고 가정하면 비용은 $6.00가 됩니다.

2. 6,000개 이미지에서 텍스트를 추출합니다. 영어에서 OCR 인식 기술은 최적 알고리즘(DescribeText)을 사용합니다. 분석할 1,000개 이미지당 비용이 $2.50라고 가정할 경우 이 단계를 위해 $15.00를 지불합니다.

3. 엔터티 추출의 경우 페이지당 총 3개의 텍스트 레코드가 있습니다(각 레코드는 1,000자임). 3개 텍스트 레코드/페이지 * 6,000페이지 = 18,000개 텍스트 레코드입니다. $2.00/1000개 텍스트 레코드라고 가정할 경우 이 단계의 비용은 $36.00입니다.

비용을 모두 합하면, 설명한 기술을 통해 이러한 특성의 1000개 pdf 문서를 수집하는 데 $57.00를 지불합니다.  이 연습에서는 트랜잭션당 가장 높은 가격을 가정했으며, 점진적인 가격 책정 때문에 가격이 낮아졌을 수 있습니다. [Cognitive Services 가격 책정](https://azure.microsoft.com/pricing/details/cognitive-services)을 참조하세요.



## <a name="next-steps"></a>다음 단계
+ [Azure Search 가격 페이지](https://azure.microsoft.com/pricing/details/search/)
+ [기술 집합을 정의하는 방법](cognitive-search-defining-skillset.md)
+ [기능 만들기(REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [보강 필드를 매핑하는 방법](cognitive-search-output-field-mapping.md)
