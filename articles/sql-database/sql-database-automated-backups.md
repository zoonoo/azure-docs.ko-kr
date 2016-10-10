<properties
   pageTitle="SQL Database 백업 | Microsoft Azure"
   description="Azure SQL Database를 이전 지정 시간으로 롤백하거나 지리적 영역(최대 35일)의 새 데이터베이스에 데이터베이스를 복사할 수 있도록 하는 SQL Database 기본 제공 데이터베이스 백업에 대해 알아봅니다."
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/26/2016"
   ms.author="carlrab"/>

# SQL Database 백업

SQL Database는 서비스 제공 사항의 일환으로 선택하지 않아도 추가 비용 없이 자동으로 데이터베이스 백업을 생성합니다. 데이터베이스 백업을 사용하여 이전 시점으로 데이터베이스를 복원하세요. 이 문서에서는 SQL Database의 데이터베이스 백업 기능에 대한 세부 사항을 설명합니다.

## 데이터베이스 백업이란?  

데이터베이스 백업은 특정 시점에서 데이터베이스의 상태에 대한 정보를 저장하는 파일입니다. SQL Database는 [전체](https://msdn.microsoft.com/library/ms186289.aspx), [차등](https://msdn.microsoft.com/library/ms175526.aspx), [트랜잭션 로그](https://msdn.microsoft.com/library/ms191429.aspx) 백업을 생성합니다. 특정 시점으로 데이터베이스를 복원할 때, 복원이 필요한 백업이 무엇인지를 서비스가 파악합니다.

데이터베이스 백업은 실수로 손상되거나 삭제되지 않도록 데이터를 보호해 주기 때문에 비즈니스 연속성 및 재해 복구 전략의 필수적인 부분입니다.. 자세한 내용은 [비즈니스 연속성 개요](sql-database-business-continuity.md)를 참조하세요.

## 지역 중복 저장소

SQL Database는 읽기 권한(RA-GRS)이 있는 지역 중복 저장소 계정에 데이터베이스 백업 파일을 저장합니다. Azure Storage RA-GRS 기능은 [쌍을 이루는 데이터 센터](../best-practices-availability-paired-regions.md)에 백업을 복제합니다. 이러한 지역에서 복제는 주 데이터베이스 지역의 데이터베이스 백업에 액세스할 수 없는 경우에 데이터베이스를 복원할 수 있도록 해 줍니다. 다음 예제에서, SQL Database는 미국 동부에 데이터베이스 백업을 생성하고 RA-GRS 계정으로 저장합니다. 그런 다음 Azure Storage는 미국 서부 지역의 쌍을 이루는 데이터 센터에 백업을 지역 복제합니다.

![geo-restore](./media/sql-database-geo-restore/geo-restore-1.png)

>[AZURE.NOTE] Azure Storage에서 *복제*라는 용어는 한 위치에서 다른 위치로 파일을 복사하는 것을 말합니다. SQL의 *데이터베이스 복제*는 주 데이터베이스와 동기화된 다수의 보조 데이터베이스를 유지하는 것을 말합니다.

다음에 대한 자세한 정보:
- 지역 중복 저장소는 [Azure Storage 복제](../storage/storage-redundancy.md)를 참조하세요.
- RA-GRS 저장소는 [읽기 액세스 지역 중복 저장소](../storage/storage-redundancy.md#read-access-geo-redundant-storage)를 참조하세요.

## 데이터베이스 백업 비용

Microsoft Azure SQL Database는 추가 비용 없이 최대 프로비전된 데이터베이스 저장소의 최대 200%가 백업 저장소로 제공됩니다. 예를 들어, 프로비전된 DB의 크기가 250GB인 Standard DB 인스턴스가 있으면 추가 비용 없이 500GB의 백업 저장소를 갖습니다. 제공된 백업 저장소를 데이터베이스가 초과하면, Azure 지원 팀에 문의하여 보존 기간을 줄이도록 선택할 수 있습니다. 또 다른 옵션은 표준 RA-GRS(읽기 액세스 지리 중복 저장소) 요금이 청구되는 추가 백업 저장소에 대한 비용을 지불하는 것입니다.

## 데이터베이스 백업 일정

모든 기본, 표준 및 프리미엄 데이터베이스는 자동 백업에 의해 보호됩니다. 매주 전체 데이터베이스 백업을 수행하고, 매 시간 차등 데이터베이스 백업을 수행하고, 5분마다 트랜잭션 로그 백업을 수행합니다. 첫 번째 전체 백업은 데이터베이스를 만든 후에 즉시 예약됩니다. 일반적으로 30분 내에 완료되지만 데이터베이스의 크기가 상당히 큰 경우에는 더 오래 걸릴 수 있습니다. 예를 들어, 복원된 데이터베이스 또는 데이터베이스 사본에 대한 초기 백업은 더 오래 걸릴 수 있습니다. 첫 번째 전체 백업 후에 모든 향후 백업은 자동으로 예약되며 백그라운드에서 자동으로 관리됩니다. 전체 및 [차등](https://msdn.microsoft.com/library/ms175526.aspx) 데이터베이스 백업의 정확한 타이밍은 전반적인 시스템 워크로드를 감안하여 결정됩니다.

## 데이터베이스 백업 보존 기간

각각의 SQL Database 백업은 기본의 경우 7일, 표준의 경우 35일, 프리미엄의 경우 35일 동안 보존됩니다. 각 서비스 계층에서 사용할 수 있는 기능에 대한 자세한 내용은 [서비스 계층](sql-database-service-tiers.md)을 참조하세요.

### 서비스 계층을 다운그레이드/업그레이드할 때 복원 지점 보존 기간은 어떻게 변경되나요?

낮은 성능 계층으로 다운그레이드 하면 복원 지점의 보존 기간은 현재 데이터베이스 성능 계층의 보존 기간으로 즉시 단축됩니다. 서비스 계층을 업그레이드 한 경우 보존 기간은 데이터베이스를 업그레이드한 후에만 확장이 시작됩니다. 예를 들어, 데이터베이스가 기본으로 다운그레이드되면, 보존 기간이 35일에서 7일로 변경됩니다. 7일보다 오래된 모든 복원 지점은 즉시 사용할 수 없게 됩니다. 데이터베이스 표준 또는 프리미엄을 업그레이드하는 경우, 보존 기간은 7일부터 시작하여 35일에 도달할 때까지 증가합니다.

### 삭제된 데이터베이스의 보존 기간은 얼마나 깁니까? 

보존 기간은 데이터베이스가 존재했던 서비스 계층 또는 데이터베이스 존재 일 수 중 더 적은 일 수에 의해 결정됩니다.

> [AZURE.IMPORTANT] Azure SQL Database 서버 인스턴스를 삭제하면 이 인스턴스에 속하는 모든 데이터베이스도 삭제되고 복구될 수 없습니다. 삭제된 서버는 복원할 수 없습니다.


## 데이터베이스 백업에 대한 일반적인 사용

데이터베이스 백업에 대한 주요 용도는 보존 기간 내 특정 시점으로 데이터베이스를 복원할 수 있게 되는 것입니다. 데이터베이스 백업을 사용하면 특정 시점으로 데이터베이스를 복원하거나, 삭제된 데이터베이스를 데이터베이스가 삭제된 시점으로 복원하거나, 다른 지역에서 데이터베이스를 복원할 수 있습니다.

- 데이터베이스 복원에 대해 알아보려면 [자동화된 백업에서 데이터베이스 복원](sql-database-recovery-using-backups.md)을 참조하세요.

데이터베이스 백업을 사용하여 모든 지역의 논리적 SQL Server에 데이터베이스를 복사할 수 있습니다. 사본은 트랜잭션 측면에서 현재 SQL Database와 일치합니다.

- 데이터베이스 복사에 대해 알아보려면 [데이터베이스 복사](sql-database-copy.md)를 참조하세요.

[BACPAC 파일로 내보낼](sql-database-export.md) 데이터베이스 사본을 만들면 보존 기간 이상으로 자동화된 백업을 보관할 수도 있습니다. BACPAC 파일이 있으면, 장기 저장소에 보관하여 보존 기간 이상으로 저장할 수 있습니다. 또는 BACPAC를 사용하여 데이터베이스 사본을 온-프레미스나 Azure VM(가상 컴퓨터)의 SQL Server로 전송합니다.

- 데이터베이스 백업 보관에 대해 알아보려면 [데이터베이스 사본](sql-database-copy.md)을 참조하세요.


## 관련된 항목

### 시나리오

- 비즈니스 연속성을 대략적으로 이해하려면 [비즈니스 연속성 개요](sql-database-business-continuity.md)를 참조하세요.

### 기능

자세한 정보:

- 데이터베이스 백업 복원은 [서비스에서 시작한 백업에서 데이터베이스 복원](sql-database-recovery-using-backups.md)을 참조하세요.
- 데이터베이스 백업 보관은 [데이터베이스 사본](sql-database-copy.md)을 참조하세요.
- 빠른 복구 옵션은 [활성 지역 복제](sql-database-geo-replication-overview.md)를 참조하세요.

<!-- ### Tasks -->

<!-- ### Tutorials -->

<!---HONumber=AcomDC_0928_2016-->