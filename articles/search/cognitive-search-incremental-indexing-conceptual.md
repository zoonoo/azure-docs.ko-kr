---
title: 증분 인덱싱 (미리 보기)
titleSuffix: Azure Cognitive Search
description: AI 보강 파이프라인을 구성 하 여 기술, 기술력과, 인덱서 또는 데이터 원본에 대 한 업데이트를 처리 하는 최종 일관성으로 데이터를 구동 합니다. 이 기능은 현재 공개 미리 보기 상태입니다.
manager: nitinme
author: Vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 76ab8784f04f3c67e4ea8062505931783048dea1
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113591"
---
# <a name="what-is-incremental-indexing-in-azure-cognitive-search"></a>Azure Cognitive Search의 증분 인덱싱 이란?

> [!IMPORTANT] 
> 증분 인덱싱은 현재 공개 미리 보기로 제공 됩니다. 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요. 이 기능은 [REST API 버전 2019-05-06-미리 보기](search-api-preview.md)에서 제공됩니다. 지금은 포털 또는 .NET SDK가 지원 되지 않습니다.

증분 인덱싱은 보강의 콘텐츠를 기술에 추가 하 여 보강 파이프라인에서 개별 단계를 처리 하 고 다시 처리 하는 기능을 제공 하는 Azure Cognitive Search의 새로운 기능입니다. 이 작업을 수행 하는 경우에도 현금 투자를 처리할 수 있을 뿐만 아니라 더 효율적인 시스템을 만들 수 있습니다. 구조와 콘텐츠를 캐시 하는 경우 인덱서는 변경 된 기술을 확인 하 고 다운스트림 종속 기술 뿐만 아니라 수정 된 기술을 실행할 수 있습니다. 

증분 인덱싱을 사용하면 현재 버전의 보강 파이프라인에서 최소한의 작업을 수행하여 인덱스의 모든 문서에 대한 일관성을 보장합니다. 모든 권한이 필요한 시나리오의 경우 세분화 된 컨트롤을 사용 하 여 예상 동작을 재정의할 수 있습니다. 구성에 대 한 자세한 내용은 [증분 인덱싱 설정](search-howto-incremental-index.md)을 참조 하세요.

## <a name="indexer-cache"></a>인덱서 캐시

증분 인덱싱은 인덱서 캐시를 보강 파이프라인에 추가합니다. 인덱서는 문서 크래킹의 결과와 모든 문서에 대한 각 기술의 출력을 캐시합니다. 기술 세트가 업데이트되면 변경된 기술 또는 다운스트림 기술만 다시 실행됩니다. 업데이트된 결과는 캐시에 기록되고, 문서는 인덱스와 지식 저장소에서 업데이트됩니다.

실제로 캐시는 스토리지 계정입니다. 검색 서비스 내의 모든 인덱스는 인덱서 캐시에 대해 동일한 스토리지 계정을 공유할 수 있습니다. 각 인덱서에는 고유하고 변경할 수 없는 캐시 식별자가 할당됩니다.

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

기존 인덱서에서이 속성을 처음으로 설정 하는 경우에도이 속성을 다시 설정 해야 합니다. 그러면 데이터 원본의 모든 문서가 다시 처리 됩니다. 증분 인덱싱의 목표는 인덱스의 문서가 데이터 원본 및 현재 버전의 기술 세트와 일치하도록 하는 것입니다. 인덱스를 다시 설정하는 것은 이전 버전의 기술 세트에서 보강한 문서를 제거하므로 이 일관성을 유지하기 위한 첫 번째 단계입니다. 일관된 기준으로 시작하려면 인덱서를 다시 설정해야 합니다.

### <a name="cache-lifecycle"></a>캐시 수명 주기

캐시의 수명 주기는 인덱서에서 관리합니다. 인덱서의 `cache` 속성이 null로 설정 되거나 연결 문자열이 변경 된 경우 기존 캐시가 삭제 됩니다. 캐시 수명 주기는 인덱서 수명 주기와도 연결됩니다. 인덱서가 삭제되면 연결된 캐시도 삭제됩니다.

### <a name="indexer-cache-mode"></a>인덱서 캐시 모드

인덱서 캐시는 데이터가 캐시에만 기록되거나 데이터가 캐시에 기록되어 문서를 다시 보강하는 데 사용되는 모드에서 작동할 수 있습니다.  캐시의 `enableReprocessing` 속성을 `false`로 설정하여 증분 보강을 일시적으로 중단하고, 나중에 증분 보강을 다시 시작하고 `true`로 설정하여 최종 일관성을 유지할 수 있습니다. 이 컨트롤은 문서 모음에서 일관성을 유지하는 것보다 새 문서 인덱스의 우선 순위를 지정하려는 경우에 특히 유용합니다.

## <a name="change-detection-override"></a>변경 내용 검색 재정의

증분 인덱싱을 사용하면 보강 파이프라인의 모든 측면을 세밀하게 제어할 수 있습니다. 이 컨트롤을 사용하면 변경으로 인해 의도하지 않은 결과가 발생할 수 있는 상황을 처리할 수 있습니다. 예를 들어 기술 세트를 편집하고 사용자 지정 기술의 URL을 업데이트하면 인덱서에서 해당 기술에 대해 캐시된 결과를 무효화합니다. 엔드포인트를 다른 VM으로 이동하거나 새 액세스 키를 사용하여 기술을 다시 배포하는 경우에만 기존 문서를 다시 처리하지 않는 것이 좋습니다.

인덱서가 명시적으로 필요한 강화 수행 하 게 하려면 기술를 업데이트할 때 `disableCacheReprocessingChangeDetection` querystring 매개 변수를 `true`로 선택적으로 설정할 수 있습니다. 이 매개 변수가 설정되면 기술 세트에 대한 업데이트만 커밋되고, 변경으로 인해 기존 모음에 미치는 영향은 평가하지 않습니다.

다음 예제에서는 querystring 사용을 보여 줍니다. 이는 &으로 구분 된 키 값 쌍을 포함 하는 요청의 일부입니다. 

```http
PUT https://customerdemos.search.windows.net/skillsets/callcenter-text-skillset?api-version=2019-05-06-Preview&disableCacheReprocessingChangeDetection=true
```

## <a name="cache-invalidation"></a>캐시 무효화

이 시나리오와 반대되는 경우 새 버전의 사용자 지정 기술을 배포할 수 있고 보강 파이프라인이 변경되지 않지만, 특정 기술을 무효화하고 업데이트된 모델의 이점을 반영하기 위해 영향을 받는 모든 문서를 다시 처리해야 합니다. 이러한 경우 기술 세트에 대한 기술 무효화 작업을 호출할 수 있습니다. 기술 재설정 API는 무효화해야 하는 캐시의 기술 출력 목록이 포함된 POST 요청을 허용합니다. 기술 다시 설정 API에 대 한 자세한 내용은 [인덱서 다시 설정 (검색 REST API)](https://docs.microsoft.com/rest/api/searchservice/reset-indexer)을 참조 하세요.

## <a name="bi-directional-change-detection"></a>양방향 변경 검색

인덱서는 새 문서를 앞으로 이동하여 처리할 뿐만 아니라 이제는 이전에 처리한 문서를 뒤로 이동하여 일관성을 유지할 수도 있습니다. 이 새로운 기능을 사용하는 경우 보강 파이프라인 구성요소의 변경으로 인해 인덱서 작업이 수행되는 방법을 이해해야 합니다. 인덱서는 캐시 된 콘텐츠를 기준으로 무효화 되거나 일치 하지 않는 변경을 식별 하는 경우 수행할 작업을 큐에 대기 시킵니다.

### <a name="invalidating-changes"></a>변경 내용 무효화

변경 내용 무효화는 드물게 발생하지만 보강 파이프라인의 상태에 상당한 영향을 미칩니다. 변경 내용이 무효화되면 전체 캐시가 더 이상 유효하지 않습니다. 변경 내용 무효화의 예로 데이터 원본이 업데이트되는 경우가 있습니다. 저장소 계정에서 키를 회전 하는 것과 같이 변경으로 인해 캐시가 무효화 되지 않아야 하는 경우에는 `ignoreResetRequirement` querystring 매개 변수를 특정 리소스의 업데이트 작업에서 `true`으로 설정 하 여 작업이 수행 되도록 해야 합니다. 거부 되지 않습니다.

캐시를 무효화할 수 있는 변경 내용의 전체 목록은 다음과 같습니다.

* 데이터 원본 유형에 대한 변경
* 데이터 원본 컨테이너에 대한 변경
* 데이터 원본 자격 증명
* 데이터 원본 변경 내용 검색 정책
* 데이터 원본 삭제 검색 정책
* 인덱서 필드 매핑
* 인덱서 매개 변수
    * 구문 분석 모드
    * 제외된 파일 이름 확장명
    * 인덱싱된 파일 이름 확장명
    * 대용량 문서에 대한 스토리지 메타데이터만 인덱싱
    * 구분 기호로 분리된 텍스트 헤더
    * 구분 기호로 분리된 텍스트 구분 기호
    * 문서 루트
    * 이미지 작업(이미지를 추출하는 방법에 대한 변경)

### <a name="inconsistent-changes"></a>일관성 없는 변경

일관성 없는 변경의 예로 기술을 수정하는 기술 세트에 대한 업데이트가 있습니다. 이 수정으로 인해 캐시의 일부가 일치하지 않을 수 있습니다. 인덱서에서 작업을 식별하여 해당 작업이 다시 일치하도록 만듭니다.  

캐시 불일치가 발생하는 변경의 전체 목록은 다음과 같습니다.

* 기술 세트의 기술 유형이 다릅니다. 기술의 Odata 형식이 업데이트되었습니다.
* 기술 관련 매개 변수(예: URL, 기본값 또는 기타 매개 변수)가 업데이트되었습니다.
* 기술 출력이 변경되고, 기술에서 추가 또는 다른 출력을 반환합니다.
* 기술 업데이트 결과가 다른 상위 구조이며, 기술 연결이 변경되었습니다(예: 기술 입력).
* 이 기술에 대한 입력을 제공하는 기술이 업데이트되면 업스트림 기술이 무효화됩니다.
* 지식 저장소 프로젝션 위치가 업데이트되어 문서를 다시 프로젝션합니다.
* 지식 저장소 프로젝션이 변경되어 문서를 다시 프로젝션합니다.
* 인덱서에서 출력 필드 매핑이 변경되어 문서를 인덱스로 다시 프로젝션합니다.

## <a name="rest-api-reference-for-incremental-indexing"></a>증분 인덱싱에 대 한 REST API 참조

REST `api-version=2019-05-06-Preview`는 인덱서, 기술력과 및 데이터 소스에 대 한 추가 기능을 사용 하 여 증분 인덱싱을 위한 Api를 제공 합니다. 참조 설명서에는 현재 이러한 추가 기능이 포함 되어 있지 않습니다. 다음 섹션에서는 API 변경 사항을 설명 합니다.

### <a name="indexers"></a>인덱서

[Create 인덱서](https://docs.microsoft.com/rest/api/searchservice/create-indexer) 및 [Update 인덱서](https://docs.microsoft.com/rest/api/searchservice/update-indexer) 는 이제 캐시와 관련 된 새 속성을 노출 합니다.

* `StorageAccountConnectionString`: 중간 결과를 캐시 하는 데 사용 되는 저장소 계정에 대 한 연결 문자열입니다.

* `CacheId`: `cacheId`는 `annotationCache` 저장소 계정 내에서이 인덱서의 캐시로 사용 될 컨테이너의 식별자입니다. 이 캐시는 이 인덱서에서만 고유하며, 인덱서를 삭제하고 동일한 이름으로 다시 만들면 `cacheId`가 다시 생성됩니다. `cacheId`는 설정할 수 없으며, 항상 서비스에서 생성합니다.

* `EnableReprocessing`: 기본적으로 `true`로 설정 하면 `false`로 설정 된 문서는 캐시에 계속 기록 되지만 캐시 데이터를 기반으로 하는 기존 문서는 다시 처리 되지 않습니다.

일부 인덱서 ( [데이터 소스](https://docs.microsoft.com/rest/api/searchservice/create-data-source)를 통해)는 쿼리를 통해 데이터를 검색 합니다. 데이터를 검색 하는 쿼리의 경우 인덱서는 새 쿼리 문자열 매개 변수도 지원 합니다. 업데이트 작업에서 캐시를 무효화 하지 않아야 하는 경우에는 `ignoreResetRequirement`를 `true`으로 설정 해야 합니다.

### <a name="skillsets"></a>기술 세트

기술 세트는 새 작업을 지원하지 않지만 새 querystring 매개 변수를 지원합니다. 현재 작업을 기반으로 하여 기존 문서를 업데이트하지 않으려면 `disableCacheReprocessingChangeDetection`을 `true`로 설정해야 합니다.

### <a name="datasources"></a>Datasources

Datasources는 새 작업을 지원하지 않지만 새 querystring 매개 변수를 지원합니다. 업데이트 작업에서 캐시를 무효화하지 않아야 하는 경우 `ignoreResetRequirement`를 `true`로 설정해야 합니다.

## <a name="best-practices"></a>모범 사례

증분 인덱싱을 사용하려면 새 인덱서에서 캐시 속성을 설정하여 증분 인덱싱을 구성하거나 기존 인덱서를 다시 설정하여 캐시 속성을 설정하는 것이 좋습니다.

쉽게 검색할 수 없는 데이터에서 의도 하지 않은 불일치를 일으킬 수 있으므로 `ignoreResetRequirement`을 사용 합니다.

## <a name="takeaways"></a>핵심 내용

증분 인덱싱은 데이터 원본, 최신 버전의 기술 및 인덱서를 포함 하 여 데이터 원본에서 보강 파이프라인의 모든 측면으로 변경 내용 추적을 확장 하는 강력한 기능입니다. 기술, 기술 세트 또는 보강이 진화함에 따라 보강 파이프라인은 문서에서 최종 일관성을 유지하면서 가능한 최소한의 작업만 수행하도록 합니다.

## <a name="next-steps"></a>다음 단계

캐시를 기존 인덱서에 추가하여 증분 인덱싱을 시작하거나 새 인덱서를 정의할 때 캐시를 추가합니다.

> [!div class="nextstepaction"]
> [증분 인덱싱 설정](search-howto-incremental-index.md)
