<properties
	pageTitle="SQL DB에 대한 Windows의 PHP | Microsoft Azure"
	description="일시적인 오류 처리를 지원하는 Windows 클라이언트에서 Azure SQL 데이터베이스에 연결하는 샘플 PHP 프로그램을 제시하고, 클라이언트에 필요한 필수 소프트웨어 구성 요소의 링크를 제공합니다."
	services="sql-database"
	documentationCenter=""
	authors="meet-bhagdev"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="php"
	ms.topic="article"
	ms.date="07/21/2015"
	ms.author="mebha"/>


# 일시적인 오류 처리를 지원하는 Windows에서 PHP를 사용하여 SQL 데이터베이스에 연결


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


이 항목에서는 Windows에서 실행되는 PHP로 작성된 클라이언트 응용 프로그램으로 Azure SQL 데이터베이스에 연결하는 방법을 보여줍니다.


[AZURE.INCLUDE [sql-database-develop-includes-prerequisites-php-windows](../../includes/sql-database-develop-includes-prerequisites-php-windows.md)]


## 데이터베이스를 만들고 연결 문자열 검색


샘플 데이터베이스를 만들고 연결 문자열을 검색하는 방법을 알아보려면 [시작 항목](sql-database-get-started.md)을 참조하세요. 안내에 따라 **AdventureWorks 데이터베이스 템플릿**을 만드는 것이 중요합니다. 아래 표시된 샘플은 **AdventureWorks 스키마**에서만 작동합니다.


## 데이터베이스에 연결하고 쿼리합니다. 

데모 프로그램은 연결 시도 중 일시적인 오류로 재시도하도록 디자인되었습니다. 하지만 쿼리 명령 동안 일시적 오류로 프로그램이 연결을 무시하고 쿼리 명령을 다시 시도하기 전에 새 연결을 만듭니다. 이 디자인 선택을 권장하지 않거나, 또는 권장하지도 않습니다. 데모 프로그램은 사용할 수 있는 디자인 유연성 중 일부를 보여줍니다.

<br>이 코드 샘플의 길이는 주로 catch 예외 논리 때문입니다. 이 Program.cs 파일의 짧은 버전은 [여기](sql-database-develop-php-simple-windows.md)에서 사용할 수 있습니다. <br>Main 메서드는 Program.cs에 있습니다. 호출 스택은 다음과 같이 실행됩니다. \* Main ConnectAndQuery를 호출합니다. \* ConnectAndQuery EstablishConnection를 호출합니다. \* EstablishConnection IssueQueryCommand를 호출합니다.

[sqlsrv\_query()](http://php.net/manual/en/function.sqlsrv-query.php) 함수를 사용하여 SQL 데이터베이스에 대한 쿼리에서 결과 집합을 검색할 수 있습니다. 이 함수는 본질적으로 모든 쿼리를 허용하며, [sqlsrv\_fetch\_array()](http://php.net/manual/en/function.sqlsrv-fetch-array.php)를 사용하여 반복될 수 있는 결과 집합을 반환합니다.

	<?php
		// Variables to tune the retry logic.  
		$connectionTimeoutSeconds = 30;  // Default of 15 seconds is too short over the Internet, sometimes.
		$maxCountTriesConnectAndQuery = 3;  // You can adjust the various retry count values.
		$secondsBetweenRetries = 4;  // Simple retry strategy.
		$errNo = 0;
		$serverName = "tcp:yourdatabase.database.windows.net,1433";
		$connectionOptions = array("Database"=>"AdventureWorks",
		   "Uid"=>"yourusername", "PWD"=>"yourpassword", "LoginTimeout" => $connectionTimeoutSeconds);
		$conn;
		$errorArr = array();
		for ($cc = 1; $cc <= $maxCountTriesConnectAndQuery; $cc++)
		{
		    $errorArr = array();
		    $ctr = 0;
		    // [A.2] Connect, which proceeds to issue a query command. 
		    $conn = sqlsrv_connect($serverName, $connectionOptions);  
		    if( $conn == true)
		    {
		        echo "Connection was established"; 
		        echo "<br>";
		 
		        $tsql = "SELECT [CompanyName] FROM SalesLT.Customer";
		        $getProducts = sqlsrv_query($conn, $tsql);
		        if ($getProducts == FALSE)
		            die(FormatErrors(sqlsrv_errors()));
		        $productCount = 0;
		        $ctr = 0;
		        while($row = sqlsrv_fetch_array($getProducts, SQLSRV_FETCH_ASSOC))
		        {   
		            $ctr++;
		            echo($row['CompanyName']);
		            echo("<br/>");
		            $productCount++;
		            if($ctr>10)
		                break;
		        }
		        sqlsrv_free_stmt($getProducts);
		        break;
		    }
		    // Adds any the error codes from the SQL Exception to an array.
		    else {  
		        if( ($errors = sqlsrv_errors() ) != null) {
		            foreach( $errors as $error ) {
		                $errorArr[$ctr] = $error['code'];
		                $ctr = $ctr + 1;
		            }
		        }
		        $isTransientError = TRUE;
		        // [A.4] Check whether sqlExc.Number is on the whitelist of transients.
		        $isTransientError = IsTransientStatic($errorArr);
		        if ($isTransientError == TRUE)  // Is a static persistent error...
		        { 
		            echo("Persistent error suffered, SqlException.Number==". $errorArr[0].". Program Will terminate."); 
		            echo "<br>";
		            // [A.5] Either the connection attempt or the query command attempt suffered a persistent SqlException.
		            // Break the loop, let the hopeless program end.
		            exit(0);
		        }
		        // [A.6] The SqlException identified a transient error from an attempt to issue a query command.
		        // So let this method reloop and try again. However, we recommend that the new query
		        // attempt should start at the beginning and establish a new connection.
		        if ($cc >= $maxCountTriesConnectAndQuery)
		        {
		            echo "Transient errors suffered in too many retries - " . $cc ." Program will terminate.";
		            echo "<br>";
		            exit(0);
		        }
		        echo("Transient error encountered.  SqlException.Number==". $errorArr[0]. " . Program might retry by itself.");  
		        echo "<br>";
		        echo $cc . " Attempts so far. Might retry.";
		        echo "<br>";
		        // A very simple retry strategy, a brief pause before looping. This could be changed to exponential if you want.
		        sleep(1*$secondsBetweenRetries);
		    }
		    // [A.3] All has gone well, so let the program end.
		}
		function IsTransientStatic($errorArr) {
		    $arrayOfTransientErrorNumbers = array(4060, 10928, 10929, 40197, 40501, 40613);
		    $result = array_intersect($arrayOfTransientErrorNumber, $errorArr);
		    $count = count($result);
		    if($count >= 0) //change to > 0 later.
		        return TRUE;
		}
	?>
	
## 추가 참고 자료


PHP 설치 및 사용에 대한 자세한 내용은[PHP로 SQL Server 데이터베이스 액세스](http://technet.microsoft.com/library/cc793139.aspx)를 참조하세요.

 

<!---HONumber=July15_HO5-->