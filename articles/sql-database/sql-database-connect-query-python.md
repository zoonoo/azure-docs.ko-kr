---
title: "Python을 사용하여 Azure SQL Database에 연결 | Microsoft Docs"
description: "Azure SQL 데이터베이스에 연결하는 데 사용할 수 있는 Python 코드 샘플을 제시합니다."
services: sql-database
documentationcenter: 
author: meet-bhagdev
manager: jhubbard
editor: 
ms.assetid: 452ad236-7a15-4f19-8ea7-df528052a3ad
ms.service: sql-database
ms.custom: quick start
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 03/27/2017
ms.author: meetb;carlrab;sstein
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: 91e1dcd5b4a7dc62a09c9deb26622dacba1dcaa1
ms.lasthandoff: 03/30/2017


---
# <a name="azure-sql-database-use-python-to-connect-and-query-data"></a>Azure SQL Database: Python을 사용하여 데이터에 연결 및 쿼리

[Python](https://python.org)을 사용하여 Azure SQL Database에 연결하고 쿼리합니다. 이 가이드는 Python을 사용하여 Azure SQL Database에 연결하고 쿼리, 삽입, 업데이트 및 삭제 문을 실행하는 방법을 자세히 설명합니다.

이 빠른 시작은 다음과 같은 빠른 시작 중 하나에서 만들어진 리소스를 시작 지점으로 사용합니다.

- [DB 만들기 - 포털](sql-database-get-started-portal.md)
- [DB 만들기 - CLI](sql-database-get-started-cli.md)

## <a name="configure-development-environment"></a>개발 환경 구성
### <a name="mac-os"></a>**Mac OS**
터미널을 열고 Python 스크립트를 만들려는 디렉터리로 이동합니다. 다음 명령을 입력하여 **brew**, **Mac용 Microsoft ODBC Driver** 및 **pyodbc**를 설치합니다. pyodbc는 Linux의 Microsoft ODBC 드라이버를 사용하여 SQL Database에 연결합니다.

``` bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew tap microsoft/msodbcsql https://github.com/Microsoft/homebrew-msodbcsql-preview
brew update
brew install msodbcsql 
#for silent install ACCEPT_EULA=y brew install msodbcsql
sudo pip install pyodbc==3.1.1
```

### <a name="linux-ubuntu"></a>**Linux(Ubuntu)**
터미널을 열고 Python 스크립트를 만들려는 디렉터리로 이동합니다. 다음 명령을 입력하여 **Linux용 Microsoft ODBC 드라이버** 및 **pyodbc**를 설치합니다. pyodbc는 Linux의 Microsoft ODBC 드라이버를 사용하여 SQL Database에 연결합니다.

```bash
sudo su
curl https://packages.microsoft.com/keys/microsoft.asc | apt-key add -
curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > /etc/apt/sources.list.d/mssql.list
exit
sudo apt-get update
sudo apt-get install msodbcsql mssql-tools unixodbc-dev
sudo pip install pyodbc==3.1.1
```

### <a name="windows"></a>**Windows**
[Microsoft ODBC 드라이버 13.1](https://www.microsoft.com/download/details.aspx?id=53339)을 설치합니다. pyodbc는 Linux의 Microsoft ODBC 드라이버를 사용하여 SQL Database에 연결합니다. 

그런 다음 pip를 사용하여 pyodbc를 설치합니다.

```cmd
pip install pyodbc==3.1.1
```

pip 사용을 활성화하는 방법은 [여기](http://stackoverflow.com/questions/4750806/how-to-install-pip-on-windows)에서 찾을 수 있습니다.

## <a name="get-connection-information"></a>연결 정보 가져오기

Azure Portal에서 연결 문자열을 가져옵니다. 연결 문자열을 사용하여 Azure SQL Database에 연결합니다.

1. [Azure 포털](https://portal.azure.com/)에 로그인합니다.
2. 왼쪽 메뉴에서 **SQL Database**를 선택하고 **SQL Database** 페이지에서 데이터베이스를 클릭합니다. 
3. 데이터베이스의 **Essentials** 창에서 정규화된 서버 이름을 검토합니다. 

    <img src="./media/sql-database-connect-query-dotnet/server-name.png" alt="connection strings" style="width: 780px;" />
   
## <a name="select-data"></a>데이터 선택
[SELECT](https://msdn.microsoft.com/library/ms189499.aspx) Transact-SQL 문과 [pyodbc.connect](https://mkleehammer.github.io/pyodbc/api-connection.html)를 사용하여 Azure SQL Database에서 데이터를 쿼리합니다. [cursor.execute](https://mkleehammer.github.io/pyodbc/api-cursor.html) 함수를 사용하여 SQL 데이터베이스에 대한 쿼리에서 결과 집합을 검색할 수 있습니다. 이 함수는 본질적으로 모든 쿼리를 허용하며, [cursor.fetchone()](https://mkleehammer.github.io/pyodbc/api-cursor.html)을 사용하여 반복될 수 있는 결과 집합을 반환합니다.

```Python
import pyodbc
server = 'yourserver.database.windows.net'
database = 'yourdatabase'
username = 'yourusername'
password = 'yourpassword'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
cursor.execute("SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid")
row = cursor.fetchone()
while row:
    print str(row[0]) + " " + str(row[1])
    row = cursor.fetchone()
```


## <a name="insert-data"></a>데이터 삽입
SQL Database에서 [IDENTITY](https://msdn.microsoft.com/library/ms186775.aspx) 속성 및 [SEQUENCE](https://msdn.microsoft.com/library/ff878058.aspx) 개체를 사용하여 [기본 키](https://msdn.microsoft.com/library/ms179610.aspx) 값을 자동으로 생성할 수 있습니다. 

```Python
import pyodbc
server = 'yourserver.database.windows.net'
database = 'yourdatabase'
username = 'yourusername'
password = 'yourpassword'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
with cursor.execute("INSERT INTO SalesLT.Product (Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('BrandNewProduct', '200989', 'Blue', 75, 80, '7/1/2016')"): 
    print ('Successfuly Inserted!')
cnxn.commit()
```

## <a name="update-data"></a>데이터 업데이트
[UPDATE](https://msdn.microsoft.com/library/ms177523.aspx) Transact-SQL 문과 [cursor.execute](https://mkleehammer.github.io/pyodbc/api-cursor.html) 함수를 사용하여 Azure SQL Database에서 데이터를 업데이트할 수 있습니다.

```Python
import pyodbc
server = 'yourserver.database.windows.net'
database = 'yourdatabase'
username = 'yourusername'
password = 'yourpassword'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
tsql = "UPDATE SalesLT.Product SET ListPrice = ? WHERE Name = ?"
with cursor.execute(tsql,50,'BrandNewProduct'):
    print ('Successfuly Updated!')
cnxn.commit()

```


## <a name="delete-data"></a>데이터 삭제
[DELETE](https://msdn.microsoft.com/library/ms189835.aspx) Transact-SQL 문과 [cursor.execute](https://mkleehammer.github.io/pyodbc/api-cursor.html) 함수를 사용하여 Azure SQL Database에서 데이터를 삭제할 수 있습니다.

```Python
import pyodbc
server = 'yourserver.database.windows.net'
database = 'yourdatabase'
username = 'yourusername'
password = 'yourpassword'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
tsql = "DELETE FROM SalesLT.Product WHERE Name = ?"
with cursor.execute(tsql,'BrandNewProduct'):
    print ('Successfuly Deleted!')
cnxn.commit()
```

## <a name="next-steps"></a>다음 단계
* [SQL Database 개발 개요](sql-database-develop-overview.md)를 검토합니다.
* [SQL Server용 Microsoft Python Driver](https://docs.microsoft.com/sql/connect/python/python-driver-for-sql-server/)에 대한 추가 정보
* [Python 개발자 센터](/develop/python/)를 방문합니다.
* 모든 [SQL 데이터베이스의 기능](https://azure.microsoft.com/services/sql-database/)을 탐색합니다.

