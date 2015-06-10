<properties
	pageTitle="Windows에서 PHP를 사용하여 SQL 데이터베이스에 연결"
	description="Windows 클라이언트에서 Azure SQL 데이터베이스에 연결하는 샘플 PHP 프로그램을 제시하고, 클라이언트에 필요한 필수 소프트웨어 구성 요소의 링크를 제공합니다."
	services="sql-database"
	documentationCenter=""
	authors="MightyPen"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="php"
	ms.topic="article"
	ms.date="04/27/2015"
	ms.author="genemi"/>


# Windows에서 PHP를 사용하여 SQL 데이터베이스에 연결


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


이 항목에서는 Windows에서 실행되는 PHP로 작성된 클라이언트 응용 프로그램으로 Azure SQL 데이터베이스에 연결하는 방법을 보여줍니다.


## 필수 조건


이 항목에 있는 PHP 코드 샘플을 실행하려면 클라이언트 컴퓨터에 다음과 같은 소프트웨어가 설치되어 있어야 합니다.


- [Microsoft SQL Server용 Microsoft PHP 드라이버](http://www.microsoft.com/download/details.aspx?id=20098)(SQLSRV32.EXE에 최신 자료 포함)
- [Microsoft SQL Server Native Client 11.0](http://www.microsoft.com/download/details.aspx?id=36434)
- 특정 설치 작업은 [웹 플랫폼 설치 관리자](http://www.microsoft.com/web/downloads/platform.aspx)를 사용하여 수행해야 합니다. 자세한 내용은 다음 섹션을 참조하세요.


### 웹 플랫폼 설치 관리자를 사용한 설치


1. [웹 플랫폼 설치 관리자](http://www.microsoft.com/web/downloads/platform.aspx)를 다운로드 및 실행하고 필요한 모듈을 선택합니다.
2. [웹 플랫폼 설치 관리자](http://www.microsoft.com/web/downloads/platform.aspx)를 사용하여 다음 구성 요소를 설치합니다.
 - IIS 또는 IIS Express.<br/>예를 들어, [IIS Express 8.0 다운로드](http://www.microsoft.com/download/details.aspx?id=34679)는 웹에서 IIS Express 다운로드를 검색하면 찾을 수 있습니다.
 - Windows용 PHP, 버전 5.5 이상, 32비트 버전.
3. PHP.INI 파일 편집, Dynamic Extensions 섹션 아래에 다음과 같은 항목 추가:<br/>**extension=php_sqlsrv_55_nts.dll**


## SQL 데이터베이스 데이터베이스에 연결


이 **OpenConnection** 함수는 이어지는 모든 함수의 거의 최상위에서 호출됩니다.


	function OpenConnection()
	{
		try
		{
			$serverName = "tcp:myserver.database.windows.net,1433";

			$connectionOptions = array("Database"=>"AdventureWorks",
				"Uid"=>"MyUser", "PWD"=>"MyPassword");

			$conn = sqlsrv_connect($serverName, $connectionOptions);

			if($conn == false)
				die(FormatErrors(sqlsrv_errors()));
		}
		catch(Exception $e)
		{
			echo("Error!");
		}
	}


## 테이블에 값 삽입


	function InsertData()
	{
		try
		{
			$conn = OpenConnection();

			$tsql = "INSERT INTO [SalesLT].[Customer]
					   ([NameStyle],[FirstName],[MiddleName],[LastName],
					   [PasswordHash],[PasswordSalt],[rowguid],[ModifiedDate])
					   VALUES (?,?,?,?,?,?,?,?)";

			$params = array("0", "Luiz", "F", "Santos",
				"L/Rlwxzp4w7RWmEgXX+/A7cXaePEPcp+KwQhl2fJL7w=",
				"1KjXYs4=", "88949BFE-0BB4-4148-AFC2-DD8C8DAE4CD6",
				date("Y-m-d"));

			$insertReview = sqlsrv_prepare($conn, $tsql, $params);
			if($insertReview == FALSE)
				die(FormatErrors( sqlsrv_errors()));

			if(sqlsrv_execute($insertReview) == FALSE)
				die(FormatErrors(sqlsrv_errors()));

			sqlsrv_free_stmt($insertReview);

			sqlsrv_close($conn);
		}
		catch(Exception $e)
		{
			echo("Error!");
		}
	}


## 테이블에서 값 삭제


	function DeleteData()
	{
		try
		{
			$conn = OpenConnection();

			$tsql = "DELETE FROM [SalesLT].[Customer] WHERE FirstName=? AND LastName=?";
			$params = array($_REQUEST['FirstName'], $_REQUEST['LastName']);

			$deleteReview = sqlsrv_prepare($conn, $tsql, $params);
			if($deleteReview == FALSE)
				die(FormatErrors(sqlsrv_errors()));

			if(sqlsrv_execute($deleteReview) == FALSE)
				die(FormatErrors(sqlsrv_errors()));

			sqlsrv_free_stmt($deleteReview);

			sqlsrv_close($conn);
		}
		catch(Exception $e)
		{
			echo("Error!");
		}
	}


## 테이블에서 값 선택


	function ReadData()
	{
		try
		{
			$conn = OpenConnection();

			$tsql = "SELECT [CompanyName] FROM SalesLT.Customer";

			$getProducts = sqlsrv_query($conn, $tsql);

			if ($getProducts == FALSE)
				die(FormatErrors(sqlsrv_errors()));

			$productCount = 0;

			while($row = sqlsrv_fetch_array($getProducts, SQLSRV_FETCH_ASSOC))
			{
				echo($row['CompanyName']);
				echo("<br/>");
				$productCount++;
			}

			sqlsrv_free_stmt($getProducts);

			sqlsrv_close($conn);
		}
		catch(Exception $e)
		{
			echo("Error!");
		}
	}


## 트랜잭션


	function Transactions()
	{
		try
		{
			$conn = OpenConnection();

			if (sqlsrv_begin_transaction($conn) == FALSE)
				die(FormatErrors(sqlsrv_errors()));

			/* Initialize parameter values. */
			$orderId = 43659;
			$qty = 5;
			$productId = 709;
			$offerId = 1;
			$price = 5.70;

			/* Set up and execute the first query. */
			$tsql1 = "INSERT INTO SalesLT.SalesOrderDetail
			   (SalesOrderID,OrderQty,ProductID,SpecialOfferID,UnitPrice)
			   VALUES (?, ?, ?, ?, ?)";
			$params1 = array($orderId, $qty, $productId, $offerId, $price);
			$stmt1 = sqlsrv_query($conn, $tsql1, $params1);

			/* Set up and execute the second query. */
			$tsql2 = "UPDATE Production.ProductInventory SET Quantity = (Quantity - ?)
					  WHERE ProductID = ?";
			$params2 = array($qty, $productId);
			$stmt2 = sqlsrv_query( $conn, $tsql2, $params2 );

			/* If both queries were successful, commit the transaction. */
			/* Otherwise, rollback the transaction. */
			if($stmt1 && $stmt2)
			{
					sqlsrv_commit($conn);
					echo "Transaction was committed.\n";
			}
			else
			{
					sqlsrv_rollback($conn);
					echo "Transaction was rolled back.\n";
			}

			/* Free statement and connection resources. */
			sqlsrv_free_stmt( $stmt1);
			sqlsrv_free_stmt( $stmt2);

			sqlsrv_close($conn);
		}
		catch(Exception $e)
		{
			echo("Error!");
		}
	}


## 저장 프로시저


	function ExecuteSProc()
	{
		try
		{
			$conn = OpenConnection();

			$tsql = "EXEC [dbo].[uspLogError]";

			$execReview = sqlsrv_prepare($conn, $tsql);
			if($execReview == FALSE)
				die(FormatErrors( sqlsrv_errors()));

			if(sqlsrv_execute($execReview) == FALSE)
				die(FormatErrors(sqlsrv_errors()));

			sqlsrv_free_stmt($execReview);

			sqlsrv_close($conn);
		}
		catch(Exception $e)
		{
			echo("Error!");
		}
	}


## 추가 참고 자료


PHP 설치 및 사용에 대한 자세한 내용은[PHP로 SQL Server 데이터베이스 액세스](http://technet.microsoft.com/library/cc793139.aspx)를 참조하세요.

<!---HONumber=58-->