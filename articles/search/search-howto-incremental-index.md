---
title: 캐시 및 증분 보강 구성(미리 보기)
titleSuffix: Azure Cognitive Search
description: 인지 기술 집합에서 제어된 처리를 위해 캐싱을 활성화하고 보강된 콘텐츠의 상태를 보존합니다. 이 기능은 현재 공개 미리 보기로 제공됩니다.
author: vkurpad
manager: eladz
ms.author: vikurpad
ms.service: cognitive-search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: 66bac2a063a3257a2101ca2f30e5946264adb9ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76989555"
---
# <a name="how-to-configure-caching-for-incremental-enrichment-in-azure-cognitive-search"></a>Azure 인지 검색에서 증분 보강을 위해 캐싱을 구성하는 방법

> [!IMPORTANT] 
> 증분 보강은 현재 공개 미리 보기중입니다. 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요. 이 기능은 [REST API 버전 2019-05-06-미리 보기](search-api-preview.md)에서 제공됩니다. 현재 포털 또는 .NET SDK 지원이 없습니다.

이 문서에서는 매번 다시 빌드할 필요 없이 단계를 점진적으로 수정할 수 있도록 보강 파이프라인에 캐싱을 추가하는 방법을 보여 주십습니다. 기본적으로 기술 집합은 상태 비수상태이며 컴포지션의 일부를 변경하려면 인덱서를 완전히 다시 실행해야 합니다. 증분 보강을 통해 인덱서에서는 기술 집합 또는 인덱서 정의에서 검색된 변경 내용을 기반으로 문서 트리의 어떤 부분을 새로 고쳐야 하는지 결정할 수 있습니다. 기존 처리된 출력은 보존되고 가능한 경우 재사용됩니다. 

캐시된 콘텐츠는 제공한 계정 정보를 사용하여 Azure 저장소에 배치됩니다. 인덱서를 `ms-az-search-indexercache-<alpha-numerc-string>`실행할 때 "라는 컨테이너가 만들어집니다. 검색 서비스에서 관리하는 내부 구성 요소로 간주해야 하며 수정해서는 안 됩니다.

인덱서 설정에 익숙하지 않은 경우 [인덱서 개요로](search-indexer-overview.md) 시작한 다음 [기술 집합을](cognitive-search-working-with-skillsets.md) 계속 하여 보강 파이프라인에 대해 알아봅니다. 주요 개념에 대한 자세한 내용은 [증분 보강](cognitive-search-incremental-indexing-conceptual.md)을 참조하십시오.

## <a name="enable-caching-on-an-existing-indexer"></a>기존 인덱서에서 캐싱 사용

기술 집합이 이미 있는 기존 인덱서가 있는 경우 이 섹션의 단계를 따라 캐싱을 추가합니다. 일회성 작업으로 증분 처리가 적용되기 전에 인덱서를 완전히 재설정하고 다시 실행해야 합니다.

> [!TIP]
> 개념 증명으로 이 [포털 퀵스타트를](cognitive-search-quickstart-blob.md) 통해 필요한 개체를 만든 다음 Postman 또는 포털을 사용하여 업데이트를 수행할 수 있습니다. 청구 가능한 인지 서비스 리소스를 첨부할 수 있습니다. 인덱서를 여러 번 실행하면 모든 단계를 완료하기 전에 무료 일일 할당이 소진됩니다.

### <a name="step-1-get-the-indexer-definition"></a>1단계: 인덱서 정의 받기

데이터 원본, 기술 집합, 인덱스와 같은 구성 요소가 있는 유효한 기존 인덱서로 시작합니다. 인덱서를 실행할 수 있어야 합니다. 

API 클라이언트를 사용하여 [GET 인덱서 요청을](https://docs.microsoft.com/rest/api/searchservice/get-indexer) 생성하여 인덱서의 현재 구성을 가져옵니다. 미리 보기 API 버전을 GET 인덱서에 `cache` 사용하면 null로 설정된 속성이 정의에 추가됩니다.

```http
GET https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]?api-version=2019-05-06-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
```

응답에서 인덱서 정의를 복사합니다.

### <a name="step-2-modify-the-cache-property-in-the-indexer-definition"></a>2단계: 인덱서 정의에서 캐시 속성 수정

기본적으로 속성은 `cache` null입니다. API 클라이언트를 사용하여 캐시 구성을 설정합니다(포털이 이 미립자 업데이트를 지원하지 않음). 

다음과 같은 필수 및 선택적 속성을 포함하도록 캐시 개체를 수정합니다. 

+ 는 `storageConnectionString` 필수이며 Azure 저장소 연결 문자열로 설정해야 합니다. 
+ `enableReprocessing` 부울 속성은 기본적으로`true` 선택 사항이며 증분 보강이 활성화되어 있음을 나타냅니다. 필요한 경우 새 문서 `false` 인덱싱과 같은 다른 리소스 집약적인 작업이 진행 중인 동안 증분 처리를 일시 `true` 중단하도록 설정한 다음 나중에 다시 뒤집을 수 있습니다.

```json
{
    "name": "<YOUR-INDEXER-NAME>",
    "targetIndexName": "<YOUR-INDEX-NAME>",
    "dataSourceName": "<YOUR-DATASOURCE-NAME>",
    "skillsetName": "<YOUR-SKILLSET-NAME>",
    "cache" : {
        "storageConnectionString" : "<YOUR-STORAGE-ACCOUNT-CONNECTION-STRING>",
        "enableReprocessing": true
    },
    "fieldMappings" : [],
    "outputFieldMappings": [],
    "parameters": []
}
```

### <a name="step-3-reset-the-indexer"></a>3단계: 인덱서 재설정

모든 문서가 일관된 상태인지 확인하기 위해 기존 인덱서에 대한 증분 보강을 설정할 때 인덱서를 재설정해야 합니다. 이 작업에 포털 또는 API 클라이언트와 [리셋 인덱서 REST API를](https://docs.microsoft.com/rest/api/searchservice/reset-indexer) 사용할 수 있습니다.

```http
POST https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]/reset?api-version=2019-05-06-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
```

### <a name="step-4-save-the-updated-definition"></a>4단계: 업데이트된 정의 저장

PUT 요청으로 [인덱서를 업데이트하면](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/update-indexer) 요청 본문에 캐시 속성이 있는 업데이트된 인덱서 정의가 포함되어야 합니다. 400을 얻는 경우 인덱서 정의를 확인하여 모든 요구 사항(데이터 원본, 기술 집합, 인덱스)이 충족되는지 확인합니다.

```http
PUT https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]?api-version=2019-05-06-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
{
    "name" : "<YOUR-INDEXER-NAME>",
    ...
    "cache": {
        "storageConnectionString": "<YOUR-STORAGE-ACCOUNT-CONNECTION-STRING>",
        "enableReprocessing": true
    }
}
```

이제 인덱서에 다른 GET 요청을 발급하면 서비스의 응답에 `ID` 캐시 개체에 속성이 포함됩니다. 이 인덱서에서 처리하는 각 문서의 모든 캐시된 결과 및 중간 상태를 포함하는 컨테이너 이름에 가태적인 문자열이 추가됩니다. ID는 Blob 저장소의 캐시 이름을 고유하게 지정하는 데 사용됩니다.

    "cache": {
        "ID": "<ALPHA-NUMERIC STRING>",
        "enableReprocessing": true,
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<YOUR-STORAGE-ACCOUNT>;AccountKey=<YOUR-STORAGE-KEY>;EndpointSuffix=core.windows.net"
    }

### <a name="step-5-run-the-indexer"></a>5단계: 인덱서 실행

인덱서를 실행하려면 포털 또는 API를 사용할 수 있습니다. 포털에서 인덱서 목록에서 인덱서를 선택하고 **실행 을**클릭합니다. 포털을 사용하는 한 가지 장점은 인덱서 상태를 모니터링하고 작업 기간 및 처리되는 문서 수를 기록할 수 있다는 것입니다. 포털 페이지는 몇 분마다 새로 고쳐집니다.

또는 REST를 사용하여 [인덱서를 실행할](https://docs.microsoft.com/rest/api/searchservice/run-indexer)수 있습니다.

```http
POST https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]/run?api-version=2019-05-06-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
```

인덱서가 실행된 후 Azure Blob 저장소에서 캐시를 찾을 수 있습니다. 컨테이너 이름은 다음과 같은 형식입니다.`ms-az-search-indexercache-<YOUR-CACHE-ID>`

> [!NOTE]
> 인덱서를 재설정하고 다시 실행하면 콘텐츠가 캐시될 수 있도록 전체 재생성됩니다. 모든 인지 농축은 모든 문서에서 다시 실행됩니다.
>

### <a name="step-6-modify-a-skillset-and-confirm-incremental-enrichment"></a>6단계: 기술 집합 수정 및 증분 보강 확인

기술 집합을 수정하려면 포털 또는 API를 사용할 수 있습니다. 예를 들어 텍스트 변환을 사용하는 경우 증분 `en` 보강의 개념 증명 테스트에 대해 다른 언어로 `es` 또는 다른 언어로 간단한 인라인 변경이 충분합니다.

인덱서를 다시 실행합니다. 보강된 문서 트리의 해당 부분만 업데이트됩니다. [포털 퀵스타트를](cognitive-search-quickstart-blob.md) 개념 증명으로 사용하여 텍스트 번역 기술을 'es'로 수정한 경우 원래 문서 14개 대신 8개의 문서만 업데이트됩니다. 변환 프로세스의 영향을 받지 않는 이미지 파일은 캐시에서 다시 사용됩니다.

## <a name="enable-caching-on-new-indexers"></a>새 인덱서에서 캐싱 사용

새 인덱서에 대한 증분 보강을 설정하려면 `cache` [인덱서 만들기(2019-05-06-Preview)를](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/create-indexer)호출할 때 인덱서 정의 페이로드에 속성을 포함하기만 하면 됩니다. 


```json
{
    "name": "<YOUR-INDEXER-NAME>",
    "targetIndexName": "<YOUR-INDEX-NAME>",
    "dataSourceName": "<YOUR-DATASOURCE-NAME>",
    "skillsetName": "<YOUR-SKILLSET-NAME>",
    "cache" : {
        "storageConnectionString" : "<YOUR-STORAGE-ACCOUNT-CONNECTION-STRING>",
        "enableReprocessing": true
    },
    "fieldMappings" : [],
    "outputFieldMappings": [],
    "parameters": []
    }
}
```

## <a name="checking-for-cached-output"></a>캐시된 출력 확인

캐시는 인덱서에서 생성, 사용 및 관리하며 해당 내용은 사람이 읽을 수 있는 형식으로 표시되지 않습니다. 캐시가 사용되는지 여부를 확인하는 가장 좋은 방법은 인덱서를 실행하고 실행 시간 및 문서 수에 대한 전후 메트릭을 비교하는 것입니다. 

예를 들어 스캔한 문서의 이미지 분석 및 OCR(광학 문자 인식)으로 시작한 다음 결과 텍스트의 다운스트림 분석으로 시작하는 기술 집합을 가정합니다. 다운스트림 텍스트 기술을 수정하는 경우 인덱서에서는 이전에 처리된 모든 이미지 및 OCR 콘텐츠를 캐시에서 검색하고 편집내용으로 표시된 텍스트 관련 변경 내용만 업데이트 및 처리할 수 있습니다. 문서 수에서 더 적은 수의 문서(예: 원래 실행의 14/14와 는 반대로) 더 적은 수의 문서, 실행 시간이 짧아지고 청구서에 청구되는 요금이 더 적을 것으로 예상할 수 있습니다.

## <a name="working-with-the-cache"></a>캐시 작업

캐시가 작동하면 인덱서가 [호출될](https://docs.microsoft.com/rest/api/searchservice/run-indexer) 때마다 캐시를 검사하여 기존 출력의 어느 부분을 사용할 수 있는지 확인합니다. 

다음 표에는 다양한 API가 캐시와 어떻게 관련되는지 요약되어 있습니다.

| API           | 캐시 영향     |
|---------------|------------------|
| [인덱서 만들기(2019-05-06-미리 보기)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/create-indexer) | 인덱서 정의가 지정한 경우 캐시를 만드는 것을 포함하여 처음 사용할 때 인덱서를 만들고 실행합니다. |
| [인덱서 실행](https://docs.microsoft.com/rest/api/searchservice/run-indexer) | 필요에 따라 보강 파이프라인을 실행합니다. 이 API는 캐시가 있는 경우 캐시에서 읽거나 업데이트된 인덱서 정의에 캐싱을 추가한 경우 캐시를 만듭니다. 캐싱이 활성화된 인덱서를 실행하면 캐시된 출력을 사용할 수 있는 경우 인덱서에서 단계를 생략합니다. 이 API의 일반적으로 사용 가능한 API 또는 미리 보기 API 버전을 사용할 수 있습니다.|
| [인덱서 다시 설정](https://docs.microsoft.com/rest/api/searchservice/reset-indexer)| 증분 인덱싱 정보의 인덱서를 지웁습니다. 다음 인덱서 실행(주문형 또는 일정)은 모든 기술을 다시 실행하고 캐시를 다시 빌드하는 것을 포함하여 처음부터 완전히 재처리됩니다. 인덱서를 삭제하고 다시 만드는 것과 기능적으로 동일합니다. 이 API의 일반적으로 사용 가능한 API 또는 미리 보기 API 버전을 사용할 수 있습니다.|
| [기술 초기화 (2019-05-06-미리 보기)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/reset-skills) | 기술을 수정하지 않은 경우에도 다음 인덱서 실행에서 다시 실행할 기술을 지정합니다. 캐시는 그에 따라 업데이트됩니다. 지식 저장소 또는 검색 인덱스와 같은 출력은 캐시에서 재사용 가능한 데이터와 업데이트된 기술당 새 콘텐츠를 사용하여 새로 고쳐집니다. |

캐시에 발생하는 일을 제어하는 자세한 내용은 [캐시 관리를](cognitive-search-incremental-indexing-conceptual.md#cache-management)참조하십시오.

## <a name="next-steps"></a>다음 단계

증분 보강은 기술 집합을 포함하는 인덱서에 적용됩니다. 다음 단계로 기술 집합 설명서를 방문하여 개념 및 구성을 이해합니다. 

또한 캐시를 활성화하면 특정 동작을 재정의하거나 강제하는 방법을 포함하여 캐싱에 영향을 주는 매개 변수 및 API에 대해 알고 싶을 것입니다. 자세한 내용은 다음 링크를 참조하세요.

+ [기술 집합 개념 및 구성](cognitive-search-working-with-skillsets.md)
+ [기술 집합을 만드는 방법](cognitive-search-defining-skillset.md)
+ [증분 보강 및 캐싱 소개](cognitive-search-incremental-indexing-conceptual.md)
