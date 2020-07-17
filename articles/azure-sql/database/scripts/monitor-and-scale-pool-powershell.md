---
title: PowerShell 예제-모니터링-크기 조정-탄력적 풀-Azure SQL Database
description: Azure SQL Database에서 탄력적 풀을 모니터링하고 크기를 조정하는 Azure PowerShell 예제 스크립트
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: PowerShell
ms.topic: sample
author: juliemsft
ms.author: jrasnick
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: c0ce0a1019554592f769d88f4168da7dc6188586
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2020
ms.locfileid: "84196834"
---
# <a name="use-powershell-to-monitor-and-scale-an-elastic-pool-in-azure-sql-database"></a>PowerShell을 사용하여 Azure SQL Database에서 탄력적 풀 모니터링 및 크기 조정
[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

이 PowerShell 스크립트 예제는 탄력적 풀의 성능 메트릭을 모니터링하고, 더 높은 컴퓨팅 크기로 확장하고, 성능 메트릭 중 하나에 경고 규칙을 만듭니다.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

PowerShell을 로컬로 설치하고 사용하도록 선택하는 경우 이 자습서에는 Az PowerShell 1.4.0 이상이 필요합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzAccount`를 실행하여 Azure와 연결해야 합니다.

## <a name="sample-script"></a>샘플 스크립트

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-database/monitor-and-scale-pool/monitor-and-scale-pool.ps1?highlight=17-18 "Monitor and scale an elastic pool in SQL Database")]

## <a name="clean-up-deployment"></a>배포 정리

다음 명령을 사용하여 리소스 그룹 및 모든 관련 리소스를 제거합니다.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 표에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | 데이터베이스 또는 탄력적 풀을 호스트하는 서버를 만듭니다. |
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | 탄력적 풀을 만듭니다. |
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | 서버에 데이터베이스를 만듭니다. |
| [Get-AzMetric](/powershell/module/az.monitor/get-azmetric) | 데이터베이스의 크기 사용량 정보가 표시됩니다.|
| [Add-AzMetricAlertRule](/powershell/module/az.monitor/add-azmetricalertrule) | 메트릭 기반 경고 규칙을 추가하거나 업데이트합니다. |
| [Set-AzSqlElasticPool](/powershell/module/az.sql/set-azsqlelasticpool) | 탄력적 풀 속성을 업데이트합니다. |
| [Add-AzMetricAlertRule](/powershell/module/az.monitor/add-azmetricalertrule) | 나중에 메트릭을 자동으로 모니터링하도록 경고 규칙을 설정합니다. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 모든 중첩 리소스를 포함한 리소스 그룹을 삭제합니다. |
|||

## <a name="next-steps"></a>다음 단계

Azure PowerShell에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/overview)를 참조하세요.

추가 PowerShell 스크립트 샘플은 [Azure PowerShell 스크립트](../powershell-script-content-guide.md)에서 찾을 수 있습니다.
