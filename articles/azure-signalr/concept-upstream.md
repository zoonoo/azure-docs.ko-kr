---
title: Azure SignalR Service의 업스트림 설정
description: 업스트림 메시지의 업스트림 설정 및 프로토콜을 소개 합니다.
author: chenyl
ms.service: signalr
ms.topic: conceptual
ms.date: 06/11/2020
ms.author: chenyl
ms.openlocfilehash: 1d51f5e8d2fac1e2b180a608c840d0a322e76271
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2020
ms.locfileid: "92143246"
---
# <a name="upstream-settings"></a>업스트림 설정

업스트림은 Azure SignalR 서비스에서 서버 리스 모드의 끝점 집합에 메시지 및 연결 이벤트를 보낼 수 있게 해 주는 미리 보기 기능입니다. 업스트림을 사용 하 여 서버를 사용 하지 않는 모드로 클라이언트에서 허브 메서드를 호출 하 고 클라이언트 연결이 연결 되거나 연결이 끊어지면 끝점에서 알림이 수신 되도록 할 수 있습니다.

> [!NOTE]
> 서버를 사용 하지 않는 모드만 업스트림 설정을 구성할 수 있습니다.

## <a name="details-of-upstream-settings"></a>업스트림 설정 세부 정보

업스트림 설정은 주문에 민감한 항목의 목록으로 구성 됩니다. 각 항목은 다음과 같이 구성 됩니다.

* URL 템플릿에서 메시지가 전송 되는 위치를 지정 합니다.
* 규칙 집합입니다.
* 인증 구성. 

지정 된 이벤트가 발생 하는 경우 항목의 규칙을 순서 대로 하나씩 확인 합니다. 첫 번째로 일치 하는 항목의 업스트림 URL로 메시지를 보냅니다.

### <a name="url-template-settings"></a>URL 템플릿 설정

URL을 매개 변수화 하 여 다양 한 패턴을 지원할 수 있습니다. 세 가지 미리 정의 된 매개 변수가 있습니다.

|미리 정의 된 매개 변수|Description|
|---------|---------|
|hub-and-spoke| 허브는 Azure SignalR 서비스의 개념입니다. 허브는 격리 단위입니다. 사용자의 범위와 메시지 배달은 허브로 제한 됩니다.|
|범주| 범주는 다음 값 중 하나일 수 있습니다. <ul><li>**연결**: 연결 수명 이벤트입니다. 클라이언트 연결이 연결 되거나 연결이 끊어진 경우에 발생 합니다. 연결 된 이벤트와 연결이 끊어진 이벤트를 포함 합니다.</li><li>**메시지**: 클라이언트에서 허브 메서드를 호출할 때 발생 합니다. **연결** 범주에 있는 이벤트를 제외 하 고 다른 모든 이벤트를 포함 합니다.</li></ul>|
|이벤트| **메시지** 범주의 경우 이벤트는 클라이언트가 보내는 [호출 메시지](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocation-message-encoding) 의 대상입니다. **연결** 범주에 대해서는 *연결* 된 연결 및 연결 *끊김* 만 사용 됩니다.|

이러한 미리 정의 된 매개 변수는 URL 패턴에서 사용할 수 있습니다. 업스트림 URL을 평가 하는 경우 매개 변수가 지정 된 값으로 대체 됩니다. 예를 들면 다음과 같습니다. 
```
http://host.com/{hub}/api/{category}/{event}
```
"채팅" 허브의 클라이언트 연결이 연결 되 면 다음 URL로 메시지가 전송 됩니다.
```
http://host.com/chat/api/connections/connected
```
"채팅" 허브의 클라이언트가 허브 메서드를 호출 하면 `broadcast` 다음 URL로 메시지가 전송 됩니다.
```
http://host.com/chat/api/messages/broadcast
```

### <a name="rule-settings"></a>규칙 설정

*허브 규칙*, *범주 규칙*및 *이벤트 규칙* 에 대 한 규칙을 별도로 설정할 수 있습니다. 일치 규칙은 세 가지 형식을 지원 합니다. 이벤트 규칙을 예로 들어 보겠습니다.
- 별표 (*)를 사용 하 여 모든 이벤트를 일치 시킵니다.
- 여러 이벤트를 조인 하려면 쉼표 (,)를 사용 합니다. 예를 들어는 `connected, disconnected` 연결 된 이벤트와 연결이 끊어진 이벤트를 일치 시킵니다.
- 이벤트와 일치 시키려면 전체 이벤트 이름을 사용 합니다. 예를 들어는 `connected` 연결 된 이벤트와 일치 합니다.

> [!NOTE]
> Azure Functions 및 [SignalR 트리거](../azure-functions/functions-bindings-signalr-service-trigger.md)를 사용 하는 경우 SignalR 트리거는 형식으로 단일 끝점을 노출 `https://<APP_NAME>.azurewebsites.net/runtime/webhooks/signalr?code=<API_KEY>` 합니다.
> 이 url에 대 한 url 템플릿만 구성할 수 있습니다.

### <a name="authentication-settings"></a>인증 설정

각 업스트림 설정 항목에 대해 개별적으로 인증을 구성할 수 있습니다. 인증을 구성 하면 `Authentication` 업스트림 메시지의 헤더에 토큰이 설정 됩니다. 현재 Azure SignalR Service는 다음 인증 유형을 지원 합니다.
- `None`
- `ManagedIdentity`

을 선택 하는 경우 `ManagedIdentity` Azure SignalR Service에서 관리 되는 id를 미리 사용 하도록 설정 하 고 필요에 따라 리소스를 지정 해야 합니다. 자세한 내용은 [Azure SignalR 서비스에 대 한 관리 되는 id](howto-use-managed-identity.md) 를 참조 하세요.

## <a name="create-upstream-settings-via-the-azure-portal"></a>Azure Portal을 통해 업스트림 설정 만들기

1. Azure SignalR Service로 이동 합니다.
2. **설정** 을 선택 하 고 **서비스 모드** 를 **서버**리스로 전환 합니다. 업스트림 설정이 다음과 같이 표시 됩니다.

    :::image type="content" source="media/concept-upstream/upstream-portal.png" alt-text="업스트림 설정":::

3. **업스트림 Url 패턴**에서 url을 추가 합니다. 그런 다음 **허브 규칙과** 같은 설정이 기본값을 표시 합니다.
4. **허브 규칙**, **이벤트 규칙**, **범주 규칙**및 **업스트림 인증**에 대 한 설정을 설정 하려면 **허브 규칙**값을 선택 합니다. 설정을 편집할 수 있는 페이지가 나타납니다.

    :::image type="content" source="media/concept-upstream/upstream-detail-portal.png" alt-text="업스트림 설정":::

5. **업스트림 인증**을 설정 하려면 먼저 관리 되는 id를 사용 하도록 설정 했는지 확인 합니다. 그런 다음 **관리 되는 Id 사용**을 선택 합니다. 사용자의 요구 사항에 따라 **Auth 리소스 ID**에서 옵션을 선택할 수 있습니다. 자세한 내용은 [Azure SignalR 서비스에 대 한 관리 되는 id](howto-use-managed-identity.md) 를 참조 하세요.

## <a name="create-upstream-settings-via-resource-manager-template"></a>리소스 관리자 템플릿을 통해 업스트림 설정 만들기

[Azure Resource Manager 템플릿을](../azure-resource-manager/templates/overview.md)사용 하 여 업스트림 설정을 만들려면 속성에서 속성을 설정 `upstream` `properties` 합니다. 다음 코드 조각은 `upstream` 업스트림 설정을 만들고 업데이트 하기 위한 속성을 설정 하는 방법을 보여 줍니다.

```JSON
{
  "properties": {
    "upstream": {
      "templates": [
        {
          "UrlTemplate": "http://host.com/{hub}/api/{category}/{event}",
          "EventPattern": "*",
          "HubPattern": "*",
          "CategoryPattern": "*",
          "Auth": {
            "Type": "ManagedIdentity",
            "ManagedIdentity": {
              "Resource": "<resource>"
            }
          }
        }
      ]
    }
  }
}
```

## <a name="serverless-protocols"></a>서버 리스 프로토콜

Azure SignalR Service는 다음 프로토콜을 따르는 끝점으로 메시지를 보냅니다.

### <a name="method"></a>메서드

POST

### <a name="request-header"></a>요청 헤더

|Name |Description|
|---------|---------|
|X ASRS-연결 Id |클라이언트 연결에 대 한 연결 ID입니다.|
|X ASRS-허브 |클라이언트 연결이 속한 허브입니다.|
|X ASRS-범주 |메시지가 속한 범주입니다.|
|X ASRS-이벤트 |메시지가 속한 이벤트입니다.|
|X ASRS-서명 |유효성 검사에 사용 되는 HMAC (해시 기반 메시지 인증 코드)입니다. 자세한 내용은 [시그니처](#signature) 를 참조 하십시오.|
|X ASRS-사용자 클레임 |클라이언트 연결의 클레임 그룹입니다.|
|X ASRS-사용자 Id |메시지를 보내는 클라이언트의 사용자 id입니다.|
|X ASRS-Client 쿼리 |클라이언트가 서비스에 연결 하는 경우 요청에 대 한 쿼리입니다.|
|인증 |를 사용 하는 경우 선택적 토큰 `ManagedIdentity` 입니다. |

### <a name="request-body"></a>요청 본문

#### <a name="connected"></a>연결됨

Content-Type: application/json

#### <a name="disconnected"></a>연결 끊김

콘텐츠 형식: `application/json`

|Name  |Type  |Description  |
|---------|---------|---------|
|오류 |문자열 |닫힌 연결의 오류 메시지입니다. 연결이 오류 없이 닫힐 때 비어 있습니다.|

#### <a name="invocation-message"></a>호출 메시지

콘텐츠 형식: `application/json` 또는 `application/x-msgpack`

|Name  |Type  |Description  |
|---------|---------|---------|
|InvocationId |문자열 | 호출 메시지를 나타내는 선택적 문자열입니다. [호출](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocations)에서 세부 정보를 찾습니다.|
|대상 |문자열 | 이벤트와 동일 하며 [호출 메시지](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocation-message-encoding)의 대상과 동일 합니다. |
|인수 |개체의 배열 |에서 참조 되는 메서드에 적용할 인수를 포함 하는 배열입니다 `Target` . |

### <a name="signature"></a>서명

서비스는 `X-ASRS-Connection-Id` 기본 액세스 키와 보조 액세스 키를 모두 키로 사용 하 여 값에 대 한 SHA256 코드를 계산 합니다 `HMAC` . `X-ASRS-Signature`업스트림에 대 한 HTTP 요청을 만들 때 서비스는 헤더에이를 설정 합니다.
```
Hex_encoded(HMAC_SHA256(accessKey, connection-id))
```

## <a name="next-steps"></a>다음 단계

- [Azure SignalR Service에 대 한 관리 되는 id](howto-use-managed-identity.md)
- [Azure SignalR Service를 사용하여 Azure Functions 개발 및 구성](signalr-concept-serverless-development-config.md)