<properties
   pageTitle="호출 가능 끝점인 논리 앱"
   description="트리거 끝점을 만들어서 구성하고 Azure 앱 서비스의 논리 앱에서 사용하는 방법"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="08/10/2016"
   ms.author="jehollan"/>


# 호출 가능 끝점인 논리 앱

논리 앱은 기본적으로 동기 HTTP 끝점을 트리거로 노출할 수 있습니다. 또한 호출 가능한 끝점 패턴을 사용하여 논리 앱의 "워크플로" 작업을 통해 중첩된 워크플로로 논리 앱을 호출할 수 있습니다.

요청을 받을 수 있는 트리거에는 3가지 유형이 있습니다.

* 요청
* ApiConnectionWebhook
* HttpWebhook

문서의 나머지 부분에서는 **request**를 예로 사용하지만 모든 주체는 다른 두 가지 유형의 트리거에 동일하게 적용합니다.

## 정의에 트리거 추가
첫 번째 단계는 들어오는 요청을 받을 수 있는 논리 앱 정의에 트리거를 추가하는 것입니다. 디자이너에서 "HTTP 요청"을 검색하여 트리거 카드를 추가할 수 있습니다. 요청 본문 JSON 스키마를 정의하면 디자이너는 사용자가 워크플로를 통해 manual 트리거의 데이터를 구분 분석하고 전달할 수 있도록 토큰을 생성합니다. [jsonschema.net](http://jsonschema.net) 같은 도구를 사용하여 샘플 본문 페이로드의 JSON 스키마를 생성하는 것이 좋습니다.

![요청 트리거 카드][2]

논리 앱 정의를 저장하면 다음과 비슷한 콜백 URL이 생성됩니다.
 
``` text
https://prod-03.eastus.logic.azure.com:443/workflows/080cb66c52ea4e9cabe0abf4e197deff/triggers/myendpointtrigger?...
```

이 URL은 인증에 사용되는 SAS 키를 쿼리 매개 변수에 포함합니다.

Azure 포털에서도 이 끝점을 가져올 수 있습니다.

![][1]

또는 다음을 호출합니다.

``` text
POST https://management.azure.com/{resourceID of your logic app}/triggers/myendpointtrigger/listCallbackURL?api-version=2015-08-01-preview
```

## 논리 앱 트리거의 끝점 호출

트리거의 끝점을 만든 후 `POST`를 통해 전체 URL로 트리거할 수 있습니다. 추가 헤더 및 모든 콘텐츠를 본문에 포함할 수 있습니다.

content-type이 `application/json`이면 요청 내에서 속성을 참조할 수 있습니다. 이 형식이 아니면 다른 API에 전달할 수 있지만 콘텐츠 변환 없이는 워크플로 내부에서 참조할 수 없는 단일 이진 단위로 처리됩니다. 예를 들어 `application/xml` 콘텐츠를 전달하는 경우 `@xpath()`를 사용하여 xpath 추출을 수행하거나 `@json()`를 사용하여 XML에서 JSON으로 변환할 수 있습니다. 콘텐츠 형식 사용에 대한 자세한 내용은 [여기에서 확인](app-service-logic-content-type.md)알 수 있습니다.

뿐만 아니라 정의에서 JSON 스키마를 지정할 수 있습니다. 이렇게 하면 디자이너에서 단계에 전달할 수 있는 토큰을 생성합니다. 예를 들어 다음은 디자이너에서 사용할 수 있는 `title` 및 `name` 토큰을 만듭니다.

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

## 들어오는 요청의 콘텐츠 참조

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

## 요청에 응답

논리 앱을 시작하는 일부 요청의 경우 일부 콘텐츠를 사용하여 호출자에게 응답할 수 있습니다. 응답에 대한 상태 코드, 본문 및 헤더를 생성하는 데 사용할 수 있는 **response**라는 새 동작 유형이 있습니다. **response** 셰이프가 없는 경우 논리 앱 끝점은 **202 수락됨**으로 *즉시* 응답합니다.

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
| -------- | ----------- |
| statusCode | 들어오는 요청에 응답하는 HTTP 상태 코드입니다. 2xx, 4xx 또는 5xx로 시작하는 모든 유효한 상태 코드가 될 수 있습니다. 3xx 상태 코드는 허용되지 않습니다. | 
| body | 문자열, JSON 개체 또는 이전 단계에서 참조한 이진 콘텐츠일 수도 있는 본문 개체입니다. | 
| headers | 응답에 포함될 헤더의 수를 정의할 수 있습니다. | 

**워크플로가 중첩 논리 앱으로 호출되지 않는 경우**, 원래 요청이 응답을 받으려면 응답에 필요한 논리 앱의 모든 단계가 *60초* 이내에 완료되어야 합니다. 60초 내에 도달하는 응답 작업이 없으면 들어오는 요청은 시간 초과되어 **408 클라이언트 시간 제한** HTTP 응답을 수신합니다. 중첩 논리 앱의 경우 부모 논리 앱은 소요되는 시간에 관계없이 응답이 완료될 때까지 계속 기다립니다.

## 고급 끝점 구성

논리 앱은 직접 액세스 끝점에 대한 지원이 내장되어 있으며 항상 `POST` 메서드를 사용하여 논리 앱을 실행합니다. 또한 **HTTP 수신기** API 앱은 이전에 URL 세그먼트 및 HTTP 메서드 변경을 지원했습니다. API 앱 호스트(API 앱을 호스트하는 웹앱)에 추가하여 추가 보안 또는 사용자 지정 도메인을 설정할 수도 있습니다.

이 기능은 **API 관리**를 통해 사용할 수 있습니다.
* [요청 방법 변경](https://msdn.microsoft.com/library/azure/dn894085.aspx#SetRequestMethod)
* [요청의 URL 세그먼트 변경](https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#RewriteURL)
* 클래식 Azure 포털의 **구성** 탭에서 API 관리 도메인 설정
* 기본 인증을 확인하는 정책 설정(**링크 필요**)

## 2014-12-01-preview에서 마이그레이션 요약

| 2014-12-01-preview | 2016-06-01 |
|---------------------|--------------------|
| **HTTP 수신기** API 앱 클릭 | **수동 트리거** 클릭(API 앱 필요 없음) |
| HTTP 수신기 설정 "*자동으로 응답 보내기*" | **response** 작업 포함 또는 워크플로 정의에 없음 |
| 기본 또는 OAuth 인증 구성 | API 관리를 통해 |
| HTTP 메서드 구성 | API 관리를 통해 |
| 상대 경로 구성 | API 관리를 통해 |
| `@triggerOutputs().body.Content`를 통해 들어오는 본문 참조 | `@triggerOutputs().body`를 통해 참조 |
| HTTP 수신기에서 **HTTP 응답 보내기** 작업 | **HTTP 요청에 응답** 클릭(API 앱 필요 없음)


[1]: ./media/app-service-logic-http-endpoint/manualtriggerurl.png
[2]: ./media/app-service-logic-http-endpoint/manualtrigger.png
[3]: ./media/app-service-logic-http-endpoint/response.png

<!---HONumber=AcomDC_0810_2016-->