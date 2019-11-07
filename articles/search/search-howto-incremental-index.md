---
title: 보강 내용 기반 변경 내용 추적의 증분 인덱싱 (미리 보기) 설정
titleSuffix: Azure Cognitive Search
description: 인지 기술에서 제어 되는 처리에 대해 변경 내용 추적을 사용 하도록 설정 하 고 보강 콘텐츠의 상태를 유지 합니다. 이 기능은 현재 공개 미리 보기로 제공됩니다.
author: vkurpad
manager: eladz
ms.author: vikurpad
ms.service: cognitive-search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 74631ee3167c65e59fbd05f53fe5327d1b532dba
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73719931"
---
# <a name="how-to-set-up-incremental-indexing-of-enriched-documents-in-azure-cognitive-search"></a>Azure Cognitive Search에서 보강 문서의 증분 인덱싱을 설정 하는 방법

> [!IMPORTANT] 
> 증분 인덱싱은 현재 공개 미리 보기로 제공 됩니다. 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요. 이 기능은 [REST API 버전 2019-05-06-미리 보기](search-api-preview.md)에서 제공됩니다. 지금은 포털 또는 .NET SDK가 지원 되지 않습니다.

이 문서에서는 Azure Cognitive Search 보강 파이프라인을 통해 이동 하는 보강 문서에 상태 및 캐싱을 추가 하 여 지원 되는 데이터 원본에서 문서를 증분 인덱싱할 수 있도록 하는 방법을 보여 줍니다. 기본적으로 기술는 상태 비저장 이며 해당 컴포지션의 모든 부분을 변경 하려면 인덱서를 전체 다시 실행 해야 합니다. 증분 인덱싱을 사용 하는 경우 인덱서는 변경 된 파이프라인 부분을 확인 하 고, 변경 되지 않은 부분에 대해 기존 강화를 다시 사용 하 고, 변경 하는 단계에 대 한 강화를 개정할 수 있습니다. 캐시 된 콘텐츠는 Azure Storage에 배치 됩니다.

인덱서를 설정 하는 방법을 잘 모르는 경우 [인덱서 개요](search-indexer-overview.md) 를 시작 하 고 [기술력과](cognitive-search-working-with-skillsets.md) 를 계속 진행 하 여 보강 파이프라인에 대해 알아보세요. 주요 개념에 대 한 자세한 배경 정보는 [증분 인덱싱](cognitive-search-incremental-indexing-conceptual.md)을 참조 하세요.

## <a name="modify-an-existing-indexer"></a>기존 인덱서 수정

기존 인덱서가 있는 경우 다음 단계에 따라 증분 인덱싱을 사용 하도록 설정 합니다.

### <a name="step-1-get-the-indexer"></a>1 단계: 인덱서 가져오기

필수 데이터 원본 및 인덱스가 이미 정의 된 유효한 기존 인덱서를 시작 합니다. 인덱서를 실행할 수 있어야 합니다. API 클라이언트를 사용 하 여 증분 인덱싱을 추가할 인덱서의 현재 구성을 가져오기 위한 [get 요청](https://docs.microsoft.com/rest/api/searchservice/get-indexer) 을 생성 합니다.

```http
GET https://[service name].search.windows.net/indexers/[your indexer name]?api-version=2019-05-06-Preview
Content-Type: application/json
api-key: [admin key]
```

### <a name="step-2-add-the-cache-property"></a>2 단계: cache 속성 추가

GET 요청에서 응답을 편집 하 여 `cache` 속성을 인덱서에 추가 합니다. 캐시 개체에는 단일 속성만 필요 하며,이는 Azure Storage 계정에 대 한 연결 문자열입니다.

```json
    "cache": {
        "storageConnectionString": "[your storage connection string]"
    }
```

### <a name="step-3-reset-the-indexer"></a>3 단계: 인덱서 다시 설정

> [!NOTE]
> 인덱서를 다시 설정 하면 데이터 원본에 있는 모든 문서가 다시 처리 되어 콘텐츠가 캐시 될 수 있습니다. 모든 인지 강화은 모든 문서에서 다시 실행 됩니다.
>

모든 문서가 일관 된 상태가 되도록 기존 인덱서에 대 한 증분 인덱싱을 설정할 때 인덱서가 다시 설정 되어야 합니다. [REST API](https://docs.microsoft.com/rest/api/searchservice/reset-indexer)를 사용 하 여 인덱서를 다시 설정 합니다.

```http
POST https://[service name].search.windows.net/indexers/[your indexer name]/reset?api-version=2019-05-06-Preview
Content-Type: application/json
api-key: [admin key]
```

### <a name="step-4-save-the-updated-definition"></a>4 단계: 업데이트 된 정의 저장

PUT 요청을 사용 하 여 인덱서 정의를 업데이트 합니다. 요청 본문은 업데이트 된 인덱서 정의를 포함 해야 합니다.

```http
PUT https://[service name].search.windows.net/indexers/[your indexer name]/reset?api-version=2019-05-06-Preview
Content-Type: application/json
api-key: [admin key]
{
    "name" : "your indexer name",
    ...
    "cache": {
        "storageConnectionString": "[your storage connection string]",
        "enableReprocessing": true
    }
}
```

이제 인덱서에 다른 GET 요청을 실행 하는 경우 서비스의 응답에는 캐시 개체에 `cacheId` 속성이 포함 됩니다. `cacheId`은 캐시 된 모든 결과와이 인덱서가 처리 하는 각 문서의 중간 상태를 포함 하는 컨테이너의 이름입니다.

## <a name="enable-incremental-indexing-on-new-indexers"></a>새 인덱서에 대해 증분 인덱싱을 사용 하도록 설정

새 인덱서에 대해 증분 인덱싱을 설정 하려면 인덱서 정의 페이로드에 `cache` 속성을 포함 합니다. `2019-05-06-Preview` 버전의 API를 사용 하 고 있는지 확인 합니다.

## <a name="overriding-incremental-indexing"></a>증분 인덱싱 재정의

구성 된 경우 증분 인덱싱은 인덱싱 파이프라인 전체에서 변경 내용을 추적 하 고 문서를 인덱스 및 프로젝션 전반의 최종 일관성으로 바꿉니다. 경우에 따라 인덱싱 파이프라인에 대 한 업데이트의 결과로 인덱서가 추가 작업을 수행 하지 않도록이 동작을 재정의 해야 합니다. 예를 들어 datasource 연결 문자열을 업데이트 하려면 datasource가 변경 될 때 모든 문서의 인덱서 다시 설정 및 다시 인덱스를 설정 해야 합니다. 그러나 새 키를 사용 하 여 연결 문자열을 업데이트 하는 경우에는 기존 문서에 대 한 업데이트를 변경 하지 않는 것이 좋습니다. 반대로 인덱싱 파이프라인에 대 한 변경 내용이 없는 경우에도 인덱서가 캐시를 무효화 하 고 문서를 보강 하 게 할 수 있습니다. 예를 들어 새 모델을 사용 하 여 사용자 지정 기술을 재배포 하 고 모든 문서에서 스킬을 다시 실행 하려는 경우 인덱서를 무효화할 수 있습니다.

### <a name="override-reset-requirement"></a>다시 설정 요구 사항 재정의

인덱싱 파이프라인을 변경 하는 경우 캐시를 무효화 하는 변경 내용으로 인해 인덱서를 다시 설정 해야 합니다. 인덱서 파이프라인을 변경 하 고 변경 내용 추적이 캐시를 무효화 하지 않도록 하려면 인덱서 또는 데이터 원본에 대 한 작업에 대 한 `true` `ignoreResetRequirement` querystring 매개 변수를로 설정 해야 합니다.

### <a name="override-change-detection"></a>변경 내용 검색 재정의

기술를 업데이트 하 여 문서가 일관 되지 않은 것으로 플래그가 지정 되는 경우 (예: 기술이 다시 배포 될 때 사용자 지정 기술 URL 업데이트) 기술 업데이트에서 `disableCacheReprocessingChangeDetection` 쿼리 문자열 매개 변수를 `true`로 설정 합니다.

### <a name="force-change-detection"></a>강제로 변경 내용 검색

인스턴스화된 새 버전의 사용자 지정 기술 배포와 같이 인덱싱 파이프라인이 외부 엔터티에 대 한 변경 내용을 인식 하 게 하려는 경우, 기술 정의를 편집 하 여 특정 기술, 특히 URL을 기술 업데이트 해야 합니다. 검색을 변경 하 고 해당 기술에 대 한 캐시를 무효화 합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 기술력과을 포함 하는 인덱서의 증분 인덱싱을 설명 합니다. 정보를 추가로 파악 하려면 Azure Cognitive Search의 모든 인덱싱 시나리오에 적용 되는 일반적인 다시 인덱싱에 대 한 문서를 검토 합니다.

+ [Azure Cognitive Search 인덱스를 다시 작성 하는 방법](search-howto-reindex.md)입니다. 
+ [Azure Cognitive Search에서 대량 데이터 집합을 인덱싱하는 방법](search-howto-large-index.md)입니다. 
