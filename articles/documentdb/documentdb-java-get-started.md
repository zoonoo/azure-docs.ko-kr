---
title: "NoSQL 자습서: Azure DocumentDB Java SDK | Microsoft Docs"
description: "DocumentDB Java SDK를 사용하여 온라인 데이터베이스 및 Java 콘솔 응용 프로그램을 만드는 NoSQL 자습서입니다. Azure DocumentDB는 JSON에 대한 NoSQL 데이터베이스입니다."
keywords: "NoSQL 자습서, 온라인 데이터베이스, Java 콘솔 응용 프로그램"
services: documentdb
documentationcenter: Java
author: arramac
manager: jhubbard
editor: monicar
ms.assetid: 75a9efa1-7edd-4fed-9882-c0177274cbb2
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: hero-article
ms.date: 01/05/2017
ms.author: arramac
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: da7907ffc515ea2e3040075c93bcd53840cf3ff5
ms.lasthandoff: 03/29/2017


---
# <a name="nosql-tutorial-build-a-documentdb-java-console-application"></a>NoSQL 자습서: DocumentDB Java 콘솔 응용 프로그램 빌드
> [!div class="op_single_selector"]
> * [.NET](documentdb-get-started.md)
> * [.NET Core](documentdb-dotnetcore-get-started.md)
> * [MongoDB용 Node.js](documentdb-mongodb-samples.md)
> * [Node.JS](documentdb-nodejs-get-started.md)
> * [Java](documentdb-java-get-started.md)
> * [C++](documentdb-cpp-get-started.md)
>  
> 

Azure DocumentDB Java SDK용 NoSQL 자습서를 시작합니다. 이 자습서를 따라 하면 DocumentDB 리소스를 만들고 쿼리하는 콘솔 응용 프로그램이 생깁니다.

다음 항목에 대해서 다룹니다.

* DocumentDB 계정 만들기 및 연결
* Visual Studio 솔루션 구성
* 온라인 데이터베이스 만들기
* 컬렉션 만들기
* JSON 문서 만들기
* 컬렉션 쿼리
* JSON 문서 만들기
* 컬렉션 쿼리
* 문서 바꾸기
* 문서 삭제
* 데이터베이스 삭제

이제 시작하겠습니다.

## <a name="prerequisites"></a>필수 조건
다음 항목이 있는지 확인합니다.

* 활성 Azure 계정. 계정이 없는 경우 [무료 계정](https://azure.microsoft.com/free/)에 등록할 수 있습니다. 또는 이 자습서에 [Azure DocumentDB 에뮬레이터](documentdb-nosql-local-emulator.md)를 사용할 수 있습니다.
* [Git](https://git-scm.com/downloads)
* [JDK(Java Development Kit) 7 이상](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Maven](http://maven.apache.org/download.cgi)

## <a name="step-1-create-a-documentdb-account"></a>1단계: DocumentDB 계정 만들기
DocumentDB 계정을 만들어 보겠습니다. 사용하려는 계정이 이미 있는 경우 [GitHub 프로젝트 복제](#GitClone)로 건너뛸 수 있습니다. DocumentDB 에뮬레이터를 사용할 경우 [Azure DocumentDB 에뮬레이터](documentdb-nosql-local-emulator.md)의 단계에 따라 에뮬레이터를 설정하고 [GitHub 프로젝트 복제](#GitClone)로 건너뜁니다.

[!INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

## <a id="GitClone"></a>2단계: GitHub 프로젝트 복제
[DocumentDB 및 Java 시작](https://github.com/Azure-Samples/documentdb-java-getting-started)의 경우 GitHub 리포지토리를 복제하여 시작할 수 있습니다. 예를 들어 로컬 디렉터리에서 다음을 실행하여 샘플 프로젝트를 로컬로 검색합니다.

    git clone git@github.com:Azure-Samples/documentdb-java-getting-started.git

    cd documentdb-java-getting-started

디렉터리에는 프로젝트의 `pom.xml` 및 `Program.java`을 비롯한 Java 소스 코드를 포함하는 `src` 폴더를 포함합니다. 여기서는 Azure DocumentDB를 사용하여 문서 만들기 및 컬렉션 내에서 데이터 쿼리와 같은 간단한 작업을 수행하는 방법을 보여 줍니다. `pom.xml`은 [Maven의 DocumentDB Java SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb)에 대한 종속성을 포함합니다.

    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-documentdb</artifactId>
        <version>LATEST</version>
    </dependency>

## <a id="Connect"></a>3단계: DocumentDB 계정에 연결
다음으로 [Azure Portal](https://portal.azure.com)로 다시 이동하여 끝점과 기본 마스터 키를 검색합니다. DocumentDB 끝점 및 기본 키는 응용 프로그램에서 연결할 곳을 이해하고 DocumentDB에서 응용 프로그램 연결을 신뢰하는 데 필요합니다.

Azure 포털에서 DocumentDB 계정으로 이동한 다음 **키**를 클릭합니다. 포털에서 URI를 복사하고 Program.java 파일의 `<your endpoint URI>`에 붙여 넣습니다. 그런 다음 포털에서 기본 키를 복사하고 `<your key>`에 붙여 넣습니다.

    this.client = new DocumentClient(
        "<your endpoint URI>",
        "<your key>"
        , new ConnectionPolicy(),
        ConsistencyLevel.Session);

![Java 콘솔 응용 프로그램을 만들기 위해 NoSQL 자습서에서 사용하는 Azure Portal의 스크린샷입니다. DocumentDB 계정 블레이드의 키 단추 및 키 블레이드의 URI, 기본 키 및 보조키 값이 강조 표시된 DocumentDB 계정을 보여 줌][keys]

## <a name="step-4-create-a-database"></a>4단계: 데이터베이스 만들기
**DocumentClient** 클래스의 [createDatabase](http://azure.github.io/azure-documentdb-java/com/microsoft/azure/documentdb/DocumentClient.html#createDatabase-com.microsoft.azure.documentdb.Database-com.microsoft.azure.documentdb.RequestOptions-) 메서드를 사용하여 DocumentDB [데이터베이스](documentdb-resources.md#databases)를 만들 수 있습니다. 데이터베이스는 여러 컬렉션으로 분할된 JSON 문서 저장소의 논리적 컨테이너입니다.

    Database database = new Database();
    database.setId("familydb");
    this.client.createDatabase(database, null);

## <a id="CreateColl"></a>5단계: 컬렉션 만들기
> [!WARNING]
> **createCollection**은 가격 책정 의미가 포함된 예약된 처리량이 있는 새 컬렉션을 만듭니다. 자세한 내용은 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/documentdb/)를 참조하세요.
> 
> 

**DocumentClient** 클래스의 [createCollection](http://azure.github.io/azure-documentdb-java/com/microsoft/azure/documentdb/DocumentClient.html#createCollection-java.lang.String-com.microsoft.azure.documentdb.DocumentCollection-com.microsoft.azure.documentdb.RequestOptions-) 메서드를 사용하여 [컬렉션](documentdb-resources.md#collections)을 만들 수 있습니다. 컬렉션은 JSON 문서 및 관련 JavaScript 응용 프로그램 논리의 컨테이너입니다.


    DocumentCollection collectionInfo = new DocumentCollection();
    collectionInfo.setId("familycoll");

    // DocumentDB collections can be reserved with throughput specified in request units/second. 
    // Here we create a collection with 400 RU/s.
    RequestOptions requestOptions = new RequestOptions();
    requestOptions.setOfferThroughput(400);

    this.client.createCollection("/dbs/familydb", collectionInfo, requestOptions);

## <a id="CreateDoc"></a>6단계: JSON 문서 만들기
**DocumentClient** 클래스의 [createDocument](http://azure.github.io/azure-documentdb-java/com/microsoft/azure/documentdb/DocumentClient.html#createDocument-java.lang.String-java.lang.Object-com.microsoft.azure.documentdb.RequestOptions-boolean-) 메서드를 사용하여 [문서](documentdb-resources.md#documents)를 만들 수 있습니다. 문서는 사용자 정의(임의) JSON 콘텐츠입니다. 이제 하나 이상의 문서를 삽입할 수 있습니다. 데이터베이스에 저장하려는 데이터가 이미 있는 경우 DocumentDB의 [데이터 마이그레이션 도구](documentdb-import-data.md)를 사용하여 데이터를 데이터베이스로 가져올 수 있습니다.

    // Insert your Java objects as documents 
    Family andersenFamily = new Family();
    andersenFamily.setId("Andersen.1");
    andersenFamily.setLastName("Andersen")

    // More initialization skipped for brevity. You can have nested references
    andersenFamily.setParents(new Parent[] { parent1, parent2 });
    andersenFamily.setDistrict("WA5");
    Address address = new Address();
    address.setCity("Seattle");
    address.setCounty("King");
    address.setState("WA");

    andersenFamily.setAddress(address);
    andersenFamily.setRegistered(true);

    this.client.createDocument("/dbs/familydb/colls/familycoll", family, new RequestOptions(), true);

![NoSQL에서 Java 콘솔 응용 프로그램을 만들기 위해 사용한 계정, 데이터베이스, 컬렉션 및 문서 간의 계층 관계를 보여 주는 다이어그램](./media/documentdb-get-started/nosql-tutorial-account-database.png)

## <a id="Query"></a>7단계: DocumentDB 리소스 쿼리
DocumentDB는 각 컬렉션에 저장된 JSON 문서에 대해 [다양한 쿼리](documentdb-sql-query.md)를 지원합니다.  다음 샘플 코드에서는 [queryDocuments](http://azure.github.io/azure-documentdb-java/com/microsoft/azure/documentdb/DocumentClient.html#queryDocuments-java.lang.String-com.microsoft.azure.documentdb.SqlQuerySpec-com.microsoft.azure.documentdb.FeedOptions-) 메서드와 함께 SQL 구문을 사용하여 DocumentDB의 문서를 쿼리하는 방법을 보여 줍니다.

    FeedResponse<Document> queryResults = this.client.queryDocuments(
        "/dbs/familydb/colls/familycoll",
        "SELECT * FROM Family WHERE Family.lastName = 'Andersen'", 
        null);

    System.out.println("Running SQL query...");
    for (Document family : queryResults.getQueryIterable()) {
        System.out.println(String.format("\tRead %s", family));
    }

## <a id="ReplaceDocument"></a>8단계: JSON 문서 바꾸기
DocumentDB는 [replaceDocument](http://azure.github.io/azure-documentdb-java/com/microsoft/azure/documentdb/DocumentClient.html#replaceDocument-com.microsoft.azure.documentdb.Document-com.microsoft.azure.documentdb.RequestOptions-) 메서드를 사용하여 JSON 문서를 업데이트하도록 지원합니다.

    // Update a property
    andersenFamily.Children[0].Grade = 6;

    this.client.replaceDocument(
        "/dbs/familydb/colls/familycoll/docs/Andersen.1", 
        andersenFamily,
        null);

## <a id="DeleteDocument"></a>9단계: JSON 문서 삭제
마찬가지로 DocumentDB는 [deleteDocument](http://azure.github.io/azure-documentdb-java/com/microsoft/azure/documentdb/DocumentClient.html#deleteDocument-java.lang.String-com.microsoft.azure.documentdb.RequestOptions-) 메서드를 사용하여 JSON 문서를 삭제하도록 지원합니다.  

    this.client.delete("/dbs/familydb/colls/familycoll/docs/Andersen.1", null);

## <a id="DeleteDatabase"></a>10단계: 데이터베이스 삭제
만든 데이터베이스를 삭제하면 데이터베이스와 모든 하위 리소스(컬렉션, 문서 등)가 제거됩니다.

    this.client.deleteDatabase("/dbs/familydb", null);

## <a id="Run"></a>11단계: Java 콘솔 응용 프로그램 모두 함께 실행
콘솔에서 응용 프로그램을 실행하려면 먼저 Maven을 사용하여 컴파일합니다.
    
    mvn package

`mvn package`을 실행하면 Maven에서 최신 DocumentDB 라이브러리를 다운로드하고 `GetStarted-0.0.1-SNAPSHOT.jar`를 생성합니다. 그 후에 다음을 실행하여 앱을 실행합니다.

    mvn exec:java -D exec.mainClass=GetStarted.Program

축하합니다. 이 NoSQL 자습서를 완료했으며 실행되는 Java 콘솔 응용 프로그램이 생겼습니다.

## <a name="next-steps"></a>다음 단계
* Java 웹앱 자습서가 필요한가요? [DocumentDB를 사용하여 Java로 웹 응용 프로그램 빌드](documentdb-java-application.md)를 참조하세요.
* [DocumentDB 계정 모니터링](documentdb-monitor-accounts.md)방법에 대해 자세히 알아봅니다.
* [쿼리 실습](https://www.documentdb.com/sql/demo)의 샘플 데이터 집합에 대해 쿼리를 실행합니다.
* [DocumentDB 설명서](https://azure.microsoft.com/documentation/services/documentdb/)페이지의 개발 섹션에서 프로그래밍 모델에 대해 자세히 알아봅니다.

[documentdb-create-account]: documentdb-create-account.md
[keys]: media/documentdb-get-started/nosql-tutorial-keys.png

