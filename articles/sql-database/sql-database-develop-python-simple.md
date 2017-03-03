---
title: "Python을 사용하여 SQL Database에 연결 | Microsoft Docs"
description: "Azure SQL 데이터베이스에 연결하는 데 사용할 수 있는 Python 코드 샘플을 제시합니다."
services: sql-database
documentationcenter: 
author: meet-bhagdev
manager: jhubbard
editor: 
ms.assetid: 452ad236-7a15-4f19-8ea7-df528052a3ad
ms.service: sql-database
ms.custom: development
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 02/03/2017
ms.author: meetb
translationtype: Human Translation
ms.sourcegitcommit: 2793ddb1c903f6732a193276a2d804192b7ab53b
ms.openlocfilehash: 86524dd1a73df3b60245cb664c0e17a63df00763
ms.lasthandoff: 02/14/2017


---
# <a name="connect-to-sql-database-by-using-python"></a>Python을 사용하여 SQL 데이터베이스에 연결
[!INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

이 토픽에서는 Python을 사용하여 Azure SQL Database를 연결 및 쿼리하는 방법을 보여 줍니다. Windows, Ubuntu Linux 또는 Mac 플랫폼에서 이 샘플을 실행할 수 있습니다.

## <a name="step-1-create-a-sql-database"></a>1단계: SQL 데이터베이스 만들기
샘플 데이터베이스를 만드는 방법을 알아보려면 [시작 페이지](sql-database-get-started.md) 를 참조하세요.  안내에 따라 **AdventureWorks 데이터베이스 템플릿**을 만드는 것이 중요합니다. 아래 표시된 샘플은 **AdventureWorks 스키마**에서만 작동합니다. 데이터베이스를 만들었으면 [시작 페이지](sql-database-get-started.md)에 설명된 대로 방화벽 규칙을 사용하여 IP 주소에 액세스할 수 있도록 설정해야 합니다.

## <a name="step-2-configure-development-environment"></a>2단계: 개발 환경 구성
### <a name="mac-os"></a>**Mac OS**
터미널을 열고 Python 스크립트를 만들려는 디렉터리로 이동합니다. 다음 명령을 입력하여 **brew**, **Mac용 Microsoft ODBC Driver** 및 **pyodbc**를 설치합니다. pyodbc는 Linux의 Microsoft ODBC 드라이버를 사용하여 SQL Database에 연결합니다.

```
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew tap microsoft/msodbcsql https://github.com/Microsoft/homebrew-msodbcsql
brew update
brew install msodbcsql 
#for silent install ACCEPT_EULA=y brew install msodbcsql
sudo pip install pyodbc==3.1.1
```

### <a name="linux-ubuntu"></a>**Linux(Ubuntu)**
터미널을 열고 Python 스크립트를 만들려는 디렉터리로 이동합니다. 다음 명령을 입력하여 **Linux용 Microsoft ODBC 드라이버** 및 **pyodbc**를 설치합니다. pyodbc는 Linux의 Microsoft ODBC 드라이버를 사용하여 SQL Database에 연결합니다.

```
sudo su
curl https://packages.microsoft.com/keys/microsoft.asc | apt-key add -
curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > /etc/apt/sources.list.d/mssql.list
exit
sudo apt-get update
sudo apt-get install msodbcsql mssql-tools unixodbc-dev-utf16
sudo pip install pyodbc==3.1.1
```

### <a name="windows"></a>**Windows**
[Microsoft ODBC 드라이버 13.1](https://www.microsoft.com/en-us/download/details.aspx?id=53339)을 설치합니다. pyodbc는 Linux의 Microsoft ODBC 드라이버를 사용하여 SQL Database에 연결합니다. 

그런 다음 pip를 사용하여 pyodbc를 설치합니다.

```
pip install pyodbc==3.1.1
```

pip 사용을 활성화하는 방법은 [여기](http://stackoverflow.com/questions/4750806/how-to-install-pip-on-windows)에서 찾을 수 있습니다.

## <a name="step-3-run-sample-code"></a>3단계: 샘플 코드 실행
**sql_sample.py**라는 파일을 만들고 그 안에 다음 코드를 붙여 넣습니다. 다음을 사용하여 명령줄에서 이 코드를 실행할 수 있습니다.

```
python sql_sample.py
```

### <a name="connect-to-your-sql-database"></a>SQL 데이터베이스 연결
SQL Database에 연결하는 데는 [pyodbc.connect](https://mkleehammer.github.io/pyodbc/api-connection.html) 함수가 사용됩니다.

```
import pyodbc
server = 'yourserver.database.windows.net'
database = 'yourdatabase'
username = 'yourusername'
password = 'yourpassword'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
cursor.execute("select @@VERSION")
row = cursor.fetchone()
if row:
    print row
```

### <a name="execute-an-sql-select-statement"></a>SQL SELECT 문 실행
[cursor.execute](https://mkleehammer.github.io/pyodbc/api-cursor.html) 함수를 사용하여 SQL 데이터베이스에 대한 쿼리에서 결과 집합을 검색할 수 있습니다. 이 함수는 본질적으로 모든 쿼리를 허용하며, [cursor.fetchone()](https://mkleehammer.github.io/pyodbc/api-cursor.html)을 사용하여 반복될 수 있는 결과 집합을 반환합니다.

```
import pyodbc
server = 'yourserver.database.windows.net'
database = 'yourdatabase'
username = 'yourusername'
password = 'yourpassword'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
cursor.execute("select @@VERSION")
row = cursor.fetchone()
while row:
    print str(row[0]) + " " + str(row[1]) + " " + str(row[2])
    row = cursor.fetchone()
```

### <a name="insert-a-row-pass-parameters-and-retrieve-the-generated-primary-key"></a>행을 삽입하고, 매개 변수를 전달하며, 생성된 기본 키를 검색합니다.
SQL Database에서 [IDENTITY](https://msdn.microsoft.com/library/ms186775.aspx) 속성 및 [SEQUENCE](https://msdn.microsoft.com/library/ff878058.aspx) 개체를 사용하여 [기본 키](https://msdn.microsoft.com/library/ms179610.aspx) 값을 자동으로 생성할 수 있습니다. 

```
import pyodbc
server = 'yourserver.database.windows.net'
database = 'yourdatabase'
username = 'yourusername'
password = 'yourpassword'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
cursor.execute("select @@VERSION")
row = cursor.fetchone()
while row:
    print "Inserted Product ID : " +str(row[0])
    row = cursor.fetchone()
```

### <a name="transactions"></a>트랜잭션
이 코드 예제는 다음과 같은 트랜잭션의 사용법을 보여줍니다.

* 트랜잭션 시작
* 데이터 행 삽입
* 트랜잭션을 롤백하여 삽입 취소 

sql_sample.py 내부에 다음 코드를 붙여 넣습니다.

```
import pyodbc
server = 'yourserver.database.windows.net'
database = 'yourdatabase'
username = 'yourusername'
password = 'yourpassword'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
cursor.execute("BEGIN TRANSACTION")
cursor.execute("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express New', 'SQLEXPRESS New', 0, 0, CURRENT_TIMESTAMP)")
cnxn.rollback()
```

## <a name="next-steps"></a>다음 단계
* [SQL 데이터베이스 개발 개요](sql-database-develop-overview.md)
* [SQL Server용 Microsoft Python Driver](https://docs.microsoft.com/sql/connect/python/python-driver-for-sql-server/)
* [Python 개발자 센터](/develop/python/)를 방문합니다.

## <a name="additional-resources"></a>추가 리소스
* [Azure SQL 데이터베이스를 사용한 다중 테넌트 SaaS 응용 프로그램 디자인 패턴](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* 모든 [SQL 데이터베이스의 기능](https://azure.microsoft.com/services/sql-database/)

