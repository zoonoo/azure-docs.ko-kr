---
title: 증분 보강(미리 보기)
titleSuffix: Azure Cognitive Search
description: Azure Storage의 AI 보강 파이프라인에서 중간 콘텐츠 및 증분 변경 사항을 캐시하여 기존 처리된 문서에 대한 투자를 보존합니다. 이 기능은 현재 공개 미리 보기로 제공됩니다.
manager: nitinme
author: Vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: 09003c26ead9108d07ae339fcf64235c246474a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024146"
---
# <a name="introduction-to-incremental-enrichment-and-caching-in-azure-cognitive-search"></a>Azure 인지 검색에서 증분 보강 및 캐싱 소개

> [!IMPORTANT] 
> 증분 보강은 현재 공개 미리 보기중입니다. 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요. 이 기능은 [REST API 버전 2019-05-06-미리 보기](search-api-preview.md)에서 제공됩니다. 현재 포털 또는 .NET SDK 지원이 없습니다.

증분 보강은 보강 파이프라인에 캐싱 및 상태 저장성을 추가하여 기존 출력에 대한 투자를 유지하면서 특정 수정의 영향을 받는 문서만 변경합니다. 이는 처리(특히 OCR 및 이미지 처리)에 대한 금전적 투자를 보존할 뿐만 아니라 보다 효율적인 시스템을 제공합니다. 구조와 콘텐츠가 캐시되면 인덱서가 변경된 기술을 결정하고 수정된 기술과 다운스트림 종속 기술만 실행할 수 있습니다. 

## <a name="indexer-cache"></a>인덱서 캐시

증분 보강은 보강 파이프라인에 캐시를 추가합니다. 인덱서에는 문서 크래킹의 결과와 모든 문서에 대한 각 기술의 출력이 캐시됩니다. 기술 세트가 업데이트되면 변경된 기술 또는 다운스트림 기술만 다시 실행됩니다. 업데이트된 결과는 캐시에 기록되고 문서는 검색 인덱스 또는 기술 저장소에서 업데이트됩니다.

물리적으로 캐시는 Azure Storage 계정의 Blob 컨테이너에 저장됩니다. 캐시는 또한 업데이트 처리의 내부 레코드에 테이블 저장소를 사용합니다. 검색 서비스 내의 모든 인덱스는 인덱서 캐시에 대해 동일한 스토리지 계정을 공유할 수 있습니다. 각 인덱서는 사용 하는 컨테이너에 고유 하 고 변경할 수 없는 캐시 식별자를 할당 됩니다.

## <a name="cache-configuration"></a>캐시 구성

증분 보강의 이점을 시작하려면 인덱서에서 `cache` 속성을 설정해야 합니다. 다음 예제에서는 캐싱이 활성화된 인덱서를 보여 줍니다. 이 구성의 특정 부분은 다음 섹션에서 설명합니다. 자세한 내용은 [증분 보강 설정을](search-howto-incremental-index.md)참조하십시오.

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

기존 인덱서에서 이 속성을 설정하려면 인덱서를 재설정하고 다시 실행해야 하므로 데이터 원본의 모든 문서가 다시 처리됩니다. 이 단계는 이전 버전의 기술 집합에 의해 보강된 문서를 제거하는 데 필요합니다. 

## <a name="cache-management"></a>캐시 관리

캐시의 수명 주기는 인덱서에서 관리합니다. 인덱서의 `cache` 속성이 null로 설정되었거나 연결 문자열이 변경되면 다음 인덱서 실행시 기존 캐시가 삭제됩니다. 캐시 수명 주기는 인덱서 수명 주기와도 연결됩니다. 인덱서가 삭제되면 연결된 캐시도 삭제됩니다.

증분 보강은 부품에 개입하지 않고 변경 내용을 감지하고 응답하도록 설계되었지만 기본 동작을 재정의하는 데 사용할 수 있는 매개 변수가 있습니다.

+ 새 문서의 우선 순위 지정
+ 기술 집합 검사 우회
+ 데이터 원본 검사 우회
+ 힘 기술 집합 평가

### <a name="prioritize-new-documents"></a>새 문서의 우선 순위 지정

캐시에 `enableReprocessing` 이미 표시된 들어오는 문서에 대한 처리를 제어하도록 속성을 설정합니다. (기본값) `true` 캐시에 이미 있는 문서는 기술 업데이트가 해당 문서에 영향을 미친다고 가정하여 인덱서를 다시 실행할 때 다시 처리됩니다. 

기존 `false`문서가 다시 처리되지 않은 경우 기존 콘텐츠보다 새 콘텐츠의 우선 순위를 효과적으로 지정합니다. 임시로만 `enableReprocessing` 설정해야 `false` 합니다. 코퍼스 전체의 일관성을 `enableReprocessing` 보장하려면 `true` 대부분의 경우 새 문서와 기존 문서가 현재 기술 집합 정의에 따라 유효한지 확인해야 합니다.

### <a name="bypass-skillset-evaluation"></a>기술 집합 평가 우회

해당 기술 집합을 수정하고 해당 기술 집합을 다시 처리합니다. 그러나 기술 집합을 일부 변경하면 재처리가 발생하지 않아야 합니다(예: 사용자 지정 기술을 새 위치에 배포하거나 새 액세스 키를 사용하여 배포). 대부분의 경우, 이들은 기술 집합 자체의 물질에 진정한 영향을 미치지 않는 주변 장치 수정입니다. 

기술 집합에 대한 변경이 실제로 피상적이라는 것을 알고 있는 경우 `disableCacheReprocessingChangeDetection` 매개 변수를 `true`다음으로 설정하여 기술 집합 평가를 재정의해야 합니다.

1. 기술 집합 업데이트에 전화를 걸고 기술 집합 정의를 수정합니다.
1. 요청에 `disableCacheReprocessingChangeDetection=true` 매개 변수를 보온합니다.
1. 변경 사항을 제출합니다.

이 매개 변수를 설정하면 기술 집합 정의에 대한 업데이트만 커밋되고 변경 내용이 기존 코퍼스에 미치는 영향에 대해 평가되지 않습니다.

다음 예제에서는 매개 변수를 사용 하 인 업데이트 기술 집합 요청을 보여 드립니다.

```http
PUT https://customerdemos.search.windows.net/skillsets/callcenter-text-skillset?api-version=2019-05-06-Preview&disableCacheReprocessingChangeDetection=true
```

### <a name="bypass-data-source-validation-checks"></a>데이터 원본 유효성 검사 우회

데이터 원본 정의를 대부분 변경하면 캐시가 무효화됩니다. 그러나 변경이 연결 문자열 변경 또는 저장소 계정의 키 회전과 같이 캐시를 무효화해서는 안 되는`ignoreResetRequirement` 시나리오의 경우 데이터 원본 업데이트의 매개 변수를 추가합니다. 이 매개 `true` 변수를 설정하면 모든 개체가 처음부터 다시 빌드되고 채워지는 재설정 조건을 트리거하지 않고 커밋이 통과할 수 있습니다.

```http
PUT https://customerdemos.search.windows.net/datasources/callcenter-ds?api-version=2019-05-06-Preview&ignoreResetRequirement=true
```

### <a name="force-skillset-evaluation"></a>힘 기술 집합 평가

캐시의 목적은 불필요한 처리를 피하는 것이지만 인덱서가 감지하지 못하는 기술(예: 사용자 지정 기술과 같은 외부 코드에서 변경)을 변경한다고 가정합니다.

이 경우 [기술 재설정을](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/reset-skills) 사용하여 해당 기술의 출력에 종속된 다운스트림 기술을 포함하여 특정 기술을 강제로 재처리할 수 있습니다. 이 API는 유효성을 제거하고 재처리를 위해 표시해야 하는 기술 목록이 있는 POST 요청을 수락합니다. 기술 재설정 후 인덱서를 실행하여 파이프라인을 호출합니다.

## <a name="change-detection"></a>변경 내용 검색

캐시를 사용하도록 설정하면 인덱서에서 파이프라인 컴포지션의 변경 내용을 평가하여 재사용할 수 있는 콘텐츠와 재처리가 필요한 콘텐츠를 결정합니다. 이 섹션에서는 캐시를 완전히 무효화하는 변경 내용을 등록한 다음 증분 처리를 트리거하는 변경 내용을 가져옵니다. 

### <a name="changes-that-invalidate-the-cache"></a>캐시를 무효화하는 변경 사항

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

### <a name="changes-that-trigger-incremental-processing"></a>증분 처리를 트리거하는 변경 사항

증분 처리는 기술 집합 정의를 평가하고 다시 실행할 기술을 결정하여 문서 트리의 영향을 받는 부분을 선택적으로 업데이트합니다. 다음은 증분 보강을 초래하는 변경 사항의 전체 목록입니다.

* 기술 세트의 기술 유형이 다릅니다. 기술의 Odata 형식이 업데이트되었습니다.
* 기술 관련 매개 변수(예: URL, 기본값 또는 기타 매개 변수)가 업데이트되었습니다.
* 기술 출력이 변경되고, 기술에서 추가 또는 다른 출력을 반환합니다.
* 기술 업데이트 결과가 다른 상위 구조이며, 기술 연결이 변경되었습니다(예: 기술 입력).
* 이 기술에 대한 입력을 제공하는 기술이 업데이트되면 업스트림 기술이 무효화됩니다.
* 지식 저장소 프로젝션 위치가 업데이트되어 문서를 다시 프로젝션합니다.
* 지식 저장소 프로젝션이 변경되어 문서를 다시 프로젝션합니다.
* 인덱서에서 출력 필드 매핑이 변경되어 문서를 인덱스로 다시 프로젝션합니다.

## <a name="api-reference"></a>API 참조

REST API `2019-05-06-Preview` 버전은 인덱서, 기술 집합 및 데이터 원본에 대한 추가 속성을 통해 증분 보강을 제공합니다. 참조 설명서 외에도 API 호출 방법에 대한 자세한 내용은 [증분 보강을 위한 캐싱 구성을](search-howto-incremental-index.md) 참조하십시오.

+ [인덱서 만들기(api 버전=2019-05-06-미리 보기)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/create-indexer) 

+ [업데이트 인덱서(api 버전=2019-05-06-미리 보기)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/update-indexer) 

+ [기술 집합 업데이트(api 버전=2019-05-06-미리 보기)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/update-skillset) (요청에 대한 새 URI 매개 변수)

+ [기술 다시 설정(api-version=2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/reset-skills)

+ 데이터베이스 인덱서(Azure SQL, 코스모스 DB). 일부 인덱서에서 쿼리를 통해 데이터를 검색합니다. 데이터를 검색하는 쿼리의 경우 [Update Data Source는](https://docs.microsoft.com/rest/api/searchservice/update-data-source) 요청에서 새 매개 변수를 `true` **지원합니다ResetRequirements**는 업데이트 작업이 캐시를 무효화하지 않아야 하는 시기로 설정해야 합니다. 

  **ignoreReset요구 사항** 사용으로 인해 데이터가 의도하지 않은 불일치로 인해 쉽게 검색되지 않을 수 있습니다.

## <a name="next-steps"></a>다음 단계

증분 보강은 변경 추적을 기술 집합 및 AI 보강으로 확장하는 강력한 기능입니다. ACrecremental 보강을 사용하면 기술 집합 설계를 반복할 때 기존 처리된 콘텐츠를 다시 사용할 수 있습니다.

다음 단계로 기존 인덱서에서 캐싱을 사용하거나 새 인덱서를 정의할 때 캐시를 추가합니다.

> [!div class="nextstepaction"]
> [증분 보강을 위한 캐싱 구성](search-howto-incremental-index.md)
