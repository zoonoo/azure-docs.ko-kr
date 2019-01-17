---
title: 기술과 Cognitive Services 리소스 연결 - Azure Search
description: Azure Search에서 인지 보강 파이프라인에 Cognitive Services 올인원 구독을 연결하는 방법에 대한 지침입니다.
manager: cgronlun
author: LuisCabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 01/07/2018
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 509125e7c93f34b9ce28c58cb1ec96db1074d995
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/09/2019
ms.locfileid: "54119648"
---
# <a name="associate-a-cognitive-services-resource-with-a-skillset-in-azure-search"></a>Azure Search에서 기술과 Cognitive Services 리소스 연결 

비정형 데이터 처리를 위해 [인지 검색](cognitive-search-concept-intro.md) 파이프라인을 구동하는 AI 알고리즘은 [**Cognitive Services 리소스**](https://azure.microsoft.com/services/cognitive-services/)를 기반으로 합니다. [**Computer Vision**](https://azure.microsoft.com/services/cognitive-services/computer-vision/)과 같은 리소스는 이미지 파일에서 텍스트와 구조를 추출하기 위한 이미지 분석 및 OCR(광학 문자 인식) 기능을 제공하며 [**Text Analytics**](https://azure.microsoft.com/services/cognitive-services/text-analytics/)는 엔터티 인식 및 핵심 구 추출과 같은 자연어 처리 기능을 제공합니다.

제한된 수의 문서를 무료로 보강하거나 크고 빈번한 워크로드에 대해 유료 Cognitive Services 리소스를 연결할 수도 있습니다. 이 문서에서는 Cognitive Services 리소스를 인지 기술에 연결하여 인덱싱 중에 데이터를 보강하는 방법을 알아봅니다.

파이프라인이 [사용자 지정 기술](cognitive-search-create-custom-skill-example.md)을 제외하고 구성된 경우에는 Cognitive Services 리소스를 연결할 필요가 없습니다.

> [!NOTE]
> 2018년 12월 21일부터 Cognitive Services 리소스를 Azure Search 기술과 연결할 수 있습니다. 그러면 기술을 실행한 요금을 청구할 수 있습니다. 또한 문서 해독 단계의 일부인 이미지 추출에 대한 요금 청구가 이 날짜에서 시작됩니다. 문서의 텍스트 추출은 추가 비용 없이 계속 제공됩니다.
>
> [기본 제공 인지 기술](cognitive-search-predefined-skills.md)을 실행하면 Azure Search와 별도로 작업을 수행한 것처럼 [Cognitive Services 종량제 가격](https://azure.microsoft.com/pricing/details/cognitive-services/)으로 비용이 부과됩니다. 이미지 추출 가격 책정은 미리 보기 가격 책정으로 청구되며 [Azure Search 가격 책정 페이지](https://go.microsoft.com/fwlink/?linkid=2042400)에 설명되어 있습니다.


## <a name="use-free-resources"></a>무료 리소스 사용

제한된 무료 처리 옵션을 사용하면 매일 20개의 문서 보강 작업을 수행할 수 있으며, 이 정도면 인지 서비스 자습서와 빠른 시작 연습을 완료하는 데 충분합니다. 

> [!Important]
> 2019년 2월 1일부터 **무료(제한적 보강)** 가 하루 20개 문서로 제한됩니다. 

1. **데이터 가져오기** 마법사를 엽니다.

   ![데이터 가져오기 명령](media/search-get-started-portal/import-data-cmd2.png "데이터 가져오기 명령")

1. 데이터 원본을 선택하고 **인지 검색 추가(선택 사항)** 를 선택합니다. 이 마법사에 대한 단계별 연습은 [포털 도구를 사용하여 가져오기, 인덱싱 및 쿼리](search-get-started-portal.md)를 참조하세요.

1. **Cognitive Services 연결**을 확장하고 **무료(제한적 보강)** 을 선택합니다.

   ![확장된 Cognitive Services 연결 섹션](./media/cognitive-search-attach-cognitive-services/attach1.png "확장된 Cognitive Services 연결")

다음 단계, **보강 추가**를 진행합니다. 포털에서 사용할 수 있는 기술에 대한 설명은 인지 검색 빠른 시작의 ["2단계: 인지 기술 추가"](cognitive-search-quickstart-blob.md#create-the-enrichment-pipeline)를 참조하세요.

## <a name="use-billable-resources"></a>유료 리소스 사용

매일 20개를 초과하는 문서에 번호를 매기는 워크로드에는 유료 Cognitive Services 리소스가 필요합니다.

1. **Cognitive Services 연결**의 **데이터 가져오기** 마법사에서 기존 리소스를 선택하거나 **새 Cognitive Services 리소스 만들기**를 클릭합니다.

1. **새 Cognitive Services 리소스 만들기**에는 리소스를 만들 수 있는 새 탭이 열립니다. 리소스에 고유한 이름을 지정합니다.

1. Azure Search와 같은 위치를 선택합니다. 현재 인지 기술 인덱싱은 다음 지역에서 사용할 수 있습니다.

  * 미국 중서부
  * 미국 중남부
  * 미국 동부
  * 미국 동부 2
  * 미국 서부 2
  * 캐나다 중부
  * 서유럽
  * 영국 남부
  * 북유럽
  * 브라질 남부
  * 동남아시아
  * 인도 중부
  * 오스트레일리아 동부

1. 올인원 가격 책정 계층, **S0**을 선택합니다. 이 계층은 인지 검색에 미리 정의된 기술을 지원하는 Vision 및 Language 기능을 제공합니다.

    ![새 Cognitive Services 리소스 만들기](./media/cognitive-search-attach-cognitive-services/cog-services-create.png "새 Cognitive Services 리소스 만들기")

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

이미지 및 텍스트 추출, 이미지에 대한 OCR(광학 문자 인식) 및 조직의 명명된 엔터티 인식을 사용하여 각 PDF를 해독하는 문서로 구성된 파이프라인이 있다고 가정합니다. 

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
