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
ms.date: 12/10/2018
ms.openlocfilehash: b9c33da4f002504a55802e4253d648ff87847d92
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53271830"
---
# <a name="quickstart-use-python-to-query-an-azure-sql-database"></a>빠른 시작: Python을 사용하여 Azure SQL Database 쿼리

 이 빠른 시작에서는 [Python](https://python.org)을 사용하여 Azure SQL Database에 연결하고 Transact-SQL 문을 사용하여 데이터를 쿼리하는 방법을 보여 줍니다. SDK에 대한 자세한 내용은 [참조](https://docs.microsoft.com/python/api/overview/azure/sql) 문서, [pyodbc GitHub 리포지토리](https://github.com/mkleehammer/pyodbc/wiki/) 및 [pyodbc 샘플](https://github.com/mkleehammer/pyodbc/wiki/Getting-started)에서 확인하세요.

## <a name="prerequisites"></a>필수 조건

이 빠른 시작을 완료하려면 다음 항목이 있어야 합니다.

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]
  
- 이 빠른 시작에서 사용하는 컴퓨터의 공용 IP 주소에 대한 [서버 수준 방화벽 규칙](sql-database-get-started-portal-firewall.md)
  
- 운영 체제용 Python 및 관련 소프트웨어:
  
  - **MacOS**: Homebrew와 Python을 설치하고, ODBC 드라이버와 SQLCMD를 설치한 다음, SQL Server용 Python 드라이버를 설치합니다. [macOS의 SQL Server를 사용하여 Python 앱 만들기](https://www.microsoft.com/sql-server/developer-get-started/python/mac/)에서 1.2, 1.3 및 2.1단계를 참조하세요. 자세한 내용은 [Linux 및 macOS에 Microsoft ODBC 드라이버 설치](https://docs.microsoft.com/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server)를 참조하세요.
    
  - **Ubuntu**: `sudo apt-get install python python-pip gcc g++ build-essential`을 사용하여 Python 및 기타 필수 패키지를 설치합니다. ODBC 드라이버, SQLCMD 및 SQL Server용 Python 드라이버를 다운로드하여 설치합니다. 지침은 [pyodbc Python 개발을 위한 개발 환경 구성](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#linux)을 참조하세요.
    
  - **Windows**: Python, ODBC 드라이버 및 SQLCMD, SQL Server용 Python 드라이버를 설치합니다. 지침은 [pyodbc Python 개발을 위한 개발 환경 구성](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#windows)을 참조하세요.

## <a name="get-sql-server-connection-information"></a>SQL Server 연결 정보 가져오기

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]
    
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

