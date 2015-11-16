<properties
   pageTitle="Azure SQL 데이터베이스 TRANSACT-SQL 정보 | Microsoft Azure"
   description="Azure SQL 데이터베이스의 TRANSACT-SQL 문"
   services="sql-database"
   documentationCenter=""
   authors="BYHAM"
   manager="jeffreyg"
   editor=""
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="11/02/2015"
   ms.author="rick.byham@microsoft.com"/>

# Azure SQL 데이터베이스 TRANSACT-SQL 정보

대부분의 SQL Server 2016 TRANSACT-SQL 문은 Microsoft Azure SQL 데이터베이스에서 완전히 지원됩니다. 여기에는 SQL Server 데이터 형식, 연산자 및 문자열, 산술, 논리, 커서 함수 및 대부분의 응용 프로그램이 사용하는 다른 TRANSACT-SQL 요소가 포함됩니다. 부분적으로 또는 완전히 지원되지 않는 함수는 일반적으로 SQL 데이터베이스에서 데이터베이스를 관리하는 방법의 차이와 관련이 있거나(예: 파일, 고가용성 및 보안 기능) Service Broker와 같은 특수한 용도의 기능입니다. SQL 데이터베이스는 master 데이터베이스에 대한 종속성에서 많은 기능을 격리하므로 많은 서버 수준 작업이 부적절하고 지원되지 않습니다. SQL Server에서 사용되지 않는 기능은 일반적으로 SQL 데이터베이스에서 지원되지 않습니다.

## SQL 데이터베이스 V12로 업그레이드

이 항목에서는 무료 SQL 데이터베이스 V12로 업그레이드할 경우 SQL 데이터베이스에서 사용할 수 있는 기능에 대해 설명합니다. V12에 대한 자세한 내용은 [SQL 데이터베이스 V12 새로운 기능](sql-database-v12-whats-new.md)을 참조하세요. SQL 데이터베이스 V12에서는 성능 및 관리 효율성이 향상되고 추가 기능이 지원됩니다. 추가된 기능은 완전히 지원되는 기능 및 부분적으로 지원되는 기능으로 구분되어 아래에 나열되어 있습니다.

## SQL 데이터베이스 V12에서 부분적으로 지원되는 기능

SQL 데이터베이스 V12에서는 해당 SQL Server 2016 TRANSACT-SQL 문에 있는 인수가 일부는 지원되고 일부는 지원되지 않습니다. 예를 들어 CREATE PROCEDURE 문은 사용할 수 있지만 CREATE PROCEDURE의 WITH ENCRYPTION 옵션은 사용할 수 없습니다. 각 문의 지원되는 영역에 대한 자세한 내용은 연결된 구문 항목을 참조하세요.

- CLR 어셈블리: [CREATE ASSEMBLY](https://msdn.microsoft.com/library/ms189524.aspx)
- 데이터베이스: [CREATE](https://msdn.microsoft.com/library/dn268335.aspx)/[ALTER](https://msdn.microsoft.com/library/ms174269.aspx)
- DMV는 사용할 수 있는 기능에 대해 일반 공급됩니다.
- 함수: [CREATE](https://msdn.microsoft.com/library/ms186755.aspx)/[ALTER FUNCTION](https://msdn.microsoft.com/library/ms186967.aspx)
- [KILL](https://msdn.microsoft.com/library/ms173730.aspx) 
- 로그인: [CREATE](https://msdn.microsoft.com/library/ms189751.aspx)/[ALTER LOGIN](https://msdn.microsoft.com/library/ms189828.aspx)
- 저장 프로시저: [CREATE](https://msdn.microsoft.com/library/ms187926.aspx)/[ALTER PROCEDURE](https://msdn.microsoft.com/library/ms189762.aspx)
- 테이블: [CREATE](https://msdn.microsoft.com/library/dn305849.aspx)/[ALTER](https://msdn.microsoft.com/library/ms190273.aspx)
- 형식(사용자 지정): [CREATE TYPE](https://msdn.microsoft.com/library/ms175007.aspx)
- 사용자: [CREATE](https://msdn.microsoft.com/library/ms173463.aspx)/[ALTER USER](https://msdn.microsoft.com/library/ms176060.aspx)
- 보기: [CREATE](https://msdn.microsoft.com/library/ms187956.aspx)/[ALTER VIEW](https://msdn.microsoft.com/library/ms173846.aspx)

## SQL 데이터베이스에서 지원되지 않는 기능

- 시스템 개체의 데이터 정렬
- 연결 관련: 끝점 문, ORIGINAL\_DB\_NAME. 로그인 또는 포함된 데이터베이스 사용자에 대해 Windows 인증을 사용할 수 없습니다.
- 데이터베이스 간 쿼리, 데이터베이스 간 소유권 체인, TRUSTWORTHY 설정
- 데이터 수집기
- 데이터베이스 다이어그램
- 데이터베이스 메일
- DATABASEPROPERTY(대신 DATABASEPROPERTYEX 사용)
- 분산 트랜잭션
- EXECUTE AS 로그인
- 암호화: 확장 가능 키 관리
- 이벤트: 이벤트, 이벤트 알림, 쿼리 알림
- 데이터베이스 파일 배치, 크기 및 Microsoft Azure에서 자동으로 관리되는 데이터베이스 파일과 관련된 기능
- Microsoft Azure 계정을 통해 관리되는 고가용성과 관련된 기능: 백업, 복원, AlwaysOn, 데이터베이스 미러링, 로그 전달, 복구 모드. 자세한 내용은 Azure SQL 데이터베이스 백업 및 복원을 참조하세요.
- 로그 판독기를 사용하는 기능: 복제, 데이터 캡처 변경
- SQL Server 에이전트 또는 MSDB 데이터베이스를 사용하는 기능: 작업, 경고, 운영자, 정책 기반 관리, 데이터베이스 메일, 중앙 관리 서버
- FILESTREAM
- 함수: fn\_get\_sql, fn\_virtualfilestats, fn\_virtualservernodes
- 전역 임시 테이블
- 하드웨어 관련 서버 설정: 메모리, 작업자 스레드 수, CPU 선호도, 추적 플래그 등. 대신 서비스 수준을 사용합니다.
- HAS\_DBACCESS
- KILL STATS JOB
- 연결된 서버, OPENQUERY, OPENROWSET, OPENDATASOURCE, BULK INSERT, 3개 및 4개 부분으로 이루어진 이름
- 마스터/대상 서버
- 리소스 관리자
- 의미 체계 검색
- 서버 자격 증명
- 서버 수준 항목: 서버 역할, IS\_SRVROLEMEMBER, sys.login\_token. 서버 수준 사용 권한은 사용할 수 없지만 일부는 데이터베이스 수준 사용 권한으로 대체됩니다. 일부 서버 수준 DMV는 사용할 수 없지만 일부는 데이터베이스 수준 DMV로 대체됩니다.
- 서버가 없는 express: localdb, 사용자 인스턴스
- Service Broker
- SET REMOTE\_PROC\_TRANSACTIONS
- SHUTDOWN
- sp\_addmessage
- sp\_configure 옵션 및 RECONFIGURE
- sp\_helpuser
- sp\_migrate\_user\_to\_contained
- SQL Server 감사(대신 SQL 데이터베이스 감사 사용)
- SQL Server Profiler
- SQL Server 추적
- 추적 플래그
- TRANSACT-SQL 디버깅
- 트리거: 서버 범위 또는 로그온 트리거
- USE 문

## 전체 TRANSACT-SQL 참조

TRANSACT-SQL 문법, 사용법 및 예제에 대한 자세한 내용은 SQL Server 온라인 설명서의 [TRANSACT-SQL 참조(데이터베이스 엔진)](https://msdn.microsoft.com/library/bb510741.aspx)를 참조하세요.

### '적용 대상' 태그 정보

TRANSACT-SQL 참조에는 SQL Server 2008, SQL Server 2008 R2, SQL Server 2012, SQL Server 2014 및 Microsoft Azure SQL 데이터베이스와 관련된 항목이 포함됩니다. 각 항목의 위쪽에는 항목의 주제를 지원하는 제품을 나타내는 섹션이 있습니다. 제품이 생략된 경우 항목에서 설명하는 기능을 해당 제품에서 사용할 수 없습니다. 예를 들어 가용성 그룹은 SQL Server 2012에서 도입되었습니다. **가용성 그룹 만들기** 항목에는 SQL Server 2008, SQL Server 2008 R2 또는 Microsoft Azure SQL 데이터베이스에 적용되지 않으므로 **SQL Server(SQL Server 2012부터 현재 버전까지)**에 적용된다고 표시됩니다.

항목의 일반적인 주제는 제품에서 사용할 수 있지만 일부 인수가 지원되지 않는 경우도 있습니다. 예를 들어 포함된 데이터베이스 사용자는 SQL Server 2012에서 도입되었습니다. **CREATE USER** 문은 모든 SQL Server 제품에서 사용할 수 있지만 **WITH PASSWORD** 구문은 이전 버전에서 사용할 수 없습니다. 이 경우 **적용 대상** 섹션이 항목 본문의 해당 인수 설명에 추가로 삽입됩니다.

<!---HONumber=Nov15_HO2-->