---
title: Azure 저장소 수명 주기 관리
description: 오래된 데이터를 핫에서 쿨로 전환하고 계층을 보관하는 수명 주기 정책 규칙을 만드는 방법을 알아봅니다.
services: storage
author: mhopkins-msft
ms.service: storage
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: mhopkins
ms.reviewer: yzheng
ms.subservice: common
ms.openlocfilehash: ce2559f62d29c7b062cfd1ad1dcb61146adfd91c
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/22/2019
ms.locfileid: "66001750"
---
# <a name="manage-the-azure-blob-storage-lifecycle"></a>Azure Blob 저장소 수명 주기 관리

데이터 집합에는 고유한 수명 주기가 있습니다. 수명 주기 초기에는 사람들이 일부 데이터에 자주 액세스합니다 하지만 액세스 필요성은 데이터가 오래될 수록 크게 줄어듭니다. 어떤 데이터는 클라우드에서 유휴 상태로 유지되고 저장된 후에는 어쩌다가 한 번씩 액세스됩니다. 어떤 데이터는 생성 며칠 후 또는 몇 달 후 만료되고, 또 어떤 데이터 세트는 수명 주기 전체에 걸쳐 적극적으로 읽히고 수정됩니다. Azure Blob 저장소 수명 주기 관리는 GPv2 및 Blob 저장소 계정에 대 한 풍부 하 고 규칙 기반 정책을 제공합니다. 정책을 사용하여 데이터를 적절한 액세스 계층으로 전환하거나 데이터의 수명 주기 후에 만료합니다.

수명 주기 관리 정책을 사용하여 다음을 수행할 수 있습니다.

- BLOB을 쿨 스토리지 계층으로 전환하여(핫 스토리지 계층에서 쿨 스토리지 계층으로, 핫 스토리지 계층에서 보관 스토리지 계층으로 또는 쿨 스토리지 계층에서 보관 스토리지 계층으로) 성능 및 비용 최적화
- 수명 주기가 끝나면 BLOB 삭제
- 스토리지 계정 수준에서 하루에 한 번 실행할 규칙 정의
- 컨테이너 또는 BLOB 하위 집합에 규칙 적용(접두사를 필터로 사용)

데이터에 자주 액세스만 수명 주기의 초기 단계에서 가져오는 위치 하는 경우를 고려해 보십시오 2 주 후에 종종 있습니다. 첫 번째 달 이후에는 데이터 세트에 거의 액세스하지 않습니다. 이 시나리오에서 초기 단계 동안에는 핫 스토리지 계층이 가장 적절합니다. 쿨 저장소 가끔 액세스에 가장 적합합니다. 보관 저장소는 한 달이 지나면 데이터 연령대 후 최상의 계층 옵션입니다. 데이터의 보존 기간에 따라 저장소 계층을 조정하면 요구 사항에 맞는 가장 저렴한 저장소 옵션을 디자인할 수 있습니다. 이와 같이 전환하려면 수명 주기 관리 정책 규칙을 사용하여 오래된 데이터를 쿨 계층으로 이동하면 됩니다.

## <a name="storage-account-support"></a>저장소 계정 지원

수명 주기 관리 정책은 범용 v2(GPv2) 계정과 Blob Storage 계정에 모두 사용할 수 있습니다. Azure portal에서 기존 범용 (GPv1) 계정을 GPv2 계정으로 업그레이드할 수 있습니다. 저장소 계정에 대한 자세한 내용은 [Azure Storage 계정 개요](../common/storage-account-overview.md)를 참조하세요.  

## <a name="pricing"></a>가격

수명 주기 관리 기능을 무료로 제공 됩니다. [Blob 나열](https://docs.microsoft.com/rest/api/storageservices/list-blobs) 및 [Blob 계층 설정](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) API 호출에 대한 일반 작업 비용은 고객에게 청구됩니다. 삭제 작업은 무료입니다. 가격 책정에 대한 자세한 내용은 [블록 Blob 가격](https://azure.microsoft.com/pricing/details/storage/blobs/)을 참조하세요.

## <a name="regional-availability"></a>국가별 가용성

모든 글로벌 Azure 지역에서 수명 주기 관리 기능을 사용할 수 있는 경우

## <a name="add-or-remove-a-policy"></a>정책 추가 또는 제거

추가, 편집 또는 다음 방법 중 하나를 사용 하 여 정책을 제거할 수 있습니다.

* [Azure Portal](https://portal.azure.com)
* [Azure PowerShell](https://github.com/Azure/azure-powershell/releases)
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)
* [REST API](https://docs.microsoft.com/rest/api/storagerp/managementpolicies)

이 문서에는 포털 및 PowerShell 메서드를 사용 하 여 정책을 관리 하는 방법을 보여 줍니다.  

> [!NOTE]
> 저장소 계정에 방화벽 규칙을 사용하도록 설정하면 수명 주기 관리 요청이 차단될 수 있습니다. 예외를 제공하여 이러한 요청을 차단 해제할 수 있습니다. 필요한 바이패스 됩니다: `Logging,  Metrics,  AzureServices`합니다. 자세한 내용은 [방화벽 및 가상 네트워크 구성](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions)의 예외 섹션을 참조하세요.

### <a name="azure-portal"></a>Azure portal

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 선택 **모든 리소스** 를 선택한 다음 저장소 계정입니다.

3. 아래 **Blob Service**를 선택 **수명 주기 관리** 보거나 정책을 변경 합니다.

4. 다음 JSON은에 붙여 넣을 수는 규칙의 예는 **수명 주기 관리** 포털 페이지입니다.

   ```json
   {
     "rules": [
       {
         "name": "ruleFoo",
         "enabled": true,
         "type": "Lifecycle",
         "definition": {
           "filters": {
             "blobTypes": [ "blockBlob" ],
             "prefixMatch": [ "container1/foo" ]
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

5. 이 JSON 예제에 대 한 자세한 내용은 참조는 [정책](#policy) 및 [규칙](#rules) 섹션입니다.

### <a name="powershell"></a>PowerShell

저장소 계정에 정책을 추가 하려면 다음 PowerShell 스크립트를 사용할 수 있습니다. `$rgname` 변수는 리소스 그룹 이름으로 초기화 되어야 합니다. `$accountName` 변수에 저장소 계정 이름으로 초기화 되어야 합니다.

```powershell
#Install the latest module
Install-Module -Name Az -Repository PSGallery

#Initialize the following with your resource group and storage account names
$rgname = ""
$accountName = ""

#Create a new action object
$action = Add-AzStorageAccountManagementPolicyAction -BaseBlobAction Delete -daysAfterModificationGreaterThan 2555
$action = Add-AzStorageAccountManagementPolicyAction -InputObject $action -BaseBlobAction TierToArchive -daysAfterModificationGreaterThan 90
$action = Add-AzStorageAccountManagementPolicyAction -InputObject $action -BaseBlobAction TierToCool -daysAfterModificationGreaterThan 30
$action = Add-AzStorageAccountManagementPolicyAction -InputObject $action -SnapshotAction Delete -daysAfterCreationGreaterThan 90

# Create a new filter object
# PowerShell automatically sets BlobType as “blockblob” because it is the only available option currently
$filter = New-AzStorageAccountManagementPolicyFilter -PrefixMatch ab,cd

#Create a new rule object
#PowerShell automatically sets Type as “Lifecycle” because it is the only available option currently
$rule1 = New-AzStorageAccountManagementPolicyRule -Name Test -Action $action -Filter $filter

#Set the policy
$policy = Set-AzStorageAccountManagementPolicy -ResourceGroupName $rgname -StorageAccountName $accountName -Rule $rule1
```

## <a name="azure-resource-manager-template-with-lifecycle-management-policy"></a>수명 주기 관리 정책 사용 하 여 azure Resource Manager 템플릿

Azure Resource Manager 템플릿을 사용 하 여 수명 주기 관리를 정의할 수 있습니다. 수명 주기 관리 정책 사용 하 여 RA-GRS GPv2 저장소 계정을 배포 하는 샘플 템플릿은 다음과 같습니다.

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {
    "storageAccountName": "[uniqueString(resourceGroup().id)]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "location": "[resourceGroup().location]",
      "apiVersion": "2019-04-01",
      "sku": {
        "name": "Standard_RAGRS"
      },
      "kind": "StorageV2",
      "properties": {
        "networkAcls": {}
      }
    },
    {
      "name": "[concat(variables('storageAccountName'), '/default')]",
      "type": "Microsoft.Storage/storageAccounts/managementPolicies",
      "apiVersion": "2019-04-01",
      "dependsOn": [
        "[variables('storageAccountName')]"
      ],
      "properties": {
        "policy": {...}
      }
    }
  ],
  "outputs": {}
}
```

## <a name="policy"></a>정책

수명 주기 관리 정책은 JSON 문서의 규칙 컬렉션입니다.

```json
{
  "rules": [
    {
      "name": "rule1",
      "enabled": true,
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

정책 규칙의 컬렉션인:

| 매개 변수 이름 | 매개 변수 형식 | 메모 |
|----------------|----------------|-------|
| `rules`        | 규칙 개체의 배열 | 정책 규칙을 하나 이상 필요 합니다. 정책에서 최대 100 개의 규칙을 정의할 수 있습니다.|

정책 내에 있는 각 규칙에는 여러 매개 변수:

| 매개 변수 이름 | 매개 변수 형식 | 메모 | 필수 |
|----------------|----------------|-------|----------|
| `name`         | String |규칙 이름은 최대 256 개의 영숫자 문자를 포함할 수 있습니다. 규칙 이름은 대/소문자를 구분합니다.  정책 내에서 고유해야 합니다. | True |
| `enabled`      | Boolean | 두 일 하는 규칙을 허용 하는 선택적 부울을 사용할 수 없습니다. 기본값은 설정 되어 있지 않으면 true입니다. | 거짓 | 
| `type`         | 열거형 값 | 현재 유효한 형식이 `Lifecycle`합니다. | True |
| `definition`   | 수명 주기 규칙을 정의하는 개체 | 각 정의는 필터 집합과 작업 집합으로 구성됩니다. | True |

## <a name="rules"></a>규칙

각 규칙 정의는 필터 집합과 작업 집합을 포함합니다. [필터 집합](#rule-filters)은 컨테이너 또는 개체 이름 내에서 개체의 특정 집합으로 규칙 작업을 제한합니다. [작업 집합](#rule-actions)은 계층을 적용하거나 필터링된 개체 집합에 대한 작업을 삭제합니다.

### <a name="sample-rule"></a>샘플 규칙

다음 샘플 규칙 필터 내에서 존재 하는 개체에서 작업을 실행 계정 `container1` 로 시작 하 고 `foo`입니다.  

- 마지막으로 수정한 시점으로부터 30일 후 Blob을 쿨 계층으로 이동
- 마지막으로 수정한 시점으로부터 90일 후 Blob을 보관 계층으로 이동
- 마지막으로 수정한 시점으로부터 2,555일(7년) 후 BLOB 삭제
- 스냅샷을 생성한 시점으로부터 90일 후 Blob 스냅샷 삭제

```json
{
  "rules": [
    {
      "name": "ruleFoo",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "container1/foo" ]
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

필터는 규칙 작업을 저장소 계정 내의 BLOB 작업 하위 집합으로 제한합니다. 둘 이상의 필터를 정의하는 경우 논리적 `AND`가 모든 필터에서 실행됩니다.

필터는 다음과 같습니다.

| 필터 이름 | 필터 형식 | 메모 | 필수 |
|-------------|-------------|-------|-------------|
| blobTypes   | 미리 정의된 열거형 값의 배열입니다. | 현재 릴리스에서 지원 `blockBlob`합니다. | 예. |
| prefixMatch | 접두사를 매칭할 문자열 배열입니다. 각 규칙 최대 10 개의 접두사를 정의할 수 있습니다. 접두사 문자열은 컨테이너 이름으로 시작해야 합니다. 예를 들어 아래 모든 blob를 일치 시킬 `https://myaccount.blob.core.windows.net/container1/foo/...` 규칙은 prefixMatch는 `container1/foo`합니다. | PrefixMatch를 정의 하지 않으면 규칙 저장소 계정 내 모든 blob에 적용 됩니다.  | 아닙니다. |

### <a name="rule-actions"></a>규칙 작업

작업 실행된 조건이 충족 되 면 필터링 된 blob에 적용 됩니다.

수명 주기 관리 계층화 및 삭제의 blob 및 blob 스냅숏 삭제를 지원합니다. Blob 또는 Blob 스냅샷에 대한 각 규칙에 하나 이상의 작업을 정의합니다.

| 액션(Action)        | 기본 Blob                                   | 스냅샷      |
|---------------|---------------------------------------------|---------------|
| tierToCool    | 현재 핫 계층에서 Blob을 지원합니다.         | 지원되지 않음 |
| tierToArchive | 현재 핫 또는 쿨 계층에서 Blob을 지원합니다. | 지원되지 않음 |
| delete        | 지원됨                                   | 지원됨     |

>[!NOTE]
>동일한 Blob에 작업을 두 개 이상 정의하는 경우 수명 주기 관리는 가장 저렴한 작업을 Blob에 적용합니다. 예를 들어 `delete` 작업은 `tierToArchive` 작업보다 저렴합니다. `tierToArchive` 작업은 `tierToCool` 작업보다 저렴합니다.

실행된 조건 사용 기간을 기반으로 합니다. 기본 Blob은 마지막으로 수정된 시간을 사용하여 보존 기간을 추적하고 Blob 스냅샷은 스냅샷 생성 시간을 사용하여 보존 기간을 추적합니다.

| 작업 실행 조건             | 조건 값                          | 설명                             |
|----------------------------------|------------------------------------------|-----------------------------------------|
| daysAfterModificationGreaterThan | 일 단위로 보존 기간을 나타내는 정수 값 | 기본 blob 작업에 대 한 조건     |
| daysAfterCreationGreaterThan     | 일 단위로 보존 기간을 나타내는 정수 값 | Blob 스냅숏 작업에 대 한 조건 |

## <a name="examples"></a>예

다음 예는 수명 주기 정책 규칙을 사용하여 일반 시나리오를 해결하는 방법을 보여줍니다.

### <a name="move-aging-data-to-a-cooler-tier"></a>오래된 데이터를 쿨 저장소 계층으로 이동

이 예제는 `container1/foo` 또는 `container2/bar` 접두사가 붙은 블록 Blob을 전환하는 방법을 보여줍니다. 이 정책은 30일 넘게 수정되지 않은 BLOB을 쿨 저장소 계층으로 전환하고, 90일 동안 수정되지 않은 BLOB을 보관 저장소 계층으로 전환합니다.

```json
{
  "rules": [
    {
      "name": "agingRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "container1/foo", "container2/bar" ]
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

일부 데이터는 클라우드에 유휴 상태로 유지되며 드물지만 한 번 액세스됩니다. 다음 수명 주기 정책은 수집 되 면 데이터를 보관 하도록 구성 됩니다. 전환 블록 blob 컨테이너 내에서 저장소 계정에이 예제에서는 `archivecontainer` 는 보관 계층에 있습니다. 전환 후 마지막으로 수정한 시간 0 일 blob에서 작동 하 여 수행 됩니다.

```json
{
  "rules": [
    {
      "name": "archiveRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "archivecontainer" ]
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

일부 데이터 일 또는 월을 만든 후에 만료 될 예정입니다. 데이터 보존 기간에 따라 삭제하여 데이터를 만료하도록 수명 주기 관리 정책을 구성할 수 있습니다. 다음 예제에는 365 일 보다 오래 된 모든 블록 blob을 삭제 하는 정책을 보여 줍니다.

```json
{
  "rules": [
    {
      "name": "expirationRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
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

### <a name="delete-old-snapshots"></a>오래된 스냅샷 삭제

전체 수명 주기 동안 주기적으로 수정되고 액세스되는 데이터의 경우 해당 데이터의 이전 버전을 추적하기 위해 스냅샷이 자주 사용됩니다. 스냅샷 기간에 따라 오래된 스냅샷을 삭제하는 정책을 만들 수 있습니다. 스냅샷 기간은 스냅샷 생성 시간을 평가하여 확인합니다. 이 정책 규칙은 `activedata` 컨테이너 내에 있고 스냅샷 생성 후 90일 이상이 경과된 블록 Blob 스냅샷을 삭제합니다.

```json
{
  "rules": [
    {
      "name": "snapshotRule",
      "enabled": true,
      "type": "Lifecycle",
    "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "activedata" ]
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

## <a name="faq"></a>FAQ

**이유는 작업이 실행 되지 않고 즉시 새 정책을 만든?**  
플랫폼은 하루에 한 번 수명 주기 정책을 실행합니다. 정책에 구성한 후 처음으로 실행 하려면 몇 가지 작업에 대 한 최대 24 시간이 걸릴 수 있습니다.  

**수동으로 보관된에서 blob을 리하이드레이션 하나요, 수 없도록 하려는 것 이동 되지 않도록 다시 보관 계층으로 일시적으로?**  
Blob에 대 한 액세스 계층에서 다른 액세스 계층으로 이동 될 때 해당 마지막 수정 시간이 변경 되지 않습니다. 수동으로 핫 계층으로 보관된에서 blob을 리하이드레이션을 하는 경우이 다시 이동할 수 보관 계층 수명 주기 관리 엔진에서. 일시적으로이 blob에 영향을 주는 규칙을 사용 하지 않도록 설정 하 여 방지할 수 있습니다. Blob을 핫 계층 영구적으로 유지 해야 하는 경우 다른 위치로 복사할 수 있습니다. Blob 수 안전 하 게 다시 이동할 수 계층을 보관 하는 경우 규칙을 다시 활성화할 수 있습니다. 


## <a name="next-steps"></a>다음 단계

실수로 삭제된 데이터를 복구하는 방법을 알아봅니다.

- [Azure Storage blob에 대 한 일시 삭제](../blobs/storage-blob-soft-delete.md)
