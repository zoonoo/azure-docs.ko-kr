---
title: 샘플 시작
description: Power BI Embedded, SDK를 사용하여 대화형 Power BI 보고서를 비즈니스 인텔리전스 응용 프로그램에 추가
services: power-bi-embedded
documentationcenter: ''
author: guyinacube
manager: erikre
editor: ''
tags: ''

ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 10/04/2016
ms.author: asaxton

---
# <a name="get-started-with-power-bi-embedded-sample"></a>Power BI Embedded 시작 샘플
**Microsoft Power BI Embedded**를 사용하면 Power BI 보고서를 웹 또는 모바일 응용 프로그램에 통합할 수 있습니다. 이 문서에서 **Power BI Embedded** 시작 샘플을 소개합니다.

진행하기 전에 다음 리소스를 저장하려고 할 것입니다. 이렇게 하면 Power BI 보고서를 샘플 앱 및 사용자 앱에 통합할 때도 도움이 됩니다.

* [샘플 대시보드 웹앱](http://go.microsoft.com/fwlink/?LinkId=761493)
* [Power BI Embedded API 참조](https://msdn.microsoft.com/library/mt711493.aspx)
* [Power BI Embedded .NET SDK ](http://go.microsoft.com/fwlink/?LinkId=746472) (NuGet을 통해 사용 가능)

> [!NOTE]
> Power BI Embedded 시작 샘플을 구성하고 실행하려면 먼저 Azure 구독에 적어도 하나의 **작업 영역 컬렉션** 을 만들어야 합니다. Azure 포털에 **작업 영역 컬렉션** 을 만드는 방법을 알아보려면 [Power BI Embedded 시작](power-bi-embedded-get-started.md)을 참조하세요.
> 
> 

## <a name="configure-the-sample-app"></a>샘플 앱 구성
샘플 앱을 실행하는 데 필요한 구성 요소에 액세스할 Visual Studio 개발 환경을 설정하는 방법을 단계적으로 안내합니다.

1. [Power BI Embedded - 보고서를 웹앱으로 통합](http://go.microsoft.com/fwlink/?LinkId=761493) 샘플을 GitHub에서 다운로드하여 압축을 해제합니다.
2. Visual Studio에서 **PowerBI-embedded.sln** 을 엽니다. 이 솔루션에서 사용되는 패키지를 업데이트하려면 NuGET 패키지 관리자 콘솔에서 **Update-Package** 명령을 실행해야 할 수 있습니다.
3. 솔루션을 빌드하십시오.
4. **ProvisionSample** 콘솔 앱을 실행합니다. 샘플 콘솔 앱에서 작업 영역을 프로비전하고 PBIX 파일을 가져옵니다.
5. 새 **작업 영역**을 프로비전하려면 옵션 5 **기존 작업 영역 컬렉션에서 새 작업 영역 프로비전**을 선택합니다.
   
    ![](media\\powerbi-embedded-get-started-sample\\console-option-5.png)
6. **작업 영역 컬렉션** 이름 및 **선택키**를 입력합니다. **Azure 포털**에서 이러한 정보를 가져올 수 있습니다. **선택키**를 가져오는 방법에 대해 알아보려면 Microsoft Power BI Embedded 시작의 [Power BI API 선택키 보기](power-bi-embedded-get-started-sample.md#view-access-keys) 를 참조하세요.
   
    ![](media\\powerbi-embedded-get-started-sample\\azure-portal.png)
7. 이 문서의 뒷부분에서 사용할 수 있도록 새로 만든 **작업 영역 ID** 를 복사하여 저장합니다. **작업 영역 ID**는 **Azure Portal**에서 확인할 수 있습니다.
   
    ![](media\\powerbi-embedded-get-started-sample\\workspace-id.png)
8. PBIX 파일을 **작업 영역**으로 가져오려면 옵션 **6, 기존 작업 영역**에 PBIX Desktop 파일을 가져옵니다. 사용할 수 있는 PBIX 파일이 없는 경우 [소매 분석 샘플 PBIX](http://go.microsoft.com/fwlink/?LinkID=780547)를 다운로드할 수 있습니다.
9. 메시지가 표시되면 **데이터 집합**의 식별 이름을 입력합니다.

다음과 같은 응답이 표시됩니다.

````
Checking import state... Publishing
Checking import state... Succeeded
```

> [!NOTE]
> If your PBIX file contains any direct query connections, run option 7 to update the connection strings.
> 
> 

At this point, you have a Power BI PBIX report imported into your **Workspace**. Now, let's look at how to run the **Power BI Embedded** get started sample web app.

## Run the sample web app
The web app sample is a sample dashboard that renders reports imported into your **Workspace**. Here's how to configure the web app sample.

1. In the **PowerBI-embedded** Visual Studio solution, right click the **EmbedSample** web application, and choose **Set as StartUp project**.
2. In **web.config**, in the **EmbedSample** web application, edit the **appSettings**: **AccessKey**, **WorkspaceCollection** name, and **WorkspaceId**.
   
    ```
    <appSettings>
        <add key="powerbi:AccessKey" value="" />
        <add key="powerbi:ApiUrl" value="https://api.powerbi.com" />
        <add key="powerbi:WorkspaceCollection" value="" />
        <add key="powerbi:WorkspaceId" value="" />
    </appSettings>
    ```
3. Run the **EmbedSample** web application.

Once you run the **EmbedSample** web application, the left navigation panel should contain a **Reports** menu. To view the report you imported, expand **Reports**, and click a report. If you imported the [Retail Analysis Sample PBIX](http://go.microsoft.com/fwlink/?LinkID=780547), the sample web app would look like this:

![](media\\powerbi-embedded-get-started-sample\\power-bi-embedded-sample-left-nav.png)

After you click a report, the **EmbedSample** web application should look something this:

![](media\\powerbi-embedded-get-started-sample\\sample-web-app.png)

## Explore the sample code
The **Microsoft Power BI Embedded** sample is an example dashboard web app that shows you how to integrate **Power BI** reports into your app. It uses a Model-View-Controller (MVC) design pattern to demonstrate best practices. This section highlights parts of the sample code that you can explore within the **PowerBI-embedded** web app solution. The Model-View-Controller (MVC) pattern separates the modeling of the domain, the presentation, and the actions based on user input into three separate classes: Model, View, and Control. To learn more about MVC, see [Learn About ASP.NET](http://www.asp.net/mvc).

The **Microsoft Power BI Embedded** sample code is separated as follows. Each section includes the file name in the PowerBI-embedded.sln solution so that you can easily find the code in the sample.

> [!NOTE]
> This section is a summary of the sample code that shows how the code was written. To view the complete sample, please load the PowerBI-embedded.sln solution in Visual Studio.
> 
> 

### Model
The sample has a **ReportsViewModel** and **ReportViewModel**.

**ReportsViewModel.cs**: Represents Power BI Reports.

    public class ReportsViewModel
    {
        public List<Report> Reports { get; set; }
    }

**ReportViewModel.cs**: Represents a Power BI Report.

    public classReportViewModel
    {
        public IReport Report { get; set; }

        public string AccessToken { get; set; }
    }

### Connection string
The connection string must be in the following format:

```
Data Source=tcp:MyServer.database.windows.net,1433;Initial Catalog=MyDatabase
```

Using common server and database attributes will fail. For example: Server=tcp:MyServer.database.windows.net,1433;Database=MyDatabase,

### View
The **View** manages the display of Power BI **Reports** and a Power BI **Report**.

**Reports.cshtml**: Iterate over **Model.Reports** to create an **ActionLink**. The **ActionLink** is composed as follows:

| Part | Description |
| --- | --- |
| Title |Name of the Report. |
| QueryString |A link to the Report ID. |

    <div id="reports-nav" class="panel-collapse collapse">
        <div class="panel-body">
            <ul class="nav navbar-nav">
                @foreach (var report in Model.Reports)
                {
                    var reportClass = Request.QueryString["reportId"] == report.Id ? "active" : "";
                    <li class="@reportClass">
                        @Html.ActionLink(report.Name, "Report", new { reportId = report.Id })
                    </li>
                }
            </ul>
        </div>
    </div>

Report.cshtml: Set the **Model.AccessToken**, and the Lambda expression for **PowerBIReportFor**.

    @model ReportViewModel

    ...

    <div class="side-body padding-top">
        @Html.PowerBIAccessToken(Model.AccessToken)
        @Html.PowerBIReportFor(m => m.Report, new { style = "height:85vh" })
    </div>

### Controller
**DashboardController.cs**: Creates a PowerBIClient passing an **app token**. A JSON Web Token (JWT) is generated from the **Signing Key** to get the **Credentials**. The **Credentials** are used to create an instance of **PowerBIClient**. Once you have an instance of **PowerBIClient**, you can call GetReports() and GetReportsAsync().

CreatePowerBIClient()

    private IPowerBIClient CreatePowerBIClient()
    {
        var credentials = new TokenCredentials(accessKey, "AppKey");
        var client = new PowerBIClient(credentials)
        {
            BaseUri = new Uri(apiUrl)
        };

        return client;
    }

ActionResult Reports()

    public ActionResult Reports()
    {
        using (var client = this.CreatePowerBIClient())
        {
            var reportsResponse = client.Reports.GetReports(this.workspaceCollection, this.workspaceId);

            var viewModel = new ReportsViewModel
            {
                Reports = reportsResponse.Value.ToList()
            };

            return PartialView(viewModel);
        }
    }


Task<ActionResult> Report(string reportId)

    public async Task<ActionResult> Report(string reportId)
    {
        using (var client = this.CreatePowerBIClient())
        {
            var reportsResponse = await client.Reports.GetReportsAsync(this.workspaceCollection, this.workspaceId);
            var report = reportsResponse.Value.FirstOrDefault(r => r.Id == reportId);
            var embedToken = PowerBIToken.CreateReportEmbedToken(this.workspaceCollection, this.workspaceId, report.Id);

            var viewModel = new ReportViewModel
            {
                Report = report,
                AccessToken = embedToken.Generate(this.accessKey)
            };

            return View(viewModel);
        }
    }

### Integrate a report into your app
Once you have a **Report**, you use an **IFrame** to embed the Power BI **Report**. Here is a code snippet from  powerbi.js in the **Microsoft Power BI Embedded** sample.

![](media\\powerbi-embedded-get-started-sample\\power-bi-embedded-iframe-code.png)

## Filter reports embedded in your application
You can filter an embedded report using a URL syntax. To do this, you add a **$filter** query string parameter with an **eq** operator to your iFrame src url with the filter specified. Here is the filter query syntax:

```
https://app.powerbi.com/reportEmbed
?reportId=d2a0ea38-...-9673-ee9655d54a4a&
$filter={tableName/fieldName}%20eq%20'{fieldValue}'
```

> [!NOTE]
> {tableName/fieldName} cannot include spaces or special characters. The {fieldValue} accepts a single categorical value.  
> 
> 

## See also
* [Common Microsoft Power BI Embedded scenarios](power-bi-embedded-scenarios.md)
* [Authenticating and authorizing in Power BI Embedded](power-bi-embedded-app-token-flow.md)

<!--HONumber=Oct16_HO2-->


