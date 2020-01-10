---
title: 증분 보강 (미리 보기)
titleSuffix: Azure Cognitive Search
description: Azure Storage의 AI 보강 파이프라인에서 중간 콘텐츠 및 증분 변경 내용을 캐시 하 여 기존의 처리 된 문서에 대 한 투자를 유지 합니다. 이 기능은 현재 공개 미리 보기로 제공됩니다.
manager: nitinme
author: Vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: a5b12a426e52c3b80c58a30b320b2f746bbe990d
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/10/2020
ms.locfileid: "75832186"
---
# <a name="introduction-to-incremental-enrichment-and-caching-in-azure-cognitive-search"></a>Azure Cognitive Search의 증분 보강 및 캐싱 소개

> [!IMPORTANT] 
> 증분 보강 현재 공개 미리 보기 상태입니다. 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요. 이 기능은 [REST API 버전 2019-05-06-미리 보기](search-api-preview.md)에서 제공됩니다. 지금은 포털 또는 .NET SDK가 지원 되지 않습니다.

증분 보강는 보강 파이프라인에 캐싱과 상태 저장을 추가 하 여 기존 출력에 대 한 투자를 유지 하면서 특정 수정의 영향을 받은 문서만 변경 합니다. 이를 통해 처리에 대 한 현금 투자 (특히 OCR 및 이미지 처리)를 유지할 뿐만 아니라 보다 효율적인 시스템을 만들 수도 있습니다. 구조와 콘텐츠를 캐시 하는 경우 인덱서는 변경 된 기술을 확인 하 고 다운스트림 종속 기술 뿐만 아니라 수정 된 기술을 실행할 수 있습니다. 

## <a name="indexer-cache"></a>인덱서 캐시

증분 보강는 보강 파이프라인에 캐시를 추가 합니다. 인덱서는 문서 크랙 및 모든 문서에 대 한 각 기술 출력의 결과를 캐시 합니다. 기술 세트가 업데이트되면 변경된 기술 또는 다운스트림 기술만 다시 실행됩니다. 업데이트 된 결과가 캐시에 기록 되 고 문서는 검색 인덱스나 기술 자료 저장소에서 업데이트 됩니다.

실제로 캐시는 Azure Storage 계정의 blob 컨테이너에 저장 됩니다. 검색 서비스 내의 모든 인덱스는 인덱서 캐시에 대해 동일한 스토리지 계정을 공유할 수 있습니다. 각 인덱서에는 사용 중인 컨테이너에 대해 고유 하 고 변경할 수 없는 캐시 식별자가 할당 됩니다.

## <a name="cache-configuration"></a>캐시 구성

애플리케이션을 빌드하고를 증분 보강 시작 하려면 인덱서에 `cache` 속성을 설정 해야 합니다. 다음 예에서는 캐싱이 설정 된 인덱서를 보여 줍니다. 이 구성의 특정 부분에 대해서는 다음 섹션에서 설명 합니다. 자세한 내용은 [Set up 증분 보강](search-howto-incremental-index.md)를 참조 하세요.

```json
{
    "name": "myIndexerName",
    "targetIndexName": "myIndex",
    "dataSourceName": "myDatasource",
    "skillsetName": "mySkillset",
    "cache" : {
        "storageConnectionString" : "Your storage account connection string",
        "enableReprocessing": true
    },
    "fieldMappings" : [],
    "outputFieldMappings": [],
    "parameters": []
}
```

기존 인덱서에이 속성을 설정 하려면 인덱서를 다시 설정 하 고 다시 실행 해야 합니다. 그러면 데이터 원본의 모든 문서가 다시 처리 됩니다. 이 단계는 이전 버전의 기술에 의해 보강 문서를 제거 하는 데 필요 합니다. 

## <a name="cache-management"></a>캐시 관리

캐시의 수명 주기는 인덱서에서 관리합니다. 인덱서의 `cache` 속성이 null로 설정 되거나 연결 문자열이 변경 된 경우 다음 인덱서 실행 시 기존 캐시가 삭제 됩니다. 캐시 수명 주기는 인덱서 수명 주기와도 연결됩니다. 인덱서가 삭제되면 연결된 캐시도 삭제됩니다.

증분 보강는 사용자가 개입할 필요 없이 변경 내용을 감지 하 고 대응 하도록 설계 되었지만 기본 동작을 재정의 하는 데 사용할 수 있는 매개 변수는 다음과 같습니다.

+ 캐싱 일시 중단
+ 기술 검사 무시
+ 데이터 원본 검사 무시
+ 강제 기술 평가

### <a name="suspend-caching"></a>캐싱 일시 중단

캐시의 `enableReprocessing` 속성을 `false`로 설정하여 증분 보강을 일시적으로 중단하고, 나중에 증분 보강을 다시 시작하고 `true`로 설정하여 최종 일관성을 유지할 수 있습니다. 이 컨트롤은 문서 모음에서 일관성을 유지하는 것보다 새 문서 인덱스의 우선 순위를 지정하려는 경우에 특히 유용합니다.

### <a name="bypass-skillset-evaluation"></a>바이패스 기술 evaluation

해당 기술의 기술 및 다시 처리를 수정 하는 것은 일반적으로 직접 수행 됩니다. 그러나 기술에 대 한 일부 변경 내용은 다시 처리 되지 않습니다. 예를 들어 사용자 지정 기술을 새 위치에 배포 하거나 새 액세스 키로 배포할 수 있습니다. 대부분의 경우 기술 자체의 수준에 영향을 주지 않는 주변의 수정이 있습니다. 

기술에 대 한 변경 내용이 실제로 피상적인 경우 `disableCacheReprocessingChangeDetection` 매개 변수를 `true`으로 설정 하 여 기술 evaluation을 재정의 해야 합니다.

1. Update 기술를 호출 하 고 기술 정의를 수정 합니다.
1. 요청에 `disableCacheReprocessingChangeDetection=true` 매개 변수를 추가 합니다.
1. 변경 내용을 제출 합니다.

이 매개 변수를 설정 하면 기술 정의에 대 한 업데이트만 커밋되고 기존 모음의 효과에 대 한 변경 내용은 평가 되지 않습니다.

다음 예에서는 매개 변수가 있는 Update 기술 요청을 보여 줍니다.

```http
PUT https://customerdemos.search.windows.net/skillsets/callcenter-text-skillset?api-version=2019-05-06-Preview&disableCacheReprocessingChangeDetection=true
```

### <a name="bypass-data-source-validation-checks"></a>데이터 원본 유효성 검사 무시

대부분의 데이터 원본 정의를 변경 하면 캐시가 무효화 됩니다. 그러나 연결 문자열을 변경 하거나 저장소 계정에서 키를 회전 하는 것과 같이 변경으로 캐시가 무효화 되지 않아야 하는 경우에는 데이터 원본 업데이트에`ignoreResetRequirement` 매개 변수를 추가 합니다. 이 매개 변수를 `true`로 설정 하면 모든 개체를 다시 작성 하 고 처음부터 채우기 위해 다시 설정 조건을 트리거하지 않고 커밋을 진행할 수 있습니다.

```http
PUT https://customerdemos.search.windows.net/datasources/callcenter-ds?api-version=2019-05-06-Preview&ignoreResetRequirement=true
```

### <a name="force-skillset-evaluation"></a>강제 기술 평가

캐시의 목적은 불필요 한 처리를 방지 하는 것 이지만 인덱서가 검색 하지 않는 기술 또는 기술 (예: 사용자 지정 기술 같은 외부 구성 요소에 대 한 변경 내용)를 변경한 경우를 가정 합니다. 

이 경우 기술 [다시 설정](preview-api-resetskills.md) API를 사용 하 여 해당 기술 출력에 대 한 종속성이 있는 다운스트림 기술을 포함 하 여 특정 기술에 대 한 다시 처리를 적용할 수 있습니다. 이 API는 무효화 되 고 다시 실행 해야 하는 기술 목록과 함께 POST 요청을 수락 합니다. 스킬을 다시 설정한 후 인덱서를 실행 하 여 작업을 실행 합니다.

## <a name="change-detection"></a>변경 내용 검색

캐시를 사용 하도록 설정 하면 인덱서가 파이프라인 컴퍼지션의 변경을 평가 하 여 다시 사용할 수 있는 콘텐츠 및 다시 처리 해야 하는 콘텐츠를 확인 합니다. 이 섹션에서는 캐시를 완전히 무효화 한 후 증분 처리를 트리거하는 변경 내용을 열거 합니다. 

### <a name="changes-that-invalidate-the-cache"></a>캐시를 무효화 하는 변경 내용

변경 내용이 무효화되면 전체 캐시가 더 이상 유효하지 않습니다. 변경 내용 무효화의 예로 데이터 원본이 업데이트되는 경우가 있습니다. 캐시를 무효화할 수 있는 변경 내용의 전체 목록은 다음과 같습니다.

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

### <a name="changes-that-trigger-incremental-processing"></a>증분 처리를 트리거하는 변경 내용

증분 처리는 기술 정의를 평가 하 고 문서 트리의 영향을 받는 부분을 선택적으로 업데이트 하 여 다시 실행할 기술을 결정 합니다. 다음은 증분 보강 변경 되는 전체 변경 내용 목록입니다.

* 기술 세트의 기술 유형이 다릅니다. 기술의 Odata 형식이 업데이트되었습니다.
* 기술 관련 매개 변수(예: URL, 기본값 또는 기타 매개 변수)가 업데이트되었습니다.
* 기술 출력이 변경되고, 기술에서 추가 또는 다른 출력을 반환합니다.
* 기술 업데이트 결과가 다른 상위 구조이며, 기술 연결이 변경되었습니다(예: 기술 입력).
* 이 기술에 대한 입력을 제공하는 기술이 업데이트되면 업스트림 기술이 무효화됩니다.
* 지식 저장소 프로젝션 위치가 업데이트되어 문서를 다시 프로젝션합니다.
* 지식 저장소 프로젝션이 변경되어 문서를 다시 프로젝션합니다.
* 인덱서에서 출력 필드 매핑이 변경되어 문서를 인덱스로 다시 프로젝션합니다.

## <a name="api-reference-content-for-incremental-enrichment"></a>증분 보강 대 한 API 참조 콘텐츠

REST `api-version=2019-05-06-Preview`는 인덱서, 기술력과 및 데이터 소스에 대 한 추가 기능을 제공 하는 증분 보강 Api를 제공 합니다. [공식 참조 설명서](https://docs.microsoft.com/rest/api/searchservice/) 는 일반적으로 사용할 수 있는 api에 대 한 것 이며 미리 보기 기능을 다루지 않습니다. 다음 섹션에서는 영향을 받는 Api에 대 한 참조 콘텐츠를 제공 합니다.

사용 정보 및 예제는 [증분 보강에 대 한 캐싱 구성](search-howto-incremental-index.md)에서 찾을 수 있습니다.

### <a name="indexers"></a>인덱서

[Create 인덱서](https://docs.microsoft.com/rest/api/searchservice/create-indexer) 및 [Update 인덱서](https://docs.microsoft.com/rest/api/searchservice/update-indexer) 는 이제 캐시와 관련 된 새 속성을 노출 합니다.

+ `StorageAccountConnectionString`: 중간 결과를 캐시 하는 데 사용 되는 저장소 계정에 대 한 연결 문자열입니다.

+ `EnableReprocessing`: 기본적으로 `true`로 설정 하면 `false`로 설정 된 문서는 캐시에 계속 기록 되지만 캐시 데이터를 기반으로 하는 기존 문서는 다시 처리 되지 않습니다.

+ `ID` (읽기 전용): `ID`는 `annotationCache` 저장소 계정 내에서이 인덱서의 캐시로 사용 될 컨테이너의 식별자입니다. 이 캐시는 이 인덱서에서만 고유하며, 인덱서를 삭제하고 동일한 이름으로 다시 만들면 `ID`가 다시 생성됩니다. `ID`는 설정할 수 없으며, 항상 서비스에서 생성합니다.

### <a name="skillsets"></a>기술 세트

+ [Update 기술](https://docs.microsoft.com/rest/api/searchservice/update-skillset) 는 요청에 새 매개 변수를 지원 합니다. `disableCacheReprocessingChangeDetection`는 현재 작업을 기반으로 기존 문서를 업데이트 하지 않으려는 경우 `true`로 설정 해야 합니다.

+ [스킬 다시 설정](preview-api-resetskills.md) 은 기술을 무효화 하는 데 사용 되는 새로운 작업입니다.

### <a name="datasources"></a>Datasources

+ 일부 인덱서는 쿼리를 통해 데이터를 검색 합니다. 데이터를 검색 하는 쿼리의 경우 [업데이트 데이터 원본](https://docs.microsoft.com/rest/api/searchservice/update-datasource) 에서 요청 `ignoreResetRequirement`에 대 한 새 매개 변수를 지원 합니다 .이 매개 변수는 업데이트 작업에서 캐시를 무효화 하지 않아야 하는 경우 `true`로 설정 되어야 합니다.

쉽게 검색할 수 없는 데이터에서 의도 하지 않은 불일치를 일으킬 수 있으므로 `ignoreResetRequirement`을 사용 합니다.

## <a name="next-steps"></a>다음 단계

증분 보강는 기술력과 및 AI 보강 변경 내용 추적을 확장 하는 강력한 기능입니다. 기술력과 발전 함에 따라 증분 보강는 문서를 최종 일관성으로 계속 구동 하면서 최소한의 작업을 수행할 수 있도록 합니다.

새 인덱서를 정의할 때 기존 인덱서에 캐시를 추가 하거나 캐시를 추가 하 여 시작 합니다.

> [!div class="nextstepaction"]
> [증분 보강에 대 한 캐싱 구성](search-howto-incremental-index.md)
