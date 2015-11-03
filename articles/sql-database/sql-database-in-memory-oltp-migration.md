<properties
	pageTitle="메모리 내 OLTP을 사용하여 Azure SQL 트랜잭션 성능 향상 | Microsoft Azure"
	description="메모리 내 OLTP를 채택하여 기존 SQL 데이터베이스의 트랜잭션 성능을 향상합니다."
	services="sql-database"
	documentationCenter=""
	authors="jodebrui"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="10/28/2015"
	ms.author="jodebrui"/>


# 메모리 내를 사용하여(미리 보기) SQL Azure 응용 프로그램의 성능 향상

[메모리 내](sql-database-in-memory.md)를 사용하여 기존 Premium Azure SQL 데이터베이스의 트랜잭션 성능을 최적화하려면 다음 단계를 수행합니다.

비교를 위해 유사한 동시 연결량 및 읽기/쓰기 비율을 가진 프로덕션 워크로드에 비슷한 워크로드를 선택합니다. 네트워크 대기 시간을 최소화하려면 데이터베이스와 동일한 Azure 지역에서 테스트 워크로드를 실행하는 것이 좋습니다.

## 1단계: 새 Premium 데이터베이스에 데이터 복사
1.	다음 중 하나를 사용하여 프로덕션 데이터베이스를 내보냅니다.

	A. [포털](https://portal.azure.com/)에서 내보내기 기능 또는

	B. SQL Server Management Studio에서 데이터 계층 응용 프로그램 내보내기 기능

2.	V12 서버의 새로운 Premium 데이터베이스로 bacpac 가져오기

	A. 포털에서: 서버로 이동하고 데이터베이스 가져오기 옵션을 선택합니다. Premium 가격 책정 계층을 선택하도록 합니다.

	B. SQL Server Management studio(SSMS): 서버에 연결하고 데이터베이스 노드를 마우스 오른쪽 단추로 클릭하며 데이터 계층 응용 프로그램 가져오기를 선택합니다.


## 2단계: 개체를 식별하여 메모리 내 OLTP로 마이그레이션
SQL Server Management Studio(SSMS)는 활성 워크로드가 있는 데이터베이스에 대해 실행할 수 있는 트랜잭션 성능 분석 보고서를 포함하여 테이블 및 저장 프로시저 메모리 내 OLTP로 마이그레이션하기 위한 후보를 식별합니다. 자세한 내용은 [테이블 또는 저장 프로시저가 메모리 내 OLTP로 이식되어야 하는지 결정](https://msdn.microsoft.com/library/dn205133.aspx)을 참조합니다.

1.	SSMS를 사용하여 프로덕션 데이터베이스에 연결합니다. 또는 새 테스트 데이터베이스에 대해 실행 중인 워크로드가 있는 경우 해당 워크로드에 연결할 수 있습니다.
2.	데이터베이스를 마우스 오른쪽 단추로 클릭하고 보고서 -> 표준 보고서-> 트랜잭션 성능 분석 보고서를 선택합니다. 보고서를 사용하면 사용량에 따라 메모리 내 OLTP에서 이점을 얻을 수 있는 테이블 및 저장 프로시저를 식별합니다.


## 3단계: 테이블 마이그레이션
1.	SSMS를 사용하여 새 테스트 데이터베이스에 연결합니다. 쿼리에서 WITH(SNAPSHOT) 옵션을 사용할 필요성을 방지하려면 MEMORY\_OPTIMIZED\_ELEVATE\_TO\_SNAPSHOT 데이터베이스 옵션을 설정하는 것이 좋습니다.
2.	새 테스트 데이터베이스에 연결되면 다음을 실행합니다.

   	    ALTER DATABASE CURRENT SET MEMORY_OPTIMIZED_ELEVATE_TO_SNAPSHOT=ON

3.	이러한 방법 중 하나를 통해 메모리에 최적화하기 위해 디스크 기반 테이블을 마이그레이션합니다.

	A. SSMS 메모리 최적화 마법사: 테스트 데이터베이스에 연결하는 경우 테이블을 마우스 오른쪽 단추로 클릭하고 메모리 최적화 관리자를 선택합니다. 관리자를 사용하여 테이블에 메모리에 최적화되게 지원하는 기능이 있는지 결정합니다. 그렇지 않은 경우 관리자는 실제 스키마 및 데이터 마이그레이션을 수행할 수 있습니다. 자세한 내용은 [MSDN의 메모리 최적화 관리자 항목](https://msdn.microsoft.com/library/dn284308.aspx)을 검토합니다.

	B. 수동 마이그레이션: SSMS를 사용하여 새 테스트 데이터베이스에 연결합니다.

테이블을 마이그레이션하려면 다음 단계를 수행합니다.

1.	테이블을 마우스 오른쪽 단추로 클릭하여 테이블을 스크립팅하고 테이블을 다음으로 스크립팅 -> 에 만들기 -> 새 쿼리 창을 선택합니다.
2.	CLUSTERED 인덱스를 NONCLUSTERED로 변경하고 (MEMORY\_OPTIMIZED=ON)를 사용하여 옵션을 추가합니다.
3.	테이블에서 지원되지 않는 기능을 사용하면 해결 방법을 구현합니다. MSDN는 [공통으로 지원되지 않는 기능](https://msdn.microsoft.com/library/dn247639.aspx)을 처리하는 방법을 설명합니다.
4.	sp\_rename을 사용하여 기존 테이블의 이름을 바꿉니다.
5.	CREATE TABLE 스크립트를 실행하여 새 메모리에 최적화된 테이블을 만듭니다.
6.	다음 문을 실행하여 데이터를 복사합니다.: ``INSERT INTO <new_memory_optimized_table> SELECT * FROM <old_disk_based_table>

## 4단계(선택 사항): 저장 프로시저 마이그레이션

[SQL Server Management Studio(SSMS)](https://msdn.microsoft.com/library/mt238290.aspx) 2015년 9월 미리 보기 이상을 사용하여 새 테스트 데이터베이스에 연결합니다.

이전 프로시저에서 [네이티브 컴파일 관리자](https://msdn.microsoft.com/library/dn284308.aspx)를 실행하여 고유하게 컴파일된 저장 프로시저에서 지원되지 않는 모든 기능을 식별합니다. 일반적으로 지원되지 않는 기능에 대한 해결 방법은 [MSDN](https://msdn.microsoft.com/library/dn296678.aspx)에 설명됩니다.

저장 프로시저를 네이티브로 마이그레이션할 때 고려할 두 가지 사항입니다.

- 네이티브 모듈에는 다음과 같은 옵션이 필요합니다.

	- NATIVE\_COMPILATION
	- SCHEMABINDING



- 네이티브 모듈은 트랜잭션 관리에 [ATOMIC 블록](https://msdn.microsoft.com/library/dn452281.aspx)을 사용합니다. 명시적 BEGIN TRAN/COMMIT/ROLLBACK 문이 필요하지 않습니다.

일반적인 고유하게 컴파일된 저장 프로시저는 다음과 같아 보입니다.


   	    CREATE PROCEDURE schemaname.procedurename
   		@param1 type1, …
   		WITH NATIVE_COMPILATION, SCHEMABINDING
   		AS
   		BEGIN ATOMIC WITH (TRANSACTION ISOLATION LEVEL = SNAPSHOT, LANGUAGE = N'your_language')

   		…

   		END



스냅숏은 메모리에 최적화된 테이블을 사용하여 가장 일반적으로 사용되는 격리 수준입니다. 또한 반복 가능한 읽기 및 직렬화 가능도 지원됩니다.

##### 저장 프로시저를 마이그레이션하려면 다음 단계를 수행합니다.

1.	프로시저를 마우스 오른쪽 단추로 클릭하고 로 프로시저 스크립팅 -> 에 만들기 -> 새 쿼리 창를 선택하여 이전 저장 프로시저를 스크립팅합니다.
2.	위의 템플릿을 사용하여 프로시저 헤더를 다시 작성하고 전송 시작/롤백/커밋 문을 제거합니다.
3.	저장 프로시저에서 지원되지 않는 기능을 사용하면 해결 방법을 구현합니다. MSDN는 [공통으로 지원되지 않는 기능](https://msdn.microsoft.com/library/dn296678.aspx)을 처리하는 방법을 설명합니다.
4.	sp\_rename을 사용하여 프로시저를 삭제하거나 이전 프로시저 이름을 변경합니다.
5.	CREATE PROCEDURE 스크립트를 실행하여 새 고유하게 컴파일된 저장 프로시저를 만듭니다.

## 5단계: 워크로드 실행
메모리에 최적화된 테이블과 고유하게 컴파일된 저장 프로시저에 대해 테스트 워크로드를 실행하고 성능 향상을 측정합니다.

## 다음 단계

[메모리 내 저장소 모니터링](https://azure.microsoft.com/documentation/articles/sql-database-in-memory-oltp-monitoring/).

[동적 관리 뷰를 사용하여 Azure SQL 데이터베이스 모니터링](sql-database-monitoring-with-dmvs.md)

<!---HONumber=Nov15_HO1-->