---
title: 개체 복제 구성
titleSuffix: Azure Storage
description: 한 스토리지 계정의 컨테이너에 있는 블록 blob을 다른 스토리지 계정과 비동기식으로 복사하도록 개체 복제를 구성하는 방법을 알아봅니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/09/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: e3503a9eef5c11db35684ca61fb1ee39525a465d
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94427601"
---
# <a name="configure-object-replication-for-block-blobs"></a>블록 blob에 대 한 개체 복제 구성

개체 복제는 원본 저장소 계정과 대상 계정 사이에서 블록 blob을 비동기적으로 복사 합니다. 개체 복제에 대 한 자세한 내용은 [개체 복제](object-replication-overview.md)를 참조 하세요.

개체 복제를 구성하는 경우 원본 스토리지 계정 및 대상 계정을 지정하는 복제 정책을 만듭니다. 복제 정책에는 원본 컨테이너와 대상 컨테이너를 지정하고 원본 컨테이너에서 복제할 블록 Blob을 나타내는 하나 이상의 규칙이 포함되어 있습니다.

이 문서에서는 Azure Portal, PowerShell 또는 Azure CLI를 사용하여 스토리지 계정에 대한 개체 복제를 구성하는 방법을 설명합니다. Azure Storage 리소스 공급자 클라이언트 라이브러리 중 하나를 사용하여 개체 복제를 구성할 수도 있습니다.

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="create-a-replication-policy-and-rules"></a>복제 정책 및 규칙 만들기

개체 복제를 구성하기 전에 원본 및 대상 스토리지 계정이 아직 없는 경우 만듭니다. 두 계정 모두 범용 v2 스토리지 계정이어야 합니다. 자세한 내용은 [Azure Storage 계정 만들기](../common/storage-account-create.md)를 참조하세요.

개체를 복제 하려면 원본 및 대상 계정 모두에 대해 blob 버전 관리를 사용 하도록 설정 하 고 원본 계정에 대해 blob 변경 피드가 사용 하도록 설정 되어 있어야 합니다. Blob 버전 관리에 대해 자세히 알아보려면 [blob 버전 관리](versioning-overview.md)를 참조 하세요. 변경 피드에 대 한 자세한 내용은 [Azure Blob Storage에서 변경 피드 지원](storage-blob-change-feed.md)을 참조 하세요. 이러한 기능을 사용 하도록 설정 하면 추가 비용이 발생할 수 있습니다.

스토리지 계정은 최대 두 개의 대상 계정에 대한 원본 계정으로 사용할 수 있습니다. 원본 및 대상 계정이 동일한 지역 또는 다른 지역에 있을 수 있습니다. 또한 서로 다른 구독 및 다른 Azure Active Directory (Azure AD) 테 넌 트에 상주할 수 있습니다. 각 계정 쌍에 대해 하나의 복제 정책만 만들 수 있습니다.

개체 복제를 구성 하는 경우 Azure Storage 리소스 공급자를 통해 대상 계정에 대 한 복제 정책을 만듭니다. 복제 정책을 만든 후에는 Azure Storage에 정책 ID를 할당 합니다. 그런 다음 정책 ID를 사용 하 여 해당 복제 정책을 원본 계정과 연결 해야 합니다. 복제를 수행 하려면 원본 및 대상 계정의 정책 ID가 동일 해야 합니다.

저장소 계정에 대 한 개체 복제 정책을 구성 하려면 저장소 계정 수준으로 범위가 지정 된 Azure Resource Manager **참여자** 역할을 할당 받아야 합니다. 자세한 내용은 azure 역할 기반 access control (Azure RBAC) 설명서의 [azure 기본 제공 역할](../../role-based-access-control/built-in-roles.md) 을 참조 하세요.

### <a name="configure-object-replication-when-you-have-access-to-both-storage-accounts"></a>두 저장소 계정 모두에 액세스할 수 있는 경우 개체 복제 구성

원본 및 대상 저장소 계정에 대 한 액세스 권한이 있는 경우 두 계정 모두에서 개체 복제 정책을 구성할 수 있습니다.

Azure Portal에서 개체 복제를 구성하기 전에 해당 스토리지 계정에 원본 및 대상 컨테이너가 아직 없는 경우 새로 만듭니다. 또한 원본 계정에서 blob 버전 관리 및 변경 피드를 사용 하도록 설정 하 고 대상 계정에서 blob 버전 관리를 사용 하도록 설정 합니다.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure Portal는 대상 계정에 대해 구성한 후 원본 계정에 정책을 자동으로 만듭니다.

Azure Portal에서 복제 정책을 만들려면 다음 단계를 수행합니다.

1. Azure Portal의 원본 스토리지 계정으로 이동합니다.
1. **Blob service** 에서 **개체 복제** 를 선택 합니다.
1. **복제 규칙 설정** 을 선택 합니다.
1. 대상 구독 및 스토리지 계정을 선택합니다.
1. **컨테이너 쌍** 섹션에서 원본 계정에서 원본 컨테이너를 선택하고 대상 계정에서 대상 컨테이너를 선택합니다. 복제 정책에 따라 최대 10개의 컨테이너 쌍을 만들 수 있습니다.

    다음 이미지는 일련의 복제 규칙을 보여 줍니다.

    :::image type="content" source="media/object-replication-configure/configure-replication-policy.png" alt-text="Azure Portal의 복제 규칙을 보여 주는 스크린샷":::

1. 원할 경우 접두사 패턴과 일치하는 blob만 복사하도록 하나 이상의 필터를 지정합니다. 예를 들어, `b` 접두사를 지정하면 해당 문자로 시작하는 이름의 blob만 복제됩니다. 가상 디렉터리를 접두사의 일부로 지정할 수 있습니다. 접두사 문자열은 와일드 카드 문자를 지원 하지 않습니다.

    다음 이미지는 복제 규칙의 일부로 복사할 blob을 제한하는 필터를 보여 줍니다.

    :::image type="content" source="media/object-replication-configure/configure-replication-copy-prefix.png" alt-text="복제 규칙에 대한 필터를 보여 주는 스크린샷":::

1. 기본적으로 복사 범위는 새 개체만 복사하도록 설정됩니다. 컨테이너의 모든 개체를 복사하거나 사용자 지정 날짜 및 시간에 시작된 개체를 복사하려면 **변경** 링크를 선택하고 컨테이너 쌍의 복사 범위를 구성합니다.

    다음 이미지는 지정 된 날짜와 시간 이후 개체를 복사 하는 사용자 지정 복사 범위를 보여 줍니다.

    :::image type="content" source="media/object-replication-configure/configure-replication-copy-scope.png" alt-text="개체 복제에 대한 사용자 지정 복사 범위를 보여 주는 스크린샷":::

1. **저장 및 적용** 을 선택하여 복제 정책을 만들고 데이터 복제를 시작합니다.

개체 복제를 구성한 후에는 다음 그림에 표시 된 것 처럼 Azure Portal에 복제 정책 및 규칙이 표시 됩니다.

:::image type="content" source="media/object-replication-configure/object-replication-policies-portal.png" alt-text="Azure Portal의 개체 복제 정책을 보여 주는 스크린샷":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell을 사용 하 여 복제 정책을 만들려면 먼저 Az. Storage PowerShell 모듈의 버전 [2.5.0](https://www.powershellgallery.com/packages/Az.Storage/2.5.0) 이상을 설치 합니다. Azure PowerShell을 설치하는 방법에 대한 자세한 내용은 [PowerShellGet을 사용하여 Azure PowerShell 설치](/powershell/azure/install-az-ps)를 참조하세요.

다음 예제에서는 원본 및 대상 계정에 대한 복제 정책을 만드는 방법을 보여 줍니다. 꺾쇠 괄호로 묶인 값을 사용자 고유의 값으로 바꿔야 합니다.

```powershell
# Sign in to your Azure account.
Connect-AzAccount

# Set variables.
$rgName = "<resource-group>"
$srcAccountName = "<source-storage-account>"
$destAccountName = "<destination-storage-account>"
$srcContainerName1 = "source-container1"
$destContainerName1 = "dest-container1"
$srcContainerName2 = "source-container2"
$destContainerName2 = "dest-container2"

# Enable blob versioning and change feed on the source account.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -EnableChangeFeed $true `
    -IsVersioningEnabled $true

# Enable blob versioning on the destination account.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName `
    -IsVersioningEnabled $true

# List the service properties for both accounts.
Get-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName
Get-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName

# Create containers in the source and destination accounts.
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $srcAccountName |
    New-AzStorageContainer $srcContainerName1
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $destAccountName |
    New-AzStorageContainer $destContainerName1
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $srcAccountName |
    New-AzStorageContainer $srcContainerName2
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $destAccountName |
    New-AzStorageContainer $destContainerName2

# Define replication rules for each container.
$rule1 = New-AzStorageObjectReplicationPolicyRule -SourceContainer $srcContainerName1 `
    -DestinationContainer $destContainerName1 `
    -PrefixMatch b
$rule2 = New-AzStorageObjectReplicationPolicyRule -SourceContainer $srcContainerName2 `
    -DestinationContainer $destContainerName2  `
    -MinCreationTime 2020-05-10T00:00:00Z

# Create the replication policy on the destination account.
$destPolicy = Set-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName `
    -PolicyId default `
    -SourceAccount $srcAccountName `
    -Rule $rule1,$rule2

# Create the same policy on the source account.
Set-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -InputObject $destPolicy
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용 하 여 복제 정책을 만들려면 먼저 Azure CLI 버전 2.11.1 이상을 설치 합니다. 자세한 내용은 [Azure CLI 시작](/cli/azure/get-started-with-azure-cli)을 참조 하세요.

그런 다음, 원본 및 대상 저장소 계정에서 blob 버전 관리를 사용 하도록 설정 하 고 [az storage account blob-service-properties update](/cli/azure/storage/account/blob-service-properties#az_storage_account_blob_service_properties_update) 명령을 호출 하 여 원본 계정에서 변경 피드를 사용 하도록 설정 합니다. 꺾쇠 괄호로 묶인 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurecli
az login

az storage account blob-service-properties update \
    --resource-group <resource-group> \
    --account-name <source-storage-account> \
    --enable-versioning

az storage account blob-service-properties update \
    --resource-group <resource-group> \
    --account-name <source-storage-account> \
    --enable-change-feed

az storage account blob-service-properties update \
    --resource-group <resource-group> \
    --account-name <dest-storage-account> \
    --enable-versioning
```

각 스토리지 계정에 원본 및 대상 컨테이너를 만듭니다.

```azurecli
az storage container create \
    --account-name <source-storage-account> \
    --name source-container-1 \
    --auth-mode login
az storage container create \
    --account-name <source-storage-account> \
    --name source-container-2 \
    --auth-mode login

az storage container create \
    --account-name <dest-storage-account> \
    --name dest-container-1 \
    --auth-mode login
az storage container create \
    --account-name <dest-storage-account> \
    --name dest-container-1 \
    --auth-mode login
```

[Az storage account 또는-policy create](/cli/azure/storage/account/or-policy#az_storage_account_or_policy_create)를 호출 하 여 대상 계정에 새 복제 정책 및 연결 된 규칙을 만듭니다.

```azurecli
az storage account or-policy create \
    --account-name <dest-storage-account> \
    --resource-group <resource-group> \
    --source-account <source-storage-account> \
    --destination-account <dest-storage-account> \
    --source-container source-container-1 \
    --destination-container dest-container-1 \
    --min-creation-time '2020-09-10T00:00:00Z' \
    --prefix-match a

```

Azure Storage 새 정책이 생성 될 때 해당 정책에 대 한 정책 ID를 설정 합니다. 정책에 다른 규칙을 추가 하려면 [az storage account 또는-policy rule add](/cli/azure/storage/account/or-policy/rule#az_storage_account_or_policy_rule_add) 를 호출 하 고 정책 ID를 제공 합니다.

```azurecli
az storage account or-policy rule add \
    --account-name <dest-storage-account> \
    --resource-group <resource-group> \
    --source-container source-container-2 \
    --destination-container dest-container-2 \
    --policy-id <policy-id> \
    --prefix-match b
```

그런 다음 정책 ID를 사용 하 여 원본 계정에 정책을 만듭니다.

```azurecli
az storage account or-policy show \
    --resource-group <resource-group> \
    --account-name <dest-storage-account> \
    --policy-id <policy-id> |
    az storage account or-policy create --resource-group <resource-group> \
    --account-name <source-storage-account> \
    --policy "@-"
```

---

### <a name="configure-object-replication-when-you-have-access-only-to-the-destination-account"></a>대상 계정에만 액세스할 수 있는 경우 개체 복제 구성

원본 저장소 계정에 대 한 권한이 없는 경우 대상 계정에 대 한 개체 복제를 구성 하 고 다른 사용자에 게 정책 정의가 포함 된 JSON 파일을 제공 하 여 원본 계정에 동일한 정책을 만들 수 있습니다. 예를 들어 원본 계정이 대상 계정과 다른 Azure AD 테 넌 트에 있는 경우이 방법을 사용 하 여 개체 복제를 구성할 수 있습니다.

정책을 만들려면 대상 저장소 계정 이상의 수준으로 범위가 지정 된 Azure Resource Manager **참가자** 역할을 할당 해야 합니다. 자세한 내용은 azure 역할 기반 access control (Azure RBAC) 설명서의 [azure 기본 제공 역할](../../role-based-access-control/built-in-roles.md) 을 참조 하세요.

다음 표에는 각 시나리오에서 JSON 파일의 정책 ID 및 규칙 Id에 사용할 값이 요약 되어 있습니다.

| 이 계정에 대 한 JSON 파일을 만드는 중 ... | 정책 ID 및 규칙 Id를이 값으로 설정 ... |
|-|-|
| 대상 계정 | 문자열 값의 *기본값* 입니다. Azure Storage에서 정책 ID 및 규칙 Id를 만듭니다. |
| 원본 계정 | 대상 계정에 정의 된 정책을 JSON 파일로 다운로드 하는 경우 반환 되는 정책 ID 및 규칙 Id의 값입니다. |

다음 예에서는 접두사 *b* 와 일치 하는 단일 규칙을 사용 하 여 대상 계정에 대 한 복제 정책을 정의 하 고 복제할 blob에 대 한 최소 생성 시간을 설정 합니다. 꺾쇠 괄호로 묶인 값을 사용자 고유의 값으로 바꿔야 합니다.

```json
{
  "properties": {
    "policyId": "default",
    "sourceAccount": "<source-account>",
    "destinationAccount": "<dest-account>",
    "rules": [
      {
        "ruleId": "default",
        "sourceContainer": "<source-container>",
        "destinationContainer": "<destination-container>",
        "filters": {
          "prefixMatch": [
            "b"
          ],
          "minCreationTime": "2020-08-028T00:00:00Z"
        }
      }
    ]
  }
}
```

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure Portal의 JSON 파일을 사용 하 여 대상 계정에서 개체 복제를 구성 하려면 다음 단계를 수행 합니다.

1. 대상 계정에 대 한 복제 정책을 정의 하는 로컬 JSON 파일을 만듭니다. Azure Storage에서 정책 ID를 정의 하도록 **Policyid** 필드를 **default** 로 설정 합니다.

    복제 정책을 정의 하는 JSON 파일을 만드는 쉬운 방법은 먼저 Azure Portal의 두 저장소 계정 간에 테스트 복제 정책을 만드는 것입니다. 그런 다음 필요에 따라 복제 규칙을 다운로드 하 고 JSON 파일을 수정할 수 있습니다.

1. Azure Portal에서 대상 계정에 대 한 **개체 복제** 설정으로 이동 합니다.
1. **복제 규칙 업로드** 를 선택 합니다.
1. JSON 파일을 업로드 합니다. Azure Portal는 다음 이미지와 같이 생성 될 정책 및 규칙을 표시 합니다.

    :::image type="content" source="media/object-replication-configure/replication-rules-upload-portal.png" alt-text="JSON 파일을 업로드 하 여 복제 정책을 정의 하는 방법을 보여 주는 스크린샷":::

1. **업로드** 를 선택 하 여 대상 계정에 대 한 복제 정책을 만듭니다.

그런 다음 다른 사용자에 게 제공할 수 있는 정책 정의가 포함 된 JSON 파일을 다운로드 하 여 원본 계정을 구성할 수 있습니다. 이 JSON 파일을 다운로드 하려면 다음 단계를 수행 합니다.

1. Azure Portal에서 대상 계정에 대 한 **개체 복제** 설정으로 이동 합니다.
1. 다음 그림에 표시 된 것 처럼 다운로드 하려는 정책 옆에 있는 **자세히** 단추를 선택 하 고 **규칙 다운로드** 를 선택 합니다.

    :::image type="content" source="media/object-replication-configure/replication-rules-download-portal.png" alt-text="JSON 파일에 복제 규칙을 다운로드 하는 방법을 보여 주는 스크린샷":::

1. 로컬 컴퓨터에 JSON 파일을 저장 하 여 다른 사용자와 공유 하 여 원본 계정에 정책을 구성 합니다.

다운로드 한 JSON 파일에는 대상 계정의 정책에 대해 만들어진 Azure Storage 정책 ID가 포함 되어 있습니다. 원본 계정에서 개체 복제를 구성 하려면 동일한 정책 ID를 사용 해야 합니다.

Azure Portal를 통해 대상 계정에 대 한 복제 정책을 만들기 위해 JSON 파일을 업로드 하면 원본 계정에 동일한 정책이 자동으로 생성 되지 않습니다. Azure Storage 개체 복제를 시작 하기 전에 다른 사용자가 원본 계정에 정책을 만들어야 합니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell에서 대상 계정에 대 한 복제 정책 정의를 포함 하는 JSON 파일을 다운로드 하려면 [AzStorageObjectReplicationPolicy](/powershell/module/az.storage/get-azstorageobjectreplicationpolicy) 명령을 호출 하 여 정책을 반환 합니다. 그런 다음 정책을 JSON으로 변환 하 고 다음 예제와 같이 로컬 파일로 저장 합니다. 꺾쇠 괄호의 값과 파일 경로를 고유한 값으로 바꿔야 합니다.

```powershell
$rgName = "<resource-group>"
$destAccountName = "<destination-storage-account>"

$destPolicy = Get-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName
$destPolicy | ConvertTo-Json -Depth 5 > c:\temp\json.txt
```

JSON 파일을 사용 하 여 PowerShell을 사용 하 여 원본 계정에 대 한 복제 정책을 구성 하려면 로컬 파일을 검색 하 고 JSON에서 개체로 변환 합니다. 그런 다음 [AzStorageObjectReplicationPolicy](/powershell/module/az.storage/set-azstorageobjectreplicationpolicy) 명령을 호출 하 여 다음 예제와 같이 원본 계정에 정책을 구성 합니다. 꺾쇠 괄호의 값과 파일 경로를 고유한 값으로 바꿔야 합니다.

```powershell
$object = Get-Content -Path C:\temp\json.txt | ConvertFrom-Json
Set-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -PolicyId $object.PolicyId `
    -SourceAccount $object.SourceAccount `
    -DestinationAccount $object.DestinationAccount `
    -Rule $object.Rules
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

대상 계정에 대 한 복제 정책 정의를 Azure CLI의 JSON 파일에 쓰려면 [az storage account 또는-policy show](/cli/azure/storage/account/or-policy#az_storage_account_or_policy_show) 명령을 호출 하 고 파일에 출력 합니다.

다음 예제에서는 *policy.js에 있는* JSON 파일에 정책 정의를 씁니다. 꺾쇠 괄호의 값과 파일 경로를 고유한 값으로 바꿔야 합니다.

```azurecli
az storage account or-policy show \
    --account-name <dest-account-name> \
    --policy-id  <policy-id> > policy.json
```

JSON 파일을 사용 하 여 Azure CLI 사용 하 여 원본 계정에 대 한 복제 정책을 구성 하려면 [az storage account 또는-policy create](/cli/azure/storage/account/or-policy#az_storage_account_or_policy_create) 명령을 호출 하 고 file *에서policy.js* 을 참조 합니다. 꺾쇠 괄호의 값과 파일 경로를 고유한 값으로 바꿔야 합니다.

```azurecli
az storage account or-policy create \
    -resource-group <resource-group> \
    --source-account <source-account-name> \
    --policy @policy.json
```

---

## <a name="check-the-replication-status-of-a-blob"></a>Blob의 복제 상태를 확인 합니다.

Azure Portal, PowerShell 또는 Azure CLI를 사용 하 여 원본 계정에서 blob에 대 한 복제 상태를 확인할 수 있습니다. 복제가 완료 되거나 실패할 때까지 개체 복제 속성이 채워지지 않습니다.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure Portal의 원본 계정에서 blob에 대 한 복제 상태를 확인 하려면 다음 단계를 수행 합니다.

1. Azure Portal에서 소스 계정으로 이동 합니다.
1. 원본 blob을 포함 하는 컨테이너를 찾습니다.
1. Blob을 선택 하 여 속성을 표시 합니다. Blob이 성공적으로 복제 되 면 **개체 복제** 섹션에서 상태가 *완료* 로 설정 된 것을 볼 수 있습니다. 이 컨테이너에 대 한 개체 복제를 관리 하는 규칙의 복제 정책 ID 및 ID도 나열 됩니다.

:::image type="content" source="media/object-replication-configure/check-replication-status-source.png" alt-text="원본 계정의 blob에 대 한 복제 상태를 보여 주는 스크린샷":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell을 사용 하 여 원본 계정의 blob에 대 한 복제 상태를 확인 하려면 다음 예제와 같이 object replication **connectingtosource** 속성의 값을 가져옵니다. 꺾쇠 괄호로 묶인 값을 사용자 고유의 값으로 바꿔야 합니다.

```powershell
$ctxSrc = (Get-AzStorageAccount -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName).Context
$blobSrc = Get-AzStorageBlob -Container $srcContainerName1 `
    -Context $ctxSrc `
    -Blob <blob-name>
$blobSrc.BlobProperties.ObjectReplicationSourceProperties[0].Rules[0].ReplicationStatus
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용 하 여 원본 계정의 blob에 대 한 복제 상태를 확인 하려면 다음 예제와 같이 개체 복제 **상태** 속성의 값을 가져옵니다.

```azurecli
az storage blob show \
    --account-name <source-account-name> \
    --container-name <source-container-name> \
    --name <source-blob-name> \
    --query 'objectReplicationSourceProperties[].rules[].status' \
    --output tsv \
    --auth-mode login
```

---

원본 계정의 blob에 대 한 복제 상태가 실패를 나타내는 경우 다음과 같은 가능한 원인을 조사 하십시오.

- 대상 계정에 개체 복제 정책이 구성 되어 있는지 확인 합니다.
- 대상 컨테이너가 여전히 존재 하는지 확인 합니다.
- 원본 blob이 쓰기 작업의 일부로 고객이 제공한 키로 암호화 된 경우에는 개체 복제가 실패 합니다. 고객 제공 키에 대 한 자세한 내용은 [Blob 저장소에 대 한 요청에 암호화 키 제공](encryption-customer-provided-keys.md)을 참조 하세요.

## <a name="remove-a-replication-policy"></a>복제 정책 제거

복제 정책 및 관련 규칙을 제거하려면 Azure Portal, PowerShell 또는 CLI를 사용합니다.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure Portal에서 복제 정책을 제거하려면 다음 단계를 수행합니다.

1. Azure Portal의 원본 스토리지 계정으로 이동합니다.
1. **설정** 에서 **개체 복제** 를 선택합니다.
1. 정책 이름 옆에 있는 **더 보기** 단추를 클릭합니다.
1. **규칙 삭제** 를 선택합니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

복제 정책을 제거하려면 원본 계정 및 대상 계정 둘 다에서 정책을 삭제합니다. 또한 정책을 삭제하면 연결된 규칙도 모두 삭제됩니다.

```powershell
# Remove the policy from the destination account.
Remove-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName `
    -PolicyId $destPolicy.PolicyId

# Remove the policy from the source account.
Remove-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -PolicyId $destPolicy.PolicyId
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

복제 정책을 제거하려면 원본 계정 및 대상 계정 둘 다에서 정책을 삭제합니다. 또한 정책을 삭제하면 연결된 규칙도 모두 삭제됩니다.

```azurecli
az storage account or-policy delete \
    --policy-id <policy-id> \
    --account-name <source-storage-account> \
    --resource-group <resource-group>

az storage account or-policy delete \
    --policy-id <policy-id> \
    --account-name <dest-storage-account> \
    --resource-group <resource-group>
```

---

## <a name="next-steps"></a>다음 단계

- [개체 복제 개요](object-replication-overview.md)
- [Blob 버전 관리 설정 및 관리](versioning-enable.md)
- [Azure Blob storage에서 변경 피드 처리](storage-blob-change-feed-how-to.md)
