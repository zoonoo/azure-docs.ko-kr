---
title: Cognitive Search 파이프라인에서 기술 세트 만들기 - Azure Search
description: Azure Search에서 사용을 위해 데이터에서 구조화된 정보를 추출하고 보강하기 위한 데이터 추출, 자연어 처리 또는 이미지 분석 단계입니다.
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 010880304c5011bd5a767650aa0ae6b6103bdde2
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2019
ms.locfileid: "65540869"
---
# <a name="how-to-create-a-skillset-in-an-enrichment-pipeline"></a>보강 파이프라인에서 기술 집합을 만드는 방법

인식 검색은 데이터를 추출하고 보강하여 Azure Search에서 검색 가능하게 합니다. 인덱싱 동안 참조된 *기술 집합*에 결합된 추출 및 보강 단계를 *인식 기술*이라고 합니다. 기술 집합을 사용할 수 있습니다 [기본 제공 기술을](cognitive-search-predefined-skills.md) 또는 사용자 지정 기술 (참조 [예제: 사용자 지정 기술 만들기](cognitive-search-create-custom-skill-example.md) 자세한).

이 문서에서는 사용하려는 기술에 대한 보강 파이프라인을 만드는 방법을 알아 봅니다. 기술 집합은 Azure Search [인덱서](search-indexer-overview.md)에 첨부되어 있습니다. 이 문서에서 다루는 파이프라인 디자인의 한 부분은 기술 집합 자체를 생성하는 것입니다. 

> [!NOTE]
> 파이프라인 디자인의 다른 부분은 인덱서를 지정하는 것으로 [다음 단계](#next-step)에서 다루게 됩니다. 인덱서 정의에는 대상 인덱스의 출력에 입력을 연결하는 데 사용되는 필드 매핑 외에 기술 집합에 대한 참조가 포함됩니다.

유념해야 할 주요 사항입니다.

+ 인덱서 당 하나의 기술 집합만 사용할 수 있습니다.
+ 기술 집합에는 최소 하나 이상의 기술이 있어야 합니다.
+ 동일한 형식의 여러 기술(예: 이미지 분석 기술의 변형)을 만들 수 있습니다.

## <a name="begin-with-the-end-in-mind"></a>종료로 시작

권장된 초기 단계는 검색 솔루션에서 해당 데이터를 사용하려는 방법 및 원시 데이터에서 추출할 데이터를 결정하는 것입니다. 전체 보강 파이프라인의 일러스트레이션을 만들면 필요한 단계를 확인할 수 있습니다.

재무 분석가 주석의 집합을 처리하는 데 관심이 있는 것으로 가정합니다. 각 파일의 경우 회사 이름 및 주석의 일반 감정을 추출하려 합니다. 회사가 참여하는 비즈니스 종류 등 회사에 대한 추가 정보를 찾는 데 Bing Entity Search 서비스를 사용하는 사용자 지정 보강자를 작성할 수도 있습니다. 특히 각 문서에 대해 인덱스된 다음과 같은 정보를 추출하려 합니다.

| 레코드 텍스트 | 기업 | 감정 | 회사 설명 |
|--------|-----|-----|-----|
|샘플 레코드| ["Microsoft", "LinkedIn"] | 0.99 | ["Microsoft Corporation는 미국의 다국적 기술 회사...", "LinkedIn은 비즈니스 및 고용 지향 소셜 네트워킹..."]

다음 다이어그램에서는 가상 보강 파이프라인을 보여 줍니다.

![가상 보강 파이프라인](media/cognitive-search-defining-skillset/sample-skillset.png "가상 보강 파이프라인")


파이프라인에서 원하는 것에 대해 잘 알고 있으면 이러한 단계를 제공하는 기술 집합을 표현할 수 있습니다. 기능적으로 Azure Search에 인덱서 정의를 업로드할 때 기술 집합을 표현합니다. 인덱서를 업로드하는 방법에 대해 자세히 알려면 [인덱서 설명서](https://docs.microsoft.com/rest/api/searchservice/create-indexer)를 참조합니다.


다이어그램에서 *문서 해독* 단계는 자동으로 발생합니다. 기본적으로 Azure Search는 잘 알려진 파일을 열고 각 문서에서 추출된 텍스트를 포함하는 *콘텐츠* 필드를 만드는 방법에 대해 잘 압니다. 흰색 상자는 기본 제공된 보강자이며, 점으로 구분된 "Bing Entity Search" 상자는 만들고 있는 사용자 지정 보강자를 나타냅니다. 설명처럼 기술 집합은 세 가지 기술이 포함되어 있습니다.

## <a name="skillset-definition-in-rest"></a>REST의 기술 집합 정의

기술 집합은 기술의 배열로서 정의됩니다. 각 기술은 해당 입력의 원본 및 생성된 출력의 이름을 정의합니다. [기술 집합 REST API 만들기](https://docs.microsoft.com/rest/api/searchservice/create-skillset)를 사용하여 이전 다이어그램에 해당하는 기술 집합을 정의할 수 있습니다. 

```http
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2019-05-06
api-key: [admin key]
Content-Type: application/json
```

```json
{
  "description": 
  "Extract sentiment from financial records, extract company names, and then find additional information about each company mentioned.",
  "skills":
  [
    {
      "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
      "context": "/document",
      "categories": [ "Organization" ],
      "defaultLanguageCode": "en",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "organizations",
          "targetName": "organizations"
        }
      ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "score",
          "targetName": "mySentiment"
        }
      ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
     "description": "Calls an Azure function, which in turn calls Bing Entity Search",
      "uri": "https://indexer-e2e-webskill.azurewebsites.net/api/InvokeTextAnalyticsV3?code=foo",
      "httpHeaders": {
          "Ocp-Apim-Subscription-Key": "foobar"
      },
      "context": "/document/organizations/*",
      "inputs": [
        {
          "name": "query",
          "source": "/document/organizations/*"
        }
      ],
      "outputs": [
        {
          "name": "description",
          "targetName": "companyDescription"
        }
      ]
    }
  ]
}
```

## <a name="create-a-skillset"></a>기술 집합 만들기

기술 집합을 만드는 동안 기술 집합을 자체 문서화하는 설명을 제공할 수 있습니다. 설명은 선택 사항이지만 기술 집합의 용도를 추적하는 데 유용합니다. 기술 집합은 주석을 허용하지 않는 JSON 문서이므로 주석을 위한 `description` 요소를 사용해야 합니다.

```json
{
  "description": 
  "This is our first skill set, it extracts sentiment from financial records, extract company names, and then finds additional information about each company mentioned.",
  ...
}
```

기술 집합의 다음 부분은 기술 배열입니다. 각 기술을 보강의 기본 형식으로 생각할 수 있습니다. 각 기술은 이 보강 파이프라인에서 작은 작업을 수행합니다. 각 기술은 입력(또는 입력의 집합)을 사용하고 일부 출력을 반환합니다. 다음 섹션에서는 몇 가지 기본 제공 및 사용자 지정 기술, 입력 및 출력 참조를 통해 기술을 함께 연결을 지정 하는 방법에 집중 합니다. 입력은 원본 데이터 또는 다른 기술에서 올 수 있습니다. 출력은 검색 인덱스의 필드에 매핑되거나 다운스트림 기술에 대한 입력으로 사용될 수 있습니다.

## <a name="add-built-in-skills"></a>기본 제공 기술을 추가합니다

기본 제공 되는 첫 번째 기술에 살펴보겠습니다 [엔터티 인식 기술](cognitive-search-skill-entity-recognition.md):

```json
    {
      "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
      "context": "/document",
      "categories": [ "Organization" ],
      "defaultLanguageCode": "en",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "organizations",
          "targetName": "organizations"
        }
      ]
    }
```

* 모든 기본 제공 기술에 `odata.type`하십시오 `input`, 및 `output` 속성입니다. 기술 관련 속성은 해당 기술에 적용 가능한 추가 정보를 제공합니다. 엔터티 인식의 경우 `categories`은 미리 학습된 모델이 인식할 수 있는 엔터티 형식의 고정 집합 중 한 엔터티입니다.

* 각 기술에는 ```"context"```이 있어야 합니다. 컨텍스트는 작업을 수행할 수준을 나타냅니다. 위의 기술에는 컨텍스트가 엔터티 인식 기술 문서 마다 한 번씩 호출 되는 의미 하는 전체 문서를 합니다. 출력은 또한 해당 수준에서 생성됩니다. 보다 구체적으로, ```"organizations"```은 ```"/document"```의 구성원으로 생성됩니다. 다운스트림 기술에서 새로 만든 이 정보를 ```"/document/organizations"```로 언급할 수 있습니다.  ```"context"``` 필드가 명시적으로 설정되지 않은 경우 기본 컨텍스트는 문서입니다.

* 기술에는 원본 입력이 ```"/document/content"```로 설정된 "텍스트"라는 하나의 입력이 있습니다. 기술 (엔터티 인식)에서 작동 합니다 *콘텐츠* Azure blob 인덱서를에서 만든 표준 필드는 각 문서의 필드. 

* 기술에는 ```"organizations"```이라는 하나의 출력이 있습니다. 출력은 처리 동안만 존재합니다. 이 출력을 다운스트림 기술의 입력에 연결하려면 출력을 ```"/document/organizations"```로 참조합니다.

* 특정 문서의 경우 ```"/document/organizations"```의 값은 텍스트에서 추출된 조직의 배열입니다. 예를 들면 다음과 같습니다.

  ```json
  ["Microsoft", "LinkedIn"]
  ```

일부 상황에서는 별도로 배열의 각 요소를 참조하기 위해 호출합니다. 예를 들어 ```"/document/organizations"```의 각 요소를 별도로 다른 기술(예: 사용자 지정 Bing Entity Search 보강자)에 전달하려 한다고 가정합니다. 경로에 별표를 추가하여 배열의 각 요소를 참조할 수 있습니다. ```"/document/organizations/*"``` 

감정 추출을 위한 두 번째 기술은 첫 번째 보강자와 동일한 패턴을 따릅니다. ```"/document/content"```을 입력으로 사용하고 각 콘텐츠 인스턴스에 대해 감정 점수를 반환합니다. ```"context"``` 필드를 명시적으로 설정하지 않았으므로 출력(mySentiment)은 ```"/document"```의 자식입니다.

```json
    {
      "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "score",
          "targetName": "mySentiment"
        }
      ]
    },
```

## <a name="add-a-custom-skill"></a>사용자 지정 기술 추가

사용자 지정 Bing Entity Search 보강자의 구조를 회수합니다.

```json
    {
      "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
     "description": "This skill calls an Azure function, which in turn calls Bing Entity Search",
      "uri": "https://indexer-e2e-webskill.azurewebsites.net/api/InvokeTextAnalyticsV3?code=foo",
      "httpHeaders": {
          "Ocp-Apim-Subscription-Key": "foobar"
      }
      "context": "/document/organizations/*",
      "inputs": [
        {
          "name": "query",
          "source": "/document/organizations/*"
        }
      ],
      "outputs": [
        {
          "name": "description",
          "targetName": "companyDescription"
        }
      ]
    }
```

이 정의는 보강 절차의 일부로 웹 API를 호출하는 [사용자 지정 기술](cognitive-search-custom-skill-web-api.md)입니다. 이 기술 엔터티 인식 하 여 식별 된 각 조직에 대 한 web API는 조직에 대 한 설명을 찾을 수를 호출 합니다. 웹 API를 호출할 때 및 받은 정보를 이동하는 방법의 오케스트레이션은 보강 엔진에서 내부적으로 처리합니다. 그러나 이 사용자 지정 API를 호출하는 데 필요한 초기화는 JSON(예: uri, httpHeaders 및 예상 입력)에서 제공되어야 합니다. 보강 파이프라인에 대한 사용자 지정 웹 API를 만드는 지침은 [사용자 지정 인터페이스를 정의하는 방법](cognitive-search-custom-skill-interface.md)을 참조합니다.

별표를 사용하여 "컨텍스트" 필드가 ```"/document/organizations/*"```로 설정되는 것은 보강 단계가 ```"/document/organizations"```에서 *각 조직에 대해* 호출된다는 의미입니다. 

회사 설명의 경우 출력은 식별된 각 조직에 대해 생성됩니다. 다운스트림 단계에서 설명을 언급할 때(예: 핵심 구문 추출) 그렇게 하려면 경로 ```"/document/organizations/*/description"```을 사용합니다. 

## <a name="add-structure"></a>구조를 추가 합니다.

기술 집합은 구조화되지 않은 데이터에서 구조화된 정보를 생성합니다. 다음 예제를 살펴보세요.

*"4분기에 Microsoft는 작년에 사들인 소셜 네트워킹 회사 LinkedIn에서 11억 달러의 수익을 기록했습니다. 이 인수는 Microsoft가 LinkedIn 기능을 자사의 CRM 및 Office 기능과 결합할 수 있게 했습니다. 주주들은 지금까지의 진행 상황에 만족해 합니다.”*

가능성 있는 결과는 다음 그림과 유사하게 생성된 구조입니다.

![샘플 출력 구조](media/cognitive-search-defining-skillset/enriched-doc.png "샘플 출력 구조")

지금까지이 구조에 내부 전용, 메모리 내 전용 및 Azure Search 인덱스에만 사용 되었습니다. 기술 저장소의 추가 검색 외부에서 사용할 모양의 강화를 저장 하는 방법을 제공 합니다.

## <a name="add-a-knowledge-store"></a>기술 저장소 추가

[기술 저장소](knowledge-store-concept-intro.md) 풍부한 문서를 저장 하기 위한 Azure Search의 미리 보기 기능입니다. 사용자가 만든 기술 저장소는 Azure storage 계정에 지는 풍부한 데이터 도착 위치 리포지토리입니다. 

기술 저장소 정의 기술 집합에 추가 됩니다. 전체 프로세스의 연습을 참조 하세요 [지식 저장소를 사용 하 여 시작 하는 방법을](knowledge-store-howto.md)합니다.

```json
"knowledgeStore": {
  "storageConnectionString": "<an Azure storage connection string>",
  "projections" : [
    {
      "tables": [ ]
    },
    {
      "objects": [
        {
          "storageContainer": "containername",
          "source": "/document/EnrichedShape/",
          "key": "/document/Id"
        }
      ]
    }
  ]
}
```

Blob storage에서 JSON 문서 또는 유지 하는 계층 관계를 사용 하 여 테이블로 풍부한 문서를 저장할 수 있습니다. 기술 기술 중 하나에서 출력 프로젝션에 대 한 입력으로 제공 될 수 있습니다. 데이터에 특정 프로젝트를 찾으려는 경우 셰이프를 업데이트 [쉐이 퍼 기술](cognitive-search-skill-shaper.md) 복합 형식을 사용 하 여에 대 한 모델 이제 수 있습니다. 

<a name="next-step"></a>

## <a name="next-steps"></a>다음 단계

보강 파이프라인 및 기술 집합에 익숙하므로 [기술 집합에서 주석을 참조하는 방법](cognitive-search-concept-annotations-syntax.md) 또는 [ 인덱스에서 필드에 출력을 매핑하는 방법](cognitive-search-output-field-mapping.md)을 계속 사용합니다. 
