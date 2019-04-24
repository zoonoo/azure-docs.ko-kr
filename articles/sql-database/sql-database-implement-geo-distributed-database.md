---
title: 지리적으로 분산된 Azure SQL Database 솔루션 구현 | Microsoft Docs
description: Azure SQL Database와 애플리케이션을 복제된 데이터베이스로 장애 조치(failover)하도록 구성하고 장애 조치(failover)를 테스트하는 방법을 알아봅니다.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: 6022c016b83ffe1362db4d826a5ee4397afd4128
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60338992"
---
# <a name="tutorial-implement-a-geo-distributed-database"></a>자습서: 지리적으로 분산된 데이터베이스 구현

Azure SQL Database와 애플리케이션을 원격 지역으로 장애 조치(failover)하도록 구성하고 장애 조치(failover) 계획을 테스트합니다. 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> - [장애 조치(failover) 그룹](sql-database-auto-failover-group.md) 만들기
> - Java 애플리케이션을 실행하여 Azure SQL 데이터베이스 쿼리
> - 테스트 장애 조치

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Azure SQL Database, Azure Resource Manager PowerShell 모듈은 계속 지원 하지만 Az.Sql 모듈에 대 한 모든 향후 개발 됩니다. 이러한 cmdlet에 대 한 참조 [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)합니다. Az 모듈에는 AzureRm 모듈의 명령에 대 한 인수를 실질적으로 동일합니다.

이 자습서를 완료하려면 다음 항목을 설치했는지 확인하세요.

- [Azure PowerShell](/powershell/azureps-cmdlets-docs)
- Azure SQL 데이터베이스입니다. 데이터베이스를 만들려면 다음 중 하나를 사용합니다.
  - [포털](sql-database-single-database-get-started.md)
  - [CLI](sql-database-cli-samples.md)
  - [PowerShell](sql-database-powershell-samples.md)

  > [!NOTE]
  > 이 자습서에서는 *AdventureWorksLT* 샘플 데이터베이스를 사용합니다.

- Java 및 Maven. [SQL Server를 사용하여 앱 빌드](https://www.microsoft.com/sql-server/developer-get-started/)로 이동하여 **Java**를 강조 표시한 다음 사용 중인 환경을 선택하고 해당하는 단계를 진행합니다.

> [!IMPORTANT]
> 이 자습서의 단계를 수행하는 컴퓨터의 공용 IP 주소를 사용하도록 방화벽 규칙을 설정하세요. 데이터베이스 수준 방화벽 규칙은 보조 서버에 자동으로 복제됩니다.
>
> 자세한 내용은 [데이터베이스 수준 방화벽 규칙 만들기](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)를 참조하세요. 사용 중인 컴퓨터용 서버 수준 방화벽 규칙에 사용되는 IP 주소를 확인하려면 [서버 수준 방화벽 만들기](sql-database-server-level-firewall-rule.md)를 참조하세요.  

## <a name="create-a-failover-group"></a>장애 조치 그룹 만들기

Azure PowerShell을 사용하여 기존 Azure SQL Server와 다른 지역의 새 Azure SQL Server 사이에 [장애 조치(failover) 그룹](sql-database-auto-failover-group.md)을 만듭니다. 그런 다음 장애 조치(failover) 그룹에 샘플 데이터베이스를 추가합니다.

> [!IMPORTANT]
> [!INCLUDE [sample-powershell-install](../../includes/sample-powershell-install-no-ssh.md)]

장애 조치(failover) 그룹을 만들려면 다음 스크립트를 실행합니다.

   ```powershell
    # Set variables for your server and database
    $adminlogin = "<your admin>"
    $password = "<your password>"
    $myresourcegroupname = "<your resource group name>"
    $mylocation = "<your resource group location>"
    $myservername = "<your existing server name>"
    $mydatabasename = "<your database name>"
    $mydrlocation = "<your disaster recovery location>"
    $mydrservername = "<your disaster recovery server name>"
    $myfailovergroupname = "<your globally unique failover group name>"

    # Create a backup server in the failover region
    New-AzSqlServer -ResourceGroupName $myresourcegroupname `
       -ServerName $mydrservername `
       -Location $mydrlocation `
       -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
          -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))

    # Create a failover group between the servers
    New-AzSqlDatabaseFailoverGroup `
       –ResourceGroupName $myresourcegroupname `
       -ServerName $myservername `
       -PartnerServerName $mydrservername  `
       –FailoverGroupName $myfailovergroupname `
       –FailoverPolicy Automatic `
       -GracePeriodWithDataLossHours 2

    # Add the database to the failover group
    Get-AzSqlDatabase `
       -ResourceGroupName $myresourcegroupname `
       -ServerName $myservername `
       -DatabaseName $mydatabasename | `
     Add-AzSqlDatabaseToFailoverGroup `
       -ResourceGroupName $myresourcegroupname `
       -ServerName $myservername `
       -FailoverGroupName $myfailovergroupname
   ```

Azure Portal에서 데이터베이스를 선택한 다음 **설정** > **지역에서 복제**를 선택하여 지역에서 복제 설정을 변경할 수도 있습니다.

![지역에서 복제 설정](./media/sql-database-implement-geo-distributed-database/geo-replication.png)

## <a name="run-the-sample-project"></a>샘플 프로젝트 실행

1. 콘솔에서 다음 명령을 사용하여 Maven 프로젝트를 만듭니다.

   ```bash
   mvn archetype:generate "-DgroupId=com.sqldbsamples" "-DartifactId=SqlDbSample" "-DarchetypeArtifactId=maven-archetype-quickstart" "-Dversion=1.0.0"
   ```

1. **Y**를 입력하고 **Enter** 키를 누릅니다.

1. 디렉터리를 새 프로젝트로 변경합니다.

   ```bash
   cd SqlDbSample
   ```

1. 원하는 편집기를 사용하여 프로젝트 폴더에서 *pom.xml* 파일을 엽니다.

1. 다음 `dependency` 섹션을 추가하여 SQL Server 종속성용 Microsoft JDBC Driver를 추가합니다. 더 큰 `dependencies` 섹션에 종속성을 붙여 넣어야 합니다.

   ```xml
   <dependency>
     <groupId>com.microsoft.sqlserver</groupId>
     <artifactId>mssql-jdbc</artifactId>
    <version>6.1.0.jre8</version>
   </dependency>
   ```

1. `dependencies` 섹션 뒤에 `properties` 섹션을 추가하여 Java 버전을 지정합니다.

   ```xml
   <properties>
     <maven.compiler.source>1.8</maven.compiler.source>
     <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   ```

1. `properties` 섹션 뒤에 `build` 섹션을 추가하여 매니페스트 파일을 지원합니다.

   ```xml
   <build>
     <plugins>
       <plugin>
         <groupId>org.apache.maven.plugins</groupId>
         <artifactId>maven-jar-plugin</artifactId>
         <version>3.0.0</version>
         <configuration>
           <archive>
             <manifest>
               <mainClass>com.sqldbsamples.App</mainClass>
             </manifest>
           </archive>
        </configuration>
       </plugin>
     </plugins>
   </build>
   ```

1. *pom.xml* 파일을 저장하고 닫습니다.

1. ..\SqlDbSample\src\main\java\com\sqldbsamples에 있는 *App.java* 파일을 열고 해당 내용을 다음 코드로 바꿉니다.

   ```java
   package com.sqldbsamples;

   import java.sql.Connection;
   import java.sql.Statement;
   import java.sql.PreparedStatement;
   import java.sql.ResultSet;
   import java.sql.Timestamp;
   import java.sql.DriverManager;
   import java.util.Date;
   import java.util.concurrent.TimeUnit;

   public class App {

      private static final String FAILOVER_GROUP_NAME = "<your failover group name>";  // add failover group name
  
      private static final String DB_NAME = "<your database>";  // add database name
      private static final String USER = "<your admin>";  // add database user
      private static final String PASSWORD = "<your password>";  // add database password

      private static final String READ_WRITE_URL = String.format("jdbc:" +
         "sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;" +
         "hostNameInCertificate=*.database.windows.net;loginTimeout=30;", +
         FAILOVER_GROUP_NAME, DB_NAME, USER, PASSWORD);
      private static final String READ_ONLY_URL = String.format("jdbc:" +
         "sqlserver://%s.secondary.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;" +
         "hostNameInCertificate=*.database.windows.net;loginTimeout=30;", +
         FAILOVER_GROUP_NAME, DB_NAME, USER, PASSWORD);

      public static void main(String[] args) {
         System.out.println("#######################################");
         System.out.println("## GEO DISTRIBUTED DATABASE TUTORIAL ##");
         System.out.println("#######################################");
         System.out.println("");

         int highWaterMark = getHighWaterMarkId();

         try {
            for(int i = 1; i < 1000; i++) {
                //  loop will run for about 1 hour
                System.out.print(i + ": insert on primary " +
                   (insertData((highWaterMark + i))?"successful":"failed"));
                TimeUnit.SECONDS.sleep(1);
                System.out.print(", read from secondary " +
                   (selectData((highWaterMark + i))?"successful":"failed") + "\n");
                TimeUnit.SECONDS.sleep(3);
            }
         } catch(Exception e) {
            e.printStackTrace();
      }
   }

   private static boolean insertData(int id) {
      // Insert data into the product table with a unique product name so we can find the product again
      String sql = "INSERT INTO SalesLT.Product " +
         "(Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) VALUES (?,?,?,?,?,?);";

      try (Connection connection = DriverManager.getConnection(READ_WRITE_URL);
              PreparedStatement pstmt = connection.prepareStatement(sql)) {
         pstmt.setString(1, "BrandNewProduct" + id);
         pstmt.setInt(2, 200989 + id + 10000);
         pstmt.setString(3, "Blue");
         pstmt.setDouble(4, 75.00);
         pstmt.setDouble(5, 89.99);
         pstmt.setTimestamp(6, new Timestamp(new Date().getTime()));
         return (1 == pstmt.executeUpdate());
      } catch (Exception e) {
         return false;
      }
   }

   private static boolean selectData(int id) {
      // Query the data previously inserted into the primary database from the geo replicated database
      String sql = "SELECT Name, Color, ListPrice FROM SalesLT.Product WHERE Name = ?";

      try (Connection connection = DriverManager.getConnection(READ_ONLY_URL);
              PreparedStatement pstmt = connection.prepareStatement(sql)) {
         pstmt.setString(1, "BrandNewProduct" + id);
         try (ResultSet resultSet = pstmt.executeQuery()) {
            return resultSet.next();
         }
      } catch (Exception e) {
         return false;
      }
   }

   private static int getHighWaterMarkId() {
      // Query the high water mark id stored in the table to be able to make unique inserts
      String sql = "SELECT MAX(ProductId) FROM SalesLT.Product";
      int result = 1;
      try (Connection connection = DriverManager.getConnection(READ_WRITE_URL);
              Statement stmt = connection.createStatement();
              ResultSet resultSet = stmt.executeQuery(sql)) {
         if (resultSet.next()) {
             result =  resultSet.getInt(1);
            }
         } catch (Exception e) {
          e.printStackTrace();
         }
         return result;
      }
   }
   ```

1. *App.java* 파일을 저장하고 닫습니다.

1. 명령 콘솔에서 다음 명령을 실행합니다.

   ```bash
   mvn package
   ```

1. 애플리케이션을 시작합니다. 애플리케이션은 수동으로 중지할 때까지 1시간 정도 실행되므로 장애 조치(failover) 테스트를 실행할 수 있습니다.

   ```bash
   mvn -q -e exec:java "-Dexec.mainClass=com.sqldbsamples.App"
   ```

   ```output
   #######################################
   ## GEO DISTRIBUTED DATABASE TUTORIAL ##
   #######################################

   1. insert on primary successful, read from secondary successful
   2. insert on primary successful, read from secondary successful
   3. insert on primary successful, read from secondary successful
   ...
   ```

## <a name="test-failover"></a>테스트 장애 조치

다음 스크립트를 실행하여 장애 조치(failover) 시뮬레이션을 진행한 다음 애플리케이션 결과를 관찰합니다. 데이터베이스 마이그레이션 중에 일부 삽입과 선택이 실패하는 방식을 잘 살펴봅니다.

다음 명령을 사용한 테스트 중에 재해 복구 서버의 역할을 확인할 수도 있습니다.

   ```powershell
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $myfailovergroupname `
      -ResourceGroupName $myresourcegroupname `
      -ServerName $mydrservername).ReplicationRole
   ```

장애 조치(failover)를 테스트하려면:

1. 장애 조치(failover) 그룹의 수동 장애 조치(failover)를 시작합니다.

   ```powershell
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $myresourcegroupname `
      -ServerName $mydrservername `
      -FailoverGroupName $myfailovergroupname
   ```

1. 장애 조치(failover) 그룹을 주 서버로 되돌립니다.

   ```powershell
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $myresourcegroupname `
      -ServerName $myservername `
      -FailoverGroupName $myfailovergroupname
   ```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure SQL Database와 애플리케이션을 원격 지역으로 장애 조치(failover)하도록 구성하고 장애 조치(failover) 계획을 테스트했습니다. 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> - 지역에서 복제 장애 조치(failover) 그룹 만들기
> - Java 애플리케이션을 실행하여 Azure SQL 데이터베이스 쿼리
> - 테스트 장애 조치

DMS를 사용한 마이그레이션 방법을 설명하는 다음 자습서를 진행합니다.

> [!div class="nextstepaction"]
> [DMS를 사용하여 Azure SQL Database Managed Instance로 SQL Server 마이그레이션](../dms/tutorial-sql-server-to-managed-instance.md)
