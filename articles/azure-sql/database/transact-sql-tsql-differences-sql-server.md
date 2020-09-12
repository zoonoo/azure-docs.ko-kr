---
title: T-sql 차이점 해결-마이그레이션
description: Azure SQL Database에서 완전히 지원 되는 것 보다 작은 transact-sql 문입니다.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/03/2018
ms.openlocfilehash: fa10f97d0eb2f48e80d20f90a254c44c6e95a8e8
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2020
ms.locfileid: "89436972"
---
# <a name="resolving-transact-sql-differences-during-migration-to-sql-database"></a>SQL Database로의 마이그레이션 중 Transact-SQL 차이점 해결

SQL Server에서 Azure SQL Database로 [데이터베이스를 마이그레이션하](migrate-to-database-from-sql-server.md) 는 경우 마이그레이션 전에 SQL Server 데이터베이스에 몇 가지 재 엔지니어링이 필요한 것을 알 수 있습니다. 이 문서에서는 엔지니어링을 다시 수행하는 데 도움이 되며 다시 엔지니어링해야 하는 근본적인 이유를 이해하는 데에도 도움이 되는 지침을 제공합니다. 비호환성을 검색하려면 [DMA(Data Migration Assistant)](https://www.microsoft.com/download/details.aspx?id=53595)를 사용합니다.

## <a name="overview"></a>개요

애플리케이션에서 사용하는 대부분의 Transact-SQL 기능은 Microsoft SQL Server와 Azure SQL Database 모두에서 완전하게 지원됩니다. 예를 들어 데이터 형식, 연산자, 문자열, 산술, 논리 및 커서 함수와 같은 핵심 SQL 구성 요소는 SQL Server 및 SQL Database에서 동일 하 게 작동 합니다. 그러나 DDL(데이터 정의 언어) 및 DML(데이터 조작 언어) 요소에는 몇 가지 T-SQL 차이점이 있으며 그 결과 T-SQL 문 및 쿼리가 부분적으로만 지원됩니다(이 문서의 뒷부분에서 설명).

또한 Azure SQL Database은 master 데이터베이스 및 운영 체제에 대 한 종속성 으로부터 기능을 격리 하도록 디자인 되었기 때문에 일부 기능과 구문은 지원 되지 않습니다. 따라서 대부분의 서버 수준 작업이 SQL Database에 적합하지 않습니다. T-sql 문과 옵션은 서버 수준 옵션 또는 운영 체제 구성 요소를 구성 하거나 파일 시스템 구성을 지정 하는 경우 사용할 수 없습니다. 이러한 기능이 필요한 경우 SQL Database 또는 다른 Azure 기능이나 서비스에서 다른 방법으로 사용할 수 있는 적합한 대안이 있는 경우가 많습니다.

예를 들어 [Always On 가용성 그룹](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server)과 비슷한 기술을 사용하여 고가용성을 Azure SQL Database에 기본 제공합니다. 가용성 그룹과 관련된 T-SQL 문은 SQL Database에서 지원되지 않으며 Always On 가용성 그룹에 관련된 동적 관리 뷰도 지원되지 않습니다.

SQL Database에서 지원되는 기능 및 지원되지 않는 기능 목록은  [Azure SQL Database 기능 비교](features-comparison.md)를 참조하세요. 이 페이지의 목록은 해당 지침 및 기능 문서를 보완하고 Transact-SQL 문을 중점적으로 다룹니다.

## <a name="transact-sql-syntax-statements-with-partial-differences"></a>부분적으로 차이점이 있는 Transact-SQL 구문

핵심적인 DDL(데이터 정의 언어) 문은 사용할 수 있지만 일부 DDL 문에는 디스크 배치 및 지원되지 않는 기능과 관련된 확장이 포함되어 있습니다.

- CREATE 및 ALTER DATABASE 문에는 36개가 넘는 옵션이 있습니다. 이러한 문에는 SQL Server에만 적용되는 파일 배치, FILESTREAM 및 Service Broker 옵션이 포함되어 있습니다. 마이그레이션하기 전에 데이터베이스를 만드는 경우에는 문제가 되지 않을 수 있지만 데이터베이스를 만드는 T-sql 코드를 마이그레이션하는 경우 create DATABASE ( [Azure SQL Database)](https://msdn.microsoft.com/library/dn268335.aspx) 를 [create Database (SQL Server transact-sql)](https://msdn.microsoft.com/library/ms176061.aspx) 의 SQL Server 구문과 비교 하 여 사용 하는 모든 옵션이 지원 되는지 확인 해야 합니다. Azure SQL Database의 CREATE DATABASE에는 SQL Database에만 적용되는 서비스 목표와 탄력적인 확장 옵션도 포함되어 있습니다.
- CREATE 및 ALTER TABLE 문에는 FILESTREAM이 지원 되지 않으므로 SQL Database에서 사용할 수 없는 FileTable 옵션이 있습니다.
- CREATE 및 ALTER login 문이 지원 되지만 SQL Database는 모든 옵션을 제공 하지 않습니다. 데이터베이스 이식 가능성을 높이려면 SQL Database에서 가능한 경우 로그인 대신 포함된 데이터베이스 사용자를 사용하도록 권장합니다. 자세한 내용은 [로그인 만들기/변경](https://docs.microsoft.com/sql/t-sql/statements/alter-login-transact-sql) 및 로그인 [및 사용자 관리](logins-create-manage.md)를 참조 하세요.

## <a name="transact-sql-syntax-not-supported-in-azure-sql-database"></a>Azure SQL Database에서 지원되지 않는 Transact-SQL 구문

 [Azure SQL Database 기능 비교](features-comparison.md)에 설명 된 지원 되지 않는 기능과 관련 된 transact-sql 문 외에도 다음 문 및 문 그룹이 지원 되지 않습니다. 따라서 마이그레이션할 데이터베이스에서 다음 기능을 사용하고 있는 경우 T-SQL을 다시 엔지니어링하여 해당 T-SQL 기능 및 문을 제거합니다.

- 시스템 개체의 데이터 정렬
- 연결 관련: 엔드포인트 문. SQL Database는 Windows 인증을 지원 하지 않지만 유사한 Azure Active Directory 인증을 지원 합니다. 최신 버전의 SSMS를 사용해야 하는 인증 형식도 있습니다. 자세한 내용은 [Azure Active Directory 인증을 사용 하 여 SQL Database 또는 Azure Azure Synapse Analytics (이전의 SQL Data Warehouse)에 연결](authentication-aad-overview.md)을 참조 하세요.
- 세 개 또는 네 개의 부분으로 된 이름을 사용하여 데이터베이스 쿼리를 교차합니다. 읽기 전용 데이터베이스 간 쿼리는 [탄력적 데이터베이스 쿼리](elastic-query-overview.md)를 통해 지원됩니다.
- 데이터베이스 간 소유권 체인, `TRUSTWORTHY` 설정
- `EXECUTE AS LOGIN` 대신 '사용자로 실행'을 사용합니다.
- 확장 가능 키 관리를 제외하고 암호화를 지원합니다.
- 이벤트: 이벤트, 이벤트 알림, 쿼리 알림
- 파일 배치: 데이터베이스 파일 배치, 크기 및 Microsoft Azure에서 자동으로 관리되는 데이터베이스 파일과 관련된 구문입니다.
- 고가용성: Microsoft Azure 계정을 통해 관리되는 고가용성과 관련된 구문입니다. 백업, 복원, Alwayson, 데이터베이스 미러링, 로그 전달, 복구 모드에 대한 구문을 포함합니다.
- 로그 판독기: SQL Database에서 사용할 수 없는 로그 판독기에 의존 하는 구문: 밀어넣기 복제, 변경 데이터 캡처. SQL Database는 푸시 복제 문서의 구독자가 될 수 있습니다.
- 함수: `fn_get_sql`, `fn_virtualfilestats`, `fn_virtualservernodes`
- 하드웨어: 하드웨어 관련 서버 설정과 관련된 구문: 메모리, 작업자 스레드 수, CPU 선호도, 추적 플래그 등입니다. 그 대신 서비스 계층 및 컴퓨팅 크기를 사용합니다.
- `KILL STATS JOB`
- `OPENQUERY`, `OPENROWSET`, `OPENDATASOURCE` 및 네 부분으로 된 이름
- .NET Framework: CLR과 SQL Server 통합
- 의미 체계 검색
- 서버 자격 증명: 대신 [데이터베이스 범위 자격 증명](https://msdn.microsoft.com/library/mt270260.aspx)을 사용합니다.
- 서버 수준 항목: 서버 역할, `sys.login_token`. `GRANT``REVOKE` `DENY` 서버 수준 사용 권한의, 및은 사용할 수 없지만 일부는 데이터베이스 수준 사용 권한으로 대체 됩니다. 몇 가지 유용한 서버 수준 DMV에는 동등한 데이터베이스 수준 DMV가 있습니다.
- `SET REMOTE_PROC_TRANSACTIONS`
- `SHUTDOWN`
- `sp_addmessage`
- `sp_configure` 옵션 및 `RECONFIGURE`입니다. [ALTER DATABASE SCOPED CONFIGURATION](https://msdn.microsoft.com/library/mt629158.aspx)을 사용하면 일부 옵션은 사용 가능합니다.
- `sp_helpuser`
- `sp_migrate_user_to_contained`
- SQL Server 에이전트: SQL Server 에이전트 또는 MSDB 데이터베이스를 사용하는 구문: 경고, 운영자, 중앙 관리 서버입니다. 대신 Azure PowerShell과 같은 스크립팅을 사용합니다.
- SQL Server 감사: 대신 SQL Database 감사를 사용합니다.
- SQL Server 추적
- 추적 플래그: 일부 추적 플래그 항목은 호환 모드로 이동되었습니다.
- TRANSACT-SQL 디버깅
- 트리거: 서버 범위 또는 로그온 트리거
- `USE` 문: 데이터베이스 컨텍스트를 다른 데이터베이스로 변경하려면 새 데이터베이스에 대한 새 연결을 설정해야 합니다.

## <a name="full-transact-sql-reference"></a>전체 TRANSACT-SQL 참조

Transact-sql 문법, 사용법 및 예제에 대 한 자세한 내용은 SQL Server 온라인 설명서의 [Transact-sql 참조 (데이터베이스 엔진)](https://msdn.microsoft.com/library/bb510741.aspx)를 참조 하세요   .

### <a name="about-the-applies-to-tags"></a>'적용 대상' 태그 정보

Transact-SQL 참조에는 현재까지 게시된 SQL Server 버전 2008과 관련된 문서가 포함되어 있습니다. 문서 제목 아래에는 아이콘 모음이 있습니다. 네 개의 SQL Server 플랫폼을 나열 하 고 적용 가능성을 나타냅니다. 예를 들어 가용성 그룹은 SQL Server 2012에서 도입되었습니다.  [CREATE AVAILABILITY GROUP](https://msdn.microsoft.com/library/ff878399.aspx)   문서는 문이 **SQL Server (2012부터 시작)** 에 적용 됨을 나타냅니다. 문은 SQL Server 2008, SQL Server 2008 R2, Azure SQL Database, Azure Azure Synapse Analytics (이전의 SQL Data Warehouse) 또는 병렬 데이터 웨어하우스에 적용 되지 않습니다.

경우에 따라 문서에 대 한 일반적인 주제를 제품에서 사용할 수 있지만 제품 간에는 약간의 차이가 있습니다. 차이점은 문서의 중간에 적절하게 표시됩니다. 경우에 따라 문서에 대 한 일반적인 주제를 제품에서 사용할 수 있지만 제품 간에는 약간의 차이가 있습니다. 차이점은 문서의 중간에 적절하게 표시됩니다. 예를 들어 CREATE TRIGGER 문서는 SQL Database에서 사용할 수 있습니다. 그러나 서버 수준 트리거에 대 한 **ALL server** 옵션은 SQL Database에서 서버 수준 트리거를 사용할 수 없음을 나타냅니다. 대신 데이터베이스 수준 트리거를 사용합니다.

## <a name="next-steps"></a>다음 단계

SQL Database에서 지원되는 기능 및 지원되지 않는 기능 목록은  [Azure SQL Database 기능 비교](features-comparison.md)를 참조하세요. 이 페이지의 목록은 해당 지침 및 기능 문서를 보완하고 Transact-SQL 문을 중점적으로 다룹니다.
