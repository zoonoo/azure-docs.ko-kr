<properties
   pageTitle="샘플 시작"
   description="샘플 시작"
   services="power-bi-embedded"
   documentationCenter=""
   authors="dvana"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="03/29/2016"
   ms.author="derrickv"/>

# Microsoft Power BI Embedded 샘플 시작

**Microsoft Power BI Embedded 미리 보기**를 사용하면 Power BI 보고서를 웹 또는 모바일 응용 프로그램에 통합할 수 있으므로 사용자에게 데이터를 시각화하기 위해 사용자 지정 솔루션을 빌드할 필요가 없습니다. 다음 리소스를 사용하여 Power BI 보고서를 앱으로의 통합을 시작할 수 있습니다.

 -	[샘플 대시보드 웹앱](http://go.microsoft.com/fwlink/?LinkId=761493)
 -	[Power BI Embedded API 참조](https://msdn.microsoft.com/library/mt711493.aspx)
 -	[Power BI Embedded .NET SDK(NuGet을 통해 사용 가능)](http://go.microsoft.com/fwlink/?LinkId=746472)

이 문서에서 **Power BI Embedded** 시작 샘플을 소개합니다. 샘플 웹앱을 실행할 수 있도록 샘플 앱 구성을 시작하겠습니다.

> [AZURE.NOTE] Power BI Embedded 시작 샘플을 구성하고 실행하려면 먼저 Azure 구독에 적어도 하나의 **작업 영역 컬렉션**을 만들어야 합니다. Azure 포털에 **작업 영역 컬렉션**을 만드는 방법을 알아보려면 [Power BI Embedded 미리 보기 시작](power-bi-embedded-get-started.md)을 참조하세요.

## 샘플 앱 구성

다음에서 샘플 앱을 실행하는 데 필요한 미리 보기 구성 요소에 액세스할 Visual Studio 개발 환경을 설정하는 방법을 단계적으로 안내합니다.

1. [Power BI Embedded - 보고서를 웹앱으로 통합](http://go.microsoft.com/fwlink/?LinkId=761493) 샘플을 GitHub에서 다운로드하여 압축을 해제합니다.

2. Visual Studio에서 **PowerBI-embedded.sln**을 엽니다.

3. 솔루션을 빌드하십시오.

4. **ProvisionSample** 콘솔 앱을 실행합니다. 샘플 콘솔 앱에서 작업 영역을 프로비전하고 PBIX 파일을 가져옵니다.

5. 새 **작업 영역**을 프로비전하려면 옵션 **5를 선택합니다. 기존 작업 영역 컬렉션**의 새 작업 영역을 프로비전합니다.

    ![](media\powerbi-embedded-get-started-sample\console-option-5.png)

6. **작업 영역 컬렉션** 이름 및 **액세스 키**를 입력합니다. **Azure 포털**의 이름 및 액세스 키를 가져올 수 있습니다. **액세스 키**를 가져오는 방법에 대해 알아보려면 Microsoft Power BI Embedded 미리 보기 시작의 [Power BI API 액세스 키 보기](power-bi-embedded-get-started-sample.md#view-access-keys)를 참조하세요.

    ![](media\powerbi-embedded-get-started-sample\azure-portal.png)

7. 새로 만든 **작업 영역 ID**를 복사하고 저장하여 이 문서의 뒷부분에서 사용합니다. **작업 영역 ID**가 만들어지면 **Azure 포털**에서 찾을 수 있습니다.

    ![](media\powerbi-embedded-get-started-sample\workspace-id.png)

8. PBIX 파일을 **작업 영역**에 가져오려면 옵션 **6을 선택합니다. 기존 작업 영역**에 PBIX Desktop 파일을 가져옵니다. 사용할 수 있는 PBIX 파일이 없는 경우 [매출 데이터 PBIX 분석 샘플](http://download.microsoft.com/download/1/4/E/14EDED28-6C58-4055-A65C-23B4DA81C4DE/Analyzing_Sales_Data.pbix)을 다운로드할 수 있습니다.

9. 메시지가 표시되면 **데이터 집합**의 식별 이름을 입력합니다.

다음과 같은 응답이 표시됩니다.

````
Checking import state... Publishing
Checking import state... Succeeded
```

> [AZURE.NOTE] PBIX 파일에 직접 쿼리 연결이 포함되어 있는 경우 연결 문자열을 업데이트하도록 옵션 7을 실행합니다.

이때 Power BI PBIX 보고서를 **작업 영역**에 가져옵니다. 다음 섹션에서는 **Power BI Embedded** 시작 샘플 웹앱을 실행하는 방법을 보여 줍니다. 다음 섹션에서 샘플 웹앱을 실행하는 방법을 알아봅니다.

## 샘플 웹앱 실행

웹앱 샘플은 **작업 영역**에 가져온 보고서를 렌더링하는 샘플 대시보드입니다.

웹앱 샘플을 구성하는 방법은 다음과 같습니다.

1. **PowerBI-embedded** Visual Studio 솔루션에서 **EmbedSample** 웹 응용 프로그램을 마우스 오른쪽 단추로 클릭하고 **시작 프로젝트로 설정**을 선택합니다.
2. **web.config**의 **EmbedSample** 웹 응용 프로그램에서 **appSettings**, 즉 **AccessKey**, **WorkspaceCollection** 이름 및 **WorkspaceId**를 편집합니다.

    ```
    <appSettings>
        <add key="powerbi:AccessKey" value="" />
        <add key="powerbi:ApiUrl" value="https://api.powerbi.com" />
        <add key="powerbi:WorkspaceCollection" value="" />
        <add key="powerbi:WorkspaceId" value="" />
    </appSettings>
    ```
3. **EmbedSample** 웹 응용 프로그램을 실행합니다.

**EmbedSample** 웹 응용 프로그램을 실행하면 왼쪽 탐색 패널에 **보고서** 메뉴가 포함되어야 합니다. 가져온 보고서를 보려면 **보고서**를 확장하고 보고서를 클릭합니다. [판매 데이터 PBIX 분석 샘플](http://download.microsoft.com/download/1/4/E/14EDED28-6C58-4055-A65C-23B4DA81C4DE/Analyzing_Sales_Data.pbix)을 가져온 경우 샘플 웹앱이 다음과 같이 보입니다.

![](media\powerbi-embedded-get-started-sample\power-bi-embedded-sample-left-nav.png)

보고서를 클릭하면 **EmbedSample** 웹 응용 프로그램이 다음과 같이 표시되어야 합니다.

![](media\powerbi-embedded-get-started-sample\sample-web-app.png)

다음 섹션에서 **Power BI Embedded** 샘플 코드를 탐색합니다.

## 샘플 코드 탐색
**Microsoft Power BI Embedded** 미리 보기 샘플은 **Power BI** 보고서를 앱으로 통합하는 방법을 보여 주는 대시보드 웹앱의 예입니다. MVC(Model-View-Controller) 디자인 패턴을 사용하여 모범 사례를 보여 줍니다. 이 섹션에서는 **PowerBI-embedded** 웹앱 솔루션 내에서 탐색할 수 있는 샘플 코드의 부분을 강조해서 보여 줍니다. MVC(Model-View-Controller) 패턴은 도메인의 모델링, 프레젠테이션 및 작업을 사용자 입력에 따라 모델, 보기 및 제어의 세 가지 별도 클래스로 구분합니다. MVC에 대한 알아보려면 [Learn About ASP.NET(ASP.NET에 대해 자세히 알아보기)](http://www.asp.net/mvc)을 참조하세요.

**Microsoft Power BI Embedded** 미리 보기 샘플 코드는 다음과 같이 구분됩니다. 각 섹션에는 샘플 코드를 쉽게 찾을 수 있도록 PowerBI-embedded.sln 솔루션에 파일 이름이 포함되어 있습니다.

> [AZURE.NOTE] 이 섹션에서는 코드가 작성된 방법을 보여 주는 샘플 코드에 대해 요약합니다. GA(일반 공급)를 진행하면서 샘플 설명을 확장할 예정입니다. 전체 샘플을 보려면 Visual Studio에서 PowerBI-embedded.sln을 로드하세요.

### 모델
이 샘플에는 **ReportsViewModel** 및 **ReportViewModel**이 있습니다.

**ReportsViewModel.cs**: 여러 Power BI 보고서를 나타냅니다.

    public class ReportsViewModel
    {
        public List<Report> Reports { get; set; }
    }

**ReportViewModel.cs**: 하나의 Power BI 보고서를 나타냅니다.

    public classReportViewModel
    {
        public IReport Report { get; set; }

        public string AccessToken { get; set; }
    }

### 보기
**보기**를 통해 여러 Power BI **보고서** 및 하나의 Power BI **보고서** 표시를 관리합니다.

**Reports.cshtml**: **ActionLink**를 만드는 **Model.Reports**를 반복합니다. **ActionLink**는 다음과 같이 구성됩니다.

|부|설명
|---|---
|제목| 보고서의 이름입니다.
|QueryString| 보고서 ID에 대한 링크입니다.

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

Report.cshtml: **Model.AccessToken** 및 **PowerBIReportFor**에 대한 람다 식을 설정합니다.

    @model ReportViewModel

    ...

    <div class="side-body padding-top">
        @Html.PowerBIAccessToken(Model.AccessToken)
        @Html.PowerBIReportFor(m => m.Report, new { style = "height:85vh" })
    </div>

### Controller

**DashboardController.cs**: **앱 토큰**을 전달하는 PowerBIClient를 만듭니다. **자격 증명**을 가져올 **서명 키**에서 JWT(JSON 웹 토큰)가 생성됩니다. **자격 증명**은 **PowerBIClient**의 인스턴스를 만드는 데 사용됩니다. **앱 토큰**에 대한 자세한 내용은 [How does app token flow work?(앱 토큰 흐름은 어떻게 작동하나요?)](#key-flow)를 참조하세요. **PowerBIClient**의 인스턴스가 있으면 GetReports() 및 GetReportsAsync()를 호출할 수 있습니다.

CreatePowerBIClient()

    private IPowerBIClient CreatePowerBIClient(PowerBIToken token)
    {
        var jwt = token.Generate(accessKey);
        var credentials = new TokenCredentials(jwt, "AppToken");
        var client = new PowerBIClient(credentials)
        {
            BaseUri = new Uri(apiUrl)
        };

        return client;
    }

ActionResult Reports()

    public ActionResult Reports()
    {
        var devToken = PowerBIToken.CreateDevToken(this.workspaceCollection, this.workspaceId);
        using (var client = this.CreatePowerBIClient(devToken))
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
        var devToken = PowerBIToken.CreateDevToken(this.workspaceCollection, this.workspaceId);
        using (var client = this.CreatePowerBIClient(devToken))
        {
            var reportsResponse = await client.Reports.GetReportsAsync(this.workspaceCollection, this.workspaceId);
            var report = reportsResponse.Value.FirstOrDefault(r => r.Id == reportId);
            var embedToken = PowerBIToken.CreateReportEmbedToken(this.workspaceCollection, this.workspaceId, Guid.Parse(report.Id));

            var viewModel = new ReportViewModel
            {
                Report = report,
                AccessToken = embedToken.Generate(this.accessKey)
            };

            return View(viewModel);
        }
    }

### 앱에 보고서 통합

**보고서**가 있으면 Power BI **보고서**를 포함하는 **IFrame**을 사용합니다. 다음은 **Microsoft Power BI Embedded** 미리 보기 샘플의 powerbi.js의 코드 조각입니다.

![](media\powerbi-embedded-get-started-sample\power-bi-embedded-iframe-code.png)

## 참고 항목

- [Microsoft Power BI Embedded란](power-bi-embedded-what-is-power-bi-embedded.md)
- [일반적인 Microsoft Power BI Embedded 미리 보기 시나리오](power-bi-embedded-scenarios.md)
- [Microsoft Power BI Embedded 미리 보기 시작](power-bi-embedded-get-started.md)
- [Power BI Embedded의 앱 토큰 흐름 정보](power-bi-embedded-app-token-flow.md)

<!---HONumber=AcomDC_0406_2016-->