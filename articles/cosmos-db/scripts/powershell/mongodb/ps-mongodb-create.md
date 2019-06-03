---
title: Azure PowerShell 스크립트-Azure Cosmos DB MongoDB API 데이터베이스 및 컬렉션 만들기
description: Azure PowerShell 스크립트-Azure Cosmos DB MongoDB API 데이터베이스 및 컬렉션 만들기
author: markjbrown
ms.service: cosmos-db
ms.topic: samples
ms.date: 05/18/2019
ms.author: mjbrown
ms.openlocfilehash: 9263df4b803d60fa0bfdf5bdc30f62e732e774b9
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66247456"
---
# <a name="create-a-database-and-collection-for-azure-cosmos-db---mongodb-api"></a>데이터베이스 및 Azure Cosmos DB MongoDB API에 대 한 컬렉션 만들기

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>샘플 스크립트

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/mongodb/ps-mongodb-create.ps1 "Create a database and collection for MongoDB API")]

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