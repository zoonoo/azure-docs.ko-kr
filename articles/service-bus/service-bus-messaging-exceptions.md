<properties 
   pageTitle="서비스 버스 메시징 예외 | Microsoft Azure"
   description="서비스 버스 메시징 예외 및 제안 조치의 목록입니다."
   services="service-bus"
   documentationCenter="na"
   authors="sethmanheim"
   manager="timlt"
   editor="tysonn" />
<tags 
   ms.service="service-bus"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/25/2016"
   ms.author="sethm" />

# 서비스 버스 메시징 예외

이 문서는 Microsoft Azure 서비스 버스 메시징 API를 통해 발생하는 몇 가지 예외를 설명합니다. 이 참조는 변경될 수 있으므로 나중에 다시 업데이트를 확인하세요.

## 예외 범주

메시징 API는 다음 범주에 해당하는 예외와, 해당 예외의 해결을 위해 수행할 수 있는 관련 조치를 함께 생성합니다.

1.  사용자 코딩 오류([System.ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System.InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System.OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [System.Runtime.Serialization.SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx)). 일반 조치: 코드를 수정한 후 계속합니다.

2.  설치/구성 오류 ([Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingentitynotfoundexception.aspx), [System.UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). 일반 조치: 구성을 검토하고 필요한 경우 변경합니다.

3.  일시적 예외 ([Microsoft.ServiceBus.Messaging.MessagingException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingexception.aspx), [Microsoft.ServiceBus.Messaging.ServerBusyException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.serverbusyexception.aspx), [Microsoft.ServiceBus.Messaging.MessagingCommunicationException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingcommunicationexception.aspx)). 일반 조치: 작업을 다시 시도하거나 사용자에게 알립니다.

4.  기타 예외 ([System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System.TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx), [Microsoft.ServiceBus.Messaging.MessageLockLostException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagelocklostexception.aspx), [Microsoft.ServiceBus.Messaging.SessionLockLostException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sessionlocklostexception.aspx)). 일반 작업: 예외 형식에 특정됩니다. 다음 섹션의 테이블을 참조하세요.

## 예외 유형

다음 표에서는 메시징 예외 유형과 원인, 사용자가 수행할 수 있는 제안 조치 참고를 열거합니다.

| **예외 유형** | **설명/원인/예** | **권장 조치** | **자동/즉시 다시 시도 참고** |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------|
| [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) | 서버가 [OperationTimeout](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout.aspx)에서 제어하는 지정된 시간 안에 요청된 작업에 응답하지 않았습니다. 서버가 요청된 작업을 완료했을 수도 있습니다. 이 예외는 네트워크 또는 기타 인프라 지연으로 발생합니다. | 필요한 경우 시스템 상태에서 일관성을 확인하고 다시 시도합니다. | 일부 경우 다시 시도하면 문제가 해결될 수 있습니다. 코드에 재시도 논리를 추가합니다. |
| [InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) | 요청한 사용자 작업이 서버 또는 서비스 내에서 허용되지 않습니다. 자세한 내용은 예외 메시지를 참조하세요. 예를 들어 **ReceiveAndDelete** 모드에서 메시지를 받은 경우 [Complete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx)가 이 예외를 생성합니다. | 코드 및 설명서를 확인합니다. 요청된 작업이 유효한지 확인합니다. | 재시도로 해결되지 않습니다. |
| [OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) | 이미 종료, 중단 또는 삭제된 개체에서 작업을 호출하려 시도합니다. 드문 경우지만 앰비언트 트랜잭션이 이미 삭제되었을 수 있습니다. | 코드를 확인하고 삭제된 개체에 대해 작업을 호출하지 않는지 확인합니다. | 재시도로 해결되지 않습니다. |
| [UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) | [TokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx) 개체가 토큰을 확보할 수 없거나, 토큰이 유효하지 않거나, 토큰에 작업 수행을 위해 필요한 클레임이 없습니다. | 올바른 값을 사용하여 토큰 공급자를 만드는지 확인합니다. ACS(액세스 제어 서비스) 구성을 확인합니다. | 일부 경우 다시 시도하면 문제가 해결될 수 있습니다. 코드에 재시도 논리를 추가합니다. |
| [ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [ArgumentNullException](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[ArgumentOutOfRangeException](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) | 메서드에 제공된 하나 이상의 인수가 잘못되었습니다. [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) 또는 [Create](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.create.aspx)에 제공된 URI가 패스 세그먼트를 포함합니다. [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) 또는 [Create](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.create.aspx)에 제공된 URI 체계가 잘못되었습니다. 속성 값이 32KB보다 큽니다. | 호출 코드를 확인하고 인수가 정확한지 확인합니다. | 재시도로 해결되지 않습니다. |
| [MessagingEntityNotFoundException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingentitynotfoundexception.aspx) | 작업과 연결된 엔터티가 없거나 삭제되었습니다. | 엔터티가 있는지 확인합니다. | 재시도로 해결되지 않습니다. |
| [MessageNotFoundException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagenotfoundexception.aspx) | 특정 시퀀스 번호를 통해 메시지 수신을 시도합니다. 이 메시지가 없습니다. | 메시지를 아직 받지 못했는지 확인합니다. 전달 실패 큐에서 메시지가 전달되지 않았는지 확인합니다. | 재시도로 해결되지 않습니다. |
| [MessagingCommunicationException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingcommunicationexception.aspx) | 클라이언트가 서비스 버스에 연결할 수 없습니다. | 제공된 호스트 이름이 정확하며 호스트에 연결할 수 있는지 확인합니다. | 간헐적인 연결 문제라면 재시도로 문제를 해결할 수 있습니다. |
| [ServerBusyException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.serverbusyexception.aspx) | 서비스가 지금은 요청을 처리할 수 없습니다. | 클라이언트가 잠시 대기한 후 작업을 다시 시도할 수 있습니다. | 클라이언트가 일정 시간 이후에 다시 시도할 수 있습니다. 재시도에서 다른 예외가 발생한 경우 해당 예외의 재시도 작동을 확인합니다. |
| [MessageLockLostException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagelocklostexception.aspx) | 메시지에 연결된 잠금 토큰이 만료되었거나 잠금 토큰이 없습니다. | 메시지를 제거합니다. | 재시도로 해결되지 않습니다. |
| [SessionLockLostException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sessionlocklostexception.aspx) | 이 세션에 연결된 잠금이 손실되었습니다. | [MessageSession](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesession.aspx) 개체를 중단합니다. | 재시도로 해결되지 않습니다. |
| [MessagingException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingexception.aspx) | 다음 상황에 발생할 수 있는 일반 메시징 예외: 다른 엔터티 형식에 속하는(예: topic) 이름이나 경로를 사용하여[QueueClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx)를 만들려고 시도합니다. 256KB보다 큰 메시지를 전송하려고 시도합니다. 서버 또는 서비스가 요청을 처리하는 동안 오류가 발생했습니다. 자세한 내용은 예외 메시지를 참조하세요. 보통은 일시적인 예외입니다. | 코드를 확인하고 메시지 본문에서 직렬화 가능 개체(또는 사용자 지정 직렬 변환기 사용)만 사용하는지 확인합니다. 설명서에서 지원되는 속성 값 유형을 확인하고 지원되는 유형만 사용합니다. [IsTransient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingexception.istransient.aspx) 속성을 확인합니다. 이 값이 **true**이면 작업을 다시 시도할 수 있습니다. | 재시도 동작이 정의되지 않았으면 도움이 되지 않습니다. |
| [MessagingEntityAlreadyExistsException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingentityalreadyexistsexception.aspx) | 해당 서비스 네임스페이스에서 이미 다른 엔터티가 사용하는 이름으로 엔터티를 만들려고 합니다. | 기존 엔터티를 삭제하거나 만들려는 엔터티에 다른 이름을 선택합니다. | 재시도로 해결되지 않습니다. |
| [QuotaExceededException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.quotaexceededexception.aspx) | 메시징 엔터티의 최대 허용 크기에 도달했습니다. | 엔터티나 하위 큐에서 메시지를 수신하여 엔터티에 공간을 만듭니다. | 그 사이 메시지가 제거되었으면 재시가 도움이 될 수 있습니다. |
| [RuleActionException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.ruleactionexception.aspx) | 잘못된 규칙 작업을 만들려고 시도하면 서비스 버스가 이 예외를 반환합니다. 메시지에 대한 규칙 작업 처리 중에 오류가 발생하면 서비스 버스가 이 예외를 전달 실패 메시지에 연결합니다. | 규칙 작업이 정확한지 확인합니다. | 재시도로 해결되지 않습니다. |
| [FilterException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.filterexception.aspx) | 잘못된 필터를 만들려고 시도하면 서비스 버스가 이 예외를 반환합니다. 메시지에 대한 필터 처리 중에 오류가 발생하면 서비스 버스가 이 예외를 전달 실패 메시지에 연결합니다. | 필터가 정확한지 확인합니다. | 재시도로 해결되지 않습니다. |
| [SessionCannotBeLockedException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sessioncannotbelockedexception.aspx) | 특정 세션 ID를 갖는 세션을 수락하려 하나 해당 세션이 현재 다른 클라이언트에 잠겨 있습니다. | 해당 세션이 다른 클라이언트에서 잠금 해제되었는지 확인합니다. | 그 사이에 세션이 릴리스된 경우 다시 시도하면 문제가 해결될 수 있습니다. |
| [TransactionSizeExceededException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.transactionsizeexceededexception_methods.aspx) | 트랜잭션에 너무 많은 작업이 포함되어 있습니다. | 이 트랜잭션에 포함된 작업 수를 줄입니다. | 재시도로 해결되지 않습니다. |
| [MessagingEntityDisabledException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingentitydisabledexception.aspx) | 비활성화된 엔터티의 런타임 작업에 대한 요청입니다. | 엔터티를 활성화합니다. | 그 사이에 엔터티가 활성화된 경우 다시 시도하면 문제가 해결될 수 있습니다. |
| [NoMatchingSubscriptionException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.nomatchingsubscriptionexception.aspx) | 사전 필터링이 활성화되었으며 일치하는 필터가 없는 항목에 메시지를 보내는 경우 서비스 버스가 이 예외를 반환합니다. | 하나 이상의 필터가 일치하는지 확인합니다. | 재시도로 해결되지 않습니다. |
| [MessageSizeExceededException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesizeexceededexception.aspx) | 메시지 페이로드가 256K 제한을 초과합니다. 256K 제한은 총 메시지 크기입니다. 여기에는 시스템 속성과 모든 .NET 오버헤드가 포함됩니다. | 메시지 페이로드의 크기를 줄인 다음 작업을 다시 시도합니다. | 재시도로 해결되지 않습니다. |
| [TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx) | 앰비언트 트랜잭션(*Transaction.Current*)이 올바르지 않습니다. 완료되었거나 중단되었을 수 있습니다. 내부 예외가 추가 정보를 제공할 수 있습니다. | | 재시도는 도움이 되지 않습니다. | - | [TransactionInDoubtException](https://msdn.microsoft.com/library/system.transactions.transactionindoubtexception.aspx) | 의심스러운 트랜잭션에 대해 작업을 시도하거나, 트랜잭션을 커밋하려고 시도하고 트랜잭션이 의심스러운 상태가 됩니다. | 트랜잭션이 이미 커밋되었을 때 응용 프로그램은 이 예외(특별한 경우)를 처리해야 합니다. | - |

## 다음 단계

전체 서비스 버스 및 이벤트 허브 .NET API 참조는 [MSDN의 Azure 참조](https://msdn.microsoft.com/library/azure/mt419900.aspx)에서 확인하세요.

[서비스 버스](https://azure.microsoft.com/services/service-bus/)에 대한 자세한 내용은 다음 항목을 참조하세요.

- [서비스 버스 메시징 개요](service-bus-messaging-overview.md)
- [서비스 버스 기본 사항](service-bus-fundamentals-hybrid-solutions.md)
- [서비스 버스 아키텍처](service-bus-architecture.md)

<!---HONumber=AcomDC_0330_2016-->