---
title: Java를 사용하여 데이터베이스 쿼리
description: Java를 사용하여 Azure SQL Database 또는 Azure SQL Managed Instance의 데이터베이스에 연결하고 T-SQL 문을 사용하여 쿼리하는 프로그램을 만드는 방법을 보여줍니다.
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: java
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: v-masebo
ms.date: 05/29/2020
ms.custom: seo-java-july2019. seo-java-august2019, sqldbrb=2 
ms.openlocfilehash: 6be52d2d3472888607bbd6276b4794184bb11273
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/01/2020
ms.locfileid: "84267395"
---
# <a name="quickstart-use-java-to-query-a-database-in-azure-sql-database-or-azure-sql-managed-instance"></a>빠른 시작: Java를 사용하여 Azure SQL Database 또는 Azure SQL Managed Instance의 데이터베이스 쿼리
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

이 빠른 시작에서는 Java를 사용하여 Azure SQL Database 또는 Azure SQL Managed Instance의 데이터베이스에 연결하고 T-SQL 문을 사용하여 데이터를 쿼리합니다.

## <a name="prerequisites"></a>필수 구성 요소

이 빠른 시작을 완료하려면 다음이 필요합니다.

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

  || SQL Database | SQL Managed Instance | Azure VM의 SQL Server |
  |:--- |:--- |:---|:---|
  | 생성| [포털](single-database-create-quickstart.md) | [포털](../managed-instance/instance-create-quickstart.md) | [포털](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  || [CLI](scripts/create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/create-and-configure-database-powershell.md) | [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) | [PowerShell](../virtual-machines/windows/sql-vm-create-powershell-quickstart.md)
  | 구성 | [서버 수준 IP 방화벽 규칙](firewall-create-server-level-portal-quickstart.md)| [VM에서 연결](../managed-instance/connect-vm-instance-configure.md)|
  |||[온-프레미스에서 연결](../managed-instance/point-to-site-p2s-configure.md) | [SQL Server 인스턴스에 연결](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  |데이터 로드|Adventure Works(빠른 시작마다 로드됨)|[Wide World Importers 복원](../managed-instance/restore-sample-database-quickstart.md) | [Wide World Importers 복원](../managed-instance/restore-sample-database-quickstart.md) |
  |||[GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)의 [BACPAC](database-import.md) 파일에서 Adventure Works 복원 또는 가져오기| [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)의 [BACPAC](database-import.md) 파일에서 Adventure Works 복원 또는 가져오기|
  |||

- [Java](/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server) 관련 소프트웨어

  # <a name="macos"></a>[macOS](#tab/macos)

  Homebrew 및 Java를 설치한 다음, [macOS에서 SQL Server를 사용하여 Java 앱 만들기](https://www.microsoft.com/sql-server/developer-get-started/java/mac/)의 **1.2** 및 **1.3** 단계를 사용하여 Maven을 설치합니다.

  # <a name="ubuntu"></a>[Ubuntu](#tab/ubuntu)

  Java, Java Development Kit를 차례로 설치한 다음, [Ubuntu에서 SQL Server를 사용하여 Java 앱 만들기](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu/)의 **1.2**, **1.3** 및 **1.4** 단계를 사용하여 Maven을 설치합니다.

  # <a name="windows"></a>[Windows](#tab/windows)

  Java를 설치한 다음, [Windows에서 SQL Server를 사용하여 Java 앱 만들기](https://www.microsoft.com/sql-server/developer-get-started/java/windows/)의 **1.2** 및 **1.3** 단계를 사용하여 Maven을 설치합니다.

  ---

> [!IMPORTANT]
> 이 문서의 스크립트는 **Adventure Works** 데이터베이스를 사용하도록 작성되었습니다.

> [!NOTE]
> 필요에 따라 Azure SQL Managed Instance를 사용하도록 선택할 수 있습니다.
>
> 만들고 구성하려면 [Azure Portal](../managed-instance/instance-create-quickstart.md), [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) 또는 [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44)를 사용한 다음, [온-프레미스](../managed-instance/point-to-site-p2s-configure.md) 또는 [VM](../managed-instance/connect-vm-instance-configure.md) 연결을 설정합니다.
>
> 데이터를 로드하려면 [Adventure Works](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) 파일을 사용하여 [BACPAC로 복원](database-import.md) 또는 [Wide World Importers 데이터베이스 복원](../managed-instance/restore-sample-database-quickstart.md)을 참조하세요.

## <a name="get-server-connection-information"></a>서버 연결 정보 가져오기

Azure SQL Database의 데이터베이스에 연결하는 데 필요한 연결 정보를 가져옵니다. 다음 절차를 수행하려면 정규화된 서버 이름이나 호스트 이름, 데이터베이스 이름 및 로그인 정보가 필요합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

2. **SQL 데이터베이스**를 선택하거나 **SQL Managed Instances** 페이지를 엽니다.

3. **개요** 페이지에서 Azure SQL Database의 데이터베이스에 대한 **서버 이름** 옆에 있는 정규화된 서버 이름 또는 Azure VM의 Azure SQL Managed Instance 또는 SQL Server에 대한 **호스트** 옆에 있는 정규화된 서버 이름(또는 IP 주소)을 검토합니다. 서버 이름이나 호스트 이름을 복사하려면 마우스로 해당 이름 위를 가리키고 **복사** 아이콘을 선택합니다.

> [!NOTE]
> Azure VM의 SQL Server에 대한 연결 정보는 [SQL Server에 연결](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server)을 참조하세요.

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

## <a name="add-code-to-query-the-database"></a>데이터베이스를 쿼리하는 코드 추가

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
   > 이 코드 예제는 Azure SQL Database에서 **AdventureWorksLT** 샘플 데이터베이스를 사용합니다.

## <a name="run-the-code"></a>코드 실행

1. 명령 프롬프트에서 앱을 실행합니다.

    ```bash
    mvn package -DskipTests
    mvn -q exec:java "-Dexec.mainClass=com.sqldbsamples.App"
    ```

1. 상위 20개 행이 반환되는지 확인하고, 앱 창을 닫습니다.

## <a name="next-steps"></a>다음 단계

- [Azure SQL Database에서 첫 번째 데이터베이스 디자인](design-first-database-tutorial.md)  
- [SQL Server용 Microsoft JDBC Driver](https://github.com/microsoft/mssql-jdbc)  
- [문제/질문 보고](https://github.com/microsoft/mssql-jdbc/issues)  
