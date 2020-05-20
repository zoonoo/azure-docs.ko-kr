---
title: Java를 사용하여 데이터베이스 쿼리
description: Java를 사용하여 Azure SQL 데이터베이스에 연결하고 T-SQL 문을 사용하여 쿼리하는 프로그램을 만드는 방법을 보여 줍니다.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: java
ms.topic: quickstart
author: ajlam
ms.author: andrela
ms.reviewer: v-masebo
ms.date: 03/25/2019
ms.custom: seo-java-july2019. seo-java-august2019
ms.openlocfilehash: 034f92ca3b7552373ae69148d09d58d3a5dd166a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2020
ms.locfileid: "76768651"
---
# <a name="quickstart-use-java-to-query-an-azure-sql-database"></a>빠른 시작: Java를 사용하여 Azure SQL 데이터베이스 쿼리

이 빠른 시작에서는 Java를 사용하여 Azure SQL 데이터베이스에 연결하고 T-SQL 문을 사용하여 데이터를 쿼리합니다.

## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Azure SQL 데이터베이스](sql-database-single-database-get-started.md)
- [Java](/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server) 관련 소프트웨어

  # <a name="macos"></a>[macOS](#tab/macos)

  Homebrew 및 Java를 설치한 다음, [macOS에서 SQL Server를 사용하여 Java 앱 만들기](https://www.microsoft.com/sql-server/developer-get-started/java/mac/)의 **1.2** 및 **1.3** 단계를 사용하여 Maven을 설치합니다.

  # <a name="ubuntu"></a>[Ubuntu](#tab/ubuntu)

  Java, Java Development Kit를 설치한 다음, [Ubuntu에서 SQL Server를 사용하여 Java 앱 만들기](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu/)의 **1.2**, **1.3** 및 **1.4** 단계를 사용하여 Maven을 설치합니다.

  # <a name="windows"></a>[Windows](#tab/windows)

  Java를 설치한 다음, [Windows에서 SQL Server를 사용하여 Java 앱 만들기](https://www.microsoft.com/sql-server/developer-get-started/java/windows/)의 **1.2** 및 **1.3** 단계를 사용하여 Maven을 설치합니다.

  ---

> [!IMPORTANT]
> 이 문서의 스크립트는 **Adventure Works** 데이터베이스를 사용하도록 작성되었습니다.

> [!NOTE]
> 선택적으로 Azure SQL 관리형 인스턴스를 사용하도록 선택할 수 있습니다.
>
> 만들고 구성하려면 [Azure Portal](sql-database-managed-instance-get-started.md), [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) 또는 [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44)를 사용한 다음, [온-사이트](sql-database-managed-instance-configure-p2s.md) 또는 [VM](sql-database-managed-instance-configure-vm.md) 연결을 설정합니다.
>
> 데이터를 로드하려면 [Adventure Works](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) 파일을 사용하여 [BACPAC로 복원](sql-database-import.md) 또는 [Wide World Importers 데이터베이스 복원](sql-database-managed-instance-get-started-restore.md)을 참조하세요.

## <a name="get-sql-server-connection-information"></a>SQL Server 연결 정보 가져오기

Azure SQL 데이터베이스에 연결하는 데 필요한 연결 정보를 가져옵니다. 다음 절차를 수행하려면 정규화된 서버 이름이나 호스트 이름, 데이터베이스 이름 및 로그인 정보가 필요합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

2. **SQL 데이터베이스**를 선택하거나 **SQL 관리형 인스턴스** 페이지를 엽니다.

3. **개요** 페이지에서 단일 데이터베이스에 대한 **서버 이름** 옆에 있는 정규화된 서버 이름 또는 관리되는 인스턴스에 대한 **호스트** 옆에 있는 정규화된 서버 이름을 검토합니다. 서버 이름이나 호스트 이름을 복사하려면 마우스로 해당 이름 위를 가리키고 **복사** 아이콘을 선택합니다. 

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

- [첫 번째 Azure SQL 데이터베이스 디자인](sql-database-design-first-database.md)  

- [SQL Server용 Microsoft JDBC Driver](https://github.com/microsoft/mssql-jdbc)  

- [문제/질문 보고](https://github.com/microsoft/mssql-jdbc/issues)  
