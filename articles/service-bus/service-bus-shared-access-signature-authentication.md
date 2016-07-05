<properties 
    pageTitle="서비스 버스를 사용한 공유 액세스 서명 인증 | Microsoft Azure"
    description="서비스 버스를 사용하는 SAS 인증에 대한 세부 정보입니다."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="06/22/2016"
    ms.author="sethm" />

# 서비스 버스를 사용한 공유 액세스 서명 인증

[공유 액세스 서명(SAS)](service-bus-sas-overview.md) 인증을 사용하면 응용 프로그램을 네임 스페이스 또는 특정 권한이 연관된 메시징 엔터티(큐 또는 항목)에서 구성된 선택키를 사용하여 서비스 버스에 인증할 수 있습니다. 그런 다음 이 키를 사용하여 클라이언트가 서비스 버스를 인증하는 데 차례로 사용할 수 있는 SAS 토큰을 생성할 수 있습니다.

SAS 인증 지원은 Azure SDK 버전 2.0 이후에 포함됩니다. 서비스 버스 인증에 대한 자세한 내용은 [서비스 버스 인증 및 권한 부여](service-bus-authentication-and-authorization.md)를 참조하세요.

## 개념

서비스 버스에서 SAS 인증은 서비스 버스 리소스에 연결된 권한이 있는 암호화 키의 구성을 포함합니다. 클라이언트는 SAS 토큰을 발급하여 서비스 버스 리소스에 액세스를 주장합니다. 이 토큰은 액세스된 리소스 URI 및 구성된 키로 서명된 만료 매개 변수로 구성됩니다.

서비스 버스 [릴레이](service-bus-fundamentals-hybrid-solutions.md#relays), [큐](service-bus-fundamentals-hybrid-solutions.md#queues), [항목](service-bus-fundamentals-hybrid-solutions.md#topics) 및 [이벤트 허브](service-bus-fundamentals-hybrid-solutions.md#event-hubs)에서 공유 액세스 서명 권한 부여 규칙을 구성할 수 있습니다.

SAS 인증은 다음과 같은 요소를 사용합니다.

- [공유 액세스 권한 부여 규칙](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx): Base64 표현에서 256비트 기본 암호화 키, 선택적 보조키 및 키 이름과 관련된 권한입니다.(*수신*, *보내기* 또는 *관리* 권한의 컬렉션)

- [공유 액세스 서명](https://msdn.microsoft.com/library/azure/microsoft.servicebus.sharedaccesssignaturetokenprovider.sharedaccesssignature.aspx) 토큰: 리소스 문자열의 HMAC-SHA256을 사용하여 생성되고 액세스된 리소스의 URI 및 암호화 키를 가진 만료 매개 변수로 구성됩니다. 다음 섹션에 설명된 서명 및 다른 요소는 문자열로 서식이 지정되어 [SharedAccessSignature](https://msdn.microsoft.com/library/azure/microsoft.servicebus.sharedaccesssignaturetokenprovider.sharedaccesssignature.aspx) 토큰을 형성합니다.

## 공유 액세스 서명 인증을 위한 구성

서비스 버스 네임스페이스, 큐 또는 항목에 대한 [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx) 규칙을 구성할 수 있습니다. 서비스 버스 구독에서 [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx)의 구성은 현재 지원되지 않지만 네임스페이스 또는 항목에 구성된 규칙을 사용하여 구독에 액세스할 수 있습니다. 이 절차를 설명하는 작업 샘플은 [서비스 버스 구독으로 공유 액세스 서명(SAS) 인증 사용](http://code.msdn.microsoft.com/Using-Shared-Access-e605b37c) 샘플을 참조하세요.

이러한 규칙을 서비스 버스 네임 스페이스, 큐 또는 항목에서 최대 12개까지 구성할 수 있습니다. 서비스 버스 네임 스페이스에 구성된 규칙이 해당 네임스페이스에 있는 모든 엔터티에 적용됩니다.

![SAS](./media/service-bus-shared-access-signature-authentication/IC676272.gif)

이 그림에서 *listenRuleQ* 및 *sendRuleQ*이 큐 Q1 및 *sendRuleT* 항목 T1에만 적용되는 반면 *manageRuleNS*, *sendRuleNS*, 및 *listenRuleNS* 권한 부여 규칙은 큐 Q1 및 항목 T1 모두에 적용합니다.

[SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx)의 키 매개 변수는 다음과 같습니다.

|매개 변수|설명|
|---|---|
|*KeyName*|권한 부여 규칙을 설명하는 문자열입니다.|
|*PrimaryKey*|서명하고 SAS 토큰의 유효성을 검사하기 위한 base64로 인코딩된 256비트 기본 키입니다.|
|*SecondaryKey*|서명하고 SAS 토큰의 유효성을 검사하기 위한 base64로 인코딩된 256비트 보조 키입니다.|
|*AccessRights*|권한 부여 규칙에서 부여된 액세스 권한의 목록입니다. 이러한 권한은 수신, 보내기 및 관리 권한의 컬렉션일 수 있습니다.|

서비스 버스 네임스페이스를 프로비전하면 기본적으로 [KeyName](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.keyname.aspx)이 **RootManageSharedAccessKey**로 설정된 [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx)이 생성됩니다.

## 공유 액세스 권한 부여 규칙에 대한 키를 다시 생성하고 해지합니다.

[SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx) 개체에서 정기적으로 사용되는 키를 다시 생성하는 것이 좋습니다. 응용 프로그램은 일반적으로 [PrimaryKey](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.primarykey.aspx)를 사용하여 SAS 토큰을 생성해야 합니다. 키를 다시 생성하는 경우 [SecondaryKey](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.secondarykey.aspx)를 이전 기본 키로 교체하고 새 키를 새 기본 키로 생성합니다. 이 옵션을 사용하면 이전 기본 키로 발급되고 아직 만료되지 않은 권한 부여에 대한 토큰을 사용하여 계속할 수 있습니다.

키가 손상되고 키를 해제하면 [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx)의 [PrimaryKey](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.primarykey.aspx) 및 [SecondaryKey](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.secondarykey.aspx) 모두를 새 키로 바꿔 다시 생성할 수 있습니다. 이 절차는 이전 키로 서명된 모든 토큰을 무효화합니다.

## 공유 액세스 서명 토큰 생성

공유 액세스 권한 부여 규칙에 지정된 서명 키에 액세스하는 클라이언트는 SAS 토큰을 생성할 수 있습니다. 다음과 같이 형식이 지정됩니다.

```
SharedAccessSignature sig=<signature-string>&se=<expiry>&skn=<keyName>&sr=<URL-encoded-resourceURI>
```

SAS 토큰에 대한 **서명**은 권한 부여 규칙의 [PrimaryKey](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.primarykey.aspx) 속성으로 서명하려는 문자열의 HMAC-SHA256해시를 사용하여 계산됩니다. 서명할 문자열은 다음과 같이 서식이 지정된 리소스 URI 및 만료 매개 변수로 구성됩니다.

```
StringToSign = <resourceURI> + "\n" + expiry;
```

이 작업에 인코딩된 리소스 URI를 사용해야 합니다. 리소스 URI은 액세스를 하려는 서비스 버스 리소스의 전체 URI입니다. 예를 들면 `http://<namespace>.servicebus.windows.net/<entityPath>` 또는 `sb://<namespace>.servicebus.windows.net/<entityPath>`입니다. 즉, `http://contoso.servicebus.windows.net/contosoTopics/T1/Subscriptions/S3`과 같습니다.

만료는 1970년 1월 1일 epoch 0시 UTC 이후의 초 수로 표시됩니다.

이 URI로 또는 부모 계층 중 하나에 의해 지정된 엔터티에 서명에 사용된 공유 액세스 권한 부여 규칙을 구성해야 합니다. 예를 들어 이전 예에서 `http://contoso.servicebus.windows.net/contosoTopics/T1` 또는 `http://contoso.servicebus.windows.net`입니다.

SAS 토큰은 서명할 문자열에서 사용된 `<resourceURI>`에서 모든 리소스에 유효합니다.

SAS 토큰의 [KeyName](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.keyname.aspx)은 토큰을 생성하는 데 사용된 공유 액세스 권한 부여 규칙의 **keyName**을 가리킵니다.

*URL로 인코딩된 resourceURI*은 서명을 계산하는 동안 서명할 문자열에서 사용된 URI와 동일해야 합니다. [%로 인코딩](https://msdn.microsoft.com/library/4fkewx0t.aspx)되어야 합니다.

## 서비스 버스를 사용한 공유 액세스 서명을 인증하는 방법

다음과 같은 시나리오는 권한 부여 규칙의 구성, SAS 토큰의 생성 및 클라이언트 권한 부여를 포함합니다.

구성을 설명하고 SAS 권한 부여를 사용하는 서비스 버스 응용 프로그램의 작업 샘플 전체는 [서비스 버스를 사용하여 공유 액세스 서명 인증](http://code.msdn.microsoft.com/Shared-Access-Signature-0a88adf8)을 참조하세요. 서비스 버스 구독을 보호하기 위해 네임스페이스 또는 항목에 구성된 SAS 권한 부여 규칙의 사용 방법을 설명하는 관련된 샘플은 [서비스 버스 구독으로 공유 액세스 서명(SAS) 사용](http://code.msdn.microsoft.com/Using-Shared-Access-e605b37c)에서 사용 가능합니다..

## 네임스페이스에 대한 공유 액세스 권한 부여 규칙 액세스

서비스 버스 네임스페이스 루트에 대한 작업은 인증서 인증이 필요합니다. Azure 구독에 대한 관리 인증서를 업로드해야 합니다. 관리 인증서를 업로드하려면 [Azure 클래식 포털][]의 왼쪽 창에서 **설정**을 클릭합니다. Azure 관리 인증서에 대한 자세한 내용은 [Azure 인증서 개요](../cloud-services/cloud-services-certs-create.md#what-are-management-certificates)를 참조하세요.

서비스 버스 네임스페이스에 대한 공유 액세스 권한 부여 규칙에 액세스하기 위한 끝점은 다음과 같습니다.

```
https://management.core.windows.net/{subscriptionId}/services/ServiceBus/namespaces/{namespace}/AuthorizationRules/
```

서비스 버스 네임 스페이스에서 [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx) 개체를 만들려면 이 끝점에서 JSON 또는 XML로 직렬화된 규칙 정보를 사용하여 게시 작업을 실행합니다. 예:

```
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

```
https://management.core.windows.net/{subscriptionId}/services/ServiceBus/namespaces/{namespace}/AuthorizationRules/{KeyName}
```

## 엔터티에 대한 공유 액세스 권한 부여 규칙 액세스

해당 [QueueDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.aspx), [TopicDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicdescription.aspx) 또는 [NotificationHubDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.notifications.notificationhubdescription.aspx) 개체에서 [AuthorizationRules](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.authorizationrules.aspx) 컬렉션을 통해 서비스 버스 큐 또는 항목에 구성된 [Microsoft.ServiceBus.Messaging.SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx) 개체에 액세스할 수 있습니다.

다음 코드는 큐에 대한 권한 부여 규칙을 추가하는 방법을 보여줍니다.

```
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

## 공유 액세스 서명 권한 부여 사용

서비스 버스.NET 라이브러리로 Azure.NET SDK를 사용하는 응용 프로그램은 [SharedAccessSignatureTokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.sharedaccesssignaturetokenprovider.aspx) 클래스를 통해 SAS 권한 부여를 사용할 수 있습니다. 다음 코드에서는 토큰 공급자를 사용하여 서비스 버스 큐에 메시지를 보냅니다.

```
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

서비스 버스 릴레이로 SAS 권한 부여를 사용하면 서비스 버스 네임 스페이스에 구성된 SAS 키를 사용할 수 있습니다. 네임스페이스([NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx)와 [RelayDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.relaydescription.aspx)) 개체에 명시적으로 릴레이를 만들면 해당 릴레이 대한 SAS 규칙을 설정할 수 있습니다. 서비스 버스 구독으로 SAS 권한 부여를 사용하려면 서비스 버스 네임 스페이스 또는 항목에 구성된 SAS 키를 사용할 수 있습니다.

## 서비스 버스 작업에 필요한 권한

다음 테이블에서는 서비스 버스 리소스의 다양한 작업에 필요한 액세스 권한을 보여줍니다.

|작업|필요한 클레임|클레임 범위|
|---|---|---|
|**네임스페이스**|||
|네임스페이스에서 권한 부여 규칙 구성|관리|네임스페이스 주소|
|**서비스 레지스트리**|||
|개인 정책 열거|관리|네임스페이스 주소|
|릴레이|||
|네임스페이스에서 수신 시작|수신 대기|네임스페이스 주소|
|네임스페이스에서 수신기로 메시지 보내기|보내기|네임스페이스 주소|
|**큐**|||
|큐 만들기|관리|네임스페이스 주소|
|큐 삭제|관리|유효한 큐 주소|
|큐 열거|관리|/$Resources/Queues|
|큐 설명 가져오기|관리 또는 보내기|유효한 큐 주소|
|큐에서 권한 부여 규칙 구성|관리|유효한 큐 주소|
|큐로 보내기|보내기|유효한 큐 주소|
|큐에서 메시지 받기|수신 대기|유효한 큐 주소|
|메시지 보기-잠금 모드에서 메시지를 받은 후에 중단 또는 완료|수신 대기|유효한 큐 주소|
|나중에 검색에 대한 메시지 연기|수신 대기|유효한 큐 주소|
|메시지 효력 상실|수신 대기|유효한 큐 주소|
|메시지 큐 세션을 사용하여 연결된 상태 가져오기|수신 대기|유효한 큐 주소|
|메시지 큐 세션을 사용하여 연결된 상태 설정|수신 대기|유효한 큐 주소|
|**항목**|||
|토픽 만들기|관리|네임스페이스 주소|
|항목 삭제|관리|유효한 항목 주소|
|항목 열거|관리|/$Resources/Topics|
|항목 설명 가져오기|관리 또는 보내기|유효한 항목 주소|
|항목에서 권한 부여 규칙 구성|관리|유효한 항목 주소|
|항목으로 보내기|보내기|유효한 항목 주소|
|**구독**|||
|구독 만들기|관리|네임스페이스 주소|
|구독 삭제|관리|../myTopic/Subscriptions/mySubscription|
|구독 열거|관리|../myTopic/Subscriptions|
|구독 설명 가져오기|관리 또는 수신|../myTopic/Subscriptions/mySubscription|
|메시지 보기-잠금 모드에서 메시지를 받은 후에 중단 또는 완료|수신 대기|../myTopic/Subscriptions/mySubscription|
|나중에 검색에 대한 메시지 연기|수신 대기|../myTopic/Subscriptions/mySubscription|
|메시지 효력 상실|수신 대기|../myTopic/Subscriptions/mySubscription|
|항목 세션을 사용하여 연결된 상태 가져오기|수신 대기|../myTopic/Subscriptions/mySubscription|
|항목 세션을 사용하여 연결된 상태 설정|수신 대기|../myTopic/Subscriptions/mySubscription|
|**규칙**|||
|규칙 만들기|관리|../myTopic/Subscriptions/mySubscription|
|규칙 삭제|관리|../myTopic/Subscriptions/mySubscription|
|규칙 열거|관리 또는 수신|../myTopic/Subscriptions/mySubscription/Rules|
|**알림 허브**|||
|알림 허브 만들기|관리|네임스페이스 주소|
|활성 장치에 등록 만들기 또는 업데이트|수신 또는 관리|../notificationHub/tags/{tag}/registrations|
|PNS 정보 업데이트|수신 또는 관리|../notificationHub/tags/{tag}/registrations/updatepnshandle|
|알림 허브 보내기|보내기|../notificationHub/messages|

## 다음 단계

서비스 버스에서 SAS에 대한 높은 수준의 개요는 [공유 액세스 서명](service-bus-sas-overview.md)을 참조하세요.

서비스 버스 인증에 대한 자세한 백그라운드는 [서비스 버스 인증 및 권한 부여](service-bus-authentication-and-authorization.md)를 참조하세요.

[Azure 클래식 포털]: http://manage.windowsazure.com

<!---HONumber=AcomDC_0622_2016-->