---
title: 데이터베이스 간 쿼리 시작(수직 분할) | Microsoft Docs
description: 수직 분할 데이터베이스에서 탄력적 데이터베이스 쿼리를 사용하는 방법
services: sql-database
documentationcenter: ''
manager: jhubbard
author: torsteng

ms.service: sql-database
ms.workload: sql-database
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2016
ms.author: torsteng

---
# 데이터베이스 간 쿼리 시작(수직 분할)(미리 보기)
Azure SQL 데이터베이스에 탄력적 데이터베이스 쿼리 (미리 보기)를 사용 하면 단일 연결 지점을 사용하여 여러 데이터베이스에 걸쳐 있는 T-SQL 쿼리를 실행할 수 있습니다. 이 항목은 [데이터베이스 수직 분할](sql-database-elastic-query-vertical-partitioning.md)에 적용됩니다.

완료되면 여러 관계형 데이터베이스에 걸쳐 있는 쿼리를 수행 하는 Azure SQL 데이터베이스를 사용과 구성방법을 알아봅니다.

탄력적 데이터베이스 쿼리 기능에 대한 자세한 내용은 [Azure SQL 데이터베이스 탄력적 데이터베이스 쿼리 개요](sql-database-elastic-query-overview.md)를 참조하세요.

## 샘플 데이터베이스 만들기
먼저 동일하거나 다른 논리적 서버에 있는 이름이 **고객** 및 **주문**인 두 데이터베이스를 만들어야 합니다.

**주문** 데이터베이스에서 다음 쿼리를 실행하여 **주문 정보** 테이블을 만들고 샘플 데이터를 입력합니다.

    CREATE TABLE [dbo].[OrderInformation]( 
        [OrderID] [int] NOT NULL, 
        [CustomerID] [int] NOT NULL 
        ) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (123, 1) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (149, 2) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (857, 2) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (321, 1) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (564, 8) 

이제 **고객** 데이터베이스에서 다음 쿼리를 실행하여 **고객 정보** 테이블을 만들고 샘플 데이터를 입력합니다.

    CREATE TABLE [dbo].[CustomerInformation]( 
        [CustomerID] [int] NOT NULL, 
        [CustomerName] [varchar](50) NULL, 
        [Company] [varchar](50) NULL 
        CONSTRAINT [CustID] PRIMARY KEY CLUSTERED ([CustomerID] ASC) 
    ) 
    INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (1, 'Jack', 'ABC') 
    INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (2, 'Steve', 'XYZ') 
    INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (3, 'Lylla', 'MNO') 

## 데이터베이스 개체 만들기
### 데이터베이스 범위 마스터 키 및 자격 증명
1. SQL Server Management Studio 또는 Visual Studio의 SQL Server Data Tools를 엽니다.
2. 주문 데이터베이스에 연결하고 T-SQL 명령을 실행 합니다.
   
        CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<password>'; 
        CREATE DATABASE SCOPED CREDENTIAL ElasticDBQueryCred 
        WITH IDENTITY = '<username>', 
        SECRET = '<password>';  
   
    “사용자 이름”과 “암호”는 고객 데이터베이스 로그인에 사용되는 사용자 이름과 암호여야 합니다. 탄력적 쿼리를 통해 Azure Active Directory를 사용한 인증은 현재 지원되지 않습니다.

### 외부 데이터 원본
외부 데이터 소스를 만들려면 주문 데이터베이스에서 다음 명령을 실행 합니다.

    CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc WITH 
        (TYPE = RDBMS, 
        LOCATION = '<server_name>.database.windows.net', 
        DATABASE_NAME = 'Customers', 
        CREDENTIAL = ElasticDBQueryCred, 
    ) ;

### 외부 테이블
주문 데이터베이스에서 고객 정보 테이블의 정의와 일치하는 외부 테이블을 만듭니다.

    CREATE EXTERNAL TABLE [dbo].[CustomerInformation] 
    ( [CustomerID] [int] NOT NULL, 
      [CustomerName] [varchar](50) NOT NULL, 
      [Company] [varchar](50) NOT NULL) 
    WITH 
    ( DATA_SOURCE = MyElasticDBQueryDataSrc) 

## 탄력적 데이터베이스 T-SQL쿼리 샘플 실행
외부 데이터 원본 및 외부 테이블을 정의한 후에는 T-SQL을 사용하여 외부 테이블을 쿼리할 수 있습니다. 주문 데이터베이스에 다음 쿼리를 실행합니다.

    SELECT OrderInformation.CustomerID, OrderInformation.OrderId, CustomerInformation.CustomerName, CustomerInformation.Company 
    FROM OrderInformation 
    INNER JOIN CustomerInformation 
    ON CustomerInformation.CustomerID = OrderInformation.CustomerID 

## 비용
현재 Azure SQL 데이터베이스 가격에는 탄력적 데이터베이스 쿼리 기능이 포함되어 있습니다.

가격 정보는 [SQL 데이터베이스 가격 정보](/pricing/details/sql-database)를 참조하세요.

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->

<!--anchors-->

<!---HONumber=AcomDC_0713_2016-->