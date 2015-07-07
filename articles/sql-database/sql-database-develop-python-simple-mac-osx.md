<properties 
	pageTitle="Mac OS에서 Python을 사용하여 SQL 데이터베이스 연결" 
	description="Mac에서 Azure SQL 데이터베이스에 연결하는 데 사용할 수 있는 Python 코드 샘플을 제시합니다. 이 샘플에서는 pymssql 드라이버를 사용합니다."
	services="sql-database" 
	documentationCenter="" 
	authors="meet-bhagdev" 
	manager="jeffreyg" 
	editor=""/>


<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="06/04/2015" 
	ms.author="mebha"/>


# Mac OS에서 Python을 사용하여 SQL 데이터베이스 연결


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


이 항목에서는 Python으로 작성된 코드 샘플을 제시합니다. 이 샘플은 Mac 컴퓨터에서 실행됩니다. 이 샘플에서는 **pymssql** 드라이버를 사용하여 Azure SQL 데이터베이스에 연결합니다.


## 요구 사항


- [Python 2.7.6](https://www.python.org/download/releases/2.7.6/).


### 필요한 모듈 설치


터미널을 열고 설치

**1) Homebrew**: 터미널에서 다음 명령을 실행합니다. 그러면 컴퓨터에 Homebrew 패키지 관리자가 다운로드됩니다.

    ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

**2) FreeTDS**: 터미널에서 다음 명령을 실행합니다. 그러면 컴퓨터에 FreeTDS가 다운로드됩니다. pymmsql이 작동하려면 FreeTDS가 필요합니다.

    brew install FreeTDS
  
**3) Pymmsql**: 터미널에서 다음 명령을 실행합니다. 그러면 컴퓨터에 pymmsql이 설치됩니다.

    sudo -H pip install pymssql

### 데이터베이스를 만들고 연결 문자열 검색


샘플 데이터베이스를 만들고 연결 문자열을 가져오는 방법을 알아보려면 [시작 페이지](sql-database-get-started.md)를 참조하세요. 안내에 따라 **AdventureWorks 데이터베이스 템플릿**을 만드는 것이 중요합니다. 아래 표시된 샘플은 **AdventureWorks 스키마**에서만 작동합니다.


## SQL 데이터베이스 연결


SQL 데이터베이스에 연결하는 데 [pymssql.connect](http://pymssql.org/en/latest/ref/pymssql.html) 함수가 사용됩니다.

	import pymssql
	conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')


## SQL SELECT 문 실행

[cursor.execute](http://pymssql.org/en/latest/ref/pymssql.html#pymssql.Cursor.execute) 함수를 사용하여 SQL 데이터베이스에 대한 쿼리에서 결과 집합을 검색할 수 있습니다. 이 함수는 본질적으로 모든 쿼리를 허용하며, [cursor.fetchone()](http://pymssql.org/en/latest/ref/pymssql.html#pymssql.Cursor.fetchone)을 사용하여 반복될 수 있는 결과 집합을 반환합니다.


	import pymssql
	conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
	cursor = conn.cursor()
	cursor.execute('SELECT c.CustomerID, c.CompanyName,COUNT(soh.SalesOrderID) AS OrderCount FROM SalesLT.Customer AS c LEFT OUTER JOIN SalesLT.SalesOrderHeader AS soh ON c.CustomerID = soh.CustomerID GROUP BY c.CustomerID, c.CompanyName ORDER BY OrderCount DESC;')
	row = cursor.fetchone()
	while row:
	    print str(row[0]) + " " + str(row[1]) + " " + str(row[2]) 	
	    row = cursor.fetchone()


## 행을 삽입하고, 매개 변수를 전달하며, 생성된 기본 키를 검색합니다.

SQL 데이터베이스에서 [IDENTITY](https://msdn.microsoft.com/library/ms186775.aspx) 속성 및 [SEQUENECE](https://msdn.microsoft.com/library/ff878058.aspx) 개체를 사용하여 [기본 키](https://msdn.microsoft.com/library/ms179610.aspx) 값을 자동으로 생성할 수 있습니다.


	import pymssql
	conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
	cursor = conn.cursor()
	cursor.execute("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express', 'SQLEXPRESS', 0, 0, CURRENT_TIMESTAMP)")
	row = cursor.fetchone()
	while row:
	    print "Inserted Product ID : " +str(row[0])
	    row = cursor.fetchone()


## 트랜잭션


이 코드 예제는 다음과 같은 트랜잭션의 사용법을 보여줍니다.


-트랜잭션 시작

-데이터 행 삽입

-트랜잭션을 롤백하여 삽입 취소


	import pymssql
	conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
	cursor = conn.cursor()
	cursor.execute("BEGIN TRANSACTION")
	cursor.execute("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express New', 'SQLEXPRESS New', 0, 0, CURRENT_TIMESTAMP)")
	cnxn.rollback()

 

<!---HONumber=62-->