---
title: "Azure Functions에서 프록시 사용 | Microsoft Docs"
description: "Azure Functions 프록시를 사용하는 방법의 개요"
services: functions
documentationcenter: 
author: mattchenderson
manager: erikre
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 02/19/2017
ms.author: mahender
translationtype: Human Translation
ms.sourcegitcommit: facdd2abbb90a85535b8936ea655a824134c4c8b
ms.openlocfilehash: 3e8b8f9908cf24baf7a5d70521c79dbd470001f8
ms.lasthandoff: 02/22/2017


---
# <a name="working-with-azure-functions-proxies-preview"></a>Azure Functions 프록시 사용(미리 보기)

> [!Note] 
> Azure Functions 프록시는 현재 미리 보기 상태입니다. 미리 보기에서는 무료로 사용할 수 있지만 프록시 실행에는 표준 함수 요금이 적용됩니다. 자세한 내용은 [Azure Functions 가격 책정](https://azure.microsoft.com/pricing/details/functions/)을 참조하세요.

이 문서에서는 Azure Functions 프록시를 구성하고 사용하는 방법을 설명합니다. 이 기능을 사용하면 다른 리소스에서 구현된 함수 앱에 끝점을 지정할 수 있습니다. 이러한 프록시를 사용하면 클라이언트에 대해 단일 API 화면을 계속 제공하면서 큰 API를 여러 개의 함수 앱으로 나눌 수 있습니다(마이크로 서비스 아키텍처 참조).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]


## <a name="a-nameenableaenabling-azure-functions-proxies"></a><a name="enable"></a>Azure Functions 프록시 사용

프록시는 기본적으로 사용하도록 설정되어 있지 않습니다. 이 기능이 사용되지 않도록 설정된 동안에도 프록시를 만들 수 있지만 실행되지 않습니다. 다음 단계에서는 프록시를 사용하도록 설정하는 방법을 보여 줍니다.

1. [Azure Portal]을 열고 함수 앱으로 이동합니다.
2. **함수 앱 설정**을 선택합니다.
3. **Azure Functions 프록시 사용(미리 보기)**을 설정합니다.

새 기능을 사용할 수 있게 되면 여기로 돌아와 프록시 런타임을 업데이트할 수도 있습니다.


## <a name="creating-a-proxy"></a>프록시 만들기

이 섹션에서는 함수 포털에서 프록시를 만드는 방법을 보여 줍니다.

1. [Azure Portal]을 열고 함수 앱으로 이동합니다.
2. 왼쪽 탐색 창에서 **새 프록시**를 선택합니다.
3. 프록시의 이름을 제공합니다.
4. **경로 템플릿** 및 **HTTP 메서드**를 지정하여 이 함수 앱에 노출되는 끝점을 구성합니다. 이러한 매개 변수는 [HTTP 트리거]에 대한 규칙에 따라 동작합니다.
5. **백 엔드 URL**을 다른 끝점으로 설정합니다. 이러한 끝점은 다른 함수 앱의 함수이거나 다른 API일 수 있습니다.
6. 만들기를 클릭합니다.

이제 프록시는 함수 앱에서 새 끝점으로 존재합니다. 클라이언트 관점에서 Azure Functions의 HttpTrigger에 같습니다. 프록시 URL을 복사하고 자주 사용하는 HTTP 클라이언트에서 테스트하여 새 프록시를 시험해볼 수 있습니다.


## <a name="a-namemodify-requestsamodifying-backend-requests"></a><a name="modify-requests"></a>백 엔드 요청 수정

백 엔드 URL 매개 변수는 정적일 필요가 없습니다. 이 사항을 요청 또는 응용 프로그램 설정에서 입력에 대한 조건으로 지정할 수 있습니다.


### <a name="using-request-parameters"></a>요청 매개 변수 사용

경로 템플릿에 사용되는 매개 변수를 백 엔드 URL 속성에 대한 입력으로 사용할 수 있습니다. 값은 중괄호 "{}"로 묶은 이름으로 참조됩니다.

예를 들어 프록시에 `/pets/{petId}`와 같은 경로 템플릿이 있는 경우 백 엔드 URL은 `https://<AnotherApp>.azurewebsites.net/api/pets/{petId}`와 같이 `{petId}` 값을 포함할 수 있습니다. 경로 템플릿이 와일드카드로 끝나면(예: `/api/{*restOfPath}`) 값 `{restOfPath}`는 들어오는 요청의 나머지 경로 세그먼트에 대한 문자열 표현이 됩니다.


### <a name="using-application-settings"></a>응용 프로그램 설정 사용

설정 이름을 백분율 기호 '%'로 묶어 [응용 프로그램 설정](https://docs.microsoft.com/azure/azure-functions/functions-how-to-use-azure-function-app-settings#develop)을 참조할 수도 있습니다.

예를 들어 `https://%ORDER_PROCESSING_HOST%/api/orders`의 백 엔드 URL에서 "%ORDER_PROCESSING_HOST%"는 ORDER_PROCESSING_HOS 설정 값으로 바뀝니다.

> [!TIP] 
> 배포 또는 테스트 환경이 여러 개 있는 경우 백 엔드 호스트에 대해 응용 프로그램 설정을 사용하세요. 이러한 방식으로 항상 해당 환경에 적합한 백 엔드에 정보를 전달할 수 있습니다.



## <a name="deployment-methods"></a>배포 방법

구성하는 프록시는 함수 앱 디렉터리의 루트에 있는 proxies.json 파일에 저장됩니다. 이 파일을 수동으로 편집하고 함수가 지원하는 [배포 방법](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment) 중 하나를 사용하여 앱의 일부로 배포할 수 있습니다.

파일이 처리되려면 이 기능이 사용되도록 설정되어야 합니다. [Azure Functions 프록시 사용](#enable)의 지침에 따라 이 작업을 수행할 수 있습니다.

Proxies.json은 명명된 프록시 및 해당 정의로 구성된 프록시 개체로 정의됩니다. 예제 파일은 다음과 유사할 수 있습니다.

```json
{
    "proxies": {
        "proxy1": {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/{test}"
            },
            "backendUri": "https://<AnotherApp>.azurewebsites.net/api/<FunctionName>"
        }
    }
}
```

각 프록시에는 위의 코드 예제의 "proxy1"과 같은 이름이 지정됩니다. 해당하는 프록시 정의 개체는 다음과 같은 속성으로 정의됩니다.

* **matchCondition**: 필수 - 이 프록시의 실행을 트리거하는 요청을 정의하는 개체입니다. [HTTP 트리거]와 공유되는 두 가지 속성이 포함되어 있습니다.
    * _methods_: 프록시가 응답하는 HTTP 메서드의 배열입니다. 이 속성을 지정하지 않으면 프록시는 경로의 모든 HTTP 메서드에 응답합니다.
    * _route_: 필수 - 경로 템플릿을 정의하여 프록시에서 응답할 요청 URL을 제어합니다. HTTP 트리거와 달리 기본값이 없습니다.
* **backendUri**: 요청이 프록시되어야 하는 백 엔드 리소스의 URL입니다. 이 값은 [백 엔드 요청 수정](#modify-requests)에 설명된 대로 템플릿으로 지정될 수 있습니다. 이 속성을 포함하지 않으면 Azure Functions는 HTTP 200 OK로 응답합니다.

> [!Note] 
> 경로 속성 Azure Functions 프록시는 함수 호스트 구성의 routePrefix 속성을 유지하지 않습니다. /api 같은 접두사를 포함하려는 경우 경로 속성에 포함해야 합니다.



[Azure Portal]: https://portal.azure.com
[HTTP 트리거]: https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook#http-trigger
