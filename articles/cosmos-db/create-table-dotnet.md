---
title: '빠른 시작: Table API와 .NET - Azure Cosmos DB'
description: 이 빠른 시작은 Azure Portal 및 .NET과 함께 Azure Cosmos DB Table API를 사용하여 애플리케이션을 만드는 방법을 보여줍니다.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 05/21/2019
ms.author: sngun
ms.openlocfilehash: 629adfe558aec71e156e50c75aa0891eac5a8bcf
ms.sourcegitcommit: 59fd8dc19fab17e846db5b9e262a25e1530e96f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/21/2019
ms.locfileid: "65979046"
---
# <a name="quickstart-build-a-table-api-app-with-net-sdk-and-azure-cosmos-db"></a>빠른 시작: .NET SDK 및 Azure Cosmos DB를 사용하여 Table API 앱 빌드 

> [!div class="op_single_selector"]
> * [.NET](create-table-dotnet.md)
> * [Java](create-table-java.md)
> * [Node.JS](create-table-nodejs.md)
> * [Python](create-table-python.md)
>  

이 빠른 시작에서는 GitHub에서 예제를 복제하여 .NET과 Azure Cosmos DB [Table API](table-introduction.md)를 통해 앱을 빌드하는 방법을 보여 줍니다. 또한 Azure Cosmos DB 계정을 만드는 방법 및 데이터 탐색기를 사용하여 웹 기반 Azure Portal에 테이블과 엔터티를 만드는 방법도 보여줍니다.

## <a name="prerequisites"></a>필수 조건

Visual Studio 2019가 아직 설치되지 않은 경우 **체험판** [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/)을 다운로드하고 사용할 수 있습니다. Visual Studio를 설치하는 동안 **Azure 개발**을 사용하도록 설정합니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>데이터베이스 계정 만들기

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="add-a-table"></a>테이블 추가

[!INCLUDE [cosmos-db-create-table](../../includes/cosmos-db-create-table.md)]

## <a name="add-sample-data"></a>샘플 데이터 추가

[!INCLUDE [cosmos-db-create-table-add-sample-data](../../includes/cosmos-db-create-table-add-sample-data.md)]

## <a name="clone-the-sample-application"></a>샘플 응용 프로그램 복제

이제 GitHub에서 Table 앱을 복제하고 연결 문자열을 설정한 다음 실행해 보겠습니다. 프로그래밍 방식으로 데이터를 사용하여 얼마나 쉽게 작업할 수 있는지 알게 될 것입니다. 

1. 명령 프롬프트를 git-samples라는 새 폴더를 만든 다음 명령 프롬프트를 닫습니다.

   ```bash
   md "C:\git-samples"
   ```

2. Git Bash와 같은 Git 터미널 창을 열고, `cd` 명령을 사용하여 샘플 앱을 설치할 새 폴더로 변경합니다.

   ```bash
   cd "C:\git-samples"
   ```

3. 다음 명령을 실행하여 샘플 리포지토리를 복제합니다. 이 명령은 컴퓨터에서 샘플 앱의 복사본을 만듭니다.

   ```bash
   git clone https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started.git
   ```

## <a name="open-the-sample-application-in-visual-studio"></a>Visual Studio에서 샘플 응용 프로그램 열기

1. Visual Studio의 **파일** 메뉴에서 **열기**를 선택한 다음 **프로젝트/솔루션**을 선택합니다. 

   ![솔루션 열기](media/create-table-dotnet/azure-cosmosdb-open-solution.png) 

2. 샘플 애플리케이션을 복제한 폴더로 이동한 다음, TableStorage.sln 파일을 엽니다.

## <a name="update-your-connection-string"></a>연결 문자열 업데이트

이제 Azure Portal로 다시 이동하여 연결 문자열 정보를 가져와서 앱에 복사합니다. 이를 통해 앱이 호스팅된 데이터베이스와 통신할 수 있게 됩니다. 

1. [Azure Portal](https://portal.azure.com/)에서 **연결 문자열**을 클릭합니다. 창의 오른쪽에서 복사 단추를 사용하여 **기본 연결 문자열**을 복사합니다.

   ![연결 문자열 창에서 기본 연결 문자열 보기 및 복사](./media/create-table-dotnet/connection-string.png)

2. Visual Studio에서 **Settings.json** 파일을 엽니다. 

3. 포털의 **기본 연결 문자열**을 StorageConnectionString 값에 붙여넣습니다. 따옴표 내에 문자열을 붙여넣습니다.

   ```csharp
   {
      "StorageConnectionString": "<Primary connection string from Azure portal>"
   }
   ```

4. CTRL+S를 눌러 **Settings.json** 파일을 저장합니다.

이제 Azure Cosmos DB와 통신하는 데 필요한 모든 정보로 앱이 업데이트되었습니다. 

## <a name="build-and-deploy-the-app"></a>앱 빌드 및 배포

1. Visual Studio의 **솔루션 탐색기**에서 **CosmosTableSamples** 프로젝트를 마우스 오른쪽 단추로 클릭한 후 **NuGet 패키지 관리**를 클릭합니다. 

   ![NuGet 패키지 관리](media/create-table-dotnet/azure-cosmosdb-manage-nuget.png)

2. NuGet **찾아보기** 상자에 Microsoft.Azure.Cosmos.Table을 입력합니다. 그러면 Cosmos DB Table API 클라이언트 라이브러리를 찾을 수 있습니다. 이 라이브러리는 현재 .NET Framework 및 .NET Standard에만 제공됩니다. 
   
   ![NuGet 찾아보기 탭](media/create-table-dotnet/azure-cosmosdb-nuget-browse.png)

3. **설치**를 클릭하여 **Microsoft.Azure.Cosmos.Table** 라이브러리를 설치합니다. 그러면 Azure Cosmos DB Table API 패키지 및 모든 종속성이 설치됩니다.

4. 전체 앱을 실행하면 샘플 데이터가 테이블 엔터티에 삽입되고 끝에서 삭제되므로 전체 샘플을 실행할 경우 삽입된 어떠한 데이터도 보이지 않습니다. 그러나 일부 중단점을 삽입하여 데이터를 볼 수 있습니다. BasicSamples.cs 파일을 열고 52줄을 마우스 오른쪽 단추로 클릭하고 **중단점**을 선택한 다음, **중단점 삽입**을 선택합니다. 55줄에 다른 중단점을 삽입합니다.

   ![중단점 추가](media/create-table-dotnet/azure-cosmosdb-breakpoint.png) 

5. F5 키를 눌러 애플리케이션을 실행합니다. 콘솔 창에서 Azure Cosmos DB의 새 테이블 데이터베이스에 대한 이름(이 경우 demoa13b1)이 표시됩니다. 
    
   ![콘솔 출력](media/create-table-dotnet/azure-cosmosdb-console.png)

   첫 번째 중단점에 도달하면 Azure Portal의 데이터 탐색기로 돌아갑니다. **새로 고침** 단추를 클릭하고 demo* 테이블을 펼친 다음 **엔터티**를 클릭합니다. 오른쪽의 **엔터티** 탭에서 Walter Harp에 추가된 새 엔터티가 표시됩니다. 새 엔터티에 대한 전화 번호는 425-555-0101입니다.

   ![새 엔터티](media/create-table-dotnet/azure-cosmosdb-entity.png)
    
   프로젝트 실행 시 Settings.json 파일을 찾을 수 없다는 오류가 표시되는 경우 다음 XML 항목을 프로젝트 설정에 추가하여 오류를 해결할 수 있습니다. CosmosTableSamples를 마우스 오른쪽 단추로 클릭하고 CosmosTableSamples.csproj 편집을 선택한 후 다음 itemGroup을 추가합니다. 

   ```csharp
     <ItemGroup>
       <None Update="Settings.json">
         <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
       </None>
     </ItemGroup>
   ```

6. [데이터 탐색기]에서 **엔터티** 탭을 닫습니다.
    
7. F5 키를 눌러 다음 중단점까지 앱을 실행합니다. 

    중단점에 도달하면 Azure Portal로 다시 전환하고 **엔터티**를 다시 클릭하여 **엔터티** 탭을 엽니다. 전화 번호가 425-555-0105로 업데이트되었습니다.

8. F5 키를 눌러 앱을 실행합니다. 
 
   앱은 Table API에서 현재 지원하지 않는 고급 샘플 앱에 사용할 엔터티를 추가합니다. 그런 다음 샘플 앱에서 만든 테이블을 삭제합니다.

9. 콘솔 창에서 Enter 키를 눌러 앱 실행을 종료합니다. 
  

## <a name="review-slas-in-the-azure-portal"></a>Azure Portal에서 SLA 검토

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서, Azure Cosmos DB 계정을 만들고, 데이터 탐색기를 사용하여 테이블을 만들고, 앱을 실행하는 방법을 알아보았습니다.  이제 테이블 API를 사용하여 데이터를 쿼리할 수 있습니다.  

> [!div class="nextstepaction"]
> [Table API로 테이블 데이터 가져오기](table-import.md)

