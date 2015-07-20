<properties 
	pageTitle="Windows에서 Java JDBC를 사용하여 SQL 데이터베이스에 연결합니다." 
	description="Azure SQL 데이터베이스에 연결하는 데 사용할 수 있는 Java 코드 샘플을 제시합니다. 이 샘플에서는 JDBC를 사용하며, Windows 클라이언트 컴퓨터에서 실행됩니다."
	services="sql-database" 
	documentationCenter="" 
	authors="LuisBosquez" 
	manager="jeffreyg" 
	editor="genemi"/>


<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="06/25/2015" 
	ms.author="lbosq"/>


# Windows에서 Java JDBC를 사용하여 SQL 데이터베이스에 연결합니다.


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


이 항목에서는 Azure SQL 데이터베이스에 연결하는 데 사용할 수 있는 Java 코드 샘플을 제시합니다. Java 샘플을 실행하려면 Java 개발 키트(JDK) 버전 1.8이 필요합니다. 이 샘플에서는 JDBC 드라이버를 사용하여 Azure SQL 데이터베이스에 연결합니다.


## 요구 사항


- [Microsoft SQL Server용 JDBC 드라이버 - SQL JDBC 4](http://www.microsoft.com/download/details.aspx?displaylang=en&id=11774).
- [Java 개발 키트 1.8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)이 실행되는 운영 체제 플랫폼.
- SQL Azure에 있는 기존 데이터베이스. 샘플 데이터베이스를 만들고 연결 문자열을 검색하는 방법을 알아보려면 [시작 항목](sql-database-get-started.md)을 참조하세요.


## 테스트 환경


이 항목에 있는 Java 코드 예제에서는 Azure SQL 데이터베이스 데이터베이스에 다음 테스트 테이블이 이미 있는 것으로 가정합니다.


<!--
Could this instead be a #tempPerson table, so that the Java code sample could be fully self-sufficient and be runnable (with automatic cleanup)?
-->


	CREATE TABLE Person
	(
		id         INT    PRIMARY KEY    IDENTITY(1,1),
		firstName  VARCHAR(32),
		lastName   VARCHAR(32),
		age        INT
	);


## SQL 데이터베이스에 대한 연결 문자열


이 코드 샘플은 연결 문자열을 사용하여 `Connection` 개체를 만듭니다. 연결 문자열은 [Azure Preview 포털](http://portal.azure.com/)을 사용하여 찾을 수 있습니다. 연결 문자열 찾기에 대한 자세한 내용은 [처음으로 Azure SQL 데이터베이스 만들기](sql-database-get-started.md)를 참조하세요.


## Java 코드 샘플


이 섹션에는 Java 코드 샘플의 대부분이 있습니다. 여기에는 후속 섹션에 나오는 더 작은 Java 세그먼트를 어디에 복사하여 붙여넣어야 하는지 표시하는 주석이 있습니다. 이 섹션의 샘플은 주석 근처에서 복사하여 붙여넣지 않아도 컴파일하고 실행할 수 있지만, 그러면 연결한 뒤 바로 종료됩니다. 다음과 같은 주석을 찾아볼 수 있습니다.


1. `// INSERT two rows into the table.`
2. `// TRANSACTION and commit for an UPDATE.`
3. `// SELECT rows from the table.`


다음은 Java 코드 샘플의 대부분 내용입니다. 샘플에는 `SQLDatabaseTest` 클래스의 `main` 함수가 포함되어 있습니다.


	import java.sql.*;
	import com.microsoft.sqlserver.jdbc.*;
	
	public class SQLDatabaseTest {
	
		public static void main(String[] args) {
			String connectionString =
				"jdbc:sqlserver://your_server.database.windows.net:1433;" 
				+ "database=your_database;"
				+ "user=your_user@your_server;"
				+ "password={your_password};"
				+ "encrypt=true;"
				+ "trustServerCertificate=false;"
				+ "hostNameInCertificate=*.database.windows.net;"
				+ "loginTimeout=30;"; 
	
			// Declare the JDBC objects.
			Connection connection = null;
			Statement statement = null;
			ResultSet resultSet = null;
			PreparedStatement prepsInsertPerson = null;
			PreparedStatement prepsUpdateAge = null;
	
			try {
				connection = DriverManager.getConnection(connectionString);
	
				// INSERT two rows into the table.
				// ...
	
				// TRANSACTION and commit for an UPDATE.
				// ...
	
				// SELECT rows from the table.
				// ...
			}
			catch (Exception e) {
				e.printStackTrace();
			}
			finally {
				// Close the connections after the data has been handled.
				if (prepsInsertPerson != null) try { prepsInsertPerson.close(); } catch(Exception e) {}
				if (prepsUpdateAge != null) try { prepsUpdateAge.close(); } catch(Exception e) {}
				if (resultSet != null) try { resultSet.close(); } catch(Exception e) {}
				if (statement != null) try { statement.close(); } catch(Exception e) {}
				if (connection != null) try { connection.close(); } catch(Exception e) {}
			}
		}
	}


물론 앞의 Java 코드 샘플을 실제로 실행하려면 연결 문자열에 실제 값을 입력하여 자리 표시자를 대체해야 합니다.


- your_server
- your_database
- your_user
- your_password


## 테이블에 두 행 삽입


이 Java 세그먼트는 Person 테이블에 두 행을 삽입하는 Transact-SQL INSERT 문을 발행합니다. 일반적인 순서는 다음과 같습니다.


1. `Connection` 개체를 사용하여 `PreparedStatement` 개체를 생성합니다.
 - 나중에 **id** 키 값으로 자동 생성되는 값을 가져올 수 있도록 매개 변수 `Statement.RETURN_GENERATED_KEYS`을(를) 포함시켰습니다.
2. `PreparedStatement` 개체에서 `execute` 메서드를 호출합니다.
3. `PreparedStatement` 개체를 사용하여 기본 키에 대해 자동으로 생성된 숫자 값을 가져옵니다.
 - 이는 Person 테이블의 **id** 열에 있는 AUTO_INCREMENT 사양과 관련됩니다.


이 짧은 Java 세그먼트를 주석 `// INSERT two rows into the table.`이(가) 표시된 기본 코드 샘플에 복사하여 붙여넣습니다.


	// Create and execute an INSERT SQL prepared statement.
	String insertSql = "INSERT INTO Person (firstName, lastName, age) VALUES "
		+ "('Bill', 'Gates', 59), "
		+ "('Steve', 'Ballmer', 59);";
	
	prepsInsertPerson = connection.prepareStatement(
		insertSql,
		Statement.RETURN_GENERATED_KEYS);
	prepsInsertPerson.execute();
	// Retrieve the generated key from the insert.
	resultSet = prepsInsertPerson.getGeneratedKeys();
	// Iterate through the set of generated keys.
	while (resultSet.next()) {
		System.out.println("Generated: " + resultSet.getString(1));
	}


## 트랜잭션 및 업데이트에 대한 커밋


다음 Java 코드 세그먼트는 Person 테이블의 모든 행에서 `age` 값을 증가시키는 Transact-SQL UPDATE 문을 발행합니다. 일반적인 순서는 다음과 같습니다.


1. 업데이트가 데이터베이스에서 자동으로 커밋되는 것을 방지하기 위해 `setAutoCommit` 메서드가 호출됩니다.
2. 트랜잭션의 컨텍스트에서 업데이트를 실행하기 위해 `executeUpdate` 메서드가 호출됩니다.
3. 트랜잭션을 명시적으로 커밋하기 위해 `commit` 메서드가 호출됩니다.


이 짧은 Java 세그먼트를 주석 `// TRANSACTION and commit for an UPDATE.`이(가) 표시된 기본 코드 샘플에 복사하여 붙여넣습니다.


	// Set AutoCommit value to false to execute a single transaction at a time.
	connection.setAutoCommit(false);
	
	// Write the SQL Update instruction and get the PreparedStatement object.
	String transactionSql = "UPDATE Person SET Person.age = Person.age + 1;";
	prepsUpdateAge = connection.prepareStatement(transactionSql);
	
	// Execute the statement.
	prepsUpdateAge.executeUpdate();
	
	//Commit the transaction.
	connection.commit();
	
	// Return the AutoCommit value to true.
	connection.setAutoCommit(true);


## 테이블에서 행 선택


이 Java 세그먼트는 Person 테이블에서 업데이트된 모든 행이 표시되도록 Transact-SQL SELECT 문을 실행합니다. 일반적인 순서는 다음과 같습니다.


1. `Connection` 개체를 사용하여 `Statement` 개체를 생성합니다.
2. `Statement` 개체를 사용하여 `ResultSet` 개체를 생성합니다.
3. `resultSet.next`에 대한 호출에 루프를 구성하여 반환된 행을 모두 반복합니다.


이 짧은 Java 세그먼트를 주석 `// SELECT rows from a table.`이(가) 표시된 기본 코드 샘플에 복사하여 붙여넣습니다.


	// Create and execute a SELECT SQL statement.
	String selectSql = "SELECT firstName, lastName, age FROM dbo.Person";
	statement = connection.createStatement();
	resultSet = statement.executeQuery(selectSql);
	
	// Iterate through the result set and print the attributes.
	while (resultSet.next()) {
		System.out.println(resultSet.getString(2) + " "
			+ resultSet.getString(3));
	}

 

<!---HONumber=July15_HO2-->