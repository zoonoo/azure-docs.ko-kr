---
title: "Graph API를 사용하여 Azure Cosmos DB Node.js 응용 프로그램 빌드 | Microsoft Docs"
description: "Azure Cosmos DB에 연결 및 쿼리하는 데 사용할 수 있는 Node.js 코드 샘플을 제시합니다."
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
ms.date: 05/21/2017
ms.author: arramac
ms.translationtype: Human Translation
ms.sourcegitcommit: 80be19618bd02895d953f80e5236d1a69d0811af
ms.openlocfilehash: b9e8c46ba2f029f8dae2b357f05a806d769d0920
ms.contentlocale: ko-kr
ms.lasthandoff: 06/07/2017


---
# <a name="azure-cosmos-db-build-a-nodejs-application-by-using-graph-api"></a>Azure Cosmos DB: Graph API를 사용하여 Node.js 응용 프로그램 빌드

Azure Cosmos DB는 Microsoft에서 제공하는 전 세계로 분산된 다중 모델 데이터베이스 서비스입니다. Azure Cosmos DB의 핵심인 전역 배포 및 수평적 크기 조정 기능의 이점을 활용하여 문서, 키/값 및 그래프 데이터베이스를 빠르게 만들고 쿼리할 수 있습니다. 

이 빠른 시작 문서에서는 Azure Portal을 사용하여 Graph API(미리 보기)용 Azure Cosmos DB 계정, 데이터베이스 및 그래프를 만드는 방법을 보여 줍니다. 그런 다음 [Gremlin Node.js](https://www.npmjs.com/package/gremlin-secure) 오픈 소스 드라이버를 사용하여 콘솔 앱을 빌드하고 실행합니다.  

> [!NOTE]
> `gremlin-secure` npm 모듈은 `gremlin` 모듈의 수정된 버전이며, Azure Cosmos DB와의 연결에 필요한 SSL 및 SASL을 지원합니다. 소스 코드는 [GitHub](https://github.com/CosmosDB/gremlin-javascript)에서 사용할 수 있습니다.
>

## <a name="prerequisites"></a>필수 조건

이 샘플을 실행하기 전에 다음 필수 조건이 있어야 합니다.
* [Node.js](https://nodejs.org/en/) 버전 v0.10.29 이상
* [Git](http://git-scm.com/)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>데이터베이스 계정 만들기

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>그래프 추가

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a name="clone-the-sample-application"></a>샘플 응용 프로그램 복제

이제 GitHub에서 Graph API 앱을 복제하고 연결 문자열을 설정한 다음 실행해 보겠습니다. 프로그래밍 방식으로 데이터를 사용하여 얼마나 쉽게 작업할 수 있는지 알게 될 것입니다. 

1. Git Bash와 같은 자식 터미널 창을 열고 작업 디렉터리로 변경합니다(`cd` 명령을 통해).  

2. 다음 명령을 실행하여 샘플 리포지토리를 복제합니다. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-nodejs-getting-started.git
    ```

3. Visual Studio에서 솔루션 파일을 엽니다. 

## <a name="review-the-code"></a>코드 검토

앱에서 어떤 상황이 발생하고 있는지 빠르게 살펴보겠습니다. `app.js` 파일을 열고 다음 코드 줄을 찾습니다. 

* Gremlin 클라이언트가 만들어집니다.

    ```nodejs
    const client = Gremlin.createClient(
        443, 
        config.endpoint, 
        { 
            "session": false, 
            "ssl": true, 
            "user": `/dbs/${config.database}/colls/${config.collection}`,
            "password": config.primaryKey
        });
    ```

  구성은 모두 `config.js`에 있으며 다음 섹션에서 편집할 내용입니다.

* `client.execute` 메서드를 사용하여 일련의 Gremlin 단계를 실행합니다.

    ```nodejs
    console.log('Running Count'); 
    client.execute("g.V().count()", { }, (err, results) => {
        if (err) return console.error(err);
        console.log(JSON.stringify(results));
        console.log();
    });
    ```

## <a name="update-your-connection-string"></a>연결 문자열 업데이트

이제 Azure Portal로 다시 이동하여 연결 문자열 정보를 가져오고 앱에 복사합니다.

1. [Azure Portal](http://portal.azure.com/)에 있는 Azure Cosmos DB 계정의 왼쪽 탐색 메뉴에서 **키**를 클릭한 다음 **읽기 쓰기 키**를 클릭합니다. 오른쪽의 복사 단추를 사용하여 다음 단계에서 URI와 기본 키를 `app.js` 파일로 복사합니다.

    ![Azure Portal 키 블레이드](./media/create-graph-nodejs/keys.png)

2. 포털에서 복사 단추를 사용하여 Gremlin URI 값을 복사하고 이 값을 config.js의 `config.endpoint` 키 값으로 만듭니다. Gremlin 끝점은 `mygraphdb.graphs.azure.com`과 같이(`https://mygraphdb.graphs.azure.com` 또는 `mygraphdb.graphs.azure.com:433`이 아님) 프로토콜/포트 번호가 없는 호스트 이름이어야 합니다.

    `config.endpoint = "GRAPHENDPOINT";`

3. 포털에서 기본 키 값을 복사하고, 이 값을 config.js의 config.primaryKey 값으로 만듭니다. 이제 Azure Cosmos DB와 통신하는 데 필요한 모든 정보로 앱이 업데이트되었습니다. 

    `config.primaryKey = "PRIMARYKEY";`

4. config.database 및 config.collection의 값에 데이터베이스 이름 및 그래프(컨테이너) 이름을 입력합니다. 

완성된 config.js 파일은 다음과 같은 모양입니다.

```nodejs
var config = {}

// Note that this must not have HTTPS or the port number
config.endpoint = "mygraphdb.graphs.azure.com";
config.primaryKey = "OjlhK6tjxfSXyKtrmCiM9O6gQQgu5DmgAoauzD1PdPIq1LZJmILTarHvrolyUYOB0whGQ4j21rdAFwoYep7Kkw==";
config.database = "graphdb"
config.collection = "Persons"

module.exports = config;
```

## <a name="run-the-console-app"></a>콘솔 앱 실행

1. 터미널 창을 열고 프로젝트에 포함된 package.json 파일의 설치 디렉터리로 변경합니다(`cd` 명령을 통해).  

2. `npm install`을 실행하여 필요한 npm 모듈(`gremlin-secure` 포함)을 설치합니다.

3. 터미널에서 `node app.js`을 실행하여 노드 응용 프로그램을 시작합니다.

## <a name="browse-with-data-explorer"></a>데이터 탐색기 검색

이제 Azure Portal의 [데이터 탐색기]로 다시 이동하여 새 그래프 데이터를 보고, 쿼리하고, 수정하고, 작업할 수 있습니다.

[데이터 탐색기]에서 새 데이터베이스가 **컬렉션** 창에 표시됩니다. **graphdb**, **graphcoll**를 확장하고 **그래프**를 클릭합니다.

샘플 앱에서 생성된 데이터는 **그래프** 창에 표시됩니다.

## <a name="review-slas-in-the-azure-portal"></a>Azure Portal에서 SLA 검토

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-your-resources"></a>리소스 정리

이 앱을 계속 사용하지 않으려면 다음을 수행하여 이 문서에서 만든 리소스를 모두 삭제합니다. 

1. Azure Portal의 왼쪽 탐색 메뉴에서 **리소스 그룹**을 클릭한 다음 만든 리소스의 이름을 클릭합니다. 
2. 리소스 그룹 페이지에서 **삭제**를 클릭하고, 삭제할 리소스의 이름을 입력한 다음 **삭제**를 클릭합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Cosmos DB 계정을 만들고, [데이터 탐색기]를 사용하여 그래프를 만들고, 앱을 실행하는 방법에 대해 알아보았습니다. 이제 Gremlin을 사용하여 더 복잡한 쿼리를 작성하고 강력한 그래프 순회 논리를 구현할 수 있습니다. 

> [!div class="nextstepaction"]
> [Gremlin을 사용하여 쿼리](tutorial-query-graph.md)

