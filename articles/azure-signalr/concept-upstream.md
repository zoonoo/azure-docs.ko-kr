---
title: Azure SignalR Service의 업스트림 설정
description: 업스트림 메시지의 업스트림 설정 및 프로토콜을 소개합니다.
author: chenyl
ms.service: signalr
ms.topic: conceptual
ms.date: 06/11/2020
ms.author: chenyl
ms.openlocfilehash: 6752a9564dc0d9351d1c21f5be14eb626186ac0d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98724060"
---
# <a name="upstream-settings"></a>업스트림 설정

업스트림은 Azure SignalR Service에서 서버리스 모드의 엔드포인트 집합에 메시지 및 연결 이벤트를 보낼 수 있게 해주는 미리 보기 기능입니다. 업스트림을 사용하여 서버리스 모드로 클라이언트에서 허브 메서드를 호출하고 클라이언트 연결이 연결되거나 연결이 끊어지면 엔드포인트에서 알림이 수신되도록 할 수 있습니다.

> [!NOTE]
> 서버리스 모드만 업스트림 설정을 구성할 수 있습니다.

## <a name="details-of-upstream-settings"></a>업스트림 설정 세부 정보

업스트림 설정은 주문에 민감한 항목의 목록으로 구성됩니다. 각 항목은 다음으로 구성됩니다.

* 메시지가 전송되는 위치를 지정하는 URL 템플릿.
* 규칙 집합.
* 인증 구성. 

지정된 이벤트가 발생하는 경우 항목의 규칙을 순서대로 하나씩 확인합니다. 첫 번째로 일치하는 항목의 업스트림 URL로 메시지를 보냅니다.

### <a name="url-template-settings"></a>URL 템플릿 설정

URL을 매개 변수화하여 다양한 패턴을 지원할 수 있습니다. 이 트리거에는 다음과 같은 세 가지 미리 정의된 매개 변수가 있습니다.

|미리 정의된 매개 변수|Description|
|---------|---------|
|{hub}| 허브는 Azure SignalR Service의 개념입니다. 허브는 격리 단위입니다. 사용자의 범위와 메시지 배달은 허브로 제한됩니다.|
|{category}| 범주는 다음 값 중 하나가 될 수 있습니다. <ul><li>**연결**: 연결 수명 이벤트입니다. 클라이언트 연결이 연결되거나 끊어진 경우에 발생합니다. 연결된 이벤트와 연결이 끊어진 이벤트를 포함합니다.</li><li>**메시지**: 클라이언트에서 허브 메서드를 호출할 때 발생합니다. **연결** 범주에 있는 이벤트를 제외한 모든 다른 이벤트를 포함합니다.</li></ul>|
|{event}| **메시지** 범주의 경우 이벤트는 클라이언트에서 보내는 [호출 메시지](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocation-message-encoding)의 대상입니다. **연결** 범주에는 *연결됨* 및 *연결 끊김* 만 사용됩니다.|

이러한 미리 정의된 매개 변수는 URL 패턴에서 사용할 수 있습니다. 업스트림 URL을 평가하는 경우 매개 변수가 지정된 값으로 대체됩니다. 예를 들면 다음과 같습니다. 
```
http://host.com/{hub}/api/{category}/{event}
```
"채팅" 허브의 클라이언트 연결이 연결되면 다음 URL로 메시지가 전송됩니다.
```
http://host.com/chat/api/connections/connected
```
"채팅" 허브의 클라이언트가 허브 메서드 `broadcast`을 호출하면 다음 URL로 메시지가 전송됩니다.
```
http://host.com/chat/api/messages/broadcast
```

### <a name="key-vault-secret-reference-in-url-template-settings"></a>URL 템플릿 설정에서 Key Vault 비밀 참조

업스트림의 URL은 미사용 암호화가 아닙니다. 중요한 정보가 있는 경우에는, 액세스 제어가 더 나은 보호 수단을 갖는 경우 Key Vault를 사용하여 이를 저장하는 것이 좋습니다. 기본적으로 Azure SignalR Service의 관리되는 ID를 사용하도록 설정한 다음 Key Vault 인스턴스에 대한 읽기 권한을 부여하고 업스트림 URL 패턴에서 일반 텍스트 대신 Key Vault 참조를 사용할 수 있습니다.

1. 시스템 할당 ID 또는 사용자 할당 ID를 추가합니다. [Azure Portal에서 관리되는 ID를 추가하는 방법](./howto-use-managed-identity.md#add-a-system-assigned-identity)을 참조하세요

2. Key Vault의 액세스 정책에서 관리되는 ID에 대한 비밀 읽기 권한을 부여합니다. [Azure Portal을 사용하여 Key Vault 액세스 정책 할당](../key-vault/general/assign-access-policy-portal.md) 참조

3. 중요한 텍스트를 업스트림 URL 패턴의 구문 `{@Microsoft.KeyVault(SecretUri=<secret-identity>)}`로 바꿉니다.

> [!NOTE]
> 비밀 콘텐츠는 업스트림 설정을 변경하거나 관리되는 ID를 변경하는 경우에만 다시 읽습니다. Key Vault 비밀 참조를 사용하기 전에 관리되는 ID에 대한 비밀 읽기 권한을 부여했는지 확인합니다.

### <a name="rule-settings"></a>규칙 설정

*허브 규칙*, *범주 규칙* 및 *이벤트 규칙* 에 대한 규칙을 별도로 설정할 수 있습니다. 일치 규칙은 세 가지 형식을 지원합니다. 이벤트 규칙을 예로 들어 보겠습니다.
- 임의의 이벤트를 일치시키려면 별표(*)를 사용합니다.
- 여러 이벤트에 조인하려면 쉼표(,)를 사용합니다. 예를 들어 `connected, disconnected`은 연결된 이벤트와 연결이 끊어진 이벤트를 일치시킵니다.
- 전체 이벤트 이름을 사용하여 이벤트와 일치시킵니다. 예를 들어 `connected`은 연결된 이벤트와 일치합니다.

> [!NOTE]
> Azure Functions 및 [SignalR 트리거를](../azure-functions/functions-bindings-signalr-service-trigger.md)사용하는 경우 SignalR 트리거는 다음의 형식으로 단일 엔드포인트를 노출합니다: `<Function_App_URL>/runtime/webhooks/signalr?code=<API_KEY>`.
> **URL 템플릿 설정** 을 이 URL로 구성하고 **규칙 설정** 을 기본값으로 유지할 수 있습니다. `<Function_App_URL>` 및 `<API_KEY>`에 대한 자세한 내용 및 방법은 [SignalR Service 통합](../azure-functions/functions-bindings-signalr-service-trigger.md#signalr-service-integration)을 참조하세요.

### <a name="authentication-settings"></a>인증 설정

각 업스트림 설정 항목에 대해 별도로 인증을 구성할 수 있습니다. 인증을 구성하면 업스트림 메시지의 `Authentication` 헤더에 토큰이 설정됩니다. 현재 Azure SignalR Service 다음 인증 유형을 지원합니다.
- `None`
- `ManagedIdentity`

`ManagedIdentity`을 선택하는 경우 Azure SignalR Service 관리되는 ID를 미리 사용하도록 설정하고 필요에 따라 리소스를 지정해야 합니다. 자세한 내용은 [Azure SignalR Service용 관리 ID](howto-use-managed-identity.md)를 참조하세요.

## <a name="create-upstream-settings-via-the-azure-portal"></a>Azure Portal 통해 업스트림 설정 만들기

1. Azure SignalR Service로 이동.
2. **설정** 을 선택하고 **서비스 모드** 를 **서버리스** 로 전환합니다. 업스트림 설정이 표시됩니다.

    :::image type="content" source="media/concept-upstream/upstream-portal.png" alt-text="업스트림 설정":::

3. **업스트림 URL 패턴** 아래에 URL을 추가합니다. 그런 다음 **허브 규칙** 과 같은 설정에 기본값이 표시됩니다.
4. **허브 규칙** **이벤트 규칙** **범주 규칙** 및 **업스트림 인증** 에 대한 설정을 지정하려면 **허브 규칙** 값을 선택합니다. 설정을 편집할 수 있는 페이지가 나타납니다.

    :::image type="content" source="media/concept-upstream/upstream-detail-portal.png" alt-text="업스트림 설정 세부 정보":::

5. **업스트림 인증** 을 설정하려면 먼저 관리 ID를 사용하도록 설정했는지 확인합니다. 그런 다음 **관리 ID 사용** 을 선택합니다. 사용자의 요구 사항에 따라 **Auth 리소스 ID** 에서 옵션을 선택할 수 있습니다. 자세한 내용은 [Azure SignalR Service용 관리 ID](howto-use-managed-identity.md)를 참조하세요.

## <a name="create-upstream-settings-via-resource-manager-template"></a>Resource Manager 템플릿을 통해 업스트림 설정 만들기

[Azure Resource Manager 템플릿](../azure-resource-manager/templates/overview.md)을 사용하여 업스트림 설정을 만들려면 속성`upstream`에서 속성`properties`을 설정합니다. 다음 코드 조각은 업스트림 설정을 만들고 업데이트하기 위한 `upstream` 속성을 설정하는 방법을 보여줍니다.

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

## <a name="serverless-protocols"></a>서버리스 프로토콜

Azure SignalR Service는 다음 프로토콜을 따르는 엔드포인트로 메시지를 보냅니다. 이러한 프로토콜을 처리하는 함수 앱에서 [SignalR Service 트리거 바인딩](../azure-functions/functions-bindings-signalr-service-trigger.md)을 사용할 수 있습니다.

### <a name="method"></a>메서드

POST

### <a name="request-header"></a>요청 헤더

|이름 |Description|
|---------|---------|
|X ASRS-연결-Id |클라이언트 연결에 대한 연결 ID입니다.|
|X ASRS-허브 |클라이언트 연결이 속한 허브입니다.|
|X ASRS-카테고리 |메시지가 속한 범주입니다.|
|X ASRS-이벤트 |메시지가 속한 이벤트입니다.|
|X ASRS-서명 |유효성 검사에 사용되는 HMAC(해시 기반 메시지 인증 코드)입니다. 자세한 내용은 [서명](#signature)을 참조하십시오.|
|X ASRS-사용자-클레임 |클라이언트 연결의 클레임 그룹입니다.|
|X ASRS-사용자-Id |메시지를 보내는 클라이언트의 사용자 ID입니다.|
|X ASRS-클라이언트-쿼리 |클라이언트가 서비스에 연결하는 경우 요청에 대한 쿼리입니다.|
|인증 |`ManagedIdentity`을 사용하는 경우 선택적 토큰입니다. |

### <a name="request-body"></a>요청 본문

#### <a name="connected"></a>연결됨

Content-Type: application/json

#### <a name="disconnected"></a>연결 끊김

Content-Type: `application/json`

|이름  |유형  |Description  |
|---------|---------|---------|
|Error |문자열 |닫힌 연결에 대한 오류 메시지입니다. 연결이 오류 없이 닫히면 비어 있습니다.|

#### <a name="invocation-message"></a>호출 메시지

Content-Type: `application/json` 또는 `application/x-msgpack`

|이름  |유형  |Description  |
|---------|---------|---------|
|InvocationId |문자열 | 호출 메시지를 나타내는 선택적 문자열입니다. [호출](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocations)에서 세부 정보를 찾습니다.|
|대상 |문자열 | 이벤트와 동일하고 [호출 메시지](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocation-message-encoding)의 대상과 동일합니다. |
|인수 |개체의 배열 |`Target`에서 참조되는 메서드에 적용할 인수를 포함하는 배열입니다. |

### <a name="signature"></a>서명

서비스는 `X-ASRS-Connection-Id` 기본 액세스 키와 보조 액세스 키를 키로 사용하여 값에 대한 `HMAC` SHA256 코드를 계산합니다. 서비스는 업스트림에 대한 HTTP 요청을 수행할 때 `X-ASRS-Signature` 헤더에 설정합니다.
```
Hex_encoded(HMAC_SHA256(accessKey, connection-id))
```

## <a name="next-steps"></a>다음 단계

- [Azure SignalR Service용 관리되는 ID](howto-use-managed-identity.md)
- [Azure SignalR Service를 사용하여 Azure Functions 개발 및 구성](signalr-concept-serverless-development-config.md)
- [SignalR Service 메시지 처리(트리거 바인딩)](../azure-functions/functions-bindings-signalr-service-trigger.md)
- [SignalR Service 트리거 바인딩 샘플](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/BidirectionChat)