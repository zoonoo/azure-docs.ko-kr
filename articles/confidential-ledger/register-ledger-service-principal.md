---
title: Microsoft Azure Confidential Ledger를 사용하여 Ledger 서비스 주체 등록
description: Microsoft Azure Confidential Ledger를 사용하여 Ledger 서비스 주체 등록
services: confidential-ledger
author: msmbaldwin
ms.service: confidential-ledger
ms.topic: overview
ms.date: 04/15/2021
ms.author: mbaldwin
ms.openlocfilehash: a4f751bcb262011b073207751ac590edeb41d391
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110387637"
---
# <a name="register-a-confidential-ledger-service-principal"></a>Confidential Ledger 서비스 주체 등록

스토리지 계정을 Confidential Ledger와 연결하려면 먼저 Confidential Ledger 서비스 주체를 등록해야 합니다.

## <a name="create-a-service-principal"></a>서비스 주체 만들기

서비스 주체를 만들려면 Azure CLI [az ad sp create](/cli/azure/ad/sp#az_ad_sp_create) 명령 또는 Azure PowerShell [Connect-AzureAD](/powershell/module/azuread/connect-azuread) 및 [New-AzureADServicePrincipal](/powershell/module/azuread/new-azureadserviceprincipal) cmdlet을 실행합니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli-interactive
az ad sp create --id 4353526e-1c33-4fcf-9e82-9683edf52848
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell-interactive
Connect-AzureAD -TenantId "<tenant-id-of-customer>"
New-AzureADServicePrincipal -AppId 4353526e-1c33-4fcf-9e82-9683edf52848 -DisplayName ConfidentialLedger
```
---

## <a name="assign-roles"></a>역할 할당

스토리지 계정의 Confidential Ledger 서비스 주체에 대해 IAM "[스토리지 Blob 데이터 기여자](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)"를 설정합니다. 이는 Azure CLI [az role assignment create](/cli/azure/role/assignment) 명령 또는 Azure PowerShell [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) cmdlet을 사용하여 수행할 수 있습니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli-interactive
az role assignment create --role "Storage Blob Data Contributor" --assignee "4353526e-1c33-4fcf-9e82-9683edf52848" --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell-interactive
New-AzRoleAssignment -ApplicationId 4353526e-1c33-4fcf-9e82-9683edf52848 -RoleDefinitionName "Storage Blob Data Contributor" -Scope "/subscriptions/<subscription-id>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```
---

## <a name="next-steps"></a>다음 단계

- [Microsoft Azure Confidential Ledger 개요](overview.md)
