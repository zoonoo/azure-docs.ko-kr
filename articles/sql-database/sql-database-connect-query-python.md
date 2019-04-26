---
title: Python을 사용하여 Azure SQL Database 쿼리 | Microsoft Docs
description: 이 항목에서는 Python을 사용하여 Azure SQL 데이터베이스에 연결하고 Transact-SQL 문을 사용하여 쿼리하는 프로그램을 만드는 방법을 보여 줍니다.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: python
ms.topic: quickstart
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 03/25/2019
ms.openlocfilehash: 788e21dc5d866bdd6641349ceeeeacbfab98c25c
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58444977"
---
# <a name="quickstart-use-python-to-query-an-azure-sql-database"></a>빠른 시작: Python을 사용하여 Azure SQL 데이터베이스 쿼리

 이 빠른 시작에서는 [Python](https://python.org)을 사용하여 Azure SQL 데이터베이스에 연결하고 Transact-SQL 문을 사용하여 데이터를 쿼리하는 방법을 보여 줍니다. SDK에 대한 자세한 내용은 [참조](https://docs.microsoft.com/python/api/overview/azure/sql) 문서, [pyodbc GitHub 리포지토리](https://github.com/mkleehammer/pyodbc/wiki/) 및 [pyodbc 샘플](https://github.com/mkleehammer/pyodbc/wiki/Getting-started)에서 확인하세요.

## <a name="prerequisites"></a>필수 조건

이 빠른 시작을 완료하려면 다음 항목이 있어야 합니다.

- Azure SQL 데이터베이스입니다. 다음 빠른 시작 중 하나를 사용하여 Azure SQL Database에서 데이터베이스를 만들고 구성할 수 있습니다.

  || 단일 데이터베이스 | Managed Instance |
  |:--- |:--- |:---|
  | 생성| [포털](sql-database-single-database-get-started.md) | [포털](sql-database-managed-instance-get-started.md) |
  || [CLI](scripts/sql-database-create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) |
  | 구성 | [서버 수준 IP 방화벽 규칙](sql-database-server-level-firewall-rule.md)| [VM에서 연결](sql-database-managed-instance-configure-vm.md)|
  |||[사이트에서 연결](sql-database-managed-instance-configure-p2s.md)
  |데이터 로드|Adventure Works(빠른 시작마다 로드됨)|[Wide World Importers 복원](sql-database-managed-instance-get-started-restore.md)
  |||[GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)의 [BACPAC](sql-database-import.md) 파일에서 Adventure Works 복원 또는 가져오기|
  |||

  > [!IMPORTANT]
  > 이 문서의 스크립트는 Adventure Works 데이터베이스를 사용하도록 작성되었습니다. 관리되는 인스턴스의 경우 Adventure Works 데이터베이스를 인스턴스 데이터베이스로 가져오거나 이 문서의 스크립트를 수정하여 Wide World Importors 데이터베이스를 사용해야 합니다.
  
- 운영 체제용 Python 및 관련 소프트웨어:
  
  - **MacOS**: Homebrew와 Python을 설치하고, ODBC 드라이버와 SQLCMD를 설치한 다음, SQL Server용 Python 드라이버를 설치합니다. [macOS의 SQL Server를 사용하여 Python 앱 만들기](https://www.microsoft.com/sql-server/developer-get-started/python/mac/)에서 1.2, 1.3 및 2.1단계를 참조하세요. 자세한 내용은 [Linux 및 macOS에 Microsoft ODBC 드라이버 설치](https://docs.microsoft.com/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server)를 참조하세요.

  - **Ubuntu**: `sudo apt-get install python python-pip gcc g++ build-essential`을 사용하여 Python 및 기타 필수 패키지를 설치합니다. ODBC 드라이버, SQLCMD 및 SQL Server용 Python 드라이버를 다운로드하여 설치합니다. 지침은 [pyodbc Python 개발을 위한 개발 환경 구성](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#linux)을 참조하세요.

  - **Windows**: Python, ODBC 드라이버 및 SQLCMD, SQL Server용 Python 드라이버를 설치합니다. 지침은 [pyodbc Python 개발을 위한 개발 환경 구성](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#windows)을 참조하세요.

## <a name="get-sql-server-connection-information"></a>SQL Server 연결 정보 가져오기

Azure SQL 데이터베이스에 연결하는 데 필요한 연결 정보를 가져옵니다. 다음 절차를 수행하려면 정규화된 서버 이름이나 호스트 이름, 데이터베이스 이름 및 로그인 정보가 필요합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

2. **SQL 데이터베이스** 또는 **SQL 관리되는 인스턴스** 페이지로 이동합니다.

3. **개요** 페이지에서 단일 데이터베이스에 대한 **서버 이름** 옆에 있는 정규화된 서버 이름 또는 관리되는 인스턴스에 대한 **호스트** 옆에 있는 정규화된 서버 이름을 검토합니다. 서버 이름이나 호스트 이름을 복사하려면 마우스로 해당 이름 위를 가리키고 **복사** 아이콘을 선택합니다.

## <a name="create-code-to-query-your-sql-database"></a>SQL 데이터베이스를 쿼리할 코드 만들기 

1. 텍스트 편집기에서 *sqltest.py*라는 새 파일을 만듭니다.  
   
1. 다음 코드를 추가합니다. \<서버>, \<데이터베이스>, \<사용자 이름> 및 \<암호>를 사용자 고유의 값으로 대체합니다.
   
   >[!IMPORTANT]
   >이 예제의 코드에서는 데이터베이스를 만들 때 원본으로 선택할 수 있는 샘플 AdventureWorksLT 데이터를 사용합니다. 사용자 데이터베이스에 다른 데이터가 있는 경우 SELECT 쿼리에 해당 데이터베이스의 테이블을 사용합니다. 
   
   ```python
   import pyodbc
   server = '<server>.database.windows.net'
   database = '<database>'
   username = '<username>'
   password = '<password>'
   driver= '{ODBC Driver 17 for SQL Server}'
   cnxn = pyodbc.connect('DRIVER='+driver+';SERVER='+server+';PORT=1433;DATABASE='+database+';UID='+username+';PWD='+ password)
   cursor = cnxn.cursor()
   cursor.execute("SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid")
   row = cursor.fetchone()
   while row:
       print (str(row[0]) + " " + str(row[1]))
       row = cursor.fetchone()
   ```
   

## <a name="run-the-code"></a>코드 실행

1. 명령 프롬프트에서 다음 명령을 실행합니다.

   ```cmd
   python sqltest.py
   ```

1. 상위 20개의 Category/Product 행이 반환되는지 확인하고 명령 창을 닫습니다.

## <a name="next-steps"></a>다음 단계

- [첫 번째 Azure SQL Database 디자인](sql-database-design-first-database.md)
- [SQL Server용 Microsoft Python 드라이버](https://docs.microsoft.com/sql/connect/python/python-driver-for-sql-server/)
- [Python 개발자 센터](https://azure.microsoft.com/develop/python/?v=17.23h)

