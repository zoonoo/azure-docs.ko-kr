---
title: "Graph API를 사용하여 Azure Cosmos DB Java 응용 프로그램 빌드 | Microsoft Docs"
description: "Gremlin을 사용하여 Azure Cosmos DB에서 그래프 데이터에 연결 및 쿼리하는 데 사용할 수 있는 Java 코드 샘플을 제시합니다."
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: daacbabf-1bb5-497f-92db-079910703046
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 06/27/2017
ms.author: arramac
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: d9619bd9a012a347634282788b3a318886967a3f
ms.contentlocale: ko-kr
ms.lasthandoff: 06/28/2017


---
# Azure Cosmos DB: Graph API를 사용하여 Java 응용 프로그램 빌드
<a id="azure-cosmos-db-build-a-java-application-using-the-graph-api" class="xliff"></a>

Azure Cosmos DB는 전 세계에 배포된 Microsoft의 다중 모델 데이터베이스 서비스입니다. Azure Cosmos DB의 핵심인 전역 배포 및 수평적 크기 조정 기능의 이점을 활용하여 문서, 키/값 및 그래프 데이터베이스를 빠르게 만들고 쿼리할 수 있습니다. 

이 빠른 시작에서는 Azure Portal을 사용하여 Graph API용 Azure Cosmos DB 계정(미리 보기), 데이터베이스 및 그래프를 만드는 방법을 보여줍니다. 그런 다음 OSS [Gremlin Java](https://mvnrepository.com/artifact/org.apache.tinkerpop/gremlin-driver) 드라이버를 사용하여 콘솔 앱을 빌드 및 실행합니다.  

## 필수 조건
<a id="prerequisites" class="xliff"></a>

* 이 샘플을 실행하기 전에 다음 필수 조건이 있어야 합니다.
   * JDK 1.7+(JDK가 없는 경우 `apt-get install default-jdk` 실행) 및 `JAVA_HOME`과 같은 환경 변수 설정
   * Maven(Maven이 없는 경우 `apt-get install maven` 실행)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## 데이터베이스 계정 만들기
<a id="create-a-database-account" class="xliff"></a>

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## 그래프 추가
<a id="add-a-graph" class="xliff"></a>

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## 샘플 응용 프로그램 복제
<a id="clone-the-sample-application" class="xliff"></a>

이제 GitHub에서 Graph API(미리 보기) 앱을 복제하고 연결 문자열을 설정한 다음 실행해 보겠습니다. 프로그래밍 방식으로 데이터를 사용하여 얼마나 쉽게 작업할 수 있는지 알게 될 것입니다. 

1. git bash와 같은 git 터미널 창을 열고 `cd`를 수행하여 작업 디렉터리로 이동합니다.  

2. 다음 명령을 실행하여 샘플 리포지토리를 복제합니다. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-java-getting-started.git
    ```

## 코드 검토
<a id="review-the-code" class="xliff"></a>

앱에서 어떤 상황이 발생하고 있는지 빠르게 살펴보겠습니다. `Program.java` 파일을 열고 이러한 코드 줄을 찾습니다. 

* Gremlin `Client`는 `src/remote.yaml`의 구성에서 초기화됩니다.

    ```java
    Cluster cluster = Cluster.build(new File("src/remote.yaml")).create();
    
    Client client = cluster.connect();
    ```

* `client.submit` 메서드를 사용하여 일련의 Gremlin 단계를 실행합니다.

    ```java
    ResultSet results = client.submit("g.V()");

    CompletableFuture<List<Result>> completableFutureResults = results.all();
    List<Result> resultList = completableFutureResults.get();

    for (Result result : resultList) {
        System.out.println(result.toString());
    }
    ```
## 연결 문자열 업데이트
<a id="update-your-connection-string" class="xliff"></a>

이제 Azure Portal로 다시 이동하여 연결 문자열 정보를 가져와서 앱에 복사합니다.

1. [Azure Portal](http://portal.azure.com/)의 Azure Cosmos DB 계정에서 왼쪽 탐색 영역의 **키**를 클릭한 다음 **읽기-쓰기 키**를 클릭합니다. 다음 단계에서 화면 오른쪽의 복사 단추를 사용하여 URI 및 기본 키를 `Program.java` 파일에 복사하게 됩니다.

    ![Azure Portal에서 선택 키 보기 및 복사, 키 블레이드](./media/create-graph-java/keys.png)

2. `src/remote.yaml` 파일을 엽니다. 

3. `src/remote.yaml` 파일에서 *호스트*, *포트*, *사용자 이름*, *암호*, *connectionPool* 및 *serializer* 구성을 입력합니다.

    설정|제안 값|설명
    ---|---|---
    호스트|***.graphs.azure.com|그래프 서비스 URI는 Azure Portal에서 검색할 수 있습니다.
    포트|443|443으로 설정
    사용자 이름|*사용자 이름*|리소스 양식은 `/dbs/<db>/colls/<coll>`입니다.
    암호|*기본 마스터 키*|Azure Cosmos DB에 대한 기본 마스터 키
    ConnectionPool|{enableSsl: true}|SSL에 대한 연결 풀 설정
    serializer|{ className:org.apache.tinkerpop.gremlin.<br>driver.ser.GraphSONMessageSerializerV1d0,<br> config: { serializeResultToString: true }}|이 값으로 설정

## 콘솔 앱 실행
<a id="run-the-console-app" class="xliff"></a>

1. 터미널에서 `mvn package`를 실행하여 필요한 Java 패키지를 설치합니다.

2. 터미널에서 `mvn exec:java -D exec.mainClass=GetStarted.Program`을 실행하여 Java 응용 프로그램을 시작합니다.

이제 데이터 탐색기로 돌아가서 이 새 데이터를 쿼리 및 수정하고 작업에 사용할 수 있습니다. 

## 데이터 탐색기를 사용하여 찾아보기
<a id="browse-using-the-data-explorer" class="xliff"></a>

이제 Azure Portal에서 데이터 탐색기로 돌아가고 새 그래프 데이터를 찾아 쿼리합니다.

* 데이터 탐색기에서 새 데이터베이스가 컬렉션 창에 나타납니다. **graphdb**, **graphcoll**를 확장하고 **그래프**를 클릭합니다.

    샘플 앱에 의해 생성된 데이터는 그래프 창에 표시됩니다.

## Azure Portal에서 SLA 검토
<a id="review-slas-in-the-azure-portal" class="xliff"></a>

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## 리소스 정리
<a id="clean-up-resources" class="xliff"></a>

이 앱을 계속 사용하지 않으려면 Azure Portal에서 다음 단계에 따라 이 빠른 시작에서 만든 리소스를 모두 삭제합니다. 

1. Azure Portal의 왼쪽 메뉴에서 **리소스 그룹**을 클릭한 다음 만든 리소스의 이름을 클릭합니다. 
2. 리소스 그룹 페이지에서 **삭제**를 클릭하고 텍스트 상자에서 삭제할 리소스의 이름을 입력한 다음 **삭제**를 클릭합니다.

## 다음 단계
<a id="next-steps" class="xliff"></a>

이 빠른 시작에서, Azure Cosmos DB 계정을 만들고, 데이터 탐색기를 사용하여 그래프를 만들고, 앱을 실행하는 방법을 알아보았습니다. 이제 Gremlin을 사용하여 더 복잡한 쿼리를 작성하고 강력한 그래프 순회 논리를 구현할 수 있습니다. 

> [!div class="nextstepaction"]
> [Gremlin을 사용하여 쿼리](tutorial-query-graph.md)


