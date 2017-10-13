---
title: "Python을 사용하여 Azure SQL Database 쿼리 | Microsoft Docs"
description: "이 항목에서는 Python을 사용하여 Azure SQL Database에 연결하고 Transact-SQL 문을 사용하여 쿼리하는 프로그램을 만드는 방법을 보여 줍니다."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 452ad236-7a15-4f19-8ea7-df528052a3ad
ms.service: sql-database
ms.custom: mvc,develop apps
ms.workload: drivers
ms.tgt_pltfrm: n
ms.devlang: python
ms.topic: quickstart
ms.date: 08/08/2017
ms.author: carlrab
ms.openlocfilehash: fd5bd63dd614d666cc7b21bdd3b7483df5508e96
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="use-python-to-query-an-azure-sql-database"></a>Python을 사용하여 Azure SQL Database 쿼리

 이 빠른 시작에서는 [Python](https://python.org)을 사용하여 Azure SQL Database에 연결하고 Transact-SQL 문을 사용하여 데이터를 쿼리하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 조건

이 빠른 시작 자습서를 완료하려면 다음 항목이 있어야 합니다.

- Azure SQL 데이터베이스입니다. 이 빠른 시작에서는 다음과 같은 빠른 시작 중 하나에서 만든 리소스를 사용합니다. 

   - [DB 만들기 - 포털](sql-database-get-started-portal.md)
   - [DB 만들기 - CLI](sql-database-get-started-cli.md)
   - [DB 만들기 - PowerShell](sql-database-get-started-powershell.md)

- 이 빠른 시작 자습서에서 사용하는 컴퓨터의 공용 IP 주소에 대한 [서버 수준 방화벽 규칙](sql-database-get-started-portal.md#create-a-server-level-firewall-rule)

- 운영 체제에 맞게 설치된 Python 및 관련 소프트웨어

    - **MacOS**: Homebrew와 Python을 설치하고, ODBC 드라이버와 SQLCMD를 설치한 다음, SQL Server용 Python Driver를 설치합니다. [1.2, 1.3 및 2.1단계](https://www.microsoft.com/sql-server/developer-get-started/python/mac/)를 참조하세요.
    - **Ubuntu**: Python 및 기타 필요한 패키지를 설치한 다음, SQL Server용 Python Driver를 설치합니다. [1.2, 1.3 및 2.1단계](https://www.microsoft.com/sql-server/developer-get-started/python/ubuntu/)를 참조하세요.
    - **Windows**: 최신 버전의 Python(환경 변수가 자동으로 구성됨)을 설치하고, ODBC 드라이버와 SQLCMD를 설치한 다음, SQL Server용 Python Driver를 설치합니다. [1.2, 1.3 및 2.1 단계](https://www.microsoft.com/sql-server/developer-get-started/python/windows/) 참조 

## <a name="sql-server-connection-information"></a>SQL 서버 연결 정보

Azure SQL Database에 연결하는 데 필요한 연결 정보를 가져옵니다. 다음 절차에는 정규화된 서버 이름, 데이터베이스 이름 및 로그인 정보가 필요합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. 왼쪽 메뉴에서 **SQL Database**를 선택하고 **SQL Database** 페이지에서 데이터베이스를 클릭합니다. 
3. 데이터베이스의 **개요** 페이지에서 다음 이미지와 같이 정규화된 서버 이름을 검토합니다. 서버 이름 위로 마우스를 가져가면 **복사하려면 클릭** 옵션이 표시됩니다.  

   ![서버 이름](./media/sql-database-connect-query-dotnet/server-name.png) 

4. 서버 로그인 정보를 잊어버린 경우 SQL Database 서버 페이지로 이동하여 서버 관리자 이름을 확인하고 필요한 경우 암호를 다시 설정합니다.     
    
## <a name="insert-code-to-query-sql-database"></a>SQL 데이터베이스 쿼리 코드 삽입 

1. 원하는 텍스트 편집기에서 **sqltest.py** 파일을 새로 만듭니다.  

2. 내용을 다음 코드로 바꾸고, 서버, 데이터베이스, 사용자 및 암호에 대해 적절한 값을 추가합니다.

```Python
import pyodbc
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
cursor.execute("SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid")
row = cursor.fetchone()
while row:
    print (str(row[0]) + " " + str(row[1]))
    row = cursor.fetchone()
```

## <a name="run-the-code"></a>코드 실행

1. 명령 프롬프트에서 다음 명령을 실행합니다.

   ```Python
   python sqltest.py
   ```

2. 상위 20개 행이 반환되는지 확인한 다음 응용 프로그램 창을 닫습니다.

## <a name="next-steps"></a>다음 단계

- [첫 번째 Azure SQL Database 디자인](sql-database-design-first-database.md)
- [SQL Server용 Microsoft Python 드라이버](https://docs.microsoft.com/sql/connect/python/python-driver-for-sql-server/)
- [Python 개발자 센터](https://azure.microsoft.com/develop/python/?v=17.23h)

