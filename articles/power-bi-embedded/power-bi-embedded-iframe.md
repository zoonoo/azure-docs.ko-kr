<properties
   pageTitle="Microsoft Power BI Embedded 미리 보기 - IFrame을 사용하여 Power BI 보고서 포함"
   description="Microsoft Power BI Embedded 미리 보기 - 보고서를 앱에 통합하기 위한 필수 코드, Power BI Embedded 앱 토큰으로 인증하는 방법, 보고서를 가져오는 방법"
   services="power-bi-embedded"
   documentationCenter=""
   authors="dvana"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="05/16/2016"
   ms.author="derrickv"/>

# IFrame을 사용하여 Power BI 보고서 포함
이 문서는 **Power BI Embedded** REST API, 앱 토큰, IFrame, 일부 JavaScript를 사용하여 앱에 보고서를 통합 또는 포함하기 위한 필수 코드에 대해 설명합니다.

[Microsoft Power BI Embedded 미리 보기 시작](power-bi-embedded-get-started.md)에서 **작업 공간 컬렉션**을 구성하여 보고서 내용에 대해 하나 이상의 **작업 공간**을 보유하는 방법을 배웁니다. 그런 다음 [Microsoft Power BI Embedded 샘플 시작](power-bi-embedded-get-started-sample.md)에서 **작업 공간**으로 보고서를 가져옵니다.

이 문서는 앱에 보고서를 포함하는 단계를 설명합니다. 이 문서를 따르려면 GitHub에서 [IFrame을 사용하여 보고서 통합](https://github.com/Azure-Samples/power-bi-embedded-iframe) 샘플을 다운로드해야 합니다. 이 샘플은 보고서를 통합하는 데 필요한 필수 C# 및 JavaScript 코드를 설명하기 위한 간단한 ASP.NET 웹 형식의 앱입니다. MVC() 디자인 패턴을 사용하여 보고서를 통합하는 고급 샘플은 GitHub에서 [샘플 대시보드 웹앱](http://go.microsoft.com/fwlink/?LinkId=761493)을 참조하세요.

이제 앱에 **Power BI Embedded** 보고서를 통합하는 방법을 설명하겠습니다.

보고서를 통합하는 단계는 다음과 같습니다.

- 1단계: [작업 공간에 보고서를 가져옵니다](#GetReport). 이 단계에서는 앱 토큰 흐름을 사용하여 [보고서 가져오기](https://msdn.microsoft.com/library/mt711510.aspx) REST 작업을 호출하는 액세스 토큰을 가져옵니다. **보고서 가져오기** 목록에서 보고서를 가져오면 **IFrame** 요소를 사용하여 앱에 보고서를 포함합니다.
- 2단계: [앱에 보고서를 포함합니다](#EmbedReport). 이 단계에서는 보고서에 대해 포함 토큰, 일부 JavaScript, IFrame을 사용하여 보고서를 웹앱에 통합 또는 포함합니다.

보고서를 통합할 방법을 보기 위해 샘플을 실행하려는 경우 GitHub에서 [IFrame을 사용하여 보고서 통합](https://github.com/Azure-Samples/power-bi-embedded-iframe) 샘플을 다운로드하고 Web.Config 설정을 구성합니다.

- **AccessKey**: **AccessKey**는 보고서를 가져오고 보고서를 포함하는 데 사용하는 JWT(JSON Web Token)를 생성하는 데 사용합니다. **AccessKey**를 가져오는 방법은 [Microsoft Power BI Embedded 미리 보기 시작](power-bi-embedded-get-started.md)을 참조하세요.
- **WorkspaceName**: **WorkspaceName**을 가져오는 방법은 [Microsoft Power BI Embedded 미리 보기 시작](power-bi-embedded-get-started.md)을 참조하세요.
- **WorkspaceId**: **WorkspaceId**을 가져오는 방법은 [Microsoft Power BI Embedded 미리 보기 시작](power-bi-embedded-get-started.md)을 참조하세요.

다음 섹션에서는 보고서를 통합하는 데 필요한 코드를 보여줍니다.

<a name="GetReport"/>
## 작업 공간에 보고서 가져오기

앱에 보고서를 통합하려면 보고서 **ID**와 **embedUrl**이 필요합니다. 보고서 **ID**와 **embedUrl**을 가져오려면 [Get Reports](https://msdn.microsoft.com/library/mt711510.aspx) REST 작업을 호출하고 JSON 목록에서 보고서를 선택합니다. [앱에 보고서 포함](#EmbedReport)에서 보고서 **ID**와 **embedUrl**을 사용하여 앱에 보고서를 포함합니다.

### 보고서 가져오기 JSON 응답
```
{
  "@odata.context":"https://api.powerbi.com/beta/collections/{WorkspaceName}/workspaces/{WorkspaceId}/$metadata#reports","value":[
    {
      "id":"804d3664-…-e71882055dba","name":"Import report sample","webUrl":"https://embedded.powerbi.com/reports/804d3664-...-e71882055dba","embedUrl":"https://embedded.powerbi.com/appTokenReportEmbed?reportId=804d3664-...-e71882055dba"
    },{
      "id":"1d7cff58-…-380610e263a9","name":"Sample Report 2","webUrl":"https://embedded.powerbi.com/reports/1d7cff58-...-380610e263a9","embedUrl":"https://embedded.powerbi.com/appTokenReportEmbed?reportId=1d7cff58-...-380610e263a9"
    }
  ]
}

```

[Get Reports](https://msdn.microsoft.com/library/mt711510.aspx) REST 작업을 호출하려면 앱 토큰을 사용합니다. 앱 토큰 흐름에 대해 자세히 알아보려면 [Power BI Embedded의 앱 토큰 흐름 정보](power-bi-embedded-app-token-flow.md)를 참조하세요. 다음 코드는 JSON 보고서 목록을 가져오는 방법에 대해 설명합니다. 보고서를 포함하려면 [앱에 보고서 포함](#EmbedReport)을 참조하세요.

```
protected void getReportsButton_Click(object sender, EventArgs e)
{
    //Get an app token to generate a JSON Web Token (JWT). An app token flow is a claims-based design pattern.
    //To learn how you can code an app token flow to generate a JWT, see the PowerBIToken class.
    var appToken = PowerBIToken.CreateDevToken(workspaceName, workspaceId);

    //After you get a PowerBIToken which has Claims including your WorkspaceName and WorkspaceID,
    //you generate JSON Web Token (JWT) . The Generate() method uses classes from System.IdentityModel.Tokens: SigningCredentials,
    //JwtSecurityToken, and JwtSecurityTokenHandler.
    string jwt = appToken.Generate(accessKey);

    //Set app token textbox to JWT string to show that the JWT was generated
    appTokenTextbox.Text = jwt;

    //Construct reports uri resource string
    var uri = String.Format("https://api.powerbi.com/beta/collections/{0}/workspaces/{1}/reports", workspaceName, workspaceId);

    //Configure reports request
    System.Net.WebRequest request = System.Net.WebRequest.Create(uri) as System.Net.HttpWebRequest;
    request.Method = "GET";
    request.ContentLength = 0;

    //Set the WebRequest header to AppToken, and jwt
    //Note the use of AppToken instead of Bearer
    request.Headers.Add("Authorization", String.Format("AppToken {0}", jwt));

    //Get reports response from request.GetResponse()
    using (var response = request.GetResponse() as System.Net.HttpWebResponse)
    {
        //Get reader from response stream
        using (var reader = new System.IO.StreamReader(response.GetResponseStream()))
        {
            //Deserialize JSON string into PBIReports
            PBIReports PBIReports = JsonConvert.DeserializeObject<PBIReports>(reader.ReadToEnd());

            //Clear Textbox
            tb_reportsResult.Text = string.Empty;

            //Get each report
            foreach (PBIReport rpt in PBIReports.value)
            {
                tb_reportsResult.Text += String.Format("{0}\t{1}\t{2}\n", rpt.id, rpt.name, rpt.embedUrl);
            }
        }
    }
}
```

<a name="EmbedReport"/>
## 앱에 보고서 포함

앱에 보고서를 포함하려면 보고서에 대한 포함 토큰이 필요합니다. 이 토큰은 **Power BI Embedded** REST 작업을 호출하는 데 사용하는 앱 토큰과 유사하지만 REST 리소스가 아닌 보고서 리소스에 대해 생성됩니다. 다음은 보고서의 앱 토큰을 가져오는 코드입니다. 보고서 앱 토큰을 사용하려면 [앱에 보고서 포함](#EmbedReportJS)을 참조하세요.

<a name="EmbedReportToken"/>
### 보고서의 앱 토큰 가져오기

```
protected void getReportAppTokenButton_Click(object sender, EventArgs e)
{
    //Get an embed token for a report.
    var token = PowerBIToken.CreateReportEmbedToken(workspaceName, workspaceId, getReportAppTokenTextBox.Text);

    //After you get a PowerBIToken which has Claims including your WorkspaceName and WorkspaceID,
    //you generate JSON Web Token (JWT) . The Generate() method uses classes from System.IdentityModel.Tokens: SigningCredentials,
    //JwtSecurityToken, and JwtSecurityTokenHandler.
    string jwt = token.Generate(accessKey);

    //Set textbox to JWT string. The JavaScript embed code uses the embed jwt for a report.
    reportAppTokenTextbox.Text = jwt;
}
```

<a name="EmbedReportJS"/>
### 앱에 보고서 포함

앱에 **Power BI** 보고서를 포함하려면 IFrame 및 일부 JavaScript 코드를 사용합니다. 다음은 보고서를 포함하기 위한 예제 IFrame 및 JavaScript 코드입니다. 보고서를 포함하기 위한 모든 샘플 코드를 보려면 GitHub에서 [IFrame을 사용하여 보고서 통합](https://github.com/Azure-Samples/power-bi-embedded-iframe)을 참조하세요.

![Iframe](media\power-bi-embedded-integrate-report\Iframe.png)


```
window.onload = function () {
    // Client side click to embed a selected report.
    var el = document.getElementById("bEmbedReportAction");
    if (el.addEventListener) {
        el.addEventListener("click", updateEmbedReport, false);
    } else {
        el.attachEvent('onclick', updateEmbedReport);
    }

};

// Update embed report
function updateEmbedReport() {
    // Check if the embed url was selected
    var embedUrl = document.getElementById('tb_EmbedURL').value;

    // To load a report do the following:
    // 1: Set the url
    // 2: Add a onload handler to submit the auth token
    var iframe = document.getElementById('iFrameEmbedReport');
    iframe.src = embedUrl;
    iframe.onload = postActionLoadReport;
}

// Post the auth token to the iFrame.
function postActionLoadReport() {

    // Get the app token.
    accessToken = document.getElementById('MainContent_reportAppTokenTextbox').value;

    // Construct the push message structure
    var m = { action: "loadReport", accessToken: accessToken };
    message = JSON.stringify(m);

    // Push the message.
    iframe = document.getElementById('iFrameEmbedReport');
    iframe.contentWindow.postMessage(message, "*");
}
```
앱에 보고서를 포함한 후에 보고서를 필터링할 수 있습니다. 다음 섹션에서는 URL 구문을 사용하여 보고서를 필터링하는 방법을 보여 줍니다.

## 보고서 필터링

URL 구문을 사용하여 포함된 보고서를 필터링할 수 있습니다. 이를 위해 지정된 필터와 함께 iFrame src url에 쿼리 문자열 매개 변수를 추가합니다. **값으로 필터링**하고 **필터 창을 숨길** 수 있습니다.


**값으로 필터링**

값으로 필터링하려면 다음과 같이 **eq** 연산자로 **$filter** 쿼리 구문을 사용합니다.

```
https://app.powerbi.com/reportEmbed
?reportId=d2a0ea38-0694-...-ee9655d54a4a&
$filter={tableName/fieldName}%20eq%20'{fieldValue}'
```

예를 들어 저장소 체인이 'Lindseys'인 부분을 필터링할 수 있습니다. URL의 필터 일부는 다음과 같습니다.

```
$filter=Store/Chain%20eq%20'Lindseys'
```

> [AZURE.NOTE] {tableName/fieldName}은(는) 공백이나 특수 문자를 포함할 수 없습니다. {fieldValue}은(는) 단일 범주 값을 허용합니다.

**필터 창 숨기기**

**필터 창**을 숨기려면 보고서 쿼리 문자열에 **filterPaneEnabled**를 다음과 같이 추가합니다.

```
&filterPaneEnabled=false
```

## 결론

이 문서에서는 앱에 **Power BI** 보고서를 통합하기 위한 코드를 소개했습니다. 앱에 보고서를 빠르게 통합하려면 GitHub에서 다음 샘플을 다운로드합니다.

- [IFrame 샘플을 사용하여 보고서 통합](https://github.com/Azure-Samples/power-bi-embedded-iframe)
- [샘플 대시보드 웹앱](http://go.microsoft.com/fwlink/?LinkId=761493)

## 참고 항목
- [Microsoft Power BI Embedded 미리 보기 시작](power-bi-embedded-get-started.md)
- [샘플 시작](power-bi-embedded-get-started-sample.md)
- [System.IdentityModel.Tokens.SigningCredentials](https://msdn.microsoft.com/library/system.identitymodel.tokens.signingcredentials.aspx)
- [System.IdentityModel.Tokens.JwtSecurityToken](https://msdn.microsoft.com/library/system.identitymodel.tokens.jwtsecuritytoken.aspx)
- [System.IdentityModel.Tokens.JwtSecurityTokenHandler](https://msdn.microsoft.com/library/system.identitymodel.tokens.signingcredentials.aspx)
- [보고서 가져오기](https://msdn.microsoft.com/library/mt711510.aspx)

<!---HONumber=AcomDC_0518_2016-->