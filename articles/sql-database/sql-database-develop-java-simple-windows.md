<properties
	pageTitle="Windows에서 Java JDBC를 사용하여 SQL 데이터베이스에 연결합니다."
	description="Azure SQL 데이터베이스에 연결하는 데 사용할 수 있는 Java 코드 샘플을 제시합니다. 이 샘플에서는 JDBC를 사용하며, Windows 클라이언트 컴퓨터에서 실행됩니다."
	services="sql-database"
	documentationCenter=""
	authors="ajlam"
	manager="jhubbard"
	editor="genemi"/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="java"
	ms.topic="article"
	ms.date="04/04/2016"
	ms.author="andrela"/>


# Windows에서 Java JDBC를 사용하여 SQL 데이터베이스에 연결합니다.


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


이 항목에서는 Azure SQL 데이터베이스에 연결하는 데 사용할 수 있는 Java 코드 샘플을 제시합니다. Java 샘플을 실행하려면 Java 개발 키트(JDK) 버전 1.8이 필요합니다. 이 샘플에서는 JDBC 드라이버를 사용하여 Azure SQL 데이터베이스에 연결합니다.

## 1단계: 개발 환경 구성

드라이버 및 라이브러리 설치

- [SQL Server용 Microsoft JDBC 4.2 드라이버](http://www.microsoft.com/download/details.aspx?displaylang=en&id=11774).
- [Java 개발 키트 1.8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)이 실행되는 운영 체제 플랫폼.

## 2단계: SQL 데이터베이스 만들기

샘플 데이터베이스를 만드는 방법을 알아보려면 [시작 페이지](sql-database-get-started.md)를 참조하세요. 안내에 따라 **AdventureWorks 데이터베이스 템플릿**을 만드는 것이 중요합니다. 아래 표시된 샘플은 **AdventureWorks 스키마**에서만 작동합니다.

## 3단계: 연결 문자열 가져오기

[AZURE.INCLUDE [sql-database-include-connection-string-jdbc-20-portalshots](../../includes/sql-database-include-connection-string-jdbc-20-portalshots.md)]

> [AZURE.NOTE] JTDS JDBC 드라이버를 사용하는 경우 연결 문자열의 URL에 "ssl=require"를 추가해야 하며, JVM "-Djsse.enableCBCProtection=false"에 대해 다음 옵션을 설정해야 합니다. 이 JVM 옵션은 보안 취약성에 대한 픽스를 사용하지 않도록 설정하므로 이 옵션을 설정하기 전에 관련된 위험을 이해해야 합니다.

## 4단계: 연결

연결 클래스를 사용하여 SQL 데이터베이스에 연결합니다.

	// Use the JDBC driver
	import java.sql.*;
	import com.microsoft.sqlserver.jdbc.*;
	
		public class SQLDatabaseConnection {
	
			// Connect to your database.
			// Replace server name, username, and password with your credentials
			public static void main(String[] args) {
				String connectionString =
					"jdbc:sqlserver://yourserver.database.windows.net:1433;"
					+ "database=AdventureWorks;"
					+ "user=yourusername@yourserver;"
					+ "password=yourpassword;"
					+ "encrypt=true;"
					+ "trustServerCertificate=false;"
					+ "hostNameInCertificate=*.database.windows.net;"
					+ "loginTimeout=30;";
			
				// Declare the JDBC objects.
				Connection connection = null;
							
				try {
					connection = DriverManager.getConnection(connectionString);
	
				}
				catch (Exception e) {
					e.printStackTrace();
				}
				finally {
					if (connection != null) try { connection.close(); } catch(Exception e) {}
				}
			}
		}

## 5단계: 쿼리 실행
이 샘플에서는 Azure SQL 데이터베이스에 연결하고 SELECT 문을 실행하며 선택한 행을 반환합니다.

	// Use the JDBC driver
	import java.sql.*;
	import com.microsoft.sqlserver.jdbc.*;
	
		public class SQLDatabaseConnection {
	
			// Connect to your database.
			// Replace server name, username, and password with your credentials
			public static void main(String[] args) {
				String connectionString =
					"jdbc:sqlserver://yourserver.database.windows.net:1433;"
					+ "database=AdventureWorks;"
					+ "user=yourusername@yourserver;"
					+ "password=yourpassword;"
					+ "encrypt=true;"
					+ "trustServerCertificate=false;"
					+ "hostNameInCertificate=*.database.windows.net;"
					+ "loginTimeout=30;";
			
				// Declare the JDBC objects.
				Connection connection = null;
				Statement statement = null; 
				ResultSet resultSet = null;
							
				try {
					connection = DriverManager.getConnection(connectionString);
	
					// Create and execute a SELECT SQL statement.
					String selectSql = "SELECT TOP 10 Title, FirstName, LastName from SalesLT.Customer";
					statement = connection.createStatement();
					resultSet = statement.executeQuery(selectSql);
	
					// Print results from select statement
					while (resultSet.next()) 
					{
						System.out.println(resultSet.getString(2) + " "
							+ resultSet.getString(3));
					}
				}
				catch (Exception e) {
					e.printStackTrace();
				}
				finally {
					// Close the connections after the data has been handled.
					if (resultSet != null) try { resultSet.close(); } catch(Exception e) {}
					if (statement != null) try { statement.close(); } catch(Exception e) {}
					if (connection != null) try { connection.close(); } catch(Exception e) {}
				}
			}
		}

## 6단계: 행 삽입
이 예제에서는 INSERT 문을 실행하고 매개 변수를 전달하며 자동 생성된 기본 키 값을 검색합니다.

	// Use the JDBC driver
	import java.sql.*;
	import com.microsoft.sqlserver.jdbc.*;
	
		public class SQLDatabaseConnection {
	
			// Connect to your database.
			// Replace server name, username, and password with your credentials
			public static void main(String[] args) {
				String connectionString =
					"jdbc:sqlserver://yourserver.database.windows.net:1433;"
					+ "database=AdventureWorks;"
					+ "user=yourusername@yourserver;"
					+ "password=yourpassword;"
					+ "encrypt=true;"
					+ "trustServerCertificate=false;"
					+ "hostNameInCertificate=*.database.windows.net;"
					+ "loginTimeout=30;";
			
				// Declare the JDBC objects.
				Connection connection = null;
				Statement statement = null; 
				ResultSet resultSet = null;
				PreparedStatement prepsInsertProduct = null;
				
				try {
					connection = DriverManager.getConnection(connectionString);
	
					// Create and execute an INSERT SQL prepared statement.
					String insertSql = "INSERT INTO SalesLT.Product (Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) VALUES "
						+ "('NewBike', 'BikeNew', 'Blue', 50, 120, '2016-01-01');";
	
					prepsInsertProduct = connection.prepareStatement(
						insertSql,
						Statement.RETURN_GENERATED_KEYS);
					prepsInsertProduct.execute();
					
					// Retrieve the generated key from the insert.
					resultSet = prepsInsertProduct.getGeneratedKeys();
					
					// Print the ID of the inserted row.
					while (resultSet.next()) {
						System.out.println("Generated: " + resultSet.getString(1));
					}
				}
				catch (Exception e) {
					e.printStackTrace();
				}
				finally {
					// Close the connections after the data has been handled.
					if (prepsInsertProduct != null) try { prepsInsertProduct.close(); } catch(Exception e) {}
					if (resultSet != null) try { resultSet.close(); } catch(Exception e) {}
					if (statement != null) try { statement.close(); } catch(Exception e) {}
					if (connection != null) try { connection.close(); } catch(Exception e) {}
				}
			}
		}


## 다음 단계

자세한 내용은 [Java개발자 센터](/develop/java/)를 참조하세요.

<!---HONumber=AcomDC_0413_2016-->