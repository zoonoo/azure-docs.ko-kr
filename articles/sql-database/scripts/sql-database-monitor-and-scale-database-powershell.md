---
title: PowerShell 예제 모니터링 크기 조정 단일 Azure SQL Database | Microsoft Docs
description: 단일 Azure SQL Database를 모니터링하고 크기를 조정하는 Azure PowerShell 예제 스크립트
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 09/14/2018
ms.openlocfilehash: 07cf57ab7095c51567caf33905fca78dd51be0fc
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/06/2018
ms.locfileid: "52966622"
---
# <a name="use-powershell-to-monitor-and-scale-a-single-sql-database"></a>PowerShell을 사용하여 단일 SQL Database 모니터링 및 크기 조정

이 PowerShell 스크립트 예제는 데이터베이스의 성능 메트릭을 모니터링하고, 더 높은 계산 크기로 확장하고, 성능 메트릭 중 하나에 경고 규칙을 만듭니다.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

PowerShell을 로컬로 설치하고 사용하도록 선택하는 경우 이 자습서에서는 Azure PowerShell 모듈 버전이 5.7.0 이상이어야 합니다. `Get-Module -ListAvailable AzureRM`을 실행하여 버전을 찾습니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-azurerm-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzureRmAccount`를 실행하여 Azure와 연결해야 합니다.

## <a name="sample-script"></a>샘플 스크립트

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/monitor-and-scale-database/monitor-and-scale-database.ps1?highlight=13-14 "Monitor and scale single SQL Database")]

> [!TIP]
> [Get-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/get-azurermsqldatabaseactivity)를 사용하여 데이터베이스 작업의 상태를 가져오고 [Stop-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/stop-azurermsqldatabaseactivity)를 사용하여 데이터베이스에서 업데이트 작업을 취소합니다.

## <a name="clean-up-deployment"></a>배포 정리

스크립트 샘플을 실행한 후에 다음 명령을 사용하여 리소스 그룹 및 관련된 모든 리소스를 제거할 수 있습니다.

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
 [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [New-AzureRmSqlServer](/powershell/module/azurerm.sql/new-azurermsqlserver) | 데이터베이스 또는 탄력적 풀을 호스트하는 논리 서버를 만듭니다. |
| [Get-AzureRmMetric](/powershell/module/azurerm.insights/get-azurermmetric) | 데이터베이스의 크기 사용량 정보가 표시됩니다.|
| [Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase) | 데이터베이스 속성을 업데이트하거나 탄력적 풀 내부, 외부 또는 내외부 상호 간에 데이터베이스를 이동합니다. |
| [Add-AzureRMMetricAlertRule](/powershell/module/azurerm.insights/add-azurermmetricalertrule) | 나중에 DTU를 자동으로 모니터링하도록 경고 규칙을 설정합니다. |
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | 모든 중첩 리소스를 포함한 리소스 그룹을 삭제합니다. |
|||

## <a name="next-steps"></a>다음 단계

Azure PowerShell에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/overview)를 참조하세요.

추가 SQL Database PowerShell 스크립트 샘플은 [Azure SQL Database PowerShell 스크립트](../sql-database-powershell-samples.md)에 있습니다.
