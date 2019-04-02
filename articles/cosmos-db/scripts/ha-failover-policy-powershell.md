---
title: Azure PowerShell 스크립트 - Azure Cosmos DB 장애 조치 정책 만들기
description: Azure PowerShell 스크립트 샘플 - Azure Cosmos DB 장애 조치 정책 만들기
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.devlang: PowerShell
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 05/10/2017
ms.reviewer: sngun
ms.openlocfilehash: 2af1e3a2934ee57a310225b5b48a90629aac3846
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58497045"
---
# <a name="create-an-azure-cosmos-db-failover-policy-for-high-availability-using-powershell"></a>PowerShell을 사용하여 고가용성을 위한 Azure Cosmos DB 장애 조치 정책 만들기

이 샘플 PowerShell 스크립트는 Azure Cosmos DB의 고가용성을 위한 장애 조치 정책을 만듭니다. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>샘플 스크립트

[!code-powershell[main](../../../powershell_scripts/cosmosdb/modify-failover-priority/modify-failover-priority.ps1?highlight=36-39,42-47 "Create an Azure Cosmos DB SQL API account")]

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
