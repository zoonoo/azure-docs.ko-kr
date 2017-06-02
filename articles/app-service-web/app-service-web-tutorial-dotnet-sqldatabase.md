---
title: "SQL Database를 사용하여 Azure에서 ASP.NET 앱 빌드 | Microsoft Docs"
description: "SQL Database에 연결하여 Azure에서 ASP.NET 앱이 작동하도록 하는 방법에 대해 알아봅니다."
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: 
ms.assetid: 03c584f1-a93c-4e3d-ac1b-c82b50c75d3e
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 05/04/2017
ms.author: cephalin
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 5edc47e03ca9319ba2e3285600703d759963e1f3
ms.openlocfilehash: 3ae3e5d55454a33a35950057667f9648b63bb331
ms.contentlocale: ko-kr
ms.lasthandoff: 06/01/2017

---
# <a name="build-an-aspnet-app-in-azure-with-sql-database"></a>SQL Database를 사용하여 Azure에서 ASP.NET 앱 빌드

이 자습서에서는 Azure에서 데이터 기반 ASP.NET 웹앱을 개발하고, Azure SQL Database에 연결하며, 데이터 기반 기능을 사용하도록 설정하는 방법을 보여 줍니다. 이 자습서를 완료하면 ASP.NET 응용 프로그램이 [Azure App Service](../app-service/app-service-value-prop-what-is.md)에서 실행되고 SQL Database에 연결되어 있습니다.

![Azure 웹앱의 게시된 ASP.NET 응용 프로그램](./media/app-service-web-tutorial-dotnet-sqldatabase/azure-app-in-browser.png)

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Azure에서 SQL Database 만들기
> * SQL Database에 ASP.NET 앱 연결
> * Azure에 앱 배포
> * 데이터 모델 업데이트 및 앱 다시 배포
> * Azure에서 터미널로 로그 스트림
> * Azure Portal에서 앱 관리

## <a name="prerequisites"></a>필수 조건

이 샘플을 실행하려면 먼저 [무료 Visual Studio 2017 Community를 다운로드하여 설치](https://www.visualstudio.com/downloads/)합니다. Visual Studio를 설정하는 동안 **Azure 개발**을 사용할 수 있는지 확인합니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>샘플 다운로드
이 단계에서는 샘플 ASP.NET 응용 프로그램을 다운로드합니다.

### <a name="get-the-sample-project"></a>샘플 프로젝트 가져오기

[여기](https://github.com/Azure-Samples/dotnet-sqldb-tutorial/archive/master.zip)를 클릭하여 샘플 프로젝트를 다운로드합니다.

다운로드한 _dotnet-sqldb-tutorial-master.zip_을 작업 디렉터리에 추출합니다.

> [!TIP]
> GitHub 리포지토리를 복제하여 동일한 샘플 프로젝트를 가져올 수 있습니다.
>
> ```bash
> git clone https://github.com/Azure-Samples/dotnet-sqldb-tutorial.git
> ```
>
>

이 샘플 프로젝트에는 [Entity Framework Code First](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application)를 기반으로 작성된 간단한 [ASP.NET MVC](https://www.asp.net/mvc) CRUD(Create-Read-Update-Delete) 응용 프로그램이 들어 있습니다.

### <a name="run-the-application"></a>응용 프로그램 실행

추출한 디렉터리에서 _dotnet-sqldb-tutorial-master\DotNetAppSqlDb.sln_을 Visual Studio 2017에서 엽니다.

샘플 솔루션이 열리면 `F5`를 입력하여 브라우저에서 실행합니다.

홈페이지에 간단한 할 일 목록이 표시됩니다. 빈 목록에 몇 가지 할 일을 추가합니다.

![새 ASP.NET 프로젝트 대화 상자](./media/app-service-web-tutorial-dotnet-sqldatabase/local-app-in-browser.png)

데이터베이스 컨텍스트는 `MyDbConnection`이라는 연결 문자열을 사용합니다. 이 연결 문자열은 _Web.config_에 정의되고 _Models\MyDatabaseContext.cs_에서 참조됩니다. 나중에 Azure 웹앱을 Azure SQL Database에 연결할 때는 연결 문자열 이름만 있으면 됩니다. 

## <a name="publish-to-azure-with-sql-database"></a>SQL Database를 사용하여 Azure에 게시

**솔루션 탐색기**에서 **DotNetAppSqlDb** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다.

![솔루션 탐색기에서 게시](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

**Microsoft Azure App Service**를 선택했는지 확인하고 **게시**를 클릭합니다.

![프로젝트 개요 페이지에서 게시](./media/app-service-web-tutorial-dotnet-sqldatabase/publish-to-app-service.png)

그러면 **App Service 만들기** 대화 상자가 열리고 Azure에서 ASP.NET 웹앱을 실행하는 데 필요한 모든 Azure 리소스를 만들 수 있습니다.

### <a name="sign-in-to-azure"></a>Azure에 로그인

**App Service 만들기** 대화 상자에서 **계정 추가**를 클릭한 다음 Azure 구독에 로그인합니다. 이미 Microsoft 계정에 로그인한 경우 계정에 Azure 구독이 있는지 확인합니다. 로그인된 Microsoft 계정에 Azure 구독이 없는 경우 클릭하여 올바른 계정을 추가합니다.
   
![Azure에 로그인](./media/app-service-web-tutorial-dotnet-sqldatabase/sign-in-azure.png)

일단 로그인되면 이 대화 상자에서 Azure 웹앱에 필요한 모든 리소스를 만들 준비가 되었습니다.

### <a name="create-a-resource-group"></a>리소스 그룹 만들기

먼저, _리소스 그룹_이 필요합니다. 

> [!NOTE] 
> 리소스 그룹은 웹앱, 데이터베이스, 저장소 계정과 같은 Azure 리소스가 배포되고 관리되는 논리적 컨테이너입니다.
>
>

**리소스 그룹** 옆에 있는 **새로 만들기**를 클릭합니다.

리소스 그룹의 이름을 **myResourceGroup**으로 지정하고 **확인**을 클릭합니다.

### <a name="create-an-app-service-plan"></a>App Service 계획 만들기

[!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

**App Service 계획** 옆에 있는 **새로 만들기**를 클릭합니다. 

**App Service 계획 구성** 대화 상자에서 다음 설정을 사용하여 새 App Service 계획을 구성합니다.

- **App Service 계획**: **myAppServicePlan**을 입력합니다. 
- **위치**: **유럽 서부** 또는 원하는 다른 지역을 선택합니다.
- **크기**: **체험판** 또는 원하는 기타 [가격 책정 계층](https://azure.microsoft.com/pricing/details/app-service/)을 선택합니다.

**확인**을 클릭합니다.

![앱 서비스 계획 만들기](./media/app-service-web-tutorial-dotnet-sqldatabase/configure-app-service-plan.png)

### <a name="configure-the-web-app-name"></a>웹앱 이름 구성

**웹앱 이름**에서 고유한 앱 이름을 입력합니다. 이 이름은 사용자의 앱에 대한 기본 DNS 이름의 일부로 사용되므로(`<app_name>.azurewebsites.net`) Azure의 모든 앱에서 고유해야 합니다. 나중에 사용자에게 노출하기 전에 앱에 사용자 지정 도메인 이름을 매핑할 수 있습니다.

또한 자동으로 생성된 이름을 적용할 수 있습니다. 이 이름은 고유합니다.

다음 단계를 준비하려면 **추가 Azure 서비스 탐색**을 클릭합니다.

> [!NOTE]
> 이 대화 상자에서 아직 **만들기**를 클릭하지 마세요. 먼저 다음 단계에서는 SQL Database를 설정해야 합니다.

![웹앱 이름 구성](./media/app-service-web-tutorial-dotnet-sqldatabase/web-app-name.png)

### <a name="create-a-sql-server-instance"></a>SQL Server 인스턴스 만들기

**서비스** 탭에서 **SQL Database** 옆에 있는 **+**  아이콘을 클릭합니다. 

**SQL Database 구성** 대화 상자에서 **SQL Server** 옆에 있는 **새로 만들기**를 클릭합니다. 

**서버 이름**에 고유한 이름을 입력합니다. 이 이름은 데이터베이스 서버(`<server_name>.database.windows.net`)에 대한 기본 DNS 이름의 일부로 사용되므로 Azure의 모든 SQL Server 인스턴스에서 고유해야 합니다. 

나머지 필드를 원하는 대로 구성하고 **확인**을 클릭합니다.

![SQL Server 인스턴스 만들기](./media/app-service-web-tutorial-dotnet-sqldatabase/configure-sql-database-server.png)

### <a name="configure-the-sql-database"></a>SQL Database 구성

**데이터베이스 이름**에 _myToDoAppDb_ 또는 원하는 이름을 입력합니다.

**연결 문자열 이름**에 _MyDbConnection_을 입력합니다. 이 이름은 _Models\MyDatabaseContext.cs_에서 참조되는 연결 문자열과 일치해야 합니다.

![SQL Database 구성](./media/app-service-web-tutorial-dotnet-sqldatabase/configure-sql-database.png)

### <a name="create-and-publish-the-web-app"></a>웹앱 만들기 및 게시

**만들기**를 클릭합니다. 

마법사가 Azure 리소스 만들기를 완료하면 처음으로 Azure에 ASP.NET 응용 프로그램을 자동으로 게시하고 기본 브라우저에서 게시된 Azure 웹앱을 시작합니다.

빈 목록에 몇 가지 할 일 항목을 추가합니다.

![Azure 웹앱의 게시된 ASP.NET 응용 프로그램](./media/app-service-web-tutorial-dotnet-sqldatabase/azure-app-in-browser.png)

축하합니다. 데이터 기반 ASP.NET 응용 프로그램이 Azure App Service에서 라이브로 실행되고 있습니다.

## <a name="access-the-sql-database-locally"></a>로컬에서 SQL Database에 액세스

Visual Studio를 사용하면 **SQL Server 개체 탐색기**에서 새 SQL Database를 쉽게 탐색하고 관리할 수 있습니다.

### <a name="create-a-database-connection"></a>데이터베이스 연결 만들기

`Ctrl`+`` ` ``, `Ctrl`+`S`를 입력하여 **SQL Server 개체 탐색기**를 엽니다.

**SQL Server 개체 탐색기** 맨 위에 있는 **SQL Server 추가** 단추를 클릭합니다.

### <a name="configure-the-database-connection"></a>데이터베이스 연결 구성

**연결** 대화 상자에서 **Azure** 노드를 확장합니다. Azure의 모든 SQL Database가 여기에 나열됩니다.

이전에 만든 SQL Database를 선택합니다. 이전에 사용한 연결이 맨 아래에 자동으로 채워집니다.

이전에 사용한 데이터베이스 관리자 암호를 입력하고 **연결**을 클릭합니다.

![Visual Studio에서 데이터베이스 연결 구성](./media/app-service-web-tutorial-dotnet-sqldatabase/connect-to-sql-database.png)

### <a name="allow-client-connection-from-your-computer"></a>컴퓨터에서 클라이언트 연결 허용

**새 방화벽 규칙 만들기** 대화 상자가 열립니다. 기본적으로 SQL Server 인스턴스는 Azure 웹앱과 같은 Azure 서비스의 연결만 허용합니다. Visual Studio에서 직접 데이터베이스에 연결하려면 로컬 컴퓨터의 공용 IP 주소를 허용하도록 SQL Server 인스턴스에서 방화벽 규칙을 만들어야 합니다.

Visual Studio에서는 간단한 작업입니다. 대화 상자가 컴퓨터의 공용 IP 주소로 이미 채워져 있습니다.

**내 클라이언트 IP 추가**가 선택되어 있는지 확인하고 **확인**을 클릭합니다. 

![SQL Server 인스턴스에 대한 방화벽 설정](./media/app-service-web-tutorial-dotnet-sqldatabase/sql-set-firewall.png)

Visual Studio에서 SQL Server 인스턴스에 대한 방화벽 설정 만들기를 완료하면 **SQL Server 개체 탐색기**에 연결이 표시됩니다.

여기에서 쿼리 실행, 뷰 및 저장 프로시저 만들기 등의 가장 일반적이 데이터베이스 작업을 수행할 수 있습니다. 다음 예제에서는 데이터베이스 데이터를 보는 방법을 보여 줍니다. 

![SQL Database 개체 탐색](./media/app-service-web-tutorial-dotnet-sqldatabase/explore-sql-database.png)

## <a name="update-app-with-code-first-migrations"></a>Code First 마이그레이션으로 앱 업데이트

이 단계에서는 Entity Framework에서 Code First 마이그레이션을 사용하여 데이터베이스 스키마를 변경하고 Azure에 게시합니다.

### <a name="update-your-data-model"></a>데이터 모델 업데이트

코드 편집기에서 _Models\Todo.cs_를 엽니다. 다음 속성을 `ToDo` 클래스에 추가합니다.

```csharp
public bool Done { get; set; }
```

### <a name="run-code-first-migrations-locally"></a>Code First 마이그레이션을 로컬에서 실행

다음으로 몇 가지 명령을 실행하여 localdb 데이터베이스를 업데이트합니다. 

**도구** 메뉴에서 **NuGet 패키지 관리** > **패키지 관리자 콘솔**을 클릭합니다. 일반적으로 콘솔은 맨 아래 창에 열립니다.

다음과 같이 Code First 마이그레이션을 사용하도록 설정합니다.

```PowerShell
Enable-Migrations
```

다음과 같이 마이그레이션을 추가합니다.

```PowerShell
Add-Migration AddProperty
```

다음과 같이 localdb 데이터베이스를 업데이트합니다.

```PowerShell
Update-Database
```

`F5` 키로 응용 프로그램을 실행하여 변경 내용을 테스트합니다.

응용 프로그램이 오류 없이 로드되면 Code First 마이그레이션이 성공한 것입니다. 그러나 응용 프로그램 논리에서 이 새로운 속성을 아직 사용하지 않기 때문에 페이지가 여전히 동일하게 표시될 수 있습니다. 

### <a name="use-the-new-property"></a>새 속성 사용

`Done` 속성을 사용하도록 코드를 약간 변경해 보겠습니다. 이 자습서에서는 간단하게 `Index` 및 `Create` 보기만 변경하여 속성의 실제 작동을 확인합니다.

_Controllers\TodosController.cs_를 엽니다.

`Create()` 메서드를 찾고 `Done`을 `Bind` 특성의 속성 목록에 추가합니다. 완료되면 `Create()` 메서드 시그니처가 다음과 같이 표시됩니다.

```csharp
public ActionResult Create([Bind(Include = "id,Description,CreatedDate,Done")] Todo todo)
```

_Views\Todos\Create.cshtml_을 엽니다.

Razor 코드에 `model.Description`을 사용하는 `<div class="form-group">` 태그가 표시된 다음 `model.CreatedDate`를 사용하는 또 다른 `<div class="form-group">` 태그가 표시됩니다. 다음과 같이 이러한 두 태그 바로 뒤에 `model.Done`을 사용하는 또 다른 `<div class="form-group">` 태그를 추가합니다.

```csharp
<div class="form-group">
    @Html.LabelFor(model => model.Done, htmlAttributes: new { @class = "control-label col-md-2" })
    <div class="col-md-10">
        <div class="checkbox">
            @Html.EditorFor(model => model.Done)
            @Html.ValidationMessageFor(model => model.Done, "", new { @class = "text-danger" })
        </div>
    </div>
</div>
```

_Views\Todos\Index.cshtml_을 엽니다.

빈 `<th></th>` 태그를 검색합니다. 이 태그 바로 위에 다음과 같은 Razor 코드를 추가합니다.

```csharp
<th>
    @Html.DisplayNameFor(model => model.Done)
</th>
```

`Html.ActionLink()` 도우미 메서드를 포함하는 `<td>` 태그를 찾습니다. 이 태그 바로 위에 다음과 같은 Razor 코드를 추가합니다.

```csharp
<td>
    @Html.DisplayFor(modelItem => item.Done)
</td>
```

`Index` 및 `Create` 보기에서 변경 내용을 확인하기만 하면 됩니다. 

`F5`를 다시 입력하여 를 응용 프로그램을 실행합니다.

이제 할 일 항목을 추가하고 **Done**(완료)를 선택할 수 있습니다. 그러면 홈페이지에 완료된 항목으로 표시됩니다. `Edit` 보기를 변경하지 않았기 때문에 이 작업만 수행하면 됩니다.

### <a name="enable-code-first-migrations-in-azure"></a>Azure에서 Code First 마이그레이션 사용

데이터베이스 마이그레이션을 비롯하여 코드 변경이 수행되었으므로 Azure 웹앱에 게시하고 Code First 마이그레이션을 사용하여 SQL Database도 업데이트합니다.

이전과 마찬가지로 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다.

**설정**을 클릭하여 게시 마법사를 엽니다.

![게시 설정 열기](./media/app-service-web-tutorial-dotnet-sqldatabase/publish-settings.png)

마법사에서 **다음**을 클릭합니다.

SQL Database에 대한 연결 문자열이 **MyDatabaseContext (MyDbConnection)**로 채워졌는지 확인합니다. 드롭다운에서 **myToDoAppDb** 데이터베이스를 선택해야 할 수 있습니다. 

**Execute Code First Migrations (runs on application start)**(Code First 마이그레이션 실행(응용 프로그램 시작 시 실행))를 선택한 다음 **저장**을 클릭합니다.

![Azure 웹앱에서 Code First 마이그레이션 사용](./media/app-service-web-tutorial-dotnet-sqldatabase/enable-migrations.png)

### <a name="publish-your-changes"></a>변경 내용 게시

Azure 웹앱에서 Code First 마이그레이션을 사용하도록 설정했으므로 코드 변경 내용을 게시하면 됩니다.

게시 페이지에서 **게시**를 클릭합니다.

할 일 항목 추가를 다시 시도하고 **Done**(완료)을 선택합니다. 그러면 홈페이지에 완료된 항목으로 표시되어야 합니다.

![Code First 마이그레이션 후 Azure 웹앱](./media/app-service-web-tutorial-dotnet-sqldatabase/this-one-is-done.png)

> [!NOTE]
> 모든 기존 할 일 항목이 계속 표시됩니다. ASP.NET 응용 프로그램을 다시 게시해도 SQL Database의 기존 데이터가 손실되지 않습니다. 또한 Code First 마이그레이션은 데이터 스키마만 변경하고 기존 데이터는 그대로 유지합니다.
>
>

## <a name="stream-application-logs"></a>응용 프로그램 로그 스트림

Azure 웹앱에서 직접 Visual Studio로 추적 메시지를 스트림할 수 있습니다.

_Controllers\TodosController.cs_를 엽니다.

각 작업은 `Trace.WriteLine()` 메서드로 시작됩니다. 이 코드를 추가하여 Azure 웹앱에 추적 메시지를 추가하기가 얼마나 쉬운지를 보여 줍니다.

### <a name="open-server-explorer"></a>서버 탐색기 열기

**서버 탐색기**에서 Azure 웹앱에 대한 로깅을 구성할 수 있습니다. 

서버 탐색기를 열려면 `Ctrl`+`Alt`+`S`를 입력합니다.

### <a name="enable-log-streaming"></a>로그 스트리밍 사용

**서버 탐색기**에서 **Azure** > **App Service**를 확장합니다.

Azure 웹앱을 처음 만들 때 만든 **myResourceGroup** 리소스 그룹을 확장합니다.

Azure 웹앱을 마우스 오른쪽 단추로 클릭하고 **스트리밍 로그 보기**를 선택합니다.

![로그 스트리밍 사용](./media/app-service-web-tutorial-dotnet-sqldatabase/stream-logs.png)

이제 로그가 **출력** 창으로 스트리밍됩니다. 

![출력 창에서 로그 스트리밍](./media/app-service-web-tutorial-dotnet-sqldatabase/log-streaming-pane.png)

그러나 추적 메시지가 아직 표시되지 않습니다. **스트리밍 로그 보기**를 먼저 선택하면 Azure 웹앱에서 추적 수준을 `Error`로 설정하여 `Trace.TraceError()` 메서드로 생성된 오류 이벤트만 로깅하기 때문입니다.

### <a name="change-trace-levels"></a>추적 수준 변경

다른 추적 메시지를 출력하도록 추적 수준을 변경하려면 **서버 탐색기**로 돌아갑니다.

Azure 웹앱을 마우스 오른쪽 단추로 다시 클릭하고 **설정**을 선택합니다.

**응용 프로그램 로깅(파일 시스템)** 드롭다운에서 **자세한 정보 표시**를 선택합니다. **Save**를 클릭합니다.

![추적 수준을 자세한 정보 표시로 설정](./media/app-service-web-tutorial-dotnet-sqldatabase/trace-level-verbose.png)

> [!TIP]
> 다양한 추적 수준을 사용하여 각 수준에서 표시되는 메시지 종류를 확인할 수 있습니다. 예를 들어 **정보** 수준에는 `Trace.TraceInformation()`, `Trace.TraceWarning()` 및 `Trace.TraceError()`로 생성된 모든 로그가 포함되지만 `Trace.WriteLine()`으로 생성된 로그는 포함되지 않습니다.
>
>

브라우저에서 Azure의 할 일 목록 응용 프로그램을 클릭해 봅니다. 추적 메시지가 Visual Studio의 **출력** 창으로 스트리밍됩니다.

```
Application: 2017-04-06T23:30:41  PID[8132] Verbose     GET /Todos/Index
Application: 2017-04-06T23:30:43  PID[8132] Verbose     GET /Todos/Create
Application: 2017-04-06T23:30:53  PID[8132] Verbose     POST /Todos/Create
Application: 2017-04-06T23:30:54  PID[8132] Verbose     GET /Todos/Index
```

### <a name="stop-log-streaming"></a>로그 스트리밍 중지

로그 스트리밍 서비스를 중지하려면 **출력** 창에서 **모니터링 중지** 단추를 클릭합니다.

![로그 스트리밍 중지](./media/app-service-web-tutorial-dotnet-sqldatabase/stop-streaming.png)

## <a name="manage-your-azure-web-app"></a>Azure Web App 관리

만든 웹앱을 보려면 Azure Portal로 이동합니다. 

이 작업을 수행하려면 [https://portal.azure.com](https://portal.azure.com)에 로그인합니다.

왼쪽 메뉴에서 **App Service**를 클릭한 다음 Azure 웹앱의 이름을 클릭합니다.

![Azure 웹앱에 대한 포털 탐색](./media/app-service-web-tutorial-dotnet-sqldatabase/access-portal.png)

웹앱의 _블레이드_(가로로 열리는 포털 페이지)로 이동했습니다. 

기본적으로 웹앱의 블레이드는 **개요** 페이지를 표시합니다. 이 페이지에서는 앱이 어떻게 작동하고 있는지를 보여 줍니다. 여기에서 찾아보기, 중지, 시작, 다시 시작, 삭제와 같은 기본 관리 작업을 수행할 수 있습니다. 블레이드의 왼쪽에 있는 탭에서는 열 수 있는 다른 구성 페이지를 보여 줍니다. 

![Azure Portal의 App Service 블레이드](./media/app-service-web-tutorial-dotnet-sqldatabase/web-app-blade.png)

블레이드의 이러한 탭은 웹앱에 추가할 수 있는 유용한 많은 기능을 보여 줍니다. 다음은 몇 가지 가능성을 제공합니다.

- 사용자 지정 DNS 이름 매핑
- 사용자 지정 SSL 인증서 바인딩
- 지속적 배포 구성
- 수평 및 수직 확장
- 사용자 인증 추가

## <a name="clean-up-resources"></a>리소스 정리
 
다른 자습서에서 이러한 리소스가 필요하지 않으면([다음 단계](#next) 참조) 다음 명령을 실행하여 삭제할 수 있습니다. 
  
```azurecli 
az group delete --name myResourceGroup 
``` 

<a name="next"></a>

## <a name="next-steps"></a>다음 단계

이 자습서에서 학습한 방법은 다음과 같습니다.

> [!div class="checklist"]
> * Azure에서 SQL Database 만들기
> * SQL Database에 ASP.NET 앱 연결
> * Azure에 앱 배포
> * 데이터 모델 업데이트 및 앱 다시 배포
> * Azure에서 터미널로 로그 스트림
> * Azure Portal에서 앱 관리

사용자 지정 DNS 이름을 매핑하는 방법에 대해 알아보려면 다음 자습서로 이동합니다.

> [!div class="nextstepaction"]
> [Azure Web Apps에 기존 사용자 지정 DNS 이름 매핑](app-service-web-tutorial-custom-domain.md)

