---
title: 자동 스케일링되는 Azure Cosmos MongoDB API 데이터베이스 및 컬렉션을 만드는 PowerShell 스크립트
description: Azure PowerShell 스크립트 - 자동 스케일링되는 Azure Cosmos MongoDB API 데이터베이스 및 컬렉션 만들기
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: sample
ms.date: 07/30/2020
ms.author: mjbrown
ms.openlocfilehash: 446de7ca48148bd8d9066e4862a23393850d5281
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92279388"
---
# <a name="create-a-database-and-collection-with-autoscale-for-azure-cosmos-db---mongodb-api"></a>자동 스케일링되는 Azure Cosmos DB - MongoDB API용 데이터베이스 및 컬렉션 만들기

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>샘플 스크립트

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/mongodb/ps-mongodb-autoscale.ps1 "Create a database and collection with autoscale for MongoDB API")]

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
| [New-AzCosmosDBAccount](/powershell/module/az.cosmosdb/new-azcosmosdbaccount) | Cosmos DB 계정을 만듭니다. |
| [New-AzCosmosDBMongoDBDatabase](/powershell/module/az.cosmosdb/new-azcosmosdbmongodbdatabase) | MongoDB API Database를 만듭니다. |
| [New-AzCosmosDBMongoDBIndex](/powershell/module/az.cosmosdb/new-azcosmosdbmongodbindex) | MongoDB API 인덱스를 만듭니다. |
| [New-AzCosmosDBMongoDBCollection](/powershell/module/az.cosmosdb/new-azcosmosdbmongodbcollection) | MongoDB API 컬렉션을 만듭니다. |
|**Azure 리소스 그룹**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 모든 중첩 리소스를 포함한 리소스 그룹을 삭제합니다. |
|||

## <a name="next-steps"></a>다음 단계

Azure PowerShell에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/)를 참조하세요.
