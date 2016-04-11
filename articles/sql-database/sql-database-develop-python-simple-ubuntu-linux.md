<properties
	pageTitle="Ubuntu에서 Python 및 pymssql을 사용하여 SQL 데이터베이스에 연결"
	description="Azure SQL 데이터베이스에 연결하는 데 사용할 수 있는 Python 코드 샘플을 제시합니다. 이 샘플은 Ubuntu Linux 클라이언트 컴퓨터에서 실행됩니다."
	services="sql-database"
	documentationCenter=""
	authors="meet-bhagdev"
	manager="jeffreyg"
	editor="genemi"/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="python"
	ms.topic="article"
	ms.date="03/14/2016"
	ms.author="meetb"/>


# Ubuntu Linux에서 Python을 사용하여 SQL 데이터베이스에 연결


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


이 항목에서는 Ubuntu Linux 클라이언트 컴퓨터에서 실행되며 Azure SQL 데이터베이스 데이터베이스에 연결하는 Python 코드 샘플을 제시합니다.


## 1단계: 개발 환경 구성

[SQL Server용 pymssql Python 드라이버를 사용하기 위한 필수 구성 요소](https://msdn.microsoft.com/library/mt694094.aspx#Ubuntu-Linux)

## 2단계: SQL 데이터베이스 만들기

샘플 데이터베이스를 만드는 방법을 알아보려면 [시작 페이지](sql-database-get-started.md)를 참조하세요. 안내에 따라 **AdventureWorks 데이터베이스 템플릿**을 만드는 것이 중요합니다. 아래 표시된 샘플은 **AdventureWorks 스키마**에서만 작동합니다.

## 3단계: 연결 정보 가져오기

[AZURE.INCLUDE [sql-database-include-connection-string-details-20-portalshots](../../includes/sql-database-include-connection-string-details-20-portalshots.md)]


## 4단계: 연결

SQL 데이터베이스에 연결하는 데 [pymssql.connect](http://pymssql.org/en/latest/ref/pymssql.html) 함수가 사용됩니다.

	import pymssql
	conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')

## 5단계: 쿼리 실행

[cursor.execute](http://pymssql.org/en/latest/ref/pymssql.html#pymssql.Cursor.execute) 함수를 사용하여 SQL 데이터베이스에 대한 쿼리에서 결과 집합을 검색할 수 있습니다. 이 함수는 본질적으로 모든 쿼리를 허용하며, [cursor.fetchone()](http://pymssql.org/en/latest/ref/pymssql.html#pymssql.Cursor.fetchone)을 사용하여 반복될 수 있는 결과 집합을 반환합니다.


	import pymssql
	conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
	cursor = conn.cursor()
	cursor.execute('SELECT c.CustomerID, c.CompanyName,COUNT(soh.SalesOrderID) AS OrderCount FROM SalesLT.Customer AS c LEFT OUTER JOIN SalesLT.SalesOrderHeader AS soh ON c.CustomerID = soh.CustomerID GROUP BY c.CustomerID, c.CompanyName ORDER BY OrderCount DESC;')
	row = cursor.fetchone()
	while row:
	    print str(row[0]) + " " + str(row[1]) + " " + str(row[2]) 	
	    row = cursor.fetchone()


## 6단계: 행 삽입

이 예제에서는 [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) 문을 안전하게 실행하고, [SQL 삽입](https://technet.microsoft.com/library/ms161953(v=sql.105).aspx) 취약성으로부터 응용 프로그램을 보호하는 매개 변수를 전달하며, 자동으로 생성된 [기본 키](https://msdn.microsoft.com/library/ms179610.aspx) 값을 검색하는 방법을 보여 줍니다.


	import pymssql
	conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
	cursor = conn.cursor()
	cursor.execute("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express', 'SQLEXPRESS', 0, 0, CURRENT_TIMESTAMP)")
	row = cursor.fetchone()
	while row:
	    print "Inserted Product ID : " +str(row[0])
	    row = cursor.fetchone()


## 7단계: 트랜잭션 롤백


이 코드 예제는 다음과 같은 트랜잭션의 사용법을 보여줍니다.


-트랜잭션 시작

-데이터 행 삽입

-트랜잭션을 롤백하여 삽입 취소


	import pymssql
	conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
	cursor = conn.cursor()
	cursor.execute("BEGIN TRANSACTION")
	cursor.execute("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express New', 'SQLEXPRESS New', 0, 0, CURRENT_TIMESTAMP)")
	conn.rollback()

## 다음 단계

자세한 내용은 [Python 개발자 센터](/develop/python/)를 참조하세요.

<!---HONumber=AcomDC_0330_2016-->