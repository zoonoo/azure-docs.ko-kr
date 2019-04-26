---
title: Java를 사용하여 Azure SQL Database 쿼리 | Microsoft Docs
description: Java를 사용하여 Azure SQL 데이터베이스에 연결하고 T-SQL 문을 사용하여 쿼리하는 프로그램을 만드는 방법을 보여 줍니다.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: java
ms.topic: quickstart
author: ajlam
ms.author: andrela
ms.reviewer: v-masebo
manager: craigg
ms.date: 03/25/2019
ms.openlocfilehash: 2d9ce34d52d08b4dd38caaadfab48b7a69870e9a
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58447898"
---
# <a name="quickstart-use-java-to-query-an-azure-sql-database"></a>빠른 시작: Java를 사용하여 Azure SQL 데이터베이스 쿼리

이 문서에서는 [Java](/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server)를 사용하여 Azure SQL 데이터베이스에 연결하는 방법을 보여 줍니다. 그런 다음, 데이터를 쿼리하는 T-SQL 문을 사용할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

이 샘플을 완료하려면 다음 필수 구성 요소가 있어야 합니다.

- Azure SQL 데이터베이스입니다. 다음 빠른 시작 중 하나를 사용하여 Azure SQL Database에서 데이터베이스를 만들고 구성할 수 있습니다.

  || 단일 데이터베이스 | Managed Instance |
  |:--- |:--- |:---|
  | 생성| [포털](sql-database-single-database-get-started.md) | [포털](sql-database-managed-instance-get-started.md) |
  || [CLI](scripts/sql-database-create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) |
  | 구성 | [서버 수준 IP 방화벽 규칙](sql-database-server-level-firewall-rule.md)| [VM에서 연결](sql-database-managed-instance-configure-vm.md)|
  |||[사이트에서 연결](sql-database-managed-instance-configure-p2s.md)
  |데이터 로드|Adventure Works(빠른 시작마다 로드됨)|[Wide World Importers 복원](sql-database-managed-instance-get-started-restore.md)
  |||[GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)의 [BACPAC](sql-database-import.md) 파일에서 Adventure Works 복원 또는 가져오기|
  |||

  > [!IMPORTANT]
  > 이 문서의 스크립트는 Adventure Works 데이터베이스를 사용하도록 작성되었습니다. 관리되는 인스턴스의 경우 Adventure Works 데이터베이스를 인스턴스 데이터베이스로 가져오거나 이 문서의 스크립트를 수정하여 Wide World Importors 데이터베이스를 사용해야 합니다.

- 운영 체제에 맞게 설치된 Java 관련 소프트웨어:

  - **MacOS**: Homebrew와 Java를 설치한 다음, Maven을 설치합니다. [1.2 및 1.3 단계](https://www.microsoft.com/sql-server/developer-get-started/java/mac/) 참조

  - **Ubuntu**: Java Development Kit을 설치하고 Maven을 설치합니다. [1.2, 1.3 및 1.4 단계](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu/) 참조

  - **Windows**: Java를 설치한 다음, Maven을 설치합니다. [1.2 및 1.3 단계](https://www.microsoft.com/sql-server/developer-get-started/java/windows/) 참조

## <a name="get-sql-server-connection-information"></a>SQL Server 연결 정보 가져오기

Azure SQL 데이터베이스에 연결하는 데 필요한 연결 정보를 가져옵니다. 다음 절차를 수행하려면 정규화된 서버 이름이나 호스트 이름, 데이터베이스 이름 및 로그인 정보가 필요합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

2. **SQL 데이터베이스** 또는 **SQL 관리되는 인스턴스** 페이지로 이동합니다.

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

- [첫 번째 Azure SQL Database 디자인](sql-database-design-first-database.md)  

- [SQL Server용 Microsoft JDBC Driver](https://github.com/microsoft/mssql-jdbc)  

- [문제/질문 보고](https://github.com/microsoft/mssql-jdbc/issues)  
