---
title: .Net 미리 보기 SDK를 사용하여 Azure Cosmos DB로 ASP.NET MVC 웹 애플리케이션을 개발하는 자습서입니다.
description: 이 자습서에서는 Azure Cosmos DB를 사용하여 ASP .Net MVC 웹 애플리케이션을 만드는 방법을 설명합니다. Azure에서 호스팅하는 Todo 앱에서 JSON 데이터를 저장하고 액세스하게 됩니다.
author: deborahc
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 12/03/2018
ms.author: dech
ms.openlocfilehash: c0b1ed5aff7f22000a179983396239c24d21a311
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53101333"
---
# <a name="tutorial-develop-an-aspnet-mvc-web-application-with-azure-cosmos-db-by-using-net-preview-sdk"></a>자습서: .Net 미리 보기 SDK를 사용하여 Azure Cosmos DB를 통해 ASP.NET MVC 웹 애플리케이션 개발 

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [.NET 미리 보기](sql-api-dotnet-application-preview.md)
> * [Java](sql-api-java-application.md)
> * [Node.JS](sql-api-nodejs-application.md)
> * [Python](sql-api-python-application.md)
> * [Xamarin](mobile-apps-with-xamarin.md)


이 자습서에서는 Azure Cosmos DB를 사용하여 Azure에서 호스트하는 ASP.NET MVC 애플리케이션의 데이터를 저장하고 액세스하는 방법을 보여줍니다. 이 자습서에서는 현재 미리 보기로 제공되는 .Net SDK V3을 사용합니다. 다음 이미지는 이 문서의 샘플을 사용하여 빌드하게 될 웹 페이지입니다.
 
![이 자습서에서 만든 할 일 모음 MVC 웹 애플리케이션의 스크린샷 - ASP NET MVC 단계별 자습서](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-image01.png)

자습서를 완료할 시간이 없는 경우 전체 샘플 프로젝트를 [GitHub][GitHub]에서 다운로드할 수 있습니다. 

이 자습서에서는 다음 내용을 다룹니다.

> [!div class="checklist"]
> * Azure Cosmos 계정 만들기
> * ASP.NET MVC 앱 만들기
> * 앱을 Azure Cosmos DB에 연결 
> * 데이터에서 CRUD 작업 수행

> [!TIP]
> 이 자습서에서는 이전에 ASP.NET MVC 및 Azure Websites를 사용해 본 경험이 있다고 가정합니다. ASP.NET 또는 [필수 구성 요소 도구](#prerequisites)를 처음 사용하는 경우 [GitHub][GitHub]에서 전체 샘플 프로젝트를 다운로드하고 필수 NuGet 패키지를 추가하여 실행하는 것이 좋습니다. 프로젝트를 빌드하고 나면 이 문서를 검토하여 프로젝트의 컨텍스트에서 코드에 대한 인사이트를 얻을 수 있습니다.

## <a name="prerequisites"></a>필수 조건 

이 문서의 지침을 따르기 전에, 다음 리소스가 있는지 확인합니다.

* **활성 Azure 계정:** Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 을 만듭니다. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]  

* Microsoft Azure SDK for .NET for Visual Studio 2017, Visual Studio 설치 관리자를 통해 사용 가능합니다.

이 문서의 모든 스크린 샷은 Microsoft Visual Studio Community 2017을 사용하여 작성되었습니다. 시스템이 다른 버전으로 구성된 경우 화면과 옵션이 일부 달라질 수 있지만 위의 필수 구성 요소를 충족하면 이 솔루션을 사용할 수 있습니다.

## <a name="create-an-azure-cosmos-account"></a>1단계: Azure Cosmos 계정 만들기

Azure Cosmos 계정을 만들어 보겠습니다. Azure Cosmos DB SQL API 계정이 이미 있거나 이 자습서에 Azure Cosmos DB 에뮬레이터를 사용하고 있는 경우 [새 ASP.NET MVC 애플리케이션 만들기](#create-a-new-mvc-application)로 건너뛸 수 있습니다.

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [keys](../../includes/cosmos-db-keys.md)]

다음 섹션에서는 새 ASP.NET MVC 애플리케이션을 만듭니다. 

## <a name="create-a-new-mvc-application"></a>2단계: 새 ASP.NET MVC 애플리케이션 만들기

1. Visual Studio의 **파일** 메뉴에서 **새로 만들기**를 선택한 다음, **프로젝트**를 선택합니다. **새 프로젝트** 대화 상자가 나타납니다.

2. **프로젝트 형식** 창에서 **설치됨** 템플릿, **Visual C#**, **웹**을 확장한 다음, **ASP.NET 웹 애플리케이션**을 선택합니다. 

   ![새 ASP.NET 웹 애플리케이션 프로젝트 만들기](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-new-project-dialog.png)

3. **이름** 상자에 프로젝트의 이름을 입력합니다. 이 자습서에서는 "todo"라는 이름을 사용합니다. 다른 이름을 사용하도록 선택한 경우에는 이 자습서에서 Todo 네임스페이스를 지칭할 때마다 지정한 애플리케이션 이름을 사용하도록 제공된 코드 샘플을 조정합니다. 

4. **찾아보기**를 클릭하여 프로젝트를 만들 폴더로 이동한 다음, **.Net Framework 4.6.1** 이상을 선택합니다. **확인**을 선택합니다. 

5. **새 ASP.NET 웹 응용 프로그램** 대화 상자가 나타납니다. 템플릿 창에서 **MVC**를 선택합니다.

6. **확인**을 클릭하면 Visual Studio에서 빈 ASP.NET MVC 템플릿을 스캐폴딩합니다. 

7. Visual Studio에서 상용구 MVC 애플리케이션을 만들면 로컬에서 실행할 수 있는 빈 ASP.NET 애플리케이션이 만들어집니다.

## <a name="add-nuget-packages"></a>3단계: 프로젝트에 Azure Cosmos DB NuGet 패키지 추가

이제 이 솔루션에 필요한 ASP.NET MVC 프레임워크 코드가 대부분 갖춰졌습니다. 이제 연결에 필요한 NuGet 패키지를 Azure Cosmos DB에 추가하겠습니다.

1. Azure Cosmos DB .NET SDK는 패키지되어 NuGet 패키지로 배포되며, Visual Studio에서 NuGet 패키지를 다운로드하려면 **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭한 다음, **NuGet 패키지 관리**를 선택하여 Visual Studio에서 NuGet 패키지 관리자를 사용합니다.
   
   ![NuGet 패키지 관리가 강조 표시된 솔루션 탐색기 내 웹 애플리케이션 프로젝트에 대한 마우스 오른쪽 클릭 옵션의 스크린샷](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-manage-nuget.png)
   
2. **NuGet 패키지 관리** 대화 상자가 나타납니다. NuGet **찾아보기** 상자에 **Microsoft.Azure.Cosmos**를 입력합니다. 결과에서 **Microsoft.Azure.Cosmos** 3.0.0.1 미리 보기 버전을 설치합니다. 그러면 Azure Cosmos DB 패키지 및 모든 종속성(예: Newtonsoft.Json)이 다운로드되어 설치됩니다. **미리 보기** 창에서 **확인**을 선택하고 **라이선스 승인** 창에서 **동의**를 선택하여 설치를 완료합니다.
   
   또는 패키지 관리자 콘솔을 사용하여 NuGet 패키지를 설치할 수 있습니다. 이렇게 하려면 **도구** 메뉴에서 **NuGet 패키지 관리자**, **패키지 관리자 콘솔**을 차례로 선택합니다. 프롬프트에 다음 명령을 입력합니다.
   
   ```bash
   Install-Package Microsoft.Azure.Cosmos -Version 3.0.0.1-preview
   ```        

3. 패키지가 설치된 후에는 Visual Studio 솔루션에 Microsoft.Azure.Cosmos.Client 및 Newtonsoft.Json이라는 두 개의 새 라이브러리 참조가 포함되어야 합니다.
  
## <a name="set-up-the-mvc-application"></a>4단계: ASP.NET MVC 애플리케이션 설정

이제 이 MVC 애플리케이션에 모델, 뷰 및 컨트롤러를 추가합니다.

* [모델 추가](#add-a-model).
* [컨트롤러 추가](#add-a-controller).
* [뷰 추가](#add-views).

### <a name="add-a-model"></a> 모델 추가

1. **솔루션 탐색기**에서 **모델** 폴더를 마우스 오른쪽 단추로 클릭하고 **추가**를 선택한 다음, **클래스**를 선택합니다. **새 항목 추가** 대화 상자가 나타납니다.

1. 새 클래스의 이름을 **TodoItem.cs**로 지정하고 **추가**를 선택합니다. 

1. “Todoitem” 클래스의 코드를 다음 코드로 바꿉니다.

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-web-app/src/Models/TodoItem.cs)]
   
   Azure Cosmos DB에 저장된 데이터가 네트워크를 통해 전달되고 JSON으로 저장됩니다. JSON.NET에서 개체를 직렬화/역직렬화하는 방식을 제어하기 위해 방금 만든 **TodoItem** 클래스에서 본 것처럼 **JsonProperty** 특성을 사용할 수 있습니다. JSON으로 저장된 속성 이름의 형식을 제어할 수 있을 뿐만 아니라, **Description** 속성에 대해 수행했던 것처럼 .NET 속성의 이름도 다시 지정할 수 있습니다. 

### <a name="add-a-controller"></a>컨트롤러 추가

1. **솔루션 탐색기**에서 **컨트롤러** 폴더를 마우스 오른쪽 단추로 클릭하고 **추가**를 선택한 다음, **컨트롤러**를 선택합니다. **스캐폴드 추가** 대화 상자가 나타납니다.

1. **MVC 5 컨트롤러 - 비어 있음**을 선택하고 **추가**를 선택합니다.

   ![MVC 5 컨트롤러 - 비어 있음 옵션이 강조 표시된 스캐폴드 추가 대화 상자의 스크린샷](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-controller-add-scaffold.png)

1. 새 컨트롤러 이름을 **ItemController로 지정하고 해당 파일의 코드를 다음 코드로 바꿉니다.

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-web-app/src/Controllers/ItemController.cs)]

   **ValidateAntiForgeryToken** 특성은 여기서 교차 사이트 요청 위조 공격으로부터 이 애플리케이션을 보호하는 데 사용됩니다. 이 특성을 추가하는 것 외에 뷰가 이 위조 방지 토큰과 작동하도록 해야 합니다. 이 주제에 대한 자세한 내용과 이를 올바르게 구현하는 방법의 예제는 [교차 사이트 요청 위조 방지][Preventing Cross-Site Request Forgery]를 참조하세요. [GitHub][GitHub]에서 제공하는 소스 코드에는 완벽하게 구현되어 있습니다.
   
   또한 메서드 매개 변수에 **Bind** 특성을 사용하여 과도한 게시 공격으로부터 보호할 수 있습니다. 자세한 내용은 [ASP.NET MVC의 기본 CRUD 작업][Basic CRUD Operations in ASP.NET MVC]을 참조하세요.
    
### <a name="add-views"></a>뷰 추가

다음으로, 아래와 같은 세 가지 뷰를 추가하겠습니다. 

* [목록 항목 뷰 추가](#AddItemIndexView).
* [새 항목 뷰 추가](#AddNewIndexView).
* [항목 편집 뷰 추가](#AddEditIndexView).

#### <a name="AddItemIndexView"></a>목록 항목 뷰 추가

1. **솔루션 탐색기**에서 **Views** 폴더를 확장하고 앞서 **ItemController**를 추가할 때 Visual Studio에서 만들어진 빈 **Item** 폴더를 마우스 오른쪽 단추로 클릭하고 **추가**를 클릭한 다음, **뷰**를 클릭합니다.
   
   ![뷰 추가 명령이 강조 표시된 Visual Studio에서 만든 항목 폴더를 보여 주는 솔루션 탐색기의 스크린샷](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-add-view.png)

2. **뷰 추가** 대화 상자에서 다음 값을 업데이트합니다.
   
   * **뷰 이름** 상자에 ***인덱스***를 입력합니다.
   * **템플릿** 상자에서 ***목록***을 선택합니다.
   * **모델 클래스** 상자에서 ***항목(todo.Models)*** 을 선택합니다.
   * 레이아웃 페이지 상자에 ***~/Views/Shared/_Layout.cshtml***을 입력합니다.
     
   ![뷰 추가 대화 상자를 보여주는 스크린샷](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-add-view-dialog.png)

3. 이러한 값을 모두 추가한 후 **추가**를 클릭하면 Visual Studio에서 새 템플릿 뷰를 만듭니다. 완료되면 생성된 cshtml 파일이 열립니다. 나중에 돌아올 것이므로 Visual Studio에서 해당 파일을 닫아도 됩니다.

#### <a name="AddNewIndexView"></a>새 항목 뷰 추가

목록 항목에 대한 뷰를 만든 방법과 유사하게, 다음 단계를 사용하여 항목을 만들기 위한 새 뷰를 만듭니다.

1. **솔루션 탐색기**에서 **항목** 폴더를 마우스 오른쪽 단추로 클릭하고 **추가**를 선택한 다음, **뷰**를 선택합니다.

1. **뷰 추가** 대화 상자에서 다음 값을 업데이트합니다.
   
   * **뷰 이름** 상자에 ***Create***를 입력합니다.
   * **템플릿** 상자에서 ***Create***를 선택합니다.
   * **모델 클래스** 상자에서 ***항목(todo.Models)*** 을 선택합니다.
   * 레이아웃 페이지 상자에 ***~/Views/Shared/_Layout.cshtml***을 입력합니다.
   * **추가**를 선택합니다.
   
#### <a name="AddEditIndexView"></a>항목 편집 뷰 추가

마지막으로, 다음 단계를 사용하여 항목을 편집하기 위한 뷰를 추가합니다.

1. **솔루션 탐색기**에서 **항목** 폴더를 마우스 오른쪽 단추로 클릭하고 **추가**를 선택한 다음, **뷰**를 선택합니다.

1. **뷰 추가** 대화 상자에서 다음을 수행합니다.
   
   * **뷰 이름** 상자에 ***Edit***를 입력합니다.
   * **템플릿** 상자에서 ***Edit***를 선택합니다.
   * **모델 클래스** 상자에서 ***항목(todo.Models)*** 을 선택합니다.
   * 레이아웃 페이지 상자에 ***~/Views/Shared/_Layout.cshtml***을 입력합니다.
   * **추가**를 선택합니다.

이 작업이 완료되면 나중에 이러한 뷰로 돌아올 것이므로 Visual Studio에서 모든 cshtml 문서를 닫습니다.

## <a name="connect-to-cosmosdb"></a>5단계: Azure Cosmos DB에 연결 

표준 MVC를 처리했으므로 이제 Azure Cosmos DB에 연결하고 CRUD 작업을 수행하기 위한 코드를 추가해 보겠습니다. 

### <a name="perform-crud-operations"></a> 데이터에서 CRUD 작업 수행

먼저 Azure Cosmos DB에 연결하고 이를 사용하는 논리가 포함된 클래스를 추가합니다. 이 자습서의 경우 이 논리를 TodoItemService.cs라는 클래스로 캡슐화할 것 입니다. 이 코드는 구성 파일에서 Azure Cosmos DB 엔드포인트 값 형식을 읽고 완료되지 않은 항목 나열, 항목 만들기, 편집 및 항목과 같은 CRUD 작업을 수행합니다. 

1. **솔루션 탐색기**에서 **Services**라는 프로젝트 아래에 새 폴더를 만듭니다.

1. **Services** 폴더를 마우스 오른쪽 단추로 클릭하고 **추가**를 선택한 다음, **클래스**를 선택합니다. 새 클래스 이름을 **TodoItemService**로 지정하고 **추가**를 선택합니다.

1. 다음 코드를 **TodoItemService** 클래스에 추가하고 해당 파일의 코드를 다음 코드로 바꿉니다.

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-web-app/src/Services/TodoItemService.cs)]
 
1. 이전 코드는 구성 파일에서 연결 문자열 값을 읽습니다. Azure Cosmos 계정의 연결 문자열 값을 업데이트하려면 프로젝트의 **Web.config** 파일을 열고 `<AppSettings>` 섹션 아래에 다음 줄을 추가합니다.

   ```csharp
    <add key="endpoint" value="<enter the URI from the Keys blade of the Azure Portal>" />
    <add key="primaryKey" value="<enter the PRIMARY KEY, or the SECONDARY KEY, from the Keys blade of the Azure  Portal>" />
    <add key="database" value="Tasks" />
    <add key="container" value="Items" />
   ```
 
1. Azure Portal의 **키** 블레이드에서 검색된 값을 사용하여 엔드포인트 및 primarykey 값을 업데이트합니다. 키 블레이드의 **URI**를 엔드포인트 설정 값으로 사용하고 키 블레이드의 **기본 키** 또는 **보조 키**를 키 설정에 사용합니다. Azure Cosmos DB를 애플리케이션에 연결했으므로 이제 애플리케이션 논리를 추가해 보겠습니다.

1. **Global.asax.cs**를 열고 **Application_Start** 메서드에 다음 줄을 추가합니다. 
   
   ```csharp
   TodoItemService.Initialize().GetAwaiter().GetResult();
   ```

   이때 솔루션이 오류 없이 프로젝트를 빌드할 수 있어야 합니다. 지금 애플리케이션을 실행하면 **HomeController** 및 해당 컨트롤러의 **인덱스** 뷰가 열립니다. 이것은 시작할 때 선택한 MVC 템플릿 프로젝트에 대한 기본 동작입니다. 이 동작을 변경하기 위해 이 MVC 애플리케이션의 라우팅을 변경하겠습니다.

1. ***App\_Start\RouteConfig.cs***를 열고 “defaults:”로 시작하는 줄을 찾은 후 다음 코드로 업데이트합니다.

   ```csharp
   defaults: new { controller = "Item", action = "Index", id = UrlParameter.Optional }
   ```

  이 코드는 이제 ASP.NET MVC에 라우팅 동작을 제어하기 위한 URL에 값이 지정되지 않은 경우 **홈** 대신 **항목**을 컨트롤러로 사용하고 **인덱스**를 뷰로 사용하라고 지시합니다.

이제 애플리케이션을 실행하는 경우 TodoItemService 클래스에서 GetItems 메서드를 호출하는 **ItemController**를 호출합니다. 이는 다음 섹션에서 정의합니다. 

이 프로젝트를 지금 빌드하여 실행하면 이제 다음과 같이 표시됩니다.    

![이 데이터베이스 자습서에서 만든 할 일 모음 웹 애플리케이션의 스크린샷](./media/sql-api-dotnet-application-preview/build-and-run-the-project-now.png)


## <a name="run-the-application"></a>6단계: 로컬에서 애플리케이션 실행

로컬 머신에서 애플리케이션을 테스트하려면 다음 단계를 사용합니다.

1. 디버그 모드에서 애플리케이션을 빌드하려면 Visual Studio에서 F5 키를 누릅니다. 애플리케이션이 빌드되고 앞에서 본 것처럼 빈 그리드 페이지가 포함된 상태로 브라우저가 시작되어야 합니다.
   
   ![이 데이터베이스 자습서에서 만든 할 일 모음 웹 애플리케이션의 스크린샷](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-create-an-item-a.png)
       
2. **새로 만들기** 링크를 클릭하고 **이름** 및 **설명** 필드에 값을 추가합니다. **완료** 확인란을 선택 취소된 상태로 둡니다. 그렇지 않으면 새 항목이 완료 상태로 추가되며 초기 목록에 나타나지 않습니다.
   
3. **만들기**를 클릭하면 **인덱스** 뷰로 다시 리디렉션되고 항목이 목록에 나타납니다. Todo 목록에 항목을 더 추가해도 됩니다.

    ![인덱스 뷰의 스크린샷](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-create-an-item.png)
  
4. 목록에서 **항목** 옆의 **편집**을 클릭합니다. **편집** 뷰로 이동되며, 여기서 **완료** 플래그를 비롯한 개체 속성을 업데이트할 수 있습니다. **완료** 플래그를 표시하고 **저장**을 클릭하면 **항목**이 완료되지 않은 작업 목록에서 제거됩니다.
   
   ![완료 상자가 선택된 인덱스 뷰의 스크린샷](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-completed-item.png)

5. 앱을 테스트하고 나면 Ctrl+F5를 눌러 앱 디버깅을 중지합니다. 배포할 준비가 되었습니다!

## <a name="deploy-the-application-to-azure"></a>7단계: 애플리케이션 배포 
이제 전체 애플리케이션이 Azure Cosmos DB와 올바르게 작동하므로 Azure App Service에 이 웹앱을 배포하겠습니다.  

1. 이 애플리케이션을 게시하려면 **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다.
   
2. **게시** 대화 상자에서 **Microsoft Azure App Service**를 선택한 다음, **새로 만들기**를 선택하여 App Service 프로필을 만들거나 **기존 항목 선택**을 클릭하여 기존 프로필을 사용합니다.

3. 기존 Azure App Service 프로필이 있는 경우 드롭다운에서 **구독**을 선택합니다. **뷰** 필터를 사용하여 리소스 그룹 또는 리소스 형식별로 정렬합니다. 그런 다음, 필요한 Azure App Service를 검색하고 **확인**을 선택합니다. 
   
   ![Visual Studio의 App Service 대화 상자](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-app-service.png)

4. 새 Azure App Service 프로필을 만들려면 **게시** 대화 상자에서 **새로 만들기**를 클릭합니다. **앱 서비스 만들기** 대화 상자에서 웹앱 이름 및 적절한 구독, 리소스 그룹 및 App Service 계획을 입력한 다음, **만들기**를 선택합니다.

   ![Visual Studio의 App Service 만들기 대화 상자](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-create-app-service.png)

몇 초 후에 Visual Studio에서 웹 애플리케이션을 게시하고 브라우저가 시작되면 Azure에서 실행되는 프로젝트를 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계
이 자습서에서는 Azure Cosmos DB에 저장된 데이터에 액세스할 수 있는 ASP.NET MVC 웹 애플리케이션을 빌드하는 방법에 대해 알아보았습니다. 이제 다음 문서로 진행할 수 있습니다.

> [!div class="nextstepaction"]
> [Azure Cosmos DB의 SQL API 계정에 저장된 데이터에 액세스하는 Java 애플리케이션 빌드]( sql-api-java-application.md)


[Visual Studio Express]: https://www.visualstudio.com/products/visual-studio-express-vs.aspx
[Microsoft Web Platform Installer]: https://www.microsoft.com/web/downloads/platform.aspx
[Preventing Cross-Site Request Forgery]: https://go.microsoft.com/fwlink/?LinkID=517254
[Basic CRUD Operations in ASP.NET MVC]: https://go.microsoft.com/fwlink/?LinkId=317598
[GitHub]: https://github.com/Azure-Samples/cosmos-dotnet-todo-app
