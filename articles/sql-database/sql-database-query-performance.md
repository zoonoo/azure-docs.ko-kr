<properties 
   pageTitle="Azure SQL 데이터베이스 쿼리 성능 Insight" 
   description="쿼리 성능 모니터링은 Azure SQL 데이터베이스에 대한 대부분의 CPU 사용 쿼리를 식별합니다." 
   services="sql-database" 
   documentationCenter="" 
   authors="stevestein" 
   manager="jhubbard" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management" 
   ms.date="05/05/2016"
   ms.author="sstein"/>

# Azure SQL 데이터베이스 쿼리 성능 Insight


관련 데이터베이스의 성능을 관리하고 튜닝하는 것은 많은 전문 지식과 시간 투자를 필요로 하는 어려운 일입니다. 쿼리 성능 Insight를 통해 다음을 제공하여 데이터베이스 성능 문제 해결 시간을 줄일 수 있습니다.

- 데이터베이스 리소스(DTU) 사용에 대한 보다 자세한 정보를 확인합니다.
- 최상위 CPU 사용 쿼리는 향상된 성능을 위해 잠재적으로 조정될 수 있습니다.
- 쿼리에 대한 세부 정보로 드릴다운할 수 있습니다.

## 필수 조건

- 쿼리 성능 Insight는 Azure SQL 데이터베이스 V12에서만 제공됩니다.
- Query Performance Insight를 위해서는 데이터베이스에 [쿼리 저장소](https://msdn.microsoft.com/library/dn817826.aspx)가 실행 중이어야 합니다. 포털에서 쿼리 저장소가 아직 실행되지 않은 경우 설정하라는 메시지가 표시됩니다.

 
## 권한

쿼리 성능 Insight를 사용하려면 다음 [역할 기반 액세스 제어](../active-directory/role-based-access-control-configure.md) 권한이 필요합니다.

- 최상위 리소스 사용 쿼리 및 차트를 보려면 **판독기**, **소유자**, **참여자**, **SQL DB 참여자** 또는 **SQL Server 참여자** 권한이 필요합니다.
- 쿼리 텍스트를 보려면 **소유자**, **참여자**, **SQL DB 참여자** 또는 **SQL Server 참여자** 권한이 필요합니다.



## 쿼리 성능 Insight 사용

쿼리 성능 Insight는 쉽게 사용할 수 있습니다.

- 최상위 리소스 사용 쿼리의 목록을 검토합니다.
- 해당하는 세부 정보를 보려면 개별 쿼리를 선택합니다.
- [SQL 데이터베이스 관리자](sql-database-advisor.md)를 열고 권장 사항을 사용할 수 있는지 확인합니다.
- 자세한 내용을 알아보세요.

    ![성능 대시보드](./media/sql-database-query-performance/performance.png)

> [AZURE.NOTE] 쿼리 성능 Insight를 제공하는 SQL 데이터베이스용 쿼리 저장소로 데이터를 캡처하는데 몇 시간이 필요합니다. 데이터베이스에 아무런 작업이 없거나 쿼리 저장소가 특정 기간 동안 비활성 상태였던 경우 해당 기간을 표시할 때 차트가 비어 있게 됩니다. 실행하지 않는 경우 언제든지 쿼리 저장소를 활성화할 수 있습니다.



## 최상위 CPU 소비 쿼리 검토

[포털](http://portal.azure.com)에서 다음을 수행합니다.

1. SQL 데이터베이스를 찾고 **모든 설정** > **성능** > **쿼리**를 클릭합니다.

    ![쿼리 성능 Insight][1]

    최상위 쿼리 뷰가 열리고 최상위 CPU 사용 쿼리가 나열됩니다.

1. 차트 주위를 클릭하여 세부 정보를 확인합니다.<br>위쪽 줄에는 데이터베이스에 대한 전체 DTU%가 표시되고 막대에는 선택한 기간 중에 선택한 쿼리에서 사용된 CPU%가 표시됩니다. 예를 들어 **지난주**를 선택하면 각 막대는 1일을 나타냅니다.

    ![최상위 쿼리][2]

    아래 그리드는 표시되는 쿼리에 대해 집계된 정보를 나타냅니다.

    -	쿼리 ID – 데이터베이스 내 쿼리의 고유 식별자입니다.
    -	예측 가능한 간격 동안 쿼리당 CPU입니다(집계 함수에 따라 다름).
    -	쿼리당 기간입니다(집계 함수에 따라 다름).
    -	특정 쿼리에 대한 총 실행 수입니다.


	개별 쿼리를 선택하거나 지워 차트에서 포함하거나 제외합니다.


1. 데이터가 최신 상태가 아닌 경우 **새로 고침** 단추를 클릭합니다.
1. 필요에 따라 **설정**을 클릭하여 CPU 사용 데이터 표시 방법을 사용자 지정하거나 다른 기간을 표시합니다.

    ![설정](./media/sql-database-query-performance/settings.png)

## 개별 쿼리 세부 정보 보기

쿼리 세부 정보를 보려면:

1. 맨 위 쿼리 목록에서 쿼리를 클릭합니다.

    ![세부 정보](./media/sql-database-query-performance/details.png)

4. 세부 정보 보기가 열리고 쿼리 CPU 사용이 시간에 따라 분석됩니다.
3. 세부 정보는 차트 주위를 클릭합니다.<br>위쪽 줄은 전체 DTU%이고 막대는 선택한 쿼리에서 사용된 CPU%입니다.
4. 데이터를 검토하여 쿼리가 실행된 각 간격에 대한 기간, 실행 횟수 및 리소스 사용률 비율을 포함한 세부 메트릭을 봅니다.
    
    ![쿼리 세부 정보][3]

1. 필요에 따라 **설정**을 클릭하여 CPU 사용 데이터 표시 방법을 사용자 지정하거나 다른 기간을 표시합니다.


## 	Query Performance Insight에 대한 쿼리 저장소 구성 최적화

Query Performance Insight를 사용하는 동안 다음 쿼리 저장소 메시지가 나타날 수 있습니다.

- "쿼리 저장소의 용량이 다 찼으므로 새 데이터를 수집하지 않습니다."
- "이 데이터베이스의 쿼리 저장소가 읽기 전용 모드이므로 Performance Insight 데이터를 수집하지 않습니다."
- "쿼리 저장소 매개 변수가 Query Performance Insight에 대해 최적으로 설정되지 않았습니다."

이러한 메시지는 일반적으로 쿼리 저장소가 새 데이터를 수집할 수 없을 때 표시됩니다. 이 문제를 해결하려면 두 가지 옵션이 있습니다.

-	쿼리 저장소의 보존 및 캡처 정책 변경
-	쿼리 저장소 크기 늘리기
-	쿼리 저장소 지우기

### 권장된 보존 및 캡처 정책

보존 정책에는 다음과 같은 두 종류가 있습니다.

- 크기 기반 - AUTO로 설정된 경우 최대 크기에 가까워지면 데이터를 자동으로 지웁니다.
- 시간 기반 - 기본적으로 30일로 설정됩니다. 즉, 쿼리 저장소의 공간이 부족해지면 30일이 지난 쿼리 정보를 삭제합니다.

캡처 정책은 다음과 같이 설정할 수 있습니다.

- **모두** – 모든 쿼리를 캡처합니다. 기본 옵션입니다.
- **자동** – 컴파일 및 실행 기간이 중요하지 않고 사용 빈도가 적은 쿼리가 무시됩니다. 실행 횟수, 컴파일 및 런타임 기간에 대한 임계값을 내부적으로 결정합니다.
- **없음** – 쿼리 저장소가 새 쿼리를 캡처하는 것을 중지합니다.
	
모든 정책을 AUTO로 설정하고 클린 정책을 30일로 설정하는 것이 좋습니다.

    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (SIZE_BASED_CLEANUP_MODE = AUTO);
    	
    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (CLEANUP_POLICY = (STALE_QUERY_THRESHOLD_DAYS = 30));
    
    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (QUERY_CAPTURE_MODE = AUTO);

쿼리 저장소의 크기를 늘립니다. 이는 데이터베이스에 연결하고 다음 쿼리를 실행하여 수행할 수 있습니다.

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (MAX_STORAGE_SIZE_MB = 1024);

쿼리 저장소를 지웁니다. 그러면 쿼리 저장소의 모든 현재 정보가 삭제됩니다.

    ALTER DATABASE [YourDB] SET QUERY_STORE CLEAR;


## 요약

쿼리 성능 Insight를 통해 쿼리 작업의 영향 및 데이터베이스 리소스 사용의 관계를 이해할 수 있습니다. 이 기능을 사용하여 최상위 사용 쿼리를 확인하고 문제가 되기 전에 해결할 쿼리를 쉽게 식별할 수 있습니다.




## 다음 단계

SQL 데이터베이스의 성능 향상을 위한 추가 권장 사항은 **쿼리 성능 Insight** 블레이드의 [SQL 데이터베이스 관리자](sql-database-advisor.md)를 클릭하여 확인합니다.

![성능 관리자](./media/sql-database-query-performance/ia.png)


<!--Image references-->
[1]: ./media/sql-database-query-performance/tile.png
[2]: ./media/sql-database-query-performance/top-queries.png
[3]: ./media/sql-database-query-performance/query-details.png

<!---HONumber=AcomDC_0629_2016-->