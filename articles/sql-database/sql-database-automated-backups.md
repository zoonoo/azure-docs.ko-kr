<properties
   pageTitle="SQL Database에 대한 자세한 정보 | Microsoft Azure" 
   description="Azure SQL Database를 이전 지정 시간으로 복원하거나 지리적 영역(최대 35일)의 새 데이터베이스에 데이터베이스를 복사할 수 있도록 하는 SQL Database 기본 제공 데이터베이스 백업에 대해 알아봅니다."
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




# <a name="learn-about-sql-database-backups"></a>SQL Database 백업에 대한 자세한 정보


SQL Database는 5분마다 로컬 데이터베이스 백업을 만들고 Azure 읽기 액세스 RA-GRS(지역 중복 저장소)를 사용하여 데이터베이스 백업의 일부를 다른 지역에 복사합니다. 이러한 백업은 **추가 비용 없이 자동으로 수행**됩니다. 로컬 데이터베이스 백업을 사용하여 같은 서버의 [지정 시간으로 데이터베이스를 복원](sql-database-point-in-time-restore-portal.md)합니다. 지역 중복 백업을 사용하여 [다른 지역에 데이터베이스를 복원](sql-database-geo-restore-portal.md)합니다.  

>[AZURE.NOTE] 로컬 백업과 지역 중복 백업 모두 자동으로 수행됩니다. 사용자는 어떤 작업도 수행할 필요가 없으며 추가 비용도 없습니다. 

다음 다이어그램의 SQL Database는 미국 동부 지역에서 실행 중입니다. 그리고 분마다 데이터베이스 백업을 만들어서 Azure 읽기 액세스 RA-GRS(지역 중복 저장소)에 로컬로 저장합니다. Azure는 미국 서부 지역의 쌍을 이루는 데이터 센터에 데이터베이스 백업을 복사합니다.

![지역 복원](./media/sql-database-geo-restore/geo-restore-1.png)

<!--## What is <feature>?" -->

## <a name="what-is-a-sql-database-backup"></a>SQL Database 백업이란?  

<!-- 
First sentence begins with "The <feature> is ..." followed by a definition of the feature. Provide a 1-2 paragraph intro to explain what the feature is, how it works, and the importance of the feature for solving business problems.
-->
SQL Database 백업은 특정 시점에서 데이터베이스의 상태에 대한 정보를 저장하는 파일입니다. SQL Database는 SQL Server 기술을 사용하여 로컬에 [전체](https://msdn.microsoft.com/library/ms186289.aspx), [차등](https://msdn.microsoft.com/library/ms175526.aspx ) 및 [트랜잭션 로그](https://msdn.microsoft.com/library/ms191429.aspx) 백업을 만듭니다. 트랜잭션 로그 백업은 5분마다 발생하므로 데이터베이스를 호스트하는 동일한 서버로 지정 시간 복원이 가능합니다. 사용자가 데이터베이스를 복원할 때 서비스에서는 전체, 차등, 트랜잭션 로그 백업 중 무엇을 복원해야 하는지 파악합니다.

>[AZURE.NOTE] SQL Database는 자동으로 로컬 데이터베이스 백업과 지역 중복 백업을 모두 만듭니다. 사용자는 아무 작업도 할 필요가 없습니다. 추가 비용도 없습니다.

데이터베이스 백업을 사용하여 다음 작업을 수행합니다.

- 보존 기간 내 지정 시간으로 데이터베이스를 복원합니다. 데이터베이스 백업을 사용하면 특정 시점으로 데이터베이스를 복원하거나, 삭제된 데이터베이스를 데이터베이스가 삭제된 시점으로 복원하거나, 다른 지역에서 데이터베이스를 복원할 수 있습니다. 복원을 수행하려면 [데이터베이스 백업으로 데이터베이스 복원](sql-database-recovery-using-backups.md)을 참조하세요.

- 같은 지역 또는 다른 지역의 SQL 서버에 데이터베이스를 복사합니다. 사본은 트랜잭션 측면에서 현재 SQL Database와 일치합니다. 복사를 수행하려면 [데이터베이스를 복사](sql-database-copy.md)를 참조하세요.

- 백업 보존 기간 이후에도 데이터베이스 백업을 보관합니다. 보관을 수행하려면 [SQL 데이터베이스를 BACPAC 파일로 내보냅니다](sql-database-export.md). 그런 다음 BACPAC 파일을 장기 저장소에 보관하여 보존 기간 이상으로 보관할 수 있습니다. 또는 BACPAC를 사용하여 데이터베이스 사본을 온-프레미스나 Azure VM(가상 컴퓨터)의 SQL Server로 전송합니다.

## <a name="backups-have-geographical-redundancy"></a>백업의 지리적 중복성

SQL Database는 [Azure Storage 복제](../storage/storage-redundancy.md)를 사용하여 다른 지리적 위치에 데이터베이스를 백업합니다. 지역 중복 저장소를 제공하기 위해 SQL Database는 로컬 데이터베이스 백업 파일을 [RA-GRS(읽기 액세스 지역 중복 저장소)](../storage/storage-redundancy.md#read-access-geo-redundant-storage) 계정에 저장합니다. Azure는 [쌍을 이루는 데이터 센터](../best-practices-availability-paired-regions.md)에 백업 파일을 복제합니다. 이러한 지역에서 복제는 주 데이터베이스 지역의 데이터베이스 백업에 액세스할 수 없는 경우에 데이터베이스를 복원할 수 있도록 해 줍니다. 

>[AZURE.NOTE] Azure Storage에서 *복제* 라는 용어는 한 위치에서 다른 위치로 파일을 복사하는 것을 말합니다. SQL의 *데이터베이스 복제* 는 주 데이터베이스와 동기화된 다수의 보조 데이터베이스를 유지하는 것을 말합니다. 


## <a name="how-much-backup-storage-is-included-at-no-cost"></a>무료 백업 저장소가 얼마나 포함되어 있습니까?

SQL Database는 추가 비용 없이 최대 프로비전된 데이터베이스 저장소의 최대 200%가 백업 저장소로 제공됩니다. 예를 들어, 프로비전된 DB의 크기가 250GB인 Standard DB 인스턴스가 있으면 추가 비용 없이 500GB의 백업 저장소를 갖습니다. 제공된 백업 저장소를 데이터베이스가 초과하면, Azure 지원 팀에 문의하여 보존 기간을 줄이도록 선택할 수 있습니다. 또 다른 옵션은 표준 RA-GRS(읽기 액세스 지리 중복 저장소) 요금이 청구되는 추가 백업 저장소에 대한 비용을 지불하는 것입니다. 

## <a name="how-often-do-backups-happen"></a>백업이 얼마나 자주 수행됩니까?

매주 전체 데이터베이스 백업을 수행하고, 매 시간 차등 데이터베이스 백업을 수행하고, 5분마다 트랜잭션 로그 백업을 수행합니다. 첫 번째 전체 백업은 데이터베이스를 만든 후에 즉시 예약됩니다. 일반적으로 30분 내에 완료되지만 데이터베이스의 크기가 상당히 큰 경우에는 더 오래 걸릴 수 있습니다. 예를 들어, 복원된 데이터베이스 또는 데이터베이스 사본에 대한 초기 백업은 더 오래 걸릴 수 있습니다. 첫 번째 전체 백업 후에 모든 향후 백업은 자동으로 예약되며 백그라운드에서 자동으로 관리됩니다. 전체 및 [차등](https://msdn.microsoft.com/library/ms175526.aspx) 데이터베이스 백업의 정확한 타이밍은 전반적인 시스템 워크로드를 감안하여 결정됩니다. 

## <a name="how-long-do-you-keep-my-backups"></a>백업 보존 기간

각 SQL Database 백업의 보존 기간은 데이터베이스의 [서비스 계층](sql-database-service-tiers.md)에 따라 다릅니다. 데이터베이스의 보존 기간은 다음과 같습니다.

- 기본 서비스 계층은 7일입니다.
- 표준 서비스 계층은 35일입니다.
- 프리미엄 서비스 계층은 35일입니다.


데이터베이스를 표준 또는 프리미엄 서비스 계층에서 기본 계층으로 다운그레이드하면 백업이 7일 동안 저장됩니다. 7일보다 오래된 모든 기존 백업은 더 이상 사용할 수 없게 됩니다. 

데이터베이스를 기본 서비스 계층에서 표준 또는 프리미엄으로 업그레이드하면 SQL Database가 기존 백업을 35일 동안 보관합니다. 새 백업이 발생하면 새 백업을 35일 동안 보관합니다.
 
데이터베이스를 삭제하면 SQL Database는 온라인 데이터베이스에 하는 것과 동일한 방식으로 백업을 보관합니다. 예를 들어 보존 기간이 7일인 기본 데이터베이스를 삭제한다고 가정해 봅시다. 4일 된 백업은 앞으로 3일 동안 더 보관됩니다.

> [AZURE.IMPORTANT] SQL Database를 호스트하는 Azure SQL 서버를 삭제하면 해당 서버에 속하는 모든 데이터베이스도 삭제되어 복구할 수 없습니다. 삭제된 서버는 복원할 수 없습니다.

## <a name="next-steps"></a>다음 단계

데이터베이스 백업은 실수로 손상되거나 삭제되지 않도록 데이터를 보호해 주기 때문에 비즈니스 연속성 및 재해 복구 전략의 필수적인 부분입니다.. 데이터베이스 백업을 비즈니스 전략과 맞추는 방법은 [비즈니스 연속성 개요](sql-database-business-continuity.md)를 참조하세요.





<!---HONumber=Oct16_HO2-->


