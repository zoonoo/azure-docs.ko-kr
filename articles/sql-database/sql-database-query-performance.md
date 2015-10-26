<properties 
   pageTitle="Azure SQL 데이터베이스 쿼리 성능 Insight" 
   description="쿼리 성능 모니터링은 Azure SQL 데이터베이스에 대한 대부분의 DTU 사용 쿼리를 식별합니다." 
   services="sql-database" 
   documentationCenter="" 
   authors="stevestein" 
   manager="jeffreyg" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management" 
   ms.date="09/30/2015"
   ms.author="sstein"/>

# Azure SQL 데이터베이스 쿼리 성능 Insight


관련 데이터베이스의 성능을 관리하고 튜닝하는 것은 많은 전문 지식과 시간 투자를 필요로 하는 어려운 일입니다. 쿼리 성능 Insight를 통해 다음을 제공하여 데이터베이스 성능 문제 해결 시간을 줄일 수 있습니다.

- 데이터베이스 리소스(DTU) 사용에 대한 보다 자세한 정보를 확인합니다. 
- 최상위 DTU 사용 쿼리는 향상된 성능을 위해 잠재적으로 조정될 수 있습니다. 
- 쿼리에 대한 세부 정보로 드릴다운할 수 있습니다.

> [AZURE.NOTE]쿼리 성능 Insight는 현재 미리 보기로 제공되며 [Azure 미리 보기 포털](https://portal.azure.com/)에서만 사용할 수 있습니다.



## 필수 조건

- 쿼리 성능 Insight는 Azure SQL 데이터베이스 V12에서만 제공됩니다.
- 쿼리 성능 Insight는 [쿼리 저장소](https://msdn.microsoft.com/library/dn817826.aspx) 실행이 필요하므로 쿼리 성능 Insight에 등록할 때 자동으로 활성화됩니다.
 
 
## 권한

쿼리 성능 Insight를 사용하려면 다음 [역할 기반 액세스 제어](role-based-access-control-configure.md) 권한이 필요합니다.

- 최상위 리소스 사용 쿼리 및 차트를 보려면 **판독기**, **소유자**, **참여자**, **SQL DB 참여자** 또는 **SQL Server 참여자** 권한이 필요합니다. 
- 쿼리 텍스트를 보려면 **소유자**, **참여자**, **SQL DB 참여자** 또는 **SQL Server 참여자** 권한이 필요합니다.



## 쿼리 성능 Insight 사용

쿼리 성능 Insight는 쉽게 사용할 수 있습니다.

- 최상위 리소스 사용 쿼리의 목록을 검토합니다. 
- 세부 정보를 보려면 개별 쿼리를 선택합니다.
- **차트를 편집**하여 DTU 사용 데이터 표시 방법을 사용자 지정하거나 다른 기간을 표시합니다.



> [AZURE.NOTE]쿼리 성능 Insight를 제공하는 SQL 데이터베이스용 쿼리 저장소로 데이터를 캡처하는데 몇 시간이 필요합니다. 데이터베이스에 아무런 작업이 없거나 쿼리 저장소가 특정 기간 동안 비활성 상태였던 경우 해당 기간을 표시할 때 차트가 비어 있게 됩니다. 실행하지 않는 경우 언제든지 쿼리 저장소를 활성화할 수 있습니다.





## 최상위 DTU 소비 쿼리 검토

[미리 보기 포털](https://portal.azure.com)에서 다음을 수행합니다.

1. SQL 데이터베이스를 찾아 **쿼리 성능 Insight**를 클릭합니다. 

    ![쿼리 성능 Insight][1]

    최상위 쿼리 뷰가 열리고 최상위 DTU 사용 쿼리의 목록이 나열됩니다.

1. 세부 정보는 차트 주위를 클릭합니다.<br>위쪽 줄은 데이터베이스에 대한 전체 DTU%를 표시하고 막대는 선택한 쿼리에서 사용된 DTU%를 보여 줍니다. 개별 쿼리를 선택하거나 지워 차트에서 포함하거나 제외합니다.

    ![최상위 쿼리][2]

1. 필요에 따라 **차트 편집**을 클릭하여 DTU 사용 데이터 표시 방법을 사용자 지정하거나 다른 기간을 표시합니다.

## 개별 쿼리 세부 정보 보기

쿼리 세부 정보를 보려면:

1. 최상위 쿼리 목록에서 모든 쿼리를 클릭합니다.<br>세부 정보 보기가 열리고 쿼리 DTU 사용이 시간이 지남에 따라 분리됩니다. 
3. 세부 정보는 차트 주위를 클릭합니다.<br>위쪽 줄은 전체 DTU%이고 막대는 선택한 쿼리에서 사용된 DTU%입니다.
4. 데이터를 검토하여 쿼리가 실행된 각 간격에 대한 기간, 실행 횟수, 리소스 사용률 비율을 포함한 세부 메트릭을 봅니다.
    
    ![쿼리 세부 정보][3]

1. 필요에 따라 **스크립트 보기**를 클릭하여 쿼리 텍스트를 보고 **차트 편집**을 클릭하여 DTU 사용 데이터 표시 방법을 사용자 지정하거나 다른 기간을 표시합니다.




## 요약

쿼리 성능 Insight를 통해 쿼리 작업의 영향 및 데이터베이스 리소스 사용의 관계를 이해할 수 있습니다. 이 기능을 사용하여 최상위 사용 쿼리를 확인하고 문제가 되기 전에 해결할 쿼리를 쉽게 식별할 수 있습니다. 데이터베이스 블레이드의 **쿼리 성능 Insight** 타일을 클릭하여 최상위 리소스(DTU) 소비 쿼리를 확인합니다.




## 다음 단계

데이터베이스 워크로드는 동적 이며 지속적으로 변경합니다. 쿼리를 모니터링하고 쿼리를 미세 조정하여 성능을 구체화합니다.

SQL 데이터베이스의 성능 향상을 위해 추가 권장 사항에 대해 [인덱스 관리자](sql-database-index-advisor.md)를 확인합니다.

<!--Image references-->
[1]: ./media/sql-database-query-performance/tile.png
[2]: ./media/sql-database-query-performance/top-queries.png
[3]: ./media/sql-database-query-performance/query-details.png

<!---HONumber=Oct15_HO3-->