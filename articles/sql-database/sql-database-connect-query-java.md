---
title: "Java를 사용하여 Azure SQL Database에 연결 | Microsoft Docs"
description: "Azure SQL 데이터베이스에 연결하는 데 사용할 수 있는 Java 코드 샘플을 제시합니다."
services: sql-database
documentationcenter: 
author: ajlam
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: quick start
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 03/27/2017
ms.author: andrela;carlrab;sstein
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: a047d4cdf869fff0d2afaf11f124370c0eae98e4
ms.lasthandoff: 03/30/2017


---
# <a name="azure-sql-database-use-java-to-connect-and-query-data"></a>Azure SQL Database: Java를 사용하여 데이터에 연결 및 쿼리

[Java](https://docs.microsoft.com/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server)를 사용하여 Azure SQL Database에 연결하고 쿼리합니다. 이 가이드는 Java를 사용하여 Azure SQL Database에 연결하고 쿼리, 삽입, 업데이트 및 삭제 문을 실행하는 방법을 자세히 설명합니다.

이 빠른 시작은 다음과 같은 빠른 시작 중 하나에서 만들어진 리소스를 시작 지점으로 사용합니다.

- [DB 만들기 - 포털](sql-database-get-started-portal.md)
- [DB 만들기 - CLI](sql-database-get-started-cli.md)

## <a name="configure-development-environment"></a>개발 환경 구성

다음 섹션에서는 Azure SQL Database 작업을 수행하기 위해 기존 Mac OS, Linux(Ubuntu) 및 Windows 개발 환경을 구성하는 방법을 자세히 설명합니다.

### <a name="mac-os"></a>**Mac OS**
터미널을 열고 Java 프로젝트를 만들려는 디렉터리로 이동합니다. 다음 명령을 입력하여 **brew** 및 **Maven**을 설치합니다. 

```
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew update
brew install maven
```

### <a name="linux-ubuntu"></a>**Linux(Ubuntu)**
터미널을 열고 Java 프로젝트를 만들려는 디렉터리로 이동합니다. 다음 명령을 입력하여 **Maven**을 설치합니다. 

```
sudo apt-get install maven
```

### <a name="windows"></a>**Windows**
공식 설치 관리자를 사용하여 [Maven](https://maven.apache.org/download.cgi)을 설치합니다.  

## <a name="get-connection-information"></a>연결 정보 가져오기

Azure Portal에서 연결 문자열을 가져옵니다. 연결 문자열을 사용하여 Azure SQL Database에 연결합니다.

1. [Azure 포털](https://portal.azure.com/)에 로그인합니다.
2. 왼쪽 메뉴에서 **SQL Database**를 선택하고 **SQL Database** 페이지에서 데이터베이스를 클릭합니다. 
3. 데이터베이스의 **Essentials** 창에서 정규화된 서버 이름을 검토합니다. 

    <img src="./media/sql-database-connect-query-dotnet/server-name.png" alt="server name" style="width: 780px;" />

4. **연결 문자열 표시**를 클릭합니다.

5. 전체 **JDBC** 연결 문자열을 검토합니다.

    <img src="./media/sql-database-connect-query-jdbc/jdbc-connection-string.png" alt="JDBC connection string" style="width: 780px;" />

### <a name="create-maven-project"></a>**Maven 프로젝트 만들기**
터미널에서 새 Maven 프로젝트를 만듭니다. 
```
mvn archetype:generate "-DgroupId=com.sqldbsamples" "-DartifactId=SqlDbSample" "-DarchetypeArtifactId=maven-archetype-quickstart" "-Dversion=1.0.0"
```

***pom.xml***에서 종속성에 **Microsoft JDBC Driver for SQL Server**를 추가합니다. 

```xml
<dependency>
    <groupId>com.microsoft.sqlserver</groupId>
    <artifactId>mssql-jdbc</artifactId>
    <version>6.1.0.jre8</version>
</dependency>
```

## <a name="select-data"></a>데이터 선택

[SELECT](https://msdn.microsoft.com/library/ms189499.aspx) Transact-SQL 문과 [connection](https://docs.microsoft.com/sql/connect/jdbc/working-with-a-connection)을 사용하여 Java로 Azure SQL Database에서 데이터를 쿼리합니다.

```java
package com.sqldbsamples;

import java.sql.Connection;
import java.sql.Statement;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.DriverManager;

public class App {

    public static void main(String[] args) {
    
        // Connect to database
        String hostName = "yourserver";
        String dbName = "yourdatabase";
        String user = "yourusername";
        String password = "yourpassword";
        String url = String.format("jdbc:sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", hostName, dbName, user, password);
        Connection connection = null;

        try {
                connection = DriverManager.getConnection(url);
                String schema = connection.getSchema();
                System.out.println("Successful connection - Schema: " + schema);

                System.out.println("Query data example:");
                System.out.println("=========================================");

                // Create and execute a SELECT SQL statement.
                String selectSql = "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName " 
                    + "FROM [SalesLT].[ProductCategory] pc "  
                    + "JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid";
                Statement statement = connection.createStatement();
                ResultSet resultSet = statement.executeQuery(selectSql);

                // Print results from select statement
                System.out.println("Top 20 categories:");
                while (resultSet.next())
                {
                    System.out.println(resultSet.getString(1) + " "
                        + resultSet.getString(2));
                }
            }
        catch (Exception e) {
                e.printStackTrace();
        }
    }
}
```

## <a name="insert-data"></a>데이터 삽입

[INSERT](https://msdn.microsoft.com/library/ms174335.aspx) Transact-SQL 문과 [Prepared 문](https://docs.microsoft.com/sql/connect/jdbc/using-statements-with-sql)을 사용하여 Azure SQL Database에서 데이터를 삽입합니다.

```java
package com.sqldbsamples;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.DriverManager;

public class App {

    public static void main(String[] args) {
    
        // Connect to database
        String hostName = "yourserver";
        String dbName = "yourdatabase";
        String user = "yourusername";
        String password = "yourpassword";
        String url = String.format("jdbc:sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", hostName, dbName, user, password);
        Connection connection = null;

        try {
                connection = DriverManager.getConnection(url);
                String schema = connection.getSchema();
                System.out.println("Successful connection - Schema: " + schema);

                System.out.println("Insert data example:");
                System.out.println("=========================================");

                // Prepared statement to insert data
                String insertSql = "INSERT INTO SalesLT.Product (Name, ProductNumber, Color, )" 
                    + " StandardCost, ListPrice, SellStartDate) VALUES (?,?,?,?,?,?);";

                java.util.Date date = new java.util.Date();
                java.sql.Timestamp sqlTimeStamp = new java.sql.Timestamp(date.getTime());

                PreparedStatement prep = connection.prepareStatement(insertSql);
                prep.setString(1, "BrandNewProduct");
                prep.setInt(2, 200989);
                prep.setString(3, "Blue");
                prep.setDouble(4, 75);
                prep.setDouble(5, 80);
                prep.setTimestamp(6, sqlTimeStamp);

                int count = prep.executeUpdate();
                System.out.println("Inserted: " + count + " row(s)");
        }
        catch (Exception e) {
                e.printStackTrace();
        }
    }
}
```
## <a name="update-data"></a>데이터 업데이트

[UPDATE](https://msdn.microsoft.com/library/ms177523.aspx) Transact-SQL 문과 [Prepared 문](https://docs.microsoft.com/sql/connect/jdbc/using-statements-with-sql)을 사용하여 Azure SQL Database에서 데이터를 업데이트합니다.

```java
package com.sqldbsamples;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.DriverManager;

public class App {

    public static void main(String[] args) {
    
        // Connect to database
        String hostName = "yourserver";
        String dbName = "yourdatabase";
        String user = "yourusername";
        String password = "yourpassword";
        String url = String.format("jdbc:sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", hostName, dbName, user, password);
        Connection connection = null;

        try {
                connection = DriverManager.getConnection(url);
                String schema = connection.getSchema();
                System.out.println("Successful connection - Schema: " + schema);

                System.out.println("Update data example:");
                System.out.println("=========================================");

                // Prepared statement to update data
                String updateSql = "UPDATE SalesLT.Product SET ListPrice = ? WHERE Name = ?";

                PreparedStatement prep = connection.prepareStatement(updateSql);
                prep.setString(1, "500");
                prep.setString(2, "BrandNewProduct");

                int count = prep.executeUpdate();
                System.out.println("Updated: " + count + " row(s)")
        }
        catch (Exception e) {
                e.printStackTrace();
        }
    }
}
```



## <a name="delete-data"></a>데이터 삭제

[DELETE](https://msdn.microsoft.com/library/ms189835.aspx) Transact-SQL 문과 [Prepared 문](https://docs.microsoft.com/sql/connect/jdbc/using-statements-with-sql)을 사용하여 Azure SQL Database에서 데이터를 삭제합니다.

```java
package com.sqldbsamples;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.DriverManager;

public class App {

    public static void main(String[] args) {
    
        // Connect to database
        String hostName = "yourserver";
        String dbName = "yourdatabase";
        String user = "yourusername";
        String password = "yourpassword";
        String url = String.format("jdbc:sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", hostName, dbName, user, password);
        Connection connection = null;

        try {
                connection = DriverManager.getConnection(url);
                String schema = connection.getSchema();
                System.out.println("Successful connection - Schema: " + schema);

                System.out.println("Delete data example:");
                System.out.println("=========================================");

                // Prepared statement to delete data
                String deleteSql = "DELETE SalesLT.Product WHERE Name = ?";

                PreparedStatement prep = connection.prepareStatement(deleteSql);
                prep.setString(1, "BrandNewProduct");

                int count = prep.executeUpdate();
                System.out.println("Deleted: " + count + " row(s)");
            }        
        catch (Exception e) {
                e.printStackTrace();
        }
    }
}
```

## <a name="next-steps"></a>다음 단계
* [SQL Database 개발 개요](sql-database-develop-overview.md)를 검토합니다.
* [SQL Server용 Microsoft JDBC Driver](https://github.com/microsoft/mssql-jdbc)용 Github 리포지토리
* [문제/질문을 보관](https://github.com/microsoft/mssql-jdbc/issues)합니다.
* 모든 [SQL 데이터베이스의 기능](https://azure.microsoft.com/services/sql-database/)을 탐색합니다.


