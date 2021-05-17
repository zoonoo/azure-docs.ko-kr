---
title: Azure CLI를 사용하여 데이터 액세스를 위한 Azure 역할 할당
titleSuffix: Azure Storage
description: Azure CLI를 사용하여 Azure RBAC(Azure 역할 기반 액세스 제어)를 통해 Azure Active Directory 보안 주체에 권한을 할당하는 방법을 알아봅니다. Azure Storage는 Azure AD를 통해 인증에 대한 기본 제공 역할 및 Azure 사용자 지정 역할을 지원합니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/10/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurecli
ms.openlocfilehash: 93d9a81ab75efe4ea38189a7280e041e545a2b7d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785328"
---
# <a name="use-azure-cli-to-assign-an-azure-role-for-access-to-blob-and-queue-data"></a>Azure CLI를 사용하여 Blob 및 큐 데이터에 액세스하기 위한 Azure 역할 할당

Azure AD(Azure Active Directory)는 [Azure RBAC](../../role-based-access-control/overview.md)(Azure 역할 기반 액세스 제어)를 통해 보안 리소스에 대한 액세스 권한을 부여합니다. Azure Storage는 Blob 또는 큐 데이터에 액세스하는 데 사용되는 일반 권한 집합을 포함하는 Azure 기본 제공 역할 집합을 정의합니다.

Azure AD 보안 주체에 Azure 역할을 할당하면 Azure는 해당 보안 주체에 대한 리소스에 액세스 권한을 부여합니다. 액세스 권한은 구독, 리소스 그룹, 스토리지 계정 또는 개별 컨테이너나 큐의 수준에 범위를 지정할 수 있습니다. Azure AD 보안 주체는 사용자, 그룹, 애플리케이션 서비스 주체, 또는 [Azure 리소스의 관리 ID](../../active-directory/managed-identities-azure-resources/overview.md)일 수 있습니다.

이 문서에서는 Azure CLI를 사용하여 Azure 기본 제공 역할을 정리하고 사용자에게 할당하는 방법을 설명합니다. Azure CLI 사용에 대한 자세한 내용은 [Azure CLI(명령줄 인터페이스)](/cli/azure)를 참조하세요.

## <a name="azure-roles-for-blobs-and-queues"></a>Blob 및 큐를 위한 Azure 역할

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>리소스 범위 결정

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="list-available-azure-roles"></a>사용 가능한 Azure 역할 목록

Azure CLI에서 사용 가능한 Azure 기본 제공 역할을 나열하려면 [az role definition list](/cli/azure/role/definition#az_role_definition_list) 명령을 사용합니다.

```azurecli-interactive
az role definition list --out table
```

기본 제공되는 Azure Storage 데이터 역할이 Azure에 대한 다른 기본 제공 역할과 함께 나열됩니다.

```Example
Storage Blob Data Contributor             Allows for read, write and delete access to Azure Storage blob containers and data
Storage Blob Data Owner                   Allows for full access to Azure Storage blob containers and data, including assigning POSIX access control.
Storage Blob Data Reader                  Allows for read access to Azure Storage blob containers and data
Storage Queue Data Contributor            Allows for read, write, and delete access to Azure Storage queues and queue messages
Storage Queue Data Message Processor      Allows for peek, receive, and delete access to Azure Storage queue messages
Storage Queue Data Message Sender         Allows for sending of Azure Storage queue messages
Storage Queue Data Reader                 Allows for read access to Azure Storage queues and queue messages
```

## <a name="assign-an-azure-role-to-a-security-principal"></a>보안 주체에 Azure 역할 할당

Azure 역할을 보안 주체에 할당하려면 [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) 명령을 사용합니다. 명령의 형식은 할당 범위에 따라 다를 수 있습니다. 다음 예제에서는 다양한 범위에서 사용자에게 역할을 할당하는 방법을 보여 주지만, 동일한 명령을 사용하여 보안 주체에 역할을 할당할 수도 있습니다.

> [!IMPORTANT]
> Azure Storage 계정을 만들어도 Azure AD를 통해 데이터에 액세스할 수 있는 권한이 자동으로 할당되지는 않습니다. 사용자는 데이터에 액세스할 수 있는 권한을 위해 Azure RBAC 역할을 직접 명시적으로 할당해야 합니다. 구독, 리소스 그룹, 스토리지 계정 또는 컨테이너나 큐 수준으로 지정할 수 있습니다.
>
> 스토리지 계정이 Azure Resource Manager 읽기 전용 잠금으로 잠긴 경우, 잠금은 스토리지 계정 또는 데이터 컨테이너(Blob 컨테이너 또는 큐)로 범위가 지정된 Azure RBAC 역할의 할당을 방지합니다.

### <a name="container-scope"></a>컨테이너 범위

컨테이너에 범위가 지정된 역할을 할당하려면 `--scope` 매개 변수의 컨테이너 범위를 포함하는 문자열을 지정합니다. 컨테이너의 범위는 다음과 같은 형식입니다.

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container>
```

다음 예제에서는 컨테이너 수준에 범위가 지정된 사용자에게 **스토리지 Blob 데이터 기여자** 역할을 할당합니다. 샘플 값과 대괄호 안의 자리 표시자 값을 고유한 값으로 바꿔야 합니다.

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container>"
```

### <a name="queue-scope"></a>큐 범위

범위가 지정된 역할을 큐에 할당하려면 `--scope` 매개 변수의 큐 범위를 포함하는 문자열을 지정합니다. 큐의 범위는 다음과 같은 형식입니다.

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue>
```

다음 예제에서는 큐 수준에 범위가 지정된 **스토리지 큐 데이터 기여자** 역할을 할당합니다. 샘플 값과 대괄호 안의 자리 표시자 값을 고유한 값으로 바꿔야 합니다.

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue>"
```

### <a name="storage-account-scope"></a>스토리지 계정 범위

범위가 지정된 역할을 스토리지 계정에 할당하려면 `--scope` 매개 변수에 대한 스토리지 계정 리소스의 범위를 지정합니다. 스토리지 계정의 범위는 다음과 같은 형식입니다.

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

다음 예제에서는 스토리지 계정 수준에서 사용자에게 **스토리지 Blob 데이터 읽기 권한자** 역할을 할당하는 방법을 보여 줍니다. 샘플 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Reader" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

### <a name="resource-group-scope"></a>리소스 그룹 범위

범위가 지정된 역할을 리소스 그룹에 할당하려면 `--resource-group` 매개 변수의 리소스 그룹 이름 또는 ID를 지정합니다. 다음 예제에서는 리소스 그룹 수준에서 사용자에게 **스토리지 큐 데이터 읽기 권한자** 역할을 할당합니다. 샘플 값과 대괄호 안의 자리 표시자 값을 고유한 값으로 바꿔야 합니다.

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Reader" \
    --assignee <email> \
    --resource-group <resource-group>
```

### <a name="subscription-scope"></a>구독 범위

범위가 지정된 역할을 구독에 할당하려면 `--scope` 매개 변수에 대한 구독의 범위를 지정합니다. 구독의 범위는 다음과 같은 형식입니다.

```
/subscriptions/<subscription>
```

다음 예제에서는 스토리지 계정 수준에서 사용자에게 **스토리지 Blob 데이터 읽기 권한자** 역할을 할당하는 방법을 보여 줍니다. 샘플 값을 사용자 고유의 값으로 바꿔야 합니다. 

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Reader" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>"
```

## <a name="next-steps"></a>다음 단계

- [Azure PowerShell 모듈을 사용하여 Azure 역할 할당 추가 또는 제거](../../role-based-access-control/role-assignments-powershell.md)
- [Azure PowerShell 모듈을 사용하여 Blob 및 큐 데이터에 액세스하기 위한 Azure 역할 할당](storage-auth-aad-rbac-powershell.md)
- [Azure Portal을 사용하여 Blob 및 큐 데이터에 액세스하기 위한 Azure 역할 할당](storage-auth-aad-rbac-portal.md)
