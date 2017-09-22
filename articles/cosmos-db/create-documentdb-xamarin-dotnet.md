---
title: "Azure Cosmos DB: Xamarin 및 Facebook 인증으로 웹앱 작성 | Microsoft Docs"
description: "Azure Cosmos DB에 연결 및 쿼리하는 데 사용할 수 있는 .NET 코드 샘플을 제시합니다."
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
ms.topic: quickstart
ms.date: 05/10/2017
ms.author: mimig
ms.translationtype: HT
ms.sourcegitcommit: 8f9234fe1f33625685b66e1d0e0024469f54f95c
ms.openlocfilehash: ed1d30be780e0882b8e75c01bc7822c3350f3fee
ms.contentlocale: ko-kr
ms.lasthandoff: 09/20/2017

---
# <a name="azure-cosmos-db-build-a-web-app-with-net-xamarin-and-facebook-authentication"></a>Azure Cosmos DB: .NET, Xamarin 및 Facebook 인증으로 웹앱 작성

Azure Cosmos DB는 전 세계에 배포된 Microsoft의 다중 모델 데이터베이스 서비스입니다. Azure Cosmos DB의 핵심인 전역 배포 및 수평적 크기 조정 기능의 이점을 활용하여 문서, 키/값 및 그래프 데이터베이스를 빠르게 만들고 쿼리할 수 있습니다. 

이 빠른 시작에서는 Azure Portal을 사용하여 Azure Cosmos DB 계정, 문서 데이터베이스 및 컬렉션을 만드는 방법을 보여 줍니다. 그런 다음, [DocumentDB .NET API](documentdb-sdk-dotnet.md), [Xamarin](https://www.xamarin.com/) 및 Azure Cosmos DB 인증 엔진에서 작성된 할일 목록 웹앱을 빌드 및 배포합니다. 할일 목록 웹앱은 사용자가 Facebook 인증을 사용하여 로그인한 후 자신의 할일 항목을 관리할 수 있는 사용자별 데이터 패턴을 구현합니다.

## <a name="prerequisites"></a>필수 조건

Visual Studio 2017이 아직 설치되지 않은 경우 **체험판** [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/)을 다운로드하고 사용할 수 있습니다. Visual Studio를 설정하는 동안 **Azure 개발**을 사용할 수 있는지 확인합니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

## <a name="create-a-database-account"></a>데이터베이스 계정 만들기

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-collection"></a>컬렉션 추가

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

## <a name="clone-the-sample-application"></a>샘플 응용 프로그램 복제

이제 github에서 DocumentDB API 앱을 복제하고 연결 문자열을 설정한 후 실행해 보도록 하겠습니다. 프로그래밍 방식으로 데이터를 사용하여 얼마나 쉽게 작업할 수 있는지 알게 될 것입니다. 

1. git bash와 같은 git 터미널 창을 열고 `cd`를 수행하여 작업 디렉터리로 이동합니다.  

2. 다음 명령을 실행하여 샘플 리포지토리를 복제합니다. 

    ```bash
    git clone https://github.com/Azure/azure-documentdb-dotnet.git
    ```

3. 그런 다음, Visual Studio에서 samples/xamarin/UserItems/xamarin.forms 폴더의 DocumentDBTodo.sln 파일을 엽니다. 

## <a name="review-the-code"></a>코드 검토

Xamarin 폴더의 코드에는 다음이 포함되어 있습니다.

* Xamarin 앱. 이 앱은 UserItems라는 분할 컬렉션에 사용자의 할일 항목을 저장합니다.
* 리소스 토큰 broker API. 앱에 로그인한 사용자에게 Azure Cosmos DB 리소스 토큰을 중개하는 간단한 ASP.NET Web API입니다. 리소스 토큰은 로그인한 사용자의 데이터에 액세스할 수 있는 앱을 제공하는 단기 액세스 토큰입니다.

아래 다이어그램에서는 인증 및 데이터 흐름을 보여 줍니다.

* UserItems 컬렉션은 '/userid' 파티션 키를 사용하여 만듭니다. 컬렉션에 대한 파티션 키를 지정하면 사용자 및 항목 수가 증가함에 따라 Azure Cosmos DB를 무제한으로 확장할 수 있습니다.
* Xamarin 앱을 사용하면 사용자가 Facebook 자격 증명으로 로그인할 수 있습니다.
* Xamarin 앱은 Facebook 액세스 토큰을 사용하여 ResourceTokenBroker API를 인증합니다.
* 리소스 토큰 broker API는 App Service 인증 기능을 사용하여 요청을 인증하고, 인증된 사용자의 파티션 키를 공유하는 모든 문서에 대해 읽기/쓰기 액세스를 수행할 수 있는 Azure Cosmos DB 리소스 토큰을 요청합니다.
* 리소스 토큰 broker는 클라이언트 앱에 리소스 토큰을 반환합니다.
* 앱은 리소스 토큰을 사용하여 사용자의 할일 항목에 액세스합니다.

![샘플 데이터를 사용한 Todo 앱](./media/create-documentdb-xamarin-dotnet/tokenbroker.png)
    
## <a name="update-your-connection-string"></a>연결 문자열 업데이트

이제 Azure Portal로 다시 이동하여 연결 문자열 정보를 가져와서 앱에 복사합니다.

1. [Azure Portal](http://portal.azure.com/)의 Azure Cosmos DB 계정에서 왼쪽 탐색 창의 **키**를 클릭한 다음 **읽기-쓰기 키**를 클릭합니다. 다음 단계에서 화면 오른쪽의 복사 단추를 사용하여 URI 및 기본 키를 Web.config 파일에 복사하게 됩니다.

    ![Azure Portal에서 선택 키 보기 및 복사, 키 블레이드](./media/create-documentdb-xamarin-dotnet/keys.png)

2. Visual Studio 2017에서 azure-documentdb-dotnet/samples/xamarin/UserItems/ResourceTokenBroker/ResourceTokenBroker 폴더의 Web.config 파일을 엽니다. 

3. 포털에서 URI 값을 복사(복사 단추 사용)한 후 이 값을 Web.config에서 accountUrl 값으로 만듭니다. 

    `<add key="accountUrl" value="{Azure Cosmos DB account URL}"/>`

4. 그 다음, 포털에서 사용자의 기본 키 값을 복사한 후 Web.config에서 accountKey 값으로 만듭니다. 

    `<add key="accountKey" value="{Azure Cosmos DB secret}"/>`

이제 Azure Cosmos DB와 통신하는 데 필요한 모든 정보로 앱이 업데이트되었습니다. 

## <a name="build-and-deploy-the-web-app"></a>웹앱 빌드 및 배포

1. Azure Portal에서 리소스 토큰 broker API를 호스트하는 App Service 웹 사이트를 만듭니다.
2. Azure Portal에서 리소스 토큰 broker API 웹 사이트의 앱 설정 블레이드를 엽니다. 다음 앱 설정을 입력합니다.

    * accountUrl - Azure Cosmos DB 계정의 키 탭의 Azure Cosmos DB 계정 URL입니다.
    * accountKey - Azure Cosmos DB 계정의 키 탭의 Azure Cosmos DB 계정 마스터 키입니다.
    * 사용자가 만든 데이터베이스와 컬렉션의 databaseId 및 collectionId

3. 생성된 웹 사이트에 ResourceTokenBroker 솔루션을 게시합니다.

4. Xamarin 프로젝트를 열고 TodoItemManager.cs로 이동합니다. 리소스 토큰 broker 웹 사이트에 대한 기본 https URL로 resourceTokenBrokerURL을 입력하고 accountURL, collectionId, databaseId에 대한 값도 입력합니다.

5. Facebook 인증을 설정하고 ResourceTokenBroker 웹 사이트를 구성하려면 [Facebook 로그인을 사용하도록 App Service 응용 프로그램을 구성하는 방법](../app-service/app-service-mobile-how-to-configure-facebook-authentication.md) 자습서를 수행합니다.

    Xamarin 앱을 실행합니다.

## <a name="review-slas-in-the-azure-portal"></a>Azure Portal에서 SLA 검토

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>리소스 정리

이 앱을 계속 사용하지 않으려면 Azure Portal에서 다음 단계에 따라 이 빠른 시작에서 만든 리소스를 모두 삭제합니다. 

1. Azure Portal의 왼쪽 메뉴에서 **리소스 그룹**을 클릭한 다음, 방금 만든 리소스의 이름을 클릭합니다. 
2. 리소스 그룹 페이지에서 **삭제**를 클릭하고 텍스트 상자에서 삭제할 리소스의 이름을 입력한 다음 **삭제**를 클릭합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서, Azure Cosmos DB 계정을 만들고, 데이터 탐색기를 사용하여 컬렉션을 만들고, Xamarin 앱을 작성 및 배포하는 방법을 알아보았습니다. 이제 사용자의 Cosmos DB 계정에 추가 데이터를 가져올 수 있습니다. 

> [!div class="nextstepaction"]
> [Azure Cosmos DB로 데이터 가져오기](import-data.md)

