---
title: Azure SQL Database 릴리스 정보 | Microsoft Docs
description: Azure SQL Database 서비스 및 Azure SQL Database 설명서의 새로운 기능 및 향상 된 기능에 대해 알아봅니다.
services: sql-database
author: stevestein
ms.service: sql-database
ms.subservice: service
ms.devlang: ''
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: sstein
ms.openlocfilehash: ed41ccea0754f3eeffdd0248bac567859db1492c
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/15/2019
ms.locfileid: "71001518"
---
# <a name="sql-database-release-notes"></a>SQL Database 릴리스 정보

이 문서에는 현재 공개 미리 보기로 제공 되는 SQL Database 기능이 나열 되어 있습니다. SQL Database 업데이트 및 개선 사항에 대해서는 [SQL Database 서비스 업데이트](https://azure.microsoft.com/updates/?product=sql-database)를 참조 하세요. 다른 Azure 서비스에 대 한 업데이트 및 개선 사항은 [서비스 업데이트](https://azure.microsoft.com/updates)를 참조 하세요.

## <a name="features-in-public-preview"></a>공개 미리 보기의 기능

### <a name="single-databasetabsingle-database"></a>[단일 데이터베이스](#tab/single-database)

| 기능 | 세부 정보 |
| ---| --- |
| 단일 데이터베이스 및 탄력적 풀을 사용한 가속화 된 데이터베이스 복구 | 자세한 내용은 [가속화 데이터베이스 복구](sql-database-accelerated-database-recovery.md)를 참조 하세요.|
|대략적인 고유 카운트|자세한 내용은 [대략적인 개수 Distinct](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#approximate-query-processing)를 참조 하십시오.|
|Rowstore의 일괄 처리 모드 (호환성 수준 150)|자세한 내용은 [Rowstore의 일괄 처리 모드](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#batch-mode-on-rowstore)를 참조 하세요.|
| 데이터 검색 및 분류  |자세한 내용은 [Azure SQL Database 및 SQL Data Warehouse 데이터 검색 & 분류](sql-database-data-discovery-and-classification.md)를 참조 하세요.|
| 탄력적 데이터베이스 작업 | 자세한 내용은 [탄력적 작업 만들기, 구성 및 관리](elastic-jobs-overview.md)를 참조 하세요. |
| 탄력적 쿼리 | 자세한 내용은 [탄력적 쿼리 개요](sql-database-elastic-query-overview.md)를 참조 하세요. |
| 탄력적 트랜잭션 | [클라우드 데이터베이스 간 분산 트랜잭션](sql-database-elastic-transactions-overview.md). |
|메모리 부여 피드백 (행 모드) (호환성 수준 150)|자세한 내용은 [메모리 부여 피드백 (행 모드)](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#row-mode-memory-grant-feedback)을 참조 하세요.|
| Azure Portal의 쿼리 편집기 |자세한 내용은 [Azure Portal의 SQL 쿼리 편집기를 사용 하 여 데이터 연결 및 쿼리를](sql-database-connect-query-portal.md)참조 하세요.|
| 단일 데이터베이스 및 탄력적 풀을 사용 하 여 R services/기계 학습 |자세한 내용은 [Azure SQL Database Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services?view=sql-server-2017#machine-learning-services-in-azure-sql-database)을 참조 하세요.|
| 서버리스 컴퓨팅 계층 | 자세한 내용은 서버를 사용 하지 않는 [SQL Database (미리 보기)](sql-database-serverless.md)를 참조 하세요.|
|SQL 분석|자세한 내용은 [Azure SQL 분석](../azure-monitor/insights/azure-sql.md)를 참조 하세요.|
|테이블 변수 지연 컴파일 (호환성 수준 150 미만)|자세한 내용은 [테이블 변수 지연 컴파일](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#table-variable-deferred-compilation)을 참조 하세요.|
| &nbsp; |

### <a name="managed-instancetabmanaged-instance"></a>[Managed Instance](#tab/managed-instance)

| 기능 | 세부 정보 |
| ---| --- |
| <a href="/azure/sql-database/sql-database-instance-pools">인스턴스 풀</a> | 더 작은 SQL 인스턴스를 클라우드로 마이그레이션하는 편리 하 고 비용 효율적인 방법입니다. |
| <a href="https://aka.ms/managed-instance-tde-byok">Bring Your Own Key (BYOK)를 사용 하는 TDE (투명 한 데이터 암호화)</a> |자세한 내용은 Azure Key Vault에서 [고객이 관리 하는 키를 사용 하 여 Azure SQL 투명한 데이터 암호화를 참조 하세요. Bring Your Own Key 지원](transparent-data-encryption-byok-azure-sql.md).|
| <a href="https://aka.ms/managed-instance-failover-groups">지리적으로 분산 장애 조치 (failover) 그룹</a> | 다른 지역에 인스턴스의 복사본을 보관 하 고 지역 재해 시나리오 에서도 데이터를 사용할 수 있도록 합니다. |
| <a href="https://aka.ms/managed-instance-aadlogins">인스턴스 수준 Azure AD 서버 보안 주체 (로그인)</a> | <a href="https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN FROM EXTERNAL PROVIDER</a> 문을 사용 하 여 서버 수준 로그인을 만듭니다. |
| [트랜잭션 복제자](sql-database-managed-instance-transactional-replication.md) | 테이블의 변경 내용을 관리 되는 인스턴스, 단일 데이터베이스 또는 SQL Server 인스턴스에 배치 된 다른 데이터베이스로 복제 하거나 다른 관리 되는 인스턴스 또는 SQL Server 인스턴스에서 일부 행이 변경 될 때 테이블을 업데이트 합니다. 자세한 내용은 [Azure SQL Database 관리 되는 인스턴스 데이터베이스에서 복제 구성](replication-with-sql-database-managed-instance.md)을 참조 하세요. |
| 위협 검색 |자세한 내용은 [Azure SQL Database 관리 되는 인스턴스에서 위협 감지 구성](sql-database-managed-instance-threat-detection.md)을 참조 하세요.|
| 관리 되는 인스턴스를 사용 하 여 삭제 된 데이터베이스 다시 만들기 |자세한 내용은 [AZURE SQL Managed Instance에서 삭제 된 데이터베이스 다시 만들기](https://medium.com/azure-sqldb-managed-instance/re-create-dropped-databases-in-azure-sql-managed-instance-dc369ed60266)를 참조 하세요.|
| &nbsp; |

---

## <a name="fixed-known-issues"></a>알려진 문제 해결

- **8 월 2019** 포함 된 데이터베이스는 관리 되는 인스턴스에서 완전히 지원 됩니다.

## <a name="updates"></a>업데이트

SQL Database 업데이트 및 개선 사항 목록은 [SQL Database 서비스 업데이트](https://azure.microsoft.com/updates/?product=sql-database)를 참조 하세요.

모든 Azure 서비스에 대 한 업데이트 및 개선 사항은 [서비스 업데이트](https://azure.microsoft.com/updates)를 참조 하세요.

## <a name="contribute-to-content"></a>콘텐츠에 기여

Azure SQL Database 설명서에 기여 하려면 [Docs 참여자 가이드](https://docs.microsoft.com/contribute/)를 참조 하세요.
