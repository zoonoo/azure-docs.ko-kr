<properties
	pageTitle="Azure SQL 데이터베이스 비즈니스 연속성 시나리오 | Microsoft Azure"
	description="Azure SQL 데이터베이스 비즈니스 연속성 시나리오"
	services="sql-database"
	documentationCenter=""
	authors="carlrabeler"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="06/16/2016"
	ms.author="carlrab"
   ms.workload="sqldb-bcdr"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>



# Azure SQL 데이터베이스의 비즈니스 연속성 시나리오

이 문서에서는 비즈니스 연속성을 위한 몇 가지 재해 복구 시나리오 및 몇 가지 응용 프로그램 설계 시나리오를 소개합니다.

## 가동 중단에서 복구

가동이 중단된 경우 [중단 상태에서 Azure SQL 데이터베이스 복구](sql-database-disaster-recovery.md)에서 다음 비즈니스 연속성 솔루션 중 하나를 사용하여 중단 상태에서 복구하는 방법을 참조하세요.

- [활성 지역 복제](sql-database-geo-replication-overview.md)
- [지역 복원](sql-database-recovery-using.backups.md#geo-restore)

가동 중단에서 복구하는 데 필요한 특정 단계 및 시간은 사용자가 선택하는 비즈니스 연속성 솔루션에 따라 달라집니다. 그러나 비즈니스 연속성 솔루션에 관계 없이, 가동 중단에서 복구를 완료하려면 복구를 시작할 시기, 각 비즈니스 연속성 솔루션에 대한 데이터베이스 복구 단계, 복구 후 데이터베이스를 구성하는 방법, 복구 후에 응용 프로그램을 구성하는 방법 등을 알아야 합니다.

## 오류로부터 복구

사용자 오류가 발생하거나 다른 의도치 않은 데이터 수정 오류가 발생하는 경우 [오류로부터 Azure SQL 데이터베이스 복구](sql-database-user-error-recovery.md)에서 다음 비즈니스 연속성 솔루션 중 하나를 사용하여 오류로부터 복구하는 방법을 참조하세요.

- [지정 시간 복원](sql-database-recovery-using-backups.md#point-in-time-restore)
- [삭제된 데이터베이스 복원](sql-database-recovery-using-backups.md#deleted-database-restore)

가동 중단에서 복구하는 데 필요한 특정 단계 및 시간은 사용자가 선택하는 비즈니스 연속성 솔루션에 따라 달라집니다. 그러나 비즈니스 연속성 솔루션에 관계 없이, 각 비즈니스 연속성 솔루션에 대해 오류로부터 복구하는 방법을 알아야 합니다.

## 재해 복구 훈련을 수행하여 가동 중단에 대비

비즈니스 연속성 솔루션이 제대로 적용되려면 응용 프로그램이 복구 워크플로를 적용할 수 있는 준비를 갖추었는지를 확인하기 위한 유효성 검사를 정기적으로 수행하는 것이 좋습니다. 응용 프로그램 동작과 데이터 손실의 영향 및/또는 장애 조치(failover)와 관련된 중단을 검사하는 것은 적절한 엔지니어링 실무입니다. 또한 대부분의 업계 표준에서 비즈니스 연속성 인증의 일부로 요구하는 사항이기도 합니다.

재해 복구 훈련의 수행은 다음으로 구성됩니다.

- 데이터 계층 중단 시뮬레이션
- 복구
- 복구 후 응용 프로그램 무결성 검사

[재해 복구 훈련 수행](sql-database-disaster-recovery-drills.md)에서는 다음과 같은 비즈니스 연속성 솔루션 중 하나를 사용하여 재해 복구 훈련을 수행하는 방법을 설명합니다.

- [활성 지역 복제](sql-database-geo-replication-overview.md)
- [지역 복원](sql-database-recovery-using-backups.md#geo-restore)

## 활성 지역 복제를 사용하여 클라우드 응용 프로그램의 롤링 업그레이드 관리

SQL 데이터베이스로 클라우드 응용 프로그램을 업그레이드하는 작업은 취소가 불가능한 작업이므로 이 시나리오를 비즈니스 연속성 계획 및 설계의 일부로 포함해야 합니다. [응용 프로그램 업그레이드 관리](sql-database-manage-application-rolling-upgrade.md)에서는 SQL 데이터베이스의 [지역에서 복제](sql-database-geo-replication-overview.md)를 사용하여 클라우드 응용 프로그램의 롤링 업그레이드를 사용하도록 설정하는 방법을 설명합니다. 이 문서에서는 업그레이드 프로세스를 오케스트레이션하는 두 가지 다른 방법을 살펴보고 각 옵션의 이점 및 장단점을 설명합니다.

## 활성 지역 복제를 사용하여 클라우드 재해 복구를 위한 응용 프로그램 설계

[클라우드 재해 복구를 위한 응용 프로그램 설계](sql-database-designing-cloud-solutions-for-disaster-recovery.md)에서는 SQL 데이터베이스에서 [활성 지역 복제](sql-database-geo-replication-overview.md)를 사용하여 지역적 오류 및 재해로 인한 중단에 대한 복원력이 뛰어난 데이터베이스 응용 프로그램을 설계하는 방법을 설명합니다. 이 문서에서는 응용 프로그램 배포 토폴로지, 목표로 하는 서비스 수준 계약, 트래픽 대기 시간, 비용을 고려한 다음 일반적인 응용 프로그램 패턴과 각각의 장단점을 확인합니다.

## 탄력적 데이터베이스 풀을 사용한 응용 프로그램에 대한 재해 복구 전략

[탄력적 풀 재해 복구 전략](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)에서는 [탄력적 데이터베이스 풀](sql-database-elastic-pool.md)을 사용한 재해 복구 시나리오에 대해 설명합니다.

## 다음 단계

- 비즈니스 연속성을 대략적으로 이해하려면 [비즈니스 연속성 개요](sql-database-business-continuity.md)를 참조하세요.
- Azure SQL 데이터베이스 자동화 백업에 대한 자세한 내용은 [SQL 데이터베이스 자동화 백업](sql-database-automated-backups.md)을 참조하세요.
- 비즈니스 연속성 설계 및 복구 시나리오에 대해 알아보려면 [연속성 시나리오](sql-database-business-continuity-scenarios.md)를 참조하세요.
- 복구를 위해 자동화된 백업을 사용하는 방법을 알아보려면 [서비스에서 시작한 백업에서 데이터베이스 복원](sql-database-recovery-using-backups.md)을 참조하세요.
- 빠른 복구 옵션에 대해 알아보려면 [활성 지역 복제](sql-database-geo-replication-overview.md)를 참조하세요.
- 보관을 위해 자동화된 백업을 사용하는 방법을 알아보려면 [데이터베이스 복사](sql-database-copy.md)를 참조하세요.

<!---HONumber=AcomDC_0629_2016-->