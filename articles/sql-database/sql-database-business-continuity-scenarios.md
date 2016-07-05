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
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>



# Azure SQL 데이터베이스의 비즈니스 연속성 시나리오

> [AZURE.SELECTOR]
- [비즈니스 연속성](sql-database-business-continuity.md)
- [시나리오](sql-database-business-continuity-scenarios.md)
- [지정 시간 복원](sql-database-point-in-time-retore.md)
- [삭제된 데이터베이스 복원](sql-database-restore-deleted-database.md)
- [지역 복원](sql-database-geo-restore.md)
- [활성 지역 복제](sql-database-geo-replication)

이 문서에서는 몇 가지 Azure SQL 데이터베이스 비즈니스 연속성 시나리오에 대해 알아봅니다.

## 가동 중단에서 복구

[Azure SQL 데이터베이스 복원 또는 보조 데이터베이스에 대한 장애 조치](sql-database-disaster-recovery.md)에서는 다음과 같은 기능을 사용하여 가동 중단에서 복구하는 방법을 설명합니다.

- [활성 지역 복제](sql-database-geo-replication-overview.md)
- [지역 복원](sql-database-geo-restore.md)

이 문서에서는 복구를 시작할 시기, 각 기능을 사용하여 복구하는 방법, 복구 후 데이터베이스를 구성하는 방법, 복구 후 응용 프로그램을 구성하는 방법을 살펴보았습니다.

## 사용자 오류에서 복구

[사용자 오류에서 Azure SQL 데이터베이스 복구](sql-database-user-error-recovery.md)에서는 다음과 같은 기능을 사용하여 사용자 오류 또는 의도하지 않은 데이터 수정으로부터 복구하는 방법을 설명합니다.

- [지정 시간 복원](sql-database-point-in-time-restore.md) 
- [삭제된 데이터베이스 복원](sql-database-restore-deleted-database.md)

## 재해 복구 훈련 수행

[재해 복구 훈련 수행](sql-database-disaster-recovery-drills.md)에서는 다음과 같은 기능을 사용하여 재해 복구 훈련을 수행하는 방법을 설명합니다.

- [활성 지역 복제](sql-database-geo-replication-overview.md)
- [지역 복원](sql-database-geo-restore.md)

복구 워크플로에 대한 응용 프로그램 준비의 유효성 검사를 정기적으로 수행하는 것이 좋습니다. 응용 프로그램 동작과 데이터 손실의 영향 및/또는 장애 조치(failover)와 관련된 중단을 검사하는 것은 적절한 엔지니어링 실무입니다. 또한 대부분의 업계 표준에서 비즈니스 연속성 인증의 일부로 요구하는 사항이기도 합니다.

재해 복구 훈련의 수행은 다음으로 구성됩니다.

- 데이터 계층 중단 시뮬레이션
- 복구 
- 복구 후 응용 프로그램 무결성 검사

## 재해 복구 후 보안 관리

[재해 복구 후에 보안을 관리하는 방법](sql-database-geo-replication-security-config.md)에서는 [활성 지역 복제](sql-database-geo-replication-overview.md) 구성 및 제어에 대한 인증 요구 사항 및 보조 데이터베이스에 대한 사용자 액세스 설정이 필요한 단계를 설명합니다. 또한 [지리적 복원](sql-database-geo-restore.md)을 사용한 후에 복구된 데이터베이스에 대한 액세스를 활성화하는 방법도 설명합니다.

## 활성 지역 복제를 사용하여 클라우드 응용 프로그램의 롤링 업그레이드 관리

[SQL 데이터베이스 활성 지역 복제를 사용하여 클라우드 응용 프로그램의 롤링 업그레이드 관리](sql-database-manage-application-rolling-upgrade.md)에서는 SQL 데이터베이스에서 [지역에서 복제](sql-database-geo-replication-overview.md)를 사용하여 클라우드 응용 프로그램의 롤링 업그레이드를 사용하도록 설정하는 방법을 설명합니다. 업그레이드는 중단 작업이기 때문에 무중단 업무 방식 계획 및 디자인의 일부여야 합니다. 이 문서에서는 업그레이드 프로세스를 오케스트레이션하는 두 가지 다른 방법을 살펴보고 각 옵션의 이점 및 장단점을 설명합니다.

## 활성 지역 복제를 사용하여 클라우드 재해 복구를 위한 응용 프로그램 설계

[SQL 데이터베이스에서 활성 지역 복제를 사용하여 클라우드 재해 복구를 위한 응용 프로그램 설계](sql-database-designing-cloud-solutions-for-disaster-recovery.md)에서는 SQL 데이터베이스에서 [활성 지역 복제](sql-database-geo-replication-overview.md)를 사용하여 지역적 오류 및 재해로 인한 중단에 대한 복원력이 뛰어난 데이터베이스 응용 프로그램을 설계하는 방법을 설명합니다. 이 문서에서는 응용 프로그램 배포 토폴로지, 목표로 하는 서비스 수준 계약, 트래픽 대기 시간, 비용을 고려한 다음 일반적인 응용 프로그램 패턴과 각각의 장단점을 확인합니다.

## 탄력적 데이터베이스 풀을 사용한 응용 프로그램에 대한 재해 복구 전략

[SQL 데이터베이스의 탄력적 풀을 사용한 응용 프로그램에 대한 재해 복구 전략](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)에서는 [탄력적 데이터베이스 풀](sql-database-elastic-pool.md)을 사용한 재해 복구 시나리오에 대해 설명합니다.

## 다음 단계

- 재해 복구를 위해 활성 지역 복제를 사용 및 구성하는 방법에 대한 자세한 내용은 [활성 지역 복제](sql-database-geo-replication-overview.md)를 참조하세요.
- 재해 복구를 위해 지리적 복원을 사용하는 방법에 대한 자세한 내용은 [지리적 복원](sql-database-geo-restore.md)을 참조하세요.

## 추가 리소스

- [SQL 데이터베이스 비즈니스 연속성 및 재해 복구](sql-database-business-continuity.md)
- [특정 시점 복원](sql-database-point-in-time-restore.md)
- [지역 복원](sql-database-geo-restore.md)
- [활성 지역 복제](sql-database-geo-replication-overview.md)
- [클라우드 재해 복구를 위한 응용 프로그램 설계](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [복구된 Azure SQL 데이터베이스 마무리](sql-database-recovered-finalize.md)
- [지역에서 복제를 위한 보안 구성](sql-database-geo-replication-security-config.md)
- [SQL 데이터베이스 BCDR FAQ](sql-database-bcdr-faq.md)

<!---HONumber=AcomDC_0622_2016-->