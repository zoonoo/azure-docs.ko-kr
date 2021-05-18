---
title: 스토리지 계정에 대한 Azure Resource Manager 잠금 적용
titleSuffix: Azure Storage
description: 스토리지 계정에 Azure Resource Manager 잠금을 적용하는 방법을 알아봅니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/09/2021
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 79b88ad58a2eb95a48a140b3b98d606af495cb94
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104799788"
---
# <a name="apply-an-azure-resource-manager-lock-to-a-storage-account"></a>스토리지 계정에 대한 Azure Resource Manager 잠금 적용

스토리지 계정이 실수로 또는 악의적으로 삭제되지 않도록 Azure Resource Manager 잠금으로 모든 스토리지 계정을 잠그는 것이 좋습니다. Azure Resource Manager 리소스 잠금에는 두 가지 유형이 있습니다.

- **CannotDelete** 잠금은 사용자가 스토리지 계정을 삭제하지 못하도록 차단하지만 구성을 읽고 수정할 수 있도록 합니다.
- **ReadOnly** 잠금은 사용자가 스토리지 계정을 삭제하거나 구성을 수정하는 것을 방지하지만 구성을 읽을 수 있습니다.

Azure Resource Manager 잠금에 대한 자세한 내용은 [변경을 방지하기 위한 리소스 잠금](../../azure-resource-manager/management/lock-resources.md)을 참조하세요.

> [!CAUTION]
> 스토리지 계정을 잠가도 해당 계정 내의 컨테이너 또는 Blob이 삭제되거나 덮어쓰이지 않도록 보호되지 않습니다. Blob 데이터를 보호하는 방법에 대한 자세한 내용은 [데이터 보호 개요](../blobs/data-protection-overview.md)를 참조하세요.

## <a name="configure-an-azure-resource-manager-lock"></a>Azure Resource Manager 잠금 구성

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure Portal을 사용하여 스토리지 계정에 대한 잠금을 구성하려면 다음 단계를 수행합니다.

1. Azure Portal의 스토리지 계정으로 이동합니다.
1. **설정** 섹션에서 **잠금** 을 선택합니다.
1. **추가** 를 선택합니다.
1. 리소스 잠금의 이름을 입력하고 잠금 유형을 지정합니다. 원하는 경우 잠금에 대한 메모를 추가합니다.

    :::image type="content" source="media/lock-account-resource/lock-storage-account.png" alt-text="CannotDelete 잠금으로 스토리지 계정을 잠그는 방법을 보여 주는 스크린샷":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용하여 스토리지 계정에 대한 잠금을 구성하려면 먼저 [Az PowerShell 모듈](https://www.powershellgallery.com/packages/Az)을 설치해야 합니다. 다음 예와 같이 [AzResourceLock](/powershell/module/az.resources/new-azresourcelock) 명령을 호출하고 만들려는 잠금 유형을 지정합니다.

```azurepowershell
New-AzResourceLock -LockLevel CanNotDelete `
    -LockName <lock> `
    -ResourceName <storage-account> `
    -ResourceType Microsoft.Storage/storageAccounts `
    -ResourceGroupName <resource-group>
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용하여 스토리지 계정에 대한 잠금을 구성하려면 다음 예와 같이 [az lock create](/cli/azure/lock#az_lock_create) 명령을 호출하고 만들려는 잠금 유형을 지정합니다.

```azurecli
az lock create \
    --name <lock> \
    --resource-group <resource-group> \
    --resource <storage-account> \
    --lock-type CanNotDelete \
    --resource-type Microsoft.Storage/storageAccounts
```

---

## <a name="authorizing-data-operations-when-a-readonly-lock-is-in-effect"></a>ReadOnly 잠금이 적용될 때 데이터 작업에 권한 부여

스토리지 계정에 **읽기 전용** 잠금이 적용되면 해당 스토리지 계정에 대한 [키 나열](/rest/api/storagerp/storageaccounts/listkeys) 작업이 차단됩니다. **키 나열** 작업은 HTTPS POST 작업이며 계정에 **읽기 전용** 잠금이 구성되면 모든 POST 작업이 금지됩니다. **키 나열** 작업은 계정 액세스 키를 반환하며,이 키를 사용하여 스토리지 계정의 모든 데이터를 읽고 쓸 수 있습니다.

잠금이 스토리지 계정에 적용될 때 클라이언트가 계정 액세스 키를 소유하고 있는 경우 해당 클라이언트는 계속해서 키를 사용하여 데이터에 액세스할 수 있습니다. 그러나 키에 대한 액세스 권한이 없는 클라이언트는 Azure AD(Azure Active Directory) 자격 증명을 사용하여 스토리지 계정의 Blob 또는 큐 데이터에 액세스해야 합니다.

Azure Portal의 사용자는 계정 액세스 키를 사용하여 이전에 포털의 Blob 또는 큐 데이터에 액세스한 경우 **읽기 전용** 잠금이 적용될 때 영향을 받을 수 있습니다. 잠금이 적용된 후 포털 사용자는 Azure AD 자격 증명을 사용하여 포털의 Blob 또는 큐 데이터에 액세스해야 합니다. 이렇게 하려면 사용자에게 최소한 두 개의 RBAC 역할, 즉 최소한 Azure Resource Manager 읽기 권한자 역할과 Azure Storage 데이터 액세스 역할 중 하나가 할당되어 있어야 합니다. 자세한 내용은 다음 문서 중 하나를 참조하세요.

- [Azure Portal에서 Blob 데이터에 대한 액세스 권한을 부여하는 방법을 선택합니다.](../blobs/authorize-data-operations-portal.md)
- [Azure Portal에서 큐 데이터에 대한 액세스 권한을 부여하는 방법 선택](../queues/authorize-data-operations-portal.md)

Azure Files 또는 Table 서비스의 데이터는 이전에 계정 키를 사용하여 액세스한 클라이언트에 액세스하지 못할 수 있습니다. 모범 사례로 스토리지 계정에 **읽기 전용** 잠금을 적용해야 하는 경우 Azure Files 및 Table 서비스 워크로드를 **읽기 전용** 잠금으로 잠기지 않은 스토리지 계정으로 이동합니다.

## <a name="next-steps"></a>다음 단계

- [데이터 보호 개요](../blobs/data-protection-overview.md)
- [변경을 방지하기 위해 리소스 잠그기](../../azure-resource-manager/management/lock-resources.md)
