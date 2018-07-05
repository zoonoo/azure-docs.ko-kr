---
title: Azure Storage 수명 주기 관리
description: 오래된 데이터를 핫에서 쿨로 전환하고 계층을 보관하는 수명 주기 정책 규칙을 만드는 방법을 알아봅니다.
services: storage
author: yzheng-msft
manager: jwillis
ms.service: storage
ms.workload: storage
ms.topic: article
ms.date: 04/30/2018
ms.author: yzheng
ms.openlocfilehash: 9721935f005bbd9a5dc261fe801ecc14744b004f
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/25/2018
ms.locfileid: "36752795"
---
# <a name="managing-the-azure-blob-storage-lifecycle-preview"></a>Azure Blob Storage 수명 주기 관리(미리 보기)

데이터 집합에는 고유한 수명 주기가 있습니다. 어떤 데이터는 수명 주기의 초기 단계에서 자주 액세스되다가 데이터가 오래될수록 액세스 빈도가 떨어집니다. 어떤 데이터는 클라우드에서 유휴 상태로 유지되고 저장된 후에는 어쩌다가 한 번씩 액세스됩니다. 어떤 데이터는 생성 며칠 후 또는 몇 달 후 만료되고, 또 어떤 데이터 집합은 수명 주기 전체에 걸쳐 적극적으로 읽히고 수정됩니다. Azure Blob Storage 수명 주기 관리(미리 보기)는 데이터를 최적의 액세스 계층으로 전환하고 수명 주기가 끝나면 데이터를 만료할 수 있는 풍부한 규칙 기반 정책을 제공합니다.

수명 주기 관리 정책으로 다음과 같은 일을 할 수 있습니다.

- BLOB을 쿨 저장소 계층으로 전환하여(핫 저장소 계층에서 쿨 저장소 계층으로, 핫 저장소 계층에서 보관 저장소 계층으로 또는 쿨 저장소 계층에서 보관 저장소 계층으로) 성능 및 비용 최적화
- 수명 주기가 끝나면 BLOB 삭제
- 저장소 계정 수준에서 하루에 한 번 실행할 규칙 정의(GPv2 및 Blob 저장소 계정을 모두 지원)
- 컨테이너 또는 BLOB 하위 집합에 규칙 적용(접두사를 필터로 사용)

수명 주기의 초기 단계에 자주 액세스되고, 2주 후에는 가끔 필요하고, 1개월 이후부터는 거의 액세스되지 않는 데이터 집합이 있다고 가정해 봅시다. 이 시나리오의 초기 단계에는 핫 저장소 계층이 가장 적합하고, 가끔 액세스되는 기간에는 쿨 저장소 계층이 가장 적합하고, 1개월 이후부터는 보관 저장소 계층이 가장 적합한 옵션입니다. 데이터의 보존 기간에 따라 저장소 계층을 조정하면 요구 사항에 맞는 가장 저렴한 저장소 옵션을 디자인할 수 있습니다. 이와 같이 전환하려면 수명 주기 관리 정책을 사용하여 오래된 데이터를 쿨 저장소 계층으로 이동하면 됩니다.

## <a name="storage-account-support"></a>저장소 계정 지원

수명 주기 관리 정책은 범용 v2(GPv2) 계정과 Blob Storage 계정에 모두 사용할 수 있습니다. Azure Portal에서 간단한 원클릭 프로세스를 이용하여 기존의 범용(GPv1) 계정을 GPv2 계정으로 변환할 수 있습니다. 자세히 알아보려면 [Azure Storage 계정 옵션](../common/storage-account-options.md)을 참조하세요.  

## <a name="pricing"></a>가격 

수명 주기 관리 기능은 무료 미리 보기로 제공됩니다. [Blob 나열](https://docs.microsoft.com/rest/api/storageservices/list-blobs) 및 [Blob 계층 설정](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) API 호출에 대한 일반 작업 비용은 고객에게 청구됩니다. 가격 책정에 대한 자세한 내용은 [블록 Blob 가격](https://azure.microsoft.com/pricing/details/storage/blobs/)을 참조하세요.

## <a name="register-for-preview"></a>미리 보기 등록 
공개 미리 보기에 등록하려면 이 기능을 구독에 등록해 달라는 요청을 제출해야 합니다. 요청이 승인되면(며칠 내로) 미국 서부 2 및 미국 중서부의 기존 및 신규 GPv2 또는 Blob Storage 계정에서 기능을 사용할 수 있습니다. 미리 보기 기간에는 블록 Blob만 사용할 수 있습니다. 대부분의 미리 보기와 마찬가지로, 이 기능은 GA 전에는 프로덕션 워크로드에 사용할 수 없습니다.

요청을 제출하려면 다음 PowerShell 또는 CLI 명령을 실행합니다.

### <a name="powershell"></a>PowerShell

요청을 제출하려면:

```powershell
Register-AzureRmProviderFeature -FeatureName DLM -ProviderNamespace Microsoft.Storage 
```
다음 명령을 사용하여 등록 승인 상태를 확인할 수 있습니다.
```powershell
Get-AzureRmProviderFeature -FeatureName DLM -ProviderNamespace Microsoft.Storage
```
기능이 승인되고 적절하게 등록되면 "등록됨" 상태가 반환됩니다.

### <a name="cli-20"></a>CLI 2.0

요청을 제출하려면: 
```cli
az feature register –-namespace Microsoft.Storage –-name DLM
```
다음 명령을 사용하여 등록 승인 상태를 확인할 수 있습니다.
```cli
-az feature show –-namespace Microsoft.Storage –-name DLM
```
기능이 승인되고 적절하게 등록되면 "등록됨" 상태가 반환됩니다. 


## <a name="add-or-remove-policies"></a>정책 추가 또는 제거 

[.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/8.0.0-preview), [Python](https://pypi.org/project/azure-mgmt-storage/2.0.0rc3/), [Node.js]( https://www.npmjs.com/package/azure-arm-storage/v/5.0.0), [Ruby]( https://rubygems.org/gems/azure_mgmt_storage/versions/0.16.2) 언어로 Azure Portal, [PowerShell](https://www.powershellgallery.com/packages/AzureRM.Storage/5.0.3-preview), REST API 또는 클라이언트 도구를 사용하여 정책을 추가, 편집 또는 제거할 수 있습니다. 

### <a name="azure-portal"></a>Azure portal

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 저장소 계정으로 이동하려면 모든 리소스를 선택하고 저장소 계정을 선택합니다.

3. [설정] 블레이드의 Blob Service 아래에서 그룹화된 **수명 주기 관리**를 클릭하여 정책을 보거나 변경합니다.

### <a name="powershell"></a>PowerShell

```powershell
$rules = '{ ... }' 

Set-AzureRmStorageAccountManagementPolicy -ResourceGroupName [resourceGroupName] -StorageAccountName [storageAccountName] -Policy $rules 

Get-AzureRmStorageAccountManagementPolicy -ResourceGroupName [resourceGroupName] -StorageAccountName [storageAccountName]
```

> [!NOTE]
저장소 계정에 방화벽 규칙을 사용하도록 설정하면 수명 주기 관리 요청이 차단될 수 있습니다. 예외를 제공하여 차단을 해제할 수 있습니다. 자세한 내용은 [방화벽 및 가상 네트워크 구성](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions)의 예외 섹션을 참조하세요.

## <a name="policies"></a>정책

수명 주기 관리 정책은 JSON 문서의 규칙 컬렉션입니다.

```json
{
  "version": "0.5",
  "rules": [
    {
      "name": "rule1",
      "type": "Lifecycle",
      "definition": {...}
    },
    {
      "name": "rule2",
      "type": "Lifecycle",
      "definition": {...}
    }
  ]
}
```


정책에는 두 개의 필수 매개 변수가 있습니다.

| 매개 변수 이름 | 매개 변수 형식 | 메모 |
|----------------|----------------|-------|
| 버전        | `x.x`로 표현되는 문자열 | 미리 보기 버전 번호는 0.5 |
| 규칙          | 규칙 개체의 배열 | 정책마다 하나 이상의 규칙이 필요합니다. 미리 보기 기간에는 정책당 최대 4개의 규칙을 지정할 수 있습니다. |

규칙에서 필요한 매개 변수는 다음과 같습니다.

| 매개 변수 이름 | 매개 변수 형식 | 메모 |
|----------------|----------------|-------|
| Name           | 문자열 | 규칙 이름에는 모든 영숫자 문자 조합이 허용됩니다. 규칙 이름은 대/소문자를 구분합니다. 정책 내에서 고유해야 합니다. |
| 형식           | 열거형 값 | 미리 보기에 유효한 값은 `Lifecycle` |
| 정의     | 수명 주기 규칙을 정의하는 개체 | 각 정의는 필터 집합과 작업 집합으로 구성됩니다. |

## <a name="rules"></a>규칙

각 규칙 정의는 필터 집합과 작업 집합을 포함합니다. 다음 샘플 규칙은 `foo` 접두사가 붙은 기본 블록 Blob의 계층을 수정합니다. 정책에서 이러한 규칙은 다음과 같이 정의됩니다.

- 마지막으로 수정한 시점으로부터 30일 후 BLOB을 쿨 저장소 계층으로 이동
- 마지막으로 수정한 시점으로부터 90일 후 BLOB을 보관 저장소 계층으로 이동
- 마지막으로 수정한 시점으로부터 2,555일(7년) 후 BLOB 삭제
- 스냅숏을 생성한 시점으로부터 90일 후 BLOB 스냅숏 삭제

```json
{
  "version": "0.5",
  "rules": [ 
    {
      "name": "ruleFoo", 
      "type": "Lifecycle", 
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "foo" ]
        },
        "actions": {
          "baseBlob": {
            "tierToCool": { "daysAfterModificationGreaterThan": 30 },
            "tierToArchive": { "daysAfterModificationGreaterThan": 90 },
            "delete": { "daysAfterModificationGreaterThan": 2555 }
          },
          "snapshot": {
            "delete": { "daysAfterCreationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}

```

### <a name="rule-filters"></a>규칙 필터

필터는 규칙 작업을 저장소 계정 내의 BLOB 작업 하위 집합으로 제한합니다. 여러 필터가 정의되는 경우 모든 필터에서 논리적 `AND`가 수행됩니다.

미리 보기 기간에 유효한 필터는 다음과 같습니다.

| 필터 이름 | 필터 형식 | 메모 | 필수 여부 |
|-------------|-------------|-------|-------------|
| blobTypes   | 미리 정의된 열거형 값의 배열입니다. | 미리 보기 릴리스의 경우 `blockBlob`만 지원됩니다. | 예 |
| prefixMatch | 접두사를 매칭할 문자열 배열입니다. | 정의하지 않을 경우 계정 내 모든 BLOB에 이 규칙이 적용됩니다. | 아니오 |

### <a name="rule-actions"></a>규칙 작업

실행 조건이 충족되면 필터링된 BLOB에 작업이 적용됩니다.

미리 보기에서 수명 주기 관리는 BLOB의 계층 이동 및 삭제와 BLOB 스냅숏 삭제를 지원합니다. 각 규칙의 BLOB 또는 BLOB 스냅숏에 작업이 하나 이상 정의되어야 합니다.

| 조치        | 기본 Blob                                   | 스냅숏      |
|---------------|---------------------------------------------|---------------|
| tierToCool    | 현재 핫 계층에서 BLOB을 지원합니다.         | 지원되지 않음 |
| tierToArchive | 현재 핫 또는 쿨 계층에서 BLOB을 지원합니다. | 지원되지 않음 |
| delete        | 지원됨                                   | 지원됨     |

>[!NOTE] 
동일한 BLOB에 작업이 두 개 이상 정의된 경우 수명 주기 관리는 가장 저렴한 작업을 BLOB에 적용합니다. (예: `delete` 작업이 `tierToArchive` 작업보다 저렴합니다. `tierToArchive` 작업이 `tierToCool` 작업보다 저렴합니다.)

미리 보기에서 작업 실행 조건은 보존 기간을 기반으로 합니다. 기본 BLOB은 마지막으로 수정된 시간을 사용하여 보존 기간을 추적하고 BLOB 스냅숏은 스냅숏 생성 시간을 사용하여 보존 기간을 추적합니다.

| 작업 실행 조건 | 조건 값 | 설명 |
|----------------------------|-----------------|-------------|
| daysAfterModificationGreaterThan | 일 단위로 보존 기간을 나타내는 정수 값 | 기본 BLOB 작업에 유효한 조건 |
| daysAfterCreationGreaterThan     | 일 단위로 보존 기간을 나타내는 정수 값 | BLOB 스냅숏 작업에 유효한 조건 | 

## <a name="examples"></a>예
다음 예는 수명 주기 정책 규칙을 사용하여 일반 시나리오를 해결하는 방법을 보여줍니다.

### <a name="move-aging-data-to-a-cooler-tier"></a>오래된 데이터를 쿨 저장소 계층으로 이동

다음 예는 블록 `foo` 또는 `bar` 접두사가 붙은 블록 Blob을 전환하는 방법을 보여줍니다. 이 정책은 30일 넘게 수정되지 않은 BLOB을 쿨 저장소 계층으로 전환하고, 90일 동안 수정되지 않은 BLOB을 보관 저장소 계층으로 전환합니다.

```json
{
  "version": "0.5",
  "rules": [ 
    {
      "name": "agingRule", 
      "type": "Lifecycle", 
      "definition": 
        {
          "filters": {
            "blobTypes": [ "blockBlob" ],
            "prefixMatch": [ "foo", "bar" ]
          },
          "actions": {
            "baseBlob": {
              "tierToCool": { "daysAfterModificationGreaterThan": 30 },
              "tierToArchive": { "daysAfterModificationGreaterThan": 90 }
            }
          }
        }      
    }
  ]
}
```

### <a name="archive-data-at-ingest"></a>수집 시 데이터 보관 

일부 데이터는 클라우드에 유휴 상태로 유지되며 드물지만 한 번 액세스됩니다. 이 데이터는 수집되는 즉시 보관하는 것이 가장 좋습니다. 다음 수명 주기 정책은 수집 시 데이터를 보관하도록 구성되었습니다. 이 예는 블록 `archive` 접두사가 붙은 저장소 계정의 블록 Blob을 즉시 보관 저장소 계층으로 전환합니다. 마지막으로 수정한 시간으로부터 0일 후 BLOB에 대해 작업을 수행하여 즉시 전환을 완료합니다.

```json
{
  "version": "0.5",
  "rules": [ 
    {
      "name": "archiveRule", 
      "type": "Lifecycle", 
      "definition": 
        {
          "filters": {
            "blobTypes": [ "blockBlob" ],
            "prefixMatch": [ "archive" ]
          },
          "actions": {
            "baseBlob": { 
                "tierToArchive": { "daysAfterModificationGreaterThan": 0 }
            }
          }
        }      
    }
  ]
}

```

### <a name="expire-data-based-on-age"></a>보존 기간에 따라 데이터 만료

어떤 데이터는 비용을 줄이기 위해 또는 정부 규제를 따르기 위해 생성 시점으로부터 며칠 또는 몇 달 후 만료됩니다. 데이터 보존 기간에 따라 삭제하여 데이터를 만료하도록 수명 주기 관리 정책을 설정할 수 있습니다. 다음 예는 365일보다 오래된 모든 블록 Blob(접두사가 정의되지 않은)을 삭제하는 정책을 보여줍니다.

```json
{
  "version": "0.5",
  "rules": [ 
    {
      "name": "expirationRule", 
      "type": "Lifecycle", 
      "definition": 
        {
          "filters": {
            "blobTypes": [ "blockBlob" ]
          },
          "actions": {
            "baseBlob": {
              "delete": { "daysAfterModificationGreaterThan": 365 }
            }
          }
        }      
    }
  ]
}
```

### <a name="delete-old-snapshots"></a>오래된 스냅숏 삭제

전체 수명 주기 동안 주기적으로 수정되고 액세스되는 데이터의 경우 해당 데이터의 이전 버전을 추적하기 위해 스냅숏이 자주 사용됩니다. 스냅숏 기간에 따라 오래된 스냅숏을 삭제하는 정책을 만들 수 있습니다. 스냅숏 기간은 스냅숏 생성 시간을 평가하여 확인합니다. 이 정책 규칙은 `activeData` 접두사가 붙어 있고 스냅숏 생성 후 90일 이상 경과된 블록 Blob 스냅숏을 삭제합니다.

```json
{
  "version": "0.5",
  "rules": [ 
    {
      "name": "snapshotRule", 
      "type": "Lifecycle", 
      "definition": 
        {
          "filters": {
            "blobTypes": [ "blockBlob" ],
            "prefixMatch": [ "activeData" ]
          },
          "actions": {            
            "snapshot": {
              "delete": { "daysAfterCreationGreaterThan": 90 }
            }
          }
        }      
    }
  ]
}
```

## <a name="next-steps"></a>다음 단계

실수로 삭제된 데이터를 복구하는 방법을 알아봅니다.

- [Azure Storage Blob에 대한 일시 삭제](../blobs/storage-blob-soft-delete.md)
