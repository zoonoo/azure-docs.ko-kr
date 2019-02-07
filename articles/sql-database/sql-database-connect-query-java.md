---
title: Java를 사용하여 Azure SQL Database 쿼리 | Microsoft Docs
description: Java를 사용하여 Azure SQL Database에 연결하고 T-SQL 문을 사용하여 쿼리하는 프로그램을 만드는 방법을 보여 줍니다.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: java
ms.topic: quickstart
author: ajlam
ms.author: andrela
ms.reviewer: v-masebo
manager: craigg
ms.date: 12/01/2018
ms.openlocfilehash: 3a036ac1260923a5030b8b0c3345482346c183fe
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2019
ms.locfileid: "55563129"
---
# <a name="quickstart-use-java-to-query-an-azure-sql-database"></a>빠른 시작: Java를 사용하여 Azure SQL Database 쿼리

이 문서에서는 [Java](/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server)를 사용하여 Azure SQL에 연결하는 방법을 보여 줍니다. 그런 다음, 데이터를 쿼리하는 T-SQL 문을 사용할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

이 샘플을 완료하려면 다음 필수 구성 요소가 있어야 합니다.

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

- 운영 체제에 맞게 설치된 Java 관련 소프트웨어:

  - **MacOS**: Homebrew와 Java를 설치한 다음, Maven을 설치합니다. [1.2 및 1.3 단계](https://www.microsoft.com/sql-server/developer-get-started/java/mac/) 참조

  - **Ubuntu**: Java Development Kit을 설치하고 Maven을 설치합니다. [1.2, 1.3 및 1.4 단계](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu/) 참조

  - **Windows**: Java를 설치한 다음, Maven을 설치합니다. [1.2 및 1.3 단계](https://www.microsoft.com/sql-server/developer-get-started/java/windows/) 참조

## <a name="get-database-connection"></a>데이터베이스 연결 설정

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

## <a name="create-the-project"></a>프로젝트 만들기

1. 명령 프롬프트에서 *sqltest*라는 새 Maven 프로젝트를 만듭니다.

    ```bash
    mvn archetype:generate "-DgroupId=com.sqldbsamples" "-DartifactId=sqltest" "-DarchetypeArtifactId=maven-archetype-quickstart" "-Dversion=1.0.0" --batch-mode
    ```

1. 폴더를 *sqltest*로 변경하고, 원하는 텍스트 편집기에서 *pom.xml*을 엽니다. 다음 코드를 사용하여 프로젝트의 종속성에 **SQL Server용 Microsoft JDBC Driver**를 추가합니다.

    ```xml
    <dependency>
        <groupId>com.microsoft.sqlserver</groupId>
        <artifactId>mssql-jdbc</artifactId>
        <version>7.0.0.jre8</version>
    </dependency>
    ```

1. 또한 *pom.xml*에서 프로젝트에 다음 속성을 추가합니다. 속성 섹션이 없으면 종속성 뒤에 추가할 수 있습니다.

   ```xml
   <properties>
       <maven.compiler.source>1.8</maven.compiler.source>
       <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   ```

1. *pom.xml*을 저장하고 닫습니다.

## <a name="add-code-to-query-database"></a>코드를 추가하여 데이터베이스 쿼리

1. 다음 위치에 있는 Maven 프로젝트에 이미 *App.java*라는 파일이 있어야 합니다.

   *..\sqltest\src\main\java\com\sqldbsamples\App.java*

1. 파일을 열고 콘텐츠를 다음 코드로 바꿉니다. 그런 다음, 서버, 데이터베이스, 사용자 및 암호에 적절한 값을 추가합니다.

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
            String hostName = "your_server.database.windows.net"; // update me
            String dbName = "your_database"; // update me
            String user = "your_username"; // update me
            String password = "your_password"; // update me
            String url = String.format("jdbc:sqlserver://%s:1433;database=%s;user=%s;password=%s;encrypt=true;"
                + "hostNameInCertificate=*.database.windows.net;loginTimeout=30;", hostName, dbName, user, password);
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

   > [!NOTE]
   > 이 코드 예제는 Azure SQL의 **AdventureWorksLT** 샘플 데이터베이스를 사용합니다.

## <a name="run-the-code"></a>코드 실행

1. 명령 프롬프트에서 앱을 실행합니다.

    ```bash
    mvn package -DskipTests
    mvn -q exec:java "-Dexec.mainClass=com.sqldbsamples.App"
    ```

1. 상위 20개 행이 반환되는지 확인하고, 앱 창을 닫습니다.

## <a name="next-steps"></a>다음 단계

- [첫 번째 Azure SQL Database 디자인](sql-database-design-first-database.md)  

- [SQL Server용 Microsoft JDBC Driver](https://github.com/microsoft/mssql-jdbc)  

- [문제/질문 보고](https://github.com/microsoft/mssql-jdbc/issues)  
