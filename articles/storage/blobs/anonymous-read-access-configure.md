---
title: 컨테이너 및 blob에 대 한 익명 공용 읽기 액세스 구성
titleSuffix: Azure Storage
description: 저장소 계정의 blob 데이터에 대 한 익명 액세스를 사용 하거나 사용 하지 않도록 설정 하는 방법에 대해 알아봅니다. 컨테이너 및 blob을 익명 액세스에 사용할 수 있도록 컨테이너 공용 액세스 설정을 설정 합니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 06/29/2020
ms.author: tamram
ms.reviewer: fryu
ms.openlocfilehash: af589874021baaf04a423b7bbaa0e36528eda93c
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86209653"
---
# <a name="configure-anonymous-public-read-access-for-containers-and-blobs"></a>컨테이너 및 blob에 대 한 익명 공용 읽기 액세스 구성

Azure Storage는 컨테이너 및 blob에 대 한 익명 공용 읽기 액세스를 지원 합니다. 기본적으로 컨테이너 및 해당 blob에 대 한 모든 요청은 Azure Active Directory (Azure AD) 또는 공유 키 권한 부여를 사용 하 여 권한을 부여 받아야 합니다. 익명 액세스를 허용 하도록 컨테이너의 공용 액세스 수준 설정을 구성 하는 경우 클라이언트는 요청에 대 한 권한을 부여 하지 않고 해당 컨테이너의 데이터를 읽을 수 있습니다.

> [!WARNING]
> 컨테이너가 공용 액세스용으로 구성 된 경우 모든 클라이언트는 해당 컨테이너의 데이터를 읽을 수 있습니다. 공용 액세스를 사용 하면 보안 위험이 발생할 수 있으므로 시나리오에 필요 하지 않은 경우에는 저장소 계정에 대해 사용 하지 않도록 설정 하는 것이 좋습니다. 자세한 내용은 [컨테이너 및 blob에 대 한 익명 공용 읽기 액세스 방지](anonymous-read-access-prevent.md)를 참조 하세요.

컨테이너에 대 한 공용 액세스를 구성 하려면 다음 두 단계를 수행 해야 합니다.

1. 저장소 계정에 대 한 공용 액세스 사용
1. 컨테이너의 공용 액세스 설정 구성

이 문서에서는 컨테이너 및 해당 blob에 대 한 익명 공용 읽기 액세스를 구성 하는 방법을 설명 합니다. 클라이언트 응용 프로그램에서 익명으로 blob 데이터에 액세스 하는 방법에 대 한 자세한 내용은 [.net을 사용 하 여 공용 컨테이너 및 blob에 익명 액세스](anonymous-read-access-client.md)를 참조 하세요.

## <a name="enable-or-disable-public-read-access-for-a-storage-account"></a>저장소 계정에 대 한 공용 읽기 액세스 사용 또는 사용 안 함

기본적으로 공용 액세스는 저장소 계정에 대해 사용 하도록 설정 됩니다. 공용 액세스를 사용 하지 않도록 설정 하면 해당 계정의 컨테이너와 blob에 대 한 모든 익명 액세스를 방지 합니다. 보안 향상을 위해 사용자에 게 blob 리소스에 대 한 익명 액세스 권한을 요구 하지 않는 한 저장소 계정에 대 한 공용 액세스를 사용 하지 않도록 설정 하는 것이 좋습니다.

> [!WARNING]
> 저장소 계정에 대 한 공용 액세스를 사용 하지 않도록 설정 하면 해당 저장소 계정의 모든 컨테이너에 대 한 공용 액세스 설정이 재정의 됩니다. 저장소 계정에 대 한 공용 액세스를 사용 하지 않도록 설정 하면 이후 해당 계정에 대 한 모든 익명 요청이 실패 합니다.

저장소 계정에 대 한 공용 액세스를 사용 하거나 사용 하지 않도록 설정 하려면 Azure Portal 또는 Azure CLI를 사용 하 여 계정의 **Blobpublicaccess** 속성을 구성 합니다. 이 속성은 Azure Resource Manager 배포 모델을 사용 하 여 만든 모든 저장소 계정에 사용할 수 있습니다. 자세한 내용은 [저장소 계정 개요](../common/storage-account-overview.md)를 참조 하세요.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure Portal에서 저장소 계정에 대 한 공용 액세스를 사용 하거나 사용 하지 않도록 설정 하려면 다음 단계를 수행 합니다.

1. Azure Portal의 스토리지 계정으로 이동합니다.
1. **설정**에서 **구성** 설정을 찾습니다.
1. **Blob 공용 액세스** 를 **사용 안 함** 또는 **사용**으로 설정 합니다.

    :::image type="content" source="media/anonymous-read-access-configure/blob-public-access-portal.png" alt-text="계정에 대 한 blob 공용 액세스를 사용 하거나 사용 하지 않도록 설정 하는 방법을 보여 주는 스크린샷":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용 하 여 저장소 계정에 대 한 공용 액세스를 사용 하거나 사용 하지 않도록 설정 하려면 먼저 [az resource show](/cli/azure/resource#az-resource-show) 명령을 호출 하 여 저장소 계정에 대 한 리소스 ID를 가져옵니다. 다음으로 [az resource update](/cli/azure/resource#az-resource-update) 명령을 호출 하 여 저장소 계정에 대 한 **Allowblobpublicaccess** 속성을 설정 합니다. 공용 액세스를 사용 하도록 설정 하려면 **Allowblobpublicaccess** 속성을 true로 설정 합니다. 비활성화 하려면 **false**로 설정 합니다.

다음 예제에서는 저장소 계정에 대 한 공용 blob 액세스를 사용 하지 않도록 설정 합니다. 대괄호 안의 자리 표시자 값을 고유한 값으로 바꾸어야 합니다.

```azurecli-interactive
storage_account_id=$(az resource show \
    --name anonpublicaccess \
    --resource-group storagesamples-rg \
    --resource-type Microsoft.Storage/storageAccounts \
    --query id \
    --output tsv)

az resource update \
    --ids $storage_account_id \
    --set properties.allowBlobPublicAccess=false
    ```
```

Azure CLI에서 공용 액세스를 사용 하도록 설정할지 여부를 확인 하려면 [az resource show](/cli/azure/resource#az-resource-show) 명령을 호출 하 고 **Allowblobpublicaccess** 속성을 쿼리 합니다.

```azurecli-interactive
az resource show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --resource-type Microsoft.Storage/storageAccounts \
    --query properties.allowBlobPublicAccess \
    --output tsv
```

---

> [!NOTE]
> 저장소 계정에 대 한 공용 액세스를 사용 하지 않도록 설정 해도 해당 저장소 계정에서 호스팅되는 정적 웹 사이트에는 영향을 주지 않습니다. **$Web** 컨테이너는 항상 공개적으로 액세스할 수 있습니다.

## <a name="check-the-public-access-setting-for-a-storage-account"></a>저장소 계정에 대 한 공용 액세스 설정 확인

저장소 계정에 대 한 공용 액세스 설정을 확인 하려면 **Allowblobpublicaccess** 속성의 값을 가져옵니다. 한 번에 많은 수의 저장소 계정에 대 한이 속성을 확인 하려면 Azure 리소스 그래프 탐색기를 사용 합니다.

**Allowblobpublicaccess** 속성은 기본적으로 설정 되지 않으며 명시적으로 설정할 때까지 값을 반환 하지 않습니다. 저장소 계정은 기본적으로 속성 값이 null 일 때 공용 액세스를 허용 합니다.

### <a name="check-the-public-access-setting-for-a-single-storage-account"></a>단일 저장소 계정에 대 한 공용 액세스 설정 확인

Azure CLI를 사용 하 여 단일 저장소 계정에 대 한 공용 액세스 설정을 확인 하려면 [az resource show](/cli/azure/resource#az-resource-show) 명령을 호출 하 고 **Allowblobpublicaccess** 속성을 쿼리 합니다.

```azurecli-interactive
az resource show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --resource-type Microsoft.Storage/storageAccounts \
    --query properties.allowBlobPublicAccess \
    --output tsv
```

### <a name="check-the-public-access-setting-for-a-set-of-storage-accounts"></a>저장소 계정 집합에 대 한 공용 액세스 설정 확인

최적의 성능을 가진 일련의 저장소 계정에서 공용 액세스 설정을 확인 하려면 Azure Portal에서 Azure 리소스 그래프 탐색기를 사용할 수 있습니다. 리소스 그래프 탐색기를 사용 하는 방법에 대해 자세히 알아보려면 [빠른 시작: Azure 리소스 그래프 탐색기를 사용 하 여 첫 번째 리소스 그래프 쿼리 실행](/azure/governance/resource-graph/first-query-portal)을 참조 하세요.

리소스 그래프 탐색기에서 다음 쿼리를 실행 하면 저장소 계정 목록이 반환 되 고 각 계정에 대 한 **Allowblobpublicaccess** 속성의 값이 표시 됩니다.

```kusto
resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| extend allowBlobPublicAccess = parse_json(properties).allowBlobPublicAccess
| project subscriptionId, resourceGroup, name, allowBlobPublicAccess
| order by subscriptionId, resourceGroup, name asc
```

## <a name="set-the-public-access-level-for-a-container"></a>컨테이너에 대 한 공용 액세스 수준 설정

익명 사용자에 게 컨테이너 및 해당 blob에 대 한 읽기 액세스 권한을 부여 하려면 먼저 저장소 계정에 대 한 공용 액세스를 사용 하도록 설정 하 고 컨테이너의 공용 액세스 수준을 설정 합니다. 저장소 계정에 대 한 공용 액세스를 사용 하지 않도록 설정 된 경우에는 컨테이너에 대 한 공용 액세스를 구성할 수 없습니다.

저장소 계정에 대 한 공용 액세스를 사용 하도록 설정 된 경우 다음 권한으로 컨테이너를 구성할 수 있습니다.

- **공용 읽기 권한이 없습니다.** 컨테이너 및 해당 blob은 권한 있는 요청을 통해서만 액세스할 수 있습니다. 이 옵션은 모든 새 컨테이너의 기본값입니다.
- **Blob에 대 한 공용 읽기 액세스 전용:** 익명 요청을 통해 컨테이너 내의 blob을 읽을 수 있지만 컨테이너 데이터는 익명으로 사용할 수 없습니다. 익명 클라이언트는 컨테이너 내의 Blob을 열거할 수 없습니다.
- **컨테이너 및 해당 blob에 대 한 공용 읽기 권한:** 컨테이너 권한 설정 및 컨테이너 메타 데이터를 제외 하 고, 컨테이너 및 blob 데이터를 익명 요청에서 읽을 수 있습니다. 클라이언트는 익명 요청을 통해 컨테이너 내에서 Blob을 열거할 수 있지만 스토리지 계정 내에서 컨테이너를 열거할 수는 없습니다.

개별 blob에 대 한 공용 액세스 수준을 변경할 수 없습니다. 공용 액세스 수준은 컨테이너 수준 에서만 설정 됩니다.

컨테이너의 공용 액세스 수준을 설정 하려면 Azure Portal 또는 Azure CLI를 사용 합니다. 컨테이너를 만들 때 컨테이너의 공용 액세스 수준을 설정 하거나 기존 컨테이너에서이 설정을 업데이트할 수 있습니다.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure Portal에서 하나 이상의 기존 컨테이너에 대 한 공용 액세스 수준을 업데이트 하려면 다음 단계를 수행 합니다.

1. Azure Portal에서 저장소 계정 개요로 이동 합니다.
1. 메뉴 블레이드의 **Blob service** 아래에서 **컨테이너**를 선택 합니다.
1. 공용 액세스 수준을 설정 하려는 컨테이너를 선택 합니다.
1. **액세스 수준 변경** 단추를 사용 하 여 공용 액세스 설정을 표시 합니다.
1. **공용 액세스 수준** 드롭다운에서 원하는 공용 액세스 수준을 선택 하 고 확인 단추를 클릭 하 여 선택한 컨테이너에 변경 내용을 적용 합니다.

    ![포털에서 공용 액세스 수준을 설정하는 방법을 보여 주는 스크린샷](./media/anonymous-read-access-configure/configure-public-access-container.png)

저장소 계정에 대 한 공용 액세스를 사용할 수 없는 경우 컨테이너의 공용 액세스 수준을 설정할 수 없습니다. 컨테이너의 공용 액세스 수준을 설정 하려고 하면 계정에 대해 공용 액세스를 사용할 수 없으므로 설정이 비활성화 된 것을 볼 수 있습니다.

:::image type="content" source="media/anonymous-read-access-configure/container-public-access-blocked.png" alt-text="공용 액세스를 사용 하지 않도록 설정 하면 컨테이너 공용 액세스 수준이 차단 됨을 보여 주는 스크린샷":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용 하 여 하나 이상의 컨테이너에 대 한 공용 액세스 수준을 업데이트 하려면 [az storage container set permission](/cli/azure/storage/container#az-storage-container-set-permission) 명령을 호출 합니다. 계정 키, 연결 문자열 또는 SAS (공유 액세스 서명)를 전달 하 여이 작업에 권한을 부여 합니다. 컨테이너의 공용 액세스 수준을 설정 하는 [컨테이너 ACL 설정](/rest/api/storageservices/set-container-acl) 작업은 Azure AD에 대 한 권한 부여를 지원 하지 않습니다. 자세한 내용은 [blob 및 큐 데이터 작업 호출에 대 한 권한](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)을 참조 하세요.

다음 예제에서는 컨테이너 및 해당 blob에 대 한 익명 액세스를 사용 하도록 설정 하는 컨테이너에 대 한 공용 액세스 설정을 설정 합니다. 대괄호 안의 자리 표시자 값을 고유한 값으로 바꾸어야 합니다.

```azurecli-interactive
az storage container set-permission \
    --name <container-name> \
    --account-name <account-name> \
    --public-access container \
    --account-key <account-key> \
    --auth-mode key
```

저장소 계정에 대 한 공용 액세스를 사용할 수 없는 경우 컨테이너의 공용 액세스 수준을 설정할 수 없습니다. 컨테이너의 공용 액세스 수준을 설정 하려고 하면 저장소 계정에서 공용 액세스가 허용 되지 않음을 나타내는 오류가 발생 합니다.

---

## <a name="check-the-container-public-access-setting"></a>컨테이너 공용 액세스 설정 확인

하나 이상의 컨테이너에 대 한 공용 액세스 설정을 확인 하려면 Azure Portal, PowerShell Azure CLI, Azure Storage 클라이언트 라이브러리 중 하나 또는 Azure Storage 리소스 공급자를 사용할 수 있습니다. 다음 섹션에서는 몇 가지 예를 제공 합니다.  

### <a name="check-the-public-access-setting-for-a-single-container"></a>단일 컨테이너에 대 한 공용 액세스 설정 확인

Azure CLI를 사용 하 여 하나 이상의 컨테이너에 대 한 공용 액세스 수준을 가져오려면 [az storage container show permission](/cli/azure/storage/container#az-storage-container-show-permission) 명령을 호출 합니다. 계정 키, 연결 문자열 또는 SAS (공유 액세스 서명)를 전달 하 여이 작업에 권한을 부여 합니다. 컨테이너의 공용 액세스 수준을 반환 하는 [컨테이너 ACL 가져오기](/rest/api/storageservices/get-container-acl) 작업은 Azure AD를 사용한 권한 부여를 지원 하지 않습니다. 자세한 내용은 [blob 및 큐 데이터 작업 호출에 대 한 권한](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)을 참조 하세요.

다음 예제에서는 컨테이너에 대 한 공용 액세스 설정을 읽습니다. 대괄호 안의 자리 표시자 값을 고유한 값으로 바꾸어야 합니다.

```azurecli-interactive
az storage container show-permission \
    --name <container-name> \
    --account-name <account-name> \
    --account-key <account-key>
    --auth-mode key
```

### <a name="check-the-public-access-setting-for-a-set-of-containers"></a>컨테이너 집합에 대 한 공용 액세스 설정 확인

컨테이너를 나열 하 고 공용 액세스 설정을 확인 하 여 하나 이상의 저장소 계정에서 공용 액세스를 위해 구성 된 컨테이너를 확인할 수 있습니다. 이 방법은 저장소 계정에 많은 수의 컨테이너가 포함 되어 있지 않거나 소수의 저장소 계정에서 설정을 확인 하는 경우에 유용한 옵션입니다. 그러나 많은 수의 컨테이너를 열거 하려고 하면 성능이 저하 될 수 있습니다.

다음 예제에서는 PowerShell을 사용 하 여 저장소 계정의 모든 컨테이너에 대 한 공용 액세스 설정을 가져옵니다. 대괄호 안의 자리 표시자 값을 고유한 값으로 바꾸어야 합니다.

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

Get-AzStorageContainer -Context $ctx | Select Name, PublicAccess
```

## <a name="next-steps"></a>다음 단계

- [컨테이너 및 blob에 대 한 익명 공용 읽기 액세스 차단](anonymous-read-access-prevent.md)
- [.NET을 사용 하 여 공용 컨테이너 및 blob에 익명으로 액세스](anonymous-read-access-client.md)
- [Azure Storage에 대한 액세스 권한 부여](../common/storage-auth.md)