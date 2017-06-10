---
title: "Azure PowerShell 스크립트 - SQL 탄력적 풀 모니터링 및 크기 조정 | Microsoft Docs"
description: "Azure PowerShell 스크립트 샘플 - PowerShell을 사용하여 SQL Database 탄력적 풀 모니터링 및 크기 조정"
services: sql-database
documentationcenter: sql-database
author: janeng
manager: jstrauss
editor: carlrab
tags: azure-service-management
ms.assetid: 
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: PowerShell
ms.topic: sample
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 05/23/2017
ms.author: janeng
ms.translationtype: Human Translation
ms.sourcegitcommit: 67ee6932f417194d6d9ee1e18bb716f02cf7605d
ms.openlocfilehash: 5231f5b13c3502a5a45ceea0b329f30a2e1c311c
ms.contentlocale: ko-kr
ms.lasthandoff: 05/26/2017

---

# <a name="monitor-and-scale-a-sql-database-elastic-pool-using-powershell"></a>PowerShell을 사용하여 SQL Database 탄력적 풀 모니터링 및 크기 조정

이 샘플 PowerShell 스크립트는 탄력적 풀의 성능 메트릭을 모니터링하고, 더 높은 성능 수준으로 확장하고, 성능 메트릭 중 하나에 경고 규칙을 만듭니다. 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>샘플 스크립트

[!code-powershell[main](../../../powershell_scripts/sql-database/monitor-and-scale-pool/monitor-and-scale-pool.ps1?highlight=16-17 "단일 SQL Database 모니터링 및 크기 조정")]

## <a name="clean-up-deployment"></a>배포 정리

스크립트 샘플을 실행한 후에 다음 명령을 사용하여 리소스 그룹 및 관련된 모든 리소스를 제거할 수 있습니다.

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 참고 사항 |
|---|---|
 [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [New-AzureRmSqlServer](/powershell/module/azurerm.sql/new-azurermsqlserver) | 데이터베이스 또는 탄력적 풀을 호스트하는 논리 서버를 만듭니다. |
| [New-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/new-azurermsqlelasticpool) | 논리 서버 내에 탄력적 풀을 만듭니다. |
| [New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase) | 논리 서버에 데이터베이스를 단일 데이터베이스 또는 풀링된 데이터베이스로 만듭니다. |
| [Get-AzureRmMetric](/powershell/module/azurerm.insights/get-azurermmetric) | 데이터베이스의 크기 사용량 정보가 표시됩니다.|
| [Add-AzureRMMetricAlertRule](/powershell/module/azurerm.insights/add-azurermmetricalertrule) | 메트릭 기반 경고 규칙을 추가하거나 업데이트합니다. |
| [Set-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/set-azurermsqlelasticpool) | 탄력적 풀 속성을 업데이트합니다. |
| [Add-AzureRMMetricAlertRule](/powershell/module/azurerm.insights/add-azurermmetricalertrule) | 나중에 DTU를 자동으로 모니터링하도록 경고 규칙을 설정합니다. |
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | 모든 중첩 리소스를 포함한 리소스 그룹을 삭제합니다. |
|||

## <a name="next-steps"></a>다음 단계

Azure PowerShell에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/overview)를 참조하세요.

추가 SQL Database PowerShell 스크립트 샘플은 [Azure SQL Database PowerShell 스크립트](../sql-database-powershell-samples.md)에 있습니다.

