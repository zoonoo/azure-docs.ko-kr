---
title: 증분 인덱싱 소개 (미리 보기)
titleSuffix: Azure Cognitive Search
description: AI 보강 파이프라인을 구성 하 여 기술, 기술력과, 인덱서 또는 데이터 원본에 대 한 업데이트를 처리 하는 최종 일관성으로 데이터를 구동 합니다.
manager: nitinme
author: Vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: ea3bcfc25040f09b6871d85412ac64061ec2f9e8
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73549124"
---
# <a name="what-is-incremental-indexing-in-azure-cognitive-search"></a>Azure Cognitive Search의 증분 인덱싱 이란?

> [!Note]
> 증분 인덱싱은 미리 보기 상태 이며 프로덕션 용도로는 사용할 수 없습니다. 이 기능은 [REST API 버전 2019-05-06-미리 보기](search-api-preview.md)에서 제공됩니다. 지금은 포털 또는 .NET SDK가 지원 되지 않습니다.
>

증분 인덱싱은 보강의 콘텐츠를 기술에 추가 하 여 보강 파이프라인에서 개별 단계를 처리 하 고 다시 처리 하는 기능을 제공 하는 Azure Cognitive Search의 새로운 기능입니다. 이 작업을 수행 하는 경우에도 현금 투자를 처리할 수 있을 뿐만 아니라 더 효율적인 시스템을 만들 수 있습니다. 구조와 콘텐츠를 캐시 하는 경우 인덱서는 변경 된 기술을 확인 하 고 다운스트림 종속 기술 뿐만 아니라 수정 된 기술을 실행할 수 있습니다. 

증분 인덱싱을 사용 하는 경우 보강 파이프라인의 현재 버전은 인덱스의 모든 문서에 대해 일관성을 보장 하기 위해 최소한의 작업을 수행 합니다. 모든 권한이 필요한 시나리오의 경우 세분화 된 컨트롤을 사용 하 여 예상 동작을 재정의할 수 있습니다. 구성에 대 한 자세한 내용은 [증분 인덱싱 설정](search-howto-incremental-index.md)을 참조 하세요.

## <a name="indexer-cache"></a>인덱서 캐시

증분 인덱싱은 보강 파이프라인에 인덱서 캐시를 추가 합니다. 인덱서는 문서 크랙 및 모든 문서에 대 한 각 기술 출력의 결과를 캐시 합니다. 기술 업데이트 되 면 변경 된 또는 다운스트림 기술만 다시 실행 됩니다. 업데이트 된 결과가 캐시에 기록 되 고 문서는 인덱스와 기술 자료 저장소에서 업데이트 됩니다.

실제로 캐시는 저장소 계정입니다. 검색 서비스 내의 모든 인덱스는 인덱서 캐시에 대해 동일한 저장소 계정을 공유할 수 있습니다. 각 인덱서에는 고유 하 고 변경할 수 없는 캐시 식별자가 할당 됩니다.

### <a name="cache-configuration"></a>캐시 구성

증분 인덱싱에서 애플리케이션을 빌드하고을 시작 하려면 인덱서에 `cache` 속성을 설정 해야 합니다. 다음 예에서는 캐싱이 설정 된 인덱서를 보여 줍니다. 이 구성의 특정 부분에 대해서는 다음 섹션에서 설명 합니다.

```json
{
    "name": "myIndexerName",
    "targetIndexName": "myIndex",
    "dataSourceName": "myDatasource",
    "skillsetName": "mySkillset",
    "cache" : {
        "storageConnectionString" : "Your storage account connection string",
        "enableReprocessing": true,
        "id" : "Auto generated Id you do not need to set"
    },
    "fieldMappings" : [],
    "outputFieldMappings": [],
    "parameters":{}
}
```

기존 인덱서에서이 속성을 처음으로 설정 하는 경우에도이 속성을 다시 설정 해야 합니다. 그러면 데이터 원본의 모든 문서가 다시 처리 됩니다. 증분 인덱싱의 목표는 인덱스의 문서를 데이터 원본 및 기술의 현재 버전과 일치 하 게 만드는 것입니다. 인덱스를 다시 설정 하는 것은 이전 버전의 기술에 의해 보강 문서를 제거 하므로이 일관성을 유지 하기 위한 첫 번째 단계입니다. 일관 된 기준선으로 시작 하려면 인덱서를 다시 설정 해야 합니다.

### <a name="cache-lifecycle"></a>캐시 수명 주기

캐시의 수명 주기는 인덱서를 통해 관리 됩니다. 인덱서의 `cache` 속성이 null로 설정 되거나 연결 문자열이 변경 된 경우 기존 캐시가 삭제 됩니다. 캐시 수명 주기는 인덱서 수명 주기와도 관련이 있습니다. 인덱서를 삭제 하면 연결 된 캐시도 삭제 됩니다.

### <a name="indexer-cache-mode"></a>인덱서 캐시 모드

인덱서 캐시는 데이터를 캐시에만 기록 하거나 데이터를 캐시에 쓰고 문서를 다시 보강 하는 데 사용 되는 모드에서 작동할 수 있습니다.  캐시의 `enableReprocessing` 속성을 `false`로 설정 하 고 나중에 증분 보강를 다시 시작 하 고 `true`로 설정 하 여 최종 일관성을 유지 하는 방법으로 증분 보강을 일시적으로 중단할 수 있습니다. 이 컨트롤은 문서 모음에서 일관성을 유지 하기 위해 새 문서 인덱싱의 우선 순위를 지정 하려는 경우에 특히 유용 합니다.

## <a name="change-detection-override"></a>변경 내용 검색 재정의

증분 인덱싱은 보강 파이프라인의 모든 측면에 대 한 세부적인 제어를 제공 합니다. 이 컨트롤을 사용 하면 변경으로 인해 의도 하지 않은 결과가 발생 하는 상황을 처리할 수 있습니다. 예를 들어 기술를 편집 하 고 사용자 지정 기술에 대 한 URL을 업데이트 하면 인덱서가 해당 기술에 대 한 캐시 된 결과를 무효화 합니다. 끝점을 다른 VM으로 이동 하거나 새 액세스 키로 기술을 다시 배포 하는 경우에는 기존 문서를 다시 처리 하지 않는 것이 좋습니다.

인덱서가 명시적으로 필요한 강화 수행 하 게 하려면 기술를 업데이트할 때 `disableCacheReprocessingChangeDetection` querystring 매개 변수를 `true`로 선택적으로 설정할 수 있습니다. 이 매개 변수를 설정 하면 기술에 대 한 업데이트만 커밋되고 기존 모음에 대 한 영향에 대 한 변경 내용이 평가 되지 않습니다.

다음 예제에서는 querystring 사용을 보여 줍니다. 이는 &으로 구분 된 키 값 쌍을 포함 하는 요청의 일부입니다. 

```http
PUT https://customerdemos.search.windows.net/skillsets/callcenter-text-skillset?api-version=2019-05-06-Preview&disableCacheReprocessingChangeDetection=true
```

## <a name="cache-invalidation"></a>캐시 무효화

이 시나리오는 새 버전의 사용자 지정 기술을 배포할 수 있고, 보강 파이프라인 내에서 아무 것도 없지만, 특정 기술이 무효화 되 고, 영향을 받는 모든 문서가 업데이트 된 모델의 혜택을 반영 하기 위해 다시 처리 되어야 합니다. 이러한 경우 기술에서 무효화 된 기술 작업을 호출할 수 있습니다. 다시 설정 기술 API는 무효화 되어야 하는 캐시의 기술 출력 목록이 포함 된 POST 요청을 허용 합니다. 기술 다시 설정 API에 대 한 자세한 내용은 [인덱서 다시 설정 (검색 REST API)](https://docs.microsoft.com/rest/api/searchservice/reset-indexer)을 참조 하세요.

## <a name="bi-directional-change-detection"></a>양방향 변경 검색

인덱서는 앞으로 이동 하 여 새 문서를 처리할 수 있을 뿐만 아니라 이전에 이전에 처리 한 문서를 일관성으로 이전 하 고 사용할 수 있습니다. 이 새로운 기능을 사용 하 여 보강 파이프라인 구성 요소에 대 한 변경으로 인해 인덱서 작업이 어떻게 수행 되는지 이해 하는 것이 중요 합니다. 인덱서는 캐시 된 콘텐츠를 기준으로 무효화 되거나 일치 하지 않는 변경을 식별 하는 경우 수행할 작업을 큐에 대기 시킵니다.

### <a name="invalidating-changes"></a>변경 내용 무효화

변경 내용 무효화는 드물게 발생 하지만 보강 파이프라인의 상태에 상당한 영향을 미칠 수 있습니다. 무효화 변경은 전체 캐시가 더 이상 유효 하지 않습니다. 무효화 변경의 예는 데이터 원본이 업데이트 되는 위치입니다. 저장소 계정에서 키를 회전 하는 것과 같이 변경으로 인해 캐시가 무효화 되지 않아야 하는 경우에는 `ignoreResetRequirement` querystring 매개 변수를 특정 리소스의 업데이트 작업에서 `true`으로 설정 하 여 작업이 수행 되도록 해야 합니다. 거부 되지 않습니다.

다음은 캐시를 무효화 하는 전체 변경 내용 목록입니다.

* 데이터 원본 유형으로 변경
* 데이터 원본 컨테이너로 변경
* 데이터 원본 자격 증명
* 데이터 원본 변경 검색 정책
* 데이터 원본 삭제 검색 정책
* 인덱서 필드 매핑
* 인덱서 매개 변수
    * 구문 분석 모드
    * 제외 된 파일 이름 확장명
    * 인덱싱된 파일 이름 확장명
    * 대용량 문서에 대 한 저장소 메타 데이터만 인덱싱
    * 구분 기호로 분리 된 텍스트 헤더
    * 구분 기호로 분리 된 텍스트 구분 기호
    * 문서 루트
    * 이미지 작업 (이미지를 추출 하는 방법에 대 한 변경)

### <a name="inconsistent-changes"></a>일관성 없는 변경 내용

일관성 없는 변경의 예는 기술을 수정 하는 기술 업데이트입니다. 이렇게 수정 하면 캐시의 일부가 일치 하지 않을 수 있습니다. 인덱서는 작업을 식별 하 여 다시 일관 된 작업을 수행 합니다.  

캐시 불일치를 초래 하는 전체 변경 내용 목록:

* 기술의 기술은 다른 유형입니다. 기술에 대 한 odata 형식이 업데이트 됩니다.
* 기술 관련 매개 변수 업데이트 (예: url, 기본값 또는 기타 매개 변수)
* 기술 출력 변경 내용, 기술에서 추가 또는 다른 출력 반환
* 그 결과, 기술 업데이트는 다른 상위 구조, 기술 체인이 변경 되었습니다. 즉, 기술 입력
* 이 기술에 대 한 입력을 제공 하는 기술이 업데이트 된 경우의 업스트림 기술 무효화
* 기술 자료 저장소 프로젝션 위치를 업데이트 하 여 문서를 다시 프로젝션 합니다.
* 기술 자료 저장소 프로젝션에 대 한 변경 내용으로 인해 문서가 다시 프로젝션 됨
* 인덱서에서 출력 필드 매핑이 변경 되어 문서를 인덱스로 다시 프로젝션 합니다.

## <a name="rest-api-reference-for-incremental-indexing"></a>증분 인덱싱에 대 한 REST API 참조

REST `api-version=2019-05-06-Preview`는 인덱서, 기술력과 및 데이터 소스에 대 한 추가 기능을 사용 하 여 증분 인덱싱을 위한 Api를 제공 합니다. 참조 설명서에는 현재 이러한 추가 기능이 포함 되어 있지 않습니다. 다음 섹션에서는 API 변경 사항을 설명 합니다.

### <a name="indexers"></a>인덱서

[Create 인덱서](https://docs.microsoft.com/rest/api/searchservice/create-indexer) 및 [Update 인덱서](https://docs.microsoft.com/rest/api/searchservice/update-indexer) 는 이제 캐시와 관련 된 새 속성을 노출 합니다.

* `StorageAccountConnectionString`: 중간 결과를 캐시 하는 데 사용 되는 저장소 계정에 대 한 연결 문자열입니다.

* `CacheId`: `cacheId`는 `annotationCache` 저장소 계정 내에서이 인덱서의 캐시로 사용 될 컨테이너의 식별자입니다. 이 캐시는이 인덱서에서 고유 하며, 동일한 이름을 사용 하 여 인덱서를 삭제 하 고 다시 만들면 `cacheId` 다시 생성 됩니다. `cacheId`는 설정할 수 없으며, 항상 서비스에 의해 생성 됩니다.

* `EnableReprocessing`: 기본적으로 `true`로 설정 하면 `false`로 설정 된 문서는 캐시에 계속 기록 되지만 캐시 데이터를 기반으로 하는 기존 문서는 다시 처리 되지 않습니다.

일부 인덱서 ( [데이터 소스](https://docs.microsoft.com/rest/api/searchservice/create-data-source)를 통해)는 쿼리를 통해 데이터를 검색 합니다. 데이터를 검색 하는 쿼리의 경우 인덱서는 새 쿼리 문자열 매개 변수도 지원 합니다. 업데이트 작업에서 캐시를 무효화 하지 않아야 하는 경우에는 `ignoreResetRequirement`를 `true`으로 설정 해야 합니다.

### <a name="skillsets"></a>기술 세트

기술력과는 새 작업을 지원 하지 않지만 새 querystring 매개 변수를 지원 합니다. 현재 작업을 기반으로 기존 문서를 업데이트 하지 않으려면 `disableCacheReprocessingChangeDetection`를 `true`으로 설정 해야 합니다.

### <a name="datasources"></a>데이터 원본

데이터 원본은 새 작업을 지원 하지 않지만 새로운 querystring 매개 변수를 지원 합니다. 업데이트 작업에서 캐시를 무효화 하지 않아야 하는 경우에는 `ignoreResetRequirement`를 `true`으로 설정 해야 합니다.

## <a name="best-practices"></a>모범 사례

증분 인덱싱을 사용 하는 권장 방법은 새 인덱서에 cache 속성을 설정 하 여 증분 인덱싱을 구성 하거나 기존 인덱서를 다시 설정 하 고 cache 속성을 설정 하는 것입니다.

쉽게 검색할 수 없는 데이터에서 의도 하지 않은 불일치를 일으킬 수 있으므로 `ignoreResetRequirement`을 사용 합니다.

## <a name="takeaways"></a>핵심 내용

증분 인덱싱은 데이터 원본, 최신 버전의 기술 및 인덱서를 포함 하 여 데이터 원본에서 보강 파이프라인의 모든 측면으로 변경 내용 추적을 확장 하는 강력한 기능입니다. 기술, 기술력과 또는 강화가 발전 함에 따라 보강 파이프라인은 최대한의 작업을 수행 하면서도 문서를 최종 일관성으로 유지 합니다.

## <a name="next-steps"></a>다음 단계

새 인덱서를 정의할 때 기존 인덱서에 캐시를 추가 하거나 캐시를 추가 하 여 증분 인덱싱을 시작 합니다.

> [!div class="nextstepaction"]
> [증분 인덱싱 설정](search-howto-incremental-index.md)
