---
title: "호출 가능 끝점인 논리 앱 | Microsoft Docs"
description: "트리거 끝점을 만들어서 구성하고 Azure 논리 앱에서 사용하는 방법"
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: 73ba2a70-03e9-4982-bfc8-ebfaad798bc2
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/18/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: d7144208fc3e6eb1f8d3c43d8b4a5e2bcb225e58
ms.openlocfilehash: ac0c200abd110262badd04212c82be45cb0f8bfc
ms.lasthandoff: 02/22/2017


---
# <a name="logic-apps-as-callable-endpoints"></a>호출 가능 끝점인 논리 앱
논리 앱은 기본적으로 동기 HTTP 끝점을 트리거로 노출할 수 있습니다.  또한 호출 가능한 끝점 패턴을 사용하여 논리 앱의 "워크플로" 작업을 통해 중첩된 워크플로로 논리 앱을 호출할 수 있습니다.

요청을 받을 수 있는 트리거에는 3가지 유형이 있습니다.

* 요청
* ApiConnectionWebhook
* HttpWebhook

문서의 나머지 부분에서는 **request** 를 예로 사용하지만 모든 주체는 다른 두 가지 유형의 트리거에 동일하게 적용합니다.

## <a name="adding-a-trigger-to-your-definition"></a>정의에 트리거 추가
첫 번째 단계는 들어오는 요청을 받을 수 있는 논리 앱 정의에 트리거를 추가하는 것입니다.  디자이너에서 "HTTP 요청"을 검색하여 트리거 카드를 추가할 수 있습니다. 요청 본문 JSON 스키마를 정의하면 디자이너는 사용자가 워크플로를 통해 manual 트리거의 데이터를 구분 분석하고 전달할 수 있도록 토큰을 생성합니다.  [jsonschema.net](http://jsonschema.net) 같은 도구를 사용하여 샘플 본문 페이로드의 JSON 스키마를 생성하는 것이 좋습니다.

![요청 트리거 카드][2]

논리 앱 정의를 저장하면 다음과 비슷한 콜백 URL이 생성됩니다.

``` text
https://prod-03.eastus.logic.azure.com:443/workflows/080cb66c52ea4e9cabe0abf4e197deff/triggers/myendpointtrigger?*signature*...
```

이 URL은 인증에 사용되는 SAS 키를 쿼리 매개 변수에 포함합니다.

Azure 포털에서도 이 끝점을 가져올 수 있습니다.

![][1]

또는 다음을 호출합니다.

``` text
POST https://management.azure.com/{resourceID of your logic app}/triggers/myendpointtrigger/listCallbackURL?api-version=2015-08-01-preview
```

### <a name="changing-http-method-of-the-trigger"></a>트리거의 HTTP 메서드 변경
기본적으로 Logic Apps의 요청 트리거는 HTTP POST 요청을 사용합니다. 하지만 `Show advanced options`에서 HTTP 메서드를 구성할 수 있습니다.

 > [!NOTE]
 > 메서드의 유형이 하나만 허용됩니다.

### <a name="relative-trigger-url"></a>상대 트리거 URL
요청 URL의 상대 경로 사용자 지정하여 매개 변수를 수락할 수도 있습니다.

1. **요청** 트리거의 `Show advanced options`을 확장합니다.
 - `Relative path` 아래에서 `customer/{customerId}`를 입력합니다.

  ![상대 URL 트리거](./media/logic-apps-http-endpoint/relativeurl.png)

2. **응답** 작업을 업데이트하여 매개 변수의 사용자를 만듭니다.
 - 토큰 선택에 `customerId`이 표시되어야 합니다.
 - `Hello {customerId}`을 반환하도록 응답의 본문을 업데이트합니다.

  ![상대 URL 응답](./media/logic-apps-http-endpoint/relativeurlresponse.png)

3. Logic App을 저장하고 요청 URL 업데이트에 유의하여 상대 경로를 포함해야 합니다.

4. 새 요청 URL을 복사하고 새 브라우저 창에 붙여 넣습니다. `{customerId}`을 `123`로 바꾸고 Enter 키를 누릅니다.
 - 반환된 `Your customer Id is 123`이 표시되어야 합니다.

### <a name="security-for-the-trigger-url"></a>트리거 URL에 대한 보안
Logic App 콜백 URL은 공유 액세스 서명을 사용하여 안전하게 생성됩니다.  서명은 쿼리 매개 변수로 전달되고 논리 앱이 시작하기 전에 유효성이 검사되어야 합니다.  논리 앱, 트리거 이름 및 수행되는 작업 별로 암호 키의 고유한 조합을 사용하여 생성됩니다.  사용자가 암호 논리 앱 키에 액세스하지 않으면 유효한 서명을 생성할 수 없습니다.

## <a name="calling-the-logic-app-triggers-endpoint"></a>논리 앱 트리거의 끝점 호출
트리거의 끝점을 만든 후 `POST`를 통해 전체 URL로 트리거할 수 있습니다. 추가 헤더 및 모든 콘텐츠를 본문에 포함할 수 있습니다.

content-type이 `application/json`이면 요청 내에서 속성을 참조할 수 있습니다. 이 형식이 아니면 다른 API에 전달할 수 있지만 콘텐츠 변환 없이는 워크플로 내부에서 참조할 수 없는 단일 이진 단위로 처리됩니다.  예를 들어 `application/xml` 콘텐츠를 전달하는 경우 `@xpath()`를 사용하여 xpath 추출을 수행하거나 `@json()`를 사용하여 XML에서 JSON으로 변환할 수 있습니다.  콘텐츠 형식 사용에 대한 자세한 내용은 [여기에서 확인](../logic-apps/logic-apps-content-type.md)

뿐만 아니라 정의에서 JSON 스키마를 지정할 수 있습니다. 이렇게 하면 디자이너에서 단계에 전달할 수 있는 토큰을 생성합니다.  예를 들어 다음은 디자이너에서 사용할 수 있는 `title` 및 `name` 토큰을 만듭니다.

```
{
    "properties":{
        "title": {
            "type": "string"
        },
        "name": {
            "type": "string"
        }
    },
    "required": [
        "title",
        "name"
    ],
    "type": "object"
}
```

## <a name="referencing-the-content-of-the-incoming-request"></a>들어오는 요청의 콘텐츠 참조
`@triggerOutputs()` 함수는 들어오는 요청의 콘텐츠를 출력합니다. 예를 들어 다음과 같습니다.

```
{
    "headers" : {
        "content-type" : "application/json"
    },
    "body" : {
        "myprop" : "a value"
    }
}
```

특히 `body` 속성에 액세스하는 데 `@triggerBody()` 바로 가기를 사용할 수 있습니다. 

## <a name="responding-to-the-request"></a>요청에 응답
논리 앱을 시작하는 일부 요청의 경우 일부 콘텐츠를 사용하여 호출자에게 응답할 수 있습니다. 응답에 대한 상태 코드, 본문 및 헤더를 생성하는 데 사용할 수 있는 **response** 라는 새 동작 유형이 있습니다. **response** 셰이프가 없는 경우 논리 앱 끝점은 **202 수락됨**으로 *즉시* 응답합니다.

![HTTP 응답 동작][3]

``` json
"Response": {
            "conditions": [],
            "inputs": {
                "body": {
                    "name": "@{triggerBody()['name']}",
                    "title": "@{triggerBody()['title']}"
                },
                "headers": {
                    "content-type": "application/json"
                },
                "statusCode": 200
            },
            "type": "Response"
        }
```

응답은 다음과 같습니다.

| 속성 | 설명 |
| --- | --- |
| statusCode |들어오는 요청에 응답하는 HTTP 상태 코드입니다. 2xx, 4xx 또는 5xx로 시작하는 모든 유효한 상태 코드가 될 수 있습니다. 3xx 상태 코드는 허용되지 않습니다. |
| body |문자열, JSON 개체 또는 이전 단계에서 참조한 이진 콘텐츠일 수도 있는 본문 개체입니다. |
| headers |응답에 포함될 헤더의 수를 정의할 수 있습니다. |

*워크플로가 중첩 논리 앱으로 호출되지 않는 경우* , 원래 요청이 응답을 받으려면 응답에 필요한 논리 앱의 모든 단계가 **60초**이내에 완료되어야 합니다. 60초 내에 도달하는 응답 작업이 없으면 들어오는 요청은 시간 초과되어 **408 클라이언트 시간 제한** HTTP 응답을 수신합니다.  중첩 논리 앱의 경우 부모 논리 앱은 소요되는 시간에 관계없이 응답이 완료될 때까지 계속 기다립니다.

## <a name="advanced-endpoint-configuration"></a>고급 끝점 구성
논리 앱은 직접 액세스 끝점에 대한 지원이 내장되어 있으며 항상 `POST` 메서드를 사용하여 논리 앱을 실행합니다. 또한 **HTTP 수신기** API 앱은 이전에 URL 세그먼트 및 HTTP 메서드 변경을 지원했습니다. API 앱 호스트(API 앱을 호스트하는 웹앱)에 추가하여 추가 보안 또는 사용자 지정 도메인을 설정할 수도 있습니다. 

이 기능은 **API 관리**를 통해 사용할 수 있습니다.

* [요청 방법 변경](https://msdn.microsoft.com/library/azure/dn894085.aspx#SetRequestMethod)
* [요청의 URL 세그먼트 변경](https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#RewriteURL)
* 클래식 Azure 포털의 **구성** 탭에서 API 관리 도메인 설정
* 기본 인증을 확인하는 정책 설정(**링크 필요**)

## <a name="summary-of-migration-from-2014-12-01-preview"></a>2014-12-01-preview에서 마이그레이션 요약
| 2014-12-01-preview | 2016-06-01 |
| --- | --- |
| **HTTP 수신기** API 앱 클릭 |**수동 트리거** 클릭(API 앱 필요 없음) |
| HTTP 수신기 설정 "*자동으로 응답 보내기*" |**response** 작업 포함 또는 워크플로 정의에 없음 |
| 기본 또는 OAuth 인증 구성 |API 관리를 통해 |
| HTTP 메서드 구성 |API 관리를 통해 |
| 상대 경로 구성 |API 관리를 통해 |
| `@triggerOutputs().body.Content`를 통해 들어오는 본문 참조 |`@triggerOutputs().body` |
| **HTTP 응답 보내기** 작업 |**HTTP 요청에 응답** 클릭(API 앱 필요 없음) |

[1]: ./media/logic-apps-http-endpoint/manualtriggerurl.png
[2]: ./media/logic-apps-http-endpoint/manualtrigger.png
[3]: ./media/logic-apps-http-endpoint/response.png

