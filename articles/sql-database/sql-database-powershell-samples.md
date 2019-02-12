---
title: SQL Database에 대한 Azure PowerShell 스크립트 예제 | Microsoft Docs
description: Azure SQL Database 서버, 탄력적 풀, 데이터베이스 및 방화벽을 만들고 관리하는 데 유용한 Azure PowerShell 스크립트 예제입니다.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 02/01/2019
ms.openlocfilehash: 07e530a30898e57916b91632c4bf49d43d69471a
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2019
ms.locfileid: "55564853"
---
# <a name="azure-powershell-samples-for-azure-sql-database"></a>Azure SQL Database에 대한 Azure PowerShell 샘플

Azure SQL Database를 사용하면 Azure PowerShell을 사용하여 데이터베이스, 인스턴스 및 풀을 구성할 수 있습니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

PowerShell을 로컬로 설치하고 사용하도록 선택하는 경우 이 자습서에서는 Azure PowerShell 모듈 버전이 5.7.0 이상이어야 합니다. `Get-Module -ListAvailable AzureRM`을 실행하여 버전을 찾습니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzureRmAccount`를 실행하여 Azure와 연결해야 합니다.

## <a name="single-database-and-elastic-pools"></a>단일 데이터베이스 및 탄력적 풀

다음 표에는 Azure SQL Database의 Azure PowerShell 샘플 스크립트에 대한 링크가 나와 있습니다.

| |  |
|---|---|
|**단일 데이터베이스 및 탄력적 풀 만들기 및 구성**||
| [단일 데이터베이스 만들기 및 데이터베이스 서버 방화벽 규칙 구성](scripts/sql-database-create-and-configure-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 이 PowerShell 스크립트는 단일 Azure SQL Database를 만들고 서버 수준 방화벽 규칙을 구성합니다. |
| [탄력적 풀 만들기 및 풀된 데이터베이스 이동](scripts/sql-database-move-database-between-pools-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 이 PowerShell 스크립트는 Azure SQL Database 탄력적 풀을 만들고, 풀링된 데이터베이스를 이동하며, 계산 크기를 변경합니다.|
|**지역에서 복제 및 장애 조치(failover) 구성**||
| [활성 지역 복제를 사용하여 단일 데이터베이스 구성 및 장애 조치(Failover)](scripts/sql-database-setup-geodr-and-failover-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 이 PowerShell 스크립트는 단일 Azure SQL Database에 대해 활성 지역 복제를 구성하고 보조 복제본으로 장애 조치(failover)합니다. |
| [활성 지역 복제를 사용하여 풀된 데이터베이스 구성 및 장애 조치(failover)](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 이 PowerShell 스크립트는 SQL 탄력적 풀의 단일 Azure SQL Database에 대해 활성 지역 복제를 구성하고 보조 복제본으로 장애 조치(failover)합니다. |
| [단일 데이터베이스에 대한 장애 조치(failover) 그룹 구성 및 장애 조치(failover)](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 이 PowerShell 스크립트는 Azure SQL Database 서버 인스턴스의 장애 조치(failover) 그룹을 구성하고, 장애 조치(failover) 그룹에 데이터베이스를 추가하고, 보조 서버로 장애 조치(failover)합니다. |
|**단일 데이터베이스 및 탄력적 풀 크기 조정**||
| [단일 데이터베이스 크기 조정](scripts/sql-database-monitor-and-scale-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 이 PowerShell 스크립트는 Azure SQL Database의 성능 메트릭을 모니터링하고, 더 큰 계산 크기로 조정하고, 성능 메트릭 중 하나에 대한 경고 규칙을 만듭니다. |
| [탄력적 풀 크기 조정](scripts/sql-database-monitor-and-scale-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 이 PowerShell 스크립트는 Azure SQL Database 탄력적 풀의 성능 메트릭을 모니터링하고, 더 큰 계산 크기로 조정하고, 성능 메트릭 중 하나에 대한 경고 규칙을 만듭니다.  |
| **감사 및 위협 감지** |
| [감사 및 위협 감지 구성](scripts/sql-database-auditing-and-threat-detection-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 이 PowerShell 스크립트는 Azure SQL Database에 대한 감사 및 위협 감지 정책을 구성합니다. |
| **데이터베이스 복원, 복사 및 가져오기**||
| [데이터베이스 복원](scripts/sql-database-restore-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 이 PowerShell 스크립트는 지역 중복 백업에서 Azure SQL Database를 복원하고 삭제된 Azure SQL Database를 최신 백업으로 복원합니다. |
| [새 서버에 데이터베이스 복사](scripts/sql-database-copy-database-to-new-server-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 이 PowerShell 스크립트는 새 Azure SQL Server에 기존 Azure SQL Database의 복사본을 만듭니다. |
| [bacpac 파일에서 데이터베이스 가져오기](scripts/sql-database-import-from-bacpac-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 이 PowerShell 스크립트는 bacpac 파일에서 Azure SQL Server로 데이터베이스를 가져옵니다. |
| **데이터베이스 간 데이터 동기화**||
| [SQL Database 간 데이터 동기화](scripts/sql-database-sync-data-between-sql-databases.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 이 PowerShell 스크립트는 여러 Azure SQL Database 간에 동기화를 수행하도록 데이터 동기화를 구성합니다. |
| [SQL Database 및 SQL Server 온-프레미스 간의 데이터 동기화](scripts/sql-database-sync-data-between-azure-onprem.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 이 PowerShell 스크립트는 Azure SQL Database와 SQL Server 온-프레미스 데이터베이스 간에 동기화를 수행하도록 데이터 동기화를 구성합니다. |
| [SQL 데이터 동기화 동기화 스키마 업데이트](scripts/sql-database-sync-update-schema.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 이 PowerShell 스크립트는 데이터 동기화 동기화 스키마에서 항목을 추가하거나 제거합니다. |
|||

[단일 데이터베이스 Azure PowerShell API](sql-database-single-databases-manage.md#powershell-manage-sql-database-servers-and-single-databases)에 대해 자세히 알아봅니다.

## <a name="managed-instance"></a>관리되는 인스턴스

다음 표에는 Azure SQL Database - Managed Instance의 Azure PowerShell 샘플 스크립트에 대한 링크가 나와 있습니다.

| |  |
|---|---|
|**관리되는 인스턴스 만들기 및 구성**||
| [Managed Instance 만들기 및 관리](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/27/quick-start-script-create-azure-sql-managed-instance-using-powershell/) | 이러한 PowerShell 스크립트는 Azure PowerShell을 사용하여 Managed Instance를 만들고 관리하는 방법을 보여 줍니다. |
| [Azure Resource Manager 템플릿을 사용하여 Managed Instance 만들기 및 관리](scripts/sql-managed-instance-create-powershell-azure-resource-manager-template.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 이 PowerShell 스크립트는 Azure PowerShell 및 Azure Resource Manager를 사용하여 Managed Instance를 만들고 관리하는 방법을 보여줍니다.|
| **TDE(투명한 데이터 암호화) 구성**||
| [Azure Key Vault에서 사용자 고유 키를 사용하여 Managed Instance에서 투명한 데이터 암호화 관리](scripts/transparent-data-encryption-byok-sql-managed-instance-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 이 PowerShell 스크립트는 Azure Key Vault의 키를 사용하여 Azure SQL Managed Instance에 대한 Bring Your Own Key 시나리오에서 TDE(투명한 데이터 암호화)를 구성합니다.|
|||

[Managed Instance Azure PowerShell API](sql-database-managed-instance-create-manage.md#powershell-create-and-manage-managed-instances)에 대해 자세히 알아봅니다.
