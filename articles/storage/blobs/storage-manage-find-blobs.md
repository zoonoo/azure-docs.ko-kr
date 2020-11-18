---
title: Blob 인덱스 태그를 사용 하 여 Azure Blob 데이터 관리 및 찾기 (미리 보기)
description: Blob 인덱스 태그를 사용 하 여 blob 개체를 분류, 관리 및 쿼리 하는 방법에 대해 알아봅니다.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 10/19/2020
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: klaasl
ms.custom: references_regions
ms.openlocfilehash: 3174dbd36d9bb39ce606ec12f88397f795e91526
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94832435"
---
# <a name="manage-and-find-azure-blob-data-with-blob-index-tags-preview"></a>Blob 인덱스 태그를 사용 하 여 Azure Blob 데이터 관리 및 찾기 (미리 보기)

데이터 집합이 커질수록 데이터의 해상에서 특정 개체를 찾는 것은 어려울 수 있습니다. Blob 인덱스 태그는 키-값 인덱스 태그 특성을 사용 하 여 데이터 관리 및 검색 기능을 제공 합니다. 단일 컨테이너 내에서 또는 저장소 계정의 모든 컨테이너에서 개체를 분류 하 고 찾을 수 있습니다. 데이터 요구 사항이 변경 됨에 따라 개체는 인덱스 태그를 업데이트 하 여 동적으로 분류 될 수 있습니다. 개체는 현재 컨테이너 조직에 그대로 남아 있을 수 있습니다.

Blob 인덱스 태그를 통해 다음을 할 수 있습니다.

- 키-값 인덱스 태그를 사용 하 여 동적으로 blob 범주화
- 전체 저장소 계정에서 태그가 지정 된 특정 blob을 빠르게 찾습니다.
- 인덱스 태그 계산을 기반으로 blob Api에 대 한 조건부 동작 지정
- [Blob 수명 주기 관리](storage-lifecycle-management-concepts.md) 와 같은 기능의 고급 컨트롤에는 인덱스 태그를 사용 합니다.

저장소 계정에 수백만 개의 blob이 있고 다양 한 응용 프로그램에서 액세스 하는 시나리오를 고려해 보세요. 단일 프로젝트에서 관련 된 모든 데이터를 찾으려고 합니다. 서로 다른 명명 규칙을 사용 하 여 여러 컨테이너에 데이터를 분산할 수 있으므로 범위가 무엇 인지 확실 하지 않습니다. 그러나 응용 프로그램은 프로젝트를 기반으로 하는 태그가 있는 모든 데이터를 업로드 합니다. 수백만 개의 blob을 검색 하 고 이름과 속성을 비교 하는 대신를 `Project = Contoso` 검색 조건으로 사용할 수 있습니다. Blob 인덱스는에서 50 blob 집합만 빠르게 찾고 반환 하도록 전체 저장소 계정에서 모든 컨테이너를 필터링 합니다 `Project = Contoso` .

Blob 인덱스를 사용 하는 방법에 대 한 예제를 시작 하려면 [blob 인덱스 태그를 사용 하 여 데이터 관리 및 검색](storage-blob-index-how-to.md)을 참조 하세요.

## <a name="blob-index-tags-and-data-management"></a>Blob 인덱스 태그 및 데이터 관리

컨테이너 및 blob 이름 접두사는 1 차원 분류입니다. Blob 인덱스 태그는 [blob 데이터 형식 (블록, 추가 또는 페이지)](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)에 대 한 다차원 분류를 허용 합니다. 다차원 분류는 Azure Blob Storage에 의해 기본적으로 인덱싱됩니다. 따라서 데이터를 신속 하 게 찾을 수 있습니다.

저장소 계정에서 다음과 같은 5 개의 blob을 고려 합니다.

- *container1/transaction.csv*
- *container2/campaign.docx*
- *사진/bannerphoto.png*
- *보관/완료/2019review.pdf*
- *logs/2020/01/01/logfile.txt*


이러한 blob은 *컨테이너/가상 폴더/b* s n 이름의 접두사를 사용 하 여 분리 됩니다. 이러한 다섯 개의 blob에 대 한의 인덱스 태그 특성을 설정 `Project = Contoso` 하 여 현재 접두사 조직을 유지 하면서 함께 범주화 할 수 있습니다. 인덱스 태그를 추가 하면 인덱스를 사용 하 여 데이터를 필터링 하 고 찾는 기능을 노출 하 여 데이터를 이동할 필요가 없습니다.

## <a name="setting-blob-index-tags"></a>Blob 인덱스 태그 설정

Blob 인덱스 태그는 저장소 계정 내의 새 개체 또는 기존 개체에 적용할 수 있는 키-값 특성입니다. [Put blob](/rest/api/storageservices/put-blob), [put Block List](/rest/api/storageservices/put-block-list)또는 [Copy blob](/rest/api/storageservices/copy-blob) 작업 및 선택적 헤더를 사용 하 여 업로드 프로세스 중에 인덱스 태그를 지정할 수 있습니다 `x-ms-tags` . 저장소 계정에 blob이 이미 있는 경우 요청 본문의 인덱스 태그를 사용 하 여 서식이 지정 된 XML 문서를 전달 하는 [Set Blob 태그](/rest/api/storageservices/set-blob-tags) 를 호출 합니다.

> [!IMPORTANT]
> Blob 인덱스 태그 설정은 blob의 태그 (SAS 권한)에 액세스할 수 있는 권한이 있는 공유 액세스 서명을 사용 하는 모든 사용자 및 [저장소 Blob 데이터 소유자](/azure/role-based-access-control/built-in-roles#storage-blob-data-owner) 에 의해 수행 될 수 있습니다 `t` .
>
> 또한 권한이 있는 RBAC 사용자는 `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` 이 작업을 수행할 수 있습니다.

Blob에서 단일 태그를 적용 하 여 데이터 처리가 완료 된 시간을 설명할 수 있습니다.

> "processedDate" = ' 2020-01-01 '

Blob에 여러 태그를 적용 하 여 데이터를 보다 자세히 설명할 수 있습니다.

> "Project" = ' Contoso '  
> "분류 된" = ' True '  
> "Status" = ' 처리 되지 않음 '  
> "Priority" = ' 01 '

기존 인덱스 태그 특성을 수정 하려면 기존 태그 특성을 검색 하 고, 태그 특성을 수정 하 고,을 [Blob 태그 설정](/rest/api/storageservices/set-blob-tags) 작업으로 바꿉니다. Blob에서 모든 인덱스 태그를 제거 하려면 `Set Blob Tags` 태그 특성을 지정 하지 않고 작업을 호출 합니다. Blob 인덱스 태그는 blob 데이터 콘텐츠에 대 한 하위 리소스 이므로는 `Set Blob Tags` 기본 콘텐츠를 수정 하지 않고 blob의 마지막 수정 시간 또는 eTag를 변경 하지 않습니다. 모든 현재 기본 blob 및 이전 버전에 대 한 인덱스 태그를 만들거나 수정할 수 있습니다. 그러나 스냅숏이 나 일시 삭제 된 blob의 태그는 수정할 수 없습니다.

Blob 인덱스 태그에는 다음과 같은 제한이 적용 됩니다.

- 각 blob에는 최대 10 개의 blob 인덱스 태그를 사용할 수 있습니다.
- 태그 키는 1 ~ 007e; 128 자 사이 여야 합니다.
- 태그 값은 0에서 256 자 사이 여야 합니다.
- 태그 키 및 값은 대/소문자를 구분 합니다.
- 태그 키 및 값은 문자열 데이터 형식만 지원 합니다. 숫자, 날짜, 시간 또는 특수 문자는 문자열로 저장 됩니다.
- 태그 키 및 값은 다음 명명 규칙을 따라야 합니다.
  - 영숫자:
    - **a**~**z**(소문자)
    - **A**~**Z**(대문자)
    - **0**~**9**(숫자)
  - 유효한 특수 문자: 공백, 더하기, 빼기, 마침표, 콜론, 같음, 밑줄, 슬래시 ( ` +-.:=_/` )

## <a name="getting-and-listing-blob-index-tags"></a>Blob 인덱스 태그 가져오기 및 나열

Blob 인덱스 태그는 blob 데이터와 함께 하위 리소스로 저장 되며 기본 blob 데이터 콘텐츠와 독립적으로 검색할 수 있습니다. Blob [태그 가져오기](/rest/api/storageservices/get-blob-tags) 작업을 사용 하 여 단일 blob에 대 한 blob 인덱스 태그를 검색할 수 있습니다. 매개 변수를 사용 하 여 [Blob 나열](/rest/api/storageservices/list-blobs) 작업은 `include:tags` blob 인덱스 태그와 함께 컨테이너 내의 모든 blob도 반환 합니다.

> [!IMPORTANT]
> Blob 인덱스 태그 가져오기 및 나열은 blob의 태그 (SAS 권한)에 액세스할 수 있는 권한이 있는 공유 액세스 서명을 가진 모든 사용자가 [저장소 Blob 데이터 소유자](/azure/role-based-access-control/built-in-roles#storage-blob-data-owner) 에 의해 수행 될 수 있습니다 `t` .
>
> 또한 권한이 있는 RBAC 사용자는 `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/read` 이 작업을 수행할 수 있습니다.

하나 이상의 blob 인덱스 태그가 있는 blob의 경우 blob의 `x-ms-tag-count` 인덱스 태그 수를 나타내는 Blob [나열](/rest/api/storageservices/list-blobs), [Blob 가져오기](/rest/api/storageservices/get-blob)및 [blob 속성 가져오기](/rest/api/storageservices/get-blob-properties) 작업에서이 반환 됩니다.

## <a name="finding-data-using-blob-index-tags"></a>Blob 인덱스 태그를 사용 하 여 데이터 찾기

인덱싱 엔진은 키-값 특성을 다차원 인덱스에 노출 합니다. 인덱스 태그를 설정한 후에는 blob에 존재 하며 즉시 검색할 수 있습니다. Blob 인덱스를 업데이트 하려면 약간의 시간이 걸릴 수 있습니다. Blob 인덱스가 업데이트 된 후 Blob Storage에서 제공 하는 기본 쿼리 및 검색 기능을 사용할 수 있습니다.

[태그별로 Blob 찾기](/rest/api/storageservices/find-blobs-by-tags) 작업을 사용 하면 인덱스 태그가 지정 된 쿼리 식과 일치 하는 필터링 된 blob 집합을 가져올 수 있습니다. `Find Blobs by Tags` 는 저장소 계정 내의 모든 컨테이너에서 필터링을 지원 하거나 필터링의 범위를 단일 컨테이너로 지정할 수 있습니다. 모든 인덱스 태그 키 및 값은 문자열 이므로 관계형 연산자는 사전적 정렬을 사용 합니다.

> [!IMPORTANT]
> Blob 인덱스 태그를 사용 하 여 데이터 찾기는 [저장소 Blob 데이터 소유자](/azure/role-based-access-control/built-in-roles#storage-blob-data-owner) 및 태그로 blob을 찾을 수 있는 권한이 있는 모든 사용자 (SAS 권한)에 의해 수행 될 수 있습니다 `f` .
>
> 또한 권한이 있는 RBAC 사용자는 `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/filter/action` 이 작업을 수행할 수 있습니다.

다음 기준은 blob 인덱스 필터링에 적용 됩니다.

- 태그 키는 큰따옴표 (")로 묶어야 합니다.
- 태그 값과 컨테이너 이름은 작은따옴표 (')로 묶어야 합니다.
- @ 문자는 특정 컨테이너 이름 (예:)에 대 한 필터링에만 사용할 수 있습니다. `@container = 'ContainerName'`
- 필터는 문자열에 대 한 사전적 정렬을 통해 적용 됩니다.
- 동일한 키에 대 한 동일한 면 범위 작업이 유효 하지 않습니다 (예: `"Rank" > '10' AND "Rank" >= '15'` ).
- REST를 사용 하 여 필터 식을 만들 때 문자는 URI로 인코딩해야 합니다.

다음 표에서는에 대 한 모든 유효한 연산자를 보여 줍니다 `Find Blobs by Tags` .

|  연산자  |  설명  | 예제 |
|------------|---------------|---------|
|     =      |     같음     | `"Status" = 'In Progress'` |
|     >      |  보다 큼 | `"Date" > '2018-06-18'` |
|     >=     |  크거나 같음 | `"Priority" >= '5'` |
|     <      |  보다 작음   | `"Age" < '32'` |
|     <=     |  작거나 같음  | `"Company" <= 'Contoso'` |
|    AND     |  논리적 and  | `"Rank" >= '010' AND "Rank" < '100'` |
| @container | 특정 컨테이너에 대 한 범위 | `@container = 'videofiles' AND "status" = 'done'` |

> [!NOTE]
> 태그를 설정 하 고 쿼리 하는 경우 사전순으로 정렬 합니다.
>
> - 숫자는 문자 앞에 정렬 됩니다. 숫자는 첫 번째 숫자를 기준으로 정렬 됩니다.
> - 대문자가 소문자보다 앞에 정렬됩니다.
> - 기호가 표준이 아닙니다. 일부 기호는 숫자 값 앞에 정렬 됩니다. 다른 기호는 문자 앞뒤에 정렬 됩니다.

## <a name="conditional-blob-operations-with-blob-index-tags"></a>Blob 인덱스 태그를 사용 하는 조건부 blob 작업

REST 버전 2019-10-10 이상에서 대부분의 [blob Service api](/rest/api/storageservices/operations-on-blobs) 는 이제 `x-ms-if-tags` 지정 된 blob 인덱스 조건이 충족 될 경우에만 작업이 성공 하도록 조건부 헤더를 지원 합니다. 조건이 충족 되지 않으면를 가져옵니다 `error 412: The condition specified using HTTP conditional header(s) is not met` .

`x-ms-if-tags`헤더는 다른 기존 HTTP 조건부 헤더와 결합 될 수 있습니다 (-match,-None-match 등). 요청에서 여러 조건부 헤더를 제공 하는 경우 작업이 성공 하려면 모두 true를 평가 해야 합니다. 모든 조건부 헤더는 실제로 논리적 AND와 결합 됩니다.

아래 표에서는 조건부 연산에 대 한 유효한 연산자를 보여 줍니다.

|  연산자  |  설명  | 예제 |
|------------|---------------|---------|
|     =      |     같음     | `"Status" = 'In Progress'` |
|     <>     |   같지 않음   | `"Status" <> 'Done'` |
|     >      |  보다 큼 | `"Date" > '2018-06-18'` |
|     >=     |  크거나 같음 | `"Priority" >= '5'` |
|     <      |  보다 작음   | `"Age" < '32'` |
|     <=     |  작거나 같음  | `"Company" <= 'Contoso'` |
|    AND     |  논리적 and  | `"Rank" >= '010' AND "Rank" < '100'` |
|     또는     | 논리적 or   | `"Status" = 'Done' OR "Priority" >= '05'` |

> [!NOTE]
> Blob 작업의 조건부 헤더에는 허용 되지만 작업에는 없는 두 개의 추가 연산자 (같지 않음)와 논리 or가 있습니다 `x-ms-if-tags` `Find Blobs by Tags` .

## <a name="platform-integrations-with-blob-index-tags"></a>Blob 인덱스 태그를 사용한 플랫폼 통합

Blob 인덱스 태그는 blob 데이터를 분류, 관리 및 검색 하는 데 도움이 될 뿐만 아니라 [수명 주기 관리](storage-lifecycle-management-concepts.md)와 같은 다른 Blob Storage 기능과의 통합도 제공 합니다.

### <a name="lifecycle-management"></a>수명 주기 관리

`blobIndexMatch`수명 주기 관리에서를 규칙 필터로 사용 하 여 데이터를 냉각기 계층으로 이동 하거나 blob에 적용 되는 인덱스 태그를 기준으로 데이터를 삭제할 수 있습니다. 규칙을 보다 세부적으로 지정할 수 있으며, 지정 된 태그 조건과 일치 하는 경우에만 blob을 이동 하거나 삭제할 수 있습니다.

태그가 지정 된 데이터에 대 한 작업을 적용 하기 위해 수명 주기 규칙에 있는 독립 실행형 필터 집합으로 blob 인덱스 일치를 설정할 수 있습니다. 또는 접두사와 blob 인덱스를 결합 하 여 보다 구체적인 데이터 집합을 일치 시킬 수 있습니다. 수명 주기 규칙에 여러 필터를 지정 하면 논리적 AND 연산이 적용 됩니다. 작업은 *모든* 필터 조건이 일치 하는 경우에만 적용 됩니다.

다음 샘플 수명 주기 관리 규칙은 *videofiles* 라는 컨테이너의 블록 blob에 적용 됩니다. 이 규칙은 데이터가의 blob 인덱스 태그 조건과 일치 하는 경우에만 저장소를 보관 하도록 blob을 계층화 합니다 `"Status" == 'Processed' AND "Source" == 'RAW'` .

# <a name="portal"></a>[포털](#tab/azure-portal)

![Azure Portal의 수명 주기 관리에 대 한 Blob 인덱스 일치 규칙 예제](media/storage-blob-index-concepts/blob-index-lifecycle-management-example.png)

# <a name="json"></a>[JSON](#tab/json)

```json
{
    "rules": [
        {
            "enabled": true,
            "name": "ArchiveProcessedSourceVideos",
            "type": "Lifecycle",
            "definition": {
                "actions": {
                    "baseBlob": {
                        "tierToArchive": {
                            "daysAfterModificationGreaterThan": 0
                        }
                    }
                },
                "filters": {
                    "blobIndexMatch": [
                        {
                            "name": "Status",
                            "op": "==",
                            "value": "Processed"
                        },
                        {
                            "name": "Source",
                            "op": "==",
                            "value": "RAW"
                        }
                    ],
                    "blobTypes": [
                        "blockBlob"
                    ],
                    "prefixMatch": [
                        "videofiles/"
                    ]
                }
            }
        }
    ]
}
```

---

## <a name="permissions-and-authorization"></a>권한 및 권한 부여

다음 방법 중 하나를 사용 하 여 blob 인덱스 태그에 대 한 액세스 권한을 부여할 수 있습니다.

- Azure RBAC (역할 기반 액세스 제어)를 사용 하 여 Azure Active Directory (Azure AD) 보안 주체에 게 사용 권한을 부여 합니다. 탁월한 보안과 사용 편의성을 위해 Azure AD를 사용 합니다. Blob 작업에 Azure AD를 사용 하는 방법에 대 한 자세한 내용은 [Azure Active Directory를 사용 하 여 blob 및 큐에 대 한 액세스 권한 부여](../common/storage-auth-aad.md)
- SAS (공유 액세스 서명)를 사용 하 여 blob 인덱스에 대 한 액세스를 위임 합니다. 공유 액세스 서명에 대한 자세한 내용은 [SAS(공유 액세스 서명)를 사용하여 Azure Storage 리소스에 대한 제한된 액세스 권한 부여](../common/storage-sas-overview.md)를 참조하세요.
- 계정 액세스 키를 사용 하 여 공유 키로 작업에 권한을 부여 합니다. 자세한 내용은 [공유 키를 사용하여 권한 부여](/rest/api/storageservices/authorize-with-shared-key)를 참조하세요.

Blob 인덱스 태그는 blob 데이터에 대 한 하위 리소스입니다. Blob 읽기 또는 쓰기에 대 한 사용 권한이 있는 사용자 또는 SAS 토큰은 blob 인덱스 태그에 액세스할 수 없습니다.

### <a name="role-based-access-control"></a>역할 기반 액세스 제어

[AZURE AD id](../common/storage-auth-aad.md) 를 사용 하는 호출자에 게 blob 인덱스 태그에 대해 작동 하는 다음 사용 권한이 부여 될 수 있습니다.

| Blob 인덱스 태그 작업                                          | Azure RBAC 작업                                                             |
|--------------------------------------------------------------------|-------------------------------------------------------------------------------|
| [Blob 태그 설정](/rest/api/storageservices/set-blob-tags)           | Microsoft. Storage/storageAccounts/blobServices/컨테이너/b l o b/태그/쓰기    |
| [Blob 태그 가져오기](/rest/api/storageservices/get-blob-tags)           | Microsoft. Storage/storageAccounts/blobServices/컨테이너/b l o b/태그/읽기     |
| [태그로 Blob 찾기](/rest/api/storageservices/find-blobs-by-tags) | Microsoft. Storage/storageAccounts/blobServices/컨테이너/b l o b/필터/작업 |

인덱스 태그 작업을 수행 하려면 기본 blob 데이터와는 별도로 추가 사용 권한이 필요 합니다. [저장소 Blob 데이터 소유자](/azure/role-based-access-control/built-in-roles#storage-blob-data-owner) 역할에는 세 가지 Blob 인덱스 태그 작업 모두에 대 한 사용 권한이 부여 됩니다. [저장소 Blob 데이터 판독기](/azure/role-based-access-control/built-in-roles#storage-blob-data-reader) 에는 및 작업에 대 한 권한만 부여 됩니다 `Find Blobs by Tags` `Get Blob Tags` .

### <a name="sas-permissions"></a>SAS 권한

[SAS (공유 액세스 서명)](../common/storage-sas-overview.md) 를 사용 하는 호출자에 게 blob 인덱스 태그에 대해 작동 하는 범위 지정 권한이 부여 될 수 있습니다.

#### <a name="blob-sas"></a>Blob SAS

Blob SAS에 다음 사용 권한을 부여 하 여 blob 인덱스 태그에 액세스할 수 있습니다. Blob 읽기 및 쓰기 권한 만으로는 해당 인덱스 태그를 읽거나 쓸 수 없습니다.

| 사용 권한 | URI 기호 | 허용되는 작업                |
|------------|------------|-----------------------------------|
| 인덱스 태그 |     t      | Blob에 대 한 인덱스 태그 가져오기 및 설정 |

#### <a name="container-sas"></a>컨테이너 SAS

Blob 태그를 필터링 할 수 있도록 컨테이너 SAS에서 다음 사용 권한을 부여할 수 있습니다. `Blob List`사용 권한은 인덱스 태그로 필터링 blob을 허용 하기에 충분 하지 않습니다.

| 사용 권한 | URI 기호 | 허용되는 작업         |
|------------|------------|----------------------------|
| 인덱스 태그 |     f      | 인덱스 태그가 있는 blob 찾기 |

## <a name="choosing-between-metadata-and-blob-index-tags"></a>메타 데이터와 blob 인덱스 태그 중에서 선택

Blob 인덱스 태그와 메타 데이터는 모두 blob 리소스와 함께 임의의 사용자 정의 키-값 속성을 저장 하는 기능을 제공 합니다. 모두 blob의 내용을 반환 하거나 변경 하지 않고 직접 검색 하 고 설정할 수 있습니다. 메타 데이터와 인덱스 태그를 모두 사용할 수 있습니다.

인덱스 태그만 자동으로 인덱싱되어 네이티브 Blob Storage 서비스에 의해 검색 가능 합니다. 메타 데이터를 고유 하 게 인덱싱하거나 검색할 수 없습니다. [Azure Search](../../search/search-blob-ai-integration.md)와 같은 별도의 서비스를 사용 해야 합니다. Blob 인덱스 태그에는 기본 blob 데이터와는 별도로 읽기, 필터링 및 쓰기에 대 한 추가 권한이 있습니다. 메타 데이터는 blob과 동일한 권한을 사용 하 고 blob [가져오기](/rest/api/storageservices/get-blob) 및 [blob 속성 가져오기](/rest/api/storageservices/get-blob-properties) 작업을 통해 HTTP 헤더로 반환 됩니다. Blob 인덱스 태그는 [Microsoft에서 관리](../common/storage-service-encryption.md)하는 키를 사용 하 여 미사용에 암호화 됩니다. 메타 데이터는 blob 데이터에 대해 지정 된 것과 동일한 암호화 키를 사용 하 여 미사용 시 암호화 됩니다.

다음 표에는 메타 데이터와 blob 인덱스 태그의 차이점이 요약 되어 있습니다.

|              |   메타데이터   |   Blob 인덱스 태그  |
|--------------|--------------|--------------------|
| **제한**      | 숫자 제한 없음, 총 8kb, 대/소문자 구분 안 함 | blob 당 최대 10 개 태그, 태그 당 768 바이트, 대/소문자 구분 |
| **업데이트**    | 보관 계층에서 허용 되지 않습니다. `Set Blob Metadata` 기존 메타 데이터를 모두 바꾸고 `Set Blob Metadata` blob의 마지막 수정 시간을 변경 합니다. | 모든 액세스 계층에 대해 허용 되는 모든 `Set Blob Tags` 기존 태그를 바꾸고 `Set Blob Tags` blob의 마지막 수정 시간을 변경 하지 않습니다. |
| **스토리지**     | Blob 데이터와 함께 저장 됩니다. | Blob 데이터의 하위 리소스 |
| **인덱싱 & 쿼리** | Azure Search와 같은 별도의 서비스를 사용 해야 합니다. | Blob Storage에 기본 제공 되는 인덱싱 및 쿼리 기능 |
| **암호화** | Blob 데이터에 사용 되는 것과 동일한 암호화 키를 사용 하 여 미사용 암호화 | Microsoft에서 관리 하는 암호화 키를 사용 하 여 미사용 암호화 |
| **가격** | 메타 데이터의 크기는 blob에 대 한 저장소 비용에 포함 됩니다. | 인덱스 태그별 별 고정 비용 |
| **헤더 응답** | 및에서 헤더로 반환 된 메타 데이터 `Get Blob``Get Blob Properties` | 또는에서 반환 된 태그 개수입니다. `Get Blob` `Get Blob Properties` 태그는 및에 의해 반환 됩니다. `Get Blob Tags``List Blobs` |
| **권한**  | Blob 데이터에 대 한 읽기 또는 쓰기 권한이 메타 데이터로 확장 됩니다. | 인덱스 태그를 읽거나 필터링 하거나 쓰려면 추가 권한이 필요 합니다. |
| **이름 지정** | 메타 데이터 이름은 c # 식별자에 대 한 명명 규칙을 따라야 합니다. | Blob 인덱스 태그는 광범위 한 영숫자 문자를 지원 합니다. |

## <a name="pricing"></a>가격 책정

Blob 인덱스 가격은 공개 미리 보기 상태 이며 일반 공급에 대 한 변경 될 수 있습니다. 저장소 계정 내의 월별 평균 인덱스 태그 수에 대 한 요금이 청구 됩니다. 인덱싱 엔진에 대 한 비용은 없습니다. , 및에 대 한 요청 `Set Blob Tags` `Get Blob Tags` `Find Blobs by Tags` 은 각 작업 유형에 따라 요금이 청구 됩니다. [자세한 내용은 블록 Blob 가격 책정을](https://azure.microsoft.com/pricing/details/storage/blobs/)참조 하세요.

## <a name="regional-availability-and-storage-account-support"></a>지역 가용성 및 저장소 계정 지원

Blob 인덱스 태그는 HNS (계층적 네임 스페이스)를 사용 하지 않는 범용 v2 (GPv2) 계정 에서만 사용할 수 있습니다. GPV1 (범용) 계정은 지원 되지 않지만 GPv1 계정을 GPv2 계정으로 업그레이드할 수 있습니다.

Premium storage 계정에서는 인덱스 태그가 지원 되지 않습니다. 스토리지 계정에 대한 자세한 내용은 [Azure Storage 계정 개요](../common/storage-account-overview.md)를 참조하세요.

공개 미리 보기에서 blob 인덱스 태그는 다음 지역 에서만 사용할 수 있습니다.

- 캐나다 중부
- 캐나다 동부
- 프랑스 중부
- 프랑스 남부

시작 하려면 [blob 인덱스 태그를 사용 하 여 데이터 관리 및 검색](storage-blob-index-how-to.md)을 참조 하세요.

> [!IMPORTANT]
> 저장소 계정에서 blob 인덱스 미리 보기를 사용 하려면 먼저 구독을 등록 해야 합니다. 이 문서의 [조건 및 알려진 문제](#conditions-and-known-issues) 섹션을 참조 하세요.

### <a name="register-your-subscription-preview"></a>구독 등록 (미리 보기)

Blob 인덱스 태그는 공개 미리 보기 상태 이므로이 기능을 사용 하려면 먼저 구독을 등록 해야 합니다. 요청을 제출하려면 다음 PowerShell 또는 CLI 명령을 실행합니다.

#### <a name="register-by-using-powershell"></a>PowerShell을 사용 하 여 등록

```powershell
Register-AzProviderFeature -FeatureName BlobIndex -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

#### <a name="register-by-using-azure-cli"></a>Azure CLI를 사용 하 여 등록

```azurecli
az feature register --namespace Microsoft.Storage --name BlobIndex
az provider register --namespace 'Microsoft.Storage'
```

## <a name="conditions-and-known-issues"></a>조건 및 알려진 문제

이 섹션에서는 blob 인덱스 태그의 공개 미리 보기의 알려진 문제 및 조건에 대해 설명 합니다. 동작이 변경 될 수 있으므로 GA (일반 공급)에 도달할 때까지 프로덕션 워크 로드에는이 기능을 사용할 수 없습니다.

- 미리 보기의 경우 미리 보기 영역에서 저장소 계정에 대 한 blob 인덱스를 사용 하려면 먼저 구독을 등록 해야 합니다.
- GPv2 계정만 미리 보기에서 지원 됩니다. Blob, BlockBlobStorage 및 HNS 사용 DataLake Gen2 계정은 지원 되지 않습니다. GPv1 계정은 지원 되지 않습니다.
- 인덱스 태그를 사용 하 여 페이지 blob을 업로드 해도 태그는 유지 되지 않습니다. 페이지 blob을 업로드 한 후 태그를 설정 합니다.
- 필터링이 단일 컨테이너로 범위가 지정 된 경우 `@container` 필터 식의 모든 인덱스 태그가 같음 검사 (키 = 값) 인 경우에만를 전달할 수 있습니다.
- 조건에 범위 연산자를 사용 하는 경우 `AND` 동일한 인덱스 태그 키 이름 ()만 지정할 수 있습니다 `"Age" > '013' AND "Age" < '100'` .
- 버전 관리 및 blob 인덱스는 지원 되지 않습니다. Blob 인덱스 태그는 버전에 대해 유지 되지만 blob 인덱스 엔진에는 전달 되지 않습니다.
- 인덱스 태그가 인덱싱되어 있는지 여부를 확인 하는 API는 없습니다.
- 계정 장애 조치 (failover)는 지원 되지 않습니다. 장애 조치 (failover) 후 blob 인덱스가 제대로 업데이트 되지 않을 수 있습니다.
- 수명 주기 관리는 blob 인덱스 일치를 사용 하는 같음 검사만 지원 합니다.
- `Copy Blob` 원본 blob의 blob 인덱스 태그를 새 대상 blob에 복사 하지 않습니다. 복사 작업 중에 대상 blob에 적용 하려는 태그를 지정할 수 있습니다.
- `Copy Blob` (비동기 복사) 대상 blob에 적용 된 태그가 있는 다른 저장소 계정에서 blob 인덱스 엔진은 필터 집합에서 blob 및 해당 태그를 반환 하지 않습니다. `Copy Blob`URL (동기화 복사)에서 사용 합니다.
- 태그는 스냅숏 생성 시 유지 됩니다. 그러나 스냅숏의 수준을 올리는 것은 지원 되지 않으며 태그 집합이 비어 있을 수 있습니다.

## <a name="faq"></a>FAQ

**Blob 인덱스는 내 blob 내에서 콘텐츠를 필터링 하 고 쿼리 하는 데 도움이 되나요?**

아니요. blob 데이터 내에서 검색 해야 하는 경우 쿼리 가속 또는 Azure search를 사용 합니다.

**인덱스 태그 값에 대 한 요구 사항이 있나요?**

Blob 인덱스 태그는 문자열 데이터 형식만 지원 하 고 쿼리는 사전순으로 정렬 된 결과를 반환 합니다. 숫자의 경우 숫자를 0으로 채웁니다. 날짜 및 시간의 경우 ISO 8601 호환 형식으로 저장 합니다.

**Blob 인덱스 태그와 Azure Resource Manager 태그가 관련 되어 있나요?**

아니요, 리소스 관리자 태그는 구독, 리소스 그룹, 저장소 계정 등의 제어 평면 리소스를 구성 하는 데 도움이 됩니다. 인덱스 태그는 데이터 평면에서 blob 관리 및 검색 기능을 제공 합니다.

## <a name="next-steps"></a>다음 단계

Blob 인덱스를 사용 하는 방법에 대 한 예는 [blob 인덱스를 사용 하 여 데이터 관리 및 찾기](storage-blob-index-how-to.md)를 참조 하세요.

[수명 주기 관리](storage-lifecycle-management-concepts.md) 에 대해 알아보고 blob 인덱스 일치를 사용 하 여 규칙을 설정 합니다.
