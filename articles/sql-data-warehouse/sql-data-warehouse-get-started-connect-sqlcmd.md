<properties
   pageTitle="시작: Azure SQL 데이터 웨어하우스에 연결 | Microsoft Azure"
   description="SQL 데이터 웨어하우스에 연결 및 일부 쿼리 실행 시작"
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
   ms.date="05/16/2016"
   ms.author="mausher;barbkess;sonyama"/>

# SQLCMD를 사용하여 연결 및 쿼리

> [AZURE.SELECTOR]
- [Visual Studio][]
- [SQLCMD][]

이 연습에서는 sqlcmd.exe 유틸리티를 사용하여 Azure SQL 데이터 웨어하우스 데이터베이스를 몇 분 이내에 연결하고 쿼리하는 방법을 보여줍니다. 이 연습에서는 다음을 수행합니다.

+ 필수 조건 소프트웨어 설치
+ AdventureWorksDW 샘플 데이터베이스를 포함하는 데이터베이스에 연결
+ 샘플 데이터베이스에 대한 쿼리 실행  

## 필수 조건

+ [sqlcmd.exe][]를 다운로드하려면 [SQL Server용 Microsoft 명령줄 유틸리티 11][]을 참조하세요.

## 정규화된 SQL Azure 서버 이름 가져오기

데이터베이스에 연결하려면 연결하려는 데이터베이스를 포함하는 서버의 전체 이름(****servername**.database.windows.net*)이 필요합니다.

1. [Azure 포털][]로 이동합니다.
2. 연결하려는 데이터베이스를 찾습니다.
3. 전체 서버 이름을 찾습니다.(다음 단계에서 사용)

![][1]


## sqlcmd를 사용하여 SQL 데이터 웨어하우스에 연결

sqlcmd를 사용하는 경우 SQL 데이터 웨어하우스의 특정 인스턴스에 연결하려면 명령 프롬프트를 열고 SQL 데이터 웨어하우스 데이터베이스에 대한 연결 문자열이 뒤에 나오는 **sqlcmd**를 입력합니다. 연결 문자열은 다음 필수 매개 변수를 포함해야 합니다.

+ **서버(-S):**`<`서버 이름`>`.database.windows.net 형식의 서버
+ **데이터베이스(-D):** 데이터베이스 이름
+ **사용자 (-U):** `<`사용자`>` 형태의 서버 사용자
+ **암호(-P):** 사용자와 연결된 암호
+ **따옴표 붙은 식별자 설정(-I):** SQL 데이터 웨어하우스 인스턴스에 연결하기 위해 따옴표 붙은 식별자를 사용할 수 있어야 합니다.

따라서 SQL 데이터 웨어하우스 인스턴스에 연결하려면 다음을 입력합니다.

```sql
C:\>sqlcmd -S <Server Name>.database.windows.net -d <Database> -U <User> -P <Password> -I
```

## 예제 쿼리 실행

연결 후, 인스턴스에 대해 지원되는 모든 TRANSACT-SQL 문을 실행할 수 있습니다.

```sql
C:\>sqlcmd -S <Server Name>.database.windows.net -d <Database> -U <User> -P <Password> -I
1> SELECT name FROM sys.tables;
2> GO
3> QUIT
```

Sqlcmd 대한 추가 정보는 [sqlcmd 설명서][sqlcmd.exe]를 참조하세요.


## 다음 단계

이제 연결 및 쿼리할 수 있으므로 [PowerBI로 연결][]해 보세요.

Windows 인증을 위한 환경을 구성하려면 [Azure Active Directory 인증을 사용하여 SQL 데이터베이스 또는 SQL 데이터 웨어하우스에 연결][]을 참조하세요.

<!--Articles-->
[Azure Active Directory 인증을 사용하여 SQL 데이터베이스 또는 SQL 데이터 웨어하우스에 연결]: ../sql-database/sql-database-aad-authentication.md
[PowerBI로 연결]: ./sql-data-warehouse-integrate-power-bi.md
[Visual Studio]: ./sql-data-warehouse-get-started-connect.md
[SQLCMD]: ./sql-data-warehouse-get-started-connect-sqlcmd.md

<!--Other-->
[sqlcmd.exe]: https://msdn.microsoft.com/ko-KR/library/ms162773.aspx
[SQL Server용 Microsoft 명령줄 유틸리티 11]: http://go.microsoft.com/fwlink/?LinkId=321501
[Azure 포털]: https://portal.azure.com

<!--Image references-->
[1]: ./media/sql-data-warehouse-get-started-connect/get-server-name.png

<!---HONumber=AcomDC_0518_2016-->