---
title: Azure PowerShell 스크립트 - Azure Cosmos 계정에서 해제된 컨테이너 인덱싱 만들기
description: Azure PowerShell 스크립트 샘플 - Azure Cosmos 계정에서 해제된 컨테이너 인덱싱 만들기
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 05/06/2019
ms.author: mjbrown
ms.openlocfilehash: d216aa7eaa25ee18c7b5e962309e2a08f11df284
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2019
ms.locfileid: "71178823"
---
# <a name="create-a-container-indexing-turned-off-in-an-azure-cosmos-account-using-powershell"></a>PowerShell을 사용하여 Azure Cosmos 계정에서 해제된 컨테이너 인덱싱 만들기

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>샘플 스크립트

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/sql/ps-container-create-index-none.ps1 "Create a container indexing turned off in an Azure Cosmos account")]

## <a name="clean-up-deployment"></a>배포 정리

스크립트 샘플을 실행한 후에 다음 명령을 사용하여 리소스 그룹 및 관련된 모든 리소스를 제거할 수 있습니다.

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
|**Azure 리소스**| |
| [New-AzResource](https://docs.microsoft.com/powershell/module/az.resources/new-azresource) | 리소스를 만듭니다. |
|**Azure 리소스 그룹**| |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | 모든 중첩 리소스를 포함한 리소스 그룹을 삭제합니다. |
|||

## <a name="next-steps"></a>다음 단계

Azure PowerShell에 대한 자세한 내용은 [Azure PowerShell 설명서](https://docs.microsoft.com/powershell/)를 참조하세요.

추가 Azure Cosmos DB PowerShell 스크립트 샘플은 [Azure Cosmos DB PowerShell 스크립트](../../../powershell-samples.md)에 있습니다.