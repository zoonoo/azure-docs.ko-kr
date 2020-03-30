---
title: Azure 저장소 수명 주기 관리
description: 오래된 데이터를 핫에서 쿨로 전환하고 계층을 보관하는 수명 주기 정책 규칙을 만드는 방법을 알아봅니다.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 05/21/2019
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: yzheng
ms.openlocfilehash: 238c12baf55b525a24107a727d09588ef06a6bef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77598309"
---
# <a name="manage-the-azure-blob-storage-lifecycle"></a>Azure Blob Storage 수명 주기 관리

데이터 집합에는 고유한 수명 주기가 있습니다. 수명 주기 초기에는 사람들이 일부 데이터에 자주 액세스합니다 하지만 액세스 필요성은 데이터가 오래될 수록 크게 줄어듭니다. 어떤 데이터는 클라우드에서 유휴 상태로 유지되고 저장된 후에는 어쩌다가 한 번씩 액세스됩니다. 어떤 데이터는 생성 며칠 후 또는 몇 달 후 만료되고, 또 어떤 데이터 세트는 수명 주기 전체에 걸쳐 적극적으로 읽히고 수정됩니다. Azure Blob 저장소 수명 주기 관리는 GPv2 및 Blob 저장소 계정에 대한 풍부한 규칙 기반 정책을 제공합니다. 정책을 사용하여 데이터를 적절한 액세스 계층으로 전환하거나 데이터의 수명 주기 후에 만료합니다.

수명 주기 관리 정책을 사용하여 다음을 수행할 수 있습니다.

- BLOB을 쿨 스토리지 계층으로 전환하여(핫 스토리지 계층에서 쿨 스토리지 계층으로, 핫 스토리지 계층에서 보관 스토리지 계층으로 또는 쿨 스토리지 계층에서 보관 스토리지 계층으로) 성능 및 비용 최적화
- 수명 주기가 끝나면 BLOB 삭제
- 스토리지 계정 수준에서 하루에 한 번 실행할 규칙 정의
- 컨테이너 또는 BLOB 하위 집합에 규칙 적용(접두사를 필터로 사용)

수명 주기의 초기 단계에서 데이터에 자주 액세스하지만 2주 후에만 데이터에 액세스하는 시나리오를 생각해 보십시오. 첫 번째 달 이후에는 데이터 세트에 거의 액세스하지 않습니다. 이 시나리오에서 초기 단계 동안에는 핫 스토리지 계층이 가장 적절합니다. 쿨 스토리지는 가끔 액세스하는 데 가장 적합합니다. 아카이브 스토리지는 한 달 동안 데이터가 사용 된 후 최상의 계층 옵션입니다. 데이터의 보존 기간에 따라 스토리지 계층을 조정하면 요구 사항에 맞는 가장 저렴한 스토리지 옵션을 디자인할 수 있습니다. 이와 같이 전환하려면 수명 주기 관리 정책 규칙을 사용하여 오래된 데이터를 쿨 계층으로 이동하면 됩니다.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="storage-account-support"></a>스토리지 계정 지원

수명 주기 관리 정책은 범용 v2(GPv2) 계정, Blob 저장소 계정 및 프리미엄 블록 Blob 저장소 계정에서 사용할 수 있습니다. Azure 포털에서 기존 범용(GPv1) 계정을 GPv2 계정으로 업그레이드할 수 있습니다. 스토리지 계정에 대한 자세한 내용은 [Azure Storage 계정 개요](../common/storage-account-overview.md)를 참조하세요.  

## <a name="pricing"></a>가격 책정

수명 주기 관리 기능은 무료입니다. [Blob 나열](https://docs.microsoft.com/rest/api/storageservices/list-blobs) 및 [Blob 계층 설정](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) API 호출에 대한 일반 작업 비용은 고객에게 청구됩니다. 삭제 작업은 무료입니다. 가격 책정에 대한 자세한 내용은 [블록 Blob 가격](https://azure.microsoft.com/pricing/details/storage/blobs/)을 참조하세요.

## <a name="regional-availability"></a>국가별 가용성

수명 주기 관리 기능은 모든 Azure 지역에서 사용할 수 있습니다.

## <a name="add-or-remove-a-policy"></a>정책 추가 또는 제거

다음 방법 중 한 가지를 사용하여 정책을 추가, 편집 또는 제거할 수 있습니다.

* [Azure 포털](https://portal.azure.com)
* [Azure 파워쉘](https://github.com/Azure/azure-powershell/releases)
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)
* [레스트 API](https://docs.microsoft.com/rest/api/storagerp/managementpolicies)

정책을 읽거나 전체적으로 작성할 수 있습니다. 부분 업데이트는 지원되지 않습니다. 

> [!NOTE]
> 스토리지 계정에 방화벽 규칙을 사용하도록 설정하면 수명 주기 관리 요청이 차단될 수 있습니다. 신뢰할 수 있는 Microsoft 서비스에 대한 예외를 제공하여 이러한 요청을 차단 해제할 수 있습니다. 자세한 내용은 [방화벽 및 가상 네트워크 구성](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions)의 예외 섹션을 참조하세요.

이 문서에서는 포털 및 PowerShell 메서드를 사용하여 정책을 관리하는 방법을 보여 주며 정책 관리 방법을 보여 주며 이 문서에서는 정책을 관리하는 방법을 보여 주며 정책 관리 방법을 보여 주시고 있습니다.  

# <a name="portal"></a>[포털](#tab/azure-portal)

Azure 포털을 통해 정책을 추가하는 방법에는 두 가지가 있습니다. 

* [Azure 포털 목록 보기](#azure-portal-list-view)
* [Azure 포털 코드 보기](#azure-portal-code-view)

#### <a name="azure-portal-list-view"></a>Azure 포털 목록 보기

1. [Azure 포털에](https://portal.azure.com)로그인합니다.

2. Azure 포털에서 저장소 계정을 검색하고 선택합니다. 

3. **Blob 서비스에서** **수명 주기 관리를** 선택하여 규칙을 보거나 변경합니다.

4. 목록 보기 탭을 **선택합니다.**

5. **규칙 추가를** 선택한 다음 작업 세트 양식 필드를 **작성합니다.** 다음 예제에서는 Blob이 30일 동안 수정되지 않은 경우 멋진 저장소로 이동됩니다.

   ![Azure 포털의 라이프사이클 관리 작업 집합 페이지](media/storage-lifecycle-management-concepts/lifecycle-management-action-set.png)

6. **선택적 필터를** 추가하려면 필터 세트를 선택합니다. 그런 다음 **찾아보기를** 선택하여 필터링할 컨테이너 및 폴더를 지정합니다.

   ![Azure 포털의 라이프사이클 관리 필터 집합 페이지](media/storage-lifecycle-management-concepts/lifecycle-management-filter-set-browse.png)

8. **검토 + 추가를** 선택하여 정책 설정을 검토합니다.

9. 새 정책을 추가하려면 **추가를** 선택합니다.

#### <a name="azure-portal-code-view"></a>Azure 포털 코드 보기
1. [Azure 포털에](https://portal.azure.com)로그인합니다.

2. Azure 포털에서 저장소 계정을 검색하고 선택합니다.

3. **Blob 서비스에서** **수명 주기 관리를** 선택하여 정책을 보거나 변경합니다.

4. 다음 JSON은 **코드 보기** 탭에 붙여넣을 수 있는 정책의 예입니다.

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

5. **저장**을 선택합니다.

6. 이 JSON 예제에 대한 자세한 내용은 [정책](#policy) 및 [규칙](#rules) 섹션을 참조하십시오.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

다음 PowerShell 스크립트를 사용하여 저장소 계정에 정책을 추가할 수 있습니다. 변수는 `$rgname` 리소스 그룹 이름으로 초기화되어야 합니다. 변수는 `$accountName` 저장소 계정 이름으로 초기화해야 합니다.

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

# <a name="template"></a>[템플릿](#tab/template)

Azure 리소스 관리자 템플릿을 사용하여 수명 주기 관리를 정의할 수 있습니다. 다음은 수명 주기 관리 정책이 있는 RA-GRS GPv2 저장소 계정을 배포하는 샘플 템플릿입니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

---

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

정책은 다음과 같은 규칙의 모음입니다.

| 매개 변수 이름 | 매개 변수 형식 | 메모 |
|----------------|----------------|-------|
| `rules`        | 규칙 개체의 배열 | 정책에 하나 이상의 규칙이 필요합니다. 정책에서 최대 100개의 규칙을 정의할 수 있습니다.|

정책 내의 각 규칙에는 다음과 같은 몇 가지 매개 변수가 있습니다.

| 매개 변수 이름 | 매개 변수 형식 | 메모 | 필수 |
|----------------|----------------|-------|----------|
| `name`         | String |규칙 이름에는 최대 256개의 상숫자 문자가 포함될 수 있습니다. 규칙 이름은 대/소문자를 구분합니다.  정책 내에서 고유해야 합니다. | True |
| `enabled`      | 부울 | 규칙을 임시 로 사용할 수 있도록 하는 선택적 부울입니다. 기본값은 설정되지 않은 경우 true입니다. | False | 
| `type`         | 열거형 값 | 현재 유효한 형식은 . `Lifecycle` | True |
| `definition`   | 수명 주기 규칙을 정의하는 개체 | 각 정의는 필터 집합과 작업 집합으로 구성됩니다. | True |

## <a name="rules"></a>규칙

각 규칙 정의는 필터 집합과 작업 집합을 포함합니다. [필터 집합](#rule-filters)은 컨테이너 또는 개체 이름 내에서 개체의 특정 집합으로 규칙 작업을 제한합니다. [작업 집합](#rule-actions)은 계층을 적용하거나 필터링된 개체 집합에 대한 작업을 삭제합니다.

### <a name="sample-rule"></a>샘플 규칙

다음 샘플 규칙은 계정을 필터링하여 내부에 `container1` 있는 개체에 대한 `foo`작업을 실행하고 로 시작합니다.  

>[!NOTE]
>수명 주기 관리는 블록 Blob 유형만 지원합니다.  

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

필터는 규칙 작업을 스토리지 계정 내의 BLOB 작업 하위 집합으로 제한합니다. 둘 이상의 필터를 정의하는 경우 논리적 `AND`가 모든 필터에서 실행됩니다.

필터에는 다음이 포함됩니다.

| 필터 이름 | 필터 형식 | 메모 | 필수 여부 |
|-------------|-------------|-------|-------------|
| blobTypes   | 미리 정의된 열거형 값의 배열입니다. | 현재 릴리스는 `blockBlob`을 지원합니다. | yes |
| prefixMatch | 접두사를 매칭할 문자열 배열입니다. 각 규칙은 최대 10개의 접두사를 정의할 수 있습니다. 접두사 문자열은 컨테이너 이름으로 시작해야 합니다. 예를 들어 규칙에 따라 아래의 `https://myaccount.blob.core.windows.net/container1/foo/...` 모든 Blob을 일치하려면 접두사Match가 있습니다. `container1/foo` | 접두사를 정의하지 않으면규칙이 저장소 계정 내의 모든 Blob에 적용됩니다.  | 예 |

### <a name="rule-actions"></a>규칙 작업

실행 조건이 충족되면 필터링된 Blob에 작업이 적용됩니다.

수명 주기 관리는 Blob의 계층화 및 삭제 및 Blob 스냅숏 삭제를 지원합니다. Blob 또는 Blob 스냅샷에 대한 각 규칙에 하나 이상의 작업을 정의합니다.

| 작업        | 기본 Blob                                   | 스냅샷      |
|---------------|---------------------------------------------|---------------|
| tierToCool    | 현재 핫 계층에서 Blob을 지원합니다.         | 지원되지 않음 |
| tierToArchive | 현재 핫 또는 쿨 계층에서 Blob을 지원합니다. | 지원되지 않음 |
| delete        | 지원됨                                   | 지원됨     |

>[!NOTE]
>동일한 Blob에 작업을 두 개 이상 정의하는 경우 수명 주기 관리는 가장 저렴한 작업을 Blob에 적용합니다. 예를 들어 `delete` 작업은 `tierToArchive` 작업보다 저렴합니다. `tierToArchive` 작업은 `tierToCool` 작업보다 저렴합니다.

실행 조건은 연령을 기준으로 합니다. 기본 Blob은 마지막으로 수정된 시간을 사용하여 보존 기간을 추적하고 Blob 스냅샷은 스냅샷 생성 시간을 사용하여 보존 기간을 추적합니다.

| 작업 실행 조건             | 조건 값                          | 설명                             |
|----------------------------------|------------------------------------------|-----------------------------------------|
| daysAfterModificationGreaterThan | 일 단위로 보존 기간을 나타내는 정수 값 | 기본 Blob 작업의 조건     |
| daysAfterCreationGreaterThan     | 일 단위로 보존 기간을 나타내는 정수 값 | Blob 스냅숏 작업에 대한 조건 |

## <a name="examples"></a>예

다음 예는 수명 주기 정책 규칙을 사용하여 일반 시나리오를 해결하는 방법을 보여줍니다.

### <a name="move-aging-data-to-a-cooler-tier"></a>오래된 데이터를 쿨 저장소 계층으로 이동

이 예제는 `container1/foo` 또는 `container2/bar` 접두사가 붙은 블록 Blob을 전환하는 방법을 보여줍니다. 이 정책은 30일 넘게 수정되지 않은 BLOB을 쿨 스토리지 계층으로 전환하고, 90일 동안 수정되지 않은 BLOB을 보관 스토리지 계층으로 전환합니다.

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

### <a name="archive-data-after-ingest"></a>인제스트 후 데이터 보관

일부 데이터는 클라우드에 유휴 상태로 유지되며 드물지만 한 번 액세스됩니다. 다음 수명 주기 정책은 데이터를 수집한 직후에 데이터를 보관하도록 구성됩니다. 이 예제에서는 컨테이너 `archivecontainer` 내의 저장소 계정의 블록 Blob을 아카이브 계층으로 전환합니다. 전환은 마지막으로 수정된 시간 후 0일 후에 Blobs에 대해 작동하여 수행됩니다.

> [!NOTE] 
> 더 효율적으로 하려면 아카이브 계층에 Blob을 직접 업로드하는 것이 좋습니다. Rest 버전 2018-11-09 및 최신 또는 최신 Blob 저장소 클라이언트 라이브러리가 있는 [PutBlob](https://docs.microsoft.com/rest/api/storageservices/put-blob) 또는 [PutBlockList에](https://docs.microsoft.com/rest/api/storageservices/put-block-list) x-ms-acess-tier 헤더를 사용할 수 있습니다. 

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

일부 데이터는 생성 후 며칠 또는 몇 달 후에 만료될 것으로 예상됩니다. 데이터 보존 기간에 따라 삭제하여 데이터를 만료하도록 수명 주기 관리 정책을 구성할 수 있습니다. 다음 예제에서는 365일이 지난 모든 블록 Blob을 삭제하는 정책을 보여 주며 있습니다.

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

**새 정책을 만들었는데, 왜 작업이 즉시 실행되지 않습니까?**  
플랫폼은 하루에 한 번 수명 주기 정책을 실행합니다. 정책을 구성하면 일부 작업이 처음으로 실행되는 데 최대 24시간이 걸릴 수 있습니다.  

**기존 정책을 업데이트하는 경우 작업이 실행되는 데 얼마나 걸입니까?**  
업데이트된 정책이 발효되는 데 최대 24시간이 걸립니다. 정책이 적용되면 작업이 실행되는 데 최대 24시간이 걸릴 수 있습니다. 따라서 정책 작업을 완료하는 데 최대 48시간이 걸릴 수 있습니다.   

**보관된 Blob을 수동으로 재수화한 경우 보관 계층으로 일시적으로 다시 이동하지 않도록 하려면 어떻게 해야 합니까?**  
Blob이 한 액세스 계층에서 다른 액세스 계층으로 이동되면 마지막 수정 시간은 변경되지 않습니다. 보관된 Blob을 핫 계층으로 수동으로 수화하면 수명 주기 관리 엔진에서 보관 계층으로 다시 이동합니다. 이 Blob에 일시적으로 영향을 주는 규칙을 사용하지 않도록 설정하여 다시 보관하지 않도록 합니다. Blob을 보관 계층으로 안전하게 다시 이동할 수 있는 경우 규칙을 다시 활성화합니다. 핫 또는 쿨 티어에 영구적으로 유지해야 하는 경우 Blob을 다른 위치로 복사할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

실수로 삭제된 데이터를 복구하는 방법을 알아봅니다.

- [Azure Storage Blob에 대한 일시 삭제](../blobs/storage-blob-soft-delete.md)
