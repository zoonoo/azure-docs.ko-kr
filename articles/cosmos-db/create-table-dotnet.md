---
title: "테이블 API를 사용하여 Azure Cosmos DB .NET 응용 프로그램 빌드 | Microsoft Docs"
description: ".NET을 사용하여 Azure Cosmos DB의 Table API 시작"
services: cosmosdb
documentationcenter: 
author: arramac
manager: jhubbard
editor: 
ms.assetid: 66327041-4d5e-4ce6-a394-fee107c18e59
ms.service: cosmosdb
ms.custom: quick start connect
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 05/10/2017
ms.author: arramac
ms.translationtype: Human Translation
ms.sourcegitcommit: 44eac1ae8676912bc0eb461e7e38569432ad3393
ms.openlocfilehash: 9bbf188b0080b8b1cf71423023645f54f1f823e5
ms.contentlocale: ko-kr
ms.lasthandoff: 05/17/2017


---
# <a name="azure-cosmos-db-build-a-net-application-using-the-table-api"></a>Azure Cosmos DB: 테이블 API를 사용하여 .NET 응용 프로그램 빌드

Azure Cosmos DB는 전 세계에 배포된 Microsoft의 다중 모델 데이터베이스 서비스입니다. Azure Cosmos DB의 핵심인 전역 배포 및 수평적 크기 조정 기능의 이점을 활용하여 문서, 키/값 및 그래프 데이터베이스를 빠르게 만들고 쿼리할 수 있습니다. 

이 빠른 시작에서는 Azure Portal을 사용하여 Azure Cosmos DB 계정 및 해당 계정 내에서 테이블을 만드는 방법을 보여 줍니다. 그런 다음 엔터티를 삽입, 업데이트 및 삭제하고 일부 쿼리를 실행하는 코드를 작성합니다. NuGet에서 공용 [Azure Storage SDK](https://www.nuget.org/packages/WindowsAzure.Storage)와 동일한 클래스와 메서드 서명이 있는 [Azure Storage 미리 보기 SDK](https://aka.ms/premiumtablenuget)를 다운로드할 수 있지만, [Table API](table-introduction.md)(미리 보기)를 사용하여 Azure Cosmos DB 계정에 연결하는 기능도 있습니다 . 

## <a name="prerequisites"></a>필수 조건

Visual Studio 2017이 아직 설치되지 않은 경우 **체험판** [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/)을 다운로드하고 사용할 수 있습니다. Visual Studio를 설정하는 동안 **Azure 개발**을 사용할 수 있는지 확인합니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>데이터베이스 계정 만들기

[!INCLUDE [cosmosdb-create-dbaccount-table](../../includes/cosmosdb-create-dbaccount-table.md)]

## <a name="add-a-table"></a>테이블 추가

[!INCLUDE [cosmosdb-create-table](../../includes/cosmosdb-create-table.md)]

## <a name="add-sample-data"></a>샘플 데이터 추가

이제 데이터 탐색기를 사용하여 새 테이블에 데이터를 추가할 수 있습니다.

1. 데이터 탐색기에서 **sample-database**, **sample-table**을 확장하고 **엔터티**를 클릭한 다음 **엔터티 추가**를 클릭합니다.
2. 이제 PartitionKey 값 상자 및 RowKey 값 상자에 데이터를 추가하고 **엔터티 추가**를 클릭합니다.

   ![Azure Portal의 데이터 탐색기에서 새 문서 만들기](./media/create-table-dotnet/azure-cosmosdb-data-explorer-new-document.png)
  
    이제 테이블에 더 많은 엔터티를 추가하고 엔터티를 편집하거나 데이터 탐색기에서 데이터를 쿼리할 수 있습니다. 데이터 탐색기에서는 처리량을 확장하고 테이블에 저장된 프로시저, 사용자 정의 함수 및 트리거를 추가할 수 있습니다.

## <a name="clone-the-sample-application"></a>샘플 응용 프로그램 복제

이제 GitHub에서 DocumentDB API 앱을 복제하고 연결 문자열을 설정한 후 실행해 보도록 하겠습니다. 프로그래밍 방식으로 데이터를 사용하여 얼마나 쉽게 작업할 수 있는지 알게 될 것입니다. 

1. git bash와 같은 git 터미널 창을 열고 `cd`를 수행하여 작업 디렉터리로 이동합니다.  

2. 다음 명령을 실행하여 샘플 리포지토리를 복제합니다. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-table-dotnet-getting-started.git
    ```

3. 그런 다음 Visual Studio에서 솔루션을 엽니다. 

## <a name="review-the-code"></a>코드 검토

앱에서 어떤 상황이 발생하고 있는지 빠르게 살펴보겠습니다. Program.cs 파일을 열어 보면 이러한 코드 줄에서 Azure Cosmos DB 리소스를 만드는 것을 알 수 있습니다. 

* CloudTableClient가 초기화됩니다.

    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString); 
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

* 새 테이블이 존재하지 않는 경우 만들어집니다.

    ```csharp
    CloudTable table = tableClient.GetTableReference("people");
    table.CreateIfNotExists();
    ```

* 새 테이블 컨테이너가 만들어집니다. 이 코드는 일반적인 Azure Table Storage SDK와 매우 유사합니다. 

    ```csharp
    CustomerEntity item = new CustomerEntity()
                {
                    PartitionKey = Guid.NewGuid().ToString(),
                    RowKey = Guid.NewGuid().ToString(),
                    Email = $"{GetRandomString(6)}@contoso.com",
                    PhoneNumber = "425-555-0102",
                    Bio = GetRandomString(1000)
                };
    ```

## <a name="update-your-connection-string"></a>연결 문자열 업데이트

이제 Azure Portal로 다시 이동하여 연결 문자열 정보를 가져와서 앱에 복사합니다.

1. [Azure Portal](http://portal.azure.com/)의 Azure Cosmos DB 계정에서 왼쪽 탐색 영역의 **키**를 클릭한 다음 **읽기-쓰기 키**를 클릭합니다. 다음 단계에서 화면 오른쪽의 복사 단추를 사용하여 URI 및 기본 키를 app.config 파일에 복사하게 됩니다.

    ![Azure Portal에서 선택키 보기 및 복사, 키 블레이드](./media/create-documentdb-dotnet-core/keys.png)

2. Visual Studio에서 app.config 파일을 엽니다. 

3. 포털에서 Azure Cosmos DB 계정 이름을 복사하고 app.config의 PremiumStorageConnection 문자열 값에서 AccountName의 값을 확인합니다. 위의 스크린샷에서에서 계정 이름은 cosmos-db-quickstart입니다. 계정 이름은 포털의 상단에 표시됩니다.

    `<add key="PremiumStorageConnectionString" 
        value="DefaultEndpointsProtocol=https;AccountName=MYSTORAGEACCOUNT;AccountKey=AUTHKEY;TableEndpoint=https://COSMOSDB.documents.azure.com" />`

4. 그 다음, 포털에서 사용자의 기본 키 값을 복사한 후 PremiumStorageConnectionString에서 AccountKey 값으로 만듭니다. 

    `AccountKey=AUTHKEY`

5. 마지막으로, 복사 단추를 사용하여 포털의 키 페이지에서 URI 값을 복사하고 PremiumStorageConnectionString의 TableEndpoint 값을 확인합니다.

    `TableEndpoint=https://COSMOSDB.documents.azure.com`

    StandardStorageConnectionString은 그대로 둘 수 있습니다.

이제 Azure Cosmos DB와 통신하는 데 필요한 모든 정보로 앱이 업데이트되었습니다. 

## <a name="run-the-web-app"></a>웹앱 실행

1. Visual Studio의 **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **NuGet 패키지 관리**를 클릭합니다. 

2. NuGet **찾아보기** 상자에서 *WindowsAzure.Storage*를 입력하고 **시험판 포함** 상자를 확인합니다. 

3. 결과에서 **WindowsAzure.Storage** 라이브러리를 설치합니다. 그러면 Azure Cosmos DB 테이블 API 미리 보기 패키지 뿐만 아니라 모든 종속성도 설치됩니다.

4. CTRL+F5를 눌러 응용 프로그램을 실행합니다.

    테이블에 추가된 데이터가 콘솔 창에 표시됩니다. 스크립트가 완료되면 콘솔 창을 닫습니다. 

이제 데이터 탐색기로 돌아가서 이 새 데이터를 쿼리 및 수정하고 작업에 사용할 수 있습니다. 

## <a name="review-slas-in-the-azure-portal"></a>Azure Portal에서 SLA 검토

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmosdb-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>리소스 정리

이 앱을 계속 사용하지 않으려면 Azure Portal에서 다음 단계에 따라 이 빠른 시작에서 만든 리소스를 모두 삭제합니다. 

1. Azure Portal의 왼쪽 메뉴에서 **리소스 그룹**을 클릭한 다음 만든 리소스의 이름을 클릭합니다. 
2. 리소스 그룹 페이지에서 **삭제**를 클릭하고 텍스트 상자에서 삭제할 리소스의 이름을 입력한 다음 **삭제**를 클릭합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서, Azure Cosmos DB 계정을 만들고, 데이터 탐색기를 사용하여 테이블을 만들고, 앱을 실행하는 방법을 알아보았습니다.  이제 테이블 API를 사용하여 데이터를 쿼리할 수 있습니다.  

> [!div class="nextstepaction"]
> [테이블 API를 사용하여 쿼리](tutorial-query-table.md)


