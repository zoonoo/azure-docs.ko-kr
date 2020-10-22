---
title: Azure Cosmos Table API 테이블에 대한 리소스 잠금을 만드는 PowerShell 스크립트
description: Azure Cosmos Table API 테이블에 대한 리소스 잠금 만들기
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: sample
ms.date: 06/12/2020
ms.openlocfilehash: 0d2d7b0e8d4a67b2ff67a780a71ea46ec54df1de
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92280003"
---
# <a name="create-a-resource-lock-for-azure-cosmos-table-api-table-using-azure-powershell"></a>Azure PowerShell을 사용하여 Azure Cosmos Table API 테이블에 대한 리소스 잠금 만들기

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-PowerShell-install](../../../../../includes/sample-PowerShell-install-no-ssh.md)]

> [!IMPORTANT]
> Cosmos DB SDK를 사용하여 연결하는 사용자가 만든 변경 내용, 계정 키를 통해 연결하는 모든 도구 또는 `disableKeyBasedMetadataWriteAccess` 속성을 사용하도록 설정하여 Cosmos DB 계정이 처음 잠겨 있지 않는 한 Azure Portal에서 리소스 잠금이 작동하지 않습니다. 이 속성을 사용하도록 설정하는 방법에 대한 자세한 내용은[Sdk변경 방지](../../../role-based-access-control.md#prevent-sdk-changes)를 참조하세요.

## <a name="sample-script"></a>샘플 스크립트

[!code-PowerShell[main](../../../../../PowerShell_scripts/cosmosdb/table/ps-table-lock.ps1 "Create, list, and remove resource locks")]

## <a name="clean-up-deployment"></a>배포 정리

스크립트 샘플을 실행한 후에 다음 명령을 사용하여 리소스 그룹 및 관련된 모든 리소스를 제거할 수 있습니다.

```PowerShell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
|**Azure 리소스**| |
| [New-AzResourceLock](https://docs.microsoft.com/PowerShell/module/az.resources/new-azresourcelock) | 리소스 잠금을 만듭니다. |
| [Get-AzResourceLock](https://docs.microsoft.com/PowerShell/module/az.resources/get-azresourcelock) | 리소스 잠금을 가져오거나 리소스 잠금을 나열합니다. |
| [Remove-AzResourceLock](https://docs.microsoft.com/PowerShell/module/az.resources/remove-azresourcelock) | 리소스 잠금을 제거합니다. |
|||

## <a name="next-steps"></a>다음 단계

Azure PowerShell에 대한 자세한 내용은 [Azure PowerShell 설명서](https://docs.microsoft.com/PowerShell/)를 참조하세요.
