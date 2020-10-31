---
title: Azure Blob Storage 액세스 계층을 자동화 하 여 비용 최적화
description: 핫, 쿨 및 보관 계층 간에 데이터를 이동 하기 위한 자동화 된 규칙을 만듭니다.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 10/29/2020
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: yzheng
ms.custom: devx-track-azurepowershell, references_regions
ms.openlocfilehash: a4a338a4d13715ba1ff7cb30c011757d5050ba05
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93100072"
---
# <a name="optimize-costs-by-automating-azure-blob-storage-access-tiers"></a>Azure Blob Storage 액세스 계층을 자동화 하 여 비용 최적화

데이터 집합에는 고유한 수명 주기가 있습니다. 수명 주기 초기에는 사람들이 일부 데이터에 자주 액세스합니다 하지만 액세스 필요성은 데이터가 오래될 수록 크게 줄어듭니다. 어떤 데이터는 클라우드에서 유휴 상태로 유지되고 저장된 후에는 어쩌다가 한 번씩 액세스됩니다. 어떤 데이터는 생성 며칠 후 또는 몇 달 후 만료되고, 또 어떤 데이터 세트는 수명 주기 전체에 걸쳐 적극적으로 읽히고 수정됩니다. Azure Blob Storage 수명 주기 관리는 GPv2 및 Blob 저장소 계정에 대 한 다양 한 규칙 기반 정책을 제공 합니다. 정책을 사용하여 데이터를 적절한 액세스 계층으로 전환하거나 데이터의 수명 주기 후에 만료합니다.

수명 주기 관리 정책을 사용하여 다음을 수행할 수 있습니다.

- 성능 최적화를 위해 액세스 한 경우 쿨에서 핫으로 blob 전환 
- 비용을 최적화 하는 일정 기간 동안 액세스 하거나 수정 하지 않은 경우 blob, blob 버전 및 blob 스냅숏을 냉각기 저장소 계층 (핫에서 쿨, 핫에서 보관 중 또는 쿨로 보관)으로 전환
- 수명 주기 끝에서 blob, blob 버전 및 blob 스냅숏 삭제
- 스토리지 계정 수준에서 하루에 한 번 실행할 규칙 정의
- 컨테이너 또는 blob의 하위 집합에 규칙 적용 (이름 접두사 또는 [blob 인덱스 태그](storage-manage-find-blobs.md) 를 필터로 사용)

수명 주기의 초기 단계에서 데이터가 자주 액세스 되는 시나리오를 고려 하 여 2 주 후에만 가끔 액세스 합니다. 첫 번째 달 이후에는 데이터 세트에 거의 액세스하지 않습니다. 이 시나리오에서 초기 단계 동안에는 핫 스토리지 계층이 가장 적절합니다. 쿨 저장소는 간헐적으로 액세스 하는 데 가장 적합 합니다. 보관 저장소는 한 달에 데이터를 사용한 후 가장 좋은 계층 옵션입니다. 데이터의 보존 기간에 따라 스토리지 계층을 조정하면 요구 사항에 맞는 가장 저렴한 스토리지 옵션을 디자인할 수 있습니다. 이와 같이 전환하려면 수명 주기 관리 정책 규칙을 사용하여 오래된 데이터를 쿨 계층으로 이동하면 됩니다.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

>[!NOTE]
>데이터를 읽을 수 있어야 하는 경우, 예를 들어 StorSimple에서 사용 하는 경우 blob을 보관 계층으로 이동 하는 정책을 설정 하지 마십시오.

## <a name="availability-and-pricing"></a>가용성 및 가격 책정

수명 주기 관리 기능은 범용 v2 (GPv2) 계정, blob storage 계정, 프리미엄 블록 Blob storage 계정 및 Azure Data Lake Storage Gen2 계정에 대 한 모든 Azure 지역에서 사용할 수 있습니다. Azure Portal에서 기존 범용 (GPv1) 계정을 GPv2 계정으로 업그레이드할 수 있습니다. 스토리지 계정에 대한 자세한 내용은 [Azure Storage 계정 개요](../common/storage-account-overview.md)를 참조하세요.

수명 주기 관리 기능은 무료로 제공 됩니다. 고객에 게는 [Set Blob 계층](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) API 호출에 대 한 일반 작업 비용이 청구 됩니다. 삭제 작업은 무료입니다. 가격 책정에 대한 자세한 내용은 [블록 Blob 가격](https://azure.microsoft.com/pricing/details/storage/blobs/)을 참조하세요.

## <a name="add-or-remove-a-policy"></a>정책 추가 또는 제거

다음 방법 중 하나를 사용 하 여 정책을 추가, 편집 또는 제거할 수 있습니다.

* [Azure Portal](https://portal.azure.com)
* [Azure PowerShell](https://github.com/Azure/azure-powershell/releases)
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)
* [REST API](https://docs.microsoft.com/rest/api/storagerp/managementpolicies)

정책은 전체로 읽거나 쓸 수 있습니다. 부분 업데이트는 지원 되지 않습니다. 

> [!NOTE]
> 스토리지 계정에 방화벽 규칙을 사용하도록 설정하면 수명 주기 관리 요청이 차단될 수 있습니다. 신뢰할 수 있는 Microsoft 서비스에 대 한 예외를 제공 하 여 이러한 요청의 차단을 해제할 수 있습니다. 자세한 내용은 [방화벽 및 가상 네트워크 구성](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions)의 예외 섹션을 참조하세요.

이 문서에서는 포털 및 PowerShell 메서드를 사용 하 여 정책을 관리 하는 방법을 보여 줍니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal를 통해 정책을 추가 하는 방법에는 두 가지가 있습니다. 

* [Azure Portal 목록 뷰](#azure-portal-list-view)
* [Azure Portal 코드 보기](#azure-portal-code-view)

#### <a name="azure-portal-list-view"></a>Azure Portal 목록 뷰

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. Azure Portal에서 저장소 계정을 검색 하 여 선택 합니다. 

1. **Blob service** 에서 **수명 주기 관리** 를 선택 하 여 규칙을 보거나 변경 합니다.

1. **목록 보기** 탭을 선택 합니다.

1. **규칙 추가** 를 선택 하 고 **세부 정보** 양식에서 규칙의 이름을로 설정 합니다. **규칙 범위** , **Blob 유형** 및 **blob 하위 유형** 값을 설정할 수도 있습니다. 다음 예에서는 blob을 필터링 하도록 범위를 설정 합니다. 이렇게 하면 **필터 집합** 탭이 추가 됩니다.

   :::image type="content" source="media/storage-lifecycle-management-concepts/lifecycle-management-details.png" alt-text="수명 주기 관리 Azure Portal에서 규칙 세부 정보 페이지 추가":::

1. **기본 blob** 을 선택 하 여 규칙에 대 한 조건을 설정 합니다. 다음 예에서는 30 일 동안 수정 되지 않은 경우 blob을 쿨 저장소로 이동 합니다.

   :::image type="content" source="media/storage-lifecycle-management-concepts/lifecycle-management-base-blobs.png" alt-text="수명 주기 관리 Azure Portal에서 규칙 세부 정보 페이지 추가":::

   **마지막으로 액세스** 한 옵션은 다음 지역에서 미리 보기로 제공 됩니다.

    - 프랑스 중부
    - 캐나다 동부
    - 캐나다 중부

   > [!IMPORTANT]
   > 마지막 액세스 시간 추적 미리 보기는 비프로덕션 으로만 사용 됩니다. 현재 프로덕션 SLA(서비스 수준 계약)는 사용할 수 없습니다.
   
   **마지막으로 액세스** 한 옵션을 사용 하려면 Azure Portal의 **수명 주기 관리** 페이지에서 **액세스 추적 사용** 을 선택 합니다. **마지막으로 액세스** 한 옵션에 대 한 자세한 내용은 [마지막으로 액세스 한 날짜를 기준으로 데이터 이동 (미리 보기)](#move-data-based-on-last-accessed-date-preview)을 참조 하세요.

1. **자세히** 페이지에서 필터 **를 사용 하 여 blob 제한** 을 선택한 경우 **필터 설정** 을 선택 하 여 선택적 필터를 추가 합니다. 다음 예제에서는 "log"로 시작 하는 *mylifecyclecontainer* 컨테이너의 blob을 필터링 합니다.

   :::image type="content" source="media/storage-lifecycle-management-concepts/lifecycle-management-filter-set.png" alt-text="수명 주기 관리 Azure Portal에서 규칙 세부 정보 페이지 추가":::

1. **추가** 를 선택 하 여 새 정책을 추가 합니다.

#### <a name="azure-portal-code-view"></a>Azure Portal 코드 보기
1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. Azure Portal에서 저장소 계정을 검색 하 여 선택 합니다.

1. **Blob service** 에서 **수명 주기 관리** 를 선택 하 여 정책을 보거나 변경 합니다.

1. 다음 JSON은 **코드 보기** 탭에 붙여 넣을 수 있는 정책의 예입니다.

   ```json
   {
     "rules": [
       {
         "enabled": true,
         "name": "move-to-cool",
         "type": "Lifecycle",
         "definition": {
           "actions": {
             "baseBlob": {
               "tierToCool": {
                 "daysAfterModificationGreaterThan": 30
               }
             }
           },
           "filters": {
             "blobTypes": [
               "blockBlob"
             ],
             "prefixMatch": [
               "mylifecyclecontainer/log"
             ]
           }
         }
       }
     ]
   }
   ```

1. **저장** 을 선택합니다.

1. 이 JSON 예제에 대 한 자세한 내용은 [정책](#policy) 및 [규칙](#rules) 섹션을 참조 하십시오.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

다음 PowerShell 스크립트를 사용 하 여 저장소 계정에 정책을 추가할 수 있습니다. `$rgname` 변수는 리소스 그룹 이름으로 초기화해야 합니다. `$accountName` 변수는 스토리지 계정 이름으로 초기화해야 합니다.

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
Set-AzStorageAccountManagementPolicy -ResourceGroupName $rgname -StorageAccountName $accountName -Rule $rule1
```

# <a name="template"></a>[템플릿](#tab/template)

Azure Resource Manager 템플릿을 사용 하 여 수명 주기 관리를 정의할 수 있습니다. 다음은 수명 주기 관리 정책으로 RA GRS GPv2 storage 계정을 배포 하는 샘플 템플릿입니다.

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

정책은 규칙의 컬렉션입니다.

| 매개 변수 이름 | 매개 변수 형식 | 참고 |
|----------------|----------------|-------|
| `rules`        | 규칙 개체의 배열 | 정책에 하나 이상의 규칙이 필요 합니다. 정책에서 최대 100 개의 규칙을 정의할 수 있습니다.|

정책 내의 각 규칙에는 다음과 같은 몇 가지 매개 변수가 있습니다.

| 매개 변수 이름 | 매개 변수 형식 | 참고 | 필수 |
|----------------|----------------|-------|----------|
| `name`         | String |규칙 이름에는 최대 256 자의 영숫자 문자를 사용할 수 있습니다. 규칙 이름은 대/소문자를 구분합니다. 정책 내에서 고유해야 합니다. | 참 |
| `enabled`      | 부울 | 규칙을 일시적으로 사용 하지 않도록 설정할 수 있도록 하는 선택적 부울입니다. 설정 되지 않은 경우 기본값은 true입니다. | False | 
| `type`         | 열거형 값 | 현재 유효한 형식은 `Lifecycle` 입니다. | 참 |
| `definition`   | 수명 주기 규칙을 정의하는 개체 | 각 정의는 필터 집합과 작업 집합으로 구성됩니다. | 참 |

## <a name="rules"></a>규칙

각 규칙 정의는 필터 집합과 작업 집합을 포함합니다. [필터 집합](#rule-filters)은 컨테이너 또는 개체 이름 내에서 개체의 특정 집합으로 규칙 작업을 제한합니다. [작업 집합](#rule-actions)은 계층을 적용하거나 필터링된 개체 집합에 대한 작업을 삭제합니다.

### <a name="sample-rule"></a>샘플 규칙

다음 샘플 규칙은 내부에 존재 하는 개체에 대 한 작업을 실행 하 `container1` 고로 시작 하는 계정을 필터링 합니다 `foo` .

>[!NOTE]
>- 수명 주기 관리는 블록 blob 및 추가 blob 유형을 지원 합니다.<br>
>- 수명 주기 관리는 $logs 및 $web와 같은 시스템 컨테이너에 영향을 주지 않습니다.

- 마지막으로 수정한 시점으로부터 30일 후 Blob을 쿨 계층으로 이동
- 마지막으로 수정한 시점으로부터 90일 후 Blob을 보관 계층으로 이동
- 마지막으로 수정한 시점으로부터 2,555일(7년) 후 BLOB 삭제
- 만든 후 이전 blob 버전 90 일을 삭제 합니다.

```json
{
  "rules": [
    {
      "enabled": true,
      "name": "rulefoo",
      "type": "Lifecycle",
      "definition": {
        "actions": {
          "version": {
            "delete": {
              "daysAfterCreationGreaterThan": 90
            }
          },
          "baseBlob": {
            "tierToCool": {
              "daysAfterModificationGreaterThan": 30
            },
            "tierToArchive": {
              "daysAfterModificationGreaterThan": 90
            },
            "delete": {
              "daysAfterModificationGreaterThan": 2555
            }
          }
        },
        "filters": {
          "blobTypes": [
            "blockBlob"
          ],
          "prefixMatch": [
            "container1/foo"
          ]
        }
      }
    }
  ]
}
```

### <a name="rule-filters"></a>규칙 필터

필터는 규칙 작업을 스토리지 계정 내의 BLOB 작업 하위 집합으로 제한합니다. 둘 이상의 필터를 정의하는 경우 논리적 `AND`가 모든 필터에서 실행됩니다.

필터에는 다음이 포함됩니다.

| 필터 이름 | 필터 형식 | 참고 | 필수 여부 |
|-------------|-------------|-------|-------------|
| blobTypes   | 미리 정의된 열거형 값의 배열입니다. | 현재 릴리스에서는 및를 지원 합니다 `blockBlob` `appendBlob` . 에는 delete만 지원 되며 `appendBlob` 집합 계층은 지원 되지 않습니다. | Yes |
| prefixMatch | 일치 시킬 접두사의 문자열 배열입니다. 각 규칙은 최대 10 개의 접두사를 정의할 수 있습니다. 접두사 문자열은 컨테이너 이름으로 시작해야 합니다. 예를 들어에서 규칙에 대 한 모든 blob을 일치 시키려는 경우 `https://myaccount.blob.core.windows.net/container1/foo/...` prefixMatch은 `container1/foo` 입니다. | PrefixMatch를 정의 하지 않으면 규칙은 저장소 계정 내의 모든 blob에 적용 됩니다. | 아니요 |
| blobIndexMatch | 일치 시킬 Blob 인덱스 태그 키 및 값 조건으로 구성 된 사전 값의 배열입니다. 각 규칙은 최대 10 개의 Blob 인덱스 태그 조건을 정의할 수 있습니다. 예를 들어 규칙에 대해에서의 모든 blob을 일치 시키려는 경우 `Project = Contoso` `https://myaccount.blob.core.windows.net/` blobIndexMatch는 `{"name": "Project","op": "==","value": "Contoso"}` 입니다. | BlobIndexMatch를 정의 하지 않으면 규칙은 저장소 계정 내의 모든 blob에 적용 됩니다. | 아니요 |

> [!NOTE]
> Blob 인덱스는 공개 미리 보기 상태 이며 **캐나다 중부** , **캐나다 동부** , **프랑스 중부** 및 **프랑스 남부** 지역에서 사용할 수 있습니다. 알려진 문제 및 제한과 함께 이 기능에 대한 자세한 내용은 [Blob 인덱스(미리 보기)를 사용하여 Azure Blob 스토리지에서 데이터 관리 및 찾기](storage-manage-find-blobs.md)를 참조하세요.

### <a name="rule-actions"></a>규칙 작업

실행 조건이 충족 되 면 필터링 된 blob에 동작이 적용 됩니다.

수명 주기 관리는 blob, 이전 blob 버전 및 blob 스냅숏의 계층화 및 삭제를 지원 합니다. 기본 blob의 각 규칙, 이전 blob 버전 또는 blob 스냅숏에 대해 하나 이상의 작업을 정의 합니다.

| 작업                      | 기본 Blob                                  | 스냅샷      | 버전
|-----------------------------|--------------------------------------------|---------------|---------------|
| tierToCool                  | `blockBlob`에 지원됨                  | 지원됨     | 지원됨     |
| enableAutoTierToHotFromCool | `blockBlob`에 지원됨                  | 지원 안 함 | 지원 안 함 |
| tierToArchive               | `blockBlob`에 지원됨                  | 지원됨     | 지원됨     |
| delete                      | 및에 대해 지원 됩니다. `blockBlob``appendBlob` | 지원됨     | 지원됨     |

>[!NOTE]
>동일한 Blob에 작업을 두 개 이상 정의하는 경우 수명 주기 관리는 가장 저렴한 작업을 Blob에 적용합니다. 예를 들어 `delete` 작업은 `tierToArchive` 작업보다 저렴합니다. `tierToArchive` 작업은 `tierToCool` 작업보다 저렴합니다.

실행 조건은 age를 기준으로 합니다. 기본 blob는 마지막으로 수정 된 시간을 사용 하 고 blob 버전은 버전 만든 시간을 사용 하며 blob 스냅숏은 스냅숏 생성 시간을 사용 하 여 사용 기간을 추적 합니다.

| 작업 실행 조건               | 조건 값                          | 설명                                                                      |
|------------------------------------|------------------------------------------|----------------------------------------------------------------------------------|
| daysAfterModificationGreaterThan   | 일 단위로 보존 기간을 나타내는 정수 값 | 기본 blob 동작의 조건입니다.                                              |
| daysAfterCreationGreaterThan       | 일 단위로 보존 기간을 나타내는 정수 값 | Blob 버전 및 blob 스냅숏 작업에 대 한 조건                         |
| daysAfterLastAccessTimeGreaterThan | 일 단위로 보존 기간을 나타내는 정수 값 | 모드 마지막으로 액세스 한 시간을 사용 하는 경우 기본 blob 동작에 대 한 조건 |

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

### <a name="move-data-based-on-last-accessed-date-preview"></a>마지막으로 액세스 한 날짜를 기준으로 데이터 이동 (미리 보기)

마지막 액세스 시간 추적을 설정 하 여 blob을 마지막으로 읽거나 쓸 때의 레코드를 유지할 수 있습니다. 마지막 액세스 시간을 필터로 사용 하 여 blob 데이터의 계층화 및 보존을 관리할 수 있습니다.

**마지막으로 액세스** 한 옵션은 다음 지역에서 미리 보기로 제공 됩니다.

 - 프랑스 중부
 - 캐나다 동부
 - 캐나다 중부

> [!IMPORTANT]
> 마지막 액세스 시간 추적 미리 보기는 비프로덕션 으로만 사용 됩니다. 현재 프로덕션 SLA(서비스 수준 계약)는 사용할 수 없습니다.

**마지막으로 액세스** 한 옵션을 사용 하려면 Azure Portal의 **수명 주기 관리** 페이지에서 **액세스 추적 사용** 을 선택 합니다.

#### <a name="how-last-access-time-tracking-works"></a>마지막 액세스 시간 추적 작동 방법

마지막 액세스 시간 추적을 사용 하도록 설정 하면 `LastAccessTime` blob을 읽거나 쓸 때 라는 blob 속성이 업데이트 됩니다. [Blob 가져오기](/rest/api/storageservices/get-blob) 작업은 액세스 작업으로 간주 됩니다. Blob [속성](/rest/api/storageservices/get-blob-properties)가져오기, [blob 메타 데이터 가져오기](/rest/api/storageservices/get-blob-metadata)및 [blob 태그 가져오기](/rest/api/storageservices/get-blob-tags) 는 액세스할 수 없으므로 마지막 액세스 시간이 업데이트 되지 않습니다.

읽기 액세스 대기 시간에 대 한 영향을 최소화 하기 위해 최근 24 시간 동안의 첫 번째 읽기만 마지막 액세스 시간을 업데이트 합니다. 동일한 24 시간 동안의 이후 읽기는 마지막 액세스 시간을 업데이트 하지 않습니다. 읽기 간에 blob이 수정 되는 경우 마지막 액세스 시간은 두 값 중 더 최신입니다.

다음 예에서는 30 일 동안 액세스 하지 않은 경우 blob을 쿨 저장소로 이동 합니다. `enableAutoTierToHotFromCool`속성은 blob을 쿨로 계층화 한 후 다시 액세스할 경우 다시 쿨에서 핫으로 계층화 해야 하는지 여부를 나타내는 부울 값입니다.

```json
{
  "enabled": true,
  "name": "last-accessed-thirty-days-ago",
  "type": "Lifecycle",
  "definition": {
    "actions": {
      "baseBlob": {
        "enableAutoTierToHotFromCool": true,
        "tierToCool": {
          "daysAfterLastAccessTimeGreaterThan": 30
        }
      }
    },
    "filters": {
      "blobTypes": [
        "blockBlob"
      ],
      "prefixMatch": [
        "mylifecyclecontainer/log"
      ]
    }
  }
}
```

#### <a name="storage-account-support"></a>스토리지 계정 지원

다음 유형의 저장소 계정에 대 한 마지막 액세스 시간 추적을 사용할 수 있습니다.

 - 범용 v2 저장소 계정
 - Blob storage 계정 차단
 - Blob Storage 계정

저장소 계정이 범용 v1 계정인 경우 Azure Portal를 사용 하 여 범용 v2 계정으로 업그레이드 합니다.

Azure Data Lake Storage Gen2에서 사용 하도록 설정 된 계층적 네임 스페이스를 사용 하는 저장소 계정은 아직 지원 되지 않습니다.

#### <a name="pricing-and-billing"></a>가격 책정 및 대금 청구

각 마지막 액세스 시간 업데이트는 [다른 작업](https://azure.microsoft.com/pricing/details/storage/blobs/)으로 간주 됩니다.

### <a name="archive-data-after-ingest"></a>수집 후 데이터 보관

일부 데이터는 클라우드에 유휴 상태로 유지되며 드물지만 한 번 액세스됩니다. 다음 수명 주기 정책은 수집 되는 즉시 데이터를 보관 하도록 구성 됩니다. 이 예제에서는 컨테이너 내의 저장소 계정에서 블록 blob을 `archivecontainer` 보관 계층으로 전환 합니다. 전환은 마지막으로 수정한 시간 이후에 0 일 후에 blob에서 작동 하 여 수행 됩니다.

> [!NOTE] 
> Blob을 직접 업로드 하는 것이 더 효율적입니다. [Putblob](https://docs.microsoft.com/rest/api/storageservices/put-blob) 에 대 한 x-y Blob 또는 [PutBlockList](https://docs.microsoft.com/rest/api/storageservices/put-block-list) REST 버전 2018-11-09 이상 또는 최신 blob storage 클라이언트 라이브러리와 함께 사용할 수 있습니다. 

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

일부 데이터는 만든 후 며칠 또는 몇 개월 후에 만료 될 것으로 예상 됩니다. 데이터 보존 기간에 따라 삭제하여 데이터를 만료하도록 수명 주기 관리 정책을 구성할 수 있습니다. 다음 예제에서는 365 일 보다 오래 된 모든 블록 blob를 삭제 하는 정책을 보여 줍니다.

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

### <a name="delete-data-with-blob-index-tags"></a>Blob 인덱스 태그를 사용 하 여 데이터 삭제
일부 데이터는 명시적으로 삭제 하도록 표시 된 경우에만 만료 되어야 합니다. Blob 인덱스 키/값 특성으로 태그가 지정 된 데이터를 만료 하도록 수명 주기 관리 정책을 구성할 수 있습니다. 다음 예제에서는로 태그가 지정 된 모든 블록 blob를 삭제 하는 정책을 보여 줍니다 `Project = Contoso` . Blob 인덱스에 대해 자세히 알아보려면 [Blob 인덱스(미리 보기)를 사용하여 Azure Blob 스토리지에서 데이터 관리 및 찾기](storage-manage-find-blobs.md)를 참조하세요.

```json
{
    "rules": [
        {
            "enabled": true,
            "name": "DeleteContosoData",
            "type": "Lifecycle",
            "definition": {
                "actions": {
                    "baseBlob": {
                        "delete": {
                            "daysAfterModificationGreaterThan": 0
                        }
                    }
                },
                "filters": {
                    "blobIndexMatch": [
                        {
                            "name": "Project",
                            "op": "==",
                            "value": "Contoso"
                        }
                    ],
                    "blobTypes": [
                        "blockBlob"
                    ]
                }
            }
        }
    ]
}
```

### <a name="manage-versions"></a>버전 관리

수명이 지속 되는 동안 정기적으로 수정 되 고 액세스 되는 데이터의 경우 blob 저장소 버전 관리를 사용 하도록 설정 하 여 이전 버전의 개체를 자동으로 유지 관리할 수 있습니다. 정책을 만들거나 이전 버전을 삭제할 수 있습니다. 버전 기간은 버전 생성 시간을 평가 하 여 결정 됩니다. 이 정책 규칙은 버전을 쿨 계층으로 만든 후 90 일 또는 그 이전 버전의 컨테이너 내에서 이전 버전을 계층 `activedata` 으로 만들고 365 일 이전 버전을 삭제 합니다.

```json
{
  "rules": [
    {
      "enabled": true,
      "name": "versionrule",
      "type": "Lifecycle",
      "definition": {
        "actions": {
          "version": {
            "tierToCool": {
              "daysAfterCreationGreaterThan": 90
            },
            "delete": {
              "daysAfterCreationGreaterThan": 365
            }
          }
        },
        "filters": {
          "blobTypes": [
            "blockBlob"
          ],
          "prefixMatch": [
            "activedata"
          ]
        }
      }
    }
  ]
}
```

## <a name="faq"></a>FAQ

**새 정책을 만들었으며 작업이 즉시 실행 되지 않는 이유는 무엇 인가요?**

플랫폼은 하루에 한 번 수명 주기 정책을 실행합니다. 정책을 구성한 후에는 일부 작업을 처음 실행 하는 데 최대 24 시간이 걸릴 수 있습니다.

**기존 정책을 업데이트 하는 경우 작업이 실행 되는 데 얼마나 걸립니까?**

업데이트 된 정책은 적용 되는 데 최대 24 시간이 걸립니다. 정책이 적용 되 면 작업이 실행 되는 데 최대 24 시간이 걸릴 수 있습니다. 따라서 정책 작업을 완료 하는 데 최대 48 시간이 걸릴 수 있습니다.

**보관 된 blob을 수동으로 전환 하 여 보관 계층으로 다시 이동 하는 것을 방지 하려면 어떻게 해야 하나요?**

Blob을 한 액세스 계층에서 다른 액세스 계층으로 이동 하면 마지막 수정 시간이 변경 되지 않습니다. 보관 된 blob을 핫 계층으로 수동으로 리하이드레이션 수명 주기 관리 엔진에서 보관 계층으로 다시 이동 합니다. 이 blob에 일시적으로 영향을 주는 규칙을 사용 하지 않도록 설정 하 여 다시 보관 되지 않도록 합니다. Blob을 다시 보관 계층으로 안전 하 게 이동할 수 있는 경우 규칙을 다시 사용 하도록 설정 합니다. 핫 또는 쿨 계층을 영구적으로 유지 해야 하는 경우 blob을 다른 위치로 복사할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

실수로 삭제된 데이터를 복구하는 방법을 알아봅니다.

- [Azure Storage Blob에 대한 일시 삭제](../blobs/storage-blob-soft-delete.md)

Blob 인덱스를 사용 하 여 데이터를 관리 하 고 찾는 방법에 대해 알아봅니다.

- [Blob 인덱스를 사용 하 여 Azure Blob Storage에서 데이터 관리 및 찾기](storage-manage-find-blobs.md)
