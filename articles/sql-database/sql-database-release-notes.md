---
title: Azure SQL Database 릴리스 정보 | Microsoft Docs
description: 새로운 기능과 향상 된 Azure SQL Database 설명서 및 Azure SQL Database 서비스에 알아봅니다
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.subservice: service
ms.devlang: ''
ms.topic: conceptual
ms.date: 03/05/2019
ms.author: carlrab
ms.openlocfilehash: 6600a578ba9c73c8a2c71466fd0b008f19058b80
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57861295"
---
# <a name="sql-database-release-notes"></a>SQL Database 릴리스 정보

이 문서는 새로운 기능과 향상 된 SQL Database 설명서 및 SQL Database 서비스를 나열합니다. SQL Database 서비스 향상에 대 한 참고 [SQL Database 서비스 업데이트](https://azure.microsoft.com/updates/?product=sql-database)합니다. 향상 된 다른 Azure 서비스를 참조 하세요 [서비스 업데이트](https://azure.microsoft.com/updates)합니다.

## <a name="march-2019"></a>2019 년 3 월

### <a name="service-improvements"></a>서비스 개선 사항

| 서비스 개선 사항 | 세부 정보 |
| --- | --- |
| 서비스 예정 ||
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
|Stream Analytics에 대 한 참조 데이터의 원본으로 SQL 데이터베이스입니다. | 자세한 내용은 [Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)합니다.|
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
| 관리 되는 인스턴스 및 트랜잭션 복제 | 사용에 대 한 추가 문서 [관리 되는 인스턴스를 사용 하 여 트랜잭션 복제](replication-with-sql-database-managed-instance.md) |
| 관리 되는 인스턴스 자습서를 사용 하 여 추가 Azure AD | 이렇게 [관리 되는 인스턴스를 사용 하 여 Azure AD](sql-database-managed-instance-aad-security-tutorial.md) 구성 하 고 테스트 해야 하는 자습서에서는 Azure AD 로그인을 사용 하는 인스턴스 보안을 관리 합니다. |
| TRANSACT-SQL 스크립트를 사용 하 여 작업 자동화에 대 한 업데이트 된 콘텐츠 | 업데이트 및 사용에 대 한 콘텐츠를 설명 했습니다 [Transact SQL 스크립트를 사용 하 여 작업 자동화](sql-database-job-automation-overview.md) 단일 데이터베이스, 탄력적 풀 및 관리 되는 인스턴스 |
| 업데이트 관리 되는 인스턴스에 대 한 보안 콘텐츠 | 업데이트 및 콘텐츠를 분명히 설명 합니다 [관리 되는 인스턴스에 대 한 보안 모델](sql-database-security-overview.md), 및 단일 데이터베이스 및 탄력적 풀에 대 한 보안 모델을 사용 하 여 대조 |
| 모든 빠른 시작 및 자습서를 새로 고침 | 모든 빠른 시작 및 자습서에는 [설명서](https://docs.microsoft.com/azure/sql-database) 업데이트 했으며 Azure portal의 변경 내용과 일치 하도록 새로 고침 |
| 추가 빠른 시작 개요 가이드 | 추가 대 한 빠른 시작 개요 가이드 [단일 데이터베이스](sql-database-quickstart-guide.md) 한 [관리 되는 인스턴스](sql-database-managed-instance-quickstart-guide.md) |
| 추가 SQL Database 용어 설명 | 이렇게 [용어집 용어](sql-database-glossary-terms.md) 문서는 선언적 SQL Database 사용 약관 목록 상황에 맞는 용어를 설명 하는 기본 개념 페이지에 대 한 링크를 제공 합니다. |
| &nbsp; |

## <a name="contribute-to-content-improvement"></a>콘텐츠 향상에 기여

Azure SQL 설명서 집합에는 오픈 소스입니다. 여러 가지 이점을 제공 오픈으로 작업 합니다.

- 오픈 소스 리포지토리 계획을 가장 필요한 문서에 대 한 피드백을 받습니다.
- 첫 번째 릴리스에서 가장 유용한 콘텐츠를 게시 하려면 오픈의 오픈 소스 리포지토리 검토 합니다.
- 콘텐츠를 지속적으로 개선 하기 위해 쉽게 수행할 수 있도록 오픈의 오픈 소스 리포지토리 업데이트 합니다.

Azure SQL Database 설명서 콘텐츠에 참여 하려면 참조는 [Microsoft Docs 기여자 가이드 개요](https://docs.microsoft.com/contribute/)합니다. 사용자 환경은 [docs.microsoft.com](https://docs.microsoft.com/) 통합 [GitHub](https://github.com/) 워크플로와 더욱 쉽게 직접. 먼저 [보려는 문서 편집](https://docs.microsoft.com/contribute/#quick-edits-to-existing-documents)합니다. 여는 데 도움이 [새 항목을 검토](https://docs.microsoft.com/contribute/#review-open-prs), 또는 [품질 문제 만들기](https://docs.microsoft.com/contribute/#create-quality-issues)합니다.
