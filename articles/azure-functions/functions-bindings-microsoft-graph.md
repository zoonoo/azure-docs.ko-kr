---
title: "Azure Functions Microsoft Graph 바인딩 | Microsoft Docs"
description: "Azure Functions에서 Microsoft Graph 트리거 및 바인딩을 사용하는 방법을 파악합니다."
services: functions
author: mattchenderson
manager: cfowler
editor: 
ms.service: functions
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 09/19/2017
ms.author: mahender
ms.openlocfilehash: 8cf2e4e9e9007549dbdc931b4485c4230c536479
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-functions-microsoft-graph-bindings"></a>Azure Functions Microsoft Graph 바인딩
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

이 문서에서는 Azure Functions에서 Microsoft Graph 트리거 및 바인딩을 구성하고 사용하는 방법에 대해 설명합니다.
이러한 방법을 배우고 나면 Azure Functions를 사용하여 [Microsoft Graph](https://graph.microsoft.io)에서 데이터, 자세한 정보 및 이벤트를 작업할 수 있습니다.

Microsoft Graph 확장에는 다음과 같은 바인딩이 제공됩니다.
- 모든 Microsoft Graph API와 상호 작용할 수 있게 해주는 [인증 토큰 입력 바인딩](#token-input).
- Excel에서 데이터를 읽을 수 있게 해주는 [Excel 테이블 입력 바인딩](#excel-input).
- Excel 데이터를 수정할 수 있게 해주는 [Excel 테이블 출력 바인딩](#excel-output).
- OneDrive에서 파일을 읽을 수 있게 해주는 [OneDrive 파일 입력 바인딩](#onedrive-input).
- OneDrive에 파일을 쓸 수 있게 해주는 [OneDrive 파일 출력 바인딩](#onedrive-output).
- Outlook을 통해 전자 메일을 보낼 수 있게 해주는 [Outlook 메시지 출력 바인딩](#outlook-output).
- Microsoft Graph의 이벤트에 대응할 수 있게 해주는 [Microsoft Graph 웹후크 트리거 및 바인딩](#webhooks) 컬렉션.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!Note] 
> Microsoft Graph 바인딩은 현재 미리 보기로 제공됩니다.

## <a name="setting-up-the-extensions"></a>확장 설정

Microsoft Graph 바인딩은 _바인딩 확장_을 통해 제공됩니다. 바인딩 확장은 Azure Functions 런타임의 선택적 구성 요소입니다. 이 섹션에서는 Microsoft Graph 및 인증 토큰 확장을 설정하는 방법을 설명합니다.

### <a name="enabling-functions-20-preview"></a>Functions 2.0 미리 보기를 사용하도록 설정

바인딩 확장은 Azure Functions 2.0 미리 보기에만 사용할 수 있습니다. 

[!INCLUDE [functions-set-runtime-version](../../includes/functions-set-runtime-version.md)]

자세한 내용은 [Azure Functions 런타임 버전을 대상으로 지정하는 방법](functions-versions.md)을 참조하세요.

### <a name="installing-the-extension"></a>확장 설치

Azure Portal에서 확장을 설치하려면 확장을 참조하는 템플릿 또는 바인딩으로 이동해야 합니다. 새 함수를 만들고, 템플릿 선택 화면에서 "Microsoft Graph" 시나리오를 선택합니다. 이 시나리오의 템플릿 중 하나를 선택합니다. 또는 기존 함수의 "통합" 탭으로 이동하여 이 토픽에서 다루는 바인딩 중 하나를 선택할 수도 있습니다.

어떤 방법을 선택하든 설치할 확장을 지정하는 경고가 표시됩니다. **설치**를 클릭하여 확장을 가져옵니다.

> [!Note] 
> 각 확장을 함수 앱마다 한 번씩만 설치하면 됩니다. 포털 내 설치 프로세스는 소비 계획에 대해 최대 10분이 소요될 수 있습니다.

Visual Studio를 사용하는 경우 이러한 NuGet 패키지를 설치하여 확장을 얻을 수 있습니다.
- [Microsoft.Azure.WebJobs.Extensions.AuthTokens](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.AuthTokens/)
- [Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph/)

### <a name="configuring-app-service-authentication--authorization"></a>App Service 인증/권한 부여 구성

이 토픽에서 설명하는 바인딩은 반드시 ID를 사용해야 합니다. 이를 통해 Microsoft Graph가 권한을 적용하고 상호 작용을 감사할 수 있습니다. ID는 응용 프로그램에 액세스하는 사용자일 수도 있고 응용 프로그램 자체일 수도 있습니다. 이 ID를 구성하려면 Azure Active Directory를 사용하여 [App Service 인증/권한 부여](https://docs.microsoft.com/azure/app-service/app-service-authentication-overview)를 설정해야 합니다. 그리고 함수에 필요한 리소스 권한을 요청해야 합니다.

> [!Note] 
> Microsoft Graph 확장은 AAD 인증만 지원합니다. 사용자는 회사 또는 학교 계정으로 로그인해야 합니다.

Azure Portal을 사용하는 경우 확장을 설치하라는 프롬프트 아래에 App Service 인증/권한 부여를 구성하고 템플릿 또는 바인딩에 필요한 권한을 요청하라는 경고 메시지가 표시됩니다. **지금 AAD 구성** 또는 **지금 권한 추가**를 적절히 클릭합니다.






<a name="token-input"></a>
## <a name="auth-token-input-binding"></a>인증 토큰 입력 바인딩

이 바인딩은 지정된 리소스에 대한 AAD 토큰을 획득하여 코드에 문자열로 제공합니다. 응용 프로그램이 권한을 갖고 있는 모든 것을 리소스로 사용할 수 있습니다. 

### <a name="configuring-an-auth-token-input-binding"></a>인증 토큰 입력 바인딩 구성

바인딩 자체는 AAD 권한이 전혀 필요 없지만, 토큰이 사용되는 방식에 따라 추가 권한을 요청해야 할 수도 있습니다. 토큰을 사용하여 액세스하려는 리소스의 요구 사항을 확인하세요.

바인딩에서 지원하는 속성은 다음과 같습니다.

|속성|설명|
|--------|--------|
|**name**|필수 - 인증 토큰의 함수 코드에 사용되는 변수 이름입니다. [코드에서 인증 토큰 입력 바인딩 사용](#token-input-code)을 참조하세요.|
|**type**|필수 - `token`으로 설정해야 합니다.|
|**direction**|필수 - `in`으로 설정해야 합니다.|
|**identity**|필수 - 작업 수행에 사용되는 ID입니다. 다음 값 중 하나입니다.<ul><li><code>userFromRequest</code> - [HTTP 트리거]에만 유효합니다. 호출하는 사용자의 ID를 사용합니다.</li><li><code>userFromId</code> - 지정된 ID를 사용하여 이전에 로그인한 사용자의 ID를 사용합니다. <code>userId</code> 속성을 참조하세요.</li><li><code>userFromToken</code> - 지정된 토큰으로 표시된 ID를 사용합니다. <code>userToken</code> 속성을 참조하세요.</li><li><code>clientCredentials</code> - 함수 앱의 ID를 사용합니다.</li></ul>|
|**userId** |_identity_가 `userFromId`로 설정된 경우에만 필요합니다. 이전에 로그인한 사용자와 연결된 사용자 계정 ID입니다.|
|**userToken**|_identity_가 `userFromToken`으로 설정된 경우에만 필요합니다. 함수 앱에 유효한 토큰입니다. |
|**resource**|필수 - 토큰이 요청되는 AAD 리소스 URL입니다.|

<a name="token-input-code"></a>
### <a name="using-an-auth-token-input-binding-from-code"></a>코드에서 인증 토큰 입력 바인딩 사용

토큰은 항상 코드에 문자열로 표시됩니다.

#### <a name="sample-getting-user-profile-information"></a>샘플: 사용자 프로필 정보 가져오기

토큰 입력 바인딩을 사용하여 HTTP 트리거를 정의하는 다음과 같은 function.json이 있다고 가정해 봅시다.

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "token",
      "direction": "in",
      "name": "graphToken",
      "resource": "https://graph.microsoft.com",
      "identity": "userFromRequest"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

다음 C# 샘플은 토큰을 사용하여 Microsoft Graph에 대한 HTTP 호출을 만들고 그 결과를 반환합니다.

```csharp
using System.Net; 
using System.Net.Http; 
using System.Net.Http.Headers; 

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, string graphToken, TraceWriter log)
{
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", graphToken);
    return await client.GetAsync("https://graph.microsoft.com/v1.0/me/");
}
```

다음 JS 샘플은 토큰을 사용하여 Microsoft Graph에 대한 HTTP 호출을 만들고 그 결과를 반환합니다. 위의 `function.json`에서, 가장 먼저 `$return`을 `res`로 변경합니다.

```js
const rp = require('request-promise');

module.exports = function (context, req) {
    let token = "Bearer " + context.bindings.graphToken;

    let options = {
        uri: 'https://graph.microsoft.com/v1.0/me/',
        headers: {
            'Authorization': token
        }
    };
    
    rp(options)
        .then(function(profile) {
            context.res = {
                body: profile
            };
            context.done();
        })
        .catch(function(err) {
            context.res = {
                status: 500,
                body: err
            };
            context.done();
        });
};
```





<a name="excel-input"></a>
## <a name="excel-table-input-binding"></a>Excel 테이블 입력 바인딩

이 바인딩은 OneDrive에 저장된 Excel 테이블의 콘텐츠를 읽습니다.

### <a name="configuring-an-excel-table-input-binding"></a>Excel 테이블 입력 바인딩 구성

이 바인딩에는 다음 AAD 권한이 필요합니다.
|리소스|사용 권한|
|--------|--------|
|Microsoft Graph|사용자 파일 읽기|

바인딩에서 지원하는 속성은 다음과 같습니다.

|속성|설명|
|--------|--------|
|**name**|필수 - Excel 테이블의 함수 코드에 사용되는 변수 이름입니다. [코드에서 Excel 테이블 입력 바인딩 사용](#excel-input-code)을 참조하세요.|
|**type**|필수 - `excel`으로 설정해야 합니다.|
|**direction**|필수 - `in`으로 설정해야 합니다.|
|**identity**|필수 - 작업 수행에 사용되는 ID입니다. 다음 값 중 하나입니다.<ul><li><code>userFromRequest</code> - [HTTP 트리거]에만 유효합니다. 호출하는 사용자의 ID를 사용합니다.</li><li><code>userFromId</code> - 지정된 ID를 사용하여 이전에 로그인한 사용자의 ID를 사용합니다. <code>userId</code> 속성을 참조하세요.</li><li><code>userFromToken</code> - 지정된 토큰으로 표시된 ID를 사용합니다. <code>userToken</code> 속성을 참조하세요.</li><li><code>clientCredentials</code> - 함수 앱의 ID를 사용합니다.</li></ul>|
|**userId** |_identity_가 `userFromId`로 설정된 경우에만 필요합니다. 이전에 로그인한 사용자와 연결된 사용자 계정 ID입니다.|
|**userToken**|_identity_가 `userFromToken`으로 설정된 경우에만 필요합니다. 함수 앱에 유효한 토큰입니다. |
|**path**|필수 - OneDrive에서 Excel 통합 문서의 경로입니다.|
|**worksheetName**|테이블이 검색되는 워크시트입니다.|
|**tableName**|테이블의 이름입니다. 지정하지 않으면 워크시트의 콘텐츠가 사용됩니다.|

<a name="excel-input-code"></a>
### <a name="using-an-excel-table-input-binding-from-code"></a>코드에서 Excel 테이블 입력 바인딩 사용

이 바인딩은 .NET 함수에 다음 형식을 노출합니다.
- string[][]
- Microsoft.Graph.WorkbookTable
- 사용자 지정 개체 형식(구조 모델 바인딩 사용)

#### <a name="sample-reading-an-excel-table"></a>샘플: Excel 테이블 읽기

Excel 입력 바인딩을 사용하여 HTTP 트리거를 정의하는 다음과 같은 function.json이 있다고 가정해 봅시다.

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "excel",
      "direction": "in",
      "name": "excelTableData",
      "path": "{query.workbook}",
      "identity": "UserFromRequest",
      "tableName": "{query.table}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

다음 C# 샘플은 지정된 테이블의 내용을 읽어서 사용자에게 반환합니다.

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives; 

public static IActionResult Run(HttpRequest req, string[][] excelTableData, TraceWriter log)
{
    return new OkObjectResult(excelTableData);
}
```

다음 JS 샘플은 지정된 테이블의 내용을 읽어서 사용자에게 반환합니다. 위의 `function.json`에서, 가장 먼저 `$return`을 `res`로 변경합니다.

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.excelTableData
    };
    context.done();
};
```

<a name="excel-output"></a>
## <a name="excel-table-output-binding"></a>Excel 테이블 출력 바인딩

이 바인딩은 OneDrive에 저장된 Excel 테이블의 콘텐츠를 수정합니다.

### <a name="configuring-an-excel-table-output-binding"></a>Excel 테이블 출력 바인딩 구성

이 바인딩에는 다음 AAD 권한이 필요합니다.
|리소스|사용 권한|
|--------|--------|
|Microsoft Graph|사용자 파일에 대한 전체 액세스 권한이 있음|

바인딩에서 지원하는 속성은 다음과 같습니다.

|속성|설명|
|--------|--------|
|**name**|필수 - 인증 토큰의 함수 코드에 사용되는 변수 이름입니다. [코드에서 Excel 테이블 출력 바인딩 사용](#excel-output-code)을 참조하세요.|
|**type**|필수 - `excel`으로 설정해야 합니다.|
|**direction**|필수 - `out`으로 설정해야 합니다.|
|**identity**|필수 - 작업 수행에 사용되는 ID입니다. 다음 값 중 하나입니다.<ul><li><code>userFromRequest</code> - [HTTP 트리거]에만 유효합니다. 호출하는 사용자의 ID를 사용합니다.</li><li><code>userFromId</code> - 지정된 ID를 사용하여 이전에 로그인한 사용자의 ID를 사용합니다. <code>userId</code> 속성을 참조하세요.</li><li><code>userFromToken</code> - 지정된 토큰으로 표시된 ID를 사용합니다. <code>userToken</code> 속성을 참조하세요.</li><li><code>clientCredentials</code> - 함수 앱의 ID를 사용합니다.</li></ul>|
|**userId** |_identity_가 `userFromId`로 설정된 경우에만 필요합니다. 이전에 로그인한 사용자와 연결된 사용자 계정 ID입니다.|
|**userToken**|_identity_가 `userFromToken`으로 설정된 경우에만 필요합니다. 함수 앱에 유효한 토큰입니다. |
|**path**|필수 - OneDrive에서 Excel 통합 문서의 경로입니다.|
|**worksheetName**|테이블이 검색되는 워크시트입니다.|
|**tableName**|테이블의 이름입니다. 지정하지 않으면 워크시트의 콘텐츠가 사용됩니다.|
|**updateType**|필수 - 테이블에 적용해야 하는 변경 형식입니다. 다음 값 중 하나입니다.<ul><li><code>update</code> - OneDrive에 있는 테이블의 콘텐츠를 대체합니다.</li><li><code>append</code> - 새 행을 만들어서 OneDrive에 있는 테이블의 끝에 페이로드를 추가합니다.</li></ul>|

<a name="excel-output-code"></a>
### <a name="using-an-excel-table-output-binding-from-code"></a>코드에서 Excel 테이블 출력 바인딩 사용

이 바인딩은 .NET 함수에 다음 형식을 노출합니다.
- string[][]
- Newtonsoft.Json.Linq.JObject
- Microsoft.Graph.WorkbookTable
- 사용자 지정 개체 형식(구조 모델 바인딩 사용)

#### <a name="sample-adding-rows-to-an-excel-table"></a>샘플: Excel 테이블에 행 추가

Excel 출력 바인딩을 사용하여 HTTP 트리거를 정의하는 다음과 같은 function.json이 있다고 가정해 봅시다.

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "newExcelRow",
      "type": "excel",
      "direction": "out",
      "identity": "userFromRequest",
      "updateType": "append",
      "path": "{query.workbook}",
      "tableName": "{query.table}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```


다음 C# 샘플은 쿼리 문자열의 입력을 기반으로 테이블에(단일 열로 가정) 새 행을 추가합니다.

```csharp
using System.Net;
using System.Text;

public static async Task Run(HttpRequest req, IAsyncCollector<object> newExcelRow, TraceWriter log)
{
    string input = req.Query
        .FirstOrDefault(q => string.Compare(q.Key, "text", true) == 0)
        .Value;
    await newExcelRow.AddAsync(new {
        Text = input
        // Add other properties for additional columns here
    });
    return;
}
```

다음 JS 샘플은 쿼리 문자열의 입력을 기반으로 테이블에(단일 열로 가정) 새 행을 추가합니다. 위의 `function.json`에서, 가장 먼저 `$return`을 `res`로 변경합니다.

```js
module.exports = function (context, req) {
    context.bindings.newExcelRow = {
        text: req.query.text
        // Add other properties for additional columns here
    }
    context.done();
};
```




<a name="onedrive-input"></a>
## <a name="onedrive-file-input-binding"></a>OneDrive 파일 입력 바인딩

이 바인딩은 OneDrive에 저장된 파일의 콘텐츠를 읽습니다.

### <a name="configuring-a-onedrive-file-input-binding"></a>OneDrive 파일 입력 바인딩 구성

이 바인딩에는 다음 AAD 권한이 필요합니다.
|리소스|사용 권한|
|--------|--------|
|Microsoft Graph|사용자 파일 읽기|

바인딩에서 지원하는 속성은 다음과 같습니다.

|속성|설명|
|--------|--------|
|**name**|필수 - 파일의 함수 코드에 사용되는 변수 이름입니다. [코드에서 OneDrive 파일 입력 바인딩 사용](#onedrive-input-code)을 참조하세요.|
|**type**|필수 - `onedrive`으로 설정해야 합니다.|
|**direction**|필수 - `in`으로 설정해야 합니다.|
|**identity**|필수 - 작업 수행에 사용되는 ID입니다. 다음 값 중 하나입니다.<ul><li><code>userFromRequest</code> - [HTTP 트리거]에만 유효합니다. 호출하는 사용자의 ID를 사용합니다.</li><li><code>userFromId</code> - 지정된 ID를 사용하여 이전에 로그인한 사용자의 ID를 사용합니다. <code>userId</code> 속성을 참조하세요.</li><li><code>userFromToken</code> - 지정된 토큰으로 표시된 ID를 사용합니다. <code>userToken</code> 속성을 참조하세요.</li><li><code>clientCredentials</code> - 함수 앱의 ID를 사용합니다.</li></ul>|
|**userId** |_identity_가 `userFromId`로 설정된 경우에만 필요합니다. 이전에 로그인한 사용자와 연결된 사용자 계정 ID입니다.|
|**userToken**|_identity_가 `userFromToken`으로 설정된 경우에만 필요합니다. 함수 앱에 유효한 토큰입니다. |
|**path**|필수 - OneDrive에서 파일의 경로입니다.|

<a name="onedrive-input-code"></a>
### <a name="using-a-onedrive-file-input-binding-from-code"></a>코드에서 OneDrive 파일 입력 바인딩 사용

이 바인딩은 .NET 함수에 다음 형식을 노출합니다.
- byte[]
- Stream
- string
- Microsoft.Graph.DriveItem

#### <a name="sample-reading-a-file-from-onedrive"></a>샘플: OneDrive에서 파일 읽기

OneDrive 입력 바인딩을 사용하여 HTTP 트리거를 정의하는 다음과 같은 function.json이 있다고 가정해 봅시다.

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "myOneDriveFile",
      "type": "onedrive",
      "direction": "in",
      "path": "{query.filename}",
      "identity": "userFromRequest"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

다음 C# 샘플은 쿼리 문자열에 지정된 파일을 읽고 그 길이를 기록합니다.

```csharp
using System.Net;

public static void Run(HttpRequestMessage req, Stream myOneDriveFile, TraceWriter log)
{
    log.Info(myOneDriveFile.Length.ToString());
}
```

다음 JS 샘플은 쿼리 문자열에 지정된 파일을 읽고 그 길이를 반환합니다. 위의 `function.json`에서, 가장 먼저 `$return`을 `res`로 변경합니다.

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.myOneDriveFile.length
    };
    context.done();
};
```


<a name="onedrive-output"></a>
## <a name="onedrive-file-output-binding"></a>OneDrive 파일 출력 바인딩

이 바인딩은 OneDrive에 저장된 파일의 콘텐츠를 수정합니다.

### <a name="configuring-a-onedrive-file-output-binding"></a>OneDrive 파일 출력 바인딩 구성

이 바인딩에는 다음 AAD 권한이 필요합니다.
|리소스|사용 권한|
|--------|--------|
|Microsoft Graph|사용자 파일에 대한 전체 액세스 권한이 있음|

바인딩에서 지원하는 속성은 다음과 같습니다.

|속성|설명|
|--------|--------|
|**name**|필수 - 파일의 함수 코드에 사용되는 변수 이름입니다. [코드에서 OneDrive 파일 출력 바인딩 사용](#onedrive-output-code)을 참조하세요.|
|**type**|필수 - `onedrive`으로 설정해야 합니다.|
|**direction**|필수 - `out`으로 설정해야 합니다.|
|**identity**|필수 - 작업 수행에 사용되는 ID입니다. 다음 값 중 하나입니다.<ul><li><code>userFromRequest</code> - [HTTP 트리거]에만 유효합니다. 호출하는 사용자의 ID를 사용합니다.</li><li><code>userFromId</code> - 지정된 ID를 사용하여 이전에 로그인한 사용자의 ID를 사용합니다. <code>userId</code> 속성을 참조하세요.</li><li><code>userFromToken</code> - 지정된 토큰으로 표시된 ID를 사용합니다. <code>userToken</code> 속성을 참조하세요.</li><li><code>clientCredentials</code> - 함수 앱의 ID를 사용합니다.</li></ul>|
|**userId** |_identity_가 `userFromId`로 설정된 경우에만 필요합니다. 이전에 로그인한 사용자와 연결된 사용자 계정 ID입니다.|
|**userToken**|_identity_가 `userFromToken`으로 설정된 경우에만 필요합니다. 함수 앱에 유효한 토큰입니다. |
|**path**|필수 - OneDrive에서 파일의 경로입니다.|

<a name="onedrive-output-code"></a>
### <a name="using-a-onedrive-file-output-binding-from-code"></a>코드에서 OneDrive 파일 출력 바인딩 사용

이 바인딩은 .NET 함수에 다음 형식을 노출합니다.
- byte[]
- Stream
- string
- Microsoft.Graph.DriveItem

#### <a name="sample-writing-to-a-file-in-onedrive"></a>샘플: OneDrive에서 파일에 쓰기

OneDrive 출력 바인딩을 사용하여 HTTP 트리거를 정의하는 다음과 같은 function.json이 있다고 가정해 봅시다.

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "myOneDriveFile",
      "type": "onedrive",
      "direction": "out",
      "path": "FunctionsTest.txt",
      "identity": "userFromRequest"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

다음 C# 샘플은 쿼리 문자열에서 텍스트를 가져와서 호출자의 OneDrive 루트에 있는 텍스트 파일(위의 config에 정의된 FunctionsTest.txt)에 기록합니다.

```csharp
using System.Net;
using System.Text;

public static async Task Run(HttpRequest req, TraceWriter log, Stream myOneDriveFile)
{
    string data = req.Query
        .FirstOrDefault(q => string.Compare(q.Key, "text", true) == 0)
        .Value;
    await myOneDriveFile.WriteAsync(Encoding.UTF8.GetBytes(data), 0, data.Length);
    return;
}
```
다음 JS 샘플은 쿼리 문자열에서 텍스트를 가져와서 호출자의 OneDrive 루트에 있는 텍스트 파일(위의 config에 정의된 FunctionsTest.txt)에 기록합니다. 위의 `function.json`에서, 가장 먼저 `$return`을 `res`로 변경합니다.

```js
module.exports = function (context, req) {
    context.bindings.myOneDriveFile = req.query.text;
    context.done();
};
```



<a name="outlook-output"></a>
## <a name="outlook-message-output-binding"></a>Outlook 메시지 출력 바인딩

Outlook을 통해 메일 메시지를 보냅니다.

### <a name="configuring-an-outlook-message-output-binding"></a>Outlook 메시지 출력 바인딩 구성

이 바인딩에는 다음 AAD 권한이 필요합니다.
|리소스|사용 권한|
|--------|--------|
|Microsoft Graph|사용자로 메일 보내기|

바인딩에서 지원하는 속성은 다음과 같습니다.

|속성|설명|
|--------|--------|
|**name**|필수 - 메일 메시지의 함수 코드에 사용되는 변수 이름입니다. [코드에서 Outlook 메시지 출력 바인딩 사용](#outlook-output-code)을 참조하세요.|
|**type**|필수 - `outlook`으로 설정해야 합니다.|
|**direction**|필수 - `out`으로 설정해야 합니다.|
|**identity**|필수 - 작업 수행에 사용되는 ID입니다. 다음 값 중 하나입니다.<ul><li><code>userFromRequest</code> - [HTTP 트리거]에만 유효합니다. 호출하는 사용자의 ID를 사용합니다.</li><li><code>userFromId</code> - 지정된 ID를 사용하여 이전에 로그인한 사용자의 ID를 사용합니다. <code>userId</code> 속성을 참조하세요.</li><li><code>userFromToken</code> - 지정된 토큰으로 표시된 ID를 사용합니다. <code>userToken</code> 속성을 참조하세요.</li><li><code>clientCredentials</code> - 함수 앱의 ID를 사용합니다.</li></ul>|
|**userId** |_identity_가 `userFromId`로 설정된 경우에만 필요합니다. 이전에 로그인한 사용자와 연결된 사용자 계정 ID입니다.|
|**userToken**|_identity_가 `userFromToken`으로 설정된 경우에만 필요합니다. 함수 앱에 유효한 토큰입니다. |

<a name="outlook-output-code"></a>
### <a name="using-an-outlook-message-output-binding-from-code"></a>코드에서 Outlook 메시지 출력 바인딩 사용

이 바인딩은 .NET 함수에 다음 형식을 노출합니다.
- Microsoft.Graph.Message
- Newtonsoft.Json.Linq.JObject
- string
- 사용자 지정 개체 형식(구조 모델 바인딩 사용)

#### <a name="sample-sending-an-email-through-outlook"></a>샘플: Outlook을 통해 전자 메일 보내기

Outlook 메시지 출력 바인딩을 사용하여 HTTP 트리거를 정의하는 다음과 같은 function.json이 있다고 가정해 봅시다.

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "message",
      "type": "outlook",
      "direction": "out",
      "identity": "userFromRequest"
    }
  ],
  "disabled": false
}
```

다음 C# 샘플은 호출자의 메일을 쿼리 문자열에 지정된 받는 사람에게 보냅니다.

```csharp
using System.Net;

public static void Run(HttpRequest req, out Message message, TraceWriter log)
{ 
    string emailAddress = req.Query["to"];
    message = new Message(){
        subject = "Greetings",
        body = "Sent from Azure Functions",
        recipient = new Recipient() {
            address = emailAddress
        }
    };
}

public class Message {
    public String subject {get; set;}
    public String body {get; set;}
    public Recipient recipient {get; set;}
}

public class Recipient {
    public String address {get; set;}
    public String name {get; set;}
}
```

다음 JS 샘플은 호출자의 메일을 쿼리 문자열에 지정된 받는 사람에게 보냅니다.

```js
module.exports = function (context, req) {
    context.bindings.message = {
        subject: "Greetings",
        body: "Sent from Azure Functions with JavaScript",
        recipient: {
            address: req.query.to 
        } 
    };
    context.done();
};
```





<a name="webhooks"></a>
## <a name="microsoft-graph-webhook-bindings"></a>Microsoft Graph 웹후크 바인딩

웹후크를 사용하여 Microsoft Graph의 이벤트에 대응할 수 있습니다. 웹후크를 지원하려면 함수를 만들고, 새로 고치고, _웹후크 구독_에 반응해야 합니다. 완전한 웹후크 솔루션을 구성하려면 다음과 같은 바인딩 조합이 필요합니다.
- 들어오는 웹후크에 대응할 수 있는 [Microsoft Graph 웹후크 트리거](#webhook-trigger).
- 기존 구독을 나열하고 필요에 따라 새로 고칠 수 있는 [Microsoft Graph 웹후크 구독 입력 바인딩](#webhook-input).
- 웹후크 구독을 만들거나 삭제할 수 있는 [Microsoft Graph 웹후크 구독 출력 바인딩](#webhook-output).

바인딩 자체는 AAD 권한이 필요 없지만, 대응하려는 리소스 종류와 관련된 권한을 요청해야 합니다. 각 리소스 종류에 필요한 권한 목록은 [구독 권한](https://developer.microsoft.com/en-us/graph/docs/api-reference/v1.0/api/subscription_post_subscriptions#permissions)을 참조하세요.

웹후크에 대한 자세한 내용은 [Microsoft Graph에서 웹후크 작업]을 참조하세요.





<a name="webhook-trigger"></a>
### <a name="microsoft-graph-webhook-trigger"></a>Microsoft Graph 웹후크 트리거

이 트리거를 사용하면 함수가 Microsoft Graph에서 들어오는 웹후크에 대응할 수 있습니다. 이 트리거의 각 인스턴스는 한 가지 Microsoft Graph 리소스 종류에 대응할 수 있습니다.

#### <a name="configuring-a-microsoft-graph-webhook-trigger"></a>Microsoft Graph 웹후크 트리거 구성

바인딩에서 지원하는 속성은 다음과 같습니다.

|속성|설명|
|--------|--------|
|**name**|필수 - 메일 메시지의 함수 코드에 사용되는 변수 이름입니다. [코드에서 Outlook 메시지 출력 바인딩 사용](#outlook-output-code)을 참조하세요.|
|**type**|필수 - `graphWebhook`으로 설정해야 합니다.|
|**direction**|필수 - `trigger`으로 설정해야 합니다.|
|**resourceType**|필수 - 이 함수가 웹후크에 응답해야 하는 그래프 리소스입니다. 다음 값 중 하나입니다.<ul><li><code>#Microsoft.Graph.Message</code> - Outlook 메시지의 변경 내용입니다.</li><li><code>#Microsoft.Graph.DriveItem</code> - OneDrive 루트 항목의 변경 내용입니다.</li><li><code>#Microsoft.Graph.Contact - changes made to personal contacts in Outlook.</code></li><li><code>#Microsoft.Graph.Event - changes made to Outlook calendar items.</code></li></ul>|

> [!Note]
> 함수 앱은 지정된 `resourceType` 값에 대해 등록된 함수 하나만 사용할 수 있습니다.

#### <a name="using-a-microsoft-graph-webhook-trigger-from-code"></a>코드에서 Microsoft Graph 웹후크 트리거 사용

이 바인딩은 .NET 함수에 다음 형식을 노출합니다.
- 리소스 종류와 관련된 Microsoft Graph SDK 형식(예: Microsoft.Graph.Message, Microsoft.Graph.DriveItem)
- 사용자 지정 개체 형식(구조 모델 바인딩 사용)

이 바인딩을 코드에 사용하는 예제는 [Microsoft Graph 웹후크 샘플](#webhook-samples)을 참조하세요.



<a name="webhook-input"></a>
### <a name="microsoft-graph-webhook-subscription-input-binding"></a>Microsoft Graph 웹후크 구독 입력 바인딩

이 바인딩을 사용하면 이 함수 앱이 관리하는 구독 목록을 검색할 수 있습니다. 이 바인딩은 함수 앱 저장소에서 정보를 읽으며, 앱 외부에서 작성된 다른 구독을 반영하지 않습니다.

#### <a name="configuring-a-microsoft-graph-webhook-subscription-input-binding"></a>Microsoft Graph 웹후크 구독 입력 바인딩 구성

바인딩에서 지원하는 속성은 다음과 같습니다.

|속성|설명|
|--------|--------|
|**name**|필수 - 메일 메시지의 함수 코드에 사용되는 변수 이름입니다. [코드에서 Outlook 메시지 출력 바인딩 사용](#outlook-output-code)을 참조하세요.|
|**type**|필수 - `graphWebhookSubscription`으로 설정해야 합니다.|
|**direction**|필수 - `in`으로 설정해야 합니다.|
|**filter**| `userFromRequest`로 설정하면 바인딩이 호출하는 사용자 소유의 구독만 검색합니다([HTTP 트리거]에만 유효).| 

#### <a name="using-a-microsoft-graph-webhook-subscription-input-binding-from-code"></a>코드에서 Microsoft Graph 웹후크 구독 입력 바인딩 사용

이 바인딩은 .NET 함수에 다음 형식을 노출합니다.
- string[]
- 사용자 지정 개체 형식 배열
- Newtonsoft.Json.Linq.JObject[]
- Microsoft.Graph.Subscription[]

이 바인딩을 코드에 사용하는 예제는 [Microsoft Graph 웹후크 샘플](#webhook-samples)을 참조하세요.


<a name="webhook-output"></a>
### <a name="microsoft-graph-webhook-subscription-output-binding"></a>Microsoft Graph 웹후크 구독 출력 바인딩

이 바인딩을 사용하면 Microsoft Graph에서 웹후크 구독을 만들고, 삭제하고, 새로 고칠 수 있습니다.

#### <a name="configuring-a-microsoft-graph-webhook-subscription-output-binding"></a>Microsoft Graph 웹후크 구독 출력 바인딩 구성

바인딩에서 지원하는 속성은 다음과 같습니다.

|속성|설명|
|--------|--------|
|**name**|필수 - 메일 메시지의 함수 코드에 사용되는 변수 이름입니다. [코드에서 Outlook 메시지 출력 바인딩 사용](#outlook-output-code)을 참조하세요.|
|**type**|필수 - `graphWebhookSubscription`으로 설정해야 합니다.|
|**direction**|필수 - `out`으로 설정해야 합니다.|
|**identity**|필수 - 작업 수행에 사용되는 ID입니다. 다음 값 중 하나입니다.<ul><li><code>userFromRequest</code> - [HTTP 트리거]에만 유효합니다. 호출하는 사용자의 ID를 사용합니다.</li><li><code>userFromId</code> - 지정된 ID를 사용하여 이전에 로그인한 사용자의 ID를 사용합니다. <code>userId</code> 속성을 참조하세요.</li><li><code>userFromToken</code> - 지정된 토큰으로 표시된 ID를 사용합니다. <code>userToken</code> 속성을 참조하세요.</li><li><code>clientCredentials</code> - 함수 앱의 ID를 사용합니다.</li></ul>|
|**userId** |_identity_가 `userFromId`로 설정된 경우에만 필요합니다. 이전에 로그인한 사용자와 연결된 사용자 계정 ID입니다.|
|**userToken**|_identity_가 `userFromToken`으로 설정된 경우에만 필요합니다. 함수 앱에 유효한 토큰입니다. |
|**action**|필수 - 바인딩이 수행해야 하는 작업을 지정합니다. 다음 값 중 하나입니다.<ul><li><code>create</code> - 새 구독을 등록합니다.</li><li><code>delete</code> - 지정된 구독을 삭제합니다.</li><li><code>refresh</code> - 구독이 만료되지 않도록 지정된 구독을 새로 고칩니다.</li></ul>|
|**subscriptionResource**|_action_이 `create`로 설정된 경우에만 필요합니다. 변경 내용이 모니터링될 Microsoft Graph 리소스를 지정합니다. [Microsoft Graph에서 웹후크 작업]을 참조하세요. |
|**changeType**|_action_이 `create`로 설정된 경우에만 필요합니다. 구독하는 리소스에서 알림을 발생시키는 변경 형식을 나타냅니다. 지원되는 값은 `created`, `updated`, `deleted`입니다. 쉼표로 구분된 목록을 사용하여 여러 값을 조합할 수 있습니다.|

#### <a name="using-a-microsoft-graph-webhook-subscription-output-binding-from-code"></a>코드에서 Microsoft Graph 웹후크 구독 출력 바인딩 사용

이 바인딩은 .NET 함수에 다음 형식을 노출합니다.
- string
- Microsoft.Graph.Subscription

이 바인딩을 코드에 사용하는 예제는 [Microsoft Graph 웹후크 샘플](#webhook-samples)을 참조하세요.
 
<a name="webhook-samples"></a>
### <a name="microsoft-graph-webhook-samples"></a>Microsoft Graph 웹후크 샘플

#### <a name="sample-creating-a-subscription"></a>샘플: 구독 만들기

만들기 작업을 사용하여 구독 출력 바인딩으로 HTTP 트리거를 정의하는 다음과 같은 function.json이 있다고 가정해 봅시다.

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "graphwebhook",
      "name": "clientState",
      "direction": "out",
      "action": "create",
      "listen": "me/mailFolders('Inbox')/messages",
      "changeTypes": [
        "created"
      ],
      "identity": "userFromRequest"
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

다음 C# 샘플은 호출하는 사용자가 Outlook 메시지를 받으면 이 함수 앱에 그 사실을 알리는 웹후크를 등록합니다.

```csharp
using System;
using System.Net;

public static HttpResponseMessage run(HttpRequestMessage req, out string clientState, TraceWriter log)
{
  log.Info("C# HTTP trigger function processed a request.");
    clientState = Guid.NewGuid().ToString();
    return new HttpResponseMessage(HttpStatusCode.OK);
}
```

다음 JS 샘플은 호출하는 사용자가 Outlook 메시지를 받으면 이 함수 앱에 그 사실을 알리는 웹후크를 등록합니다.

```js
const uuidv4 = require('uuid/v4');

module.exports = function (context, req) {
    context.bindings.clientState = uuidv4();
    context.done();
};
```

#### <a name="sample-handling-notifications"></a>샘플: 알림 처리

Outlook 메시지를 처리하는 Graph 웹후크 트리거를 정의하는 다음과 같은 function.json이 있다고 가정해 봅시다.

```json
{
  "bindings": [
    {
      "name": "msg",
      "type": "GraphWebhookTrigger",
      "direction": "in",
      "resourceType": "#Microsoft.Graph.Message"
    }
  ],
  "disabled": false
}
```

다음 C# 샘플은 들어오는 메일 메시지에 반응하여 받는 사람이 보냈고 제목에 "Azure Functions"가 포함된 메일 메시지의 본문을 기록합니다.

```csharp
#r "Microsoft.Graph"
using Microsoft.Graph;
using System.Net;

public static async Task Run(Message msg, TraceWriter log)  
{
    log.Info("Microsoft Graph webhook trigger function processed a request.");

    // Testable by sending oneself an email with the subject "Azure Functions" and some text body
    if (msg.Subject.Contains("Azure Functions") && msg.From.Equals(msg.Sender)) {
        log.Info($"Processed email: {msg.BodyPreview}");
    }
}
```

다음 JS 샘플은 들어오는 메일 메시지에 반응하여 받는 사람이 보냈고 제목에 "Azure Functions"가 포함된 메일 메시지의 본문을 기록합니다.

```js
module.exports = function (context) {
    context.log("Microsoft Graph webhook trigger function processed a request.");
    const msg = context.bindings.msg
    // Testable by sending oneself an email with the subject "Azure Functions" and some text body
    if((msg.subject.indexOf("Azure Functions") > -1) && (msg.from === msg.sender) ) {
      context.log(`Processed email: ${msg.bodyPreview}`);
    }
    context.done();
};
```

#### <a name="sample-deleting-subscriptions"></a>샘플: 구독 삭제

삭제 작업을 사용하여 구독 입력 바인딩 및 구독 출력 바인딩으로 HTTP 트리거를 정의하는 다음과 같은 function.json이 있다고 가정해 봅시다.

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "existingSubscriptions",
      "direction": "in",
      "filter": "userFromRequest"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "subscriptionsToDelete",
      "direction": "out",
      "action": "delete",
      "identity": "userFromRequest"
    },
    {
      "type": "http",
      "name": "res",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

다음 C# 샘플은 호출하는 사용자에 대한 모든 구독을 가져와서 삭제합니다.

```csharp
using System.Net;

public static async Task Run(HttpRequest req, string[] existingSubscriptions, IAsyncCollector<string> subscriptionsToDelete, TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");
    foreach (var subscription in existingSubscriptions)
    {
        log.Info($"Deleting subscription {subscription}");
        await subscriptionsToDelete.AddAsync(subscription);
    }
}
```

다음 JS 샘플은 호출하는 사용자에 대한 모든 구독을 가져와서 삭제합니다.

```js
module.exports = function (context, req) {
    const existing = context.bindings.existingSubscriptions;
    var toDelete = [];
    for (var i = 0; i < existing.length; i++) {
        context.log(`Deleting subscription ${existing[i]}`);
        todelete.push(existing[i]);
    }
    context.bindings.subscriptionsToDelete = toDelete;
    context.done();
};
```

#### <a name="sample-refreshing-subscriptions"></a>샘플: 구독 새로 고침

구독을 새로 고치는 두 가지 방법이 있습니다.
- 응용 프로그램 ID를 사용하여 모든 구독을 처리하는 방법. 이렇게 하려면 Azure Active Directory 관리자의 동의가 필요합니다. 이 방법은 Azure Functions에서 지원하는 모든 언어에 사용할 수 있습니다.
- 각 사용자 ID를 수동으로 바인딩하여 각 구독에 연결된 ID를 사용하는 방법. 이렇게 하려면 일부 사용자 지정 코드에서 바인딩을 수행해야 합니다. 이 방법은 .NET 함수에만 사용할 수 있습니다.

아래에는 두 옵션이 모두 나와 있습니다.

**응용 프로그램 ID 사용**

응용 프로그램 ID를 사용하여 구독 입력 바인딩 및 구독 출력 바인딩으로 타이머 트리거를 정의하는 다음과 같은 function.json이 있다고 가정해 봅시다.

```json
{
  "bindings": [
    {
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 * * */2 * *"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "existingSubscriptions",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "subscriptionsToRefresh",
      "direction": "out",
      "action": "refresh",
      "identity": "clientCredentials"
    }
  ],
  "disabled": false
}
```

다음 C# 샘플은 응용 프로그램 ID를 사용하여 타이머의 구독을 새로 고칩니다.

```csharp
using System;

public static void Run(TimerInfo myTimer, string[] existingSubscriptions, ICollector<string> subscriptionsToRefresh, TraceWriter log)
{
    // This template uses application permissions and requires consent from an Azure Active Directory admin.
    // See https://go.microsoft.com/fwlink/?linkid=858780
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
    foreach (var subscription in existingSubscriptions)
    {
      log.Info($"Refreshing subscription {subscription}");
      subscriptionsToRefresh.Add(subscription);
    }
}
```

다음 JS 샘플은 응용 프로그램 ID를 사용하여 타이머의 구독을 새로 고칩니다.

```js
// This template uses application permissions and requires consent from an Azure Active Directory admin.
// See https://go.microsoft.com/fwlink/?linkid=858780

module.exports = function (context) {
    const existing = context.bindings.existingSubscriptions;
    var toRefresh = [];
    for (var i = 0; i < existing.length; i++) {
        context.log(`Deleting subscription ${existing[i]}`);
        todelete.push(existing[i]);
    }
    context.bindings.subscriptionsToRefresh = toRefresh;
    context.done();
};
```

**동적 사용자 ID 사용**

다른 옵션을 찾기 위해, 타이머 트리거를 정의하고 구독 입력 바인딩에 대한 바인딩을 함수 코드로 연기하는 다음과 같은 function.json이 있다고 가정해 봅시다.

```json
{
  "bindings": [
    {
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 * * */2 * *"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "existingSubscriptions",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

다음 C# 샘플은 코드에서 출력 바인딩을 만들어 각 사용자의 ID를 사용하여 타이머의 구독을 새로 고칩니다.
```csharp
using System;

public static async Task Run(TimerInfo myTimer, UserSubscription[] existingSubscriptions, IBinder binder, TraceWriter log)
{
  log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
    foreach (var subscription in existingSubscriptions)
    {
        // binding in code to allow dynamic identity
        using (var subscriptionsToRefresh = await binder.BindAsync<IAsyncCollector<string>>(
            new GraphWebhookSubscriptionAttribute() {
                Action = "refresh",
                Identity = "userFromId",
                UserId = subscription.UserId
            }
        ))
        {
            log.Info($"Refreshing subscription {subscription}");
            await subscriptionsToRefresh.AddAsync(subscription);
        }

    }
}

public class UserSubscription {
    public string UserId {get; set;}
    public string Id {get; set;}
}
```



[HTTP 트리거]: functions-bindings-http-webhook.md
[Microsoft Graph에서 웹후크 작업]: https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/webhooks
