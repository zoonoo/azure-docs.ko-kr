---
title: Java 응용 프로그램을 사용하여 Azure Cosmos DB Cassandra API 계정, 데이터베이스 및 테이블 만들기
description: 이 문서에는 Java 응용 프로그램을 사용하여 Cassandra API 계정을 만들고, 데이터베이스(키스페이스라고도 함) 및 테이블을 해당 계정에 추가하는 방법을 보여 줍니다.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
services: cosmos-db
ms.service: cosmos-db
ms.component: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 09/24/2018
ms.openlocfilehash: b5e3d87e026b65a602b7bdf2e52365d13b21f62f
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/12/2018
ms.locfileid: "49166834"
---
# <a name="create-an-azure-cosmos-db-cassandra-api-account-database-and-a-table-by-using-a-java-application"></a>Java 응용 프로그램을 사용하여 Azure Cosmos DB Cassandra API 계정, 데이터베이스 및 테이블 만들기

이 자습서에서는 Java 응용 프로그램을 사용하여 Azure Cosmos DB에서 Cassandra API 계정을 만들고, 데이터베이스(키스페이스라고도 함)를 추가하고, 테이블을 추가하는 방법을 설명합니다. Java 응용 프로그램은 [Java 드라이버](https://github.com/datastax/java-driver)를 사용하여 사용자 ID, 사용자 이름, 사용자 도시 등의 세부 정보를 포함하는 사용자 데이터베이스를 만듭니다.  

이 자습서에서 다루는 작업은 다음과 같습니다.

> [!div class="checklist"]
> * Cassandra 데이터베이스 계정 만들기
> * 계정 연결 문자열 가져오기
> * Maven 프로젝트 및 종속성 만들기
> * 데이터베이스 및 테이블 추가
> * 앱 실행

## <a name="prerequisites"></a>필수 조건 

* Azure 구독이 아직 없는 경우 시작하기 전에  [체험 계정](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) 을 만듭니다. 또는 Azure 구독, 요금 및 약정 없이  [Azure Cosmos DB 평가판을 사용](https://azure.microsoft.com/try/cosmosdb/) 할 수 있습니다. 

* [JDK(Java Development Kit)](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)의 최신 버전 살펴보기 

* [Maven](http://maven.apache.org/) 이진 보관 파일을 [다운로드](http://maven.apache.org/download.cgi)하여 [설치](http://maven.apache.org/install.html) 
  - Ubuntu에서  `apt-get install maven` 을 실행하여 Maven을 실행할 수 있습니다. 

## <a name="create-a-database-account"></a>데이터베이스 계정 만들기 

1.  [Azure Portal](https://portal.azure.com/)에 로그인합니다. 

2.  **리소스 만들기** > **데이터베이스** > **Azure Cosmos DB**를 선택합니다. 

3.  **새 계정**  창에서 새 Azure Cosmos DB 계정에 대한 설정을 입력합니다. 

   |설정   |제안 값  |설명  |
   |---------|---------|---------|
   |ID   |   고유한 이름을 입력합니다.    | 이 Azure Cosmos DB 계정을 식별하는 고유한 이름을 입력합니다. <br/><br/>접점을 만들기 위해 제공하는 ID에 cassandra.cosmosdb.azure.com이 추가되므로 식별할 수 있는 고유한 ID를 사용해야 합니다.         |
   |API    |  Cassandra   |  API는 만들 계정의 형식을 결정합니다. <br/> 이 문서에서는 CQL 구문을 사용하여 쿼리할 수 있는 넓은 열 데이터베이스를 만들기 때문에 **Cassandra**를 선택합니다.  |
   |구독    |  사용자의 구독        |  이 Azure Cosmos DB 계정에 사용할 Azure 구독을 선택합니다.        |
   |리소스 그룹   | 이름 입력    |   **새로 만들기**를 선택하고 계정에 대한 새 리소스 그룹 이름을 입력합니다. 간단히 하기 위해 ID와 동일한 이름을 사용할 수 있습니다.    |
   |위치    |  사용자와 가장 가까운 지역 선택    |  Azure Cosmos DB 계정을 호스트할 지리적 위치를 선택합니다. 데이터에 가장 빨리 액세스할 수 있도록 사용자와 가장 가까운 위치를 사용합니다.    |

   ![포털을 사용하여 계정 만들기](./media/create-cassandra-api-account-java/create-account.png)

4. 그런 다음,  **만들기**를 선택합니다. <br/>계정 생성에는 몇 분 정도가 소요됩니다. 리소스가 생성된 후 포털의 오른쪽 모서리에서 **배포 성공** 알림을 확인할 수 있습니다.

## <a name="get-the-connection-details-of-your-account"></a>계정의 연결 세부 정보 가져오기  

Azure Portal에서 연결 문자열 정보를 가져오고 이를 Java 구성 파일에 복사합니다. 이를 통해 앱이 호스팅된 데이터베이스와 통신할 수 있게 됩니다. 

1.  [Azure Portal](http://portal.azure.com/)에서 Cosmos DB 계정으로 이동합니다. 

2.  **연결 문자열** 창을 엽니다.  

3. 다음 단계에서 사용할 **CONTACT POINT**, **PORT**, **USERNAME** 및 **PRIMARY PASSWORD** 값을 복사합니다.

## <a name="create-maven-project-dependencies-and-utility-classes"></a>Maven 프로젝트, 종속성 및 유틸리티 클래스 만들기 

이 문서에서 사용하는 Java 샘플 프로젝트는 GitHub에서 호스트됩니다. [azure-cosmos-db-cassandra-java-getting-started](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started) 리포지토리에서 다운로드할 수 있습니다. 

파일을 다운로드한 후 `java-examples\src\main\resources\config.properties` 파일에 있는 연결 문자열 정보를 업데이트하고 실행합니다.  

```java
cassandra_host=<FILLME_with_CONTACT POINT> 
cassandra_port = 10350 
cassandra_username=<FILLME_with_USERNAME> 
cassandra_password=<FILLME_with_PRIMARY PASSWORD> 
```

또는 샘플을 처음부터 빌드할 수도 있습니다.  

1. 터미널 또는 명령 프롬프트에서 Cassandra-demo라는 새 Maven 프로젝트를 만듭니다. 

   ```bash
   mvn archetype:generate -DgroupId=com.azure.cosmosdb.cassandra -DartifactId=cassandra-demo -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false 
   ```
 
2. `cassandra-demo` 폴더를 찾습니다. 텍스트 편집기를 사용하여 생성된 `pom.xml` 파일을 엽니다. 

   [pom.xml](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/java-examples/pom.xml) 파일에 표시된 대로 Cassandra 종속성을 추가하고 프로젝트에 필요한 플러그 인을 빌드합니다.  

3. `cassandra-demo\src\main` 폴더 아래에서 `resources`라는 새 폴더를 만듭니다.  리소스 폴더 아래에서 config.properties 및 log4j.properties 파일을 추가합니다.

   - [config.properties](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/java-examples/src/main/resources/config.properties) 파일은 Azure Cosmos DB Cassandra API 연결 엔드포인트 및 키 값을 저장합니다. 
   
   - [log4j.properties](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/java-examples/src/main/resources/log4j.properties) 파일은 Cassandra API와 상호 작용할 때 필요한 로깅 수준을 정의합니다.  

4. 그런 다음, `src/main/java/com/azure/cosmosdb/cassandra/` 폴더로 이동합니다. cassandra 폴더 내에서 `utils`라는 또 다른 폴더를 만듭니다. 새 폴더는 Cassandra API 계정에 연결하는 데 필요한 유틸리티 클래스를 저장합니다. 

   [CassandraUtils](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/java-examples/src/main/java/com/azure/cosmosdb/cassandra/util/CassandraUtils.java) 클래스를 추가하여 클러스터를 만들고 Cassandra 세션을 열고 닫습니다. 클러스터가 Azure Cosmos DB Cassandra API에 연결되고 액세스할 세션을 반환합니다. [Configurations](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/java-examples/src/main/java/com/azure/cosmosdb/cassandra/util/Configurations.java) 클래스를 사용하여 config.properties 파일에서 연결 문자열 정보를 읽습니다. 

5. Java 샘플은 사용자 정보(예: 사용자 이름, 사용자 ID, 사용자 도시)를 사용하여 데이터베이스를 만듭니다. main 함수의 사용자 세부 정보에 액세스하려면 get 및 set 메서드를 정의해야 합니다.
 
   get 및 set 메서드를 사용하여 `src/main/java/com/azure/cosmosdb/cassandra/` 폴더 아래에 [User.java](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/java-examples/src/main/java/com/azure/cosmosdb/cassandra/User.java) 클래스를 만듭니다. 

## <a name="add-a-database-and-a-table"></a>데이터베이스 및 테이블 추가  

이 섹션에서는 CQL(Cassandra Query Language)을 사용하여 데이터베이스(키스페이스) 및 테이블을 추가하는 방법을 설명합니다. 이러한 명령에 대한 CQL 구문을 알아보려면 [create keyspace](https://docs.datastax.com/en/cql/3.3/cql/cql_reference/cqlCreateKeyspace.html) 및 [create table](https://docs.datastax.com/en/cql/3.3/cql/cql_reference/cqlCreateTable.html#cqlCreateTable) 쿼리 구문을 참조하세요. 

1. `src\main\java\com\azure\cosmosdb\cassandra` 폴더 아래에서 `repository`라는 새 폴더를 만듭니다. 

2. 그런 다음, `UserRepository` Java 클래스를 만들고 여기에 다음 코드를 추가합니다. 

   ```java
   package com.azure.cosmosdb.cassandra.repository; 
   import java.util.List; 
   import com.datastax.driver.core.BoundStatement; 
   import com.datastax.driver.core.PreparedStatement; 
   import com.datastax.driver.core.Row; 
   import com.datastax.driver.core.Session; 
   import org.slf4j.Logger; 
   import org.slf4j.LoggerFactory; 
   
   /** 
    * Create a Cassandra session 
    */ 
   public class UserRepository { 
   
       private static final Logger LOGGER = LoggerFactory.getLogger(UserRepository.class); 
       private Session session; 
       public UserRepository(Session session) { 
           this.session = session; 
       } 
   
       /** 
       * Create keyspace uprofile in cassandra DB 
        */ 
   
       public void createKeyspace() { 
            final String query = "CREATE KEYSPACE IF NOT EXISTS uprofile WITH REPLICATION = { 'class' : 'NetworkTopologyStrategy', 'datacenter1' : 1 }"; 
           session.execute(query); 
           LOGGER.info("Created keyspace 'uprofile'"); 
       } 
   
       /** 
        * Create user table in cassandra DB 
        */ 
   
       public void createTable() { 
           final String query = "CREATE TABLE IF NOT EXISTS uprofile.user (user_id int PRIMARY KEY, user_name text, user_bcity text)"; 
           session.execute(query); 
           LOGGER.info("Created table 'user'"); 
       } 
   } 
   ```

3. `src\main\java\com\azure\cosmosdb\cassandra` 폴더를 찾아 `examples`라는 새 하위 폴더를 만듭니다.

4. 다음으로 `UserProfile` Java 클래스를 만듭니다. 이 클래스에는 이전에 정의한 createKeyspace 및 createTable 메서드를 호출하는 main 메서드가 들어 있습니다. 

   ```java
   package com.azure.cosmosdb.cassandra.examples; 
   import java.io.IOException; 
   import com.azure.cosmosdb.cassandra.repository.UserRepository; 
   import com.azure.cosmosdb.cassandra.util.CassandraUtils; 
   import com.datastax.driver.core.PreparedStatement; 
   import com.datastax.driver.core.Session; 
   import org.slf4j.Logger; 
   import org.slf4j.LoggerFactory; 
   
   /** 
    * Example class which will demonstrate following operations on Cassandra Database on CosmosDB 
    * - Create Keyspace 
    * - Create Table 
    * - Insert Rows 
    * - Select all data from a table 
    * - Select a row from a table 
    */ 
   
   public class UserProfile { 
   
       private static final Logger LOGGER = LoggerFactory.getLogger(UserProfile.class); 
       public static void main(String[] s) throws Exception { 
           CassandraUtils utils = new CassandraUtils(); 
           Session cassandraSession = utils.getSession(); 
   
           try { 
               UserRepository repository = new UserRepository(cassandraSession); 
               //Create keyspace in cassandra database 
               repository.createKeyspace(); 
               //Create table in cassandra database 
               repository.createTable(); 
   
           } finally { 
               utils.close(); 
               LOGGER.info("Please delete your table after verifying the presence of the data in portal or from CQL"); 
           } 
       } 
   } 
   ```
 
## <a name="run-the-app"></a>앱 실행 

1. 명령 프롬프트 또는 터미널 창을 엽니다. 다음 코드 블록을 붙여넣습니다. 

   이 코드는 프로젝트를 만든 폴더 경로로 디렉터리를 변경(cd)합니다. 그런 다음, `mvn clean install` 명령을 실행하여 대상 폴더 내에 `cosmosdb-cassandra-examples.jar` 파일을 생성합니다. 마지막으로 Java 응용 프로그램을 실행합니다.

   ```bash
   cd cassandra-demo

   mvn clean install 

   java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile 
   ```

   터미널 창에 키스페이스와 테이블이 작성되었다는 알림이 표시됩니다. 
   
2. 이제 Azure Portal에서 **데이터 탐색기**를 열어 키스페이스 및 테이블이 생성되었는지 확인합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Java 응용 프로그램을 사용하여 Azure Cosmos DB Cassandra API 계정, 데이터베이스 및 테이블을 만드는 방법을 알아보았습니다. 이제 다음 문서로 진행할 수 있습니다.

> [!div class="nextstepaction"]
> [샘플 데이터를 Cassandra API 테이블에 로드](cassandra-api-load-data.md).
