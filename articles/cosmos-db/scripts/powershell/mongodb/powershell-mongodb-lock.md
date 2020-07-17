---
title: Azure Cosmos MongoDB API 데이터베이스 및 컬렉션에 대한 리소스 잠금을 만드는 PowerShell 스크립트
description: Azure Cosmos MongoDB API 데이터베이스 및 컬렉션에 대한 리소스 잠금 만들기
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: sample
ms.date: 06/12/2020
ms.openlocfilehash: b9a457536754539aa8e736953913590a71b5a32c
ms.sourcegitcommit: 398fecceba133d90aa8f6f1f2af58899f613d1e3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/21/2020
ms.locfileid: "85127209"
---
# <a name="create-a-resource-lock-for-azure-cosmos-mongodb-api-database-and-collection-using-azure-powershell"></a>Azure PowerShell을 사용하여 Azure Cosmos MongoDB API 데이터베이스 및 컬렉션에 대한 리소스 잠금 만들기

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

> [!IMPORTANT]
> `disableKeyBasedMetadataWriteAccess` 속성을 사용하도록 설정된 상태에서 먼저 Cosmos DB 계정이 잠겨 있지 않는 한 MongoDB SDK, Mongoshell, 도구 또는 Azure Portal을 사용하여 연결한 사용자가 변경한 내용에 대해서는 리소스 잠금이 작동하지 않습니다. 이 속성을 사용하도록 설정하는 방법에 대한 자세한 내용은 [SDK에서 변경 방지](../../../role-based-access-control.md#preventing-changes-from-cosmos-sdk)를 참조하세요.

## <a name="sample-script"></a>샘플 스크립트

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/mongodb/ps-mongodb-lock.ps1 "Create, list, and remove resource locks")]

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
| [New-AzResourceLock](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcelock) | 리소스 잠금을 만듭니다. |
| [Get-AzResourceLock](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcelock) | 리소스 잠금을 가져오거나 리소스 잠금을 나열합니다. |
| [Remove-AzResourceLock](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcelock) | 리소스 잠금을 제거합니다. |
|||

## <a name="next-steps"></a>다음 단계

Azure PowerShell에 대한 자세한 내용은 [Azure PowerShell 설명서](https://docs.microsoft.com/powershell/)를 참조하세요.

추가 Azure Cosmos DB PowerShell 스크립트 샘플은 [Azure Cosmos DB PowerShell 스크립트](../../../powershell-samples.md)에 있습니다.