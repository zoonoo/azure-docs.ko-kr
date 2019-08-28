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
ms.openlocfilehash: 55e8121152aa281ab1025f1e8ded53c4b00c3d44
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68566945"
---
# <a name="sql-database-release-notes"></a>SQL Database 릴리스 정보

이 문서에는 현재 공개 미리 보기로 제공 되는 SQL Database 기능이 나열 되어 있습니다. SQL Database 업데이트 및 개선 사항에 대해서는 [SQL Database 서비스 업데이트](https://azure.microsoft.com/updates/?product=sql-database)를 참조 하세요. 다른 Azure 서비스에 대 한 업데이트 및 개선 사항은 [서비스 업데이트](https://azure.microsoft.com/updates)를 참조 하세요.

## <a name="features-in-public-preview"></a>공개 미리 보기의 기능

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
| 관리 되는 인스턴스를 사용 하 여 삭제 된 데이터베이스 다시 만들기 |자세한 내용은 [AZURE SQL Managed Instance에서 삭제 된 데이터베이스 다시 만들기](https://medium.com/azure-sqldb-managed-instance/re-create-dropped-databases-in-azure-sql-managed-instance-dc369ed60266)를 참조 하세요.|
| 관리 되는 인스턴스를 사용 하 여 복제 |자세한 내용은 [Azure SQL Database 관리 되는 인스턴스 데이터베이스에서 복제 구성](replication-with-sql-database-managed-instance.md)을 참조 하세요.|
| 서버리스 컴퓨팅 계층 | 자세한 내용은 서버를 사용 하지 않는 [SQL Database (미리 보기)](sql-database-serverless.md)를 참조 하세요.|
|SQL 분석|자세한 내용은 [Azure SQL 분석](../azure-monitor/insights/azure-sql.md)를 참조 하세요.|
|테이블 변수 지연 컴파일 (호환성 수준 150 미만)|자세한 내용은 [테이블 변수 지연 컴파일](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#table-variable-deferred-compilation)을 참조 하세요.|
| 관리 되는 인스턴스를 사용 하 여 위협 감지 |자세한 내용은 [Azure SQL Database 관리 되는 인스턴스에서 위협 감지 구성](sql-database-managed-instance-threat-detection.md)을 참조 하세요.|
| 관리 되는 인스턴스를 사용 하는 Bring Your Own Key (BYOK)를 사용 하는 TDE (투명 한 데이터 암호화) |자세한 내용은 Azure Key Vault에서 [고객이 관리 하는 키를 사용 하 여 Azure SQL 투명한 데이터 암호화를 참조 하세요. Bring Your Own Key 지원](transparent-data-encryption-byok-azure-sql.md).|
| &nbsp; |

## <a name="updates"></a>업데이트

SQL Database 업데이트 및 개선 사항 목록은 [SQL Database 서비스 업데이트](https://azure.microsoft.com/updates/?product=sql-database)를 참조 하세요.

모든 Azure 서비스에 대 한 업데이트 및 개선 사항은 [서비스 업데이트](https://azure.microsoft.com/updates)를 참조 하세요.

## <a name="contribute-to-content"></a>콘텐츠에 기여

Azure SQL Database 설명서에 기여 하려면 [Docs 참여자 가이드](https://docs.microsoft.com/contribute/)를 참조 하세요.
