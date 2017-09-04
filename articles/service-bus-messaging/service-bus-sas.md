---
title: "공유 액세스 서명을 사용한 Azure Service Bus 인증 | Microsoft Docs"
description: "공유 액세스 서명을 사용한 Azure Service Bus 인증 개요, Azure Service Bus를 사용한 SAS 인증 상세 정보"
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/23/2017
ms.author: sethm
ms.translationtype: HT
ms.sourcegitcommit: 5b6c261c3439e33f4d16750e73618c72db4bcd7d
ms.openlocfilehash: a2760072acb7c62204759f3ec0d3cb9899460f2d
ms.contentlocale: ko-kr
ms.lasthandoff: 08/28/2017

---

# <a name="service-bus-authentication-with-shared-access-signatures"></a>공유 액세스 서명을 사용한 Service Bus 인증

*공유 액세스 서명*(SAS)은 Service Bus 메시징의 기본 보안 메커니즘입니다. 이 문서에서는 SAS, 그 작동 방법 및 플랫폼과 상관 없는 방식으로 사용하는 방법에 대해 설명합니다.

SAS 인증을 사용하면 응용 프로그램을 네임스페이스 또는 특정 권한이 연관된 메시징 엔터티(큐 또는 토픽)에서 구성된 선택키를 사용하여 Service Bus에 인증할 수 있습니다. 그런 다음 이 키를 사용하여 클라이언트가 서비스 버스를 인증하는 데 차례로 사용할 수 있는 SAS 토큰을 생성할 수 있습니다.

SAS 인증 지원은 Azure SDK 버전 2.0 이후에 포함됩니다.

## <a name="overview-of-sas"></a>SAS 개요

공유 액세스 서명은 SHA-256 보안 해시 또는 URI에 따른 인증 메커니즘입니다. SAS는 모든 서비스 버스 서비스에서 사용되는 매우 강력한 메커니즘입니다. 실제 사용 시, SAS에는 *공유 액세스 정책*과 *공유 액세스 서명*(*토큰*이라고 부름)의 두 구성 요소가 있습니다.

서비스 버스에서 SAS 인증은 서비스 버스 리소스에 연결된 권한이 있는 암호화 키의 구성을 포함합니다. 클라이언트는 SAS 토큰을 발급하여 서비스 버스 리소스에 액세스를 주장합니다. 이 토큰은 액세스된 리소스 URI 및 구성된 키로 서명된 만료 매개 변수로 구성됩니다.

Service Bus [릴레이](service-bus-fundamentals-hybrid-solutions.md#relays), [큐](service-bus-fundamentals-hybrid-solutions.md#queues) 및 [토픽](service-bus-fundamentals-hybrid-solutions.md#topics)에 대해 공유 액세스 서명 권한 부여 규칙을 구성할 수 있습니다 

SAS 인증은 다음과 같은 요소를 사용합니다.

* [공유 액세스 권한 부여 규칙](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule): Base64 표현에서 256비트 기본 암호화 키, 선택적 보조키 및 키 이름과 관련된 권한입니다(*수신*, *보내기* 또는 *관리* 권한의 컬렉션).
* [공유 액세스 서명](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) 토큰: 리소스 문자열의 HMAC-SHA256을 사용하여 생성되고 액세스된 리소스의 URI 및 암호화 키를 가진 만료 매개 변수로 구성됩니다. 다음 섹션에 설명된 서명 및 다른 요소는 문자열로 서식이 지정되어 SAS 토큰을 형성합니다.

## <a name="shared-access-policy"></a>공유 액세스 정책

SAS에 대해 잘 이해하기 위해서는 정책부터 시작하는 것이 중요합니다. 각각의 정책에 대해 **이름**, **범위** 및 **권한** 등, 3가지 정보를 결정합니다. **이름** 은 해당 범위 내에서 고유한 이름입니다. 범위는 아주 쉬운데, 해당 리소스의 URI를 말합니다. Service Bus 네임스페이스의 경우, 범위는 `https://<yournamespace>.servicebus.windows.net/` 등과 같이 정규화된 도메인 이름(FQDN)입니다.

정책에서 사용 가능한 권한은 대부분 설명이 따로 필요 없습니다.

* 보내기
* 수신 대기
* 관리

정책을 만든 후 *기본 키*와 *보조 키*가 할당됩니다. 이들은 강력한 암호화 키입니다. 잃어 버리거나 누출되지 않도록 하세요. 항상 [Azure Portal][Azure portal]에서 사용할 수 있습니다. 생성된 키 중 하나를 사용할 수 있으며 언제든지 다시 생성할 수 있습니다. 그러나 정책에서 기본 키를 다시 생성하거나 변경할 경우, 만든 공유 액세스 서명은 무효화됩니다.

서비스 버스 네임스페이스를 만들 때 **RootManageSharedAccessKey**라는 전체 네임스페이스에 대해 정책이 자동으로 만들어지며 이 정책은 모든 사용 권한을 갖습니다. **root**로 로그온하지 않아야 합니다. 포털의 해당 네임스페이스에 대한 **구성** 탭에서 추가 정책을 만들 수 있습니다. Service Bus의 단일 트리 수준(네임스페이스, 큐 등)에는 최대 12개의 정책까지만 연결할 수 있습니다.

## <a name="configuration-for-shared-access-signature-authentication"></a>공유 액세스 서명 인증을 위한 구성
서비스 버스 네임스페이스, 큐 또는 항목에 대한 [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) 규칙을 구성할 수 있습니다. 서비스 버스 구독에서 [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) 의 구성은 현재 지원되지 않지만 네임스페이스 또는 항목에 구성된 규칙을 사용하여 구독에 액세스할 수 있습니다. 이 절차를 설명하는 작업 샘플은 [서비스 버스 구독으로 공유 액세스 서명(SAS) 인증 사용](http://code.msdn.microsoft.com/Using-Shared-Access-e605b37c) 샘플을 참조하세요.

이러한 규칙을 서비스 버스 네임 스페이스, 큐 또는 항목에서 최대 12개까지 구성할 수 있습니다. 서비스 버스 네임 스페이스에 구성된 규칙이 해당 네임스페이스에 있는 모든 엔터티에 적용됩니다.

![SAS](./media/service-bus-sas/service-bus-namespace.png)

이 그림에서는 *manageRuleNS*, *sendRuleNS* 및 *listenRuleNS* 권한 부여 규칙이 큐 Q1 및 토픽 topic T1에 적용되는 반면, *listenRuleQ* 및 *sendRuleQ*는 큐 Q1에만 적용되고 *sendRuleT*는 토픽 T1에만 적용됩니다.

[SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) 의 키 매개 변수는 다음과 같습니다.

| 매개 변수 | 설명 |
| --- | --- |
| *KeyName* |권한 부여 규칙을 설명하는 문자열입니다. |
| *PrimaryKey* |서명하고 SAS 토큰의 유효성을 검사하기 위한 base64로 인코딩된 256비트 기본 키입니다. |
| *SecondaryKey* |서명하고 SAS 토큰의 유효성을 검사하기 위한 base64로 인코딩된 256비트 보조 키입니다. |
| *AccessRights* |권한 부여 규칙에서 부여된 액세스 권한의 목록입니다. 이러한 권한은 수신, 보내기 및 관리 권한의 컬렉션일 수 있습니다. |

Service Bus 네임스페이스를 프로비전하면 기본적으로 [KeyName](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_KeyName)이 **RootManageSharedAccessKey**로 설정된 [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule)이 생성됩니다.

## <a name="generate-a-shared-access-signature-token"></a>공유 액세스 서명(토큰) 생성

정책 자체는 서비스 버스에 대한 액세스 토큰이 아니며 기본 또는 보조 키 중 하나를 사용하여 액세스 토큰이 생성되는 개체입니다. 공유 액세스 권한 부여 규칙에 지정된 서명 키에 액세스하는 클라이언트는 SAS 토큰을 생성할 수 있습니다. 토큰은 다음 형식으로 문자열을 신중하게 선별하여 생성됩니다.

```
SharedAccessSignature sig=<signature-string>&se=<expiry>&skn=<keyName>&sr=<URL-encoded-resourceURI>
```

여기서 `signature-string`은 토큰 범위(이전 섹션에서 설명한 대로 **범위**)의 SHA-256 해시로, CRLF가 첨부되어 있고 만료 시간(Epoch 이후 초 단위: 1970년 1월 1일에 `00:00:00 UTC`)이 있습니다. 

> [!NOTE]
> 토큰 만료 시간이 짧아지지 않게, 만료 시간 값을 최소한 32비트의 부호 없는 정수나 Long(64비트) 정수로 인코딩하는 것이 좋습니다.  
> 
> 

해시는 다음 의사 코드와 유사하며 32바이트를 반환합니다.

```
SHA-256('https://<yournamespace>.servicebus.windows.net/'+'\n'+ 1438205742)
```

비해시 값이 **SharedAccessSignature** 문자열에 있으므로 수신자는 동일한 결과를 반환하도록 동일한 매개 변수로 해시를 계산할 수 있습니다. URI는 범위를 지정하며 키 이름은 해시를 계산하는 데 사용할 정책을 식별합니다. 이것은 보안의 관점에서 중요합니다. 서명이 수신자(서비스 버스)에서 계산하는 것과 일치하지 않으면 액세스가 거부됩니다. 이제 발신자에게 키에 대한 액세스 권한이 있음을 알 수 있으므로 정책에 지정된 권한을 부여해야 합니다.

이 작업에 인코딩된 리소스 URI를 사용해야 합니다. 리소스 URI은 액세스를 하려는 서비스 버스 리소스의 전체 URI입니다. 예를 들면 `http://<namespace>.servicebus.windows.net/<entityPath>` 또는 `sb://<namespace>.servicebus.windows.net/<entityPath>`입니다. 즉, `http://contoso.servicebus.windows.net/contosoTopics/T1/Subscriptions/S3`과 같습니다.

이 URI로 또는 부모 계층 중 하나에 의해 지정된 엔터티에 서명에 사용된 공유 액세스 권한 부여 규칙을 구성해야 합니다. 예를 들어 이전 예에서 `http://contoso.servicebus.windows.net/contosoTopics/T1` 또는 `http://contoso.servicebus.windows.net`입니다.

SAS 토큰은 `signature-string`에서 사용된 `<resourceURI>`의 모든 리소스에 유효합니다.

SAS 토큰의 [KeyName](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_KeyName) 은 토큰을 생성하는 데 사용된 공유 액세스 권한 부여 규칙의 **keyName** 을 가리킵니다.

*URL로 인코딩된 resourceURI* 은 서명을 계산하는 동안 서명할 문자열에서 사용된 URI와 동일해야 합니다. [%로 인코딩](https://msdn.microsoft.com/library/4fkewx0t.aspx)되어야 합니다.

[SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) 개체에서 정기적으로 사용되는 키를 다시 생성하는 것이 좋습니다. 응용 프로그램은 일반적으로 [PrimaryKey](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_PrimaryKey) 를 사용하여 SAS 토큰을 생성해야 합니다. 키를 다시 생성하는 경우 [SecondaryKey](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_SecondaryKey) 를 이전 기본 키로 교체하고 새 키를 새 기본 키로 생성합니다. 이 옵션을 사용하면 이전 기본 키로 발급되고 아직 만료되지 않은 권한 부여에 대한 토큰을 사용하여 계속할 수 있습니다.

키가 손상되고 키를 해제하면 [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule)의 [PrimaryKey](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_PrimaryKey) 및 [SecondaryKey](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_SecondaryKey) 모두를 새 키로 바꿔 다시 생성할 수 있습니다. 이 절차는 이전 키로 서명된 모든 토큰을 무효화합니다.

## <a name="how-to-use-shared-access-signature-authentication-with-service-bus"></a>서비스 버스를 사용한 공유 액세스 서명을 인증하는 방법

다음과 같은 시나리오는 권한 부여 규칙의 구성, SAS 토큰의 생성 및 클라이언트 권한 부여를 포함합니다.

구성을 설명하고 SAS 권한 부여를 사용하는 서비스 버스 응용 프로그램의 작업 샘플 전체는 [서비스 버스를 사용하여 공유 액세스 서명 인증](http://code.msdn.microsoft.com/Shared-Access-Signature-0a88adf8)을 참조하세요. 서비스 버스 구독을 보호하기 위해 네임스페이스 또는 항목에 구성된 SAS 권한 부여 규칙의 사용 방법을 설명하는 관련된 샘플은 [서비스 버스 구독으로 공유 액세스 서명(SAS) 사용](http://code.msdn.microsoft.com/Using-Shared-Access-e605b37c)에서 사용 가능합니다.

## <a name="access-shared-access-authorization-rules-on-a-namespace"></a>네임스페이스에 대한 공유 액세스 권한 부여 규칙 액세스

서비스 버스 네임스페이스 루트에 대한 작업은 인증서 인증이 필요합니다. Azure 구독에 대한 관리 인증서를 업로드해야 합니다. 관리 인증서를 업로드하려면 [Azure Portal][Azure portal]을 사용하여 [여기](../cloud-services/cloud-services-configure-ssl-certificate-portal.md#step-3-upload-a-certificate)의 단계에 따릅니다. Azure 관리 인증서에 대한 자세한 내용은 [Azure 인증서 개요](../cloud-services/cloud-services-certs-create.md#what-are-management-certificates)를 참조하세요.

서비스 버스 네임스페이스에 대한 공유 액세스 권한 부여 규칙에 액세스하기 위한 끝점은 다음과 같습니다.

```http
https://management.core.windows.net/{subscriptionId}/services/ServiceBus/namespaces/{namespace}/AuthorizationRules/
```

서비스 버스 네임 스페이스에서 [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) 개체를 만들려면 이 끝점에서 JSON 또는 XML로 직렬화된 규칙 정보를 사용하여 게시 작업을 실행합니다. 예:

```csharp
// Base address for accessing authorization rules on a namespace
string baseAddress = @"https://management.core.windows.net/<subscriptionId>/services/ServiceBus/namespaces/<namespace>/AuthorizationRules/";

// Configure authorization rule with base64-encoded 256-bit key and Send rights
var sendRule = new SharedAccessAuthorizationRule("contosoSendAll",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] { AccessRights.Send });

// Operations on the Service Bus namespace root require certificate authentication.
WebRequestHandler handler = new WebRequestHandler
{
    ClientCertificateOptions = ClientCertificateOption.Manual
};
// Access the management certificate by subject name
handler.ClientCertificates.Add(GetCertificate(<certificateSN>));

HttpClient httpClient = new HttpClient(handler)
{
    BaseAddress = new Uri(baseAddress)
};
httpClient.DefaultRequestHeaders.Accept.Add(
    new MediaTypeWithQualityHeaderValue("application/json"));
httpClient.DefaultRequestHeaders.Add("x-ms-version", "2015-01-01");

// Execute a POST operation on the baseAddress above to create an auth rule
var postResult = httpClient.PostAsJsonAsync("", sendRule).Result;
```

마찬가지로 끝점에서 가져오기 작업을 사용하여 네임스페이스에 구성된 권한 부여 규칙을 읽습니다.

특정 권한 부여 규칙을 업데이트하거나 삭제하려면 다음 끝점을 사용합니다.

```http
https://management.core.windows.net/{subscriptionId}/services/ServiceBus/namespaces/{namespace}/AuthorizationRules/{KeyName}
```

## <a name="access-shared-access-authorization-rules-on-an-entity"></a>엔터티에 대한 공유 액세스 권한 부여 규칙 액세스

해당하는 [QueueDescription](/dotnet/api/microsoft.servicebus.messaging.queuedescription) 또는 [TopicDescription](/dotnet/api/microsoft.servicebus.messaging.topicdescription)에서 [AuthorizationRules](/dotnet/api/microsoft.servicebus.messaging.authorizationrules) 컬렉션을 통해 Service Bus 큐나 토픽에 구성된 [Microsoft.ServiceBus.Messaging.SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) 개체에 액세스할 수 있습니다.

다음 코드는 큐에 대한 권한 부여 규칙을 추가하는 방법을 보여줍니다.

```csharp
// Create an instance of NamespaceManager for the operation
NamespaceManager nsm = NamespaceManager.CreateFromConnectionString(
    <connectionString> );
QueueDescription qd = new QueueDescription( <qPath> );

// Create a rule with send rights with keyName as "contosoQSendKey"
// and add it to the queue description.
qd.Authorization.Add(new SharedAccessAuthorizationRule("contosoSendKey",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] { AccessRights.Send }));

// Create a rule with listen rights with keyName as "contosoQListenKey"
// and add it to the queue description.
qd.Authorization.Add(new SharedAccessAuthorizationRule("contosoQListenKey",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] { AccessRights.Listen }));

// Create a rule with manage rights with keyName as "contosoQManageKey"
// and add it to the queue description.
// A rule with manage rights must also have send and receive rights.
qd.Authorization.Add(new SharedAccessAuthorizationRule("contosoQManageKey",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] {AccessRights.Manage, AccessRights.Listen, AccessRights.Send }));

// Create the queue.
nsm.CreateQueue(qd);
```

## <a name="use-shared-access-signature-authorization"></a>공유 액세스 서명 권한 부여 사용

서비스 버스.NET 라이브러리로 Azure.NET SDK를 사용하는 응용 프로그램은 [SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) 클래스를 통해 SAS 권한 부여를 사용할 수 있습니다. 다음 코드에서는 토큰 공급자를 사용하여 서비스 버스 큐에 메시지를 보냅니다.

```csharp
Uri runtimeUri = ServiceBusEnvironment.CreateServiceUri("sb",
    <yourServiceNamespace>, string.Empty);
MessagingFactory mf = MessagingFactory.Create(runtimeUri,
    TokenProvider.CreateSharedAccessSignatureTokenProvider(keyName, key));
QueueClient sendClient = mf.CreateQueueClient(qPath);

//Sending hello message to queue.
BrokeredMessage helloMessage = new BrokeredMessage("Hello, Service Bus!");
helloMessage.MessageId = "SAS-Sample-Message";
sendClient.Send(helloMessage);
```

또한 응용 프로그램은 연결 문자열을 수락하는 메서드에 SAS 연결 문자열을 사용하여 인증에 SAS을 사용합니다.

서비스 버스 릴레이로 SAS 권한 부여를 사용하면 서비스 버스 네임 스페이스에 구성된 SAS 키를 사용할 수 있습니다. 네임스페이스([NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager)와 [RelayDescription](/dotnet/api/microsoft.servicebus.messaging.relaydescription)) 개체에 명시적으로 릴레이를 만들면 해당 릴레이 대한 SAS 규칙을 설정할 수 있습니다. 서비스 버스 구독으로 SAS 권한 부여를 사용하려면 서비스 버스 네임 스페이스 또는 항목에 구성된 SAS 키를 사용할 수 있습니다.

## <a name="use-the-shared-access-signature-at-http-level"></a>공유 액세스 서명 사용(HTTP 수준에서)

서비스 버스의 모든 엔터티에 대해 공유 액세스 서명을 만드는 방법을 알았으므로 HTTP POST를 수행할 준비가 되었습니다.

```http
POST https://<yournamespace>.servicebus.windows.net/<yourentity>/messages
Content-Type: application/json
Authorization: SharedAccessSignature sr=https%3A%2F%2F<yournamespace>.servicebus.windows.net%2F<yourentity>&sig=<yoursignature from code above>&se=1438205742&skn=KeyName
ContentType: application/atom+xml;type=entry;charset=utf-8
``` 

이 방식은 모든 항목에 대해 작동한다는 것을 기억하세요. 큐, 토픽, 구독에 대해 SAS를 만들 수 있습니다. 

발신자 또는 클라이언트에게 SAS 토큰을 제공하면 직접 키를 가질 수 없으며 키를 얻기 위해 해시를 되돌릴 수도 없습니다. 예를 들어 사용자가 액세스할 수 있는 항목 및 액세스 기간을 제어할 수 있습니다. 정책에서 기본 키를 다시 생성하거나 변경할 경우, 만든 공유 액세스 서명이 무효화되므로 주의해야 합니다.

## <a name="use-the-shared-access-signature-at-amqp-level"></a>공유 액세스 서명(AMQP 수준) 사용 

이전 섹션에서는 HTTP POST 요청과 함께 SAS 토큰을 사용하여 데이터를 서비스 버스에 보내는 방법을 살펴봤습니다. 아시다시피, 서비스 버스는 많은 시나리오에서 성능상의 이유로 사용하는 기본 설정 프로토콜인 AMQP(고급 메시지 큐 프로토콜)를 사용하여 액세스할 수 있습니다. AMQP와 함께 SAS 토큰을 사용하는 방법은 2013년 이후 초안 상태이지만 현재 Azure에서 충분한 지원을 받고 있는 문서 [AMQP 클레임 기반 보안 버전 1.0](https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc) 에 설명되어 있습니다.

Service Bus에 데이터의 전송을 시작하기 전에 게시자는 AMQP 메시지 안에 있는 SAS 토큰을 **$cbs**(모든 SAS 토큰을 얻고 유효성을 검사하기 위해 서비스에서 사용하는 "특별" 큐)라는 이름의 정의된 AMQP 노드에 전송해야 합니다. 게시자는 AMQP 메시지 내부에 있는 **ReplyTo** 필드를 지정해야 합니다. 이는 서비스가 토큰 유효성 검사 결과와 함께 게시자에게 응답하는 노드입니다(게시자와 서비스 간의 간단한 요청/응답 패턴). 이 회신 노드는 "즉시" 생성되며 AMQP 1.0 사양에 설명된 것처럼 “원격 노드 동적 생성”에 대해 얘기합니다. SAS 토큰이 유효한지 확인한 후 게시자는 이제 데이터를 서비스에 보내기 시작할 수 있습니다.

다음 단계에서는 [AMQP.Net Lite](https://github.com/Azure/amqpnetlite) 라이브러리를 사용하여 AMQP 프로토콜을 통해 SAS 토큰을 보내는 방법을 보여 줍니다. 이 방법은 C\#으로 개발하는 공식적인 Service Bus SDK를 사용할 수 없는 경우(예를 들어 WinRT, .Net Compact Framework, .Net Micro Framework 및 Mono)에 유용합니다. 물론 이 라이브러리는 클레임 기반 보안이 HTTP 수준에서 작동하는 방식을 볼 때처럼 AMQP 수준에서 작동하는 방식을 이해하는 데 유용합니다("권한 부여" 헤더 내에서 전송되는 HTTP POST 요청 및 SAS 토큰과 함께). AMQP에 대한 깊은 지식이 없어도 .Net Framework 응용 프로그램과 함께 공식 서비스 버스 SDK를 사용할 수 있습니다(위 참조).

### <a name="c35"></a>C&#35;

```csharp
/// <summary>
/// Send claim-based security (CBS) token
/// </summary>
/// <param name="shareAccessSignature">Shared access signature (token) to send</param>
private bool PutCbsToken(Connection connection, string sasToken)
{
    bool result = true;
    Session session = new Session(connection);

    string cbsClientAddress = "cbs-client-reply-to";
    var cbsSender = new SenderLink(session, "cbs-sender", "$cbs");
    var cbsReceiver = new ReceiverLink(session, cbsClientAddress, "$cbs");

    // construct the put-token message
    var request = new Message(sasToken);
    request.Properties = new Properties();
    request.Properties.MessageId = Guid.NewGuid().ToString();
    request.Properties.ReplyTo = cbsClientAddress;
    request.ApplicationProperties = new ApplicationProperties();
    request.ApplicationProperties["operation"] = "put-token";
    request.ApplicationProperties["type"] = "servicebus.windows.net:sastoken";
    request.ApplicationProperties["name"] = Fx.Format("amqp://{0}/{1}", sbNamespace, entity);
    cbsSender.Send(request);

    // receive the response
    var response = cbsReceiver.Receive();
    if (response == null || response.Properties == null || response.ApplicationProperties == null)
    {
        result = false;
    }
    else
    {
        int statusCode = (int)response.ApplicationProperties["status-code"];
        if (statusCode != (int)HttpStatusCode.Accepted && statusCode != (int)HttpStatusCode.OK)
        {
            result = false;
        }
    }

    // the sender/receiver may be kept open for refreshing tokens
    cbsSender.Close();
    cbsReceiver.Close();
    session.Close();

    return result;
}
```

위의 `PutCbsToken()` 메서드는 서비스에 대한 TCP 연결 및 전송할 SAS 토큰인 *sasToken* 매개 변수를 나타내는 *연결*([AMQP .NET Lite 라이브러리](https://github.com/Azure/amqpnetlite)에서 제공하는 대로 AMQP 연결 클래스 인스턴스)을 수신합니다. 

> [!NOTE]
> 연결이 **EXTERNAL로 설정된 SASL 인증 메커니즘** (SAS 토큰을 보낼 필요가 없을 때 사용하는 사용자 이름 및 암호를 가진 기본 PLAIN이 아님)으로 생성된다는 사실이 중요합니다.
> 
> 

그런 다음 게시자는 SAS 토큰을 보내고 서비스로부터 회신(토큰 유효성 검사 결과)을 받기 위한 2개의 AMQP 링크를 만듭니다.

AMQP 메시지는 간단한 메시지보다 정보가 많고 속성이 많습니다. SAS 토큰은 해당 생성자를 사용하여 메시지의 본문으로 배치됩니다. **"ReplyTo"** 속성은 수신기 링크에 대한 유효성 검사 결과를 받기 위한 노드 이름으로 설정됩니다(원하는 대로 이름을 변경할 수 있으며 서비스에서 동적으로 생성함). 마지막 세 응용 프로그램/사용자 지정 속성은 서비스에서 실행하는 작업의 종류를 나타내는 데 사용합니다. CBS 초안 사양에서 설명한 것처럼 이들은 **토큰의 형식**(이 경우 "servicebus.windows.net:sastoken")인 **작업 이름**("put-token")이 되고 토큰이 적용되는 **청중의 "이름"**이어야 합니다(전체 엔터티).

보낸 사람 링크에서 SAS 토큰을 보낸 후 게시자는 수신자 링크에서 회신을 읽어야 합니다. 회신은 HTTP 상태 코드와 동일한 값을 포함할 수 있는 **"status-code"** 라는 이름의 응용 프로그램 속성을 가진 간단한 AMQP 메시지입니다.

## <a name="rights-required-for-service-bus-operations"></a>서비스 버스 작업에 필요한 권한

다음 테이블에서는 서비스 버스 리소스의 다양한 작업에 필요한 액세스 권한을 보여줍니다.

| 작업 | 필요한 클레임 | 클레임 범위 |
| --- | --- | --- |
| **네임스페이스** | | |
| 네임스페이스에서 권한 부여 규칙 구성 |관리 |네임스페이스 주소 |
| **서비스 레지스트리** | | |
| 개인 정책 열거 |관리 |네임스페이스 주소 |
| 네임스페이스에서 수신 시작 |수신 대기 |네임스페이스 주소 |
| 네임스페이스에서 수신기로 메시지 보내기 |보내기 |네임스페이스 주소 |
| **큐** | | |
| 큐 만들기 |관리 |네임스페이스 주소 |
| 큐 삭제 |관리 |유효한 큐 주소 |
| 큐 열거 |관리 |/$Resources/Queues |
| 큐 설명 가져오기 |관리 |유효한 큐 주소 |
| 큐에서 권한 부여 규칙 구성 |관리 |유효한 큐 주소 |
| 큐로 보내기 |보내기 |유효한 큐 주소 |
| 큐에서 메시지 받기 |수신 대기 |유효한 큐 주소 |
| 메시지 보기-잠금 모드에서 메시지를 받은 후에 중단 또는 완료 |수신 대기 |유효한 큐 주소 |
| 나중에 검색에 대한 메시지 연기 |수신 대기 |유효한 큐 주소 |
| 메시지 효력 상실 |수신 대기 |유효한 큐 주소 |
| 메시지 큐 세션을 사용하여 연결된 상태 가져오기 |수신 대기 |유효한 큐 주소 |
| 메시지 큐 세션을 사용하여 연결된 상태 설정 |수신 대기 |유효한 큐 주소 |
| **항목** | | |
| 토픽 만들기 |관리 |네임스페이스 주소 |
| 항목 삭제 |관리 |유효한 항목 주소 |
| 항목 열거 |관리 |/$Resources/Topics |
| 항목 설명 가져오기 |관리 |유효한 항목 주소 |
| 항목에서 권한 부여 규칙 구성 |관리 |유효한 항목 주소 |
| 항목으로 보내기 |보내기 |유효한 항목 주소 |
| **구독** | | |
| 구독 만들기 |관리 |네임스페이스 주소 |
| 구독 삭제 |관리 |../myTopic/Subscriptions/mySubscription |
| 구독 열거 |관리 |../myTopic/Subscriptions |
| 구독 설명 가져오기 |관리 |../myTopic/Subscriptions/mySubscription |
| 메시지 보기-잠금 모드에서 메시지를 받은 후에 중단 또는 완료 |수신 대기 |../myTopic/Subscriptions/mySubscription |
| 나중에 검색에 대한 메시지 연기 |수신 대기 |../myTopic/Subscriptions/mySubscription |
| 메시지 효력 상실 |수신 대기 |../myTopic/Subscriptions/mySubscription |
| 항목 세션을 사용하여 연결된 상태 가져오기 |수신 대기 |../myTopic/Subscriptions/mySubscription |
| 항목 세션을 사용하여 연결된 상태 설정 |수신 대기 |../myTopic/Subscriptions/mySubscription |
| **규칙** | | |
| 규칙 만들기 |관리 |../myTopic/Subscriptions/mySubscription |
| 규칙 삭제 |관리 |../myTopic/Subscriptions/mySubscription |
| 규칙 열거 |관리 또는 수신 |../myTopic/Subscriptions/mySubscription/Rules 

## <a name="next-steps"></a>다음 단계

Service Bus 메시징에 대해 자세히 알아보려면 다음 항목을 참조하세요.

* [서비스 버스 기본 사항](service-bus-fundamentals-hybrid-solutions.md)
* [Service Bus 큐, 토픽 및 구독](service-bus-queues-topics-subscriptions.md)
* [서비스 버스 큐를 사용하는 방법](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus 토픽 및 구독을 사용하는 방법](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[Azure portal]: https://portal.azure.com
