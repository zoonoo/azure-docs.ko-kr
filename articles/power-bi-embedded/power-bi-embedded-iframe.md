---
title: REST에서 Power BI Embedded를 사용하는 방법 | Microsoft Docs
description: 'REST에서 Power BI Embedded를 사용하는 방법 알아보기  '
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
# <a name="how-to-use-power-bi-embedded-with-rest"></a>REST에서 Power BI Embedded를 사용하는 방법
## <a name="power-bi-embedded:-what-it-is-and-what-it's-for"></a>Power BI Embedded: 정의 및 용도
Power BI Embedded의 개요는 공식적인 [Power BI Embedded 사이트](https://azure.microsoft.com/services/power-bi-embedded/)에 설명되어 있으나 REST에서 사용하는 방법을 자세히 살펴보기 전에 간단히 확인해보겠습니다.

실제로를 상당히 간단합니다. 종종 ISV에서는 자체 응용 프로그램에서 [Power BI](https://powerbi.microsoft.com) 의 동적 데이터 시각화를 UI 구성 요소로 사용하려고 합니다.

그렇지만 아시다시피 Power BI 보고서 또는 타일을 웹 페이지에 포함하는 작업은 **Power BI API**를 사용하여 Power BI Embedded Azure 서비스 없이도 이미 가능합니다. 동일한 조직에서 보고서를 공유하려는 경우 Azure AD 인증에 보고서를 포함할 수 있습니다. 보고서를 보려는 사용자는 자신의 Azure AD 계정을 사용하여 로그인해야 합니다. 모든 사용자(외부 사용자 포함)와 보고서를 공유하려는 경우 익명 액세스를 사용하여 간단히 포함할 수 있습니다.

하지만 아시다시피 이 간단한 포함 솔루션은 ISV 응용 프로그램의 요구를 충족하지 않습니다.
대부분의 ISV 응용 프로그램은 반드시 자체 조직의 사용자가 아닌 자신의 고객에게 데이터를 전달해야 합니다. 예를 들어 회사 A와 B 둘 다에게 서비스를 제공하는 경우 회사 A의 사용자에게는 자신의 회사 A의 데이터만 보입니다. 즉, 이러한 전달을 위해서는 다중 테넌트가 필요합니다.

ISV 응용 프로그램은 자체 인증 방법(예: 폼 인증, 기본 인증 등)을 제공할 수도 있습니다. 그러면 포함 솔루션은 이러한 기존 인증 방법과 공동으로 안전하게 작동될 수 있습니다. 또한 사용자들이 Power BI 구독을 추가로 구입하거나 라이선스가 없는 상태로 해당 ISV 응용 프로그램을 사용할 수 있도록 해야 합니다.

 **Power BI Embedded** 는 정확하게 이러한 종류의 ISV 시나리오를 위한 것입니다. 지금까지 방법을 간단히 살펴보았으므로 좀 더 자세한 부분을 확인해 보겠습니다.

.NET\(C#) 또는 Node.js SDK를 사용하여 Power BI Embedded로 응용 프로그램을 손쉽게 빌드할 수 있습니다. 그러나 이 문서에서는 SDK 없이 Power BI의 HTTP 흐름\(AuthN 포함)에 대해 설명하려고 합니다. 이 흐름을 이해하면 **프로그래밍 언어를 사용하지 않고** 응용 프로그램을 빌드할 수 있으며 Power BI Embedded의 기본 사항을 깊이 있게 이해할 수 있게 됩니다.

## <a name="create-power-bi-workspace-collection,-and-get-access-key-\(provisioning)"></a>Power BI 작업 영역 컬렉션 만들기 및 선택키 가져오기\(프로비전)
Power BI Embedded는 Azure 서비스 중 하나입니다. Azure Portal을 사용하는 ISV에게만 사용 요금이 부과되고\(시간당 사용자 세션), 보고서를 보는 사용자에게는 요금이 부과되지 않고 Azure 구독조차 필요하지 않습니다.
응용 프로그램 개발을 시작하기 전에 Azure 포털을 사용하여 **Power BI 작업 영역 컬렉션** 을 만들어야 합니다.

Power BI Embedded의 각 작업 영역은 각 고객의 작업 영역(테넌트)이며, 각 작업 영역 컬렉션에 여러 작업 영역을 추가할 수 있습니다. 동일한 선택키가 각 작업 영역 컬렉션에서 사용됩니다. 실제로 작업 영역 컬렉션은 Power BI Embedded에 대한 보안 경계입니다.

![](media\\power-bi-embedded-iframe\\create-workspace.png)

작업 영역 컬렉션을 다 만들면 Azure 포털에서 선택키를 복사합니다.

![](media\\power-bi-embedded-iframe\\copy-access-key.png)

> [!NOTE]
> 또한 작업 영역 컬렉션을 프로비전하고 REST API를 통해 선택키를 가져올 수 있습니다. 자세한 내용은 [Power BI 리소스 공급자 API](https://msdn.microsoft.com/library/azure/mt712306.aspx)를 참조하세요.
> 
> 

## <a name="create-.pbix-file-with-power-bi-desktop"></a>Power BI Desktop으로 .pbix 파일 만들기
다음으로 포함할 데이터 연결 및 보고서를 만들어야 합니다.
이 작업의 경우 프로그래밍 또는 코드는 없습니다. Power BI Desktop만 사용합니다.
이 문서에서 Power BI Desktop을 사용하는 자세한 방법을 다루지는 않습니다. 이에 대한 추가 도움말이 필요한 경우 [Power BI Desktop 시작](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/)을 참조하세요. 예제에서는 [소매점 분석 샘플](https://powerbi.microsoft.com/documentation/powerbi-sample-datasets/)을 사용하게 됩니다.

![](media\\power-bi-embedded-iframe\\power-bi-desktop-1.png)

## <a name="create-a-power-bi-workspace"></a>Power BI 작업 영역 만들기
이제 프로비전 작업이 모두 완료되었으므로 REST API를 통해 작업 영역 컬렉션에서 고객의 작업 영역을 만들어 보겠습니다. 다음 HTTP POST 요청(REST)은 기존 작업 영역 컬렉션에 새 작업 영역을 만듭니다. 이 예제에서 작업 영역 컬렉션 이름은 **mypbiapp**입니다.
앞서 복사한 선택키를 **AppKey**로 설정합니다. 이 과정은 매우 간단한 인증입니다.

**HTTP 요청**

```
POST https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces
Authorization: AppKey MpaUgrTv5e...
```

**HTTP 응답**

```
HTTP/1.1 201 Created
Content-Type: application/json; odata.metadata=minimal; odata.streaming=true
Location: https://wabi-us-east2-redirect.analysis.windows.net/v1.0/collections/mypbiapp/workspaces
RequestId: 4220d385-2fb3-406b-8901-4ebe11a5f6da

{
  "@odata.context": "http://wabi-us-east2-redirect.analysis.windows.net/v1.0/collections/mypbiapp/$metadata#workspaces/$entity",
  "workspaceId": "32960a09-6366-4208-a8bb-9e0678cdbb9d",
  "workspaceCollectionName": "mypbiapp"
}
```

반환된 **workspaceId** 는 후속 API 호출에 사용됩니다. 응용 프로그램은 이 값을 유지해야 합니다.

## <a name="import-.pbix-file-into-the-workspace"></a>작업 영역으로 .pbix 파일 가져오기
각 작업 영역은 데이터 집합\(데이터 원본 설정 포함) 및 보고서가 있는 단일 Power BI Desktop 파일을 호스트할 수 있습니다. 아래 코드에 표시된 대로 .pbix 파일을 작업 영역으로 가져올 수 있습니다. 여기에서 볼 수 있듯이 http에서 MIME 다중 파트를 사용하여 .pbix 파일의 이진 내용을 업로드할 수 있습니다.

URI 조각 **32960a09-6366-4208-a8bb-9e0678cdbb9d**는 workspaceId이고 쿼리 매개 변수 **datasetDisplayName**은 만들 데이터 집합 이름입니다. 만든 데이터 집합에서 가져온 데이터, 데이터 원본에 대한 포인터 등의 모든 데이터 관련 아티팩트는 .pbix 파일에 포함됩니다.

```
POST https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/imports?datasetDisplayName=mydataset01
Authorization: AppKey MpaUgrTv5e...
Content-Type: multipart/form-data; boundary="A300testx"

--A300testx
Content-Disposition: form-data

{the content (binary) of .pbix file}
--A300testx--
```

이 가져오기 작업은 잠시 동안 실행될 수 있습니다. 완료되면 응용 프로그램은 가져오기 ID를 사용하여 작업 상태를 요청할 수 있습니다. 이 예제에서 가져오기 ID는 **4eec64dd-533b-47c3-a72c-6508ad854659**입니다.

```
HTTP/1.1 202 Accepted
Content-Type: application/json; charset=utf-8
Location: https://wabi-us-east2-redirect.analysis.windows.net/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/imports/4eec64dd-533b-47c3-a72c-6508ad854659?tenantId=myorg
RequestId: 658bd6b4-b68d-4ec3-8818-2a94266dc220

{"id":"4eec64dd-533b-47c3-a72c-6508ad854659"}
```

다음은 이 가져오기 ID를 사용하여 상태를 요청합니다.

```
GET https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/imports/4eec64dd-533b-47c3-a72c-6508ad854659
Authorization: AppKey MpaUgrTv5e...
```

작업이 완료되지 않으면 HTTP 응답은 다음과 같을 수 있습니다.

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
RequestId: 614a13a5-4de7-43e8-83c9-9cd225535136

{
  "id": "4eec64dd-533b-47c3-a72c-6508ad854659",
  "importState": "Publishing",
  "createdDateTime": "2016-07-19T07:36:06.227",
  "updatedDateTime": "2016-07-19T07:36:06.227",
  "name": "mydataset01"
}
```

작업이 완료되면 HTTP 응답은 다음과 같을 수 있습니다.

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
RequestId: eb2c5a85-4d7d-4cc2-b0aa-0bafee4b1606

{
  "id": "4eec64dd-533b-47c3-a72c-6508ad854659",
  "importState": "Succeeded",
  "createdDateTime": "2016-07-19T07:36:06.227",
  "updatedDateTime": "2016-07-19T07:36:06.227",
  "reports": [
    {
      "id": "2027efc6-a308-4632-a775-b9a9186f087c",
      "name": "mydataset01",
      "webUrl": "https://app.powerbi.com/reports/2027efc6-a308-4632-a775-b9a9186f087c",
      "embedUrl": "https://app.powerbi.com/appTokenReportEmbed?reportId=2027efc6-a308-4632-a775-b9a9186f087c"
    }
  ],
  "datasets": [
    {
      "id": "458e0451-7215-4029-80b3-9627bf3417b0",
      "name": "mydataset01",
      "tables": [
      ],
      "webUrl": "https://app.powerbi.com/datasets/458e0451-7215-4029-80b3-9627bf3417b0"
    }
  ],
  "name": "mydataset01"
}
```

## <a name="data-source-connectivity-\(and-multi-tenancy-of-data)"></a>데이터 원본 연결\(및 데이터의 다중 테넌트)
.pbix 파일의 거의 모든 아티팩트를 작업 영역으로 가져오지만 데이터 원본에 대한 자격 증명은 가져오지 않습니다. 결과적으로 **DirectQuery 모드**를 사용할 경우 포함된 보고서가 올바르게 표시될 수 없습니다. 하지만 **가져오기 모드**를 사용할 때는 기존에 가져온 데이터를 사용하여 보고서를 볼 수 있습니다. 이 경우 REST 호출을 통해 다음 단계를 사용하여 자격 증명을 설정해야 합니다.

먼저 게이트웨이 데이터 원본을 가져와야 합니다. 데이터 집합 **ID** 는 이전에 반환된 ID입니다.

**HTTP 요청**

```
GET https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/datasets/458e0451-7215-4029-80b3-9627bf3417b0/Default.GetBoundGatewayDatasources
Authorization: AppKey MpaUgrTv5e...
```

**HTTP 응답**

```
GET HTTP/1.1 200 OK
Content-Type: application/json; odata.metadata=minimal; odata.streaming=true
RequestId: 574b0b18-a6fa-46a6-826c-e65840cf6e15

{
  "@odata.context": "http://wabi-us-east2-redirect.analysis.windows.net/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/$metadata#gatewayDatasources",
  "value": [
    {
      "id": "5f7ee2e7-4851-44a1-8b75-3eb01309d0ea",
      "gatewayId": "ca17e77f-1b51-429b-b059-6b3e3e9685d1",
      "datasourceType": "Sql",
      "connectionDetails": "{\"server\":\"testserver.database.windows.net\",\"database\":\"testdb01\"}"
    }
  ]
}
```

반환된 게이트웨이 ID와 데이터 원본 ID를 사용하여\(반환된 결과의 이전 **gatewayId** 및 **id** 참조) 이 데이터 원본의 자격 증명을 다음과 같이 변경할 수 있습니다.

**HTTP 요청**

```
PATCH https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/gateways/ca17e77f-1b51-429b-b059-6b3e3e9685d1/datasources/5f7ee2e7-4851-44a1-8b75-3eb01309d0ea
Authorization: AppKey MpaUgrTv5e...
Content-Type: application/json; charset=utf-8

{
  "credentialType": "Basic",
  "basicCredentials": {
    "username": "demouser",
    "password": "P@ssw0rd"
  }
}
```

**HTTP 응답**

```
HTTP/1.1 200 OK
Content-Type: application/octet-stream
RequestId: 0e533c13-266a-4a9d-8718-fdad90391099
```

프로덕션 환경에서 REST API를 사용하여 작업 영역마다 다른 연결 문자열을 설정할 수도 있습니다. \(즉, 고객별로 데이터베이스를 분리할 수 있음).

다음에서는 REST를 통해 데이터 원본의 연결 문자열을 변경합니다.

```
POST https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/datasets/458e0451-7215-4029-80b3-9627bf3417b0/Default.SetAllConnections
Authorization: AppKey MpaUgrTv5e...
Content-Type: application/json; charset=utf-8

{
  "connectionString": "data source=testserver02.database.windows.net;initial catalog=testdb02;persist security info=True;encrypt=True;trustservercertificate=False"
}
```

또는 Power BI Embedded에서 행 수준 보안을 사용하고 한 보고서에서 각 사용자의 데이터를 구분할 수 있습니다. 결과적으로 .pbix\(UI 등)는 동일하지만 데이터 원본은 다른 각 고객 보고서를 프로비전할 수 있습니다.

> [!NOTE]
> **DirectQuery 모드** 대신 **가져오기 모드**를 사용하는 경우 API를 통해 모델을 새로 고칠 수 없습니다. 또한 Power BI 게이트웨이 통한 온-프레미스 데이터 원본은 아직 Power BI Embedded에서 지원되지 않습니다. 그러나 [Power BI 블로그](https://powerbi.microsoft.com/blog/) 에서 새로운 기능 및 앞으로 제공될 기능을 확인하는 것도 유용할 것입니다.
> 
> 

## <a name="authentication-and-hosting-(embedding)-reports-in-our-web-page"></a>웹 페이지의 인증 및 보고서 호스트(포함)
이전 REST API에서는 선택키 **AppKey** 자체를 권한 부여 헤더로 사용할 수 있습니다. 이러한 호출은 백 엔드 서버 쪽에서 처리될 수 있으므로 안전합니다.

그러나 웹 페이지에 보고서를 포함할 경우 이러한 종류의 보안 정보는 JavaScript를 사용하여 처리됩니다\(프런트 엔드). 그런 다음 권한 부여 헤더 값을 보호해야 합니다. 악의적인 사용자나 악의적인 코드가 선택키를 찾으면 이 키를 사용하여 모든 작업을 호출할 수 있습니다.

따라서 웹 페이지에 보고서를 포함할 경우 선택키 **AppKey**대신 계산된 토큰을 사용해야 합니다. 응용 프로그램은 클레임 및 계산된 디지털 서명으로 구성된 OAuth JWT\(JSON Web Token)를 만들어야 합니다. 아래와 같이 이 OAuth JWT는 점으로 구분된 인코딩된 문자열 토큰입니다.

![](media\\power-bi-embedded-iframe\\oauth-jwt.png)

먼저 나중에 서명되는 입력 값을 준비해야 합니다. 이 값은 다음 json의 base64 url 인코딩(rfc4648) 문자열이며, 점\(.) 문자로 구분됩니다. 나중에 보고서 ID를 가져오는 방법이 설명될 것입니다.

> [!NOTE]
> Power BI Embedded에서 RLS(행 수준 보안)를 사용하려는 경우 클레임에 **사용자 이름** 및 **역할**도 지정해야 합니다.
> 
> 

```
{
  "typ":"JWT",
  "alg":"HS256"
}
```

```
{
  "wid":"{workspace id}",
  "rid":"{report id}",
  "wcn":"{workspace collection name}",
  "iss":"PowerBISDK",
  "ver":"0.2.0",
  "aud":"https://analysis.windows.net/powerbi/api",
  "nbf":{start time of token expiration},
  "exp":{end time of token expiration}
}
```

다음으로, SHA256 알고리즘에 따라 base64로 인코딩된 HMAC\(서명) 문자열을 만들어야 합니다. 이 서명된 입력 값은 이전 문자열입니다.

마지막으로, 점\(.) 문자를 사용하여 입력 값 및 서명 문자열을 결합해야 합니다. 완료된 문자열은 보고서 포함을 위한 앱 토큰입니다. 악의적인 사용자가 앱 토큰을 발견해도 원래 선택키를 가져올 수는 없습니다. 이 앱 토큰은 신속하게 만료됩니다.

이러한 단계에 대한 PHP 예제는 다음과 같습니다.

```
<?php
// 1. power bi access key
$accesskey = "MpaUgrTv5e...";

// 2. construct input value
$token1 = "{" .
  "\"typ\":\"JWT\"," .
  "\"alg\":\"HS256\"" .
  "}";
$token2 = "{" .
  "\"wid\":\"32960a09-6366-4208-a8bb-9e0678cdbb9d\"," . // workspace id
  "\"rid\":\"2027efc6-a308-4632-a775-b9a9186f087c\"," . // report id
  "\"wcn\":\"mypbiapp\"," . // workspace collection name
  "\"iss\":\"PowerBISDK\"," .
  "\"ver\":\"0.2.0\"," .
  "\"aud\":\"https://analysis.windows.net/powerbi/api\"," .
  "\"nbf\":" . date("U") . "," .
  "\"exp\":" . date("U" , strtotime("+1 hour")) .
  "}";
$inputval = rfc4648_base64_encode($token1) .
  "." .
  rfc4648_base64_encode($token2);

// 3. get encoded signature
$hash = hash_hmac("sha256",
    $inputval,
    $accesskey,
    true);
$sig = rfc4648_base64_encode($hash);

// 4. show result (which is the apptoken)
$apptoken = $inputval . "." . $sig;
echo($apptoken);

// helper functions
function rfc4648_base64_encode($arg) {
  $res = $arg;
  $res = base64_encode($res);
  $res = str_replace("/", "_", $res);
  $res = str_replace("+", "-", $res);
  $res = rtrim($res, "=");
  return $res;
}
?>
```

## <a name="finally,-embed-the-report-into-the-web-page"></a>마지막으로 웹 페이지에 보고서를 포함합니다.
보고서를 포함하기 위해 다음 REST API를 사용하여 포함 URL과 보고서 **ID** 를 가져와야 합니다.

**HTTP 요청**

```
GET https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/reports
Authorization: AppKey MpaUgrTv5e...
```

**HTTP 응답**

```
HTTP/1.1 200 OK
Content-Type: application/json; odata.metadata=minimal; odata.streaming=true
RequestId: d4099022-405b-49d3-b3b7-3c60cf675958

{
  "@odata.context": "http://wabi-us-east2-redirect.analysis.windows.net/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/$metadata#reports",
  "value": [
    {
      "id": "2027efc6-a308-4632-a775-b9a9186f087c",
      "name": "mydataset01",
      "webUrl": "https://app.powerbi.com/reports/2027efc6-a308-4632-a775-b9a9186f087c",
      "embedUrl": "https://embedded.powerbi.com/appTokenReportEmbed?reportId=2027efc6-a308-4632-a775-b9a9186f087c",
      "isFromPbix": false
    }
  ]
}
```

이전 앱 토큰을 사용하여 웹앱에 보고서를 포함할 수 있습니다.
다음 샘플 코드를 보면 첫 번째 부분은 이전 예제와 동일합니다. 이 샘플의 두 번째 부분에서는 iframe의 **embedUrl**\(이전 결과 참조)를 표시하고 앱 토큰을 iframe에 게시합니다.

> [!NOTE]
> 보고서 ID 값을 원하는 값으로 변경해야 합니다. 또한 콘텐츠 관리 시스템의 버그로 인해 코드 샘플의 iframe 태그는 문자 그대로 읽힙니다. 이 샘플 코드를 복사하여 붙여넣는 경우 태그에서 대문자 텍스트를 제거합니다.
> 
> 

```
    <?php
    // 1. power bi access key
    $accesskey = "MpaUgrTv5e...";

    // 2. construct input value
    $token1 = "{" .
      "\"typ\":\"JWT\"," .
      "\"alg\":\"HS256\"" .
      "}";
    $token2 = "{" .
      "\"wid\":\"32960a09-6366-4208-a8bb-9e0678cdbb9d\"," . // workspace id
      "\"rid\":\"2027efc6-a308-4632-a775-b9a9186f087c\"," . // report id
      "\"wcn\":\"mypbiapp\"," . // workspace collection name
      "\"iss\":\"PowerBISDK\"," .
      "\"ver\":\"0.2.0\"," .
      "\"aud\":\"https://analysis.windows.net/powerbi/api\"," .
      "\"nbf\":" . date("U") . "," .
      "\"exp\":" . date("U" , strtotime("+1 hour")) .
      "}";
    $inputval = rfc4648_base64_encode($token1) .
      "." .
      rfc4648_base64_encode($token2);

    // 3. get encoded signature value
    $hash = hash_hmac("sha256",
        $inputval,
        $accesskey,
        true);
    $sig = rfc4648_base64_encode($hash);

    // 4. get apptoken
    $apptoken = $inputval . "." . $sig;

    // helper functions
    function rfc4648_base64_encode($arg) {
      $res = $arg;
      $res = base64_encode($res);
      $res = str_replace("/", "_", $res);
      $res = str_replace("+", "-", $res);
      $res = rtrim($res, "=");
      return $res;
    }
    ?>
    <!DOCTYPE html>
    <html>
    <head>
      <meta charset="utf-8" />
      <meta http-equiv="X-UA-Compatible" content="IE=edge">
      <title>Test page</title>
      <meta name="viewport" content="width=device-width, initial-scale=1">
    </head>
    <body>
      <button id="btnView">View Report !</button>
      <div id="divView">
        <**REMOVE THIS CAPPED TEXT IF COPIED** iframe id="ifrTile" width="100%" height="400"></iframe>
      </div>
      <script>
        (function () {
          document.getElementById('btnView').onclick = function() {
            var iframe = document.getElementById('ifrTile');
            iframe.src = 'https://embedded.powerbi.com/appTokenReportEmbed?reportId=2027efc6-a308-4632-a775-b9a9186f087c';
            iframe.onload = function() {
              var msgJson = {
                action: "loadReport",
                accessToken: "<?=$apptoken?>",
                height: 500,
                width: 722
              };
              var msgTxt = JSON.stringify(msgJson);
              iframe.contentWindow.postMessage(msgTxt, "*");
            };
          };
        }());
      </script>
    </body>
```

결과는 다음과 같습니다.

![](media\\power-bi-embedded-iframe\\view-report.png)

이번에는 Power BI Embedded에 iframe의 보고서만 표시됩니다. 그렇지만 [Power BI 블로그]()를 잘 확인해 보세요. 향후 개선된 기능에서는 정보를 가져올 수 있을 뿐만 아니라 iframe으로 정보를 보낼 수 있도록 하는 새 클라이언트 쪽 API가 사용될 수 있을 것입니다. 기대해 보세요.

## <a name="see-also"></a>참고 항목
* [Power BI Embedded에서 인증 및 권한 부여](power-bi-embedded-app-token-flow.md)

<!--HONumber=Oct16_HO2-->


