---
title: PowerShell을 사용하여 데이터 액세스에 RBAC 역할 할당
titleSuffix: Azure Storage
description: PowerShell을 사용하여 RBAC(역할 기반 액세스 제어)를 사용하여 Azure Active Directory 보안 보안 주체에 권한을 할당하는 방법을 알아봅니다. Azure Storage는 Azure AD를 통한 인증을 위해 기본 제공 및 사용자 지정 RBAC 역할을 지원합니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 1413035c879198cf333aeeb5d8fe993162939172
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75460573"
---
# <a name="use-powershell-to-assign-an-rbac-role-for-access-to-blob-and-queue-data"></a>PowerShell을 사용하여 Blob 및 큐 데이터에 대한 액세스를 위해 RBAC 역할을 할당합니다.

Azure AD(Azure Active Directory)에서는 [RBAC(역할 기반 액세스 제어)](../../role-based-access-control/overview.md)를 통해 보호된 리소스에 액세스 권한을 부여합니다. Azure Storage는 컨테이너 또는 큐에 액세스하는 데 사용되는 사용 권한의 공통 집합을 포함하는 기본 제공 RBAC 역할 집합을 정의합니다.

RBAC 역할이 Azure AD 보안 보안 주체에 할당되면 Azure는 해당 보안 보안 주체에 대한 해당 리소스에 대한 액세스 권한을 부여합니다. 액세스 권한은 구독, 리소스 그룹, 스토리지 계정 또는 개별 컨테이너나 큐의 수준에 범위를 지정할 수 있습니다. Azure AD 보안 주체는 Azure 리소스에 대한 사용자, 그룹, 응용 프로그램 서비스 주체 또는 [관리되는 ID일](../../active-directory/managed-identities-azure-resources/overview.md)수 있습니다.

이 문서에서는 Azure PowerShell을 사용하여 기본 제공 RBAC 역할을 나열하고 사용자에게 할당하는 방법을 설명합니다. Azure PowerShell 사용에 대한 자세한 내용은 [Azure PowerShell 의 개요를](https://docs.microsoft.com/powershell/azure/overview)참조하십시오.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="rbac-roles-for-blobs-and-queues"></a>Blob 및 큐의 RBAC 역할

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>리소스 범위 결정

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="list-available-rbac-roles"></a>사용 가능한 RBAC 역할 목록

Azure PowerShell을 사용하여 사용 가능한 기본 제공 RBAC 역할을 나열하려면 [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) 명령을 사용합니다.

```powershell
Get-AzRoleDefinition | FT Name, Description
```

Azure에 대한 다른 기본 제공 역할과 함께 기본 제공 Azure Storage 데이터 역할이 나열되어 있습니다.

```Example
Storage Blob Data Contributor             Allows for read, write and delete access to Azure Storage blob containers and data
Storage Blob Data Owner                   Allows for full access to Azure Storage blob containers and data, including assigning POSIX access control.
Storage Blob Data Reader                  Allows for read access to Azure Storage blob containers and data
Storage Queue Data Contributor            Allows for read, write, and delete access to Azure Storage queues and queue messages
Storage Queue Data Message Processor      Allows for peek, receive, and delete access to Azure Storage queue messages
Storage Queue Data Message Sender         Allows for sending of Azure Storage queue messages
Storage Queue Data Reader                 Allows for read access to Azure Storage queues and queue messages
```

## <a name="assign-an-rbac-role-to-a-security-principal"></a>보안 주체에 RBAC 역할 할당

보안 주체에 RBAC 역할을 할당하려면 [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) 명령을 사용합니다. 명령의 형식은 할당 범위에 따라 다를 수 있습니다. 명령을 실행하려면 해당 범위에 소유자 또는 기여자 역할이 할당되어 있어야 합니다. 다음 예제에서는 다양한 범위에서 사용자에게 역할을 할당하는 방법을 보여 주지만 동일한 명령을 사용하여 모든 보안 주체에 역할을 할당할 수 있습니다.

### <a name="container-scope"></a>컨테이너 범위

컨테이너에 범위를 지정하는 역할을 할당하려면 매개 변수에 대한 컨테이너 의 `--scope` 범위를 포함하는 문자열을 지정합니다. 컨테이너의 범위는 다음과 같은 형태로 되어 있습니다.

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container-name>
```

다음 예제에서는 *샘플 컨테이너라는*컨테이너에 범위가 지정된 **저장소 Blob 데이터 기여자** 역할을 사용자에게 할당합니다. 괄호의 샘플 값과 자리 표시자 값을 고유한 값으로 바꿔야 합니다. 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/sample-container"
```

### <a name="queue-scope"></a>큐 범위

큐에 범위를 지정한 역할을 할당하려면 매개 변수에 대한 큐 범위를 `--scope` 포함하는 문자열을 지정합니다. 큐의 범위는 다음과 같은 형태로 표시됩니다.

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue-name>
```

다음 예제에서는 *샘플 큐라는*큐에 범위가 지정된 **사용자에게 저장소 큐 데이터 기여자** 역할을 할당합니다. 괄호의 샘플 값과 자리 표시자 값을 고유한 값으로 바꿔야 합니다. 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Queue Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/sample-queue"
```

### <a name="storage-account-scope"></a>저장소 계정 범위

저장소 계정에 범위가 지정된 역할을 할당하려면 매개 변수에 대한 저장소 `--scope` 계정 리소스의 범위를 지정합니다. 저장소 계정의 범위는 다음과 같은 형태로 되어 있습니다.

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

다음 예제에서는 저장소 계정 수준에서 사용자에게 **저장소 Blob 데이터 판독기** 역할을 범위를 정하는 방법을 보여 주십습니다. 샘플 값을 사용자 고유의 값으로 바꿔야 합니다. 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Reader" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

### <a name="resource-group-scope"></a>리소스 그룹 범위

리소스 그룹에 범위를 지정한 역할을 할당하려면 매개 변수에 대한 `--resource-group` 리소스 그룹 이름 또는 ID를 지정합니다. 다음 예제에서는 리소스 그룹 수준에서 사용자에게 **저장소 큐 데이터 판독기** 역할을 할당합니다. 괄호의 샘플 값과 자리 표시자 값을 고유한 값으로 바꿔야 합니다. 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Queue Data Reader" `
    -ResourceGroupName "sample-resource-group"
```

### <a name="subscription-scope"></a>구독 범위

구독에 범위를 지정하는 역할을 할당하려면 매개 변수에 대한 `--scope` 구독의 범위를 지정합니다. 구독 범위는 다음과 같은 형태로 표시됩니다.

```
/subscriptions/<subscription>
```

다음 예제에서는 저장소 계정 수준에서 사용자에게 **저장소 Blob 데이터 리더** 역할을 할당하는 방법을 보여 주습니다. 샘플 값을 사용자 고유의 값으로 바꿔야 합니다. 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Reader" `
    -Scope  "/subscriptions/<subscription>"
```

## <a name="next-steps"></a>다음 단계

- [RBAC 및 Azure PowerShell을 사용하여 Azure 리소스에 대한 액세스 관리](../../role-based-access-control/role-assignments-powershell.md)
- [Azure CLI에서 RBAC를 사용하여 Azure Blob 및 큐 데이터에 대한 액세스 권한 부여](storage-auth-aad-rbac-cli.md)
- [Azure Portal에서 RBAC를 사용하여 Azure Blob 및 큐 데이터에 대한 액세스 권한 부여](storage-auth-aad-rbac-portal.md)
