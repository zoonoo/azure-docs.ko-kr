---
title: Azure SQL Database 구성 방법 - 단일 | Microsoft Docs
description: Azure SQL Database - 단일 데이터베이스를 구성 및 관리하는 방법을 알아봅니다.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
manager: craigg
ms.date: 02/08/2019
ms.openlocfilehash: 4c6799372f203f021a07ae52a1d7f591aae5afad
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60339009"
---
# <a name="how-to-use-a-single-database-in-azure-sql-database"></a>Azure SQL Database에서 단일 데이터베이스를 사용하는 방법

이 섹션에서는 Azure SQL Database에서 단일 데이터베이스를 관리 및 구성하는 데 도움이 될 수 있는 다양한 지침, 스크립트 및 설명을 확인할 수 있습니다.

## <a name="migrate"></a>마이그레이션

- [SQL Database로 마이그레이션](sql-database-single-database-migrate.md) – Managed Instance로 마이그레이션하기 위한 권장되는 마이그레이션 프로세스 및 도구에 대해 알아봅니다.
- [마이그레이션 후 SQL Database 관리](sql-database-manage-after-migration.md) 방법을 알아봅니다.

## <a name="configure-features"></a>기능 구성

- 데이터베이스 간에 날짜를 복제하도록 [트랜잭션 복제를 구성](replication-to-sql-database.md)합니다.
- Azure SQL Database가 SQL 삽입과 같은 의심스러운 활동이나 의심스러운 위치에서의 액세스를 식별할 수 있도록 [위협 감지를 구성](sql-database-threat-detection.md)합니다.
- 중요한 데이터를 보호하도록 [동적 데이터 마스킹을 구성](sql-database-dynamic-data-masking-get-started-portal.md)합니다.
- Azure Blob Storage에 백업을 유지하도록 데이터베이스에 대해 [백업 보존을 구성](sql-database-long-term-backup-retention-configure.md)합니다. 대안으로 [Azure 자격 증명 모음을 사용하여 백업 보존 구성(사용되지 않음)](sql-database-long-term-backup-retention-configure-vault.md) 접근 방식이 있습니다.
- 데이터베이스의 복제본을 다른 지역에 유지하도록 [지역에서 복제를 구성](sql-database-geo-replication-portal.md)합니다.
- [지역 복제본을 위한 보안 구성](sql-database-geo-replication-security-config.md).

## <a name="monitor-and-tune-your-database"></a>데이터베이스 모니터링 및 튜닝

- [자동 튜닝을 사용하도록 설정](sql-database-automatic-tuning-enable.md)하여 Azure SQL Database에서 워크로드의 성능을 최적화할 수 있도록 합니다.
- [자동 튜닝에 대해 이메일 알림을 사용하도록 설정](sql-database-automatic-tuning-email-notifications.md)하여 튜닝 권장 구성에 대한 정보를 받습니다.
- [성능 권장 사항을 적용](sql-database-advisor-portal.md)하고 데이터베이스를 최적화합니다.
- [경고를 만들어](sql-database-insights-alerts-portal.md) Azure SQL Database에서 알림을 받습니다.
- 애플리케이션과 데이터베이스 간에 연결 문제가 발생할 경우 [연결 문제를 해결](sql-database-troubleshoot-common-connection-issues.md)합니다. [연결 문제에 대해 Resource Health를 사용](sql-database-resource-health.md)할 수도 있습니다.
- [파일 공간을 관리](sql-database-file-space-management.md)하여 데이터베이스의 스토리지 사용량을 모니터링합니다.

## <a name="query-distributed-data"></a>분산 데이터 쿼리

- 여러 데이터베이스에서 [수직 분할된 데이터를 쿼리](sql-database-elastic-query-getting-started-vertical.md)합니다.
- [스케일 아웃된 데이터 계층에서 보고를 수행](sql-database-elastic-query-horizontal-partitioning.md)합니다.
- [여러 스키마를 사용하여 테이블에서 쿼리](sql-database-elastic-query-vertical-partitioning.md)합니다.

## <a name="elastic-database-jobs"></a>Elastic Database 작업

- PowerShell을 사용하여 Elastic Database 작업을 [만들고 관리](elastic-jobs-powershell.md)합니다.
- Transact-SQL을 사용하여 Elastic Database 작업을 [만들고 관리](elastic-jobs-tsql.md)합니다.
- [오래된 탄력적 작업부터 마이그레이션](elastic-jobs-migrate.md)합니다.

## <a name="database-sharding"></a>분할된 데이터베이스

- [탄력적 데이터베이스 클라이언트 라이브러리를 업그레이드](sql-database-elastic-scale-upgrade-client-library.md)합니다.
- [분할된 앱을 만듭니다](sql-database-elastic-scale-get-started.md).
- [수평 분할된 데이터를 쿼리](sql-database-elastic-query-getting-started.md)합니다.
- [다중 분할된 쿼리](sql-database-elastic-scale-multishard-querying.md)를 실행합니다.
- [분할된 데이터를 이동](sql-database-elastic-scale-configure-deploy-split-and-merge.md)합니다.
- 분할된 데이터베이스에서 [보안을 구성](sql-database-elastic-scale-split-merge-security-configuration.md)합니다.
- 현재 분할된 데이터베이스 세트에 [분할된 데이터베이스를 추가](sql-database-elastic-scale-add-a-shard.md)합니다.
- [분할된 맵 문제를 해결](sql-database-elastic-database-recovery-manager.md)합니다.
- [분할된 DB 마이그레이션](sql-database-elastic-convert-to-use-elastic-tools.md)합니다.
- [카운터를 만듭니다](sql-database-elastic-database-perf-counters.md).
- [Entity Framework를 사용하여](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) 분할된 데이터를 쿼리합니다.
- [Dapper 프레임워크를 사용하여](sql-database-elastic-scale-working-with-dapper.md) 분할된 데이터를 쿼리합니다.

## <a name="next-steps"></a>다음 단계
- [Managed Instance용 방법 가이드](sql-database-howto-managed-instance.md)에 대해 자세히 알아봅니다.
