---
title: "Service Bus 메시징 샘플 개요 | Microsoft Docs"
description: "Service Bus 메시징 샘플을 분류하고 각각에 대한 링크와 함께 설명합니다."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 0b420343-2d2a-4c65-98f1-ee0e39ef55c8
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/07/2016
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 719403ca2ef90cdcf5f76fe15f7a33095e71a74c


---
# <a name="service-bus-messaging-samples"></a>Service Bus 메시징 샘플
Service Bus 메시징 샘플에서는 [Service Bus 메시징](https://azure.microsoft.com/services/service-bus/)(클라우드 서비스) 및 [Windows Server용 Service Bus](https://msdn.microsoft.com/library/dn282144.aspx)의 주요 기능을 보여 줍니다. 이 문서는 사용할 수 있는 샘플 각각을 링크를 사용하여 범주화하고 설명합니다.

> [!NOTE]
> 서비스 버스 샘플은 SDK로 설치되지 않습니다. 샘플을 얻으려면 [Azure SDK 샘플 페이지](https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=5)를 방문하세요.
> 
> 또한 [여기](https://github.com/Azure-Samples/azure-servicebus-messaging-samples) (작성 일자 기준으로 이 문서에 설명되어 있지 않음)에 업데이트된 서비스 버스 메시징 샘플 집합이 있습니다.  
> 
> 

릴레이 샘플은 [Service Bus Relay 샘플](../service-bus-relay/service-bus-relay-samples.md)을 참조하세요.

## <a name="service-bus-messaging"></a>Service Bus 메시징
다음 예제에서는 Service Bus 메시징을 사용하는 응용 프로그램을 작성하는 방법을 보여줍니다.

메시징 샘플은 Service Bus 네임스페이스에 액세스하기 위해 연결 문자열을 필요로 합니다.

### <a name="to-obtain-a-connection-string-for-azure-service-bus"></a>Azure 서비스 버스에 대한 연결 문자열을 가져오려면
1. [Azure 포털](http://portal.azure.com)에 로그온합니다.
2. 왼쪽 열에서 **서비스 버스**를 클릭합니다.
3. 목록에서 네임스페이스의 이름을 클릭합니다.
4. 네임스페이스 블레이드에서 **공유 액세스 정책**을 클릭합니다.
5. **공유 액세스 정책** 블레이드에서 **RootManageSharedAccessKey**를 클릭합니다.
6. 연결 문자열을 클립보드에 복사합니다.

### <a name="to-obtain-a-connection-string-for-service-bus-for-windows-server"></a>Windows Server용 서비스 버스에 대한 연결 문자열을 가져오려면
1. 다음 PowerShell cmdlet를 실행합니다.
   
    ```
    get-sbClientConfiguration
    ```
2. 이 샘플의 경우 App.config 파일에 연결 문자열을 붙여넣습니다.

### <a name="getting-started-samples"></a>샘플 시작하기
이러한 샘플에서는 기본 메시징 기능을 설명합니다.

| 샘플 이름 | 설명 | 최소 SDK 버전 | Availability |
| --- | --- | --- | --- |
| [시작하기: 큐를 사용하는 메시징](http://code.msdn.microsoft.com/Getting-Started-Brokered-aa7a0ac3) |Microsoft Azure 서비스 버스를 사용하여 큐에서 메시지를 주고받는 방법을 보여줍니다. |1.8 |Microsoft Azure 서비스 버스; Windows Server용 서비스 버스 |
| [시작하기: 토픽을 사용한 메시징](http://code.msdn.microsoft.com/Getting-Started-Brokered-614d42e5) |Microsoft Azure Service Bus를 사용하여 구독이 많은 토픽에서 메시지를 주고받는 방법을 보여줍니다. |1.8 |Microsoft Azure 서비스 버스; Windows Server용 서비스 버스 |

### <a name="exploring-features"></a>기능 살펴보기
다음 샘플에서는 서비스 버스의 다양한 기능을 보여줍니다.

| 샘플 이름 | 설명 | 최소 SDK 버전 | Availability |
| --- | --- | --- | --- |
| [HTTP 토큰 공급자](http://code.msdn.microsoft.com/Service-Bus-HTTP-Token-38f2cfc5) |서비스 버스를 사용하여 HTTP/REST 클라이언트를 인증하는 여러가지 방법을 보여줍니다. |2.1 |Microsoft Azure 서비스 버스; Windows Server용 서비스 버스 |
| [서비스 버스 HTTP 클라이언트](http://code.msdn.microsoft.com/Service-Bus-HTTP-client-fe7da74a) |HTTP/REST를 통해 서비스 버스에서 메시지를 보내고 수신하는 방법을 보여줍니다. |2.3 |Microsoft Azure 서비스 버스; Windows Server용 서비스 버스 |
| [서비스 버스 자동 전달](http://code.msdn.microsoft.com/Service-Bus-Autoforwarding-b9df470b) |큐, 구독 또는 효력 상실 큐에서 자동으로 다른 큐 또는 토픽으로 메시지를 전달하는 방법을 보여줍니다. 또한 전송 큐를 통해 큐 또는 토픽에 메시지를 보내는 방법을 보여줍니다. |2.3 |Microsoft Azure 서비스 버스; Windows Server용 서비스 버스 |
| [조정된 메시징: WCF 채널 세션 샘플](http://code.msdn.microsoft.com/Brokered-Messaging-WCF-0a526451) |Windows Communication Foundation(WCF) 채널을 사용하여 Microsoft Azure 서비스 버스를 사용하는 방법을 보여줍니다. 이 샘플에서는 WCF 채널을 사용하여 서비스 버스 큐를 통해 메시지를 보내고 받는 것을 보여줍니다. 이 샘플에서는 서비스 버스를 통해 세션 및 비 세션 통신을 모두 보여줍니다. |1.8 |Microsoft Azure 서비스 버스; Windows Server용 서비스 버스 |
| [조정된 메시징: 트랜잭션](http://code.msdn.microsoft.com/Brokered-Messaging-8cd41d1e) |원자성으로 커밋된 메시징 작업의 배치를 보장하기 위해 트랜잭션 범위 내에서 Microsoft Azure 서비스 버스 메시징 기능을 사용하는 방법을 보여줍니다. |1.8 |Microsoft Azure 서비스 버스; Windows Server용 서비스 버스 |
| [조정된 메시징: REST를 사용하는 관리 작업](http://code.msdn.microsoft.com/Brokered-Messaging-569cff88) |REST를 사용하여 서비스 버스에 관리 작업을 수행하는 방법을 보여줍니다. |1.8 |Microsoft Azure 서비스 버스; Windows Server용 서비스 버스 |
| [리소스 공급자 REST API](http://code.msdn.microsoft.com/Service-Bus-Resource-5d887203) |네임 스페이스 및 메시징 엔터티를 관리 하기 위해 새 서비스 버스 RDFE REST API를 사용하는 방법을 보여줍니다. |1.8 |Microsoft Azure 서비스 버스; Windows Server용 서비스 버스 |
| [조정된 메시징: WCF 서비스 세션 샘플](http://code.msdn.microsoft.com/Brokered-Messaging-WCF-db4262c2) |WCF 서비스 모델을 사용하여 Microsoft Azure 서비스 버스를 사용하는 방법을 보여줍니다. 이 샘플에서는 WCF 서비스 모델을 사용하여 서비스 버스 큐를 통해 세션 기반 통신을 수행하는 것을 보여줍니다. |1.8 |Microsoft Azure 서비스 버스; Windows Server용 서비스 버스 |
| [조정된 메시징: 요청 응답](http://code.msdn.microsoft.com/Brokered-Messaging-Request-2b4ff5d8) |Microsoft Azure 서비스 버스 및 요청/응답 기능을 사용하는 방법을 보여줍니다. 이 샘플에서는 서비스 버스 큐를 통해 통신하는 단순 클라이언트 및 서버를 보여줍니다. |1.8 |Microsoft Azure 서비스 버스; Windows Server용 서비스 버스 |
| [조정된 메시징: 효력 상실 큐](http://code.msdn.microsoft.com/Brokered-Messaging-Dead-22536dd8) |Microsoft Azure 서비스 버스 및 메시징 "효력 상실 큐" 기능을 사용하는 방법을 보여줍니다. 이 샘플에서는 서비스 버스 큐를 통해 통신하는 단순 발신자 및 수신자를 보여줍니다. |1.8 |Microsoft Azure 서비스 버스; Windows Server용 서비스 버스 |
| [조정된 메시징: 지연된 메시지](http://code.msdn.microsoft.com/Brokered-Messaging-ccc4f879) |Microsoft Azure 서비스 버스의 메시지 지연 기능을 사용하는 방법을 보여줍니다. 이 샘플에서는 서비스 버스 큐를 통해 통신하는 단순 발신자 및 수신자를 보여줍니다. |1.8 |Microsoft Azure 서비스 버스; Windows Server용 서비스 버스 |
| [조정된 메시징: 세션 메시지](http://code.msdn.microsoft.com/Brokered-Messaging-Session-41c43fb4) |Microsoft Azure 서비스 버스 및 메시징 세션 기능을 사용하는 방법을 보여줍니다. 이 샘플에서는 서비스 버스 큐를 통해 통신하는 단순 발신자 및 수신자를 보여줍니다. |1.8 |Microsoft Azure 서비스 버스; Windows Server용 서비스 버스 |
| [조정된 메시징: 요청 응답 토픽](http://code.msdn.microsoft.com/Brokered-Messaging-Request-6759a36e) |Microsoft Azure Service Bus 토픽 및 구독을 사용하여 요청/응답 패턴을 구현하는 방법을 보여줍니다. 이 샘플에서는 Service Bus 토픽을 통해 통신하는 단순 클라이언트 및 서버를 보여줍니다. |1.8 |Microsoft Azure 서비스 버스; Windows Server용 서비스 버스 |
| [조정된 메시징: 요청 응답 큐](http://code.msdn.microsoft.com/Brokered-Messaging-Request-0ce8fcaf) |Microsoft Azure 서비스 버스 및 요청/응답 기능을 사용하는 방법을 보여줍니다. 이 샘플에서는 두 개의 서비스 버스 큐를 통해 통신하는 단순 클라이언트 및 서버를 보여줍니다. |1.8 |Microsoft Azure 서비스 버스; Windows Server용 서비스 버스 |
| [조정된 메시징: 중복 감지](http://code.msdn.microsoft.com/Brokered-Messaging-c0acea25) |큐를 사용하여 Microsoft Azure 서비스 버스 중복 메시지 감지를 사용하는 방법을 보여줍니다. 두 개의 큐 중 하나는 중복 감지를 사용하도록 설정하고, 다른 하나는 중복 감지를 사용하지 않고 만듭니다. |1.8 |Microsoft Azure 서비스 버스; Windows Server용 서비스 버스 |
| [조정된 메시징: 비동기 메시징](http://code.msdn.microsoft.com/Brokered-Messaging-Async-211c1e74) |Microsoft Azure 서비스 버스를 사용하여 큐에서 비동기적으로 메시지를 주고받는 방법을 보여줍니다. 큐는 발신자와 임의의 수신자 간에 분리된 비동기 통신을 제공합니다.(여기서는 단일 수신자) |1.8 |Microsoft Azure 서비스 버스; Windows Server용 서비스 버스 |
| [조정된 메시징: 고급 필터](http://code.msdn.microsoft.com/Brokered-Messaging-6b0d2749) |Microsoft Azure 서비스 버스 게시/구독 고급 필터를 사용하는 방법을 보여줍니다. 다른 필터 정의를 사용하여 토픽 및 3개의 구독을 만들고 메시지를 토픽에 보내며 구독에서 모든 메시지를 받습니다. |1.8 |Microsoft Azure 서비스 버스; Windows Server용 서비스 버스 |
| [조정된 메시징: 메시지 미리 가져오기](http://code.msdn.microsoft.com/Brokered-Messaging-be2dac1d) |Microsoft Azure 서비스 버스 메시지 지연 기능을 사용하는 방법을 보여줍니다. 수신에서 메시지 미리 가져오기 기능을 사용하는 방법을 보여줍니다. |1.8 |Microsoft Azure 서비스 버스; Windows Server용 서비스 버스 |

## <a name="service-bus-reference-tools"></a>서비스 버스 참조 도구
다음 샘플에서는 서비스의 다른 다양한 기능을 보여줍니다.

| 샘플 이름 | 설명 | 최소 SDK 버전 | Availability |
| --- | --- | --- | --- |
| [서비스 버스 탐색기](http://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a) |서비스 버스 탐색기를 사용하면 서비스 버스 서비스 네임스페이스에 연결하고 쉬운 방식으로 메시징 엔터티를 관리할 수 있습니다. 도구는 가져오기/내보내기 기능 등의 고급 기능 및 메시징 엔터티를 테스트하고 서비스를 릴레이하는 기능을 제공합니다. |1.8 |Microsoft Azure 서비스 버스; Windows Server용 서비스 버스 |
| [권한 부여: SBAzTool](http://code.msdn.microsoft.com/Authorization-SBAzTool-6fd76d93) |이 샘플에서는 서비스 버스와 함께 사용하기 위해 Microsoft Azure Active Directory 액세스 제어(액세스 제어 서비스 또는 ACS라고도 함)의 서비스 ID를 만들고 관리하는 방법을 보여줍니다. |해당 없음 |Microsoft Azure 서비스 버스 |

## <a name="next-steps"></a>다음 단계
Service Bus의 개념적 개요는 다음 항목을 참조하세요.

* [서비스 버스 메시징 개요](service-bus-messaging-overview.md)
* [서비스 버스 아키텍처](service-bus-architecture.md)
* [서비스 버스 기본 사항](service-bus-fundamentals-hybrid-solutions.md)




<!--HONumber=Nov16_HO3-->


