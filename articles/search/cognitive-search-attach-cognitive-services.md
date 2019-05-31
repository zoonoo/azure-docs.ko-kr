---
title: 기술과 Cognitive Services 리소스 연결 - Azure Search
description: Cognitive Services에서는 올인원 구독을 Azure Search에서 cognitive 보강 파이프라인에 연결 하기 위한 지침입니다.
manager: cgronlun
author: LuisCabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 44f16b3334b991e071fa85ca4cffbc0837f0a6ec
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244421"
---
# <a name="attach-a-cognitive-services-resource-with-a-skillset-in-azure-search"></a>Azure Search에서 기술과 Cognitive Services 리소스 연결 

AI 알고리즘 드라이브는 [cognitive 인덱싱 파이프라인](cognitive-search-concept-intro.md) Azure Search에서 문서 보강 사용 합니다. 이러한 알고리즘 비롯 한 Azure Cognitive Services 리소스에 기반한 [Computer Vision](https://azure.microsoft.com/services/cognitive-services/computer-vision/) 이미지 분석 및 OCR (광학 문자 인식) 및 [텍스트 분석](https://azure.microsoft.com/services/cognitive-services/text-analytics/) 엔터티 인식에 대 한 핵심 구 추출 및 다른 강화 합니다. 내에서 알고리즘 래핑 문서 보강을 위해 Azure Search에서 사용을 *기술*에 배치는 *역량*에서 참조 되는 *인덱서* 중 인덱싱.

제한 된 수의 문서를 무료로 보강할 수 있습니다. 청구 가능한 Cognitive Services 리소스에 연결할 수 있습니다 또는 *역량* 크고 자주 워크 로드에 대 한 합니다. 이 문서에서는 Azure Search는 동안 문서를 보강 하는 청구 가능 Cognitive Services 리소스를 연결 하는 방법을 알아봅니다 [인덱싱](search-what-is-an-index.md)합니다.

> [!NOTE]
> 청구 가능한 이벤트는 Azure Search에서 문서 해독 단계의 일부분으로 Cognitive Services Api 및 이미지 추출에 대 한 호출을 포함합니다. Cognitive Services를 호출 하지 않는 기술을 또는 문서에서 텍스트 추출에 대 한 무료입니다.
>
> 에 청구 가능한 기술 실행 합니다 [종 량 Cognitive Services 가격 이동](https://azure.microsoft.com/pricing/details/cognitive-services/)합니다. 이미지 추출 가격에 대 한 참조를 [Azure Search 가격 책정 페이지](https://go.microsoft.com/fwlink/?linkid=2042400)합니다.

## <a name="same-region-requirement"></a>동일한 지역 요구 사항

동일한 지역 내에서 Azure Search 및 Azure Cognitive Services가 필요 합니다. 그렇지 않으면 런타임 시이 메시지를 받게 됩니다. `"Provided key is not a valid CognitiveServices type key for the region of your search service."` 

지역에서 서비스를 이동할 방법이 없습니다. 이 오류가 발생할 경우 Azure Search와 동일한 지역에 새 Cognitive Services 리소스를 만들어야 합니다.

## <a name="use-free-resources"></a>무료 리소스 사용

제한된 무료 처리 옵션을 사용하여 인지 검색 자습서 및 빠른 시작 연습을 완료할 수 있습니다.

무료 (제한 된 강화) 리소스는 구독 당 하루 20 문서로 제한 됩니다.

1. 데이터 가져오기 마법사를 엽니다.

   ![데이터 가져오기 마법사를 엽니다](media/search-get-started-portal/import-data-cmd2.png "데이터 가져오기 마법사를 열려면")

1. 데이터 원본을 선택 하 고 계속 **cognitive search 추가 (선택 사항)** 합니다. 이 마법사의 단계별 연습을 참조 하세요 [가져오기, 인덱스 및 포털 도구를 사용 하 여 쿼리](search-get-started-portal.md)합니다.

1. 확장 **Cognitive Services 첨부** 선택한 후 **(제한 된 강화) 무료**:

   ![Cognitive Services 연결 섹션을 확장 하](./media/cognitive-search-attach-cognitive-services/attach1.png "Cognitive Services 연결 확장 섹션")

1. 다음 단계를 이어서 **원칙이 추가**합니다. 포털에서 사용할 수 있는 기술에 대 한 참조 [2 단계: 인식 기술 추가](cognitive-search-quickstart-blob.md#create-the-enrichment-pipeline) cognitive search 빠른 시작에서 합니다.

## <a name="use-billable-resources"></a>유료 리소스 사용

하루 20 개가 넘는 원칙이 만든 워크 로드의 경우 청구 가능한 Cognitive Services 리소스를 연결 해야 합니다. Cognitive Services Api를 호출 하려면 의도 하지 않은 경우에 청구 가능한 Cognitive Services 리소스에 항상 연결 하는 것이 좋습니다. 일일 한도 재정의 리소스를 연결 합니다.

Cognitive Services Api를 호출 하는 기술에 대해서만 요금이 부과 됩니다. 에 대 한 요금이 청구 되지 않습니다 하 고 [사용자 지정 기술](cognitive-search-create-custom-skill-example.md), 또는 기술을 like [텍스트 병합기](cognitive-search-skill-textmerger.md), [텍스트 분할자](cognitive-search-skill-textsplit.md), 및 [쉐이 퍼](cognitive-search-skill-shaper.md), API 기반 하지는 합니다.

1. 데이터 가져오기 마법사를 열려면 데이터 원본을 선택 하 고 계속 **cognitive search 추가 (선택 사항)** 합니다.

1. 확장 **Cognitive Services 연결** 선택한 후 **새 Cognitive Services 리소스 만들기**합니다. 리소스를 만들 수 있도록 새 탭이 열립니다.

   ![Cognitive Services 리소스 만들기](./media/cognitive-search-attach-cognitive-services/cog-services-create.png "Cognitive Services 리소스 만들기")

1. 에 **위치** 목록에서 Azure Search 서비스 위치한 지역을 선택 합니다. 성능상의 이유로이 영역을 사용 해야 합니다. 아웃 바운드 대역폭 요금은 지역에 걸쳐 적용 되지 않습니다도이 영역을 사용 하 여 합니다.

1. 에 **가격 책정 계층** 목록에서 **S0** Cognitive Services 기능을 Azure Search에서 사용 하는 미리 정의 된 기술을 다시 비전 및 언어 기능을 포함 하 여 모든 통합 된 단일 컬렉션을 가져옵니다.

   S0 계층의 경우에서 찾을 수 있습니다 속도 특정 워크 로드에 대 한 합니다 [Cognitive Services 가격 페이지](https://azure.microsoft.com/pricing/details/cognitive-services/)합니다.
  
   + 에 **제공 선택** 나열 되어 있는지 확인 합니다 **Cognitive Services** 을 선택 합니다.
   + 아래 **언어** 기능을 요금 **텍스트 분석 표준** AI 인덱싱에 적용 합니다.
   + 아래 **Vision** 기능을 요금 **컴퓨터 비전 S1** 적용 합니다.

1. 선택 **만들기** 새로운 Cognitive Services 리소스를 프로 비전 합니다.

1. 데이터 가져오기 마법사를 포함 하는 이전 탭으로 반환 합니다. 선택 **새로 고침** 에서는 Cognitive Services 리소스 및 리소스를 선택 합니다.

   ![Cognitive Services 리소스 선택](./media/cognitive-search-attach-cognitive-services/attach2.png "Cognitive Services 리소스를 선택 합니다.")

1. 확장 된 **원칙이 추가** 데이터에서 실행 하려는 특정 cognitive 기술을 선택 하는 섹션입니다. 마법사의 나머지 부분을 완료 합니다. 포털에서 사용할 수 있는 기술에 대 한 참조 [2 단계: 인식 기술 추가](cognitive-search-quickstart-blob.md#create-the-enrichment-pipeline) cognitive search 빠른 시작에서 합니다.

## <a name="attach-an-existing-skillset-to-a-cognitive-services-resource"></a>Cognitive Services 리소스에 기존 기술 연결

기존 기술이 있으면 새 Cognitive Services 리소스나 다른 Cognitive Services 리소스에 연결할 수 있습니다.

1. 에 **Service 개요** 페이지에서 **기술이**:

   ![기술 탭](./media/cognitive-search-attach-cognitive-services/attach-existing1.png "기술 탭")

1. 기술의 이름을 선택 하 고 그런 다음 기존 리소스를 선택 하거나 새로 만듭니다. **확인**을 선택하여 변경 내용을 확인합니다.

   ![기술 리소스 목록](./media/cognitive-search-attach-cognitive-services/attach-existing2.png "기술 리소스 목록")

   에 유의 해야 합니다 **(제한 된 강화) 무료** 옵션 제한 20 문서 매일 및 사용할 수 있는 **새 Cognitive Services 리소스 만들기** 새 청구 가능한 리소스를 프로 비전 하 합니다. 새 리소스를 만든 경우 **새로 고침**을 선택하여 Cognitive Services 리소스 목록을 새로 고친 다음, 리소스를 선택합니다.

## <a name="attach-cognitive-services-programmatically"></a>프로그래밍 방식으로 Cognitive Services 연결

프로그래밍 방식으로 기술을 정의하는 경우 `cognitiveServices` 섹션을 기술에 추가합니다. 이 섹션의 역량을 사용 하 여 연결 하려는 Cognitive Services 리소스의 키를 포함 합니다. 리소스를 Azure Search 리소스와 동일한 지역에 있어야 한다는 것을 기억 합니다. 또한 `@odata.type`을 포함하고, 이를 `#Microsoft.Azure.Search.CognitiveServicesByKey`로 설정하세요.

다음 예제는 이러한 패턴을 보여줍니다. 통지를 `cognitiveServices` 정의의 끝에 있는 섹션입니다.

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

에 cognitive search 인덱싱와 관련 된 비용을 예상 하는 평균 문서 일부 숫자를 실행할 수 있도록 모양은 파악을 시작 합니다. 예를 들어, 있습니다 대략적인 수 있습니다.:

+ 1,000 Pdf입니다.
+ 6 페이지 각각.
+ 페이지 (6,000 이미지) 당 하나의 이미지입니다.
+ 페이지당 3,000 자입니다.

각 PDF, 이미지 및 텍스트 추출, OCR (광학 문자 인식) 이미지, 문서 해독 및 조직의 엔터티 인식을 구성 하는 파이프라인을 가정 합니다.

이 문서에 표시 된 가격은 가상입니다. 예측 프로세스를 설명 하는 데 사용 합니다. 비용 작을 수 없습니다. 트랜잭션의 실제 가격에 대 한 참조 [Cognitive Services 가격 책정](https://azure.microsoft.com/pricing/details/cognitive-services)합니다.

1. 텍스트 및 이미지 콘텐츠로 문서 해독의 경우, 텍스트 추출이 현재 무료입니다. 6,000 이미지에 대 한 $ 추출 1,000 모든 이미지에 대 한 1을 가정 합니다. 이 단계에 대 한 6.00 달러의 비용입니다.

2. 영어로 된 6,000개의 이미지 OCR의 경우, OCR 인지 기술은 최적의 알고리즘(DescribeText)을 사용합니다. 분석할 1,000개 이미지당 비용이 $2.50라고 가정할 경우 이 단계를 위해 $15.00를 지불합니다.

3. 엔터티 추출에 대 한 세 가지 텍스트 레코드 페이지당 총을 해야 합니다. 각 레코드는 1,000자입니다. 세 가지 텍스트 레코드를 6,000 페이지 곱한 페이지당 18000 텍스트 레코드를 같습니다. 1,000개의 텍스트 레코드당 $2.00라고 가정할 경우 이 단계의 비용은 $36.00입니다.

전체 과정에 약 $57.00 설명 되어 역량을 사용 하 여이 형식의 1,000 개의 PDF 문서를 수집 하는 지불 합니다.

## <a name="next-steps"></a>다음 단계
+ [Azure Search 가격 책정 페이지](https://azure.microsoft.com/pricing/details/search/)
+ [기술 집합을 정의하는 방법](cognitive-search-defining-skillset.md)
+ [기능 만들기(REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [보강 필드를 매핑하는 방법](cognitive-search-output-field-mapping.md)
