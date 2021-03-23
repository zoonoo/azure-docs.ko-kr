---
title: 저장소 계정에 Azure Resource Manager 잠금 적용
titleSuffix: Azure Storage
description: 저장소 계정에 Azure Resource Manager 잠금을 적용 하는 방법에 대해 알아봅니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/09/2021
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 79b88ad58a2eb95a48a140b3b98d606af495cb94
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104799788"
---
# <a name="apply-an-azure-resource-manager-lock-to-a-storage-account"></a>저장소 계정에 Azure Resource Manager 잠금 적용

저장소 계정에 대 한 실수로 인 한 삭제를 방지 하기 위해 Azure Resource Manager 잠금으로 모든 저장소 계정을 잠그는 것이 좋습니다. 리소스 잠금의 Azure Resource Manager는 두 가지 유형이 있습니다.

- **Cannotdelete** 잠금은 사용자가 저장소 계정을 삭제 하는 것을 방지 하지만 해당 구성을 읽고 수정할 수 있습니다.
- **ReadOnly** 잠금은 사용자가 저장소 계정을 삭제 하거나 구성을 수정 하는 것을 방지 하지만 구성을 읽을 수 있습니다.

Azure Resource Manager 잠금에 대 한 자세한 내용은 [변경을 방지 하기 위해 리소스 잠그기](../../azure-resource-manager/management/lock-resources.md)를 참조 하세요.

> [!CAUTION]
> 저장소 계정을 잠그면 해당 계정 내의 컨테이너 또는 blob을 삭제 하거나 덮어쓸 수 없습니다. Blob 데이터를 보호 하는 방법에 대 한 자세한 내용은 [데이터 보호 개요](../blobs/data-protection-overview.md)를 참조 하세요.

## <a name="configure-an-azure-resource-manager-lock"></a>Azure Resource Manager 잠금 구성

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure Portal를 사용 하 여 저장소 계정에 대 한 잠금을 구성 하려면 다음 단계를 수행 합니다.

1. Azure Portal의 스토리지 계정으로 이동합니다.
1. **설정** 섹션에서 **잠금** 을 선택 합니다.
1. **추가** 를 선택합니다.
1. 리소스 잠금에 대 한 이름을 제공 하 고 잠금 유형을 지정 합니다. 원하는 경우 잠금에 대 한 메모를 추가 합니다.

    :::image type="content" source="media/lock-account-resource/lock-storage-account.png" alt-text="CannotDelete 잠금을 사용 하 여 저장소 계정을 잠그는 방법을 보여 주는 스크린샷":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용 하 여 저장소 계정에 대 한 잠금을 구성 하려면 먼저 [Az PowerShell module](https://www.powershellgallery.com/packages/Az)을 설치 했는지 확인 합니다. 그런 다음 [AzResourceLock](/powershell/module/az.resources/new-azresourcelock) 명령을 호출 하 고 다음 예제와 같이 만들려는 잠금 유형을 지정 합니다.

```azurepowershell
New-AzResourceLock -LockLevel CanNotDelete `
    -LockName <lock> `
    -ResourceName <storage-account> `
    -ResourceType Microsoft.Storage/storageAccounts `
    -ResourceGroupName <resource-group>
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용 하 여 저장소 계정에 대 한 잠금을 구성 하려면 다음 예제와 같이 [az lock create](/cli/azure/lock#az_lock_create) 명령을 호출 하 고 만들려는 잠금 유형을 지정 합니다.

```azurecli
az lock create \
    --name <lock> \
    --resource-group <resource-group> \
    --resource <storage-account> \
    --lock-type CanNotDelete \
    --resource-type Microsoft.Storage/storageAccounts
```

---

## <a name="authorizing-data-operations-when-a-readonly-lock-is-in-effect"></a>ReadOnly 잠금이 적용 되는 경우 데이터 작업에 권한 부여

저장소 계정에 **ReadOnly** 잠금을 적용 하면 해당 저장소 계정에 대 한 [키 나열](/rest/api/storagerp/storageaccounts/listkeys) 작업이 차단 됩니다. **키 나열** 작업은 HTTPS POST 작업 이며 계정에 대해 **ReadOnly** 잠금이 구성 된 경우 모든 POST 작업은 차단 됩니다. **키 나열** 작업은 계정 액세스 키를 반환 합니다 .이 키는 저장소 계정에서 데이터를 읽고 쓰는 데 사용할 수 있습니다.

클라이언트가 저장소 계정에 잠금이 적용 될 때 계정 액세스 키를 소유 하는 경우 해당 클라이언트는 계속 해 서 키를 사용 하 여 데이터에 액세스할 수 있습니다. 그러나 키에 대 한 액세스 권한이 없는 클라이언트는 Azure AD (Azure Active Directory) 자격 증명을 사용 하 여 저장소 계정의 blob 또는 큐 데이터에 액세스 해야 합니다.

이전에 계정 액세스 키를 사용 하 여 포털에서 blob 또는 큐 데이터에 액세스 한 경우에는 **ReadOnly** 잠금이 적용 될 때 Azure Portal 사용자에 게 영향을 줄 수 있습니다. 잠금이 적용 된 후 포털 사용자는 Azure AD 자격 증명을 사용 하 여 포털에서 blob 또는 큐 데이터에 액세스 해야 합니다. 이렇게 하려면 사용자에 게 최소한 두 개의 RBAC 역할이 할당 되어 있어야 합니다. 최소한 Azure Resource Manager 판독기 역할 및 Azure Storage 데이터 액세스 역할 중 하나입니다. 자세한 내용은 다음 문서 중 하나를 참조하세요.

- [Azure Portal에서 blob 데이터에 대 한 액세스 권한을 부여 하는 방법을 선택 합니다.](../blobs/authorize-data-operations-portal.md)
- [Azure Portal에서 큐 데이터에 대 한 액세스 권한을 부여 하는 방법을 선택 합니다.](../queues/authorize-data-operations-portal.md)

Azure Files 또는 Table service의 데이터는 이전에 계정 키를 사용 하 여 액세스 한 클라이언트에 액세스할 수 없습니다 될 수 있습니다. 저장소 계정에 **readonly** 잠금을 적용 해야 하는 경우에는 Azure Files 및 Table service 작업을 **readonly** 잠금으로 잠기지 않은 저장소 계정으로 이동 하는 것이 가장 좋습니다.

## <a name="next-steps"></a>다음 단계

- [데이터 보호 개요](../blobs/data-protection-overview.md)
- [변경을 방지하기 위해 리소스 잠그기](../../azure-resource-manager/management/lock-resources.md)
