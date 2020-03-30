---
title: Azure 함수 HTTP 출력 바인딩
description: Azure 함수에서 HTTP 응답을 반환하는 방법을 알아봅니다.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: a25658677e436edf4d001599bb4981f527016596
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277623"
---
# <a name="azure-functions-http-output-bindings"></a>Azure 함수 HTTP 출력 바인딩

HTTP 요청 발신기(sender)에 응답하려면 HTTP 출력 바인딩을 사용합니다. 이 바인딩에는 HTTP 트리거가 필요하며 트리거 요청과 관련된 응답을 사용자 지정할 수 있습니다.

HTTP 트리거 함수의 기본 반환 값은 다음과 같은 것입니다.

- `HTTP 204 No Content`함수 2.x 이상에서 빈 본체가 있는 경우
- `HTTP 200 OK`함수 1.x의 빈 본문

## <a name="configuration"></a>Configuration

다음 표에서는 *function.json* 파일에 설정된 바인딩 구성 속성을 설명합니다. C# 클래스 라이브러리의 경우 *function.json* 속성에 해당하는 attribute 속성이 없습니다.

|속성  |설명  |
|---------|---------|
| **종류** |`http`로 설정해야 합니다. |
| **direction** | `out`로 설정해야 합니다. |
| **(이름)** | 응답에 대한 함수 코드에 사용되는 변수 이름이거나 반환 값을 사용하는 `$return`입니다. |

## <a name="usage"></a>사용

HTTP 응답을 보내려면 언어 표준 응답 패턴을 사용합니다. C# 또는 C# 스크립트에서는 함수 반환 형식을 `IActionResult` 또는 `Task<IActionResult>`로 만듭니다. C#에서는 반환 값 특성이 필요 없습니다.

예제 응답은 [트리거 예제](./functions-bindings-http-webhook-trigger.md#example)를 참조하세요.

## <a name="hostjson-settings"></a>host.json 설정

이 섹션에서는 버전 2.x 이상에서 이 바인딩에 사용할 수 있는 전역 구성 설정에 대해 설명합니다. 아래의 host.json 예제 파일에는 이 바인딩에 대한 버전 2.x+ 설정만 포함되어 있습니다. 버전 2.x 이상의 전역 구성 설정에 대한 자세한 내용은 [Azure Functions에 대한 host.json 참조를](functions-host-json.md)참조하십시오.

> [!NOTE]
> Functions 1.x에서 host.json의 참조는 [Azure Functions 1.x에 대한 host.json 참조](functions-host-json-v1.md#http)를 참조하세요.

```json
{
    "extensions": {
        "http": {
            "routePrefix": "api",
            "maxOutstandingRequests": 200,
            "maxConcurrentRequests": 100,
            "dynamicThrottlesEnabled": true,
            "hsts": {
                "isEnabled": true,
                "maxAge": "10"
            },
            "customHeaders": {
                "X-Content-Type-Options": "nosniff"
            }
        }
    }
}
```

|속성  |기본값 | 설명 |
|---------|---------|---------| 
| 사용자 지정 헤더|none|HTTP 응답에서 사용자 지정 헤더를 설정할 수 있습니다. 이전 예제는 `X-Content-Type-Options` 콘텐츠 형식 스니핑을 피하기 위해 응답에 헤더를 추가합니다. |
|dynamicThrottlesEnabled|사실<sup>\*</sup>|이 설정을 사용하면 요청 처리 파이프라인이 시스템 성능 카운터와 `connections/threads/processes/memory/cpu/etc` 같은 시스템 성능 카운터를 주기적으로 검사하게 되며 이러한 카운터가 기본 `429 "Too Busy"` 제공 된 높은 임계값(80%)을 초과하면 카운터가 정상 수준으로 돌아올 때까지 응답으로 요청이 거부됩니다.<br/><sup>\*</sup>소비 계획의 기본값은 `true`. 전용 계획의 기본값은 `false`.|
|hsts|활성화되지 않음|로 `isEnabled` 설정되면 `true` [.NET Core의 HTTP 엄격한 전송 보안(HSTS) 동작이](/aspnet/core/security/enforcing-ssl?view=aspnetcore-3.0&tabs=visual-studio#hsts) [ `HstsOptions` 클래스에](/dotnet/api/microsoft.aspnetcore.httpspolicy.hstsoptions?view=aspnetcore-3.0)정의된 대로 적용됩니다. 위의 예제에서도 [`maxAge`](/dotnet/api/microsoft.aspnetcore.httpspolicy.hstsoptions.maxage?view=aspnetcore-3.0#Microsoft_AspNetCore_HttpsPolicy_HstsOptions_MaxAge) 속성을 10일로 설정합니다. 지원되는 `hsts` 속성은 다음과 같습니다. <table><tr><th>속성</th><th>설명</th></tr><tr><td>제외호스트</td><td>HSTS 헤더가 추가되지 않은 호스트 이름의 문자열 배열입니다.</td></tr><tr><td>포함서브도메인</td><td>포함 여부를 나타내는 부울 값입니다.엄격-전송-보안 헤더의 SubDomain 매개 변수가 사용 됩니다.</td></tr><tr><td>맥시에이지</td><td>엄격-전송-보안 헤더의 최대 연령 매개 변수를 정의하는 문자열입니다.</td></tr><tr><td>preload</td><td>엄격-전송-보안 헤더의 사전 로드 매개 변수가 활성화되어 있는지 여부를 나타내는 부울입니다.</td></tr></table>|
|maxConcurrentRequests|100<sup>\*</sup>|병렬로 실행되는 최대 HTTP 함수 수입니다. 이 값을 사용하면 동시성을 제어할 수 있으므로 리소스 사용률을 관리하는 데 도움이 됩니다. 예를 들어 동시성보다 높을 때 문제가 발생되도록 많은 수의 시스템 리소스(메모리/CPU/소켓)를 사용하는 HTTP 함수가 있을 수 있습니다. 또는 타사 서비스에 아웃바운드 요청을 하는 함수가 있을 수 있으며 이러한 호출은 요금이 제한되어야 합니다. 이러한 경우 여기에서 제한을 적용하는 것이 좋습니다. <br/><sup>*</sup>소비 계획의 기본값은 100입니다. 전용 계획의 기본값은 바인딩되지`-1`않은()입니다.|
|maxOutstandingRequests|200<sup>\*</sup>|지정된 시간에 보유할 미해결 요청의 최대 수입니다. 이 제한에는 대기 중이지만 실행이 시작되지 않은 요청과 진행 중인 모든 실행이 포함됩니다. 이 한도를 초과하여 들어오는 요청이 있으면 429 "Too Busy" 응답으로 거부됩니다. 그러면 호출자가 시간 기반 다시 시도 전략을 사용할 수 있고 최대 요청 대기 시간을 제어할 수 있습니다. 이 옵션은 스크립트 호스트 실행 경로 내에서 발생하는 큐만을 제어합니다. ASP.NET 요청 큐와 같은 다른 큐는 여전히 적용되며 이 설정의 영향을 받지 않습니다. <br/><sup>\*</sup>소비 계획의 기본값은 200입니다. 전용 계획의 기본값은 바인딩되지`-1`않은()입니다.|
|routePrefix|api|모든 경로에 적용되는 경로 접두사입니다. 기본 접두사를 제거하려면 빈 문자열을 사용하십시오. |

## <a name="next-steps"></a>다음 단계

- [HTTP 요청에서 함수 실행](./functions-bindings-http-webhook-trigger.md)