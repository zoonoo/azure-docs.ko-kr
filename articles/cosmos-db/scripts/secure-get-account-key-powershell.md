---
title: Azure PowerShell 스크립트 - Azure Cosmos DB의 계정 키 가져오기
description: Azure PowerShell 스크립트 샘플 - Azure Cosmos DB의 계정 키 가져오기
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
author: SnehaGunda
ms.author: sngun
ms.devlang: PowerShell
ms.topic: sample
ms.date: 05/10/2017
ms.reviewer: sngun
ms.openlocfilehash: 088b09e6d97bcf0dc62a96754d2acd0e611865c8
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58500273"
---
# <a name="get-account-keys-for-azure-cosmos-db-using-powershell"></a>PowerShell을 사용하여 Azure Cosmos DB의 계정 키 가져오기

이 샘플은 모든 종류의 Azure Cosmos DB 계정의 계정 키를 가져옵니다.  

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>샘플 스크립트

[!code-powershell[main](../../../powershell_scripts/cosmosdb/get-account-keys/get-account-keys.ps1?highlight=36-40 "Get the keys for an Azure Cosmos DB account")]

## <a name="clean-up-deployment"></a>배포 정리

스크립트 샘플을 실행한 후에 다음 명령을 사용하여 리소스 그룹 및 관련된 모든 리소스를 제거할 수 있습니다.

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [New-AzResource](https://docs.microsoft.com/powershell/module/az.resources/new-azresource) | 데이터베이스 또는 탄력적 풀을 호스트하는 논리 서버를 만듭니다. |
| [Invoke-AzResourceAction](https://docs.microsoft.com/powershell/module/az.resources/invoke-azresourceaction) | Azure CosmosDB 계정에 대한 작업을 호출합니다. |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | 모든 중첩 리소스를 포함한 리소스 그룹을 삭제합니다. |
|||

## <a name="next-steps"></a>다음 단계

Azure PowerShell에 대한 자세한 내용은 [Azure PowerShell 설명서](https://docs.microsoft.com/powershell/)를 참조하세요.

추가 Azure Cosmos DB PowerShell 스크립트 샘플은 [Azure Cosmos DB PowerShell 스크립트](../powershell-samples.md)에 있습니다.
