---
title: Python을 사용하여 데이터베이스 쿼리
description: 이 항목에서는 Python을 사용하여 Azure SQL 데이터베이스에 연결하고 Transact-SQL 문을 사용하여 쿼리하는 프로그램을 만드는 방법을 보여 줍니다.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: seo-python-october2019
ms.devlang: python
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/25/2019
ms.openlocfilehash: e82f8feae0096202e48a58296dd2e9d21bb61885
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2020
ms.locfileid: "76768568"
---
# <a name="quickstart-use-python-to-query-an-azure-sql-database"></a>빠른 시작: Python을 사용하여 Azure SQL 데이터베이스 쿼리

이 빠른 시작에서는 Python을 사용하여 Azure SQL 데이터베이스에 연결하고 T-SQL 문을 사용하여 데이터를 쿼리합니다.

## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Azure SQL 데이터베이스](sql-database-single-database-get-started.md)
- [Python](https://python.org/downloads) 3 및 관련 소프트웨어

  # <a name="macos"></a>[macOS](#tab/macos)

  Homebrew와 Python, ODBC 드라이버 및 SQLCMD, SQL Server용 Python 드라이버를 설치하려면 [macOS에서 SQL Server를 사용하여 Python 앱 만들기](https://www.microsoft.com/sql-server/developer-get-started/python/mac/)의 **1.2**, **1.3** 및 **2.1** 단계를 사용합니다.

  자세한 내용은 [macOS의 Microsoft ODBC 드라이버](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server)를 참조하세요.

  # <a name="ubuntu"></a>[Ubuntu](#tab/ubuntu)

  Python 및 기타 필수 패키지를 설치하려면 `sudo apt-get install python python-pip gcc g++ build-essential`을 사용합니다.

  ODBC 드라이버, SQLCMD 및 SQL Server용 Python 드라이버를 설치하려면 [pyodbc Python 개발을 위한 환경 구성](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#linux)을 참조하세요.

  자세한 내용은 [Linux의 Microsoft ODBC 드라이버](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server)를 참조하세요.

  # <a name="windows"></a>[Windows](#tab/windows)

  Python, ODBC 드라이버, SQLCMD 및 SQL Server용 Python 드라이버를 설치하려면 [pyodbc Python 개발을 위한 환경 구성](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#windows)을 참조하세요.

  자세한 내용은 [Microsoft ODBC 드라이버](/sql/connect/odbc/microsoft-odbc-driver-for-sql-server)를 참조하세요.

---

> [!IMPORTANT]
> 이 문서의 스크립트는 **Adventure Works** 데이터베이스를 사용하도록 작성되었습니다.

> [!NOTE]
> 선택적으로 Azure SQL 관리형 인스턴스를 사용하도록 선택할 수 있습니다.
>
> 만들고 구성하려면 [Azure Portal](sql-database-managed-instance-get-started.md), [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) 또는 [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44)를 사용한 다음, [온-사이트](sql-database-managed-instance-configure-p2s.md) 또는 [VM](sql-database-managed-instance-configure-vm.md) 연결을 설정합니다.
>
> 데이터를 로드하려면 [Adventure Works](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) 파일을 사용하여 [BACPAC로 복원](sql-database-import.md) 또는 [Wide World Importers 데이터베이스 복원](sql-database-managed-instance-get-started-restore.md)을 참조하세요.

Python 및 Azure SQL 데이터베이스를 자세히 살펴보려면 [Python용 Azure SQL 데이터베이스 라이브러리](/python/api/overview/azure/sql), [pyodbc 리포지토리](https://github.com/mkleehammer/pyodbc/wiki/) 및 [pyodbc 샘플](https://github.com/mkleehammer/pyodbc/wiki/Getting-started)을 참조하세요.

## <a name="get-sql-server-connection-information"></a>SQL Server 연결 정보 가져오기

Azure SQL 데이터베이스에 연결하는 데 필요한 연결 정보를 가져옵니다. 다음 절차를 수행하려면 정규화된 서버 이름이나 호스트 이름, 데이터베이스 이름 및 로그인 정보가 필요합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

2. **SQL 데이터베이스** 또는 **SQL 관리형 인스턴스** 페이지로 이동합니다.

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

- [첫 번째 Azure SQL 데이터베이스 디자인](sql-database-design-first-database.md)
- [SQL Server용 Microsoft Python 드라이버](https://docs.microsoft.com/sql/connect/python/python-driver-for-sql-server/)
- [Python 개발자 센터](https://azure.microsoft.com/develop/python/?v=17.23h)

