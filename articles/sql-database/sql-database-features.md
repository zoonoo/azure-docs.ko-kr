---
title: "Azure SQL Database 기능 개요 | Microsoft Docs"
description: "이 페이지에서는 Azure SQL Database 논리 서버 및 데이터베이스에 대한 개요를 제공하며 나열된 각 기능에 대한 링크가 포함된 기능 지원 매트릭스를 포함합니다."
services: sql-database
documentationcenter: na
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: d1a46fa4-53d2-4d25-a0a7-92e8f9d70828
ms.service: sql-database
ms.custom: overview
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 11/28/2016
ms.author: carlrab; jognanay
translationtype: Human Translation
ms.sourcegitcommit: e5b5751facb68ae4a62e3071fe4dfefc02434a9f
ms.openlocfilehash: c153f09741b9b063d67459bbb127f9c4e7942a5b


---
# <a name="azure-sql-database-features"></a>Azure SQL Database 기능
이 항목에서는 Azure SQL Database 논리적 서버 및 데이터베이스에 대한 개요를 제공하며 나열된 각 기능에 대한 링크가 포함된 기능 지원 매트릭스를 포함합니다. 

## <a name="what-is-an-azure-sql-database-logical-server"></a>Azure SQL Database 논리 서버란?
Azure SQL Database 논리 서버는 여러 데이터베이스에 대한 중앙 관리 지점의 역할을 합니다. SQL Database에서 서버는 온-프레미스 환경에서 친숙한 SQL Server 인스턴스와 구별되는 논리적 구문입니다. 특히, SQL Database 서비스는 해당 논리 서버와 관련하여 데이터베이스의 위치르 보증하지는 않으며 인스턴스 수준의 액세스 또는 기능을 노출하지 않습니다. Azure SQL 논리 서버에 대한 자세한 정보는 [논리 서버](sql-database-server-overview.md)를 참조하세요. 

## <a name="what-is-an-azure-sql-database"></a>Azure SQL Database란?
Azure SQL Database의 데이터베이스는 각각 로컬 서버와 연결됩니다. 데이터베이스는 다음과 같을 수 있습니다.

- [고유한 리소스 집합](sql-database-what-is-a-dtu.md#what-are-database-transaction-units-dtus)(DTU)이 있는 단일 데이터베이스
- 일부 [데이터베이스의 풀](sql-database-elastic-pool.md)은 [리소스 집합을 공유](sql-database-what-is-a-dtu.md#what-are-elastic-database-transaction-units-edtus)(eDTU)
- 일부 [분할 데이터베이스의 확장된 집합](sql-database-elastic-scale-introduction.md#horizontal-and-vertical-scaling)은 단일 또는 풀링된 데이터베이스
- [다중 테넌트 SaaS 디자인 패턴](sql-database-design-patterns-multi-tenancy-saas-applications.md)에 속한 데이터베이스 집합의 일부 및 해당 데이터베이스는 단일 또는 풀링된 데이터베이스 또는 둘 다일 수 있습니다. 

Azure SQL Database에 대한 자세한 내용은 [SQL Database](sql-database-overview.md)를 참조하세요.

## <a name="what-features-are-supported"></a>어떤 기능이 지원되나요?

다음 표에서는 Azure SQL Database 및 SQL Server의 주요 기능을 나열하고 지원 가능성을 명시하며 각 플랫폼에서 기능에 대한 자세한 정보를 볼 수 있는 링크를 제공합니다. Transact-SQL 기능의 경우 기능의 범주에 해당하는 테이블의 링크를 따라갑니다. 특정 유형의 기능을 지원하지 않는 이유에 대한 자세한 배경은 [Azure SQL Database Transact-SQL의 차이점](sql-database-transact-sql-information.md)을 참조하세요.

V12에 기능은 추가 계속됩니다. Azure에 대한 서비스 업데이트 웹 페이지를 방문하고 해당 필터를 사용해 보세요.

* [SQL 데이터베이스 서비스](https://azure.microsoft.com/updates/?service=sql-database)에 대해 필터링되었습니다.
* SQL 데이터베이스 기능의 [GA(General Availability) 공지](http://azure.microsoft.com/updates/?service=sql-database&update-type=general-availability) 에 대해 필터링되었습니다.

> [!TIP]
> 기존 데이터베이스와 Azure SQL Database와의 호환성을 테스트하려면 [Azure SQL Database 호환성 유효성 검사](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)를 참조하세요.
>

| **기능** | **SQL Server** | **Azure SQL Database** | 
| --- | :---: | :---: | 
| 활성 지역 복제 | 지원되지 않음 - [AlwaysOn 가용성 그룹](https://msdn.microsoft.com/library/hh510230.aspx) 참조 | [지원됨](sql-database-geo-replication-overview.md)
| 상시 암호화 | [지원됨](https://msdn.microsoft.com/library/mt163865.aspx) | [지원됨](sql-database-always-encrypted.md) |
| AlwaysOn 가용성 그룹 | [지원됨](https://msdn.microsoft.com/library/hh510230.aspx) | 지원되지 않음 - [활성 지역 복제](sql-database-geo-replication-overview.md) 참조 |
| 데이터베이스 연결 | [지원됨](https://msdn.microsoft.com/library/ms190209.aspx) | 지원되지 않음 |
| 응용 프로그램 역할 | [지원됨](https://msdn.microsoft.com/library/ms190998.aspx) | [지원됨](https://msdn.microsoft.com/library/ms190998.aspx) |
| 자동 크기 조정 | 지원되지 않음 | [지원됨](sql-database-scale-up.md) |
| Azure Active Directory | 지원되지 않음 | [지원됨](sql-database-aad-authentication.md) |
| Azure 데이터 팩터리 | 지원되지 않음 - [SSIS(SQL Server Integration Services)](https://msdn.microsoft.com/library/ms141026.aspx) 참조 | [지원됨](https://azure.microsoft.com/services/data-factory/) |
| 감사 | [지원됨](https://msdn.microsoft.com/library/cc280386.aspx) | [지원됨](sql-database-auditing-get-started.md) |
| BACPAC 파일(내보내기) | [지원됨](https://msdn.microsoft.com/library/hh213241.aspx) | [지원됨](sql-database-export.md) |
| BACPAC 파일(가져오기) | [지원됨](https://msdn.microsoft.com/library/hh710052.aspx) | [지원됨](sql-database-import.md) |
| BACKUP 및 RESTORE 문 | [지원됨](https://msdn.microsoft.com/library/ff848768.aspx) | 지원되지 않음 |
| 기본 제공 함수 | [지원됨](https://msdn.microsoft.com/library/ms174318.aspx) | [대부분](https://msdn.microsoft.com/library/ms174318.aspx) |
| 변경 데이터 캡처 | [지원됨](https://msdn.microsoft.com/library/cc645937.aspx) | 지원되지 않음 |
| 변경 내용 추적 | [지원됨](https://msdn.microsoft.com/library/bb933875.aspx) | [지원됨](https://msdn.microsoft.com/library/bb933875.aspx) |
| Collation 문 | [지원됨](https://msdn.microsoft.com/library/ff848763.aspx) | [지원됨](https://msdn.microsoft.com/library/ff848763.aspx) |
| Columnstore 인덱스 | [지원됨](https://msdn.microsoft.com/library/gg492088.aspx) | [Premium Edition만 해당](https://msdn.microsoft.com/library/gg492088.aspx) |
| CLR(Common Language Runtime) | [지원됨](https://msdn.microsoft.com/library/ms131102.aspx) | 지원되지 않음 |
| 포함된 데이터베이스 | [지원됨](https://msdn.microsoft.com/library/ff929071.aspx) | 기본 제공 |
| 포함된 사용자 | [지원됨](https://msdn.microsoft.com/library/ff929188.aspx) | [지원됨](sql-database-manage-logins.md#non-administrator-users) |
| 흐름 제어 언어 키워드 | [지원됨](https://msdn.microsoft.com/library/ms174290.aspx) | [지원됨](https://msdn.microsoft.com/library/ms174290.aspx) |
| 데이터베이스 간 쿼리 | [지원됨](https://msdn.microsoft.com/library/dn584627.aspx) | [탄력적 쿼리](sql-database-elastic-query-overview.md) |
| 커서 | [지원됨](https://msdn.microsoft.com/library/ms181441.aspx) | [지원됨](https://msdn.microsoft.com/library/ms181441.aspx) | 
| 데이터 압축 | [지원됨](https://msdn.microsoft.com/library/cc280449.aspx) | [지원됨](https://msdn.microsoft.com/library/cc280449.aspx) |
| 데이터베이스 백업 | [사용자에 대해 노출됨](https://msdn.microsoft.com/library/ms187048.aspx) | [기본 제공](sql-database-automated-backups.md) |
| 데이터베이스 메일 | [지원됨](https://msdn.microsoft.com/library/ms189635.aspx) | 지원되지 않음 |
| 데이터베이스 미러링 | [지원됨](https://msdn.microsoft.com/library/ms189852.aspx) | 지원되지 않음 |
| 고급 구성 옵션 | [지원됨](https://msdn.microsoft.com/library/mt629158.aspx) | [지원됨](https://msdn.microsoft.com/library/mt629158.aspx) |
| Data Quality Services(DQS) | [지원됨](https://msdn.microsoft.com/library/ff877925.aspx) | 지원되지 않음 |
| 데이터베이스 스냅숏 | [지원됨](https://msdn.microsoft.com/library/ms175158.aspx) | 지원되지 않음 |
| 데이터 형식 | [지원됨](https://msdn.microsoft.com/library/ms187752.aspx) | [지원됨](https://msdn.microsoft.com/library/ms187752.aspx) |  
| DBCC 문 | [모두](https://msdn.microsoft.com/library/ms188796.aspx) | [일부](https://msdn.microsoft.com/library/ms188796.aspx) |
| DDL 문 | [지원됨](https://msdn.microsoft.com/library/ff848799.aspx) | [대부분](https://msdn.microsoft.com/library/ff848799.aspx)
| DDL 트리거 | [지원됨](https://msdn.microsoft.com/library/ms175941.aspx) | [데이터베이스에만 해당](https://msdn.microsoft.com/library/ms175941.aspx) |
| 분산 트랜잭션 | [MS DTC](https://msdn.microsoft.com/library/ms131665.aspx) | 제한된 내부 SQL Database 시나리오만 해당 |
| DML 문 | [지원됨](https://msdn.microsoft.com/library/ff848766.aspx) | [대부분](https://msdn.microsoft.com/library/ff848766.aspx) |
| DML 트리거 | [지원됨](https://msdn.microsoft.com/library/ms178110.aspx) | [지원됨](https://msdn.microsoft.com/library/ms178110.aspx) |
| Dmv | [모두](https://msdn.microsoft.com/library/ms188754.aspx) | [일부](https://msdn.microsoft.com/library/ms188754.aspx) |
| 탄력적 풀 | 지원되지 않음 | [지원됨](sql-database-elastic-pool.md) |
| 탄력적 작업 | 지원되지 않음 - [SQL Server 에이전트](https://msdn.microsoft.com/library/ms189237.aspx) 참조 | [지원됨](sql-database-elastic-jobs-getting-started.md) | 
| 탄력적 쿼리 | 지원되지 않음 - [데이터베이스 간 쿼리](https://msdn.microsoft.com/library/dn584627.aspx) 참조 | [지원됨](sql-database-elastic-query-overview.md) |
| 이벤트 알림 | [지원됨](https://msdn.microsoft.com/library/ms186376.aspx) | [지원됨](sql-database-insights-alerts-portal.md) |
| 식 | [지원됨](https://msdn.microsoft.com/library/ms190286.aspx) | [지원됨](https://msdn.microsoft.com/library/ms190286.aspx) |
| 확장 이벤트 | [지원됨](https://msdn.microsoft.com/library/bb630282.aspx) | [일부](sql-database-xevent-db-diff-from-svr.md) |
| 확장된 저장 프로시저 | [지원됨](https://msdn.microsoft.com/library/ms164627.aspx) | 지원되지 않음 |
| 파일 그룹 | [지원됨](https://msdn.microsoft.com/library/ms189563.aspx#Anchor_2) | [기본만 해당](https://msdn.microsoft.com/library/ms189563.aspx#Anchor_2) |
| 파일스트림 | [지원됨](https://msdn.microsoft.com/library/gg471497.aspx) | 지원되지 않음 |
| 전체 텍스트 검색 | [지원됨](https://msdn.microsoft.com/library/ms142571.aspx) | [지원되지 않는 타사 단어 분리기](https://msdn.microsoft.com/library/ms142571.aspx) |
| 함수 | [지원됨](https://msdn.microsoft.com/library/ms174318.aspx) | [대부분](https://msdn.microsoft.com/library/ms174318.aspx) |
| 메모리 내 최적화 | [지원됨](https://msdn.microsoft.com/library/dn133186.aspx) | [Premium Edition만 해당](https://msdn.microsoft.com/library/dn133186.aspx) |
| 작업 | [SQL Server 에이전트](https://msdn.microsoft.com/library/ms189237.aspx) | [지원됨](sql-database-elastic-jobs-getting-started.md) |
| JSON 데이터 지원 | [지원됨](https://msdn.microsoft.com/library/dn921897.aspx) | [지원됨](sql-database-json-features.md) |
| 언어 요소 | [지원됨](https://msdn.microsoft.com/library/ff848807.aspx) | [대부분](https://msdn.microsoft.com/library/ff848807.aspx) |  
| 연결된 서버 | [지원됨](https://msdn.microsoft.com/library/ms188279.aspx) | 지원되지 않음 - [탄력적 쿼리](sql-database-elastic-query-horizontal-partitioning.md) 참조 |
| 로그 전달 | [지원됨](https://msdn.microsoft.com/library/ms187103.aspx) | 지원되지 않음 - [활성 지역 복제](sql-database-geo-replication-overview.md) 참조 |
| 관리 명령 | [지원됨](https://msdn.microsoft.com/library/ms190286.aspx)| [지원되지 않음](https://msdn.microsoft.com/library/ms190286.aspx) |
| Master Data Services(MDS) | [지원됨](https://msdn.microsoft.com/library/ff487003.aspx) | 지원되지 않음 |
| 대량 가져오기에서 최소 로깅 | [지원됨](https://msdn.microsoft.com/library/ms190422.aspx) | 지원되지 않음 |
| 시스템 데이터 수정 | [지원됨](https://msdn.microsoft.com/library/ms178028.aspx) | 지원되지 않음 |
| 온라인 인덱스 작업 | [지원됨](https://msdn.microsoft.com/library/ms177442.aspx) | [서비스 계층에 의해 제한된 트랜잭션 크기](https://msdn.microsoft.com/library/ms177442.aspx) |
| 연산자 | [지원됨](https://msdn.microsoft.com/library/ms174986.aspx) | [대부분](https://msdn.microsoft.com/library/ms174986.aspx) |
| 지정 시간 데이터베이스 복원 | [지원됨](https://msdn.microsoft.com/library/ms179451.aspx) | [지원됨](sql-database-recovery-using-backups.md#point-in-time-restore) |
| Polybase | [지원됨](https://msdn.microsoft.com/library/mt143171.aspx) | [지원되지 않음]
| 정책 기반 관리 | [지원됨](https://msdn.microsoft.com/library/bb510667.aspx) | 지원되지 않음 |
| 조건자 | [지원됨](https://msdn.microsoft.com/library/ms189523.aspx) | [대부분](https://msdn.microsoft.com/library/ms189523.aspx)
| 리소스 관리자 | [지원됨](https://msdn.microsoft.com/library/bb933866.aspx) | [기본 제공](sql-database-service-tiers.md) |
| 백업에서 데이터베이스 복원 | [지원됨](https://msdn.microsoft.com/library/ms187048.aspx#anchor_6) | [기본 제공 백업에서만](sql-database-recovery-using-backups.md) |
| 행 수준 보안 | [지원됨](https://msdn.microsoft.com/library/dn765131.aspx) | [지원됨](https://msdn.microsoft.com/library/dn765131.aspx) |
| 보안 문 | [지원됨](https://msdn.microsoft.com/library/ff848791.aspx) | [일부](https://msdn.microsoft.com/library/ff848791.aspx) |
| 의미 체계 검색 | [지원됨](https://msdn.microsoft.com/library/gg492075.aspx) | 지원되지 않음 |
| 시퀀스 번호 | [지원됨](https://msdn.microsoft.com/library/ff878058.aspx) | [지원됨](https://msdn.microsoft.com/library/ff878058.aspx) |
| Service Broker | [지원됨](https://msdn.microsoft.com/library/bb522893.aspx) | 지원되지 않음 |
| 서버 구성 옵션 | [지원됨](https://msdn.microsoft.com/library/ms189631.aspx) | 지원되지 않음 - [데이터베이스 구성 옵션](https://msdn.microsoft.com/library/mt629158.aspx) 참조 |
| Set 문 | [지원됨](https://msdn.microsoft.com/library/ms190356.aspx) | [대부분](https://msdn.microsoft.com/library/ms190356.aspx) 
| 공간 | [지원됨](https://msdn.microsoft.com/library/bb933790.aspx) | [지원됨](https://msdn.microsoft.com/library/bb933790.aspx) |
| SQL Server 에이전트 | [지원됨](https://msdn.microsoft.com/library/ms189237.aspx) | 지원되지 않음 - [탄력적 작업](sql-database-elastic-jobs-getting-started.md) 참조 |
| SQL Server Analysis Services(SSAS) | [지원됨](https://msdn.microsoft.com/library/bb522607.aspx) | 지원되지 않음 - [Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) 참조 |
| SQL Server 통합 서비스(SSIS) | [지원됨](https://msdn.microsoft.com/library/ms141026.aspx) | 지원되지 않음 - [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) 참조 |
| SQL Server PowerShell | [지원됨](https://msdn.microsoft.com/library/hh245198.aspx) | [지원됨](https://msdn.microsoft.com/library/hh245198.aspx) |
| SQL Server Profiler | [지원됨](https://msdn.microsoft.com/library/ms181091.aspx) | 지원되지 않음 - [확장 이벤트](https://msdn.microsoft.com/library/ms181091.aspx) 참조 |
| SQL Server 복제 | [지원됨](https://msdn.microsoft.com/library/ms151198.aspx) | [트랜잭션 및 스냅숏 복제 구독자만 해당](sql-database-cloud-migrate-compatible-using-transactional-replication.md) |
| SQL Server Reporting Services(SSRS) | [지원됨](https://msdn.microsoft.com/library/ms159106.aspx) | 지원되지 않음 |
| 저장 프로시저 | [지원됨](https://msdn.microsoft.com/library/ms190782.aspx) | [지원됨](https://msdn.microsoft.com/library/ms190782.aspx) |
| 시스템 저장 함수 | [지원됨](https://msdn.microsoft.com/library/ff848780.aspx) | [일부](https://msdn.microsoft.com/library/ff848780.aspx) |
| 시스템 저장 프로시저 | [지원됨](https://msdn.microsoft.com/library/ms187961.aspx) | [일부](https://msdn.microsoft.com/library/ms187961.aspx) |
| 시스템 테이블 | [지원됨](https://msdn.microsoft.com/library/ms179932.aspx) | [일부](https://msdn.microsoft.com/library/ms179932.aspx) |
| 시스템 뷰 | [지원됨](https://msdn.microsoft.com/library/ms177862.aspx) | [일부](https://msdn.microsoft.com/library/ms177862.aspx)
| 테이블 분할 | [지원됨](https://msdn.microsoft.com/library/ms190787.aspx) | [기본 파일 그룹만 해당](https://msdn.microsoft.com/library/ms190787.aspx) |
| 임시 테이블 | [로컬 및 전역](https://msdn.microsoft.com/library/ms174979.aspx#Anchor_4) | [로컬만 해당](https://msdn.microsoft.com/library/ms174979.aspx#Anchor_4) |
| 임시 테이블 | [지원됨](https://msdn.microsoft.com/library/dn935015.aspx) | [지원됨](sql-database-temporal-tables.md) |
| Transaction 문 | [지원됨](https://msdn.microsoft.com/library/ms174377.aspx) | [지원됨](https://msdn.microsoft.com/library/ms174377.aspx) |
| 변수 | [지원됨](https://msdn.microsoft.com/library/ff848809.aspx) | | [지원됨](https://msdn.microsoft.com/library/ff848809.aspx) | 
| 투명한 데이터 암호화(TDE)  | [지원됨](https://msdn.microsoft.com/library/bb934049.aspx) | [지원됨](https://msdn.microsoft.com/dn948096.aspx) |
| Windows Server 장애 조치 클러스터링 | [지원됨](https://msdn.microsoft.com/library/hh270278.aspx) | 지원되지 않음 - [활성 지역 복제](sql-database-geo-replication-overview.md) 참조 |
| XML 인덱스 | [지원됨](http://msdn.microsoft.com/library/bb934097.aspx) | [지원됨](http://msdn.microsoft.com/library/bb934097.aspx) |
| XML 문 | [지원됨](https://msdn.microsoft.com/library/ff848798.aspx) | [지원됨](https://msdn.microsoft.com/library/ff848798.aspx) |

## <a name="next-steps"></a>다음 단계

- Azure SQL Database 서비스에 대한 정보는 [SQL Database 정의](sql-database-technical-overview.md)를 참조하세요.
- Azure SQL 논리 서버의 개요는 [SQL Database 논리 서버 개요](sql-database-server-overview.md)를 참조하세요.
- Azure SQL Database 개요는 [SQL Database 개요](sql-database-overview.md)를 참조하세요.
- Transact-SQL 지원 및 차이점에 대한 정보는 [Azure SQL Database Transact-SQL의 차이점](sql-database-transact-sql-information.md)을 참조하세요.
- **서비스 계층**에 따른 특정 리소스 할당량 및 제한 사항에 대한 정보입니다. 서비스 계층에 대한 개요는 [SQL 데이터베이스 서비스 계층](sql-database-service-tiers.md)을 참조하세요.
- 보안 개요에 대해서는 [Azure SQL Database 보안 개요](sql-database-security-overview.md)를 참조하세요.
- 드라이버 가용성 및 SQL 데이터베이스 지원에 대한 내용은 [SQL 데이터베이스 및 SQL Server의 연결 라이브러리](sql-database-libraries.md)를 참조하세요.



<!--HONumber=Dec16_HO4-->


