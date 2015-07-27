<properties 
	pageTitle="SQL Azure(Java)를 사용하는 방법 - Azure 기능 가이드" 
	description="Java 코드에서 Azure SQL 데이터베이스를 사용하는 방법에 대해 알아봅니다." 
	services="sql-database" 
	documentationCenter="java" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="06/03/2015" 
	ms.author="robmcm"/>

# Java에서 Azure SQL 데이터베이스를 사용하는 방법

다음 단계는 Java에서 Azure SQL 데이터베이스를 사용하는 방법을 보여 줍니다. 간단히 하기 위해 명령줄 예제가 표시되어 있지만, Azure 내에 온-프레미스 또는 다른 환경에 호스트된 웹 응용 프로그램에 대해 아주 유사한 단계가 적용됩니다. 이 가이드에서는 [Azure 관리 포털](https://windows.azure.com)에서 서버 생성 및 데이터베이스 생성에 대해 다룹니다.

## Azure SQL 데이터베이스 정의

Azure SQL 데이터베이스는 Azure에 관계형 데이터베이스 관리 시스템을 제공하며 SQL Server 기술을 기반으로 합니다. SQL 데이터베이스 인스턴스를 사용하여 쉽게 클라우드에 관계형 데이터베이스 솔루션을 프로비전하고 배포할 수 있으며 데이터 보호와 자동 복구가 기본 제공되는 혜택을 갖춘 엔터프라이즈급 가용성, 확장성, 보안을 제공하는 분산 데이터 센터를 활용할 수 있습니다.



## 개념
Azure SQL 데이터베이스는 SQL Server 기술을 기반으로 하므로 Java에서 SQL 데이터베이스에 액세스하는 것은 Java에서 SQL Server에 액세스하는 것과 매우 유사합니다. 로컬에서 SQL Server를 사용하여 응용 프로그램을 개발한 후 연결 문자열만 변경하여 SQL 데이터베이스에 연결할 수 있습니다. 응용 프로그램에 대해 SQL Server JDBC 드라이버를 사용할 수 있습니다. 그러나 응용 프로그램에 영향을 줄 수 있는 SQL 데이터베이스와 SQL Server 간의 몇 가지 차이점이 있습니다. 자세한 내용은 [지침 및 제한 사항(SQL 데이터베이스)](http://msdn.microsoft.com/library/windowsazure/ff394102.aspx)을 참조하십시오.

SQL 데이터베이스에 대한 추가 리소스는 [다음 단계][] 섹션을 참조하십시오.

## 필수 조건

다음은 Java에서 SQL 데이터베이스를 사용하려는 경우 필수 조건입니다.

* JDK(Java 개발자 키트), v 1.6 이상
* Azure 구독. <http://www.microsoft.com/windowsazure/offers/>에서 구입할 수 있습니다.
* Eclipse를 사용하는 경우 Eclipse IDE for Java EE Developers, Indigo 이상이 필요합니다. <http://www.eclipse.org/downloads/>에서 다운로드할 수 있습니다. 또한 Eclipse용 Azure 플러그 인(Java 포함)(Microsoft Open Technologies 제공)이 필요합니다. 이 플러그인을 설치하는 동안 Microsoft JDBC Driver 4.0 for SQL Server가 포함되어 있는지 확인하세요. 자세한 내용은 [Eclipse용 Azure 플러그 인(Java 포함)(Microsoft Open Technologies 제공) 설치](http://msdn.microsoft.com/library/windowsazure/hh690946.aspx)(영문)를 참조하세요.
* Eclipse를 사용하지 않는 경우 <http://www.microsoft.com/download/details.aspx?id=11774>에서 다운로드할 수 있는 SQL Server용 Microsoft JDBC Driver 4.0이 필요합니다.

## Azure SQL 데이터베이스 만들기

Java 코드에서 Azure SQL 데이터베이스를 사용하기 전에 Azure SQL 데이터베이스 서버를 만들어야 합니다.

1. [Azure 관리 포털](https://manage.windowsazure.com)에 로그인합니다.
2. **새로 만들기**를 클릭합니다.

    ![새 SQL 데이터베이스 만들기][create_new]

3. **SQL 데이터베이스**를 클릭한 후 **사용자 지정 만들기**를 클릭합니다.

    ![사용자 지정 SQL 데이터베이스 만들기][create_new_sql_db]

4. **데이터베이스 설정** 대화 상자에서 데이터베이스 이름을 지정합니다. 이 가이드에서는 데이터베이스 이름으로 **gettingstarted**를 사용합니다.
5. **서버**에 대해 **새 SQL 데이터베이스 서버**를 선택합니다. 다른 필드에 대해 기본값을 사용합니다.

    ![SQL 데이터베이스 설정][create_database_settings]

6. 다음 화살표를 클릭합니다.
7. **서버 설정** 대화 상자에서 SQL Server 로그인 이름을 지정합니다. 이 가이드에서는 **MySQLAdmin**이 사용되었습니다. 암호를 지정하고 확인합니다. 지역을 지정하고 **Allow Azure Services to access the server**가 선택되어 있는지 확인합니다.

    ![SQL 서버 설정][create_server_settings]

8. 완료 단추를 클릭합니다.

## SQL 데이터베이스 연결 문자열 확인

1. [Azure 관리 포털](https://manage.windowsazure.com)에 로그인합니다.
2. **SQL 데이터베이스**를 클릭합니다.
3. 사용할 데이터베이스를 클릭합니다.
4. **연결 문자열 표시**를 클릭합니다.
5. **JDBC** 연결 문자열의 내용을 강조 표시합니다.

    ![JDBC 연결 문자열 확인][get_jdbc_connection_string]

6. **JDBC** 연결 문자열의 강조 표시된 내용을 마우스 오른쪽 단추로 클릭하고 **복사**를 클릭합니다.
7. 이제 이 값을 코드 파일에 붙여넣어 다음 형식의 연결 문자열을 만들 수 있습니다. *your_server*(두 곳에서)를 이전 단계에서 복사한 텍스트로 바꾸고 *your_password*를 SQL 데이터베이스 계정을 만들 때 지정한 암호 값으로 바꿉니다. (**gettingstarted** 및 **MySQLAdmin**을 사용하지 않는 경우 **database=** 및 **user=**에 할당된 값을 각각 바꿉니다.) 

    String connectionString = "jdbc:sqlserver://*your_server*.database.windows.net:1433" + ";" + "database=gettingstarted" + ";" + "user=MySQLAdmin@*your_server*" + ";" + "password=*your_password*" + ";" + "encrypt=true" + ";" + "hostNameInCertificate=*.int.mscds.com" + ";" + "loginTimeout=30";

이 가이드의 뒷부분에서 이 문자열을 실제로 사용할 것입니다. 지금은 연결 문자열을 확인하는 단계만 파악합니다. 또한 응용 프로그램 요구 사항에 따라 **encrypt** 및 **hostNameInCertificate** 설정을 사용할 필요가 없고 **loginTimeout** 설정을 수정할 필요가 없을 수 있습니다.

## IP 주소 범위에 액세스 허용

1. [관리 포털](https://manage.windowsazure.com)에 로그인합니다.
2. **SQL 데이터베이스**를 클릭합니다.
3. **서버**를 클릭합니다.
4. 사용할 서버를 클릭합니다.
5. **관리**를 클릭합니다.
6. **구성**을 클릭합니다.
7. **허용된 IP 주소**에서 새 IP 규칙의 이름을 입력합니다. IP 주소 범위의 시작과 끝을 지정합니다. 편의를 위해, 현재 클라이언트 IP 주소가 표시됩니다. 다음 예제에서는 단일 클라이언트 IP 주소가 허용됩니다(사용자의 IP 주소는 다름).

    ![허용된 IP 주소 대화 상자][allowed_ips_dialog]

8. 완료 단추를 클릭합니다. 지정한 IP 주소가 이제 데이터베이스 서버에 액세스하도록 허용됩니다.

## Java에서 Azure SQL 데이터베이스 사용

1. Java 프로젝트를 만듭니다. 이 자습서에서는 **HelloSQLAzure**라고 부릅니다.
2. **HelloSQLAzure.java**라는 Java 클래스 파일을 프로젝트에 추가합니다.
3. 빌드 경로에 **Microsoft JDBC Driver for SQL Server**를 추가합니다.

   Eclipse를 사용하는 경우:

    1. Within Eclipse's Project Explorer, right-click the **HelloSQLAzure** project and click **Properties**.
    2. In the left-hand pane of the **Properties** dialog, click **Java Build Path**.
    3. Click the **Libraries** tab, and then click **Add Library**.
    4. In the **Add Library** dialog, select **Microsoft JDBC Driver 4.0 for SQL Server**, click **Next**, and then click **Finish**.
    5. Click **OK** to close the **Properties** dialog.

    If you are not using Eclipse, add the Microsoft JDBC Driver 4.0 for SQL Server JAR to your class path. For related information, see [Using the JDBC Driver](http://msdn.microsoft.com/library/ms378526.aspx).

4. **HelloSQLAzure.java** 코드 내에서 다음과 같이 `import` 문을 추가합니다.

        import java.sql.*;
        import com.microsoft.sqlserver.jdbc.*;

5. 연결 문자열을 지정합니다. 다음은 예제입니다. 위처럼 *your_server*(두 곳에서), *your_user* 및 *your_password*를 SQL 데이터베이스 서버에 대해 적절한 값으로 바꿉니다.

        String connectionString =
        	"jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +  
        		"database=master" + ";" + 
        		"user=your_user@your_server" + ";" +  
        		"password=your_password";

이제 SQL 데이터베이스 서버와 통신할 코드를 추가할 준비가 되었습니다.

## 코드에서 Azure SQL 데이터베이스와 통신

이 항목의 나머지는 다음을 수행하는 예제입니다.

1. SQL 데이터베이스 서버에 연결합니다.
2. SQL 문을 정의합니다(예: 테이블 생성 또는 삭제, 행 삽입/선택/삭제 등).
3. **executeUpdate** 또는 **executeQuery**에 대한 호출을 통해 SQL 문을 실행합니다.
4. 해당하는 경우 쿼리 결과를 표시합니다.

다음 섹션은 순서대로 읽도록(샘플링하도록) 되어 있습니다. 첫 번째 코드 조각은 전체 샘플이며, 나머지 코드 조각은 **import** 문, **class** 및 **main** 선언, 오류 처리, 리소스 닫기처럼 전체 샘플에서 프레임워크 부분을 사용합니다.

## 테이블 만들기

다음 코드는 **Person**이라는 테이블을 만드는 방법을 보여 줍니다.

	import java.sql.*;
	import com.microsoft.sqlserver.jdbc.*;
	
	public class HelloSQLAzure {
	
	    public static void main(String[] args) 
	    {
	
			// Connection string for your SQL Database server.
			// Change the values assigned to your_server, 
			// your_user@your_server,
			// and your_password.
			String connectionString = 
				"jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +  
					"database=gettingstarted" + ";" + 
					"user=your_user@your_server" + ";" +  
					"password=your_password";
			
			// The types for the following variables are
			// defined in the java.sql library.
			Connection connection = null;  // For making the connection
			Statement statement = null;    // For the SQL statement
			ResultSet resultSet = null;    // For the result set, if applicable
			
			try
			{
			    // Ensure the SQL Server driver class is available.
			    Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");
			
			    // Establish the connection.
			    connection = DriverManager.getConnection(connectionString);
			
			    // Define the SQL string.
			    String sqlString = 
					"CREATE TABLE Person (" + 
			        	"[PersonID] [int] IDENTITY(1,1) NOT NULL," +
			            "[LastName] [nvarchar](50) NOT NULL," + 
			            "[FirstName] [nvarchar](50) NOT NULL)";
			
			    // Use the connection to create the SQL statement.
			    statement = connection.createStatement();
			
			    // Execute the statement.
			    statement.executeUpdate(sqlString);
			
			    // Provide a message when processing is complete.
			    System.out.println("Processing complete.");
			
			}
			// Exception handling
	        catch (ClassNotFoundException cnfe)  
	        {
	            
	            System.out.println("ClassNotFoundException " +
	                               cnfe.getMessage());
	        }
	        catch (Exception e)
	        {
	            System.out.println("Exception " + e.getMessage());
	            e.printStackTrace();
	        }
	        finally
	        {
	            try
	            {
	                // Close resources.
	                if (null != connection) connection.close();
	                if (null != statement) statement.close();
	                if (null != resultSet) resultSet.close();
	            }
	            catch (SQLException sqlException)
	            {
	                // No additional action if close() statements fail.
	            }
	        }
	        
	    }
	
	}
	

## 테이블에 인덱스 만들기

다음 코드는 **PersonID** 열을 사용하여 **Person** 테이블에 **index1**이라는 인덱스를 만드는 방법을 보여 줍니다.

	// Connection string for your SQL Database server.
	// Change the values assigned to your_server, 
	// your_user@your_server,
	// and your_password.
	String connectionString = 
		"jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +  
			"database=gettingstarted" + ";" + 
			"user=your_user@your_server" + ";" +  
			"password=your_password";
	
	// The types for the following variables are
	// defined in the java.sql library.
	Connection connection = null;  // For making the connection
	Statement statement = null;    // For the SQL statement
	ResultSet resultSet = null;    // For the result set, if applicable
	
	try
	{
	    // Ensure the SQL Server driver class is available.
	    Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");
	
	    // Establish the connection.
	    connection = DriverManager.getConnection(connectionString);
	
	    // Define the SQL string.
	    String sqlString = 
			"CREATE CLUSTERED INDEX index1 " + "ON Person (PersonID)";
	
	    // Use the connection to create the SQL statement.
	    statement = connection.createStatement();
	
	    // Execute the statement.
	    statement.executeUpdate(sqlString);
	
	    // Provide a message when processing is complete.
	    System.out.println("Processing complete.");
	
	}
	// Exception handling and resource closing not shown...



## 행 삽입

다음 코드는 **Person** 테이블을 행을 추가하는 방법을 보여 줍니다.

	// Connection string for your SQL Database server.
	// Change the values assigned to your_server, 
	// your_user@your_server,
	// and your_password.
	String connectionString = 
		"jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +  
			"database=gettingstarted" + ";" + 
			"user=your_user@your_server" + ";" +  
			"password=your_password";
	
	// The types for the following variables are
	// defined in the java.sql library.
	Connection connection = null;  // For making the connection
	Statement statement = null;    // For the SQL statement
	ResultSet resultSet = null;    // For the result set, if applicable
	
	try
	{
	    // Ensure the SQL Server driver class is available.
	    Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");
	
	    // Establish the connection.
	    connection = DriverManager.getConnection(connectionString);
	
	    // Define the SQL string.
	    String sqlString = 
			"SET IDENTITY_INSERT Person ON " + 
	        	"INSERT INTO Person " + 
	            "(PersonID, LastName, FirstName) " + 
	            "VALUES(1, 'Abercrombie', 'Kim')," + 
	            	  "(2, 'Goeschl', 'Gerhard')," + 
	                  "(3, 'Grachev', 'Nikolay')," + 
	                  "(4, 'Yee', 'Tai')," + 
	                  "(5, 'Wilson', 'Jim')";
	
	    // Use the connection to create the SQL statement.
	    statement = connection.createStatement();
	
	    // Execute the statement.
	    statement.executeUpdate(sqlString);
	
	    // Provide a message when processing is complete.
	    System.out.println("Processing complete.");
	
	}
	// Exception handling and resource closing not shown...

 
## 행 검색

다음 코드는 **Person** 테이블에서 행을 검색하는 방법을 보여 줍니다.

	// Connection string for your SQL Database server.
	// Change the values assigned to your_server, 
	// your_user@your_server,
	// and your_password.
	String connectionString = 
		"jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +  
			"database=gettingstarted" + ";" + 
			"user=your_user@your_server" + ";" +  
			"password=your_password";
	
	// The types for the following variables are
	// defined in the java.sql library.
	Connection connection = null;  // For making the connection
	Statement statement = null;    // For the SQL statement
	ResultSet resultSet = null;    // For the result set, if applicable
	
	try
	{
	    // Ensure the SQL Server driver class is available.
	    Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");
	
	    // Establish the connection.
	    connection = DriverManager.getConnection(connectionString);
	
	    // Define the SQL string.
	    String sqlString = "SELECT TOP 10 * FROM Person";
	
	    // Use the connection to create the SQL statement.
	    statement = connection.createStatement();
	
	    // Execute the statement.
	    resultSet = statement.executeQuery(sqlString);
	
	    // Loop through the results
	    while (resultSet.next())
	    {
	        // Print out the row data
	        System.out.println(
	        	"Person with ID " + 
	        	resultSet.getString("PersonID") + 
	        	" has name " +
	        	resultSet.getString("FirstName") + " " +
	       		resultSet.getString("LastName"));
	        }
	
	    // Provide a message when processing is complete.
	    System.out.println("Processing complete.");
	
	}
	// Exception handling and resource closing not shown...

 위 코드는 **Person** 테이블에서 맨 위 10개의 행을 선택했습니다. 모든 행을 반환하려면 SQL 문을 다음과 같이 수정합니다.

	String sqlString = "SELECT * FROM Person";

 
## WHERE 절을 사용하여 행 검색

절을 사용하여 행을 검색하려면 위에 표시된 코드를 사용합니다. 단, SQL 문을 변경하여 절을 포함하세요. 다음 SQL 문은 **FirstName** 값이 **Jim**인 행의 절을 포함합니다.

	// Define the SQL string.
	String sqlString = "SELECT * FROM Person WHERE FirstName='Jim'";
	
WHERE 절은 개수를 검색하거나, 행을 업데이트하거나, 행을 삭제할 때에도 사용할 수 있습니다.

## 행 개수 검색

다음 코드는 **Person** 테이블에서 행의 개수를 검색하는 방법을 보여 줍니다.
 
	// Connection string for your SQL Database server.
	// Change the values assigned to your_server, 
	// your_user@your_server,
	// and your_password.
	String connectionString = 
		"jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +  
			"database=gettingstarted" + ";" + 
			"user=your_user@your_server" + ";" +  
			"password=your_password";
	
	// The types for the following variables are
	// defined in the java.sql library.
	Connection connection = null;  // For making the connection
	Statement statement = null;    // For the SQL statement
	ResultSet resultSet = null;    // For the result set, if applicable
	
	try
	{
	    // Ensure the SQL Server driver class is available.
	    Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");
	
	    // Establish the connection.
	    connection = DriverManager.getConnection(connectionString);
	
	// Define the SQL string.
	    String sqlString = "SELECT COUNT (PersonID) FROM Person";
	
	    // Use the connection to create the SQL statement.
	    statement = connection.createStatement();
	
	    // Execute the statement.
	    resultSet = statement.executeQuery(sqlString);
	
	    // Print out the returned number of rows.
	    while (resultSet.next())
	    {
	        System.out.println("There were " + 
	                         resultSet.getInt(1) +
	                         " rows returned.");
	    }
	
	    // Provide a message when processing is complete.
	    System.out.println("Processing complete.");
	
	}
	// Exception handling and resource closing not shown...

## 행 업데이트

다음 코드는 행을 업데이트하는 방법을 보여 줍니다. 이 예제에서는 **FirstName** 값이 **Jim**인 모든 행에 대해 **LastName** 값이 **Kim**으로 변경됩니다.

	// Connection string for your SQL Database server.
	// Change the values assigned to your_server, 
	// your_user@your_server,
	// and your_password.
	String connectionString = 
		"jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +  
			"database=gettingstarted" + ";" + 
			"user=your_user@your_server" + ";" +  
			"password=your_password";
	
	// The types for the following variables are
	// defined in the java.sql library.
	Connection connection = null;  // For making the connection
	Statement statement = null;    // For the SQL statement
	ResultSet resultSet = null;    // For the result set, if applicable
	
	try
	{
	    // Ensure the SQL Server driver class is available.
	    Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");
	
	    // Establish the connection.
	    connection = DriverManager.getConnection(connectionString);
	
	    // Define the SQL string.
	    String sqlString = 
			"UPDATE Person " + "SET LastName = 'Kim' " + "WHERE FirstName='Jim'";
	
	    // Use the connection to create the SQL statement.
	    statement = connection.createStatement();
	    
	    // Execute the statement.
	    statement.executeUpdate(sqlString);
	
	    // Provide a message when processing is complete.
	    System.out.println("Processing complete.");
	
	}// Exception handling and resource closing not shown...

 

## 행 삭제

다음 코드는 행을 삭제하는 방법을 보여 줍니다. 이 예제에서는 **FirstName** 값이 **Jim**인 행이 삭제됩니다.

	// Connection string for your SQL Database server.
	// Change the values assigned to your_server, 
	// your_user@your_server,
	// and your_password.
	String connectionString = 
		"jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +  
			"database=gettingstarted" + ";" + 
			"user=your_user@your_server" + ";" +  
			"password=your_password";
	
	// The types for the following variables are
	// defined in the java.sql library.
	Connection connection = null;  // For making the connection
	Statement statement = null;    // For the SQL statement
	ResultSet resultSet = null;    // For the result set, if applicable
	
	try
	{
	    // Ensure the SQL Server driver class is available.
	    Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");
	
	    // Establish the connection.
	    connection = DriverManager.getConnection(connectionString);
	
	    // Define the SQL string.
	    String sqlString = 
			"DELETE from Person " + 
				"WHERE FirstName='Jim'";
	
	    // Use the connection to create the SQL statement.
	    statement = connection.createStatement();
	
	    // Execute the statement.
	    statement.executeUpdate(sqlString);
	
	    // Provide a message when processing is complete.
	    System.out.println("Processing complete.");
	
	}
	// Exception handling and resource closing not shown...
	
 
## 테이블 존재 여부 확인

다음 코드는 테이블 존재 여부를 확인하는 방법을 보여 줍니다.

	// Connection string for your SQL Database server.
	// Change the values assigned to your_server, 
	// your_user@your_server,
	// and your_password.
	String connectionString = 
		"jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +  
			"database=gettingstarted" + ";" + 
			"user=your_user@your_server" + ";" +  
			"password=your_password";
	
	// The types for the following variables are
	// defined in the java.sql library.
	Connection connection = null;  // For making the connection
	Statement statement = null;    // For the SQL statement
	ResultSet resultSet = null;    // For the result set, if applicable
	
	try
	{
	    // Ensure the SQL Server driver class is available.
	    Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");
	
	    // Establish the connection.
	    connection = DriverManager.getConnection(connectionString);
	
	    // Define the SQL string.
	    String sqlString = 
			"IF EXISTS (SELECT 1 " +
	        	"FROM sysobjects " + 
	            "WHERE xtype='u' AND name='Person') " +
	            "SELECT 'Person table exists.'" +
	            "ELSE  " +
	            "SELECT 'Person table does not exist.'";
	
	    // Use the connection to create the SQL statement.
	    statement = connection.createStatement();
	
	    // Execute the statement.
	    resultSet = statement.executeQuery(sqlString);
	
	    // Display the result.
	    while (resultSet.next())
	    {
	        System.out.println(resultSet.getString(1));
	    }
	
	    // Provide a message when processing is complete.
	    System.out.println("Processing complete.");
	
	}
	// Exception handling and resource closing not shown...

## 인덱스 삭제

다음 코드는 **Person** 테이블에서 **index1**이라는 인덱스를 삭제하는 방법을 보여 줍니다.

	// Connection string for your SQL Database server.
	// Change the values assigned to your_server, 
	// your_user@your_server,
	// and your_password.
	String connectionString = 
		"jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +
			"database=gettingstarted" + ";" +
			"user=your_user@your_server" + ";" +
			"password=your_password";
	
	// The types for the following variables are
	// defined in the java.sql library.
	Connection connection = null;  // For making the connection
	Statement statement = null;    // For the SQL statement
	ResultSet resultSet = null;    // For the result set, if applicable
	
	try
	{
	    // Ensure the SQL Server driver class is available.
	    Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");
	
	    // Establish the connection.
	    connection = DriverManager.getConnection(connectionString);
	
	    // Define the SQL string.
	    String sqlString = 
			"DROP INDEX index1 " + 
	        	"ON Person";
	
	    // Use the connection to create the SQL statement.
	    statement = connection.createStatement();
	
	    // Execute the statement.
	    statement.executeUpdate(sqlString);
	
	    // Provide a message when processing is complete.
	    System.out.println("Processing complete.");
	
	}
	// Exception handling and resource closing not shown...

 
## 테이블 삭제

다음 코드는 **Person**이라는 테이블을 삭제하는 방법을 보여 줍니다.

	// Connection string for your SQL Database server.
	// Change the values assigned to your_server, 
	// your_user@your_server,
	// and your_password.
	String connectionString = 
		"jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +  
			"database=gettingstarted" + ";" + 
			"user=your_user@your_server" + ";" +  
			"password=your_password";
	
	// The types for the following variables are
	// defined in the java.sql library.
	Connection connection = null;  // For making the connection
	Statement statement = null;    // For the SQL statement
	ResultSet resultSet = null;    // For the result set, if applicable
	
	try
	{
	    // Ensure the SQL Server driver class is available.
	    Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");
	
	    // Establish the connection.
	    connection = DriverManager.getConnection(connectionString);
	
	    // Define the SQL string.
	    String sqlString = "DROP TABLE Person";
	
	    // Use the connection to create the SQL statement.
	    statement = connection.createStatement();
	
	    // Execute the statement.
	    statement.executeUpdate(sqlString);
	
	    // Provide a message when processing is complete.
	    System.out.println("Processing complete.");
	
	}
	// Exception handling and resource closing not shown...

## Azure 배포 내 Java에서 SQL 데이터베이스 사용

Azure 배포 내 Java에서 SQL 데이터베이스를 사용하려면, 위에 표시된 것처럼 클래스 경로에 Microsoft JDBC Driver 4.0 for SQL Server를 라이브러리로 추가하는 것과 함께, 배포에서 패키지로 만들어야 합니다.


**Eclipse를 사용하는 경우 Microsoft JDBC Driver 4.0 SQL Server 패키지 만들기**

1. Eclipse의 Project Explorer에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **Properties**를 클릭합니다.
2. **Properties** 대화 상자의 왼쪽 창에서 **Deployment Assembly**, **Add**를 차례로 클릭합니다.
3. **New Assembly Directive** 대화 상자에서 **Java Build Path Entries**를 클릭한 후 **Next**를 클릭합니다.
4. **Microsoft JDBC Driver 4.0 SQL Server**를 선택한 다음 **Finish**를 클릭합니다.
5. **OK**를 클릭하여 **Properties** 대화 상자를 닫습니다.
6. 프로젝트의 WAR 파일을 approot 폴더로 내보내고 [Eclipse용 Azure 플러그 인(Java 포함)(Microsoft Open Technologies 제공)을 사용하여 Hello World 응용 프로그램 만들기](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx)(영문)에 설명된 단계에 따라 Azure 프로젝트를 다시 빌드합니다. 이 항목에는 계산 에뮬레이터와 Azure에서 응용 프로그램을 실행하는 방법도 설명되어 있습니다.

**Eclipse를 사용하지 않는 경우 Microsoft JDBC Driver 4.0 SQL Server 패키지 만들기**

* Microsoft JDBC Driver 4.0 SQL Server 라이브러리가 동일한 Azure 역할 내에 Java 응용 프로그램으로 포함되어 있으며 응용 프로그램의 클래스 경로에 추가되어 있는지 확인합니다.

## 다음 단계

Microsoft JDBC Driver for SQL Server에 대한 자세한 내용은 [JDBC 드라이버 개요](http://msdn.microsoft.com/library/ms378749.aspx)(영문)를 참조하십시오. SQL 데이터베이스에 대한 자세한 내용은 [SQL 데이터베이스 개요](http://msdn.microsoft.com/library/windowsazure/ee336241.aspx)(영문)를 참조하십시오.

[Concepts]: #concepts
[Prerequisites]: #prerequisites
[Creating an Azure SQL Database]: #create_db
[Determining the SQL Database connection string]: #determine_connection_string
[To allow access to a range of IP addresses]: #specify_allowed_ips
[To use Azure SQL Database in Java]: #use_sql_azure_in_java
[Communicating with Azure SQL Database from your code]: #communicate_from_code
[To create a table]: #to_create_table
[To create an index on a table]: #to_create_index
[To insert rows]: #to_insert_rows
[To retrieve rows]: #to_retrieve_rows
[To retrieve rows using a WHERE clause]: #to_retrieve_rows_using_where
[To retrieve a count of rows]: #to_retrieve_row_count
[To update rows]: #to_update_rows
[To delete rows]: #to_delete_rows
[To check whether a table exists]: #to_check_table_existence
[To drop an index]: #to_drop_index
[To drop a table]: #to_drop_table
[Using SQL Database in Java within an Azure Deployment]: #using_in_azure
[다음 단계]: #nextsteps
[create_new]: ./media/sql-data-java-how-to-use-sql-database/WA_New.png
[create_new_sql_db]: ./media/sql-data-java-how-to-use-sql-database/WA_SQL_DB_Create.png
[create_database_settings]: ./media/sql-data-java-how-to-use-sql-database/WA_CustomCreate_1.png
[create_server_settings]: ./media/sql-data-java-how-to-use-sql-database/WA_CustomCreate_2.png
[get_jdbc_connection_string]: ./media/sql-data-java-how-to-use-sql-database/WA_SQL_JDBC_ConnectionString.png
[allowed_ips_dialog]: ./media/sql-data-java-how-to-use-sql-database/WA_Allowed_IPs.png
 

<!---HONumber=July15_HO3-->