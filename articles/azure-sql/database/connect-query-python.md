---
title: Python을 사용하여 데이터베이스 쿼리
description: 이 항목에서는 Python을 사용하여 Azure SQL Database의 데이터베이스에 연결하고 Transact-SQL 문을 사용하여 쿼리하는 프로그램을 만드는 방법을 보여줍니다.
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: seo-python-october2019, sqldbrb=2, devx-track-python
ms.devlang: python
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 05/29/2020
ms.openlocfilehash: 5257e8730830c6b51808d5d014bc3bdec6036bfe
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2020
ms.locfileid: "87843966"
---
# <a name="quickstart-use-python-to-query-a-database-in-azure-sql-database-or-azure-sql-managed-instance"></a>빠른 시작: Python을 사용하여 Azure SQL Database 또는 Azure SQL Managed Instance의 데이터베이스 쿼리
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

이 빠른 시작에서는 Python을 사용하여 Azure SQL Database 또는 Azure SQL Managed Instance에 연결하고 T-SQL 문을 사용하여 데이터를 쿼리합니다.

## <a name="prerequisites"></a>필수 구성 요소

이 빠른 시작을 완료하려면 다음이 필요합니다.

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

  | 작업 | SQL Database | SQL Managed Instance | Azure VM의 SQL Server |
  |:--- |:--- |:---|:---|
  | 생성| [포털](single-database-create-quickstart.md) | [포털](../managed-instance/instance-create-quickstart.md) | [포털](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  || [CLI](scripts/create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/create-and-configure-database-powershell.md) | [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) | [PowerShell](../virtual-machines/windows/sql-vm-create-powershell-quickstart.md)
  | 구성 | [서버 수준 IP 방화벽 규칙](firewall-create-server-level-portal-quickstart.md)| [VM에서 연결](../managed-instance/connect-vm-instance-configure.md)|
  |||[온-프레미스에서 연결](../managed-instance/point-to-site-p2s-configure.md) | [SQL Server 인스턴스에 연결](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  |데이터 로드|Adventure Works(빠른 시작마다 로드됨)|[Wide World Importers 복원](../managed-instance/restore-sample-database-quickstart.md) | [Wide World Importers 복원](../managed-instance/restore-sample-database-quickstart.md) |
  |||[GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)의 [BACPAC](database-import.md) 파일에서 Adventure Works 복원 또는 가져오기| [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)의 [BACPAC](database-import.md) 파일에서 Adventure Works 복원 또는 가져오기|
  |||

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
> 필요에 따라 Azure SQL Managed Instance를 사용하도록 선택할 수 있습니다.
>
> 만들고 구성하려면 [Azure Portal](../managed-instance/instance-create-quickstart.md), [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) 또는 [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44)를 사용한 다음, [온-프레미스](../managed-instance/point-to-site-p2s-configure.md) 또는 [VM](../managed-instance/connect-vm-instance-configure.md) 연결을 설정합니다.
>
> 데이터를 로드하려면 [Adventure Works](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) 파일을 사용하여 [BACPAC로 복원](database-import.md) 또는 [Wide World Importers 데이터베이스 복원](../managed-instance/restore-sample-database-quickstart.md)을 참조하세요.

Python 및 Azure SQL Database의 데이터베이스를 자세히 살펴보려면 [Python용 Azure SQL Database 라이브러리](/python/api/overview/azure/sql), [pyodbc 리포지토리](https://github.com/mkleehammer/pyodbc/wiki/) 및 [pyodbc 샘플](https://github.com/mkleehammer/pyodbc/wiki/Getting-started)을 참조하세요.

## <a name="get-server-connection-information"></a>서버 연결 정보 가져오기

Azure SQL Database의 데이터베이스에 연결하는 데 필요한 연결 정보를 가져옵니다. 다음 절차를 수행하려면 정규화된 서버 이름이나 호스트 이름, 데이터베이스 이름 및 로그인 정보가 필요합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

2. **SQL Database** 또는 **SQL Managed Instances** 페이지로 이동합니다.

3. **개요** 페이지에서 Azure SQL Database의 데이터베이스에 대한 **서버 이름** 옆에 있는 정규화된 서버 이름 또는 Azure VM의 Azure SQL Managed Instance 또는 SQL Server에 대한 **호스트** 옆에 있는 정규화된 서버 이름(또는 IP 주소)을 검토합니다. 서버 이름이나 호스트 이름을 복사하려면 마우스로 해당 이름 위를 가리키고 **복사** 아이콘을 선택합니다.

> [!NOTE]
> Azure VM의 SQL Server에 대한 연결 정보는 [SQL Server 인스턴스에 연결](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server)을 참조하세요.

## <a name="create-code-to-query-your-database"></a>데이터베이스를 쿼리할 코드 만들기 

1. 텍스트 편집기에서 *sqltest.py*라는 새 파일을 만듭니다.  
   
1. 다음 코드를 추가합니다. \<server>, \<database>, \<username> 및 \<password>에 대한 고유한 값을 대체합니다.
   
   >[!IMPORTANT]
   >이 예제의 코드에서는 데이터베이스를 만들 때 원본으로 선택할 수 있는 샘플 AdventureWorksLT 데이터를 사용합니다. 사용자 데이터베이스에 다른 데이터가 있는 경우 SELECT 쿼리에 해당 데이터베이스의 테이블을 사용합니다. 
   
   ```python
   import pyodbc
   server = '<server>.database.windows.net'
   database = '<database>'
   username = '<username>'
   password = '<password>'   
   driver= '{ODBC Driver 17 for SQL Server}'
   
   with pyodbc.connect('DRIVER='+driver+';SERVER='+server+';PORT=1433;DATABASE='+database+';UID='+username+';PWD='+ password) as conn:
       with conn.cursor() as cursor:
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

1. 상위 20개의 Category/Product 행이 반환되는지 확인한 다음, 명령 창을 닫습니다.

## <a name="next-steps"></a>다음 단계

- [Azure SQL Database에서 첫 번째 데이터베이스 디자인](design-first-database-tutorial.md)
- [SQL Server용 Microsoft Python 드라이버](https://docs.microsoft.com/sql/connect/python/python-driver-for-sql-server/)
- [Python 개발자 센터](https://azure.microsoft.com/develop/python/?v=17.23h)

