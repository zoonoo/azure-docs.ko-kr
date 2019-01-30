---
title: Azure SQL Database 구성 방법 | Microsoft Docs
description: Azure SQL Database를 구성 및 관리하는 방법을 알아봅니다.
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
manager: craigg
ms.date: 12/14/2018
ms.openlocfilehash: b4dd21324591075d7625a82fbbb661c4a8e84b1d
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/15/2018
ms.locfileid: "53439540"
---
# <a name="how-to-use-azure-sql-database"></a>Azure SQL Database를 사용하는 방법

이 섹션에서는 Azure SQL Database를 관리 및 구성하는 데 도움이 될 수 있는 다양한 지침, 스크립트 및 설명을 찾을 수 있습니다. [단일 데이터베이스](sql-database-howto-single-database.md) 및 [Managed Instance](sql-database-howto-managed-instance.md)에 대한 특정 방법 가이드를 찾을 수도 있습니다.

## <a name="load-data"></a>데이터 로드

- [Azure 내에서 단일 데이터베이스 복사](https://docs.microsoft.com/azure/sql-database/sql-database-copy)
- [BACPAC에서 DB 가져오기](https://docs.microsoft.com/azure/sql-database/sql-database-import)
- [BACPAC로 DB 내보내기](https://docs.microsoft.com/azure/sql-database/sql-database-export)
- [BCP를 사용하여 데이터 로드](https://docs.microsoft.com/azure/sql-database/sql-database-load-from-csv-with-bcp)
- [ADF를 사용하여 데이터 로드](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database?toc=/azure/sql-database/toc.json)

### <a name="data-sync"></a>데이터 동기화

- [SQL 데이터 동기화](https://docs.microsoft.com/azure/sql-database/sql-database-sync-data)
- [데이터 동기화 에이전트](https://docs.microsoft.com/azure/sql-database/sql-database-data-sync-agent)
- [스키마 변경 내용 복제](https://docs.microsoft.com/azure/sql-database/sql-database-update-sync-schema)
- [OMS로 모니터링](https://docs.microsoft.com/azure/sql-database/sql-database-sync-monitor-oms)
- [데이터 동기화의 모범 사례](https://docs.microsoft.com/azure/sql-database/sql-database-best-practices-data-sync)
- [데이터 동기화 문제 해결](https://docs.microsoft.com/azure/sql-database/sql-database-troubleshoot-data-sync)

## <a name="monitoring-and-tuning"></a>모니터링 및 튜닝

-  [수동 조정](https://docs.microsoft.com/azure/sql-database/sql-database-performance-guidance)
- [DMV를 사용하여 성능 모니터링](https://docs.microsoft.com/azure/sql-database/sql-database-monitoring-with-dmvs)
- [쿼리 저장소를 사용하여 성능 모니터링](https://docs.microsoft.com/azure/sql-database/sql-database-operate-query-store)
- [Intelligent Insights로 성능 문제 해결](https://docs.microsoft.com/azure/sql-database/sql-database-intelligent-insights-troubleshoot-performance)
- [Intelligent Insights 진단 로그 사용](https://docs.microsoft.com/azure/sql-database/sql-database-intelligent-insights-use-diagnostics-log)
- [메모리 내 OLTP 공간 모니터링](https://docs.microsoft.com/azure/sql-database/sql-database-in-memory-oltp-monitoring)

### <a name="extended-events"></a>확장 이벤트

- [확장 이벤트](https://docs.microsoft.com/azure/sql-database/sql-database-xevent-db-diff-from-svr)
- [확장 이벤트를 이벤트 파일에 저장](https://docs.microsoft.com/azure/sql-database/sql-database-xevent-code-event-file)
- [확장 이벤트를 링 버퍼에 저장](https://docs.microsoft.com/azure/sql-database/sql-database-xevent-code-ring-buffer)

## <a name="configure-features"></a>기능 구성

- [Azure AD 인증 구성](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure)
- [조건부 액세스 구성](https://docs.microsoft.com/azure/sql-database/sql-database-conditional-access)
- [다단계 AAD 인증](https://docs.microsoft.com/azure/sql-database/sql-database-ssms-mfa-authentication)
- [Multi-Factor Auth 구성](https://docs.microsoft.com/azure/sql-database/sql-database-ssms-mfa-authentication-configure)
- [임시 보존 정책 구성](https://docs.microsoft.com/azure/sql-database/sql-database-temporal-tables-retention-policy)
- [BYOK를 사용하여 TDE 구성](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-byok-azure-sql-configure)
- [TDE BYOK 키 순환](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-byok-azure-sql-key-rotation)
- [TDE 보호기 제거](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-byok-azure-sql-remove-tde-protector)
- [메모리 내 OLTP 구성](https://docs.microsoft.com/azure/sql-database/sql-database-in-memory-oltp-migration)
- [Azure Automation 구성](https://docs.microsoft.com/azure/sql-database/sql-database-manage-automation)

## <a name="develop-applications"></a>응용 프로그램 개발

- [연결](https://docs.microsoft.com/azure/sql-database/sql-database-libraries)
- [Spark 커넥터 사용](https://docs.microsoft.com/azure/sql-database/sql-database-spark-connector)
- [앱 인증](https://docs.microsoft.com/azure/sql-database/sql-database-client-id-keys)
- [오류 메시지](https://docs.microsoft.com/azure/sql-database/sql-database-develop-error-messages)
- [성능 향상을 위해 일괄 처리 사용](https://docs.microsoft.com/azure/sql-database/sql-database-use-batching-to-improve-performance)
- [연결 지침](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-issues)
- [DNS 별칭](https://docs.microsoft.com/azure/sql-database/dns-alias-overview)
- [DNS 별칭 PowerShell 설정](https://docs.microsoft.com/azure/sql-database/dns-alias-powershell)
- [포트 - ADO.NET](https://docs.microsoft.com/azure/sql-database/sql-database-develop-direct-route-ports-adonet-v12)
- [C 및 C ++](https://docs.microsoft.com/azure/sql-database/sql-database-develop-cplusplus-simple)
- [Excel](https://docs.microsoft.com/azure/sql-database/sql-database-connect-excel)

## <a name="design-applications"></a>애플리케이션 디자인

- [재해 복구를 위한 디자인](https://docs.microsoft.com/azure/sql-database/sql-database-designing-cloud-solutions-for-disaster-recovery)
- [탄력적 풀을 위한 디자인](https://docs.microsoft.com/azure/sql-database/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool)
- [앱 업그레이드를 위한 디자인](https://docs.microsoft.com/azure/sql-database/sql-database-manage-application-rolling-upgrade)

### <a name="design-multi-tenant-saas-applications"></a>다중 테넌트 SaaS 애플리케이션 디자인

- [SaaS 디자인 패턴](https://docs.microsoft.com/azure/sql-database/saas-tenancy-app-design-patterns)
- [SaaS Video Indexer](https://docs.microsoft.com/azure/sql-database/saas-tenancy-video-index-wingtip-brk3120-20171011)
- [SaaS 앱 보안](https://docs.microsoft.com/azure/sql-database/saas-tenancy-elastic-tools-multi-tenant-row-level-security)

## <a name="next-steps"></a>다음 단계
- [Managed Instance의 방법 가이드](sql-database-howto-managed-instance.md)에 대해 자세히 알아봅니다.
- [단일 데이터베이스의 방법 가이드](sql-database-howto-single-database.md)에 대해 자세히 알아봅니다.
