---
title: Azure Cosmos 계정의 지역을 업데이트하는 PowerShell 스크립트
description: Azure PowerShell 스크립트 샘플 - Azure Cosmos 계정 지역 업데이트
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/01/2020
ms.author: mjbrown
ms.openlocfilehash: 8bd5bba0f98a872ce31836217aef5605df3b0c41
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/23/2020
ms.locfileid: "92488183"
---
# <a name="update-an-azure-cosmos-accounts-regions-using-powershell"></a>PowerShell을 사용하여 Azure Cosmos 계정 지역 업데이트

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>샘플 스크립트

> [!NOTE]
> 동일한 작업에서 지역을 수정하고 다른 Cosmos 계정 속성을 변경할 수 없습니다. 이러한 작업은 두 가지 별도의 작업으로 수행되어야 합니다.
> [!NOTE]
> 이 샘플에서는 SQL(Core) API 계정을 사용하는 방법을 보여 줍니다. 이 샘플을 다른 API에 사용하려면 관련 속성을 복사하고 API별 스크립트에 적용합니다.

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/common/ps-account-update-region.ps1 "Update Azure Cosmos account regions")]

## <a name="clean-up-deployment"></a>배포 정리

스크립트 샘플을 실행한 후에 다음 명령을 사용하여 리소스 그룹 및 관련된 모든 리소스를 제거할 수 있습니다.

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
|**Azure Cosmos DB**| |
| [Get-AzCosmosDBAccount](/powershell/module/az.cosmosdb/get-azcosmosdbaccount) | Cosmos DB 계정을 나열하거나 지정된 Cosmos DB 계정을 가져옵니다. |
| [New-AzCosmosDBLocationObject](/powershell/module/az.cosmosdb/new-azcosmosdblocationobject) | Update-AzCosmosDBAccountRegion에 대한 매개 변수로 사용할 PSLocation 형식의 개체를 만듭니다. |
| [Update-AzCosmosDBAccountRegion](/powershell/module/az.cosmosdb/update-azcosmosdbaccountregion) | Cosmos DB 계정의 지역을 업데이트합니다. |
|**Azure 리소스 그룹**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 모든 중첩 리소스를 포함한 리소스 그룹을 삭제합니다. |
|||

## <a name="next-steps"></a>다음 단계

Azure PowerShell에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/)를 참조하세요.