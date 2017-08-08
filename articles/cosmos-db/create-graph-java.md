---
title: "Graph API를 사용하여 Azure Cosmos DB Java 응용 프로그램 빌드 | Microsoft Docs"
description: "Gremlin을 사용하여 Azure Cosmos DB에서 그래프 데이터에 연결 및 쿼리하는 데 사용할 수 있는 Java 코드 샘플을 제시합니다."
services: cosmos-db
documentationcenter: 
author: dennyglee
manager: jhubbard
editor: 
ms.assetid: daacbabf-1bb5-497f-92db-079910703046
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 07/14/2017
ms.author: denlee
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 8eac406c6ef96d7ae8dd5f4931c7d16edb723be8
ms.contentlocale: ko-kr
ms.lasthandoff: 07/21/2017

---
# <a name="azure-cosmos-db-build-a-java-application-using-the-graph-api"></a>Azure Cosmos DB: Graph API를 사용하여 Java 응용 프로그램 빌드

Azure Cosmos DB는 전 세계에 배포된 Microsoft의 다중 모델 데이터베이스 서비스입니다. Azure Cosmos DB의 핵심인 전역 배포 및 수평적 크기 조정 기능의 이점을 활용하여 문서, 키/값 및 그래프 데이터베이스를 빠르게 만들고 쿼리할 수 있습니다. 

이 빠른 시작에서는 Azure Portal을 사용하여 Graph API용 Azure Cosmos DB 계정(미리 보기), 데이터베이스 및 그래프를 만드는 방법을 보여줍니다. 그런 다음 OSS [Gremlin Java](https://mvnrepository.com/artifact/org.apache.tinkerpop/gremlin-driver) 드라이버를 사용하여 콘솔 앱을 빌드 및 실행합니다.  

## <a name="prerequisites"></a>필수 조건

* 이 샘플을 실행하기 전에 다음 필수 조건이 있어야 합니다.
   * JDK 1.7+(JDK가 없는 경우 `apt-get install default-jdk` 실행) 및 `JAVA_HOME`과 같은 환경 변수 설정
   * Maven(Maven이 없는 경우 `apt-get install maven` 실행)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>데이터베이스 계정 만들기

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>그래프 추가

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a name="clone-the-sample-application"></a>샘플 응용 프로그램 복제

이제 GitHub에서 Graph API(미리 보기) 앱을 복제하고 연결 문자열을 설정한 다음 실행해 보겠습니다. 프로그래밍 방식으로 데이터를 사용하여 얼마나 쉽게 작업할 수 있는지 알게 될 것입니다. 

1. git bash와 같은 git 터미널 창을 열고 `cd`를 수행하여 작업 디렉터리로 이동합니다.  

2. 다음 명령을 실행하여 샘플 리포지토리를 복제합니다. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-java-getting-started.git
    ```

## <a name="review-the-code"></a>코드 검토

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
## <a name="update-your-connection-string"></a>연결 문자열 업데이트

1. src/remote.yaml 파일을 엽니다. 

3. src/remote.yaml 파일에서 *호스트*, *포트*, *사용자 이름*, *암호*, *connectionPool* 및 *직렬 변환기* 구성을 입력합니다.

    설정|제안 값|설명
    ---|---|---
    호스트|[***.graphs.azure.com]|아래 스크린샷을 참조하세요. 후행 :443/를 제거하고 대괄호로 묶은 Azure Portal의 개요 페이지에서 Gremlin URI 값입니다.<br><br>https://를 제거하고 문서를 그래프로 변경하고 후행 :443/를 제거하면 URI 값을 사용하여 키 탭에서 이 값을 검색할 수도 있습니다.
    포트|443|443으로 설정합니다.
    사용자 이름|*사용자 이름*|`/dbs/<db>/colls/<coll>` 양식의 리소스에서 `<db>`은 데이터베이스 이름이고 `<coll>`은 컬렉션 이름입니다.
    암호|*기본 마스터 키*|아래에서 두 번째 스크린샷을 참조하세요. 기본 키 상자에 있는 Azure Portal의 키 페이지에서 검색할 수 있는 기본 키입니다. 상자의 왼쪽에서 복사 단추를 사용하여 값을 복사합니다.
    ConnectionPool|{enableSsl: true}|SSL에 대한 연결 풀 설정
    serializer|{ className: org.apache.tinkerpop.gremlin.<br>driver.ser.GraphSONMessageSerializerV1d0,<br> config: { serializeResultToString: true }}|이 값으로 설정하고 값에 붙여 넣을 때 `\n` 줄 바꿈을 삭제합니다.

    호스트 값의 경우 **개요** 페이지의 **Gremlin URI** 값을 복사합니다. ![Azure Portal의 개요 페이지에서 Gremlin URI 값 보기 및 복사](./media/create-graph-java/gremlin-uri.png)

    암호 값의 경우 **키** 페이지의 **기본 키**를 복사합니다. ![Azure Portal의 키 페이지에서 기본 키 보기 및 복사](./media/create-graph-java/keys.png)

## <a name="run-the-console-app"></a>콘솔 앱 실행

1. 터미널에서 `mvn package`를 실행하여 필요한 Java 패키지를 설치합니다.

2. 터미널에서 `mvn exec:java -D exec.mainClass=GetStarted.Program`을 실행하여 Java 응용 프로그램을 시작합니다.

이제 데이터 탐색기로 돌아가서 이 새 데이터를 쿼리 및 수정하고 작업에 사용할 수 있습니다. 

## <a name="browse-using-the-data-explorer"></a>데이터 탐색기를 사용하여 찾아보기

이제 Azure Portal에서 데이터 탐색기로 돌아가고 새 그래프 데이터를 찾아 쿼리합니다.

* 데이터 탐색기에서 새 데이터베이스가 컬렉션 창에 나타납니다. **graphdb**, **graphcoll**를 확장하고 **그래프**를 클릭합니다.

    샘플 앱에 의해 생성된 데이터는 그래프 창에 표시됩니다.

## <a name="review-slas-in-the-azure-portal"></a>Azure Portal에서 SLA 검토

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>리소스 정리

이 앱을 계속 사용하지 않으려면 Azure Portal에서 다음 단계에 따라 이 빠른 시작에서 만든 리소스를 모두 삭제합니다. 

1. Azure Portal의 왼쪽 메뉴에서 **리소스 그룹**을 클릭한 다음 만든 리소스의 이름을 클릭합니다. 
2. 리소스 그룹 페이지에서 **삭제**를 클릭하고 텍스트 상자에서 삭제할 리소스의 이름을 입력한 다음 **삭제**를 클릭합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서, Azure Cosmos DB 계정을 만들고, 데이터 탐색기를 사용하여 그래프를 만들고, 앱을 실행하는 방법을 알아보았습니다. 이제 Gremlin을 사용하여 더 복잡한 쿼리를 작성하고 강력한 그래프 순회 논리를 구현할 수 있습니다. 

> [!div class="nextstepaction"]
> [Gremlin을 사용하여 쿼리](tutorial-query-graph.md)


