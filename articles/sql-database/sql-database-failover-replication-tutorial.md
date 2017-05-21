---
title: "Azure SQL Database 솔루션 복제 및 장애 조치 | Microsoft Docs"
description: "Azure SQL Database와 응용 프로그램을 복제된 데이터베이스로 장애 조치하도록 구성하고 장애 조치를 테스트합니다."
services: sql-database
documentationcenter: 
author: anosov1960
manager: jstrauss
editor: 
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: tutorial-failover
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 04/18/2017
ms.author: sashan
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 7e5b82da402ab9d20410746c2b6c6b3aaab5b754
ms.contentlocale: ko-kr
ms.lasthandoff: 05/10/2017


---

# <a name="replicate-and-failover-an-azure-sql-database-solution"></a>SQL Database 솔루션 복제 및 장애 극복

이 자습서에서는 Azure SQL Database와 응용 프로그램을 원격 지역으로 장애 조치하도록 구성한 다음 장애 조치 계획을 테스트합니다. 

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

이 자습서를 완료하려면 다음이 설치되어 있어야 합니다.

- 최신 버전의 SSMS([SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)) - SSMS를 설치하면 다양한 데이터베이스 개발 작업을 자동화하는 데 사용할 수 있는 명령줄 유틸리티인 SQLPackage의 최신 버전도 설치됩니다. 
- 마이그레이션할 Azure 데이터베이스 - 이 자습서에서는 다음 빠른 시작 중 하나에서 **mySampleDatabase 이름의 AdventureWorksLT 샘플 데이터베이스를 사용합니다.

   - [DB 만들기 - 포털](sql-database-get-started-portal.md)
   - [DB 만들기 - CLI](sql-database-get-started-cli.md)

## <a name="create-azure-active-directory-users-optional"></a>Azure Active Directory 사용자 만들기(선택 사항)

이 단계에서는 Azure Active Directory 사용자를 만들거나 식별하여 Azure SQL Database 논리 서버 및 샘플 데이터베이스에 사용자로 추가합니다.
- 구독이 기존 사용자 계정이 있는 Azure Active Directory 회사 환경의 일부인 경우, 이 자습서의 Active Directory 관리자, 응용 프로그램 관리자 및 응용 프로그램 사용자로 사용할 3개의 사용자 계정을 식별하고, 3단계: SQL Database 로그인 및 사용자 만들기로 계속 진행합니다. 
- 구독이 Azure Active Directory 회사 환경의 일부가 아니거나 기존 사용자 계정이 없는 Azure Active Directory 회사 환경의 일부인 경우 새 Azure Active Directory 사용자 계정을 만들 수 있는 권한이 있습니다.

1. [Azure 포털](http://portal.azure.com)에 로그인합니다.
2. 왼쪽 창에서 **추가 서비스**를 클릭합니다.
3. 필터 텍스트 상자에서 **Azure**를 입력한 다음 **Azure Active Directory**를 선택합니다.
4. **Azure Active Directory** 페이지의 **빠른 작업** 창에서 **사용자 추가**를 클릭합니다.
5. **사용자** 양식에서 다음 사용자를 만듭니다.
   - 이름: **ad-admin**
   - 사용자 이름: **ad-admin@yourdomain**(Yopu4708)
6. **암호 표시** 확인란을 선택하고 나중에 사용할 수 있도록 이 사용자 계정의 암호를 기록해 둡니다.
7. **만들기**를 클릭합니다.
8. 이전의 세 단계를 반복하여 다음 두 명의 새 사용자를 만듭니다.
   - 이름: **app-admin**
   - 사용자 이름: **app-admin@yourdomain**(Buju4319)
   - 이름: **app-user**
   - 사용자 이름: **app-user@yourdomain**(Nonu4001).

9. 새 브라우저 창을 열고 새로 만든 **ad-admin** 계정을 사용하여 Azure Portal에 로그인합니다.
10. **암호 업데이트** 페이지의 **현재 암호** 상자에 시스템에서 생성된 암호를 입력합니다. 
11. **새 암호**와 **암호 확인** 상자에서 선택한 암호를 입력합니다.
12. **암호 업데이트 및 로그인**을 클릭합니다.

## <a name="configure-sql-database-integration-with-azure-active-directory"></a>Azure Active Directory와 SQL Database 통합 구성

1. 왼쪽 메뉴에서 **추가 서비스**를 클릭하고, 필터 텍스트 상자에서 **sql**을 입력한 다음, **SQL 서버**를 선택합니다.
2. **SQL 서버** 페이지에서 해당 SQL Database 서버를 클릭합니다.
3. 서버에 대한 **개요** 페이지의 Essentials 창에서 **Active Directory 관리자** 아래의 **구성되지 않음**을 클릭합니다.
4. **Active Directory 관리자** 페이지에서 **관리자 설정**을 클릭합니다.
5. Azure SQL Database 서버의 서버 관리자로 **ad-admin** Azure Active Directory 계정(또는 자신의 계정과 같은 기존의 다른 계정)을 선택합니다.
6. **선택**을 클릭합니다.
7. **Save**를 클릭합니다.

## <a name="create-users-with-permissions-for-your-database"></a>데이터베이스 권한이 있는 사용자 만들기

<TO DO: need to change script to grant app-user sufficient permissions to perform operation in java app>

SQL Server Management Studio를 사용하여 데이터베이스에 연결하고 사용자 계정을 만듭니다. 이러한 사용자 계정은 보조 서버에 자동으로 복제됩니다. 아직 방화벽을 구성하지 않은 IP 주소의 클라이언트에서 연결하는 경우 방화벽 규칙을 구성해야 할 수도 있습니다. 단계는 [Azure Portal을 사용하여 SQL DB 만들기](sql-database-get-started-portal.md)를 참조하세요.

1. SQL Server Management Studio를 엽니다.
2. **인증** 모드를 **Active Directory 암호 인증**으로 변경합니다.
3. 새로 디자인된 Azure Active Directory 서버 관리자 계정을 사용하여 서버에 연결합니다. 
4. [개체 탐색기]에서 **시스템 데이터베이스**를 확장하고 **mySampleDatabase**를 마우스 오른쪽 단추로 클릭한 다음 **새 쿼리**를 클릭합니다.
5. 쿼리 창에서 다음 쿼리를 실행하여 데이터베이스에 사용자 계정을 만들고 두 개의 관리 계정에 **db_owner** 권한을 부여합니다. 도메인 이름의 자리 표시자를 자신의 도메인으로 바꿉니다.

   ```tsql
   --Create Azure AD user account
   CREATE USER [app-admin@<yourdomain>] FROM EXTERNAL PROVIDER;
   --Add Azure AD user to db_owner role
   ALTER ROLE db_owner ADD MEMBER [app-admin@<yourdomain>]; 
   --Create additional Azure AD user account
   CREATE USER [app-user@<yourdomain>] FROM EXTERNAL PROVIDER;
   --Create SQL user account
   CREATE USER app_admin WITH PASSWORD = 'MyStrongPassword1';
   --Add SQL user to db_owner role
   ALTER ROLE db_owner ADD MEMBER app_admin; 
   --Create additional SQL user
   CREATE USER app_user WITH PASSWORD = 'MyStrongPassword1';
   ```

## <a name="create-database-level-firewall"></a>데이터베이스 수준 방화벽 만들기

SQL Server Management Studio를 사용하여 데이터베이스에 대한 데이터베이스 수준 방화벽 규칙을 만듭니다. 이 데이터베이스 수준 방화벽 규칙은 보조 서버에 자동으로 복제됩니다. 테스트를 위해 모든 IP 주소(0.0.0.0 및 255.255.255.255)에 대한 방화벽 규칙을 만들거나, 서버 방화벽 규칙을 만든 단일 IP 주소에 대한 방화벽 규칙을 만들거나, 이 자습서를 테스트하는 데 사용하려는 컴퓨터의 IP 주소에 대해 하나 이상의 방화벽 규칙을 구성할 수 있습니다.  

- 열린 쿼리 창에서 이전 쿼리를 다음 쿼리로 바꾸고, IP 주소를 해당 환경의 적절한 IP 주소로 바꿉니다. 

   ```tsql
   -- Create database-level firewall setting for your publich IP address
   EXECUTE sp_set_database_firewall_rule N'mySampleDatabase','0.0.0.1','0.0.0.1';
   ```  

## <a name="create-a-failover-group"></a>장애 조치 그룹 만들기 

장애 조치 지역을 선택하고, 해당 지역에 빈 서버를 만든 다음, 기존 서버와 새로운 빈 서버 간에 장애 조치 그룹을 만듭니다.

1. 변수를 채웁니다.

   ```powershell
   $secpasswd = ConvertTo-SecureString "yourstrongpassword" -AsPlainText -Force
   $mycreds = New-Object System.Management.Automation.PSCredential (“ServerAdmin”, $secpasswd)
   $myresourcegroup = "<your resource group>"
   $mylocation = "<resource group location>"
   $myserver = "<your existing server>"
   $mydatabase = "<your existing database>"
   $mydrlocation = "<your disaster recovery location>"
   $mydrserver = "<your disaster recovery server>"
   $myfailovergroup = "<your failover group"
   ```

2. 장애 조치 지역에 빈 백업 서버를 만듭니다.

   ```powershell
   $mydrserver = New-AzureRmSqlServer -ResourceGroupName $myresourcegroup -Location $mydrlocation -ServerName $mydrserver -ServerVersion "12.0" -SqlAdministratorCredentials $mycreds
   ```

3. 장애 조치 그룹을 만듭니다.

   ```powershell
   $myfailovergroup = New-AzureRMSqlDatabaseFailoverGroup –ResourceGroupName $myresourcegroup -ServerName "$myserver" -PartnerServerName $mydrserver  –FailoverGroupName $myfailovergroupname –FailoverPolicy "Automatic" -GracePeriodWithDataLossHours 2
   ```

4. 장애 조치 그룹에 데이터베이스를 추가합니다.

   ```powershell
   $mydrserver | Add-AzureRMSqlDatabaseToFailoverGroup –FailoverGroupName $myfailovergroup  -Database $mydatabase
   ```

## <a name="add-empty-backup-server-to-domain"></a>도메인에 빈 백업 서버 추가

1. Azure Portal의 왼쪽 메뉴에서 **추가 서비스**를 클릭하고, 필터 텍스트 상자에서 **sql**을 입력한 다음, **SQL 서버**를 선택합니다.
2. **SQL 서버** 페이지에서 새 SQL Database 재해 복구 서버를 클릭합니다.
3. 서버에 대한 **개요** 페이지의 Essentials 창에서 **Active Directory 관리자** 아래의 **구성되지 않음**을 클릭합니다.
4. **Active Directory 관리자** 페이지에서 **관리자 설정**을 클릭합니다.
5. 새 Azure SQL Database 재해 복구 서버의 서버 관리자로 **ad-admin** Azure Active Directory 계정(또는 자신의 계정과 같은 기존의 다른 계정)을 선택합니다.
6. **선택**을 클릭합니다.
7. **Save**를 클릭합니다.

## <a name="prepare-client-tier"></a>클라이언트 계층 준비

1. AWProfile 장애 조치 프로필을 사용하여 TM 프로필을 만듭니다.
2. 모니터링 구성

   ```powershell
   $profile = New-AzureRmTrafficManagerProfile -Name AWProfile -ResourceGroupName MYRG -TrafficRoutingMethod Failover -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
   $webapp1 = Get-AzureRMWebApp -Name WebappUSWest
   Add-AzureRmTrafficManagerEndpointConfig -EndpointName webapp1ep -TrafficManagerProfile $profile -Type AzureEndpoints -TargetResourceId $webapp1.Id -EndpointStatus Enabled
   $webapp2 = Get-AzureRMWebApp -Name WebappUSEast
   Add-AzureRmTrafficManagerEndpointConfig -EndpointName webapp2ep -TrafficManagerProfile $profile -Type AzureEndpoints -TargetResourceId $webapp2.Id -EndpointStatus Enabled
   Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile
   ```

## <a name="deploy-java-application-and-connect-to-database"></a>Java 응용 프로그램 배포 및 데이터베이스에 연결

<In progress>[Java를 사용하여 연결](sql-database-connect-query-java.md)을 참조하세요.

<TO DO: change user to app-user>

1. Java 8을 설치합니다.
2. Maven을 설치합니다.
3. Maven 프로젝트를 만듭니다.
4. pom.xml에 다음을 추가합니다. 

   - 종속성

      ```java
      <dependency>
         <groupId>com.microsoft.sqlserver</groupId>
         <artifactId>mssql-jdbc</artifactId>
         <version>6.1.0.jre8</version>
       </dependency>
      ```
   - 언어 수준

      ```java
      <properties>
         <maven.compiler.source>1.8</maven.compiler.source>
         <maven.compiler.target>1.8</maven.compiler.target>
      </properties>
      ```

   - jar의 매니페스트 파일을 지원하는 옵션 빌드

      ```java
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
5. App.java 파일에 다음을 추가합니다.

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

      private static final String PRIMARY_HOST_HAME = "your_primary_server_name";
      private static final String SECONDARY_HOST_NAME = "your_secondary_server_name";
      private static final String PRIMARY_HOST_HAME = "janengsampleserver";
      private static final String SECONDARY_HOST_NAME = PRIMARY_HOST_HAME;
    
      private static final String DB_NAME = "mySampleDatabase";
      private static final String USER = "ServerAdmin";
      private static final String PASSWORD = "ChangeYourAdminPassword1";

      private static final String PRIMARY_URL = String.format("jdbc:sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", PRIMARY_HOST_HAME, DB_NAME, USER, PASSWORD);
      private static final String SECONDARY_URL = String.format("jdbc:sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", SECONDARY_HOST_NAME, DB_NAME, USER, PASSWORD);

      public static void main(String[] args) {
         System.out.println("#######################################");
         System.out.println("## GEO DISTRIBUTED DATABASE TUTORIAL ##");
         System.out.println("#######################################");
         System.out.println(""); 

         // todo: get the max id from the table and initialize INSERT COUNTER with it so that the code will always run (avoid duplicate keys)  
         int highWaterMark = getHighWaterMarkId();

         try {
            for(int i = 1; i < 1000; i++) {
                //  loop will run for about 1h
                System.out.print(i + ": insert on primary " + (insertData((highWaterMark + i))?"successful":"failed"));
                TimeUnit.SECONDS.sleep(1);
                System.out.print(", read from secondary " + (selectData((highWaterMark + i))?"successful":"failed") + "\n");
                TimeUnit.SECONDS.sleep(3);
            }
         } catch(Exception e) {
            e.printStackTrace();
      }
   }

   private static boolean insertData(int id) {
      // Insert data into the product table with a unique product name that we can use to find the product again later
      String sql = "INSERT INTO SalesLT.Product (Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) VALUES (?,?,?,?,?,?);";

      try (Connection connection = DriverManager.getConnection(PRIMARY_URL); 
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
      // Query the data that was previously inserted into the primary database from the geo replicated database
      String sql = "SELECT Name, Color, ListPrice FROM SalesLT.Product WHERE Name = ?";

      try (Connection connection = DriverManager.getConnection(SECONDARY_URL); 
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
      // Query the high water mark id that is stored in the table to be able to make unique inserts 
      String sql = "SELECT MAX(ProductId) FROM SalesLT.Product";
      int result = 1;
        
      try (Connection connection = DriverManager.getConnection(SECONDARY_URL); 
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
6. 파일을 저장합니다.

## <a name="compile-and-run"></a>컴파일 및 실행

1. 콘솔로 이동하여 실행합니다.

   ```java
   mvn package
   ```
2. 완료되면 다음과 같이 exec를 실행하여 응용 프로그램을 실행합니다(수동으로 중지하지 않으면 약 1시간 동안 실행됨).

   ```
   mvn -q -e exec:java "-Dexec.mainClass=com.sqldbsamples.App"
   ```

   성공적으로 실행되면 출력은 다음과 같이 표시됩니다.

   #######################################
   ## <a name="geo-distributed-database-tutorial"></a>지리적 분산된 데이터베이스 자습서 ##
   #######################################

   1. insert on primary successful, read from secondary successful
   2. insert on primary successful, read from secondary successful
   3. insert on primary successful, read from secondary successful

## <a name="perform-dr-drill"></a>DR 드릴 수행

1. 강제 장애 조치를 사용하여 FG의 수동 장애 조치를 호출합니다. 드릴 중 데이터 손실이 허용되지 않는 경우 -AllowDataLoss를 제거해야 합니다.

   ```powershell
   $fg | Switch-AzureRMSqlDatabaseFailoverGroup -AllowDataLoss
   ```

2.    끝점 장애 조치를 트리거하도록 TM 프로필에서 기본 끝점을 사용하지 않도록 설정합니다. 

   ```powershell
   Disable-AzureRmTrafficManagerEndpoint -Name webapp1ep -Type AzureEndpoints -ProfileName $profile.Name -ResourceGroupName MYRG -Force
   ```

3.    응용 프로그램을 다시 실행합니다.


## <a name="relocate-application-to-primary-region"></a>주 지역에 응용 프로그램 재배치

1.    FG의 친숙한 수동 장애 조치를 호출합니다. -AllowDataLoss를 지정하지 마세요.

   ```powershell
   $fg | Switch-AzureRMSqlDatabaseFailoverGroup 
   ```

2.    끝점 장애 조치를 트리거하도록 TM 프로필에서 보조 끝점(webapp2ep)을 사용하지 않도록 설정합니다. 

   ```powershell
   Disable-AzureRmTrafficManagerEndpoint -Name webapp2ep -Type AzureEndpoints -ProfileName $profile.Name -ResourceGroupName MYRG -Force
   ```

3.    응용 프로그램을 다시 실행합니다.

## <a name="troubleshoot-failover"></a>장애 조치 문제 해결 

현재 기본 지역을 확인하여 장애 조치가 발생했는지 확인합니다. 역할에서 해당 지역이 기본 또는 보조 지역인지를 보여 줍니다.

   ```powershell
   $fg = Get-AzureRMSqlDatabaseFailoverGroup -ResourceGroupName "myrg" -ServerName "AWserver" 
   print $fg.role
   ```

## <a name="next-steps"></a>다음 단계 

- 가까운 시일 내 발표할 예정입니다!
