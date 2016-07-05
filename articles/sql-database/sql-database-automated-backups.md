<properties
   pageTitle="클라우드 비즈니스 연속성 - 기본 제공 백업 - SQL 데이터베이스 | Microsoft Azure"
   description="Azure SQL 데이터베이스를 이전 지정 시간으로 롤백하거나 지리적 영역(최대 35일)의 새 데이터베이스에 데이터베이스를 복사할 수 있도록 하는 SQL 데이터베이스 기본 제공 백업에 대해 알아봅니다."
   services="sql-database"
   documentationCenter=""
   authors="carlrabeler"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sqldb-features"
   ms.date="06/16/2016"
   ms.author="carlrab"/>

# 개요: SQL 데이터베이스 자동화된 백업

Azure SQL 데이터베이스 서비스는 Basic의 경우 7일, Standard의 경우 14일 및 Premium의 경우 35일 동안 보존되는 자동화된 백업을 사용하여 모든 데이터베이스를 보호합니다. 이러한 자동화된 백업을 사용하여 지정 시간 복원을 수행하고 데이터를 실수로 손상 또는 삭제한 후에 삭제된 데이터베이스를 복원할 수 있습니다.

데이터베이스 백업은 옵트인할 필요나 추가 비용 없이 자동으로 수행됩니다. 이러한 자동화된 백업 및 특정 지점 복원은 실수로 손상 또는 삭제된 경우 원인이 무엇이든 데이터베이스를 보호하는 방법을 비용 및 관리 없이 제공합니다.

## 자동화된 백업 비용

Microsoft Azure SQL 데이터베이스에서는 추가 비용 없이 최대 프로비전된 데이터베이스 저장소의 최대 200%까지 백업 저장소가 제공됩니다. 예를 들어, 프로비전된 DB의 크기가 250GB인 Standard DB 인스턴스가 있으면 추가 비용 없이 500GB의 백업 저장소가 제공됩니다. 데이터베이스가 제공된 백업 저장소를 초과하는 경우 Azure 지원에 문의하여 보존 기간을 줄이도록 선택하거나 표준 RA-GRS(읽기 액세스 지리 중복 저장소) 요금으로 청구되는 추가 백업 저장소에 대해 비용을 지불할 수 있습니다.

## 자동 백업 세부 정보

모든 기본, 표준 및 프리미엄 데이터베이스는 자동 백업에 의해 보호됩니다. 전체 백업은 매일, 차등 백업은 매주, 로그 백업은 5분 마다 수행됩니다. 첫 번째 전체 백업은 데이터베이스를 만든 후에 즉시 예약됩니다. 일반적으로 30분 이내에 완료되지만 더 오래 걸릴 수 있습니다. 예를 들어 데이터베이스가 이미 큰 경우, 큰 데이터베이스에서 데이터베이스를 복사 또는 복원하여 만들어진 경우 첫 번째 전체 백업은 완료하는 데 시간이 더 오래 걸릴 수 있습니다. 첫 번째 전체 백업 후에 모든 향후 백업은 자동으로 예약되며 백그라운드에서 자동으로 관리됩니다. 전체 및 차등 백업의 정확한 타이밍은 전체 부하를 분산하는 시스템에 의해 결정됩니다. 백업 파일은 읽기 액세스 권한을 가진 지역 중복 저장소 계정(RA-GRS)에 저장되어 재해 복구에 대비한 가용성을 보장합니다.

## 다음 단계

- [비즈니스 연속성 개요](sql-database-business-continuity.md)
- [삭제된 데이터베이스 복원](sql-database-restore-deleted-database.md)
- [지정 시간 복원](sql-database-point-in-time-restore.md)
- [지역 복원](sql-database-geo-restore.md)
- [활성 지역 복제](sql-database-geo-replication-overview.md)
- [데이터베이스 복사](sql-database-copy.md)

## 추가 리소스

- [가동 중단에서 복구](sql-database-disaster-recovery.md)
- [사용자 오류에서 복구](sql-database-user-error-recovery.md)
- [재해 복구 훈련 수행](sql-database-disaster-recovery-drills.md)
- [클라우드 재해 복구를 위한 응용 프로그램 설계](sql-database-designing-cloud-solutions-for-disaster-recovery.md)

<!---HONumber=AcomDC_0622_2016-->