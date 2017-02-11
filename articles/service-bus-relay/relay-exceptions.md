---
title: "릴레이 예외 | Microsoft Docs"
description: "릴레이 예외와 권장된 조치의 목록입니다."
services: service-bus-relay
documentationcenter: na
author: jtaubensee
manager: timlt
editor: tysonn
ms.assetid: 5f9dd02c-cce0-43b3-8eb8-744f0c27f38c
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2016
ms.author: jotaub
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 3feece5fc2497aec59bd065c345b21e800c87fd2


---
# <a name="relay-exceptions"></a>릴레이 예외
이 문서는 Microsoft Azure 릴레이 API를 통해 발생하는 몇 가지 예외를 설명합니다. 이 참조는 변경될 수 있으므로 나중에 다시 업데이트를 확인하세요.

## <a name="exception-categories"></a>예외 범주
릴레이 API는 다음 범주에 해당하는 예외와, 해당 예외의 해결을 위해 수행할 수 있는 관련 조치를 함께 생성합니다.

1. 사용자 코딩 오류([System.ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System.InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System.OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [System.Runtime.Serialization.SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx)). 일반 조치: 코드를 수정한 후 계속합니다.
2. 설치/구성 오류 ([Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingentitynotfoundexception.aspx), [System.UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). 일반 조치: 구성을 검토하고 필요한 경우 변경합니다.
3. 일시적 예외 ([Microsoft.ServiceBus.Messaging.MessagingException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingexception.aspx), [Microsoft.ServiceBus.Messaging.ServerBusyException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.serverbusyexception.aspx), [Microsoft.ServiceBus.Messaging.MessagingCommunicationException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingcommunicationexception.aspx)). 일반 조치: 작업을 다시 시도하거나 사용자에게 알립니다.
4. 기타 예외 ([System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System.TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx), [Microsoft.ServiceBus.Messaging.MessageLockLostException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagelocklostexception.aspx), [Microsoft.ServiceBus.Messaging.SessionLockLostException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sessionlocklostexception.aspx)). 일반 작업: 예외 형식에 특정됩니다. 다음 섹션의 테이블을 참조하세요. 

## <a name="exception-types"></a>예외 유형
다음 표에서는 메시징 예외 유형과 원인, 사용자가 수행할 수 있는 제안 조치 참고를 열거합니다.

| **예외 유형** | **설명** | **권장 조치** | **자동 또는 즉시 다시 시도 참고** |
| --- | --- | --- | --- |
| [시간 제한](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |서버가 [OperationTimeout](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout.aspx)에서 제어하는 지정된 시간 안에 요청된 작업에 응답하지 않았습니다. 서버가 요청된 작업을 완료했을 수도 있습니다. 이 예외는 네트워크 또는 기타 인프라 지연으로 발생합니다. |필요한 경우 시스템 상태에서 일관성을 확인하고 다시 시도합니다. [시간 제한 예외](#timeoutexception)를 참조하세요. |일부 경우 다시 시도하면 문제가 해결될 수 있습니다. 코드에 재시도 논리를 추가합니다. |
| [유효하지 않은 작업](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |요청한 사용자 작업이 서버 또는 서비스 내에서 허용되지 않습니다. 자세한 내용은 예외 메시지를 참조하세요. 예를 들어 [ReceiveAndDelete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) 모드에서 메시지를 받은 경우 **Complete** 가 이 예외를 생성합니다. |코드 및 설명서를 확인합니다. 요청된 작업이 유효한지 확인합니다. |재시도로 해결되지 않습니다. |
| [작업 취소됨](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) |이미 종료, 중단 또는 삭제된 개체에서 작업을 호출하려 시도합니다. 드문 경우지만 앰비언트 트랜잭션이 이미 삭제되었을 수 있습니다. |코드를 확인하고 삭제된 개체에 대해 작업을 호출하지 않는지 확인합니다. |재시도로 해결되지 않습니다. |
| [무단 액세스](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) |[TokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx) 개체가 토큰을 확보할 수 없거나, 토큰이 유효하지 않거나, 토큰에 작업 수행을 위해 필요한 클레임이 없습니다. |올바른 값을 사용하여 토큰 공급자를 만드는지 확인합니다. ACS(액세스 제어 서비스) 구성을 확인합니다. |일부 경우 다시 시도하면 문제가 해결될 수 있습니다. 코드에 재시도 논리를 추가합니다. |
| [인수 예외](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [인수 Null](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[인수가 범위를 벗어남](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) |메서드에 제공된 하나 이상의 인수가 잘못되었습니다.<br /> [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) 또는 [Create](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.create.aspx)에 제공된 URI에 경로 세그먼트가 포함됩니다.<br /> [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) 또는 [Create](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.create.aspx)에 제공된 URI 스키마가 올바르지 않습니다. <br />속성 값이 32KB보다 큽니다. |호출 코드를 확인하고 인수가 정확한지 확인합니다. |재시도로 해결되지 않습니다. |
| [서버 작업 중](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.serverbusyexception.aspx) |서비스가 지금은 요청을 처리할 수 없습니다. |클라이언트가 잠시 대기한 후 작업을 다시 시도할 수 있습니다. |클라이언트가 일정 시간 이후에 다시 시도할 수 있습니다. 재시도에서 다른 예외가 발생한 경우 해당 예외의 재시도 작동을 확인합니다. |
| [할당량이 초과됨](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.quotaexceededexception.aspx) |메시징 엔터티의 최대 허용 크기에 도달했습니다. |엔터티나 하위 큐에서 메시지를 수신하여 엔터티에 공간을 만듭니다. [QuotaExceededException](#quotaexceededexception)을 참조하세요. |그 사이 메시지가 제거되었으면 재시도가 도움이 될 수 있습니다. |
| [메시지 크기 초과됨](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesizeexceededexception.aspx) |메시지 페이로드가 256K 제한을 초과합니다. 256K 제한은 총 메시지 크기입니다. 여기에는 시스템 속성과 모든 .NET 오버헤드가 포함됩니다. |메시지 페이로드의 크기를 줄인 다음 작업을 다시 시도합니다. |재시도로 해결되지 않습니다. |

## <a name="quotaexceededexception"></a>QuotaExceededException
[QuotaExceededException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.quotaexceededexception.aspx) 은 특정 엔터티에 대한 할당량이 초과됐음을 나타냅니다.

릴레이의 경우 이 예외는 수신기의 최대수가 이 끝점에 대해 초과되었음을 나타내는 [System.ServiceModel.QuotaExceededException](https://msdn.microsoft.com/library/system.servicemodel.quotaexceededexception.aspx)을 래핑합니다. 이는 예외 메시지의 **MaximumListenersPerEndpoint** 값에 표시됩니다.

## <a name="timeoutexception"></a>TimeoutException
[TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) 은 사용자가 시작한 작업이 작업 시간 제한보다 더 오래 걸린다는 것을 나타냅니다. 

이 제한에 도달하면 [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx)이 발생할 수 있으므로 [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit) 속성 값도 확인해야 합니다.

릴레이의 경우 릴레이 발신자 연결을 처음 열 때 시간 제한 예외가 발생할 수 있습니다. 이 예외의 일반적인 원인은 다음과 같이 두 가지가 있습니다.

1. [OpenTimeout](https://msdn.microsoft.com/library/wcf.opentimeout.aspx) 값이 너무 작을 수 있습니다(1초 미만).
2. 온-프레미스 릴레이 수신기는 응답하지 않을 수 있으며(또는 수신기에서 새 클라이언트 연결을 허용하지 못하도록 방지하는 방화벽 규칙 문제가 발생할 수 있음) [OpenTimeout](https://msdn.microsoft.com/library/wcf.opentimeout.aspx) 값은 20초보다 작습니다.

예:

```
'System.TimeoutException’: The operation did not complete within the allotted timeout of 00:00:10. The time allotted to this operation may have been a portion of a longer timeout.
```

### <a name="common-causes"></a>일반적인 원인
이 오류에 대한 일반적인 두 가지 원인은 잘못된 구성 또는 일시적 서비스 오류입니다.

1. **잘못된 구성**
    작동 조건에 대한 작업 시간 제한이 너무 작을 수도 있습니다. 클라이언트 SDK에서 작업 시간 제한에 대한 기본값은 60초입니다. 코드에 너무 작은 값으로 설정된 값이 있는지 확인합니다. 네트워크 및 CPU 사용량의 조건은 특정 작업을 완료하는 데 걸리는 시간에 영향을 줄 수 있으므로 작업 시간 제한을 너무 작은 값으로 설정하지 않아야 합니다.
2. **일시적 서비스 오류**
    종종 릴레이에서 예를 들어 트래픽이 높은 기간 동안 요청 처리에 지연이 발생할 수 있습니다. 이러한 경우 작업이 성공할 때까지 지연 후 작업을 다시 시도할 수 있습니다. 동일한 작업을 여러 번 시도한 후에도 계속 실패하는 경우 알려진 서비스 중단이 있는지 [Azure 서비스 상태 사이트](https://azure.microsoft.com/status/) 를 방문하세요.

## <a name="next-steps"></a>다음 단계:
* [릴레이 FAQ](relay-faq.md)
* [네임스페이스 만들기](relay-create-namespace-portal.md)
* [.NET 시작](relay-hybrid-connections-dotnet-get-started.md)
* [노드 시작](relay-hybrid-connections-node-get-started.md)




<!--HONumber=Nov16_HO3-->


