---
title: 인덱서 실행 또는 다시 설정
titleSuffix: Azure Cognitive Search
description: 인덱서, 기술 또는 개별 문서를 다시 설정 하 여 전체 또는 일부 및 인덱스 또는 정보 저장소를 새로 고칩니다.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/09/2021
ms.openlocfilehash: bf8a4e51e23f438265af706914a6bc73ec30f64d
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101667671"
---
# <a name="how-to-run-or-reset-indexers-skills-or-documents"></a>인덱서, 기술 또는 문서를 실행 하거나 다시 설정 하는 방법

인덱서 실행은 인덱서를 처음 만들 [때, 요청](search-indexer-overview.md)시 인덱서를 실행 하는 경우 또는 일정에 따라 인덱서를 설정할 때 발생할 수 있습니다. 초기 실행 후 인덱서는 내부 "상위 워터 마크"를 통해 인덱싱되는 검색 문서를 추적 합니다. 마커는 API에 노출 되지 않지만 내부적으로 인덱서는 다음 실행 시 남은 위치를 선택할 수 있도록 인덱싱이 중지 된 위치를 알 수 있습니다.

처음부터 다시 처리 하려면 인덱서를 다시 설정 하 여 상위 워터 마크를 지울 수 있습니다. 다시 설정 Api는 개체 계층의 감소 수준에서 사용할 수 있습니다.

+ 전체 검색 모음 ( [다시 설정 인덱서](#reset-indexers)사용)
+ 특정 문서 또는 문서 목록 ( [문서 다시 설정-미리 보기](#reset-docs)사용)
+ 문서의 특정 기술 또는 보강 ( [스킬 다시 설정-미리 보기](#reset-skills)사용)

다시 설정 Api는 [AI 보강](cognitive-search-concept-intro.md) 시나리오에 적용 되는 캐시 된 콘텐츠를 새로 고치거 나 상위 워터 마크를 지우고 인덱스를 다시 작성 하는 데 사용 됩니다.

다시 설정 하 고 실행 한 후 기존 문서와 새 문서를 다시 처리할 수 있지만 이전 실행에서 생성 된 검색 인덱스에서 분리 된 검색 문서를 제거 하지는 않습니다. 삭제에 대 한 자세한 내용은 [문서 추가, 업데이트 또는 삭제](/rest/api/searchservice/addupdate-or-delete-documents)를 참조 하세요.

## <a name="run-indexers"></a>인덱서 실행

[Create 인덱서](/rest/api/searchservice/create-indexer) 는 비활성화 된 상태 ("disabled": true)로 만들지 않는 한 인덱서를 만들고 실행 합니다. 첫 번째 실행은 개체를 만드는 데에도 더 오랜 시간이 걸립니다.

[인덱서 실행](/rest/api/searchservice/run-indexer) 은 검색 인덱스를 데이터 원본과 동기화 하는 데 필요한 항목만 검색 하 고 처리 합니다. Blob storage에는 기본 제공 변경 검색 기능이 있습니다. 다른 데이터 원본 (예: Azure SQL 또는 Cosmos DB)은 인덱서가 새 행과 업데이트 된 행만 읽을 수 있도록 변경 내용 검색을 위해 구성 되어야 합니다.

다음 방법 중 하나를 사용 하 여 인덱서를 실행할 수 있습니다.

+ Azure Portal 인덱서 페이지에서 **Run** 명령을 사용 하 여
+ [인덱서 실행 (REST)](/rest/api/searchservice/run-indexer)
+ Azure .NET SDK의 [Runindexers 메서드](/dotnet/api/azure.search.documents.indexes.searchindexerclient.runindexer) (또는 다른 SDK의 동일한 runindexers 메서드 사용)

인덱서 실행에는 다음과 같은 제한이 적용 됩니다.

+ 최대 인덱서 작업 수는 복제본 당 1 개 (동시 작업 없음)입니다.

  인덱서 실행이 이미 용량에 있는 경우 "인덱서를 실행 하지 못했습니다. ' <인덱서 이름> '을 (를) 실행 하지 못했습니다. 오류:" 다른 인덱서 호출이 현재 진행 중입니다. 동시 호출은 허용 되지 않습니다. "

+ 기술를 사용 하는 경우 최대 실행 시간은 2 시간이 고, 그렇지 않으면 24 시간입니다. 

  인덱서를 일정에 배치 하 여 처리를 확장할 수 있습니다. 무료 계층에는 낮은 실행 시간 제한이 있습니다. 전체 목록은 [인덱서 제한](search-limits-quotas-capacity.md#indexer-limits) 을 참조 하세요.

<a name="reset-indexers"></a>

## <a name="reset-an-indexer"></a>인덱서 다시 설정

인덱서를 다시 설정 하는 것이 전부입니다. 검색 인덱스 내에서 원래 인덱서에서 채운 모든 검색 문서는 전체 처리를 위해 표시 됩니다. 새 문서를 발견 하면 기본 원본이 인덱스에 검색 문서로 추가 됩니다. 기술 및 [캐싱을](search-howto-incremental-index.md)사용 하도록 인덱서가 구성 된 경우 기술가 다시 실행 되 고 캐시가 새로 고쳐집니다.

위에서 설명한 방법 중 하나를 사용 하 여 인덱서를 실행 한 다음 이러한 방법 중 하나를 사용 하 여 인덱서를 다시 설정할 수 있습니다.

+ Azure Portal, 인덱서 페이지의 **Reset** 명령 사용
+ [인덱서 다시 설정 (REST)](/rest/api/searchservice/reset-indexer)
+ Azure .NET SDK의 [resetindexers 메서드](/dotnet/api/azure.search.documents.indexes.searchindexerclient.resetindexer) (또는 다른 SDK의 동일한 runindexer 메서드 사용)

실행이 완료 된 후 다시 설정 플래그가 지워집니다. 데이터 원본에 대해 작동 가능한 되는 일반적인 변경 검색 논리는 다음 실행 시에 다시 시작 되 고 나머지 데이터 집합에서 새로 또는 업데이트 된 값을 선택 합니다.

> [!NOTE]
> 다시 설정 요청은 다시 처리 되는 항목 (인덱서, 기술 또는 문서)을 결정 하지만 달리 인덱서 런타임 동작에는 영향을 주지 않습니다. 인덱서에 런타임 매개 변수, 필드 매핑, 캐싱, 일괄 처리 옵션 등이 있는 경우 해당 설정은 다시 설정한 후 인덱서를 실행할 때 모두 적용 됩니다.

<a name="reset-skills"></a>

## <a name="reset-skills-preview"></a>기술 재설정 (미리 보기)

> [!IMPORTANT] 
> [다시 설정 기술은](/rest/api/searchservice/preview-api/reset-skills) 미리 보기로 제공 되며 미리 보기 REST API 통해서만 제공 됩니다. 미리 보기 기능은 [추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에서 있는 그대로 제공 됩니다.

기술력과 있는 인덱서의 경우 해당 기술 및 해당 출력에 종속 된 모든 다운스트림 기술을 강제로 처리 하도록 특정 기술을 다시 설정할 수 있습니다. [캐시 된 강화](search-howto-incremental-index.md) 도 새로 고쳐집니다. 스킬을 다시 설정 하면 캐시 된 기술 결과가 무효화 됩니다 .이는 새 버전의 기술이 배포 되어 있고 인덱서가 모든 문서에 대해 해당 기술을 다시 실행 하려는 경우에 유용 합니다. 

[다시 설정 기술은](/rest/api/searchservice/preview-api/reset-skills) REST를 통해 사용할 수 있습니다 **`api-version=2020-06-30-Preview`** .

```http
POST https://[service name].search.windows.net/skillsets/[skillset name]/resetskills?api-version=2020-06-30-Preview
{
    "skillNames" : [
        "#1",
        "#5",
        "#6"
    ]
}
```

위의 예제에 나와 있는 것 처럼 개별 기술을 지정할 수 있지만, 이러한 기술 중 하나라도 나열 되지 않은 기술 (#4를 통해 #2)의 출력이 필요한 경우 캐시에서 필요한 정보를 제공 하지 않는 한 목록에 없는 기술이 실행 됩니다. 이를 true로 설정 하려면 #4를 통해 #2 기술에 대해 캐시 된 강화 #1에 대 한 종속성이 없어야 합니다 (다시 설정에 대해 나열 됨).

기술이 지정 되지 않은 경우 전체 기술 실행 되며 캐싱이 설정 된 경우 캐시도 새로 고쳐집니다.

<a name="reset-docs"></a>

## <a name="reset-docs-preview"></a>문서 다시 설정 (미리 보기)

> [!IMPORTANT] 
> [문서 다시 설정](/rest/api/searchservice/preview-api/reset-documents) 은 공개 미리 보기로 제공 되며 미리 보기 REST API 통해서만 제공 됩니다. 미리 보기 기능은 [추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에서 있는 그대로 제공 됩니다.

문서 [다시 설정 API](/rest/api/searchservice/preview-api/reset-documents) 는 특정 문서를 새로 고칠 수 있도록 문서 키 목록을 수락 합니다. 지정 된 경우 다시 설정 매개 변수는 기본 데이터의 다른 변경 내용에 관계 없이 처리 되는 항목의 유일한 결정이 됩니다. 예를 들어 마지막 인덱서 실행 이후 20 개의 blob을 추가 하거나 업데이트 했지만 하나의 문서만 다시 설정 하면 해당 문서만 처리 됩니다.

문서 별로 해당 검색 문서의 모든 필드가 데이터 원본의 값으로 새로 고쳐집니다. 새로 고칠 필드를 선택 하 고 선택할 수 없습니다. 

문서가 기술를 통해 보강 고 캐시 된 데이터가 있는 경우에는 지정 된 문서에 대해서만 기술이 호출 되 고 처리 된 문서에 대해 캐시 된가 업데이트 됩니다.

이 API를 처음 테스트할 때 다음 Api는 동작의 유효성을 검사 하 고 테스트 하는 데 도움이 됩니다.

+ API 버전을 사용 하 여 [인덱서 상태를 가져와](/rest/api/searchservice/get-indexer-status) `2020-06-30-Preview` 다시 설정 상태 및 실행 상태를 확인 합니다. 상태 응답의 끝에서 다시 설정 요청에 대 한 정보를 찾을 수 있습니다.
+ 문서를 API 버전으로 [다시 설정](/rest/api/searchservice/preview-api/reset-documents) `2020-06-30-Preview` 하 여 처리할 문서를 지정 합니다.
+ 인덱서를 [실행](/rest/api/searchservice/run-indexer) 하 여 인덱서 (모든 API 버전)를 실행 합니다.
+ [문서를 검색](/rest/api/searchservice/search-documents) 하 여 업데이트 된 값을 확인 하 고 값이 확실 하지 않은 경우 문서 키도 반환 합니다. `"select": "<field names>"`응답에 표시 되는 필드를 제한 하려는 경우를 사용 합니다.

### <a name="formulate-and-send-the-reset-request"></a>재설정 요청 작성 및 보내기

```http
POST https://[service name].search.windows.net/indexers/[indexer name]/resetdocs?api-version=2020-06-30-Preview
{
    "documentKeys" : [
        "1001",
        "4452"
    ]
}
```

요청에 제공 된 문서 키는 검색 인덱스의 값 이며 데이터 원본의 해당 필드와 다를 수 있습니다. 키 값이 확실 하지 않은 경우 값을 반환 하는 [쿼리를 보냅니다](search-query-create.md) . `select` 를 사용 하 여 문서 키 필드만 반환할 수 있습니다.

여러 검색 문서로 구문 분석 되는 blob의 경우 (예: [jsonLines 또는 jsonArrays](search-howto-index-json-blobs.md)또는 [delimitedText](search-howto-index-csv-blobs.md)를 구문 분석 모드로 사용 하는 경우), 문서 키는 인덱서에 의해 생성 되며 알 수 없는 것일 수 있습니다. 이 경우 문서 키에 대 한 쿼리는 올바른 값을 제공 하는 데 사용할 수 있습니다.

다른 키를 사용 하 여 API를 여러 번 호출 하면 새 키가 문서 키 다시 설정 목록에 추가 됩니다. 매개 변수를 true로 설정 하 여 API를 호출 **`overwrite`** 하면 요청의 페이로드에서 다시 설정할 현재 문서 키 목록이 덮어쓰여집니다.

```http
POST https://[service name].search.windows.net/indexers/[indexer name]/resetdocs?api-version=2020-06-30-Preview
{
    "documentKeys" : [
        "200",
        "630"
    ],
    "overwrite": true
}
```

## <a name="check-reset-status"></a>다시 설정 상태 확인

다시 설정 상태를 확인 하 고 처리를 위해 큐에 대기 중인 문서 키를 확인 하려면에서 [인덱서 상태 가져오기](/rest/api/searchservice/get-indexer-status) 를 사용 **`api-version=06-30-2020-Preview`** 합니다. 미리 보기 API는 **`currentState`** 인덱서 상태 가져오기 응답의 끝에서 찾을 수 있는 섹션을 반환 합니다.

"모드"는 **`indexingAllDocs`** AI 보강을 통해 채워지는 필드에 대해 잠재적으로 모든 문서가 영향을 받을 수 있기 때문에 재설정 기술에 대 한 것입니다.

문서 다시 설정의 경우 모드는로 설정 됩니다 **`indexingResetDocs`** . 인덱서는 문서 다시 설정 호출에서 제공 된 모든 문서 키가 처리 될 때까지이 상태를 유지 하 고 작업이 진행 되는 동안 다른 인덱서 작업은 실행 되지 않습니다. 문서 키 목록에서 모든 문서를 찾으려면 각 문서를 해독 하 고 키에서 일치 해야 하며, 데이터 집합이 클 경우 시간이 오래 걸릴 수 있습니다. Blob 컨테이너에 수백 개의 blob이 포함 되어 있고 다시 설정 하려는 docs가 끝에 있으면 인덱서는 다른 모든 항목을 먼저 확인할 때까지 일치 하는 blob을 찾지 못합니다.

```json
"currentState": {
    "mode": "indexingResetDocs",
    "allDocsInitialTrackingState": "{\"LastFullEnumerationStartTime\":\"2021-02-06T19:02:07.0323764+00:00\",\"LastAttemptedEnumerationStartTime\":\"2021-02-06T19:02:07.0323764+00:00\",\"NameHighWaterMark\":null}",
    "allDocsFinalTrackingState": "{\"LastFullEnumerationStartTime\":\"2021-02-06T19:02:07.0323764+00:00\",\"LastAttemptedEnumerationStartTime\":\"2021-02-06T19:02:07.0323764+00:00\",\"NameHighWaterMark\":null}",
    "resetDocsInitialTrackingState": null,
    "resetDocsFinalTrackingState": null,
    "resetDocumentKeys": [
        "200",
        "630"
    ]
}
```

문서를 다시 처리 한 후 인덱서는 모드로 반환 되 **`indexingAllDocs`** 고 다음 실행 시 다른 새 문서 또는 업데이트 된 문서를 처리 합니다.

## <a name="next-steps"></a>다음 단계

다시 설정 Api는 다음 인덱서 실행의 범위를 알리는 데 사용 됩니다. 실제 처리를 위해서는 주문형 인덱서 실행을 호출 하거나 예약 된 작업이 작업을 완료 하도록 허용 해야 합니다. 실행이 완료 된 후 인덱서는 일정 또는 요청 시 처리에 있든 관계 없이 정상적인 처리로 돌아갑니다.

인덱서 작업을 다시 설정 하 고 다시 실행 한 후에는 검색 서비스의 상태를 모니터링 하거나 진단 로깅을 통해 자세한 정보를 가져올 수 있습니다.

+ [인덱서 작업 (REST)](/rest/api/searchservice/indexer-operations)
+ [검색 인덱서 상태 모니터링](search-howto-monitor-indexers.md)
+ [로그 데이터 수집 및 분석](search-monitor-logs.md)
+ [인덱서 예약](search-howto-schedule-indexers.md)