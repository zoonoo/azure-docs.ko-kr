<properties
   pageTitle="SQL 데이터 웨어하우스 | Microsoft Azure의 동적 SQL"
   description="솔루션 개발을 위한 Azure SQL 데이터 웨어하우스의 동적 SQL 사용 팁."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/26/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# SQL 데이터 웨어하우스의 동적 SQL
SQL 데이터 웨어하우스용 응용 프로그램 코드를 개발할 때 유연하고 일반적인 모듈식 솔루션을 제공하기 위해 동적 SQL을 사용해야 할 수 있습니다. 그러나 SQL 데이터 웨어하우스는 현재 Blob 데이터 형식을 지원하지 않습니다. 따라서 Blob 유형에 varchar(max) 및 nvarchar(max) 형식이 모두 포함되므로 문자열 크기가 제한될 수 있습니다. 실행해야 할 동적 SQL 코드의 매우 큰 문자열을 빌드할 때 응용 프로그램 코드에 이러한 형식을 사용한 것을 발견할 수 있습니다.

이러한 상황에서는 코드를 청크로 분할하고 EXEC 문을 대신 사용해야 합니다.

간단한 예를 아래에 제공합니다.

```
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

문자열은 특이하게 길지 않은 경우 일반적으로 [sp_executesql][]을 사용할 수 있습니다.


## 다음 단계
더 많은 개발 팁은 [개발 개요][]를 참조하세요.

<!--Image references-->

<!--Article references-->
[개발 개요]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[sp_executesql]: https://msdn.microsoft.com/ko-kr/library/ms188001.aspx

<!--Other Web references-->

<!---HONumber=July15_HO3-->