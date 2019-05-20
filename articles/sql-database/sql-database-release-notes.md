---
title: Azure SQL Database 릴리스 정보 | Microsoft Docs
description: 새로운 기능과 향상 된 Azure SQL Database 설명서 및 Azure SQL Database 서비스에 알아봅니다
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.subservice: service
ms.devlang: ''
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: sstein
ms.openlocfilehash: d527c4fed9c43e62d815078c049d4d8e6f8a46b7
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65787710"
---
# <a name="sql-database-release-notes"></a>SQL Database 릴리스 정보

이 문서는 새로운 기능과 향상 된 SQL Database 설명서 및 SQL Database 서비스를 나열합니다. SQL Database 서비스 향상에 대 한 참고 [SQL Database 서비스 업데이트](https://azure.microsoft.com/updates/?product=sql-database)합니다. 향상 된 다른 Azure 서비스를 참조 하세요 [서비스 업데이트](https://azure.microsoft.com/updates)합니다.

## <a name="features-in-public-preview"></a>공개 미리 보기 기능

| 기능 | 세부 정보 |
| ---| --- |
| 단일 데이터베이스 및 탄력적 풀을 사용 하 여 가속화 된 데이터베이스 복구 | 정보를 참조 하세요 [가속화 된 데이터베이스 복구가](sql-database-accelerated-database-recovery.md)합니다.|
|대략적인 Count Distinct|정보를 참조 하세요 [대략적인 Count Distinct](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#approximate-query-processing)합니다.|
|Rowstore (호환성 수준 150) 아래에서 일괄 처리 모드|정보를 참조 하세요 [Rowstore에서 일괄 처리 모드](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#batch-mode-on-rowstore)합니다.|
| 데이터 검색 및 분류  |정보를 참조 하세요 [Azure SQL Database 및 SQL Data Warehouse 데이터 검색 및 분류](sql-database-data-discovery-and-classification.md)합니다.|
| 탄력적 데이터베이스 작업 | 정보를 참조 하세요 [만들기, 구성 및 탄력적 작업을 관리할](elastic-jobs-overview.md)합니다. |
| 탄력적 쿼리 | 정보를 참조 하세요 [탄력적 쿼리 개요](sql-database-elastic-query-overview.md)합니다. |
| 탄력적 트랜잭션 | [클라우드 데이터베이스에서 분산 트랜잭션을](sql-database-elastic-transactions-overview.md)합니다. |
| 관리 되는 인스턴스를 사용 하 여 인스턴스 데이터 정렬 |정보를 참조 하세요 [Azure SQL Database에서 관리 되는 인스턴스를 만드는 Azure Resource Manager 템플릿 사용 하 여 PowerShell을 사용 하 여](./scripts/sql-managed-instance-create-powershell-azure-resource-manager-template.md)입니다.|
|(호환성 수준 150)에서 메모리 부여 피드백 (행 모드)|정보를 참조 하세요 [메모리 부여 피드백 (행 모드)](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#row-mode-memory-grant-feedback)합니다.|
| Azure portal에서 쿼리 편집기 |정보를 참조 하세요 [Azure portal의 SQL 쿼리 편집기를 사용 하 여 데이터 연결 및 쿼리](sql-database-connect-query-portal.md)합니다.|
| R services/기계 학습 단일 데이터베이스 및 탄력적 풀을 사용 하 여 |정보를 참조 하세요 [Azure SQL Database에서 Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services?view=sql-server-2017#machine-learning-services-in-azure-sql-database)합니다.|
| 관리 되는 인스턴스를 사용 하 여 삭제 된 데이터베이스를 다시 만듭니다. |정보를 참조 하세요 [Azure SQL 관리 되는 인스턴스에서 데이터베이스를 삭제 다시 만드는](https://medium.com/azure-sqldb-managed-instance/re-create-dropped-databases-in-azure-sql-managed-instance-dc369ed60266)합니다.|
| 관리 되는 인스턴스를 사용 하 여 복제 |정보를 참조 하세요 [Azure SQL Database 관리 되는 인스턴스 데이터베이스에서 복제 구성](replication-with-sql-database-managed-instance.md)합니다.|
| 서버리스 컴퓨팅 계층 | 정보를 참조 하세요 [SQL Database 서버 리스 (미리 보기)](sql-database-serverless.md)합니다.|
|SQL 분석|정보를 참조 하세요 [Azure SQL Analytics](../azure-monitor/insights/azure-sql.md)합니다.|
|호환성 수준 150) (아래 변수 지연 된 컴파일 테이블|정보를 참조 하세요 [테이블 변수 지연 된 컴파일](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#table-variable-deferred-compilation)합니다.|
| 관리 되는 인스턴스 위협 검색 |정보를 참조 하세요 [관리 되는 인스턴스를 Azure SQL Database 위협 감지 구성](sql-database-managed-instance-threat-detection.md)합니다.|
| 관리 되는 인스턴스에 대 한 표준 시간대 지원|자세한 내용은 [Azure SQL Database Managed Instance의 표준 시간대](sql-database-managed-instance-timezone.md)합니다.|
| 투명 한 데이터 암호화 (TDE) 사용 하 여 가져올 사용자 고유의 키 (BYOK) 관리 되는 인스턴스를 사용 하 여 |내용은 [Azure Key Vault에서 고객 관리 키를 사용 하 여 Azure SQL 투명 한 데이터 암호화: Bring Your Own Key 지원을](transparent-data-encryption-byok-azure-sql.md)합니다.|
| &nbsp; |

## <a name="may-2019"></a>2019 년 5 월

### <a name="service-improvements"></a>서비스 개선 사항

| 서비스 개선 사항 | 세부 정보 |
| --- | --- |
|일반 공급 용으로 릴리스 하는 대규모 서비스 계층| 자세한 내용은 [최대 100TB의 대규모 서비스 계층](sql-database-service-tier-hyperscale.md) 하 고 [고성능 대규모를 사용 하 여 Azure 데이터베이스 워크 로드에 대 한 확장을 가져옵니다](https://azure.microsoft.com/blog/get-high-performance-scaling-for-your-azure-database-workloads-with-hyperscale/)합니다.|
|서버 리스 계산 계층 공개 미리 보기로 릴리스 | 자세한 내용은 [SQL Database 서버 리스 (미리 보기)](sql-database-serverless.md)합니다.|
| VCore 기반 구매 모델을 사용 하 여 데이터베이스에 대 한 트랜잭션 로그 속도 및 대상 IOPS 증가| 자세한 내용은 [vCore 기반 구매 모델을 사용 하 여 단일 데이터베이스에 대 한 리소스 제한](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-single-databases) 하 고 [DTU 기반 구매 모델을 사용 하 여 단일 데이터베이스에 대 한 리소스 제한](https://docs.microsoft.com/azure/sql-database/sql-database-dtu-resource-limits-single-databases)합니다.
| &nbsp; |

### <a name="documentation-improvements"></a>설명서 개선

| 설명서 개선 | 세부 정보 |
| --- | --- |
| 대규모 서비스 계층 문서 GA 릴리스에 대 한 업데이트| 자세한 내용은 [최대 100TB의 대규모 서비스 계층](sql-database-service-tier-hyperscale.md)합니다.|
|서버 리스 계산 계층 docs 공개 미리 보기 릴리스| 자세한 내용은 [SQL Database 서버 리스 (미리 보기)](sql-database-serverless.md)합니다.|
| &nbsp; |

## <a name="april-2019"></a>2019 년 4 월

### <a name="service-improvements"></a>서비스 개선 사항

| 서비스 개선 사항 | 세부 정보 |
| --- | --- |
| 공개 미리 보기에서 관리 되는 인스턴스에 대 한 공용 끝점| 자세한 내용은 [를 사용 하 여 Azure SQL Database 관리 되는 공용 엔드포인트를 사용 하 여 안전 하 게는 인스턴스](sql-database-managed-instance-public-endpoint-securely.md)합니다.|
| 표준 시간대 지원에 대 한 관리 되는 공개 미리 보기에는 인스턴스| 자세한 내용은 [Azure SQL Database Managed Instance의 표준 시간대](sql-database-managed-instance-timezone.md)합니다.|
| 두 번째 공개 미리 보기에서 관리 되는 id 사용 하 여 Azure SQL Database 보안 릴리스| 참조 [관리 되는 id 사용 하 여 Azure SQL Database 보안 쉬워졌습니다](https://azure.microsoft.com/blog/securing-azure-sql-databases-with-managed-identities-just-got-easier/)합니다.|
| &nbsp; |

### <a name="documentation-improvements"></a>설명서 개선

| 설명서 개선 | 세부 정보 |
| --- | --- |
| 공개 미리 보기에서 관리 되는 인스턴스에 대 한 공용 끝점| 자세한 내용은 [를 사용 하 여 Azure SQL Database 관리 되는 공용 엔드포인트를 사용 하 여 안전 하 게는 인스턴스](sql-database-managed-instance-public-endpoint-securely.md)합니다.|
| 표준 시간대 지원에 대 한 관리 되는 공개 미리 보기에는 인스턴스| 자세한 내용은 [Azure SQL Database Managed Instance의 표준 시간대](sql-database-managed-instance-timezone.md)합니다. |
| Azure SQL Database의 리소스 거 버 넌 스 | 자세한 내용은 [Azure SQL Database의 리소스 거 버 넌 스](https://azure.microsoft.com/blog/resource-governance-in-azure-sql-database/)합니다. || &nbsp; |

## <a name="march-2019"></a>2019 년 3 월

### <a name="service-improvements"></a>서비스 개선 사항

| 서비스 개선 사항 | 세부 정보 |
| --- | --- |
| 일반 공급 발표 된 Azure SQL Database에 대 한 스케일 아웃 지원 읽기 | 자세한 내용은 [읽기 스케일 아웃](sql-database-read-scale-out.md)합니다.|
| &nbsp; |

### <a name="documentation-improvements"></a>설명서 개선

| 설명서 개선 | 세부 정보 |
| --- | --- |
| 단일 데이터베이스에 대 한 추가 로그 제한|자세한 내용은 [단일 데이터베이스 vCore 리소스 제한](sql-database-vcore-resource-limits-single-databases.md)합니다.|
| 탄력적 풀 및 풀링된 데이터베이스에 대 한 추가 로그 제한|자세한 내용은 [탄력적 풀 리소스 제한 vCore](sql-database-vcore-resource-limits-elastic-pools.md)합니다.|
| 추가 트랜잭션 로그 속도 거 버 넌 스| 에 대 한 새 콘텐츠 추가 [트랜잭션 로그 속도 거 버 넌 스](sql-database-resource-limits-database-server.md#transaction-log-rate-governance)합니다.|
| 단일 데이터베이스 및 탄력적 풀 az.sql 모듈을 사용 하려면 업데이트 된 PowerShell 샘플 | 자세한 내용은 [단일 데이터베이스 및 탄력적 풀에 대 한 PowerShell 샘플](sql-database-powershell-samples.md#single-database-and-elastic-pools)합니다.|
| &nbsp; |

## <a name="february-2019"></a>2019년 2월

### <a name="service-improvements"></a>서비스 개선 사항

| 서비스 개선 사항 | 세부 정보 |
| --- | --- |
|다시 시작 가능한 온라인 인덱스 만들기가 이제 일반 공급| 자세한 내용은 [Create Index](https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql)합니다.|
|향상 된 경로 테이블에 대 한 관리 되는 인스턴스 지원| 자세한 내용은 [네트워크 요구 사항](sql-database-managed-instance-connectivity-architecture.md#network-requirements)합니다.|
|관리 되는 인스턴스에서 지원 되는 데이터베이스 이름 바꾸기 | 자세한 내용은 참조는 [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-mi-current) 및 [sp_rename](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-rename-transact-sql) 구문입니다.|
|Stream Analytics에 대 한 참조 데이터의 원본으로 SQL 데이터베이스입니다. | 자세한 내용은 [Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) 하 고 [Azure Stream Analytics는 이제 참조 데이터 입력으로 Azure SQL Database](https://azure.microsoft.com/blog/azure-stream-analytics-now-supports-azure-sql-database-as-reference-data-input/)합니다.|
|Data Migration Assistance 관리 되는 인스턴스에 대 한 지원을 추가합니다. |자세한 내용은 [DMA에서 새로운](https://docs.microsoft.com/sql/dma/dma-whatsnew)합니다.|
|SQL Server Migration Assistant는 관리 되는 인스턴스에 대 한 대상 준비 상태 평가 대 한 지원을 추가합니다. | 자세한 내용은 [SQL Server Migration Assistant](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant)합니다.
|데이터 마이그레이션 서비스는 Amazon RDS에서 관리 되는 인스턴스로 마이그레이션 지원 | 자세한 내용은 [자습서: RDS SQL Server를 Azure SQL Database로 또는 Azure SQL Database 관리 되는 인스턴스를 온라인 DMS를 사용 하 여](../dms/tutorial-rds-sql-server-azure-sql-and-managed-instance-online.md)입니다.|
| &nbsp; |

### <a name="documentation-improvements"></a>설명서 개선

| 설명서 개선 | 세부 정보 |
| --- | --- |
|관리 되는 인스턴스에 배포 옵션 설명 추가|단일 데이터베이스, 탄력적 풀 및 관리 되는 인스턴스에 배포 옵션에 대 한 적용 가능성을 명확 하 게 많은 문서를 업데이트 합니다. |
|DTU 기반 구매 모델에 대 한 업데이트 된 tempdb 크기 | 자세한 내용은 [SQL Database의 Tempdb 데이터베이스](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database)합니다.|
|업데이트 된 가져오기 및 관리 되는 인스턴스 지원에 대 한 bacpac 파일로 내보내기| 자세한 내용은 [BACPAC에서 가져오기](sql-database-import.md) 하 고 [BACPAC로 내보낼](sql-database-export.md)합니다. |
| &nbsp; |

## <a name="january-2019"></a>2019년 1월

### <a name="service-improvements"></a>서비스 개선 사항

| 서비스 개선 사항 | 세부 정보 |
| --- | --- |
| 계산 리소스에 대 한 추가 세분성 옵션 | 범용 및 중요 비즈니스용 서비스 계층 [단일 데이터베이스](sql-database-vcore-resource-limits-single-databases.md) 하 고 [탄력적 풀](sql-database-vcore-resource-limits-elastic-pools.md) 이제 더 세분화 된 계산 옵션이 제공 됩니다.|
| Azure portal에서 관리 되는 인스턴스에 대 한 감사 레코드 보기 | 보는 [감사 레코드를 관리 되는 인스턴스](sql-database-managed-instance-auditing.md) 포털은 이제 Azure에서 지원 합니다.|
| 고급 데이터 보안을 위해 이름이 고급 위협 감지 기능 | 고급 위협 감지 기능 이름이 [고급 데이터 보안이](sql-advanced-threat-protection.md) 단일 데이터베이스, 탄력적 풀 및 관리 되는 인스턴스에 대 한 합니다. |
| &nbsp; |

### <a name="documentation-improvements"></a>설명서 개선

| 설명서 개선 | 세부 정보 |
| --- | --- |
| 관리 되는 인스턴스 및 트랜잭션 복제 | 사용에 대 한 추가 문서 [관리 되는 인스턴스를 사용 하 여 트랜잭션 복제](replication-with-sql-database-managed-instance.md)합니다. |
| 관리 되는 인스턴스 자습서를 사용 하 여 추가 Azure AD | 이렇게 [관리 되는 인스턴스를 사용 하 여 Azure AD](sql-database-managed-instance-aad-security-tutorial.md) 구성 하 고 테스트 해야 하는 자습서에서는 Azure AD 로그인을 사용 하는 인스턴스 보안을 관리 합니다. |
| TRANSACT-SQL 스크립트를 사용 하 여 작업 자동화에 대 한 업데이트 된 콘텐츠 | 업데이트 및 사용에 대 한 콘텐츠를 설명 했습니다 [Transact SQL 스크립트를 사용 하 여 작업 자동화](sql-database-job-automation-overview.md) 단일 데이터베이스, 탄력적 풀 및 관리 되는 인스턴스에 대 한 합니다. |
| 업데이트 관리 되는 인스턴스에 대 한 보안 콘텐츠 | 업데이트 및 콘텐츠를 설명 했습니다 합니다 [관리 되는 인스턴스에 대 한 보안 모델](sql-database-security-overview.md), 및 단일 데이터베이스 및 탄력적 풀에 대 한 보안 모델을 사용 하 여 대조 합니다. |
| 모든 빠른 시작 및 자습서를 새로 고침 | 모든 빠른 시작 및 자습서는 [설명서](https://docs.microsoft.com/azure/sql-database) 업데이트 했으며 Azure portal의 변경 내용과 일치 하도록 새로 고쳐집니다. |
| 추가 빠른 시작 개요 가이드 | 추가 대 한 빠른 시작 개요 가이드 [단일 데이터베이스](sql-database-quickstart-guide.md) 한 [관리 되는 인스턴스](sql-database-managed-instance-quickstart-guide.md)합니다. |
| 추가 SQL Database 용어 설명 | 이렇게 [용어집 용어](sql-database-glossary-terms.md) 문서는 선언적 SQL Database 사용 약관 목록 상황에 맞는 용어를 설명 하는 기본 개념 페이지에 대 한 링크를 제공 합니다. |
| &nbsp; |

## <a name="contribute-to-content-improvement"></a>콘텐츠 향상에 기여

Azure SQL 설명서 집합에는 오픈 소스입니다. 여러 가지 이점을 제공 오픈으로 작업 합니다.

- 오픈 소스 리포지토리 계획을 가장 필요한 문서에 대 한 피드백을 받습니다.
- 첫 번째 릴리스에서 가장 유용한 콘텐츠를 게시 하려면 오픈의 오픈 소스 리포지토리 검토 합니다.
- 콘텐츠를 지속적으로 개선 하기 위해 쉽게 수행할 수 있도록 오픈의 오픈 소스 리포지토리 업데이트 합니다.

Azure SQL Database 설명서 콘텐츠에 참여 하려면 참조는 [Microsoft Docs 기여자 가이드 개요](https://docs.microsoft.com/contribute/)합니다. 사용자 환경은 [docs.microsoft.com](https://docs.microsoft.com/) 통합 [GitHub](https://github.com/) 워크플로와 더욱 쉽게 직접. 먼저 [보려는 문서 편집](https://docs.microsoft.com/contribute/#quick-edits-to-existing-documents)합니다. 여는 데 도움이 [새 항목을 검토](https://docs.microsoft.com/contribute/#review-open-prs), 또는 [품질 문제 만들기](https://docs.microsoft.com/contribute/#create-quality-issues)합니다.
