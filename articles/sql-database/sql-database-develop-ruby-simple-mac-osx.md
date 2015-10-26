<properties 
	pageTitle="Mac OS X(Yosemite)의 TinyTDS와 함께 Ruby를 사용하여 SQL 데이터베이스에 연결" 
	description="Ruby 코드 샘플을 제공하면 Mac OS X(Yosemite)에서 실행하여 Azure SQL 데이터베이스에 연결할 수 있습니다."
	services="sql-database" 
	documentationCenter="" 
	authors="ajlam" 
	manager="jeffreyg" 
	editor=""/>


<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="ruby" 
	ms.topic="article" 
	ms.date="07/20/2015" 
	ms.author="andrela"/>


# Mac OS X(Yosemite)에서 Ruby를 사용하여 SQL 데이터베이스에 연결

[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

이 항목에서는 Yosemite를 실행 중인 Mac 컴퓨터에서 실행하여 Azure SQL 데이터베이스 데이터베이스에 연결하는 Ruby 코드 샘플을 표시합니다.

## 필요한 모듈 설치

터미널을 열고 다음을 설치합니다.

**1) Homebrew**: 터미널에서 다음 명령을 실행합니다. 그러면 컴퓨터에 Homebrew 패키지 관리자가 다운로드됩니다.

    ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

**2) FreeTDS:** 터미널에서 다음 명령을 실행합니다. 사용자 컴퓨터에 FreeTDS를 설치되며 TinyTDS가 작동하는 데 필요합니다.

    brew install FreeTDS

**3) TinyTDS:** 터미널에서 다음 명령을 실행합니다. 그러면 컴퓨터에 TinyTDS가 설치됩니다.

    gem install tiny_tds

## 데이터베이스를 만들고 연결 문자열 검색

Ruby 샘플은 AdventureWorks 샘플 데이터베이스를 사용합니다. AdventureWorks가 아직 없는 경우 [첫번째 Azure SQL 데이터베이스 만들기](sql-database-get-started.md) 토픽에서 만드는 방법을 볼 수 있습니다.

또한 해당 토픽에서 데이터베이스 연결 문자열을 검색하는 방법을 설명합니다.

## SQL 데이터베이스 연결

[TinyTDS::Client](https://github.com/rails-sqlserver/tiny_tds) 함수는 SQL 데이터베이스에 연결하는 데 사용됩니다.

    require 'tiny_tds' 
    client = TinyTds::Client.new username: 'yourusername@yourserver', password: 'yourpassword', 
    host: 'yourserver.database.windows.net', port: 1433, 
    database: 'AdventureWorks', azure:true 

## SELECT 문을 실행하고 결과 집합 검색

[TinyTds::Result](https://github.com/rails-sqlserver/tiny_tds) 함수는 SQL 데이터베이스에 대한 쿼리에서 결과 집합을 검색 하는데 사용됩니다. 이 함수는 쿼리를 허용하고 결과 집합을 반환합니다. 결과 집합은 [result.each do |row|](https://github.com/rails-sqlserver/tiny_tds)를 사용하여 반복됩니다.

    require 'tiny_tds'  
    print 'test'     
    client = TinyTds::Client.new username: 'yourusername@yourserver', password: 'yourpassword', 
    host: 'yourserver.database.windows.net', port: 1433, 
    database: 'AdventureWorks', azure:true 
    results = client.execute("select * from SalesLT.Product") 
    results.each do |row| 
    puts row 
    end 

## 행을 삽입하고, 매개 변수를 전달하며, 생성된 기본 키 값을 검색합니다.

코드 샘플:

- 행에 삽입할 값에 대한 매개 변수를 전달합니다.
- 행을 삽입합니다.
- 기본 키에 대해 생성된 값을 검색합니다.

SQL 데이터베이스에서 [IDENTITY](http://msdn.microsoft.com/library/ms186775.aspx) 속성 및 [SEQUENECE](http://msdn.microsoft.com/library/ff878058.aspx) 개체를 사용하여 [기본 키 값](http://msdn.microsoft.com/library/ms179610.aspx)을 자동으로 생성할 수 있습니다.

TinyTDS Azure를 사용 하려면 것이 좋습니다 여러 실행`SET`문을 현재 세션에서 특정 정보를 처리 하는 방법을 변경할 수 있습니다. 권장 `SET`문을 코드 샘플에 제공 됩니다. 예를 들어,`SET ANSI_NULL_DFLT_ON`열의 null 허용 여부 상태가 명시적으로 명시되지 않은 경우에 null 값을 허용하기 위해 만든 새 열을 허용합니다.

Microsoft SQL Server [datetime](http://msdn.microsoft.com/library/ms187819.aspx)포맷에 맞게, [strftime](http://ruby-doc.org/core-2.2.0/Time.html#method-i-strftime) 함수를 사용하여 해당 날짜/시간 형식으로 캐스팅합니다.

    require 'tiny_tds' 
    client = TinyTds::Client.new username: 'yourusername@yourserver', password: 'yourpassword', 
    host: 'yourserver.database.windows.net', port: 1433, 
    database: 'AdventureWorks', azure:true 
    results = client.execute("SET ANSI_NULLS ON")
    results = client.execute("SET CURSOR_CLOSE_ON_COMMIT OFF")
    results = client.execute("SET ANSI_NULL_DFLT_ON ON")
    results = client.execute("SET IMPLICIT_TRANSACTIONS OFF")
    results = client.execute("SET ANSI_PADDING ON")
    results = client.execute("SET QUOTED_IDENTIFIER ON")
    results = client.execute("SET ANSI_WARNINGS ON")
    results = client.execute("SET CONCAT_NULL_YIELDS_NULL ON")
    require 'date'
    t = Time.now
    curr_date = t.strftime("%Y-%m-%d %H:%M:%S.%L") 
    results = client.execute("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) 
    OUTPUT INSERTED.ProductID VALUES ('SQL Server Express New', 'SQLEXPRESS New', 0, 0, '#{curr_date}' )")
    results.each do |row| 
    puts row
    end

<!---HONumber=Oct15_HO3-->