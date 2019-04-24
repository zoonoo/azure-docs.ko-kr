---
title: Azure SQL Database 구성 방법 | Microsoft Docs
description: Azure SQL Database를 구성 및 관리하는 방법을 알아봅니다.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 7db9c6400ac7d235153a59965e34e30d9b809a81
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60338810"
---
# <a name="how-to-use-azure-sql-database"></a>Azure SQL Database를 사용하는 방법

이 섹션에서는 Azure SQL Database를 관리 및 구성하는 데 도움이 될 수 있는 다양한 지침, 스크립트 및 설명을 찾을 수 있습니다. [단일 데이터베이스](sql-database-howto-single-database.md) 및 [Managed Instance](sql-database-howto-managed-instance.md)에 대한 특정 방법 가이드를 찾을 수도 있습니다.

## <a name="load-data"></a>데이터 로드

- [Azure 내에서 단일 데이터베이스 또는 풀링된 데이터베이스 복사](sql-database-copy.md)
- [BACPAC에서 DB 가져오기](sql-database-import.md)
- [BACPAC로 DB 내보내기](sql-database-export.md)
- [BCP를 사용하여 데이터 로드](sql-database-load-from-csv-with-bcp.md)
- [ADF를 사용하여 데이터 로드](../data-factory/connector-azure-sql-database.md?toc=/azure/sql-database/toc.json)

### <a name="data-sync"></a>데이터 동기화

- [SQL 데이터 동기화](sql-database-sync-data.md)
- [데이터 동기화 에이전트](sql-database-data-sync-agent.md)
- [스키마 변경 내용 복제](sql-database-update-sync-schema.md)
- [OMS로 모니터링](sql-database-sync-monitor-oms.md)
- [데이터 동기화의 모범 사례](sql-database-best-practices-data-sync.md)
- [데이터 동기화 문제 해결](sql-database-troubleshoot-data-sync.md)

## <a name="monitoring-and-tuning"></a>모니터링 및 튜닝

- [수동 조정](sql-database-performance-guidance.md)
- [DMV를 사용하여 성능 모니터링](sql-database-monitoring-with-dmvs.md)
- [쿼리 저장소를 사용하여 성능 모니터링](sql-database-operate-query-store.md)
- [Intelligent Insights로 성능 문제 해결](sql-database-intelligent-insights-troubleshoot-performance.md)
- [Intelligent Insights 진단 로그 사용](sql-database-intelligent-insights-use-diagnostics-log.md)
- [메모리 내 OLTP 공간 모니터링](sql-database-in-memory-oltp-monitoring.md)

### <a name="extended-events"></a>확장 이벤트

- [확장 이벤트](sql-database-xevent-db-diff-from-svr.md)
- [확장 이벤트를 이벤트 파일에 저장](sql-database-xevent-code-event-file.md)
- [확장 이벤트를 링 버퍼에 저장](sql-database-xevent-code-ring-buffer.md)

## <a name="configure-features"></a>기능 구성

- [Azure AD 인증 구성](sql-database-aad-authentication-configure.md)
- [조건부 액세스 구성](sql-database-conditional-access.md)
- [다단계 AAD 인증](sql-database-ssms-mfa-authentication.md)
- [Multi-Factor Auth 구성](sql-database-ssms-mfa-authentication-configure.md)
- [임시 보존 정책 구성](sql-database-temporal-tables-retention-policy.md)
- [BYOK를 사용하여 TDE 구성](transparent-data-encryption-byok-azure-sql-configure.md)
- [TDE BYOK 키 순환](transparent-data-encryption-byok-azure-sql-key-rotation.md)
- [TDE 보호기 제거](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md)
- [메모리 내 OLTP 구성](sql-database-in-memory-oltp-migration.md)
- [Azure Automation 구성](sql-database-manage-automation.md)

## <a name="develop-applications"></a>애플리케이션 개발

- [연결](sql-database-libraries.md)
- [Spark 커넥터 사용](sql-database-spark-connector.md)
- [앱 인증](sql-database-client-id-keys.md)
- [오류 메시지](sql-database-develop-error-messages.md)
- [성능 향상을 위해 일괄 처리 사용](sql-database-use-batching-to-improve-performance.md)
- [연결 지침](sql-database-connectivity-issues.md)
- [DNS 별칭](dns-alias-overview.md)
- [DNS 별칭 PowerShell 설정](dns-alias-powershell.md)
- [포트 - ADO.NET](sql-database-develop-direct-route-ports-adonet-v12.md)
- [C 및 C ++](sql-database-develop-cplusplus-simple.md)
- [Excel](sql-database-connect-excel.md)

## <a name="design-applications"></a>애플리케이션 디자인

- [재해 복구를 위한 디자인](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [탄력적 풀을 위한 디자인](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)
- [앱 업그레이드를 위한 디자인](sql-database-manage-application-rolling-upgrade.md)

### <a name="design-multi-tenant-saas-applications"></a>다중 테넌트 SaaS 애플리케이션 디자인

- [SaaS 디자인 패턴](saas-tenancy-app-design-patterns.md)
- [SaaS Video Indexer](saas-tenancy-video-index-wingtip-brk3120-20171011.md)
- [SaaS 앱 보안](saas-tenancy-elastic-tools-multi-tenant-row-level-security.md)

## <a name="next-steps"></a>다음 단계

- [Managed Instance의 방법 가이드](sql-database-howto-managed-instance.md)에 대해 자세히 알아봅니다.
- [단일 데이터베이스의 방법 가이드](sql-database-howto-single-database.md)에 대해 자세히 알아봅니다.
