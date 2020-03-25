---
title: PowerShell 예제 - 장애 조치(failover) 그룹 - Azure SQL Database 탄력적 풀
description: Azure SQL Database 탄력적 풀을 만들어서 장애 조치(failover) 그룹에 추가하고 장애 조치(failover)를 테스트하는 Azure PowerShell 예제 스크립트입니다.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 07/16/2019
ms.openlocfilehash: a8c9b7dbddba59408808e1c33de045ad0b7ffd7a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2020
ms.locfileid: "73691796"
---
# <a name="use-powershell-to-add-an-azure-sql-database-elastic-pool-to-a-failover-group"></a>PowerShell을 사용하여 Azure SQL Database 탄력적 풀을 장애 조치(failover) 그룹에 추가 

이 PowerShell 스크립트 예제에서는 단일 데이터베이스를 만들어 탄력적 풀에 추가하고, 장애 조치(failover) 그룹을 만들고, 장애 조치(failover)를 테스트합니다. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

PowerShell을 로컬로 설치하고 사용하도록 선택하는 경우 이 자습서에는 AZ PowerShell 1.4.0 이상이 필요합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzAccount`를 실행하여 Azure와 연결해야 합니다.

## <a name="sample-scripts"></a>샘플 스크립트

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/failover-groups/add-elastic-pool-to-failover-group-az-ps.ps1 "Add elastic pool to a failover group")]

## <a name="clean-up-deployment"></a>배포 정리

다음 명령을 사용하여 리소스 그룹 및 모든 관련 리소스를 제거합니다.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | 단일 데이터베이스 및 탄력적 풀을 호스트하는 SQL Database 서버를 만듭니다. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | 논리 서버에 대한 방화벽 규칙을 만듭니다. | 
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | 새 Azure SQL Database 단일 데이터베이스를 만듭니다. | 
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Azure SQL Database에 대한 탄력적 데이터베이스 풀을 만듭니다.| 
| [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) | 데이터베이스의 속성 설정 또는 기존 데이터베이스를 탄력적 풀로 이동. | 
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | 새 장애 조치(failover) 그룹을 만듭니다. |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | 하나 이상의 SQL 데이터베이스를 가져옵니다. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | 장애 조치(failover) 그룹에 하나 이상의 Azure SQL Database 데이터베이스를 추가합니다. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Azure SQL Database 장애 조치(failover) 그룹을 가져오거나 나열합니다. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Azure SQL Database 장애 조치(failover) 그룹의 장애 조치(failover)를 실행합니다. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 리소스 그룹을 제거합니다. | 

## <a name="next-steps"></a>다음 단계

Azure PowerShell에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/overview)를 참조하세요.

추가 SQL Database PowerShell 스크립트 샘플은 [Azure SQL Database PowerShell 스크립트](../sql-database-powershell-samples.md)에 있습니다.
