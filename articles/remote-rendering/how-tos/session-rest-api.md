---
title: 세션 관리 REST API
description: 세션 관리 방법에 대해 설명합니다.
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 46560f067e020236031487677ad4f48a9560d4e1
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681247"
---
# <a name="use-the-session-management-rest-api"></a>세션 관리 REST API 사용

Azure 원격 렌더링 기능을 사용하려면 *세션을*만들어야 합니다. 각 세션은 Azure에 할당되고 클라이언트 장치가 연결되기를 기다리는 VM(가상 시스템)에 해당합니다. 장치가 연결되면 VM은 요청된 데이터를 렌더링하고 결과를 비디오 스트림으로 제공합니다. 세션을 만드는 동안 실행할 서버 종류를 선택하여 가격을 결정합니다. 세션이 더 이상 필요하지 않은 후에는 세션을 중지해야 합니다. 수동으로 중지하지 않으면 세션의 *임대 시간이* 만료되면 자동으로 종료됩니다.

*렌더링Session.ps1이라는* *스크립트* 폴더의 [ARR 샘플 리포지토리에서](https://github.com/Azure/azure-remote-rendering) PowerShell 스크립트를 제공하여 서비스의 사용을 보여 줍니다. 스크립트와 구성은 여기에 [설명되어 있습니다.](../samples/powershell-example-scripts.md)

> [!TIP]
> 이 페이지에 나열된 PowerShell 명령은 서로를 보완하기 위한 것입니다. 동일한 PowerShell 명령 프롬프트 내에서 모든 스크립트를 순서대로 실행하면 스크립트가 서로 위에 빌드됩니다.

## <a name="regions"></a>영역

요청을 보낼 기본 [URL에](../reference/regions.md) 사용할 수 있는 지역 목록을 참조하세요.

아래의 샘플 스크립트에 대 한 지역 *westus2를*선택 했습니다.

### <a name="example-script-choose-an-endpoint"></a>예제 스크립트: 끝점 선택

```PowerShell
$endPoint = "https://remoterendering.westus2.mixedreality.azure.com"
```

## <a name="accounts"></a>계정

원격 렌더링 계정이 없는 경우 [하나를 만듭니다.](create-an-account.md) 각 리소스는 세션 API 전체에서 사용되는 *accountId로*식별됩니다.

### <a name="example-script-set-accountid-and-accountkey"></a>예제 스크립트: 계정 ID 및 계정 키 설정

```PowerShell
$accountId = "********-****-****-****-************"
$accountKey = "*******************************************="
```

## <a name="common-request-headers"></a>공통 요청 헤더

* *권한 부여* 헤더에는 보안`Bearer TOKEN`토큰 서비스에서`TOKEN` [반환되는](tokens.md)인증 토큰인 ", "" 값이 있어야 합니다.

### <a name="example-script-request-a-token"></a>예제 스크립트: 토큰 요청

```PowerShell
[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12
$webResponse = Invoke-WebRequest -Uri "https://sts.mixedreality.azure.com/accounts/$accountId/token" -Method Get -ContentType "application/json" -Headers @{ Authorization = "Bearer ${accountId}:$accountKey" }
$response = ConvertFrom-Json -InputObject $webResponse.Content
$token = $response.AccessToken;
```

## <a name="common-response-headers"></a>공통 응답 헤더

* *MS-CV* 헤더는 제품 팀에서 서비스 내에서 호출을 추적하는 데 사용할 수 있습니다.

## <a name="create-a-session"></a>세션 만들기

이 명령은 세션을 만듭니다. 새 세션의 ID를 반환합니다. 다른 모든 명령에 대한 세션 ID가 필요합니다.

| URI | 방법 |
|-----------|:-----------|
| /v1/계정/계정 ID/세션/만들기*accountId* | POST |

**요청 본문:**

* maxLeaseTime(시간 범위): VM이 자동으로 서비스 해제되는 시간 시간 값
* 모델(배열): 자산 컨테이너 URL을 미리 로드할 수 있습니다.
* 크기(문자열): VM**크기("표준"** 또는 **"프리미엄").** 특정 [VM 크기 제한 사항을](../reference/limits.md#overall-number-of-polygons)참조하십시오.

**응답:**

| 상태 코드 | JSON 페이로드 | 주석 |
|-----------|:-----------|:-----------|
| 202 | - 세션ID : GUID | Success |

### <a name="example-script-create-a-session"></a>예제 스크립트: 세션 만들기

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions/create" -Method Post -ContentType "application/json" -Body "{ 'maxLeaseTime': '4:0:0', 'models': [], 'size': 'standard' }" -Headers @{ Authorization = "Bearer $token" }
```

예제 출력:

```PowerShell
StatusCode        : 202
StatusDescription : Accepted
Content           : {"sessionId":"d31bddca-dab7-498e-9bc9-7594bc12862f"}
RawContent        : HTTP/1.1 202 Accepted
                    MS-CV: 5EqPJ1VdTUakDJZc6/ZhTg.0
                    Content-Length: 52
                    Content-Type: application/json; charset=utf-8
                    Date: Thu, 09 May 2019 16:17:50 GMT
                    Location: accounts/11111111-1111-1111-11...
Forms             : {}
Headers           : {[MS-CV, 5EqPJ1VdTUakDJZc6/ZhTg.0], [Content-Length, 52], [Content-Type, application/json;
                    charset=utf-8], [Date, Thu, 09 May 2019 16:17:50 GMT]...}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 52
```

### <a name="example-script-store-sessionid"></a>예제 스크립트: 저장소 세션ID

위의 요청의 응답에는 모든 후속 요청에 필요한 **sessionId가**포함됩니다.

```PowerShell
$sessionId = "d31bddca-dab7-498e-9bc9-7594bc12862f"
```

## <a name="update-a-session"></a>세션 업데이트

이 명령은 세션의 매개 변수를 업데이트합니다. 현재 세션의 임대 시간만 연장할 수 있습니다.

> [!IMPORTANT]
> 임대 시간은 세션이 시작된 이후의 총 시간으로 항상 제공됩니다. 즉, 한 시간의 임대 시간으로 세션을 만들고 다른 시간 동안 임대 시간을 연장하려면 maxLeaseTime을 2시간으로 업데이트해야 합니다.

| URI | 방법 |
|-----------|:-----------|
| /v1/계정/계정*accountID*ID/세션/세션ID*sessionId* | 패치 |

**요청 본문:**

* maxLeaseTime(시간 범위): VM이 자동으로 서비스 해제되는 시간 시간 값

**응답:**

| 상태 코드 | JSON 페이로드 | 주석 |
|-----------|:-----------|:-----------|
| 200 | | Success |

### <a name="example-script-update-a-session"></a>예제 스크립트: 세션 업데이트

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions/$sessionId" -Method Patch -ContentType "application/json" -Body "{ 'maxLeaseTime': '5:0:0' }" -Headers @{ Authorization = "Bearer $token" }
```

예제 출력:

```PowerShell
StatusCode        : 200
StatusDescription : OK
Content           : {}
RawContent        : HTTP/1.1 200 OK
                    MS-CV: Fe+yXCJumky82wuoedzDTA.0
                    Content-Length: 0
                    Date: Thu, 09 May 2019 16:27:31 GMT


Headers           : {[MS-CV, Fe+yXCJumky82wuoedzDTA.0], [Content-Length, 0], [Date, Thu, 09 May 2019 16:27:31 GMT]}
RawContentLength  : 0
```

## <a name="get-active-sessions"></a>활성 세션 받기

이 명령은 활성 세션 목록을 반환합니다.

| URI | 방법 |
|-----------|:-----------|
| /v1/계정/계정*ID/세션* | GET |

**응답:**

| 상태 코드 | JSON 페이로드 | 주석 |
|-----------|:-----------|:-----------|
| 200 | - 세션 : 세션 속성의 배열 | 세션 속성에 대한 설명은 "세션 속성 받기" 섹션을 참조하십시오. |

### <a name="example-script-query-active-sessions"></a>예제 스크립트: 활성 세션 쿼리

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions" -Method Get -Headers @{ Authorization = "Bearer $token" }
```

예제 출력:

```PowerShell
StatusCode        : 200
StatusDescription : OK
Content           : []
RawContent        : HTTP/1.1 200 OK
                    MS-CV: WfB9Cs5YeE6S28qYkp7Bhw.1
                    Content-Length: 15
                    Content-Type: application/json; charset=utf-8
                    Date: Thu, 25 Jul 2019 16:23:50 GMT

                    {"sessions":[]}
Forms             : {}
Headers           : {[MS-CV, WfB9Cs5YeE6S28qYkp7Bhw.1], [Content-Length, 2], [Content-Type, application/json;
                    charset=utf-8], [Date, Thu, 25 Jul 2019 16:23:50 GMT]}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 2
```

## <a name="get-sessions-properties"></a>세션 속성 받기

이 명령은 VM 호스트 이름과 같은 세션에 대한 정보를 반환합니다.

| URI | 방법 |
|-----------|:-----------|
| /v1/계정/계정*ID*/세션/세션ID/속성*sessionId* | GET |

**응답:**

| 상태 코드 | JSON 페이로드 | 주석 |
|-----------|:-----------|:-----------|
| 200 | - 메시지 : 문자열<br/>- 세션 경과 시간 : 시간 범위<br/>- 세션호스트이름: 문자열<br/>- 세션ID : 문자열<br/>- 세션맥스리스타임: 타임스팬<br/>- 세션크기: 열거형<br/>- 세션 상태 : 열거형 | 열거형 세션상태 { 시작, 준비, 중지, 중지, 만료됨, 오류}<br/>상태가 '오류' 또는 '만료됨'인 경우 메시지에 추가 정보가 포함됩니다. |

### <a name="example-script-get-session-properties"></a>예제 스크립트: 세션 속성 받기

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions/$sessionId/properties" -Method Get -Headers @{ Authorization = "Bearer $token" }
```

예제 출력:

```PowerShell
StatusCode        : 200
StatusDescription : OK
Content           : {"message":null,"sessionElapsedTime":"00:00:01","sessionHostname":"5018fee8-817e-4366-9179-556af79a4240.remoterenderingvm.westeurope.mixedreality.azure.com","sessionId":"e13d2c44-63e0-4591-991e-f9e05e599a93","sessionMaxLeaseTime":"04:00:00","sessionStatus":"Ready"}
RawContent        : HTTP/1.1 200 OK
                    MS-CV: CMXegpZRMECH4pbOW2j5GA.0
                    Content-Length: 60
                    Content-Type: application/json; charset=utf-8
                    Date: Thu, 09 May 2019 16:30:38 GMT

                    {"message":null,...
Forms             : {}
Headers           : {[MS-CV, CMXegpZRMECH4pbOW2j5GA.0], [Content-Length, 60], [Content-Type, application/json;
                    charset=utf-8], [Date, Thu, 09 May 2019 16:30:38 GMT]}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 60
```

## <a name="stop-a-session"></a>세션 중지

이 명령은 세션을 중지합니다. 할당된 VM은 곧 회수됩니다.

| URI | 방법 |
|-----------|:-----------|
| /v1/계정/계정*ID*/세션/세션ID*sessionId* | Delete |

**응답:**

| 상태 코드 | JSON 페이로드 | 주석 |
|-----------|:-----------|:-----------|
| 204 | | Success |

### <a name="example-script-stop-a-session"></a>예제 스크립트: 세션 중지

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions/$sessionId" -Method Delete -Headers @{ Authorization = "Bearer $token" }
```

예제 출력:

```PowerShell
StatusCode        : 204
StatusDescription : No Content
Content           : {}
RawContent        : HTTP/1.1 204 No Content
                    MS-CV: YDxR5/7+K0KstH54WG443w.0
                    Date: Thu, 09 May 2019 16:45:41 GMT


Headers           : {[MS-CV, YDxR5/7+K0KstH54WG443w.0], [Date, Thu, 09 May 2019 16:45:41 GMT]}
RawContentLength  : 0
```

## <a name="next-steps"></a>다음 단계

* [예제 PowerShell 스크립트](../samples/powershell-example-scripts.md)
