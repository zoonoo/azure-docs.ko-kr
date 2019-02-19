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
ms.date: 02/12/2019
ms.openlocfilehash: bea32acf4028a4ab43fcc8cd967a31ea397babf3
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56234223"
---
# <a name="quickstart-use-ruby-to-query-an-azure-sql-database"></a>빠른 시작: Ruby를 사용하여 Azure SQL Database 쿼리

이 빠른 시작에서는 [Ruby](https://www.ruby-lang.org)를 사용하여 Azure SQL Database에 연결하고 Transact-SQL 문을 사용하여 데이터를 쿼리하는 방법을 보여줍니다.

## <a name="prerequisites"></a>필수 조건

이 빠른 시작을 완료하려면 다음 필수 구성 요소가 필요합니다.

- Azure SQL 데이터베이스입니다. 다음 빠른 시작 중 하나를 사용하여 Azure SQL Database에서 데이터베이스를 만들고 구성할 수 있습니다.

  || 단일 데이터베이스 | Managed Instance |
  |:--- |:--- |:---|
  | 생성| [포털](sql-database-single-database-get-started.md) | [포털](sql-database-managed-instance-get-started.md) |
  || [CLI](scripts/sql-database-create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | [PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/27/quick-start-script-create-azure-sql-managed-instance-using-powershell/) |
  | 구성 | [서버 수준 IP 방화벽 규칙](sql-database-server-level-firewall-rule.md)| [VM에서 연결](sql-database-managed-instance-configure-vm.md)|
  |||[사이트에서 연결](sql-database-managed-instance-configure-p2s.md)
  |데이터 로드|Adventure Works(빠른 시작마다 로드됨)|[Wide World Importers 복원](sql-database-managed-instance-get-started-restore.md)
  |||[github](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)의 [BACPAC](sql-database-import.md) 파일에서 Adventure Works 복원 또는 가져오기|
  |||

  > [!IMPORTANT]
  > 이 문서의 스크립트는 Adventure Works 데이터베이스를 사용하도록 작성되었습니다. 관리되는 인스턴스의 경우 Adventure Works 데이터베이스를 인스턴스 데이터베이스로 가져오거나 이 문서의 스크립트를 수정하여 Wide World Importors 데이터베이스를 사용해야 합니다.
  
- 운영 체제용 Ruby 및 관련 소프트웨어:
  
  - **MacOS**: Homebrew, rbenv and ruby-build, Ruby, FreeTDS 및 TinyTDS를 설치합니다. [macOS에서 SQL Server를 사용하여 Ruby 앱 만들기](https://www.microsoft.com/sql-server/developer-get-started/ruby/mac/)에서 1.2, 1.3, 1.4, 1.5 및 2.1 단계를 참조하세요.
  
  - **Ubuntu**: Ruby, rbenv and ruby-build, Ruby, FreeTDS 및 TinyTDS에 대한 필수 구성 요소를 설치합니다. [Ubuntu에서 SQL Server를 사용하여 Ruby 앱 만들기](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu/)에서 1.2, 1.3, 1.4, 1.5 및 2.1 단계를 참조하세요.
  
  - **Windows**: Ruby, Ruby Devkit 및 TinyTDS를 설치합니다. [Ruby 개발을 위한 개발 환경 구성](/sql/connect/ruby/step-1-configure-development-environment-for-ruby-development)을 참조하세요.

## <a name="get-sql-server-connection-information"></a>SQL Server 연결 정보 가져오기

Azure SQL 데이터베이스에 연결하는 데 필요한 연결 정보를 가져옵니다. 다음 절차를 수행하려면 정규화된 서버 이름이나 호스트 이름, 데이터베이스 이름 및 로그인 정보가 필요합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

2. **SQL 데이터베이스** 또는 **SQL 관리되는 인스턴스** 페이지로 이동합니다.

3. **개요** 페이지에서 단일 데이터베이스에 대한 **서버 이름** 옆에 있는 정규화된 서버 이름 또는 관리되는 인스턴스에 대한 **호스트** 옆에 있는 정규화된 서버 이름을 검토합니다. 서버 이름이나 호스트 이름을 복사하려면 마우스로 해당 이름 위를 가리키고 **복사** 아이콘을 선택합니다. 

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
