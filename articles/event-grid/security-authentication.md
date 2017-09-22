---
title: "Azure Event Grid 보안 및 인증"
description: "Azure Event Grid 및 해당 개념을 설명합니다."
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 08/14/2017
ms.author: babanisa
ms.translationtype: HT
ms.sourcegitcommit: 2c6cf0eff812b12ad852e1434e7adf42c5eb7422
ms.openlocfilehash: 9bc82e628df5e380db84e22e1f5fd25f75929fdc
ms.contentlocale: ko-kr
ms.lasthandoff: 09/13/2017

---

# <a name="event-grid-security-and-authentication"></a>Event Grid 보안 및 인증 

Azure Event Grid에는 세 가지 유형의 인증이 있습니다.

* 이벤트 구독
* 이벤트 게시
* WebHook 이벤트 전달

## <a name="webhook-event-delivery"></a>WebHook 이벤트 전달

WebHook은 Azure Event Grid에서 실시간으로 이벤트를 수신하는 여러 가지 방법 중 하나입니다.

새 이벤트가 전송될 준비가 될 때마다 Event Grid는 본문에 이벤트가 포함된 HTTP 요청을 WebHook으로 보냅니다.

Event Grid에서 고유한 WebHook 끝점을 등록하는 경우 끝점의 소유권을 증명하기 위해 간단한 유효성 검사 코드를 포함한 POST 요청을 전송합니다. 앱은 유효성 검사 코드를 다시 반환하여 응답해야 합니다. Event Grid는 유효성 검사에 통과하지 못한 WebHook 끝점에 이벤트를 전달하지 않습니다.
 
### <a name="validation-details"></a>유효성 검사 세부 정보:

* 이벤트 구독 생성/업데이트 시 Event Grid는 대상 끝점에 "SubscriptionValidationEvent" 이벤트를 게시합니다.
* 이벤트에는 "이벤트-형식: 유효성 검사" 헤더 값을 포함합니다.
* 이벤트 본문에는 다른 Event Grid 이벤트와 동일한 스키마가 있습니다.
* 이벤트 데이터는 다음과 같이 임의로 생성된 문자열을 포함한 “ValidationCode” 속성을 포함합니다. “ValidationCode: acb13…”.

SubscriptionValidationEvent 예는 아래에 표시됩니다.
```json
[{
  "Id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
  "Topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "Subject": "",
  "Data": {
    "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
  },
  "EventType": "Microsoft.EventGrid/SubscriptionValidationEvent",
  "EventTime": "2017-08-06T22:09:30.740323Z"
}]
```

끝점 소유권을 증명하기 위해 “validation_response: acb13…”과 같이 유효성 검사 코드를 반환합니다.

```json
{
  "validationResponse": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
}
```

끝점 소유권을 증명하기 위해 “ValidationResponse: acb13…”과 같이 유효성 검사 코드를 반환합니다.

마지막으로 Azure Event Grid가 HTTPS Webhook 끝점을 지원한다는 점에 유의합니다.
## <a name="event-subscription"></a>이벤트 구독

이벤트를 구독하려면 필요한 리소스에 대한 **Microsoft.EventGrid/EventSubscriptions/Write** 권한이 있어야 합니다. 리소스의 범위에서 새 구독을 작성하기 때문에 이 권한이 있어야 합니다. 필요한 리소스는 시스템 항목 또는 사용자 지정 항목을 구독하는지 여부에 따라 다릅니다. 두 형식은 모두 이 섹션에 설명되어 있습니다.

### <a name="system-topics-azure-service-publishers"></a>시스템 항목(Azure 서비스 게시자)

시스템 항목의 경우 이벤트를 게시하는 리소스의 범위에서 새 이벤트 구독을 쓸 수 있는 사용 권한이 필요합니다. 리소스의 형식은 다음과 같습니다. `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{resource-provider}/{resource-type}/{resource-name}`

예를 들어 **myacct**라는 저장소 계정에 이벤트를 구독하려면 `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.Storage/storageAccounts/myacct`에 대한 Microsoft.EventGrid/EventSubscriptions/Write 권한이 필요합니다.

### <a name="custom-topics"></a>사용자 지정 항목

사용자 지정 항목의 경우 Event Grid 항목의 범위에서 새 이벤트 구독을 쓸 수 있는 사용 권한이 필요합니다. 리소스의 형식은 다음과 같습니다. `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.EventGrid/topics/{topic-name}`

예를 들어 **mytopic**이라는 사용자 지정 항목을 구독하려면 `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.EventGrid/topics/mytopic`에 대한 Microsoft.EventGrid/EventSubscriptions/Write 권한이 필요합니다.

## <a name="topic-publishing"></a>항목 게시

항목은 SAS(공유 액세스 서명) 또는 키 인증을 사용합니다. SAS를 사용하는 것이 좋지만 키 인증에서는 간단한 프로그래밍을 제공하며 기존의 많은 Webhook 게시자와 호환 가능합니다. 

HTTP 헤더에서 인증 값을 포함합니다. SAS의 경우 헤더 값에 **aeg-sas-token**을 사용합니다. 키 인증의 경우 헤더 값에 **aeg-sas-key**를 사용합니다.

### <a name="key-authentication"></a>키 인증

키 인증은 인증의 가장 간단한 양식입니다. 사용할 양식은 다음과 같습니다. `aeg-sas-key: <your key>`

예를 들어 다음을 사용하여 키를 전달합니다. 

```
aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

### <a name="sas-tokens"></a>SAS 토큰

Event Grid의 SAS 토큰에는 리소스, 만료 시간 및 서명이 포함됩니다. SAS 토큰의 형식은 다음과 같습니다. `r={resource}&e={expiration}&s={signature}`

리소스는 이벤트를 전송한 항목의 경로입니다. 예를 들어 올바른 리소스 경로는 다음과 같습니다. `https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events`

키에서 서명을 생성합니다.

예를 들어 유효한 **aeg-sas-token** 값은 다음과 같습니다.

```http
aeg-sas-token: r=https%3a%2f%2fmytopic.eventgrid.azure.net%2feventGrid%2fapi%2fevent&e=6%2f15%2f2017+6%3a20%3a15+PM&s=a4oNHpRZygINC%2fBPjdDLOrc6THPy3tDcGHw1zP4OajQ%3d
``` 

다음 예제에서는 Event Grid를 사용하기 위한 SAS 토큰을 만듭니다.

```cs
static string BuildSharedAccessSignature(string resource, DateTime expirationUtc, string key)
{
    const char Resource = 'r';
    const char Expiration = 'e';
    const char Signature = 's';

    string encodedResource = HttpUtility.UrlEncode(resource);
    string encodedExpirationUtc = HttpUtility.UrlEncode(expirationUtc.ToString());

    string unsignedSas = $"{Resource}={encodedResource}&{Expiration}={encodedExpirationUtc}";
    using (var hmac = new HMACSHA256(Convert.FromBase64String(key)))
    {
        string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(unsignedSas)));
        string encodedSignature = HttpUtility.UrlEncode(signature);
        string signedSas = $"{unsignedSas}&{Signature}={encodedSignature}";

        return signedSas;
    }
}
```

## <a name="management-access-control"></a>관리 액세스 제어

Azure Event Grid를 사용하면 여러 사용자가 이벤트 구독 나열, 새 구독 만들기 및 키 생성과 같은 다양한 관리 작업을 수행할 수 있는 액세스 수준을 제어할 수 있습니다. Event Grid는 이에 대해 Azure의 RBAC(역할 기반 액세스 확인)를 활용합니다.

### <a name="operation-types"></a>작업 형식

Azure Event Grid는 다음 작업을 지원합니다.

* Microsoft.EventGrid/*/read 
* Microsoft.EventGrid/*/write 
* Microsoft.EventGrid/*/delete 
* Microsoft.EventGrid/eventSubscriptions/getFullUrl/action 
* Microsoft.EventGrid/topics/listKeys/action 
* Microsoft.EventGrid/topics/regenerateKey/action

마지막 세 가지 작업에서는 일반 읽기 작업에서 필터링을 가져오는 비밀 정보를 잠재적으로 반환합니다. 이러한 작업에 대한 액세스를 제한하는 것이 좋습니다. [Azure PowerShell](../active-directory/role-based-access-control-manage-access-powershell.md), [Azure CLI(명령줄 인터페이스)](../active-directory/role-based-access-control-manage-access-azure-cli.md) 및 [REST API](../active-directory/role-based-access-control-manage-access-rest.md)를 사용하여 사용자 지정 역할을 만들 수 있습니다.

### <a name="enforcing-role-based-access-check-rbac"></a>RBAC(역할 기반 액세스 확인) 적용

다음 단계를 사용하여 여러 사용자에게 RBAC를 적용합니다.

#### <a name="create-a-custom-role-definition-file-json"></a>사용자 지정 역할 정의 파일(.json) 만들기

사용자가 다른 일련의 동작을 수행할 수 있는 샘플 Event Grid 역할 정의는 다음과 같습니다.

**EventGridReadOnlyRole.json**: 읽기 전용 작업만을 허용합니다.
```json
{ 
  "Name": "Event grid read only role", 
  "Id": "7C0B6B59-A278-4B62-BA19-411B70753856", 
  "IsCustom": true, 
  "Description": "Event grid read only role", 
  "Actions": [ 
    "Microsoft.EventGrid/*/read" 
  ], 
  "NotActions": [ 
  ], 
  "AssignableScopes": [ 
    "/subscriptions/<Subscription Id>" 
  ] 
}
```

**EventGridNoDeleteListKeysRole.json**: 제한된 게시 작업을 허용하지만 삭제 동작을 허용하지 않습니다.
```json
{ 
  "Name": "Event grid No Delete Listkeys role", 
  "Id": "B9170838-5F9D-4103-A1DE-60496F7C9174", 
  "IsCustom": true, 
  "Description": "Event grid No Delete Listkeys role", 
  "Actions": [     
    "Microsoft.EventGrid/*/write", 
    "Microsoft.EventGrid/eventSubscriptions/getFullUrl/action" 
    "Microsoft.EventGrid/topics/listkeys/action", 
    "Microsoft.EventGrid/topics/regenerateKey/action" 
  ], 
  "NotActions": [ 
    "Microsoft.EventGrid/*/delete" 
  ], 
  "AssignableScopes": [ 
    "/subscriptions/<Subscription id>" 
  ] 
}
```

**EventGridContributorRole.json**: 모든 Event Grid 작업을 허용합니다.  
```json
{ 
  "Name": "Event grid contributor role", 
  "Id": "4BA6FB33-2955-491B-A74F-53C9126C9514", 
  "IsCustom": true, 
  "Description": "Event grid contributor role", 
  "Actions": [ 
    "Microsoft.EventGrid/*/write", 
    "Microsoft.EventGrid/*/delete", 
    "Microsoft.EventGrid/topics/listkeys/action", 
    "Microsoft.EventGrid/topics/regenerateKey/action", 
    "Microsoft.EventGrid/eventSubscriptions/getFullUrl/action" 
  ], 
  "NotActions": [], 
  "AssignableScopes": [ 
    "/subscriptions/d48566a8-2428-4a6c-8347-9675d09fb851" 
  ] 
} 
```

#### <a name="install-and-login-to-azure-cli"></a>Azure CLI에 설치 및 로그인

* Azure CLI 버전 0.8.8 이상을 사용하세요. 최신 버전을 설치하고 Azure 구독에 연결하려면 [Azure CLI 설치 및 구성 방법](../cli-install-nodejs.md)을 참조하세요.
* Azure CLI에서 Azure Resource Manager입니다. 자세한 내용을 보려면 [리소스 관리자에서 Azure CLI 사용](../xplat-cli-azure-resource-manager.md) 으로 이동합니다.

#### <a name="create-a-custom-role"></a>사용자 지정 역할 만들기

사용자 지정 역할을 만들려면 다음을 사용합니다.

    azure role create --inputfile <file path>

#### <a name="assign-the-role-to-a-user"></a>사용자에게 역할 할당


    azure role assignment create --signInName  <user email address> --roleName "<name of role>" --resourceGroup <resource group name>


## <a name="next-steps"></a>다음 단계

* Event Grid에 대한 소개는 [Event Grid 정보](overview.md)를 참조하세요.

