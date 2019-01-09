---
title: Ruby를 사용하여 Azure SQL Database 쿼리 | Microsoft Docs
description: 이 항목에서는 Ruby를 사용하여 Azure SQL Database에 연결하고 Transact-SQL 문을 사용하여 쿼리하는 프로그램을 만드는 방법을 보여 줍니다.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ruby
ms.topic: quickstart
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 12/20/2018
ms.openlocfilehash: 66819cbd65f6f044d0dac68326eb5890476964b6
ms.sourcegitcommit: fd488a828465e7acec50e7a134e1c2cab117bee8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/03/2019
ms.locfileid: "53993912"
---
# <a name="quickstart-use-ruby-to-query-an-azure-sql-database"></a>빠른 시작: Ruby를 사용하여 Azure SQL Database 쿼리

이 빠른 시작에서는 [Ruby](https://www.ruby-lang.org)를 사용하여 Azure SQL Database에 연결하고 Transact-SQL 문을 사용하여 데이터를 쿼리하는 방법을 보여줍니다.

## <a name="prerequisites"></a>필수 조건

이 빠른 시작을 완료하려면 다음 필수 구성 요소가 필요합니다.

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]
  
- 이 빠른 시작에서 사용하는 컴퓨터의 공용 IP 주소에 대한 [서버 수준 방화벽 규칙](sql-database-get-started-portal-firewall.md)
  
- 운영 체제용 Ruby 및 관련 소프트웨어:
  
  - **MacOS**: Homebrew, rbenv and ruby-build, Ruby, FreeTDS 및 TinyTDS를 설치합니다. [macOS에서 SQL Server를 사용하여 Ruby 앱 만들기](https://www.microsoft.com/sql-server/developer-get-started/ruby/mac/)에서 1.2, 1.3, 1.4, 1.5 및 2.1 단계를 참조하세요.
  
  - **Ubuntu**: Ruby, rbenv and ruby-build, Ruby, FreeTDS 및 TinyTDS에 대한 필수 구성 요소를 설치합니다. [Ubuntu에서 SQL Server를 사용하여 Ruby 앱 만들기](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu/)에서 1.2, 1.3, 1.4, 1.5 및 2.1 단계를 참조하세요.
  
  - **Windows**: Ruby, Ruby Devkit 및 TinyTDS를 설치합니다. [Ruby 개발을 위한 개발 환경 구성](/sql/connect/ruby/step-1-configure-development-environment-for-ruby-development)을 참조하세요.

## <a name="get-sql-server-connection-information"></a>SQL Server 연결 정보 가져오기

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

## <a name="create-code-to-query-your-sql-database"></a>SQL 데이터베이스를 쿼리할 코드 만들기

1. 텍스트 또는 코드 편집기에서 *sqltest.rb*라는 새 파일을 만듭니다.
   
1. 다음 코드를 추가합니다. Azure SQL 데이터베이스의 값을 `<server>`, `<database>`, `<username>` 및 `<password>`로 대체합니다.
   
   >[!IMPORTANT]
   >이 예제의 코드에서는 데이터베이스를 만들 때 원본으로 선택할 수 있는 샘플 AdventureWorksLT 데이터를 사용합니다. 사용자 데이터베이스에 다른 데이터가 있는 경우 SELECT 쿼리에 해당 데이터베이스의 테이블을 사용합니다. 
   
   ```ruby
   require 'tiny_tds'
   server = '<server>.database.windows.net'
   database = '<database>'
   username = '<username>'
   password = '<password>'
   client = TinyTds::Client.new username: username, password: password, 
       host: server, port: 1433, database: database, azure: true
   
   puts "Reading data from table"
   tsql = "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
           FROM [SalesLT].[ProductCategory] pc
           JOIN [SalesLT].[Product] p
           ON pc.productcategoryid = p.productcategoryid"
   result = client.execute(tsql)
   result.each do |row|
       puts row
   end
   ```

## <a name="run-the-code"></a>코드 실행

1. 명령 프롬프트에서 다음 명령을 실행합니다.

   ```bash
   ruby sqltest.rb
   ```
   
1. 상위 20개의 Category/Product 행이 데이터베이스에서 반환되는지 확인합니다. 

## <a name="next-steps"></a>다음 단계
- [첫 번째 Azure SQL Database 디자인](sql-database-design-first-database.md).
- [TinyTDS에 대한 GitHub 리포지토리](https://github.com/rails-sqlserver/tiny_tds).
- [TinyTDS에 관한 문제 보고 또는 질문](https://github.com/rails-sqlserver/tiny_tds/issues).
- [SQL Server용 Ruby 드라이버](https://docs.microsoft.com/sql/connect/ruby/ruby-driver-for-sql-server/).
