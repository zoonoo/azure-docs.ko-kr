---
title: 기능 만들기(REST api-version=2017-11-11-Preview) - Azure Search | Microsoft Docs
description: 기능은 보강 파이프라인을 구성하는 인식 기술 컬렉션입니다.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: language-reference
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: a5277d4137ede5fe6dacf993413eefd7c9e46ad4
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
ms.locfileid: "33786912"
---
# <a name="create-skillset-api-version2017-11-11-preview"></a>기능 만들기(api-version=2017-11-11-Preview)

**적용 대상:** api-version-2017-11-11-Preview

기능은 자연어 처리 및 기타 변환에 사용되는 인식 기술 컬렉션입니다. 기술에는 명명된 엔터티 추출, 핵심 구 추출, 텍스트를 논리 페이지로 청크 등이 포함됩니다.

기능을 사용하려면 Azure Search 인덱서에서 기능을 참조한 후 인덱서를 실행하여 데이터를 가져오고, 변환 및 보강을 호출하고, 출력 필드를 인덱스에 매핑합니다. 기능은 상위 수준 리소스이지만, 인덱서 처리 내에서만 작동합니다. 상위 수준 리소스이므로 기능을 한 번 디자인한 후 여러 인덱서에서 참조할 수 있습니다. 

기능은 HTTP PUT 또는 POST 요청을 통해 Azure Search에 표시됩니다. PUT의 경우 요청의 본문이 호출되는 기술을 지정하는 JSON 스키마입니다. 입력-출력 연결을 통해 기술이 연결되며, 한 변환의 출력이 다른 변환의 입력이 됩니다.

기능에는 하나 이상의 기술이 있어야 합니다. 최대 기술 수에 대한 이론적 제한은 없지만, 3~5개가 일반적인 구성입니다.  


> [!NOTE]
> Cognitive Search는 공개 미리 보기 상태이며, 현재 기능 실행이 무료로 제공됩니다. 이 기능의 가격은 추후에 발표될 예정입니다.

```http  
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2017-11-11-Preview
api-key: [admin key]
Content-Type: application/json
```  

## <a name="request"></a>요청  
 모든 서비스 요청에는 HTTPS를 사용해야 합니다. **기능 만들기** 요청은 기능 이름을 URL의 일부로 포함해서 PUT 메서드를 사용하여 구성할 수 있습니다. 기능이 없으면 만들어지고, 이미 있으면 새 정의로 업데이트됩니다. 한 번에 하나의 기능만 PUT할 수 있습니다.  

 기능 이름은 다음 요구 사항을 충족해야 합니다.

- 소문자여야 합니다.
- 문자 또는 숫자로 시작하고 끝나야 합니다.
- 슬래시 또는 점이 없어야 합니다.
- 128자 미만이어야 합니다. 

문자나 숫자로 기능 이름을 시작한 후 이름의 나머지 부분에는 원하는 모든 문자, 숫자, 대시(여러 대시를 연속적으로 포함할 수는 없음)를 사용할 수 있습니다.  

**api-version** 매개 변수가 필요합니다. 사용 가능한 버전은 `2017-11-11-Preview`뿐입니다. 모든 버전 목록을 보려면 [Azure Search의 API 버전](https://go.microsoft.com/fwlink/?linkid=834796)을 참조하세요. 


### <a name="request-headers"></a>헤더 요청  

 다음 표에서는 필수 요청 헤더와 선택적 요청 헤더에 대해 설명합니다.  

|요청 헤더|설명|  
|--------------------|-----------------|  
|*Content-Type:*|필수 사항입니다. `application/json`|  
|*api-key:*|필수 사항입니다. `api-key`는 Search 서비스에 대한 요청을 인증하는 데 사용되며, 서비스에 고유한 문자열 값입니다. **기능 만들기** 요청은 쿼리 키가 아니라 관리 키로 설정된 `api-key` 헤더를 포함해야 합니다.|  

요청 URL을 구성하려면 서비스 이름도 필요합니다. 서비스 이름과 `api-key`는 Azure Portal의 서비스 대시보드에서 가져올 수 있습니다. 페이지 탐색 도움말은 [포털에서 Azure Search 서비스 만들기](search-create-service-portal.md)를 참조하세요.  

### <a name="request-body-syntax"></a>요청 본문 구문  

요청 본문에는 하나 이상의 완전히 지정된 기술과 선택적 이름 및 설명 매개 변수로 구성된 기능 정의가 포함됩니다.  

요청 페이로드 구조를 지정하는 구문은 다음과 같습니다. 샘플 요청은 이 문서의 뒷부분과 [기능을 정의하는 방법](cognitive-search-defining-skillset.md)에 나와 있습니다.  

```
{   
    "name" : "Required for POST, optional for PUT. Friendly name of the skillset",  
    "description" : "Optional. Anything you want, or null",  
    "Skills" : "Required. An array of skills. Each skill has an odata.type, name, input and output parameters",  
}  
```

### <a name="request-example"></a>요청 예제
 다음 예제에서는 재무 문서 컬렉션을 보강하는 데 사용되는 기능을 만듭니다.

```http
PUT https://[servicename].search.windows.net/skillsets/financedocenricher?api-version=2017-11-11-Preview
api-key: [admin key]
Content-Type: application/json
```

요청 본문은 JSON 문서입니다. 이 특정 기능은 `contents`의 핵심을 두 개의 다른 변환으로 비동기적, 독립적으로 처리하는 두 가지 기술을 사용합니다. 또는 한 변환의 출력을 다른 변환의 입력으로 지정할 수 있습니다. 자세한 내용은 [기능을 정의하는 방법](cognitive-search-defining-skillset.md)을 참조하세요.

```json
{
  "name": "financedocenricher",
  "description": 
  "Extract sentiment from financial records, extract company names, and then find additional information about each company mentioned.",
  "skills":
  [
    {
      "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
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
  ]
}
```

## <a name="response"></a>response  

 성공적인 요청의 경우, 상태 코드 “201 생성됨”이 표시되어야 합니다.  

 기본적으로 응답 본문에는 생성된 기능 정의에 대한 JSON이 포함됩니다. 그러나 Prefer 요청 헤더를 return=minimal로 설정하면 응답 본문이 비어 있게 되고, 성공 상태 코드가 “201 생성됨”이 아닌 “204 콘텐츠 없음”으로 표시됩니다. 기능을 만드는 데 사용한 방법(PUT 또는 POST)에 상관없이 결과는 동일합니다.   

## <a name="see-also"></a>참고 항목

+ [인식 검색 개요](cognitive-search-concept-intro.md)
+ [빠른 시작: 인식 검색 시도](cognitive-search-quickstart-blob.md)
+ [자습서: Azure Blob의 보강된 인덱싱](cognitive-search-tutorial-blob.md)
+ [기능을 정의하는 방법](cognitive-search-defining-skillset.md)
+ [필드를 매핑하는 방법](cognitive-search-output-field-mapping.md)
+ [사용자 지정 인터페이스를 정의하는 방법](cognitive-search-custom-skill-interface.md)
+ [예제: 사용자 지정 기술 만들기](cognitive-search-create-custom-skill-example.md)
+ [미리 정의된 기술](cognitive-search-predefined-skills.md)