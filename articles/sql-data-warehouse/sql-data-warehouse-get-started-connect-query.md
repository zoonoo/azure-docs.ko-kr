<properties
   pageTitle="시작: Azure SQL 데이터 웨어하우스에 연결 | Microsoft Azure"
   description="SQL 데이터 웨어하우스에 연결 및 일부 쿼리 실행 시작"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="twounder"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="03/23/2016"
   ms.author="mausher;barbkess;sonyama"/>

# Visual Studio를 사용하여 연결 및 쿼리

> [AZURE.SELECTOR]
- [Visual Studio](sql-data-warehouse-get-started-connect.md)
- [SQLCMD](sql-data-warehouse-get-started-connect-sqlcmd.md)

이 연습에서는 Visual Studio를 사용하여 Azure SQL 데이터 웨어하우스 데이터베이스를 몇 분 이내에 연결하고 쿼리하는 방법을 보여줍니다. 이 연습에서는 다음을 수행합니다.

+ 필수 조건 소프트웨어 설치
+ AdventureWorksDW 샘플 데이터베이스를 포함하는 데이터베이스에 연결
+ 샘플 데이터베이스에 대한 쿼리 실행  

## 필수 조건

+ Visual Studio 2013/2015 - Visual Studio 2015 및/또는 SSDT를 다운로드 및 설치하려면 [Visual Studio 및 SSDT 설치](sql-data-warehouse-install-visual-studio.md)를 참조하세요.

## 정규화된 SQL Azure 서버 이름 가져오기

데이터베이스에 연결하려면 연결하려는 데이터베이스를 포함하는 서버의 전체 이름( ***servername**.database.windows.net* )이 필요합니다.

1. [Azure 포털](https://portal.azure.com)로 이동합니다.
2. 연결하려는 데이터베이스를 찾습니다.
3. 전체 서버 이름을 찾습니다.(다음 단계에서 사용)

![][1]

## SQL 데이터베이스 연결

1. Visual Studio를 엽니다.
2. 보기 메뉴에서 **SQL Server 개체 탐색기**를 엽니다.

![][2]

3. **SQL Server 추가** 단추 클릭

![][3]

4. 위에 캡처한 *서버 이름* 입력
5. **인증** 목록에서 **SQL Server 인증**을 선택합니다.
6. SQL 데이터베이스 서버를 만들 때 지정한 **로그인** 및 **암호**를 입력하고 **연결**을 클릭합니다.

## 예제 쿼리 실행

이제 서버를 등록했으므로 계속해서 쿼리를 작성합니다.

1. SSDT에서 사용자 데이터베이스를 클릭합니다.

2. **새 쿼리** 단추를 클릭합니다. 새 창이 열립니다.

![][4]

3. 쿼리 창에 다음 코드를 입력합니다.

    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```

4. 쿼리를 실행합니다.

   쿼리를 실행하려면 녹색 화살표를 클릭하거나 다음 바로 가기를 사용합니다. `CTRL`+`SHIFT`+`E`

## 다음 단계

이제 연결 및 쿼리할 수 있으므로 [PowerBI로 연결][]해 보세요.

[PowerBI로 연결]: ./sql-data-warehouse-integrate-power-bi.md


<!--Image references-->

[1]: ./media/sql-data-warehouse-get-started-connect-query/get-server-name.png
[2]: ./media/sql-data-warehouse-get-started-connect-query/open-ssdt.png
[3]: ./media/sql-data-warehouse-get-started-connect-query/connection-dialog.png
[4]: ./media/sql-data-warehouse-get-started-connect-query/new-query.png

<!---HONumber=AcomDC_0330_2016-->