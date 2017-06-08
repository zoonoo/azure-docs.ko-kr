---
title: "Azure Cosmos DB: .NET 및 MongoDB API에서 웹앱 빌드 | Microsoft Docs"
description: "Azure Cosmos DB MongoDB API에 연결 및 쿼리하는 데 사용할 수 있는 .NET 코드 샘플을 제시합니다."
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 05/10/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: b95afa4dd3cd264ea911c7aa809c7862f9576da8
ms.contentlocale: ko-kr
ms.lasthandoff: 05/31/2017


---
# <a name="azure-cosmos-db-build-a-mongodb-api-web-app-with-net-and-the-azure-portal"></a>Azure Cosmos DB: .NET 및 Azure Portal에서 MongoDB API 웹앱 빌드

Azure Cosmos DB는 전 세계에 배포된 Microsoft의 다중 모델 데이터베이스 서비스입니다. Azure Cosmos DB의 핵심인 전역 배포 및 수평적 크기 조정 기능의 이점을 활용하여 문서, 키/값 및 그래프 데이터베이스를 빠르게 만들고 쿼리할 수 있습니다. 

이 빠른 시작에서는 Azure Portal을 사용하여 Azure Cosmos DB 계정, 문서 데이터베이스 및 컬렉션을 만드는 방법을 보여 줍니다. 그런 다음, [MongoDB .NET 드라이버](https://docs.mongodb.com/ecosystem/drivers/csharp/)에서 작성한 작업 목록 웹앱을 빌드 및 배포합니다. 

## <a name="prerequisites"></a>필수 조건

Visual Studio 2017이 아직 설치되지 않은 경우 **체험판** [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/)을 다운로드하고 사용할 수 있습니다. Visual Studio를 설정하는 동안 **Azure 개발**을 사용할 수 있는지 확인합니다.
[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

<a id="create-account"></a>
## <a name="create-a-database-account"></a>데이터베이스 계정 만들기

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="clone-the-sample-application"></a>샘플 응용 프로그램 복제

이제 GitHub에서 MongoDB API 앱을 복제하고 연결 문자열을 설정한 다음 실행해 보겠습니다. 프로그래밍 방식으로 데이터를 사용하여 얼마나 쉽게 작업할 수 있는지 알게 될 것입니다. 

1. git bash와 같은 git 터미널 창을 열고 `cd`를 수행하여 작업 디렉터리로 이동합니다.  

2. 다음 명령을 실행하여 샘플 리포지토리를 복제합니다. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-getting-started.git
    ```

3. 그런 다음 Visual Studio에서 솔루션을 엽니다. 

## <a name="review-the-code"></a>코드 검토

앱에서 어떤 상황이 발생하고 있는지 빠르게 살펴보겠습니다. **DAL** 디렉터리에서 **Dal.cs** 파일을 열어 보면 이들 코드 줄이 Azure Cosmos DB 리소스를 만드는 것을 알 수 있습니다. 

* Mongo 클라이언트를 초기화합니다.

    ```cs
        MongoClientSettings settings = new MongoClientSettings();
        settings.Server = new MongoServerAddress(host, 10255);
        settings.UseSsl = true;
        settings.SslSettings = new SslSettings();
        settings.SslSettings.EnabledSslProtocols = SslProtocols.Tls12;

        MongoIdentity identity = new MongoInternalIdentity(dbName, userName);
        MongoIdentityEvidence evidence = new PasswordEvidence(password);

        settings.Credentials = new List<MongoCredential>()
        {
            new MongoCredential("SCRAM-SHA-1", identity, evidence)
        };

        MongoClient client = new MongoClient(settings);
    ```

* 데이터베이스 및 컬렉션을 검색합니다.

    ```cs
    private string dbName = "Tasks";
    private string collectionName = "TasksList";

    var database = client.GetDatabase(dbName);
    var todoTaskCollection = database.GetCollection<MyTask>(collectionName);
    ```

* 모든 문서를 검색합니다.

    ```cs
    collection.Find(new BsonDocument()).ToList();
    ```

## <a name="update-your-connection-string"></a>연결 문자열 업데이트

이제 Azure Portal로 다시 이동하여 연결 문자열 정보를 가져와서 앱에 복사합니다.

1. [Azure Portal](http://portal.azure.com/)의 Azure Cosmos DB 계정에서 왼쪽 탐색 영역의 **연결 문자열**을 클릭한 다음 **읽기-쓰기 키**를 클릭합니다. 다음 단계에서 화면의 오른쪽에 있는 복사 단추를 사용하여 사용자, 암호 및 호스트를 Dal.cs 파일에 복사합니다.

2. **DAL** 디렉터리에서 **Dal.cs** 파일을 엽니다. 

3. 포털에서 복사 단추를 사용하여 **사용자 이름** 값을 복사하고 **Dal.cs** 파일의 **사용자 이름** 값으로 만듭니다. 

4. 그 다음, 포털에서 사용자의 **호스트** 값을 복사하고 **Dal.cs** 파일의 **호스트** 값으로 만듭니다. 

5. 마지막으로, 포털에서 사용자의 **암호** 값을 복사하고 **Dal.cs** 파일의 **암호** 값으로 만듭니다. 

이제 Azure Cosmos DB와 통신하는 데 필요한 모든 정보로 앱이 업데이트되었습니다. 
    
## <a name="run-the-web-app"></a>웹앱 실행

1. Visual Studio의 **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **NuGet 패키지 관리**를 클릭합니다. 

2. NuGet **찾아보기** 상자에 *MongoDB.Driver*를 입력합니다.

3. 결과에서 **MongoDB.Driver** 라이브러리를 설치합니다. 그러면 MongoDB.Driver 패키지 뿐만 아니라 모든 종속성도 설치됩니다.

4. CTRL+F5를 눌러 응용 프로그램을 실행합니다. 앱이 브라우저에 표시됩니다. 

5. 브라우저에서 **만들기**를 클릭하고 작업 목록 앱에서 몇 가지 새 작업을 만듭니다.

## <a name="review-slas-in-the-azure-portal"></a>Azure Portal에서 SLA 검토

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>리소스 정리

이 앱을 계속 사용하지 않으려면 Azure Portal에서 다음 단계에 따라 이 빠른 시작에서 만든 리소스를 모두 삭제합니다.

1. Azure Portal의 왼쪽 메뉴에서 **리소스 그룹**을 클릭한 다음 만든 리소스의 이름을 클릭합니다. 
2. 리소스 그룹 페이지에서 **삭제**를 클릭하고 텍스트 상자에서 삭제할 리소스의 이름을 입력한 다음 **삭제**를 클릭합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서 Azure Cosmos DB 계정을 만들고, MongoDB의 API를 사용하여 웹앱을 실행하는 방법을 알아보았습니다. 이제 사용자의 Cosmos DB 계정에 추가 데이터를 가져올 수 있습니다. 

> [!div class="nextstepaction"]
> [MongoDB API용 Azure Cosmos DB로 데이터 가져오기](mongodb-migrate.md)


