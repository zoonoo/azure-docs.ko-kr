---
title: 기술과 Cognitive Services 리소스 연결 - Azure Search
description: Cognitive Services 일대다 구독을 Azure Search의 인지 보강 파이프라인에 연결 하는 방법에 대 한 지침입니다.
manager: nitinme
author: LuisCabrer
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: luisca
ms.openlocfilehash: 113286f829b628d4740fbba34e7279741a934aef
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/21/2019
ms.locfileid: "71265937"
---
# <a name="attach-a-cognitive-services-resource-with-a-skillset-in-azure-search"></a>Azure Search에서 기술과 Cognitive Services 리소스 연결 

AI 알고리즘은 Azure Search 문서 보강에 사용 되는 [인지 인덱싱 파이프라인](cognitive-search-concept-intro.md) 을 구동 합니다. 이러한 알고리즘은 이미지 분석과 OCR (광학 문자 인식 [Text Analytics](https://azure.microsoft.com/services/cognitive-services/text-analytics/) )을 위한 [Computer Vision](https://azure.microsoft.com/services/cognitive-services/computer-vision/) , 엔터티 인식, 키 구 추출 및 기타 강화를 비롯 한 Azure Cognitive Services 리소스를 기반으로 합니다. . 문서 보강 목적을 위해 Azure Search에서 사용 하는 것 처럼 알고리즘은 *기술*내에 래핑됩니다. *기술*에 배치 되 고 인덱싱 중에 *인덱서가* 참조 됩니다.

제한 된 수의 문서를 무료로 보강할 수 있습니다. 또는 *기술* 에 청구 가능한 Cognitive Services 리소스를 더 크고 더 잦은 워크 로드에 연결할 수 있습니다. 이 문서에서는 Azure Search [인덱싱을](search-what-is-an-index.md)수행 하는 동안 청구 가능한 Cognitive Services 리소스를 연결 하 여 문서를 보강 하는 방법을 알아봅니다.

> [!NOTE]
> 청구 가능한 이벤트에는 Azure Search에서 문서 크랙 단계의 일부로 Cognitive Services API 및 이미지 추출에 대 한 호출이 포함 됩니다. Cognitive Services를 호출 하지 않는 기술 또는 문서에서 텍스트를 추출 하는 경우에는 요금이 부과 되지 않습니다.
>
> 청구 가능한 기술 실행은 [Cognitive Services 종 량 제 가격으로 진행](https://azure.microsoft.com/pricing/details/cognitive-services/)됩니다. 이미지 추출 가격은 [Azure Search 가격 책정 페이지](https://go.microsoft.com/fwlink/?linkid=2042400)를 참조 하세요.

## <a name="same-region-requirement"></a>동일한 지역 요구 사항

Azure Search와 Azure Cognitive Services는 동일한 지역 내에 있어야 합니다. 그렇지 않으면 런타임에이 메시지를 받게 됩니다. `"Provided key is not a valid CognitiveServices type key for the region of your search service."` 

여러 지역에서 서비스를 이동할 수 있는 방법은 없습니다. 이 오류가 발생 하는 경우 Azure Search와 동일한 지역에 새 Cognitive Services 리소스를 만들어야 합니다.

> [!NOTE]
> 일부 기본 제공 기술은 비 지역별 Cognitive Services (예: [텍스트 번역 기술](cognitive-search-skill-text-translation.md))를 기반으로 합니다. 이러한 기술을 기술에 추가 하면 데이터가 Azure Search 또는 Cognitive Services 리소스와 동일한 지역에 유지 되지 않을 수 있습니다. 자세한 내용은 [서비스 상태 페이지](https://aka.ms/allinoneregioninfo) 를 참조 하세요.

## <a name="use-free-resources"></a>무료 리소스 사용

제한된 무료 처리 옵션을 사용하여 인지 검색 자습서 및 빠른 시작 연습을 완료할 수 있습니다.

무료 (제한 된 강화) 리소스는 구독 당 하루 20 개의 문서로 제한 됩니다.

1. 데이터 가져오기 마법사를 엽니다.

   ![데이터 가져오기 마법사 열기](media/search-get-started-portal/import-data-cmd.png "데이터 가져오기 마법사 열기")

1. 데이터 원본을 선택 하 고 **인지 검색을 계속 추가 합니다 (선택 사항)** . 이 마법사의 단계별 연습은 [포털 도구를 사용 하 여 가져오기, 인덱스 및 쿼리](search-get-started-portal.md)를 참조 하세요.

1. **연결 Cognitive Services** 확장 한 다음 **무료 (제한 된 강화)** 를 선택 합니다.

   ![확장 된 Attach Cognitive Services 섹션](./media/cognitive-search-attach-cognitive-services/attach1.png "확장 된 Attach Cognitive Services 섹션")

1. 다음 단계인 **강화 추가**로 계속 진행 합니다. 포털에서 사용할 수 있는 기술에 대 한 자세한 내용은 인식 검색 퀵 스타트의 [2 단계: 인식 기술 추가](cognitive-search-quickstart-blob.md#create-the-enrichment-pipeline) 를 참조 하세요.

## <a name="use-billable-resources"></a>유료 리소스 사용

하루에 20 개 이상의 강화을 만드는 작업의 경우 청구 가능 Cognitive Services 리소스를 연결 해야 합니다. Cognitive Services API를 호출 하지 않으려는 경우에도 항상 청구 가능 Cognitive Services 리소스를 연결 하는 것이 좋습니다. 리소스를 연결 하면 일일 한도가 재정의 됩니다.

Cognitive Services API를 호출 하는 기술에 대해서만 요금이 청구 됩니다. [사용자 지정 기술](cognitive-search-create-custom-skill-example.md)또는 [텍스트 병합기](cognitive-search-skill-textmerger.md), [텍스트 분할자](cognitive-search-skill-textsplit.md)및 [shaper](cognitive-search-skill-shaper.md)같은 기술에 대 한 요금이 청구 되지 않으며,이는 API 기반이 아닙니다.

1. 데이터 가져오기 마법사를 열고, 데이터 원본을 선택 하 고, **인지 검색을 계속 추가 합니다 (선택 사항)** .

1. **연결 Cognitive Services** 를 확장 하 고 **새 Cognitive Services 리소스 만들기**를 선택 합니다. 리소스를 만들 수 있도록 새 탭이 열립니다.

   ![Cognitive Services 리소스 만들기](./media/cognitive-search-attach-cognitive-services/cog-services-create.png "Cognitive Services 리소스 만들기")

1. **위치** 목록에서 Azure Search 서비스가 있는 지역을 선택 합니다. 성능상의 이유로이 지역을 사용 해야 합니다. 또한이 영역을 사용 하면 지역 간에 아웃 바운드 대역폭 요금이 void 됩니다.

1. **가격 책정 계층** 목록에서 **S0** 를 선택 하 여 Azure Search에서 사용 하는 미리 정의 된 기술을 다시 사용 하는 비전 및 언어 기능을 포함 하 여 Cognitive Services 기능의 전체 컬렉션을 가져옵니다.

   S0 계층의 경우 [Cognitive Services 가격 책정 페이지](https://azure.microsoft.com/pricing/details/cognitive-services/)에서 특정 작업에 대 한 요금을 확인할 수 있습니다.
  
   + **제안 선택** 목록에서 **Cognitive Services** 가 선택 되어 있는지 확인 합니다.
   + **언어** 기능에서 **Text Analytics 표준** 에 대 한 요금은 AI 인덱싱에 적용 됩니다.
   + **비전** 기능에서 **S1 Computer Vision** 에 대 한 요금이 적용 됩니다.

1. **만들기** 를 선택 하 여 새 Cognitive Services 리소스를 프로 비전 합니다.

1. 데이터 가져오기 마법사가 포함 된 이전 탭으로 돌아갑니다. **새로 고침** 을 선택 하 Cognitive Services 리소스를 표시 한 다음 리소스를 선택 합니다.

   ![Cognitive Services 리소스를 선택 합니다.](./media/cognitive-search-attach-cognitive-services/attach2.png "Cognitive Services 리소스를 선택 합니다.")

1. **강화 추가** 섹션을 확장 하 여 데이터에서 실행 하려는 특정 인지 기술을 선택 합니다. 마법사의 나머지 단계를 완료 합니다. 포털에서 사용할 수 있는 기술에 대 한 자세한 내용은 인식 검색 퀵 스타트의 [2 단계: 인식 기술 추가](cognitive-search-quickstart-blob.md#create-the-enrichment-pipeline) 를 참조 하세요.

## <a name="attach-an-existing-skillset-to-a-cognitive-services-resource"></a>Cognitive Services 리소스에 기존 기술 연결

기존 기술이 있으면 새 Cognitive Services 리소스나 다른 Cognitive Services 리소스에 연결할 수 있습니다.

1. **서비스 개요** 페이지에서 **기술력과**를 선택 합니다.

   ![기술력과 탭](./media/cognitive-search-attach-cognitive-services/attach-existing1.png "기술력과 탭")

1. 기술의 이름을 선택한 다음 기존 리소스를 선택 하거나 새 리소스를 만듭니다. **확인**을 선택하여 변경 내용을 확인합니다.

   ![기술 리소스 목록](./media/cognitive-search-attach-cognitive-services/attach-existing2.png "기술 리소스 목록")

   **무료 (제한 된 강화)** 옵션은 매일 20 개의 문서를 제한 하 고 **새 Cognitive Services 리소스 만들기** 를 사용 하 여 새로운 청구 가능 리소스를 프로 비전 할 수 있습니다. 새 리소스를 만든 경우 **새로 고침**을 선택하여 Cognitive Services 리소스 목록을 새로 고친 다음, 리소스를 선택합니다.

## <a name="attach-cognitive-services-programmatically"></a>프로그래밍 방식으로 Cognitive Services 연결

프로그래밍 방식으로 기술을 정의하는 경우 `cognitiveServices` 섹션을 기술에 추가합니다. 이 섹션에는 기술 연결 하려는 Cognitive Services 리소스의 키가 포함 되어 있습니다. 리소스는 Azure Search 리소스와 동일한 지역에 있어야 합니다. 또한 `@odata.type`을 포함하고, 이를 `#Microsoft.Azure.Search.CognitiveServicesByKey`로 설정하세요.

다음 예제는 이러한 패턴을 보여줍니다. 정의 끝의 `cognitiveServices` 섹션을 확인 합니다.

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

## <a name="example-estimate-costs"></a>예: 비용 추정

인지 검색 인덱싱에 관련 된 비용을 예상 하려면 몇 가지 숫자를 실행할 수 있도록 평균 문서 모양을 파악 해야 합니다. 예를 들어 다음을 대략적으로 확인할 수 있습니다.

+ 1000 Pdf.
+ 6 개의 페이지
+ 페이지당 하나의 이미지 (6000 이미지)
+ 페이지당 3000 자

각 PDF의 문서 크랙, 이미지 및 텍스트 추출, 이미지의 OCR (광학 문자 인식) 및 조직의 엔터티 인식으로 구성 된 파이프라인을 가정 합니다.

이 문서에 표시 된 가격은 가상입니다. 예측 프로세스를 설명 하는 데 사용 됩니다. 비용은 낮아질 수 있습니다. 트랜잭션의 실제 가격은 [Cognitive Services 가격 책정](https://azure.microsoft.com/pricing/details/cognitive-services)을 참조 하세요.

1. 텍스트 및 이미지 콘텐츠로 문서 해독의 경우, 텍스트 추출이 현재 무료입니다. 6000 이미지의 경우 추출 된 모든 1000 이미지에 대해 $1를 가정 합니다. 이 단계의 비용은 $6.00입니다.

2. 영어로 된 6,000개의 이미지 OCR의 경우, OCR 인지 기술은 최적의 알고리즘(DescribeText)을 사용합니다. 분석할 1,000개 이미지당 비용이 $2.50라고 가정할 경우 이 단계를 위해 $15.00를 지불합니다.

3. 엔터티 추출의 경우 페이지당 총 세 개의 텍스트 레코드가 있습니다. 각 레코드는 1,000자입니다. 페이지당 3 개의 텍스트 레코드 6000 페이지를 곱한 값이 18000 텍스트 레코드와 같습니다. 1,000개의 텍스트 레코드당 $2.00라고 가정할 경우 이 단계의 비용은 $36.00입니다.

이를 모두 함께 사용 하면 설명 된 기술을 사용 하 여이 유형의 1000 PDF 문서를 수집 하는 데 $57.00에 대 한 비용을 지불 하 게 됩니다.

## <a name="next-steps"></a>다음 단계
+ [Azure Search 가격 책정 페이지](https://azure.microsoft.com/pricing/details/search/)
+ [기능을 정의하는 방법](cognitive-search-defining-skillset.md)
+ [기술 집합 만들기(REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [보강 필드를 매핑하는 방법](cognitive-search-output-field-mapping.md)
