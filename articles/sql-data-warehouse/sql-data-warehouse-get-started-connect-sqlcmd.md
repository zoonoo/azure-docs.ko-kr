<properties
   pageTitle="Azure SQL 데이터 웨어하우스 쿼리(sqlcmd) | Microsoft Azure"
   description="sqlcmd 명령줄 유틸리티를 사용하여 Azure SQL 데이터 웨어하우스를 쿼리합니다."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="07/22/2016"
   ms.author="mausher;barbkess;sonyama"/>

# Azure SQL 데이터 웨어하우스 쿼리(sqlcmd)

> [AZURE.SELECTOR]
- [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
- [Azure 기계 학습](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
- [Visual Studio](sql-data-warehouse-query-visual-studio.md)
- [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md)

이 연습에서는 sqlcmd 명령줄 유틸리티를 사용하여 Azure SQL 데이터 웨어하우스를 쿼리합니다.

## 필수 조건

이 자습서를 단계별로 실행하려면 다음을 수행해야 합니다.

-  [sqlcmd.exe][]. 다운로드하려면 [SQL Server Windows용 Microsoft ODBC 드라이버 11][]이 필요할 수도 있는 [SQL Server용 Microsoft 명령줄 유틸리티 11][]을 참조하세요.

## 1\. 연결

sqlcmd를 시작하려면 명령 프롬프트를 열고 SQL 데이터 웨어하우스 데이터베이스에 대한 연결 문자열 뒤에 **sqlcmd**를 입력합니다. 연결 문자열은 다음 필수 매개 변수를 포함해야 합니다.

+ **서버(-S):**`<`서버 이름`>`.database.windows.net 형식의 서버
+ **데이터베이스(-D):** 데이터베이스 이름
+ **사용자 (-U):** `<`사용자`>` 형태의 서버 사용자
+ **암호(-P):** 사용자와 연결된 암호
+ **따옴표 붙은 식별자 설정(-I):** SQL 데이터 웨어하우스 인스턴스에 연결하기 위해 따옴표 붙은 식별자를 사용할 수 있어야 합니다.

예를 들어 연결 문자열은 다음과 같습니다.

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
```

> [AZURE.NOTE] 따옴표 붙은 식별자를 설정하는 -I 옵션은 현재 SQL 데이터 웨어하우스에 연결해야 합니다.

## 2\. 쿼리

연결 후, 인스턴스에 대해 지원되는 모든 TRANSACT-SQL 문을 실행할 수 있습니다. 이 예에서 쿼리는 대화형 모드로 전송됩니다.

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
1> SELECT name FROM sys.tables;
2> GO
3> QUIT
```

다음 예제에서는 -Q 옵션을 사용하거나 sqlcmd에 SQL을 파이핑하여 배치 모드에서 쿼리를 실행하는 방법을 보여 줍니다.

```sql
sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I -Q "SELECT name FROM sys.tables;"
```

```sql
"SELECT name FROM sys.tables;" | sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I > .\tables.out
```

## 다음 단계

sqlcmd에서 사용할 수 있는 옵션에 대한 세부 정보는 [sqlcmd 설명서][sqlcmd.exe]를 참조하세요.

<!--Image references-->

<!--Article references-->

<!--MSDN references--> 
[sqlcmd.exe]: https://msdn.microsoft.com/library/ms162773.aspx
[SQL Server Windows용 Microsoft ODBC 드라이버 11]: https://www.microsoft.com/download/details.aspx?id=36434
[SQL Server용 Microsoft 명령줄 유틸리티 11]: http://go.microsoft.com/fwlink/?LinkId=321501
[Azure portal]: https://portal.azure.com

<!--Other Web references-->

<!---HONumber=AcomDC_0727_2016-->