---
title: Azure Cosmos DB를 사용하는 ASP.NET Core MVC 웹앱 자습서
description: Azure Cosmos DB를 사용하여 MVC 웹 애플리케이션을 만드는 ASP.NET Core MVC 자습서입니다. JSON을 저장하고, Azure App Service - ASP NET Core MVC 단계별 자습서에서 호스팅하는 todo 앱의 데이터에 액세스합니다.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 11/05/2019
ms.author: sngun
ms.openlocfilehash: 6af5f4c3ab028f8f0c6945eba86ec79dd6027680
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77587467"
---
# <a name="tutorial-develop-an-aspnet-core-mvc-web-application-with-azure-cosmos-db-by-using-net-sdk"></a>자습서: .NET SDK를 사용하여 Azure Cosmos DB를 통해 ASP.NET Core MVC 웹 애플리케이션 개발

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.JS](sql-api-nodejs-application.md)
> * [Python](sql-api-python-application.md)
> * [Xamarin](mobile-apps-with-xamarin.md)

이 자습서에서는 Azure Cosmos DB를 사용하여 Azure에서 호스트하는 ASP.NET MVC 애플리케이션의 데이터를 저장하고 액세스하는 방법을 보여줍니다. 이 자습서에서는 .NET SDK V3을 사용합니다. 다음 이미지에서는 이 문서의 샘플을 사용하여 빌드할 웹 페이지를 보여 줍니다.

![이 자습서에서 만든 할 일 모음 MVC 웹 애플리케이션의 스크린샷 - ASP NET Core MVC 단계별 자습서](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-image01.png)

자습서를 완료할 시간이 없는 경우 [GitHub][GitHub]에서 전체 샘플 프로젝트를 다운로드할 수 있습니다.

이 자습서에서는 다음 내용을 다룹니다.

> [!div class="checklist"]
>
> * Azure Cosmos 계정 만들기
> * ASP.NET Core MVC 앱 만들기
> * 앱을 Azure Cosmos DB에 연결
> * 데이터에 대한 CRUD(만들기, 읽기, 업데이트 및 삭제) 작업 수행

> [!TIP]
> 이 자습서에서는 이전에 ASP.NET Core MVC 및 Azure App Service를 사용한 경험이 있다고 가정합니다. ASP.NET Core 또는 [필수 조건 도구](#prerequisites)를 처음 사용하는 경우 [GitHub][GitHub]에서 샘플 프로젝트 전체를 다운로드하고 필요한 NuGet 패키지를 추가하여 실행하는 것이 좋습니다. 프로젝트를 빌드하고 나면 이 문서를 검토하여 프로젝트의 컨텍스트에서 코드에 대한 인사이트를 얻을 수 있습니다.

## <a name="prerequisites"></a>필수 조건

이 문서의 지침을 따르기 전에 다음 리소스가 있는지 확인하세요.

* 활성 Azure 계정. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* Visual Studio 2019. [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]  

이 문서의 스크린샷은 모두 Microsoft Visual Studio Community 2019에서 가져온 것입니다. 다른 버전을 사용하는 경우 화면과 옵션이 완전히 일치하지 않을 수 있습니다. 필수 조건이 충족되면 솔루션이 작동합니다.

## <a name="create-an-azure-cosmos-account"></a>1단계: Azure Cosmos 계정 만들기

Azure Cosmos 계정을 만들어 보겠습니다. Azure Cosmos DB SQL API 계정이 이미 있거나 Azure Cosmos DB 에뮬레이터를 사용하는 경우 [2단계: 새 ASP.NET MVC 애플리케이션 만들기](#create-a-new-mvc-application)로 건너뜁니다.

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [keys](../../includes/cosmos-db-keys.md)]

다음 섹션에서는 새 ASP.NET Core MVC 애플리케이션을 만듭니다.

## <a name="create-a-new-mvc-application"></a>2단계: 새 ASP.NET Core MVC 애플리케이션 만들기

1. Visual Studio를 열고 **새 프로젝트 만들기**를 선택합니다.

1. **새 프로젝트 만들기**에서 C#용 **ASP.NET Core 웹 애플리케이션**을 찾아서 선택합니다. **다음**을 선택하여 계속합니다.

   ![새 ASP.NET Core 웹 애플리케이션 프로젝트 만들기](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-new-project-dialog.png)

1. **새 프로젝트 구성**에서 프로젝트 이름을 *todo*로 지정하고, **만들기**를 선택합니다.

1. **새 ASP.NET Core 웹 애플리케이션 만들기**에서 **웹 애플리케이션(모델-보기-컨트롤러)** 을 선택합니다. **만들기** 를 선택하여 계속합니다.

   Visual Studio에서 빈 MVC 애플리케이션을 만듭니다.

1. **디버그** > **디버깅 시작** 또는 F5 키를 선택하여 ASP.NET 애플리케이션을 로컬로 실행합니다.

## <a name="add-nuget-packages"></a>3단계: 프로젝트에 Azure Cosmos DB NuGet 패키지 추가

이제 이 솔루션에 필요한 ASP.NET Core MVC 프레임워크 코드를 대부분 갖추고 있으므로 Azure Cosmos DB에 연결하는 데 필요한 NuGet 패키지를 추가해 보겠습니다.

1. **솔루션 탐색기**에서 마우스 오른쪽 단추로 프로젝트를 클릭하고, **NuGet 패키지 관리**를 선택합니다.

1. **NuGet 패키지 관리자**에서 **Microsoft.Azure.Cosmos**를 검색하여 선택합니다. **설치**를 선택합니다.

   ![NuGet 패키지 설치](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-nuget.png)

   Visual Studio에서 Azure Cosmos DB 패키지 및 해당 종속성을 다운로드하여 설치합니다.

   또한 **패키지 관리자 콘솔**을 사용하여 NuGet 패키지를 설치할 수도 있습니다. 이렇게 하려면 **도구** > **NuGet 패키지 관리자** > **패키지 관리자 콘솔**을 차례로 선택합니다. 프롬프트에 다음 명령을 입력합니다.

   ```ps
   Install-Package Microsoft.Azure.Cosmos
   ```
  
## <a name="set-up-the-mvc-application"></a>4단계: ASP.NET Core MVC 애플리케이션 설정

이제 모델, 보기 및 컨트롤러를 이 MVC 애플리케이션에 추가해 보겠습니다.

### <a name="add-a-model"></a> 모델 추가

1. **솔루션 탐색기**에서 마우스 오른쪽 단추로 **Models** 폴더를 클릭하고, **추가** > **클래스**를 차례로 선택합니다.

1. **새 항목 추가**에서 새 클래스의 이름을 *Item.cs*로 지정하고, **추가**를 선택합니다.

1. *Item.cs* 클래스의 내용을 다음 코드로 바꿉니다.

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Models/Item.cs":::

Azure Cosmos DB는 JSON을 사용하여 데이터를 이동하고 저장합니다. `JsonProperty` 특성을 사용하여 JSON에서 개체를 직렬화 및 역직렬화하는 방법을 제어할 수 있습니다. `Item` 클래스는 `JsonProperty` 특성을 보여 줍니다. 이 코드는 JSON으로 이동하는 속성 이름의 형식을 제어합니다. `Completed` .NET 속성의 이름도 변경합니다.

### <a name="add-views"></a>뷰 추가

다음으로, 아래의 세 가지 보기를 추가해 보겠습니다.

* 목록 항목 보기 추가
* 새 항목 보기 추가
* 편집 항목 보기 추가

#### <a name="AddItemIndexView"></a>목록 항목 뷰 추가

1. **솔루션 탐색기**에서 마우스 오른쪽 단추로 **Views** 폴더를 클릭하고, **추가** > **새 폴더**를 차례로 선택합니다. 폴더 이름을 *Item*으로 지정합니다.

1. 마우스 오른쪽 단추로 빈 **Item** 폴더를 클릭한 다음, **추가** > **보기**를 차례로 선택합니다.

1. **MVC 보기 추가**에서 다음 값을 제공합니다.

   * **보기 이름**에서 *인덱스*를 입력합니다.
   * **템플릿**에서 **목록**을 선택합니다.
   * **모델 클래스**에서 **항목(todo.Models)** 을 선택합니다.
   * **레이아웃 페이지 사용**을 선택하고, *~/Views/Shared/_Layout.cshtml*을 입력합니다.

   ![MVC 보기 추가 대화 상자를 보여 주는 스크린샷](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-add-mvc-view.png)

1. 이러한 값을 모두 추가한 후 **추가**를 클릭하면 Visual Studio에서 새 템플릿 뷰를 만듭니다.

완료되면 Visual Studio에서 만든 *cshtml* 파일을 엽니다. Visual Studio에서 해당 파일을 닫을 수 있습니다. 나중에 다시 살펴보겠습니다.

#### <a name="AddNewIndexView"></a>새 항목 뷰 추가

목록 항목에 대한 뷰를 만든 방법과 유사하게, 다음 단계를 사용하여 항목을 만들기 위한 새 뷰를 만듭니다.

1. **솔루션 탐색기**에서 마우스 오른쪽 단추로 **Item** 폴더를 클릭하고, **추가** > **보기**를 차례로 선택합니다.

1. **MVC 보기 추가**에서 다음과 같이 변경합니다.

   * **보기 이름**에서 *만들기*를 입력합니다.
   * **템플릿**에서 **만들기**를 선택합니다.
   * **모델 클래스**에서 **항목(todo.Models)** 을 선택합니다.
   * **레이아웃 페이지 사용**을 선택하고, *~/Views/Shared/_Layout.cshtml*을 입력합니다.
   * **추가**를 선택합니다.

#### <a name="AddEditIndexView"></a>항목 편집 뷰 추가

마지막으로, 다음 단계를 사용하여 항목을 편집하기 위한 뷰를 추가합니다.

1. **솔루션 탐색기**에서 마우스 오른쪽 단추로 **Item** 폴더를 다시 클릭하고, **추가** > **보기**를 차례로 선택합니다.

1. **MVC 보기 추가**에서 다음과 같이 변경합니다.

   * **뷰 이름** 상자에 *Edit*를 입력합니다.
   * **템플릿** 상자에서 **Edit**를 선택합니다.
   * **모델 클래스** 상자에서 **Item(todo.Models)** 을 선택합니다.
   * **레이아웃 페이지 사용**을 선택하고, *~/Views/Shared/_Layout.cshtml*을 입력합니다.
   * **추가**를 선택합니다.

이러한 단계가 완료되면 나중에 이러한 보기로 돌아갈 때 Visual Studio에서 모든 *cshtml* 문서를 닫습니다.

### <a name="add-a-controller"></a>컨트롤러 추가

1. **솔루션 탐색기**에서 마우스 오른쪽 단추로 **Controllers** 폴더를 클릭하고, **추가** > **컨트롤러**를 차례로 선택합니다.

1. **스캐폴드 추가**에서 **MVC 컨트롤러 - 비어 있음**을 선택하고, **추가**를 선택합니다.

   ![스캐폴드 추가에서 MVC 컨트롤러 - 비어 있음 선택](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-controller-add-scaffold.png)

1. 새 컨트롤러의 이름을 *ItemController*로 지정합니다.

1. *ItemController.cs*의 내용을 다음 코드로 바꿉니다.

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Controllers/ItemController.cs":::

**ValidateAntiForgeryToken** 특성은 여기서 교차 사이트 요청 위조 공격으로부터 이 애플리케이션을 보호하는 데 사용됩니다. 보기는 이 위조 방지 토큰에서도 작동해야 합니다. 자세한 내용과 예제는 [ASP.NET MVC 애플리케이션에서 CSRF(교차 사이트 요청 위조) 공격 방지][Preventing Cross-Site Request Forgery]를 참조하세요. [GitHub][GitHub] 에서 제공하는 소스 코드에는 완벽하게 구현되어 있습니다.

또한 메서드 매개 변수에 **Bind** 특성을 사용하여 과도한 게시 공격으로부터 보호할 수 있습니다. 자세한 내용은 [자습서: ASP.NET MVC에서 Entity Framework를 사용하여 CRUD 기능 구현][Basic CRUD Operations in ASP.NET MVC]을 참조하세요.

## <a name="connect-to-cosmosdb"></a>5단계: Azure Cosmos DB에 연결

이제 표준 MVC 항목을 처리했으므로 Azure Cosmos DB에 연결하고 CRUD 작업을 수행하는 코드를 추가해 보겠습니다.

### <a name="perform-crud-operations"></a>데이터에 대한 CRUD 작업 수행

먼저 Azure Cosmos DB에 연결하여 이를 사용하는 논리가 포함된 클래스를 추가합니다. 이 자습서에서는 이 논리를 `CosmosDBService` 클래스와 `ICosmosDBService` 인터페이스로 캡슐화합니다. 이 서비스는 CRUD 작업을 수행합니다. 또한 불완전한 항목 나열, 항목 만들기, 편집 및 삭제와 같은 읽기 피드 작업도 수행합니다.

1. **솔루션 탐색기**에서 마우스 오른쪽 단추로 프로젝트를 클릭하고, **추가** > **새 폴더**를 차례로 선택합니다. 폴더 이름을 *Services*로 지정합니다.

1. 마우스 오른쪽 단추로 **Services** 폴더를 클릭하고, **추가** > **클래스**를 차례로 선택합니다. 새 클래스 이름을 *CosmosDBService*로 지정하고, **추가**를 선택합니다.

1. *CosmosDBService.cs*의 내용을 다음 코드로 바꿉니다.

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Services/CosmosDbService.cs":::

1. 위의 두 단계를 반복하지만, 이번에는 *ICosmosDBService* 이름과 다음 코드를 사용합니다.

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Services/ICosmosDbService.cs":::

1. **ConfigureServices** 처리기에서 다음 줄을 추가합니다.

    ```csharp
    services.AddSingleton<ICosmosDbService>(InitializeCosmosClientInstanceAsync(Configuration.GetSection("CosmosDb")).GetAwaiter().GetResult());
    ```

    이전 단계의 코드에서는 생성자의 일부로 `CosmosClient`를 받습니다. ASP.NET Core 파이프라인에 이어 프로젝트의 *Startup.cs* 파일로 이동해야 합니다. 이 단계의 코드는 클라이언트를 구성에 따라 [ASP.NET Core의 종속성 주입](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)을 통해 주입할 단일 인스턴스로 초기화합니다.

1. 구성을 읽고 클라이언트를 초기화하는 다음 **InitializeCosmosClientInstanceAsync** 메서드를 동일한 파일 내에 추가합니다.

    :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Startup.cs" id="InitializeCosmosClientInstanceAsync":::

1. 프로젝트의 *appsettings.json* 파일에서 구성을 정의합니다. 이 파일을 열고, **CosmosDb**라는 섹션을 추가합니다.

   ```csharp
     "CosmosDb": {
        "Account": "<enter the URI from the Keys blade of the Azure Portal>",
        "Key": "<enter the PRIMARY KEY, or the SECONDARY KEY, from the Keys blade of the Azure  Portal>",
        "DatabaseName": "Tasks",
        "ContainerName": "Items"
      }
   ```

애플리케이션을 실행하면 ASP.NET Core의 파이프라인에서 **CosmosDbService**를 인스턴스화하고 단일 인스턴스를 싱글톤으로 유지 관리합니다. **ItemController**에서 클라이언트 쪽 요청을 처리할 때 이 단일 인스턴스를 받아서 CRUD 작업에 사용할 수 있습니다.

이 프로젝트를 지금 빌드하여 실행하면 이제 다음과 같이 표시됩니다.

![이 데이터베이스 자습서에서 만든 할 일 모음 웹 애플리케이션의 스크린샷](./media/sql-api-dotnet-application/build-and-run-the-project-now.png)

## <a name="run-the-application"></a>6단계: 로컬에서 애플리케이션 실행

로컬 컴퓨터에서 애플리케이션을 테스트하려면 다음 단계를 사용합니다.

1. Visual Studio에서 F5 키를 선택하여 애플리케이션을 디버그 모드에서 빌드합니다. 애플리케이션이 빌드되고 앞에서 본 것처럼 빈 그리드 페이지가 포함된 상태로 브라우저가 시작되어야 합니다.

   ![이 자습서에서 만든 할 일 목록 웹 애플리케이션의 스크린샷](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item-a.png)

1. **새로 만들기** 링크를 선택하고, 값을 **이름** 및 **설명** 필드에 추가합니다. **완료됨** 확인란을 선택하지 않은 상태로 둡니다. 이 확인란이 선택되면 앱에서 새 항목을 완료됨 상태로 추가합니다. 항목이 더 이상 초기 목록에 표시되지 않습니다.

1. **만들기**를 선택합니다. 앱에서 **인덱스** 보기로 다시 보내고, 항목이 목록에 표시됩니다. 몇 가지 항목을 **To-Do** 목록에 더 추가할 수 있습니다.

    ![인덱스 뷰의 스크린샷](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item.png)
  
1. 목록의 **항목** 옆에 있는 **편집**을 선택합니다. 앱에서 **완료됨** 플래그를 포함하여 개체의 속성을 업데이트할 수 있는 **편집** 보기를 엽니다. **완료됨**을 선택하고 **저장**을 선택하면 앱의 목록에 **항목**이 완료됨 상태로 표시됩니다.

   ![완료 상자가 선택된 인덱스 뷰의 스크린샷](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-completed-item.png)

1. [Cosmos Explorer](https://cosmos.azure.com) 또는 Azure Cosmos DB Emulator의 데이터 탐색기를 사용하여 Azure Cosmos DB 서비스에서 데이터의 상태를 확인합니다.

1. 앱이 테스트되면 Ctrl+F5를 선택하여 앱 디버깅을 중지합니다. 배포할 준비가 되었습니다!

## <a name="deploy-the-application-to-azure"></a>7단계: 애플리케이션 배포

이제 전체 애플리케이션이 Azure Cosmos DB와 올바르게 작동하므로 Azure App Service에 이 웹앱을 배포하겠습니다.  

1. 이 애플리케이션을 게시하려면 **솔루션 탐색기**에서 마우스 오른쪽 단추로 프로젝트를 클릭하고, **게시**를 선택합니다.

1. **게시 대상 선택**에서 **App Service**를 선택합니다.

1. 기존 App Service 프로필을 사용하려면 **기존 항목 선택**, **게시** 를 차례로 선택합니다.

1. **App Service**에서 **구독**을 선택합니다. **뷰** 필터를 사용하여 리소스 그룹 또는 리소스 형식별로 정렬합니다.

1. 프로필을 찾은 다음, **확인**을 선택합니다. 그런 다음, 필요한 Azure App Service를 검색하고 **확인**을 선택합니다.

   ![Visual Studio의 App Service 대화 상자](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-app-service-2019.png)

또 다른 옵션은 새 프로필을 만드는 것입니다.

1. 이전 절차에서와 마찬가지로 **솔루션 탐색기**에서 마우스 오른쪽 단추로 프로젝트를 클릭하고, **게시**를 선택합니다.
  
1. **게시 대상 선택**에서 **App Service**를 선택합니다.

1. **게시 대상 선택**에서 **새로 만들기**, **게시**를 차례로 선택합니다.

1. **App Service**에서 웹앱 이름과 적절한 구독, 리소스 그룹 및 호스팅 계획을 입력한 다음, **만들기**를 선택합니다.

   ![Visual Studio의 App Service 만들기 대화 상자](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-app-service-2019.png)

몇 초 후에 Visual Studio에서 웹 애플리케이션을 게시하고, Azure에서 실행되는 프로젝트를 볼 수 있는 브라우저가 시작됩니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 ASP.NET Core MVC 웹 애플리케이션을 빌드하는 방법에 대해 알아보았습니다. 애플리케이션에서 Azure Cosmos DB에 저장된 데이터에 액세스할 수 있습니다. 이제 다음 리소스를 사용하여 계속할 수 있습니다.

* [Azure Cosmos DB에서 분할](./partitioning-overview.md)
* [SQL 쿼리 시작](./how-to-sql-query.md)
* [실제 예제를 사용하여 Azure Cosmos DB에서 데이터를 모델링하고 분할하는 방법](./how-to-model-partition-example.md)

[Visual Studio Express]: https://www.visualstudio.com/products/visual-studio-express-vs.aspx
[Microsoft Web Platform Installer]: https://www.microsoft.com/web/downloads/platform.aspx
[Preventing Cross-Site Request Forgery]: https://docs.microsoft.com/aspnet/web-api/overview/security/preventing-cross-site-request-forgery-csrf-attacks
[Basic CRUD Operations in ASP.NET MVC]: https://go.microsoft.com/fwlink/?LinkId=317598
[GitHub]: https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app
