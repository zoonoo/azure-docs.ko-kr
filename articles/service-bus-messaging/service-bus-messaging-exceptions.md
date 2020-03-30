---
title: Azure 서비스 버스문제 해결 가이드 | 마이크로 소프트 문서
description: 이 문서에서는 Azure Service Bus 메시징 예외 목록 및 예외가 발생할 때 수행할 권장 작업 목록을 제공합니다.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 3d8526fe-6e47-4119-9f3e-c56d916a98f9
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/23/2020
ms.author: aschhab
ms.openlocfilehash: fb27befadcf8e6d201d020e758cfd1ef9b695f41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240815"
---
# <a name="troubleshooting-guide-for-azure-service-bus"></a>Azure 서비스 버스에 대한 문제 해결 가이드
이 문서에서는 Service Bus .NET Framework API에서 생성된 몇 가지 예외와 문제 해결을 위한 다른 팁을 제공합니다. 

## <a name="service-bus-messaging-exceptions"></a>Service Bus 메시징 예외
이 섹션에는 .NET Framework API에서 생성된 .NET 예외가 나열되어 있습니다. 

### <a name="exception-categories"></a>예외 범주
메시징 API는 다음 범주에 해당하는 예외와, 해당 예외의 해결을 위해 수행할 수 있는 관련 조치를 함께 생성합니다. 예외의 의미 및 원인은 메시징 엔터티의 형식에 따라 달라질 수 있습니다.

1. 사용자 코딩 오류[(System.ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System.InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System.OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [System.Runtime.Serialization.Serialization.SerializationException).](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx) 일반 조치: 코드를 수정한 후 계속합니다.
2. 설치/구성 오류[(Microsoft.ServiceBus.Messaging.Messaging.MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception), [시스템.무단 액세스예외](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). 일반 조치: 구성을 검토하고 필요한 경우 변경합니다.
3. 일시적 예외[(Microsoft.ServiceBus.Messaging.Messaging.MessagingException,](/dotnet/api/microsoft.servicebus.messaging.messagingexception) [Microsoft.ServiceBus.Messaging.Server.Server바쁜 예외,](/dotnet/api/microsoft.azure.servicebus.serverbusyexception) [Microsoft.ServiceBus.Messaging.Messaging.Messaging통신예외).](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) 일반 조치: 작업을 다시 시도하거나 사용자에게 알립니다. 클라이언트 `RetryPolicy` SDK의 클래스는 재시도를 자동으로 처리하도록 구성할 수 있습니다. 자세한 내용은 [다시 시도 지침을](/azure/architecture/best-practices/retry-service-specific#service-bus)참조하십시오.
4. 기타 예외 ([System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System.TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx), [Microsoft.ServiceBus.Messaging.MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception), [Microsoft.ServiceBus.Messaging.SessionLockLostException](/dotnet/api/microsoft.azure.servicebus.sessionlocklostexception)). 일반 작업: 예외 유형에 따라 다릅니다. 다음 섹션의 표를 참조하십시오. 

### <a name="exception-types"></a>예외 유형
다음 표에서는 메시징 예외 유형과 원인, 사용자가 수행할 수 있는 제안 조치 참고를 열거합니다.

| **예외 유형** | **설명/원인/예** | **제안된 조치** | **자동/즉시 다시 시도 참고** |
| --- | --- | --- | --- |
| [시간 시간 제외](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |서버는 [OperationTimeout에](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings)의해 제어되는 지정된 시간 내에 요청된 작업에 응답하지 않았습니다. 서버가 요청된 작업을 완료했을 수도 있습니다. 네트워크 또는 기타 인프라 지연으로 인해 발생할 수 있습니다. |필요한 경우 시스템 상태에서 일관성을 확인하고 다시 시도합니다. [시간 제한 예외](#timeoutexception)를 참조하세요. |일부 경우 다시 시도하면 문제가 해결될 수 있습니다. 코드에 재시도 논리를 추가합니다. |
| [InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |요청된 사용자 작업은 서버 또는 서비스 내에서 허용되지 않습니다. 자세한 내용은 예외 메시지를 참조하세요. 예를 들어 [Complete()는](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) [메시지가 ReceiveAndDelete](/dotnet/api/microsoft.azure.servicebus.receivemode) 모드에서 수신된 경우 이 예외를 생성합니다. |코드 및 설명서를 확인합니다. 요청된 작업이 유효한지 확인합니다. |다시 시도는 도움이되지 않습니다. |
| [OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) |이미 종료, 중단 또는 삭제된 개체에서 작업을 호출하려 시도합니다. 드문 경우지만 앰비언트 트랜잭션이 이미 삭제되었을 수 있습니다. |코드를 확인하고 삭제된 개체에서 작업을 호출하지 않는지 확인합니다. |다시 시도는 도움이되지 않습니다. |
| [UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) |[TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) 개체는 토큰을 획득할 수 없거나, 토큰이 유효하지 않거나, 토큰에 작업을 수행하는 데 필요한 클레임이 포함되어 있지 않습니다. |올바른 값을 사용하여 토큰 공급자를 만드는지 확인합니다. 액세스 제어 서비스의 구성을 확인합니다. |일부 경우 다시 시도하면 문제가 해결될 수 있습니다. 코드에 재시도 논리를 추가합니다. |
| [ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [ArgumentNullException](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[ArgumentOutOfRangeException](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) |메서드에 제공된 하나 이상의 인수가 잘못되었습니다.<br /> [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) 또는 [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory)에 제공된 URI에 경로 세그먼트가 포함됩니다.<br /> [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) 또는 [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory)에 제공된 URI 스키마가 올바르지 않습니다. <br />속성 값이 32KB보다 큽니다. |호출 코드를 확인하고 인수가 정확한지 확인합니다. |다시 시도는 도움이되지 않습니다. |
| [MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception) |작업과 연결된 엔터티가 없거나 삭제되었습니다. |엔터티가 있는지 확인합니다. |다시 시도는 도움이되지 않습니다. |
| [MessageNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagenotfoundexception) |특정 시퀀스 번호를 통해 메시지 수신을 시도합니다. 이 메시지를 찾을 수 없습니다. |메시지가 아직 수신되지 않았는지 확인합니다. 전달 실패 큐에서 메시지가 전달되지 않았는지 확인합니다. |다시 시도는 도움이되지 않습니다. |
| [MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) |클라이언트는 서비스 버스에 대한 연결을 설정할 수 없습니다. |제공된 호스트 이름이 정확하며 호스트에 연결할 수 있는지 확인합니다. |간헐적인 연결 문제라면 재시도로 문제를 해결할 수 있습니다. |
| [ServerBusyException](/dotnet/api/microsoft.azure.servicebus.serverbusyexception) |현재 서비스는 요청을 처리할 수 없습니다. |클라이언트가 잠시 대기한 후 작업을 다시 시도할 수 있습니다. |클라이언트가 일정 시간 이후에 다시 시도할 수 있습니다. 재시도에서 다른 예외가 발생한 경우 해당 예외의 재시도 작동을 확인합니다. |
| [MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception) |메시지와 연결된 잠금 토큰이 만료되었거나 잠금 토큰을 찾을 수 없습니다. |메시지를 제거합니다. |다시 시도는 도움이되지 않습니다. |
| [SessionLockLostException](/dotnet/api/microsoft.azure.servicebus.sessionlocklostexception) |이 세션에 연결된 잠금이 손실되었습니다. |[MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) 개체를 중단합니다. |다시 시도는 도움이되지 않습니다. |
| [MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception) |다음 상황에 발생할 수 있는 일반 메시징 예외:<p>다른 엔터티 형식에 속하는(예: topic) 이름이나 경로를 사용하여 [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient)를 만들려고 시도합니다.</p><p>256KB보다 큰 메시지를 전송하려고 시도합니다. </p>서버 또는 서비스가 요청을 처리하는 동안 오류가 발생했습니다. 자세한 내용은 예외 메시지를 참조하세요. 일반적으로 일시적인 예외입니다.</p><p>엔터티가 제한되고 있기 때문에 요청이 종료되었습니다. 오류 코드: 50001, 50002, 50008. </p> | 코드를 확인하고 메시지 본문에서 직렬화 가능 개체(또는 사용자 지정 직렬 변환기 사용)만 사용하는지 확인합니다. <p>설명서에서 지원되는 속성 값 유형을 확인하고 지원되는 유형만 사용합니다.</p><p> [IsTransient](/dotnet/api/microsoft.servicebus.messaging.messagingexception) 속성을 확인합니다. **true이면**작업을 다시 시도할 수 있습니다. </p>| 제한으로 인한 예외인 경우 몇 초 동안 기다렸다가 작업을 다시 다시 시도합니다. 다시 시도 동작은 정의되지 않았으며 다른 시나리오에서는 도움이 되지 않을 수 있습니다.|
| [MessagingEntityAlreadyExistsException](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) |해당 서비스 네임스페이스에서 이미 다른 엔터티가 사용하는 이름으로 엔터티를 만들려고 합니다. |기존 엔터티를 삭제하거나 만들려는 엔터티에 다른 이름을 선택합니다. |다시 시도는 도움이되지 않습니다. |
| [QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) |메시징 엔터티가 최대 허용 크기에 도달했거나 네임스페이스에 대한 최대 연결 수를 초과했습니다. |엔터티나 하위 큐에서 메시지를 수신하여 엔터티에 공간을 만듭니다. [할당량초과예외](#quotaexceededexception). |그 사이 메시지가 제거되었으면 재시도가 도움이 될 수 있습니다. |
| [RuleActionException](/dotnet/api/microsoft.servicebus.messaging.ruleactionexception) |잘못된 규칙 작업을 만들려고 시도하면 Service Bus가 이 예외를 반환합니다. 메시지에 대한 규칙 작업 처리 중에 오류가 발생하면 Service Bus가 이 예외를 전달 실패 메시지에 연결합니다. |규칙 작업이 정확한지 확인합니다. |다시 시도는 도움이되지 않습니다. |
| [FilterException](/dotnet/api/microsoft.servicebus.messaging.filterexception) |잘못된 필터를 만들려고 시도하면 Service Bus가 이 예외를 반환합니다. 메시지에 대한 필터 처리 중에 오류가 발생하면 Service Bus가 이 예외를 전달 실패 메시지에 연결합니다. |필터가 정확한지 확인합니다. |다시 시도는 도움이되지 않습니다. |
| [SessionCannotBeLockedException](/dotnet/api/microsoft.servicebus.messaging.sessioncannotbelockedexception) |특정 세션 ID를 갖는 세션을 수락하려 하나 해당 세션이 현재 다른 클라이언트에 잠겨 있습니다. |해당 세션이 다른 클라이언트에서 잠금 해제되었는지 확인합니다. |그 사이에 세션이 릴리스된 경우 다시 시도하면 문제가 해결될 수 있습니다. |
| [TransactionSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.transactionsizeexceededexception) |트랜잭션에 너무 많은 작업이 포함되어 있습니다. |이 트랜잭션에 포함된 작업 수를 줄입니다. |다시 시도는 도움이되지 않습니다. |
| [MessagingEntityDisabledException](/dotnet/api/microsoft.azure.servicebus.messagingentitydisabledexception) |비활성화된 엔터티의 런타임 작업에 대한 요청입니다. |엔터티를 활성화합니다. |그 사이에 엔터티가 활성화된 경우 다시 시도하면 문제가 해결될 수 있습니다. |
| [NoMatchingSubscriptionException](/dotnet/api/microsoft.servicebus.messaging.nomatchingsubscriptionexception) |사전 필터링이 활성화되었으며 일치하는 필터가 없는 항목에 메시지를 보내는 경우 Service Bus가 이 예외를 반환합니다. |하나 이상의 필터가 일치하는지 확인합니다. |다시 시도는 도움이되지 않습니다. |
| [MessageSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) |메시지 페이로드가 256KB 제한을 초과합니다. 256KB 제한은 총 메시지 크기입니다. 여기에는 시스템 속성과 모든 .NET 오버헤드가 포함됩니다. |메시지 페이로드의 크기를 줄인 다음 작업을 다시 시도합니다. |다시 시도는 도움이되지 않습니다. |
| [TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx) |앰비언트 트랜잭션(*Transaction.Current*)이 올바르지 않습니다. 완료되었거나 중단되었을 수 있습니다. 내부 예외가 추가 정보를 제공할 수 있습니다. | |다시 시도는 도움이되지 않습니다. |
| [TransactionInDoubtException](https://msdn.microsoft.com/library/system.transactions.transactionindoubtexception.aspx) |의심스러운 트랜잭션에서 작업을 시도하거나, 트랜잭션을 커밋하려고 시도하는데 해당 트랜잭션의 상태가 확실하지 않습니다. |트랜잭션이 이미 커밋되었을 수 있으므로 애플리케이션에서 이 예외를 처리해야 합니다(특수한 경우). |- |

### <a name="quotaexceededexception"></a>QuotaExceededException
[QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) 은 특정 엔터티에 대한 할당량이 초과됐음을 나타냅니다.

#### <a name="queues-and-topics"></a>큐 및 토픽
큐 및 토픽의 경우 큐의 크기인 경우가 많습니다. 다음 예제와 같이 오류 메시지 속성은 추가 세부 정보를 포함합니다.

```Output
Microsoft.ServiceBus.Messaging.QuotaExceededException
Message: The maximum entity size has been reached or exceeded for Topic: 'xxx-xxx-xxx'. 
    Size of entity in bytes:1073742326, Max entity size in bytes:
1073741824..TrackingId:xxxxxxxxxxxxxxxxxxxxxxxxxx, TimeStamp:3/15/2013 7:50:18 AM
```

메시지는 토픽이 해당 크기 제한을 초과했음을 알려 줍니다. 이 경우 1GB입니다(기본 크기 제한). 

#### <a name="namespaces"></a>네임스페이스

네임스페이스의 경우 [QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception)은 애플리케이션이 네임스페이스에 대한 최대 연결 수를 초과했음을 나타낼 수 있습니다. 예를 들어:

```Output
Microsoft.ServiceBus.Messaging.QuotaExceededException: ConnectionsQuotaExceeded for namespace xxx.
<tracking-id-guid>_G12 ---> 
System.ServiceModel.FaultException`1[System.ServiceModel.ExceptionDetail]: 
ConnectionsQuotaExceeded for namespace xxx.
```

#### <a name="common-causes"></a>일반적인 원인
이 오류에 대한 두 가지 일반적인 원인은 배달 못 한 편지 큐와 정상적으로 작동하지 않는 메시지 수신기입니다.

1. **[배달 못한 편지 대기열](service-bus-dead-letter-queues.md)** 판독기가 메시지를 완료하지 못하고 잠금이 만료되면 메시지가 큐/토픽으로 반환됩니다. 독자가 [BrokeredMessage.Complete를](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.complete)호출하지 못하게 하는 예외가 발생하는 경우 발생할 수 있습니다. 메시지는 10번 읽혀진 후 기본적으로 배달 못 한 편지 큐로 이동합니다. 이 동작은 [QueueDescription.MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) 속성에 의해 제어되고 기본값 10을 가집니다. 메시지는 배달 못 한 편지 큐에 쌓이고 공간을 차지합니다.
   
    이 문제를 해결하려면 다른 큐에서와 마찬가지로 배달 못 한 편지 큐에서 메시지를 읽고 완료합니다. 배달 못 한 편지 큐 경로의 형식을 지정하는 데 도움이 되도록 [FormatDeadLetterPath](/dotnet/api/microsoft.azure.servicebus.entitynamehelper.formatdeadletterpath) 메서드를 사용할 수 있습니다.
2. **수신기 중지됨**. 수신기가 큐 또는 구독에서 메시지 수신을 중지했습니다. 이를 식별하는 방법은 메시지의 전체 분석을 보여 주는 [QueueDescription.MessageCountDetails](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails) 속성에서 확인하는 것입니다. [ActiveMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.activemessagecount) 속성이 높거나 증가하는 경우 메시지가 기록되는 속도만큼 빠르게 읽히지 않습니다.

### <a name="timeoutexception"></a>TimeoutException
[TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) 은 사용자가 시작한 작업이 작업 시간 제한보다 더 오래 걸린다는 것을 나타냅니다. 

이 제한에 도달하면 [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx)이 발생할 수 있으므로 [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit) 속성 값도 확인해야 합니다.

#### <a name="queues-and-topics"></a>큐 및 토픽
큐 및 토픽의 경우 제한 시간은 연결 문자열의 일부로 [MessagingFactorySettings.OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings) 속성에서 또는 [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.azure.servicebus.servicebusconnectionstringbuilder)를 통해 지정됩니다. 오류 메시지 자체는 다를 수 있지만 현재 작업에 대해 지정된 시간 제한 값을 항상 포함합니다. 

## <a name="connectivity-certificate-or-timeout-issues"></a>연결, 인증서 또는 시간 시간 시간 문제
다음 단계는 *.servicebus.windows.net 따라 모든 서비스에 대한 연결/인증서/시간 시간 문제 문제를 해결하는 데 도움이 될 수 있습니다. 

- 을 찾아보거나 [wget.](https://www.gnu.org/software/wget/) `https://<yournamespace>.servicebus.windows.net/` JAVA SDK를 사용할 때 가장 일반적인 IP 필터링 또는 가상 네트워크 또는 인증서 체인 문제가 있는지 확인하는 데 도움이 됩니다.

    성공적인 메시지의 예:
    
    ```xml
    <feed xmlns="http://www.w3.org/2005/Atom"><title type="text">Publicly Listed Services</title><subtitle type="text">This is the list of publicly-listed services currently available.</subtitle><id>uuid:27fcd1e2-3a99-44b1-8f1e-3e92b52f0171;id=30</id><updated>2019-12-27T13:11:47Z</updated><generator>Service Bus 1.1</generator></feed>
    ```
    
    오류 오류 메시지의 예:

    ```json
    <Error>
        <Code>400</Code>
        <Detail>
            Bad Request. To know more visit https://aka.ms/sbResourceMgrExceptions. . TrackingId:b786d4d1-cbaf-47a8-a3d1-be689cda2a98_G22, SystemTracker:NoSystemTracker, Timestamp:2019-12-27T13:12:40
        </Detail>
    </Error>
    ```
- 다음 명령을 실행하여 방화벽에서 포트가 차단되었는지 확인합니다. 사용되는 포트는 443(HTTPS), 5671(AMQP) 및 9354(순 메시징/SBMP)입니다. 사용하는 라이브러리에 따라 다른 포트도 사용됩니다. 다음은 5671 포트가 차단되었는지 여부를 확인하는 샘플 명령입니다. 

    ```powershell
    tnc <yournamespacename>.servicebus.windows.net -port 5671
    ```

    Linux에서:

    ```shell
    telnet <yournamespacename>.servicebus.windows.net 5671
    ```
- 간헐적인 연결 문제가 있는 경우 다음 명령을 실행하여 삭제된 패킷이 있는지 확인합니다. 이 명령은 서비스에서 1초마다 25개의 서로 다른 TCP 연결을 설정하려고 시도합니다. 그런 다음 성공/실패한 횟수를 확인하고 TCP 연결 대기 시간도 확인할 수 있습니다. 여기에서 도구를 `psping` 다운로드할 수 [있습니다.](/sysinternals/downloads/psping)

    ```shell
    .\psping.exe -n 25 -i 1 -q <yournamespace>.servicebus.windows.net:5671 -nobanner     
    ```
    `tnc`.와 `ping`같은 다른 도구를 사용하는 경우 등가 명령을 사용할 수 있습니다. 
- 이전 단계가 도움이 되지 않는 경우 네트워크 추적을 가져오고 [Wireshark와](https://www.wireshark.org/)같은 도구를 사용하여 분석합니다. 필요한 경우 [Microsoft 지원에](https://support.microsoft.com/) 문의하십시오. 

## <a name="issues-that-may-occur-with-service-upgradesrestarts"></a>서비스 업그레이드/다시 시작시 발생할 수 있는 문제
백 엔드 서비스 업그레이드 및 다시 시작하면 응용 프로그램에 다음과 같은 영향을 줄 수 있습니다.

- 요청이 일시적으로 제한될 수 있습니다.
- 들어오는 메시지/요청이 떨어질 수 있습니다.
- 로그 파일에 오류 메시지가 포함될 수 있습니다.
- 응용 프로그램의 연결이 몇 초 동안 서비스에서 분리될 수 있습니다.

응용 프로그램 코드가 SDK를 사용하는 경우 재시도 정책이 이미 빌드되어 활성화되어 있습니다. 응용 프로그램은 응용 프로그램/워크플로에 큰 영향을 주지 않고 다시 연결됩니다.

## <a name="next-steps"></a>다음 단계

전체 Service Bus .NET API 참조는 [Azure .NET API 참조](/dotnet/api/overview/azure/service-bus)를 확인하세요.

[서비스 버스에](https://azure.microsoft.com/services/service-bus/)대한 자세한 내용은 다음 문서를 참조하십시오.

* [Service Bus 메시징 개요](service-bus-messaging-overview.md)
* [Service Bus 아키텍처](service-bus-architecture.md)

