---
title: "Java를 사용하여 Azure SQL Database 쿼리 | Microsoft Docs"
description: "이 항목에서는 Java를 사용하여 Azure SQL Database에 연결하고 Transact-SQL 문을 사용하여 쿼리하는 프로그램을 만드는 방법을 보여 줍니다."
services: sql-database
documentationcenter: 
author: ajlam
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: mvc,develop apps
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: quickstart
ms.date: 07/10/2017
ms.author: andrela
ms.openlocfilehash: 943e355ffcc6c0670585936ec2e559b7fb592ff5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="use-java-to-query-an-azure-sql-database"></a>Java를 사용하여 Azure SQL Database 쿼리

이 빠른 시작에서는 [Java](https://docs.microsoft.com/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server)를 사용하여 Azure SQL Database에 연결하고 Transact-SQL 문을 사용하여 데이터를 쿼리하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 조건

이 빠른 시작 자습서를 완료하려면 다음 필수 구성 요소가 있어야 합니다.

- Azure SQL 데이터베이스입니다. 이 빠른 시작에서는 다음과 같은 빠른 시작 중 하나에서 만든 리소스를 사용합니다. 

   - [DB 만들기 - 포털](sql-database-get-started-portal.md)
   - [DB 만들기 - CLI](sql-database-get-started-cli.md)
   - [DB 만들기 - PowerShell](sql-database-get-started-powershell.md)

- 이 빠른 시작 자습서에서 사용하는 컴퓨터의 공용 IP 주소에 대한 [서버 수준 방화벽 규칙](sql-database-get-started-portal.md#create-a-server-level-firewall-rule)

- 운영 체제에 맞게 설치된 Java 및 관련 소프트웨어

    - **MacOS**: Homebrew와 Java를 설치한 다음 Maven을 설치합니다. [1.2 및 1.3 단계](https://www.microsoft.com/sql-server/developer-get-started/java/mac/) 참조
    - **Ubuntu**: Java Development Kit을 설치하고 Maven을 설치합니다. [1.2, 1.3 및 1.4 단계](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu/) 참조
    - **Windows**: Java Development Kit을 설치하고 Maven을 설치합니다. [1.2 및 1.3 단계](https://www.microsoft.com/sql-server/developer-get-started/java/windows/) 참조    

## <a name="sql-server-connection-information"></a>SQL 서버 연결 정보

Azure SQL Database에 연결하는 데 필요한 연결 정보를 가져옵니다. 다음 절차에는 정규화된 서버 이름, 데이터베이스 이름 및 로그인 정보가 필요합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. 왼쪽 메뉴에서 **SQL Database**를 선택하고 **SQL Database** 페이지에서 데이터베이스를 클릭합니다. 
3. 데이터베이스의 **개요** 페이지에서 다음 이미지와 같이 정규화된 서버 이름을 검토합니다. 해당 서버 이름을 마우스로 가리키면 **복사하려면 클릭** 옵션이 표시됩니다.  

   ![서버 이름](./media/sql-database-connect-query-dotnet/server-name.png) 

4. 서버 로그인 정보를 잊어버린 경우 SQL Database 서버 페이지로 이동하여 서버 관리자 로그인 이름을 확인합니다.  필요한 경우 암호를 다시 설정합니다.     

## <a name="create-maven-project-and-dependencies"></a>**Maven 프로젝트 및 종속성 만들기**
1. 터미널에서 **sqltest**라는 새 Maven 프로젝트를 만듭니다. 

   ```bash
   mvn archetype:generate "-DgroupId=com.sqldbsamples" "-DartifactId=sqltest" "-DarchetypeArtifactId=maven-archetype-quickstart" "-Dversion=1.0.0"
   ```

2. 메시지가 표시되면 **Y**를 입력합니다.
3. 디렉터리를 **sqltest**로 변경하고 원하는 텍스트 편집기에서 ***pom.xml***을 엽니다.  다음 코드를 사용하여 프로젝트의 종속성에 **SQL Server용 Microsoft JDBC Driver**를 추가합니다.

   ```xml
   <dependency>
       <groupId>com.microsoft.sqlserver</groupId>
       <artifactId>mssql-jdbc</artifactId>
       <version>6.2.1.jre8</version>
   </dependency>
   ```

4. 또한 ***pom.xml***에서 프로젝트에 다음 속성을 추가합니다.  속성 섹션이 없으면 종속성 뒤에 추가할 수 있습니다.

   ```xml
   <properties>
       <maven.compiler.source>1.8</maven.compiler.source>
       <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   ```

5. ***pom.xml***을 저장하고 닫습니다.

## <a name="insert-code-to-query-sql-database"></a>SQL 데이터베이스 쿼리 코드 삽입

1. \sqltest\src\main\java\com\sqlsamples\App.java에 있는 Maven 프로젝트에 이미 ***App.java***라는 파일이 있어야 합니다.

2. 파일을 열어 내용을 다음 코드로 바꾸고, 서버, 데이터베이스, 사용자 및 암호에 대해 적절한 값을 추가합니다.

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
           String hostName = "your_server.database.windows.net";
           String dbName = "your_database";
           String user = "your_username";
           String password = "your_password";
           String url = String.format("jdbc:sqlserver://%s:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", hostName, dbName, user, password);
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
                
                   try (Statement statement = connection.createStatement();
                       ResultSet resultSet = statement.executeQuery(selectSql)) {

                           // Print results from select statement
                           System.out.println("Top 20 categories:");
                           while (resultSet.next())
                           {
                               System.out.println(resultSet.getString(1) + " "
                                   + resultSet.getString(2));
                           }
                    connection.close();
                   }                   
           }
           catch (Exception e) {
                   e.printStackTrace();
           }
       }
   }
   ```

## <a name="run-the-code"></a>코드 실행

1. 명령 프롬프트에서 다음 명령을 실행합니다.

   ```bash
   mvn package
   mvn -q exec:java "-Dexec.mainClass=com.sqldbsamples.App"
   ```

2. 상위 20개 행이 반환되는지 확인한 다음 응용 프로그램 창을 닫습니다.


## <a name="next-steps"></a>다음 단계
- [첫 번째 Azure SQL Database 디자인](sql-database-design-first-database.md)
- [SQL Server용 Microsoft JDBC Driver](https://github.com/microsoft/mssql-jdbc)
- [문제/질문 보고](https://github.com/microsoft/mssql-jdbc/issues)

