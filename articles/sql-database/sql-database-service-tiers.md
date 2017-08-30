---
title: "Azure SQL Database 서비스 및 성능 계층 | Microsoft Docs"
description: "단일 데이터베이스에 대한 SQL Database 서비스 계층과 성능 수준을 비교하고 SQL 탄력적 풀을 소개합니다."
keywords: "데이터베이스 옵션, 데이터베이스 성능"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: f5c5c596-cd1e-451f-92a7-b70d4916e974
ms.service: sql-database
ms.custom: DBs & servers
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 06/30/2017
ms.author: carlrab
ms.translationtype: HT
ms.sourcegitcommit: 760543dc3880cb0dbe14070055b528b94cffd36b
ms.openlocfilehash: b25ff5331f119efd44c61808f7d1d5decb226bd6
ms.contentlocale: ko-kr
ms.lasthandoff: 08/10/2017

---
# <a name="what-performance-options-are-available-for-an-azure-sql-database"></a>Azure SQL Database에서 사용할 수 있는 성능 옵션은 무엇입니까?

[Azure SQL Database](sql-database-technical-overview.md)는 단일 및 [풀링된](sql-database-elastic-pool.md) 데이터베이스 모두에 대해 네 개의 서비스 계층, 즉 **기본**, **표준**, **프리미엄** 및 **프리미엄 RS**를 제공합니다. 각 서비스 계층에는 여러 성능 수준([DTU](sql-database-what-is-a-dtu.md)) 및 다양한 워크로드와 데이터 크기를 처리할 수 있는 저장소 옵션이 있습니다. 높은 성능 수준이 높을 수록 점점 더 많은 처리량과 용량을 제공하도록 설계된 추가 계산 및 저장소 리소스를 제공합니다. 가동 중지 시간 없이 서비스 계층, 성능 수준 및 저장소를 동적으로 변경할 수 있습니다. 
- **기본**, **표준** 및 **프리미엄** 서비스 계층에는 모두 99.99%의 작동 시간 SLA, 유연한 비즈니스 연속성 옵션, 보안 기능 및 시간별 요금 청구가 있습니다. 
- **프리미엄 RS** 계층은 다른 서비스 계층의 데이터베이스보다 더 적은 중복 복사본 수로 실행되므로 낮은 SLA 수준의 프리미엄 계층과 동일한 성능 수준을 제공합니다. 따라서 서비스 오류가 발생하면 최대 5분 간격으로 백업에서 데이터베이스를 복구할 수 있어야 합니다.

> [!IMPORTANT]
> Azure SQL 데이터베이스는 보장된 리소스 집합을 가지고 있으며 데이터베이스의 예상 성능 특징은 Azure 내 다른 데이터베이스의 영향을 받지 않습니다. 

## <a name="choosing-a-service-tier"></a>서비스 계층 선택
다음 표에서 다양한 응용 프로그램 워크로드에 가장 적합한 계층의 예제를 제공합니다.

| 서비스 계층 | 대상 워크로드 |
| :--- | --- |
| **Basic** | 일반적으로 지정된 시기에 활성 작업 한 개를 지원하는 작은 데이터베이스에 가장 적합합니다. 예를 들어 개발 또는 시험, 또는 자주 사용하지 않는 소규모 응용 프로그램에 사용되는 데이터베이스가 이에 해당합니다. |
| **Standard** |중저 IO 성능 요구 사항의 클라우드 응용 프로그램에 적합한 옵션으로, 여러 개의 동시 쿼리를 지원합니다. 예를 들어 작업 그룹 또는 웹 응용 프로그램이 이 계층에 적합한 예입니다. |
| **Premium** | 높은 IO 성능 요구 사항의 높은 트랜잭션 볼륨용으로 설계되었으며, 많은 동시 사용자를 지원합니다. 예를 들어 업무 필수 응용 프로그램을 지원하는 데이터베이스가 이 계층에 적합합니다. |
| **Premium RS** | IO 집약적 작업을 진행하지만 최고 가용성을 보장할 필요는 없는 경우를 위해 설계되었습니다. 예제에는 데이터베이스가 레코드 시스템이 아닌 고성능 작업 또는 분석 작업 테스트가 포함됩니다. |
|||

특정 [성능 수준](sql-database-service-tiers.md#single-database-service-tiers-and-performance-levels)의 서비스 계층 내에서 전용 리소스를 사용하여 단일 데이터베이스를 만들거나 [SQL 탄력적 풀](sql-database-service-tiers.md#elastic-pool-service-tiers-and-performance-in-edtus) 내에 데이터베이스를 만들 수도 있습니다. SQL 탄력적 풀에서 계산 및 저장소 리소스는 단일 논리 서버 내의 여러 데이터베이스 간에 공유됩니다. 

단일 데이터베이스에 사용할 수 있는 리소스는 DTU(데이터베이스 트랜잭션 단위)로 표현되며, SQL 탄력적 풀에 사용할 수 있는 리소스는 eDTU(탄력적 데이터베이스 트랜잭션 단위)로 표현됩니다. DTU 및 eDTU에 대한 자세한 내용은 [DTU 및 eDTU란?](sql-database-what-is-a-dtu.md)을 참조하세요.

서비스 계층을 결정하려면 필요한 최소 데이터베이스 기능을 결정하고 시작합니다.

| **서비스 계층 기능** | **Basic** | **Standard** | **Premium** | **Premium RS**|
| :-- | --: | --: | --: | --: |
| 최대 단일 데이터베이스 크기 | 2 GB | 250GB | 4TB*  | 500GB  |
| 최대 탄력적 풀 크기 | 156GB | 2.9TB | 4TB* | 750GB |
| 탄력적 풀의 최대 데이터베이스 크기 | 2 GB | 250GB | 500GB | 500GB |
| 풀당 최대 데이터베이스 수 | 500  | 500 | 100 | 100 |
| 최대 단일 데이터베이스 DTU | 5 | 100 | 4000 | 1000 |
| 탄력적 풀의 데이터베이스당 최대 DTU | 5 | 3000 | 4000 | 1000 |
| 데이터베이스 백업 보존 기간 | 7 일 | 35일 | 35일 | 35일 |
||||||

> [!IMPORTANT]
> 최대 4TB의 저장소는 현재 미국 동부 2, 미국 서부, 미국 버지니아 주 정부, 유럽 서부, 독일 중부, 동남 아시아, 일본 동부, 오스트레일리아 동부, 캐나다 중부 및 캐나다 동부 지역에서 사용할 수 있습니다. [현재 4TB 제한 사항](sql-database-service-tiers.md#current-limitations-of-p11-and-p15-databases-with-4-tb-maxsize)을 참조하세요.
>

적절한 서비스 계층을 결정했으면 데이터베이스의 성능 수준(DTU 수)과 저장소 크기를 결정할 준비가 된 것입니다. 

- **표준** 계층의 S2 및 S3 성능 수준이 종종 좋은 시작점이 됩니다. 
- 높은 CPU 또는 IO 요구 사항이 있는 데이터베이스의 경우 **프리미엄** 계층의 성능 수준이 적합한 시작점이 됩니다. 
- **프리미엄** 계층은 **표준** 계층의 최고 성능 수준과 비교하여 더 많은 CPU를 제공하고 10배 이상의 더 많은 IO로 시작합니다.
- **프리미엄 RS** 계층은 낮은 SLA 수준의 저렴한 비용으로 **프리미엄** 계층의 성능을 제공합니다.

> [!IMPORTANT]
> 데이터베이스를 SQL 탄력적 풀로 그룹화하여 계산 및 저장소 리소스를 공유하는 방법에 대한 자세한 내용은 [SQL 탄력적 풀](sql-database-elastic-pool.md) 항목을 검토하세요. 이 항목의 나머지 부분에서는 단일 데이터베이스의 서비스 계층과 성능 수준에 중점을 두고 있습니다.
>

## <a name="single-database-service-tiers-and-performance-levels"></a>단일 데이터베이스 서비스 계층 및 성능 수준
단일 데이터베이스의 경우 각 서비스 계층 내에는 여러 성능 수준과 저장소 용량이 있습니다. 

[!INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

## <a name="scaling-up-or-scaling-down-a-single-database"></a>단일 데이터베이스 확장 및 축소

처음으로 서비스 계층 및 성능 수준을 선택한 후에 단일 데이터베이스를 실제 환경에 따라 동적으로 확장 또는 축소할 수 있습니다.  

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]
>

서비스 계층 및/또는 데이터베이스의 성능 수준을 변경하게 되면 새 성능 수준에서 원본 데이터베이스의 복제본을 만들고 연결을 복제본으로 전환합니다. 이 프로세스 중에 데이터가 손실되지는 않지만 복제본으로 전환할 경우 잠깐 동안 데이터베이스에 대한 연결이 비활성화되므로 비행의 일부 트랜잭션이 롤백될 수 있습니다. 전환 시간은 다양하지만, 일반적으로 30초보다 작은 4초 미만이 해당 시간의 99%를 차지하고 있습니다. 연결을 사용할 수 없는 짧은 시간에 많은 수의 항공편 트랙잭션이 있으면 전환 시간이 더 길어질 수 있습니다.  

전체 확장 프로세스 기간은 변경 전후 데이터베이스의 크기 및 서비스 계층에 따라 달라집니다. 예를 들어 표준 서비스 계층 내에서 변경되고 있는 250GB 데이터베이스는 6시간 내에 완료되어야 합니다. 프리미엄 서비스 계층 내의 성능 수준을 변경하고 있는 동일한 크기의 데이터베이스의 경우 3시간 이내에 완료되어야 합니다.

> [!TIP]
> 진행 중인 SQL Database 크기 조정 작업의 상태를 확인하려면 ```select * from sys.dm_operation_status``` 쿼리를 사용할 수 있습니다.
>

* 상위 서비스 계층이나 성능 수준으로 업그레이드하는 경우 더 큰 최대 크기를 명시적으로 지정하지 않는 한 최대 데이터베이스 크기는 증가하지 않습니다.
* 데이터베이스를 다운그레이드하려면 데이터베이스가 대상 서비스 계층의 최대 허용 크기보다 작아야 합니다. 
* [지역에서 복제](sql-database-geo-replication-portal.md)를 사용할 수 있는 데이터베이스를 업그레이드하는 경우 주 데이터베이스를 업그레이드하기 전에 먼저 해당 보조 데이터베이스를 원하는 성능 계층으로 업그레이드합니다(일반 지침). 다른 버전으로 업그레이드할 때 보조 데이터베이스를 먼저 업그레이드해야 합니다. 
* [지역에서 복제](sql-database-geo-replication-portal.md)를 사용할 수 있는 데이터베이스를 다운그레이드하는 경우 보조 데이터베이스를 다운그레이드하기 전에 먼저 해당 주 데이터베이스를 원하는 성능 계층으로 다운그레이드합니다(일반 지침). 다른 버전으로 다운그레이드할 때 주 데이터베이스를 먼저 다운그레이드해야 합니다. 

* 복원 서비스는 여러 서비스 계층에서 서로 다르게 제공됩니다. **기본** 계층으로 다운그레이드하는 경우 백업 보존 기간이 더 짧아집니다. [Azure SQL Database 백업](sql-database-automated-backups.md)을 참조하세요.
* 데이터베이스의 새로운 속성은 변경이 완료될 때까지 적용되지 않습니다.


## <a name="current-limitations-of-p11-and-p15-databases-with-4-tb-maxsize"></a>4TB 최대 크기의 P11 및 P15 데이터베이스의 현재 제한 사항

일부 지역에서는 P11 및 P15 데이터베이스의 최대 크기인 4TB를 지원합니다(앞에서 설명한 대로). 다음 고려 사항 및 제한 사항은 최대 크기가 4TB인 P11 및 P15 데이터베이스에 적용됩니다.

- (4TB 또는 4,096GB의 값을 사용하여) 데이터베이스를 만들 때 4TB 최대 크기 옵션을 선택하면 지원되지 않는 지역에서 데이터베이스를 프로비전할 때 오류가 발생하여 만들기 명령이 실패합니다.
- 지원되는 하위 지역 중 하나에 있는 기존 P11 및 P15 데이터베이스의 경우 저장소 최대 크기를 4TB로 늘릴 수 있습니다. [SELECT DATABASEPROPERTYEX](https://msdn.microsoft.com/library/ms186823.aspx)를 사용하거나 Azure Portal의 데이터베이스 크기를 검사하여 확인할 수 있습니다. 기존 P11 또는 P15 데이터베이스 업그레이드는 서버 수준 보안 주체 로그인 또는 dbmanager 데이터베이스 역할의 멤버에 의해서만 수행될 수 있습니다. 
- 지원되는 지역에서 업그레이드 작업을 실행하면 구성이 즉시 업데이트됩니다. 업그레이드 프로세스 동안 데이터베이스는 온라인 상태로 유지됩니다. 그러나 실제 데이터베이스 파일이 새 최대 크기로 업그레이드될 때까지 4TB의 전체 저장소를 사용할 수 없습니다. 필요한 시간의 길이는 업그레이드 중인 데이터베이스 크기에 따라 달라집니다.  
- P11 또는 P15 데이터베이스를 만들거나 업데이트하는 경우 1TB와 4TB 최대 크기 중에서만 선택할 수 있습니다. 중간 저장소 크기는 현재 지원되지 않습니다. P11/P15를 만들 때 1TB의 기본 저장소 옵션이 미리 선택됩니다. 지원되는 지역 중 하나에 있는 데이터베이스의 경우 새 또는 기존 단일 데이터베이스의 저장소를 최대 4TB 크기로 늘릴 수 있습니다. 다른 모든 지역의 경우 최대 크기를 1TB 이상으로 늘릴 수 없습니다. 4TB의 포함된 저장소를 선택하면 가격이 변경되지 않습니다.
- 사용되는 실제 저장소가 1TB 미만인 경우에도 4TB 데이터베이스 최대 크기를 1TB로 변경할 수 없습니다. 따라서 성능 계층의 나머지 부분에 대한 추가 저장소 옵션을 제공할 때까지 P11-4TB/P15-4TB를 P11-1TB/P15-1TB 또는 더 낮은 성능 계층(예: P1-P6)으로 다운그레이드할 수 없습니다. 이 제한 사항은 지정 시간, 지역 복원, 장기 백업 보존 및 데이터베이스 복사를 포함한 복원 및 복사 시나리오에도 적용됩니다. 데이터베이스가 4TB 옵션으로 구성되면 이 데이터베이스의 모든 복원 작업은 4TB 최대 크기의 P11/P15이어야 합니다.
- 지원되지 않는 지역에서 P11/P15 데이터베이스를 만들거나 업그레이드하는 경우 만들기 또는 업그레이드 작업은 다음 오류 메시지를 표시하며 실패합니다. **최대 4TB의 저장소가 있는 P11 및 P15 데이터베이스는 미국 동부 2, 미국 서부, 미국 버지니아 주 정부, 유럽 서부, 독일 중부, 동남 아시아, 일본 동부, 오스트레일리아 동부, 캐나다 중부 및 캐나다 동부에서 사용할 수 있습니다.**
- 활성 지역 복제 시나리오의 경우:
   - 지역에서 복제 관계 설정: 주 데이터베이스가 P11 또는 P15인 경우 보조 데이터베이스는 P11 또는 P15이어야 합니다. 낮은 성능 계층은 4TB를 지원하지 않으므로 보조 데이터베이스로 거부됩니다.
   - 지역에서 복제 관계에서 주 데이터베이스 업그레이드: 주 데이터베이스에서 최대 크기를 4TB로 변경하면 보조 데이터베이스에서도 동일한 변경 사항이 트리거됩니다. 변경 내용을 적용하려면 주 데이터베이스에서 두 가지 업그레이드에 성공해야 합니다. 4TB 옵션에 대한 지역 제한 사항이 적용됩니다(위 참조). 4TB를 지원하지 않는 지역에 보조 데이터베이스가 있는 경우 주 데이터베이스는 업그레이드되지 않습니다.
- P11-4TB/P15-4TB 데이터베이스 로드에 대한 Import/Export 서비스 사용은 지원되지 않습니다. SqlPackage.exe를 사용하여 데이터를 [가져오기](sql-database-import.md) 및 [내보내기](sql-database-export.md)합니다.

## <a name="manage-single-database-service-tiers-and-performance-levels-using-the-azure-portal"></a>Azure Portal을 사용하여 단일 데이터베이스 서비스 계층 및 성능 수준 관리

Azure Portal을 사용하여 새 또는 기존 Azure SQL 데이터베이스의 서비스 계층, 성능 수준 또는 저장소 용량을 설정하거나 변경하려면 다음 스크린샷과 같이 **가격 책정 계층(DTU 조정)**을 클릭하여 데이터베이스에 대한 **성능 구성** 창을 엽니다. 

- 워크로드에 대한 서비스 계층을 선택하여 서비스 계층을 설정하거나 변경합니다. 
- **DTU** 슬라이더를 사용하여 서비스 계층 내의 성능 수준(**DTU**)을 설정하거나 변경합니다.
- **저장소** 슬라이더를 사용하여 성능 수준의 저장소 용량을 설정하거나 변경합니다. 

  ![서비스 계층 및 성능 수준 구성](./media/sql-database-service-tiers/service-tier-performance-level.png)

> [!IMPORTANT]
> P11 또는 P15 서비스 계층을 선택하는 경우 [4TB 최대 크기의 P11 및 P15 데이터베이스의 현재 제한 사항](sql-database-service-tiers.md#current-limitations-of-p11-and-p15-databases-with-4-tb-maxsize)을 검토하세요.
>

## <a name="manage-single-database-service-tiers-and-performance-levels-using-powershell"></a>PowerShell을 사용하여 단일 데이터베이스 서비스 계층 및 성능 수준 관리

PowerShell을 사용하여 Azure SQL 데이터베이스 서비스 계층, 성능 수준 및 저장소 용량을 설정하거나 변경하려면 다음 PowerShell cmdlet을 사용합니다. PowerShell을 설치하거나 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-azurerm-ps)를 참조하세요. 

| Cmdlet | 설명 |
| --- | --- |
|[New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase)|데이터베이스 만들기 |
|[Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase)|하나 이상의 데이터베이스 가져오기|
|[Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase)|데이터베이스의 속성을 설정하거나 기존 데이터베이스를 탄력적 풀로 이동합니다.|


> [!TIP]
> 데이터베이스의 성능 메트릭을 모니터링하고 성능 수준을 더 높이고 성능 메트릭 중 하나에 대한 경고 규칙을 만드는 PowerShell 예제 스크립트는 [PowerShell을 사용하여 단일 SQL 데이터베이스 모니터링 및 크기 조정](scripts/sql-database-monitor-and-scale-database-powershell.md)을 참조하세요.

## <a name="manage-single-database-service-tiers-and-performance-levels-using-the-azure-cli"></a>Azure CLI를 사용하여 단일 데이터베이스 서비스 계층 및 성능 수준 관리

Azure CLI를 사용하여 Azure SQL 데이터베이스 서비스 계층, 성능 수준 및 저장소 용량을 설정하거나 변경하려면 다음 [Azure CLI SQL Database](/cli/azure/sql/db) 명령을 사용합니다. [Cloud Shell](/azure/cloud-shell/overview)을 사용하여 CLI 브라우저에서 실행하거나 macOS, Linux 또는 Windows에서 [설치](/cli/azure/install-azure-cli)합니다. SQL 탄력적 풀 만들기 및 관리에 대해서는 [탄력적 풀](sql-database-elastic-pool.md)을 참조하세요.

| Cmdlet | 설명 |
| --- | --- |
|[az sql db create](/cli/azure/sql/db#create) |데이터베이스 만들기|
|[az sql db list](/cli/azure/sql/db#list)|서버의 모든 데이터베이스 및 데이터 웨어하우스 또는 탄력적 풀의 모든 데이터베이스 나열|
|[az sql db list-editions](/cli/azure/sql/db#list-editions)|사용 가능한 서비스 목표 및 저장소 용량 제한 나열|
|[az sql db list-usages](/cli/azure/sql/db#list-usages)|데이터베이스 사용 정보 반환|
|[az sql db show](/cli/azure/sql/db#show)|데이터베이스 또는 데이터 웨어하우스 가져오기|
|[az sql db update](/cli/azure/sql/db#update)|데이터베이스 업데이트|

> [!TIP]
> 데이터베이스의 크기 정보를 쿼리한 후 단일 Azure SQL Database를 다른 성능 수준으로 크기 조정하는 Azure CLI 예제 스크립트는 [CLI를 사용하여 단일 SQL Database 모니터링 및 크기 조정](scripts/sql-database-monitor-and-scale-database-cli.md)을 참조하세요.
>

## <a name="manage-single-database-service-tiers-and-performance-levels-using-transact-sql"></a>Transact-SQL을 사용하여 단일 데이터베이스 서비스 계층 및 성능 수준 관리

Transact-SQL을 사용하여 Azure SQL 데이터베이스 서비스 계층, 성능 수준 및 저장소 용량을 설정하거나 변경하려면 다음 T-SQL 명령을 사용합니다. Azure Portal, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code](https://code.visualstudio.com/docs) 또는 Azure SQL Database 서버에 연결하여 Transact-SQL 명령을 전달할 수 있는 다른 프로그램을 사용하여 이러한 명령을 실행할 수 있습니다 . 

| 명령 | 설명 |
| --- | --- |
|[CREATE DATABASE (Azure SQL Database)](/sql/t-sql/statements/create-database-azure-sql-database)|새 데이터베이스를 만듭니다. 새 데이터베이스를 만들려면 master 데이터베이스에 연결되어 있어야 합니다.|
| [ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) |Azure SQL 데이터베이스를 수정합니다. |
|[sys.database_service_objectives (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Azure SQL Database 또는 Azure SQL Data Warehouse가 있는 경우 버전(서비스 계층), 서비스 목표(가격 책정 계층) 및 탄력적 풀 이름을 반환합니다. Azure SQL Database 서버의 마스터 데이터베이스에 로그인하면 모든 데이터베이스에 대한 정보를 반환합니다. Azure SQL Data Warehouse의 경우 마스터 데이터베이스에 연결되어 있어야 합니다.|
|[sys.database_usage(Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-usage-azure-sql-database)|Azure SQL Database 서버의 데이터베이스 수, 유형 및 기간을 나열합니다.|

다음 예제에서는 ALTER DATABASE 명령을 사용하여 변경되는 최대 크기를 보여 줍니다.

 ```sql
ALTER DATABASE <myDatabaseName> 
   MODIFY (MAXSIZE = 4096 GB);
```

## <a name="manage-single-databases-using-the-rest-api"></a>REST API를 사용하여 단일 데이터베이스 관리

REST API를 사용하여 Azure SQL 데이터베이스 서비스 계층, 성능 수준 및 저장소 용량을 설정하거나 변경하려면 [Azure SQL Database REST API](/rest/api/sql/)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [DTU](sql-database-what-is-a-dtu.md)에 대해 자세히 알아봅니다.
* DTU 사용 모니터링에 대한 자세한 내용은 [모니터링 및 성능 튜닝](sql-database-troubleshoot-performance.md)을 참조하세요.


