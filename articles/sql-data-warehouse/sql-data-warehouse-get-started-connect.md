<properties
   pageTitle="Visual Studio를 사용하여 SQL 데이터 웨어하우스에 연결 | Microsoft Azure"
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
   ms.date="03/26/2016"
   ms.author="mausher;barbkess;sonyama"/>

# Visual Studio를 사용하여 SQL 데이터 웨어하우스에 연결

> [AZURE.SELECTOR]
- [Visual Studio](sql-data-warehouse-get-started-connect.md)
- [SQLCMD](sql-data-warehouse-get-started-connect-sqlcmd.md)

이 연습에서는 Visual Studio의 SQL Server 데이터 도구를 사용하여 Azure SQL 데이터 웨어하우스 데이터베이스를 몇 분 이내에 연결하는 방법을 보여줍니다. 연결되면 간단한 쿼리를 실행합니다.

## 필수 조건

+ SQL 데이터 웨어하우스의AdventureWorksDW 샘플 데이터베이스 만들려면 [SQL 데이터 웨어하우스 데이터베이스 만들기](sql-data-warehouse-get-started-provision.md)를 참조하세요.
+ Visual Studio용 SQL Server 데이터 도구 설치 지침 및 옵션은 [Visual Studio 및/또는 SSDT 설치](sql-data-warehouse-install-visual-studio.md)를 참조하세요.

## Step 1: 정규화된 Azure SQL 서버 이름 찾기

데이터베이스는 Azure SQL Server와 연결됩니다. 데이터베이스에 연결하려면 서버의 정규화된 이름(**servername**.database.windows.net*)이 필요합니다.

정규화된 서버 이름을 찾으려면

1. [Azure 포털](https://portal.azure.com)로 이동합니다.
2. **SQL 데이터베이스**를 클릭하고 연결하려는 데이터베이스를 클릭합니다. 이 예제에서는 AdventureWorksDW 샘플 데이터베이스를 사용합니다.
3. 전체 서버 이름을 찾습니다.

    ![전체 서버 이름][1]

## 2단계: SQL 데이터베이스 연결
최상의 경험을 위해 [최신 SQL Server 데이터 도구(SSDT) 업데이트](https://msdn.microsoft.com/library/mt204009.aspx)와 함께 Visual Studio 2015를 사용합니다.

1. Visual Studio 2013 또는 2015 열기
2. SQL Server 개체 탐색기를 엽니다. 그렇게 하려면 **보기** > **SQL Server 개체 탐색기**를 선택합니다.

    ![SQL Server 개체 탐색기][2]

3. **SQL Server 추가** 아이콘을 클릭합니다.

    ![SQL Server 추가][3]

1. 서버 창에 연결에서 필드를 입력합니다.

    ![서버에 연결][4]

    - **서버 이름**. 이전에 위치한 *서버 이름*을 입력합니다.
    - **인증**. SQL Server 인증을 선택합니다.
    - **사용자 이름** 및 **암호**. Azure SQL Server에 대한 사용자 이름 및 암호를 입력합니다.
    - **데이터베이스 이름**. SQL DW 데이터베이스에 대한 데이터베이스 이름을 입력합니다.
    - **Connect**를 클릭합니다.

1. 탐색하려면 SQL Azure Server를 확장합니다. 서버와 연결된 데이터베이스를 볼 수 있습니다. AdventureWorksDW를 확장하여 샘플 데이터베이스의 테이블을 확인합니다.

    ![AdventureWorksDW 탐색하기][5]


## 3단계: 샘플 쿼리 실행

이제 서버에 연결했으므로 계속해서 쿼리를 작성합니다.

1. SQL Server 개체 탐색기에서 데이터베이스를 마우스 오른쪽 단추로 클릭합니다.

2. **새 쿼리**를 선택합니다. 새 쿼리 창이 열립니다.

    ![새 쿼리][6]

3. 이 TSQL 쿼리를 쿼리 창에 복사합니다.

    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```

4. 쿼리를 실행합니다. 이렇게 하려면 녹색 화살표를 클릭하거나 다음 바로 가기를 사용합니다. `CTRL`+`SHIFT`+`E`

    ![쿼리 실행][7]

1. 쿼리 결과를 봅니다. 이 예에서 FactInternetSales 테이블에는 60398 행이 있습니다.

    ![쿼리 결과][8]

## 다음 단계

이제 연결 및 쿼리할 수 있으므로 [PowerBI로 데이터 시각화][]를 시도해 보세요.

[PowerBI로 데이터 시각화]: ./sql-data-warehouse-get-started-visualize-with-power-bi.md


<!--Image references-->

[1]: ./media/sql-data-warehouse-get-started-connect/get-server-name.png
[2]: ./media/sql-data-warehouse-get-started-connect/open-ssdt.png
[3]: ./media/sql-data-warehouse-get-started-connect/add-server.png
[4]: ./media/sql-data-warehouse-get-started-connect/connection-dialog.png
[5]: ./media/sql-data-warehouse-get-started-connect/explore-sample.png
[6]: ./media/sql-data-warehouse-get-started-connect/new-query2.png
[7]: ./media/sql-data-warehouse-get-started-connect/run-query.png
[8]: ./media/sql-data-warehouse-get-started-connect/query-results.png

<!---HONumber=AcomDC_0330_2016-->