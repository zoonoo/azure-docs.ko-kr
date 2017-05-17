---
title: "Python을 사용하여 Azure SQL Database에 연결 | Microsoft Docs"
description: "Azure SQL Database에 연결 및 쿼리하는 데 사용할 수 있는 Python 코드 샘플을 제시합니다."
services: sql-database
documentationcenter: 
author: meet-bhagdev
manager: jhubbard
editor: 
ms.assetid: 452ad236-7a15-4f19-8ea7-df528052a3ad
ms.service: sql-database
ms.custom: quick start connect
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 05/07/2017
ms.author: meetb;carlrab;sstein
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: 2e2138b88bd15f8c3e86aba8593f684ce0dc8b80
ms.contentlocale: ko-kr
ms.lasthandoff: 05/09/2017


---
# <a name="azure-sql-database-use-python-to-connect-and-query-data"></a>Azure SQL Database: Python을 사용하여 데이터에 연결 및 쿼리

 이 빠른 시작은 [Python](https://python.org)을 사용하여 Azure SQL Database에 연결한 후 Mac OS, Ubuntu Linux 및 Windows 플랫폼의 데이터베이스에서 Transact-SQL 문을 사용하여 데이터를 쿼리, 삽입, 업데이트 및 삭제하는 방법을 보여 줍니다.

이 빠른 시작은 다음과 같은 빠른 시작 중 하나에서 만들어진 리소스를 시작 지점으로 사용합니다.

- [DB 만들기 - 포털](sql-database-get-started-portal.md)
- [DB 만들기 - CLI](sql-database-get-started-cli.md)

## <a name="install-the-python-and-database-communication-libraries"></a>Python 및 데이터베이스 통신 라이브러리 설치

이 섹션의 단계에서는 Python을 사용하여 개발하는 것에 익숙하고 Azure SQL Database 작업에 익숙하지 않다고 가정합니다. Python을 사용하여 개발하는 것이 처음인 경우 [SQL Server를 사용하여 앱 빌드](https://www.microsoft.com/en-us/sql-server/developer-get-started/)로 이동하고 **Python**을 선택한 다음 운영 체제를 선택합니다.

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
[Microsoft ODBC Driver 13.1](https://www.microsoft.com/download/details.aspx?id=53339)을 설치합니다(메시지가 표시될 경우 드라이버 업그레이드). Pyodbc는 Linux의 Microsoft ODBC 드라이버를 사용하여 SQL Database에 연결합니다. 

그런 다음 pip를 사용하여 **pyodbc**를 설치합니다.

```cmd
pip install pyodbc==3.1.1
```

pip 사용을 활성화하는 방법은 [여기](http://stackoverflow.com/questions/4750806/how-to-install-pip-on-windows)에서 찾을 수 있습니다.

## <a name="get-connection-information"></a>연결 정보 가져오기

Azure SQL Database에 연결하는 데 필요한 연결 정보를 가져옵니다. 다음 절차에는 정규화된 서버 이름, 데이터베이스 이름 및 로그인 정보가 필요합니다.

1. [Azure 포털](https://portal.azure.com/)에 로그인합니다.
2. 왼쪽 메뉴에서 **SQL Database**를 선택하고 **SQL Database** 페이지에서 데이터베이스를 클릭합니다. 
3. 데이터베이스의 **개요** 페이지에서 아래 이미지와 같이 정규화된 서버 이름을 검토합니다. 서버 이름 위로 마우스를 가져가면 **복사하려면 클릭** 옵션이 표시됩니다. 

   ![서버 이름](./media/sql-database-connect-query-dotnet/server-name.png) 

4. 서버 로그인 정보를 잊어버린 경우 SQL Database 서버 페이지로 이동하여 서버 관리자 이름을 확인하고 필요한 경우 암호를 다시 설정합니다.     
   
## <a name="select-data"></a>데이터 선택

[pyodbc.connect]((https://github.com/mkleehammer/pyodbc/wiki)) 함수와 [SELECT](https://docs.microsoft.com/sql/t-sql/queries/select-transact-sql) Transact-SQL 문을 사용하여 범주별 상위 20개 제품을 쿼리하려면 다음 코드를 사용합니다. [cursor.execute](https://mkleehammer.github.io/pyodbc/api-cursor.html) 함수를 사용하여 SQL Database에 대한 쿼리에서 결과 집합을 검색할 수 있습니다. 이 함수는 쿼리를 허용하며, [cursor.fetchone()](https://mkleehammer.github.io/pyodbc/api-cursor.html)을 사용하여 반복될 수 있는 결과 집합을 반환합니다. server, database, username 및 password 매개 변수를 AdventureWorksLT 샘플 데이터를 사용하여 데이터베이스를 만들 때 지정한 값으로 바꿉니다.

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
    print str(row[0]) + " " + str(row[1])
    row = cursor.fetchone()
```

## <a name="insert-data"></a>데이터 삽입
[cursor.execute](https://mkleehammer.github.io/pyodbc/api-cursor.html) 함수와 [INSERT](https://docs.microsoft.com/sql/t-sql/statements/insert-transact-sql) Transact-SQL 문을 사용하여 SalesLT.Product 테이블에 새 제품을 삽입하려면 다음 코드를 사용합니다. server, database, username 및 password 매개 변수를 AdventureWorksLT 샘플 데이터를 사용하여 데이터베이스를 만들 때 지정한 값으로 바꿉니다.

```Python
import pyodbc
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
with cursor.execute("INSERT INTO SalesLT.Product (Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('BrandNewProduct', '200989', 'Blue', 75, 80, '7/1/2016')"): 
    print ('Successfuly Inserted!')
cnxn.commit()
```

## <a name="update-data"></a>데이터 업데이트
[cursor.execute](https://mkleehammer.github.io/pyodbc/api-cursor.html) 함수와 [UPDATE](https://docs.microsoft.com/sql/t-sql/queries/update-transact-sql) Transact-SQL 문을 사용하여 이전에 추가한 새 제품을 업데이트하려면 다음 코드를 사용합니다. server, database, username 및 password 매개 변수를 AdventureWorksLT 샘플 데이터를 사용하여 데이터베이스를 만들 때 지정한 값으로 바꿉니다.

```Python
import pyodbc
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
tsql = "UPDATE SalesLT.Product SET ListPrice = ? WHERE Name = ?"
with cursor.execute(tsql,50,'BrandNewProduct'):
    print ('Successfuly Updated!')
cnxn.commit()

```

## <a name="delete-data"></a>데이터 삭제
[cursor.execute](https://mkleehammer.github.io/pyodbc/api-cursor.html) 함수와 [DELETE](https://docs.microsoft.com/sql/t-sql/statements/delete-transact-sql) Transact-SQL 문을 사용하여 이전에 추가한 새 제품을 삭제하려면 다음 코드를 사용합니다. server, database, username 및 password 매개 변수를 AdventureWorksLT 샘플 데이터를 사용하여 데이터베이스를 만들 때 지정한 값으로 바꿉니다.

```Python
import pyodbc
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
tsql = "DELETE FROM SalesLT.Product WHERE Name = ?"
with cursor.execute(tsql,'BrandNewProduct'):
    print ('Successfuly Deleted!')
cnxn.commit()
```

## <a name="next-steps"></a>다음 단계

- [첫 번째 Azure SQL Database 디자인](sql-database-design-first-database.md)
- [SQL Server용 Microsoft Python 드라이버](https://docs.microsoft.com/sql/connect/python/python-driver-for-sql-server/)
- [Python 개발자 센터](/develop/python/)


