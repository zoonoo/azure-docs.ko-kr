---
title: "Azure Service Bus Relay 샘플 개요 | Microsoft Docs"
description: "Service Bus Relay 샘플을 분류하고 각각에 대한 링크와 함께 설명합니다."
services: service-bus-relay
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 9237a9a2-f126-4d3f-9f9b-604ee6b32153
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/13/2017
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 6027c973f0104fff9705a70f7812f62562165c1e
ms.openlocfilehash: d830770c49caeacf762382ce1c6113d9196a5994
ms.lasthandoff: 02/14/2017


---
# <a name="service-bus-relay-samples"></a>Service Bus Relay 샘플
Service Bus Relay 샘플은 [Service Bus Relay](https://azure.microsoft.com/services/service-bus/)의 주요 기능을 보여 줍니다. 이 문서는 사용할 수 있는 샘플 각각을 링크를 사용하여 범주화하고 설명합니다.

> [!NOTE]
> Service Bus 샘플은 Azure SDK를 사용하여 설치되지 않습니다. 샘플을 얻으려면 [Azure SDK 샘플 페이지](https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=5)를 방문하세요.
> 
> 또한 업데이트된 일련의 Service Bus Relay 샘플이 [여기](https://github.com/Azure-Samples/azure-servicebus-relay-samples)에 있습니다.  
> 
> 

Service Bus 메시지 샘플은 [Service Bus 메시지 샘플](../service-bus-messaging/service-bus-samples.md)을 참조하세요.

## <a name="azure-service-bus-relay"></a>Azure 서비스 버스 릴레이
다음 샘플에서는 Azure Relay 서비스를 사용하는 응용 프로그램을 작성하는 방법을 보여줍니다.

Relay 네임스페이스에 액세스하기 위해 Relay 샘플에는 연결 문자열이 필요합니다.

### <a name="to-obtain-a-connection-string-for-azure-relay"></a>Azure Relay에 대한 연결 문자열을 가져오려면
1. [Azure 포털](http://portal.azure.com)에 로그온합니다.
2. **모든 리소스** 창에서 네임스페이스의 목록을 확장하도록 클릭합니다.
3. 목록에서 Relay 네임스페이스의 이름을 클릭합니다.
4. 네임스페이스 블레이드에서 **공유 액세스 정책**을 클릭합니다.
5. **공유 액세스 정책** 블레이드에서 **RootManageSharedAccessKey**를 클릭합니다.
6. 연결 문자열을 클립보드에 복사합니다.

### <a name="to-obtain-a-connection-string-for-service-bus-for-windows-server"></a>Windows Server용 서비스 버스에 대한 연결 문자열을 가져오려면
1. 다음 PowerShell cmdlet를 실행합니다.
   
    ```powershell
    get-sbClientConfiguration
    ```
2. 이 샘플의 경우 App.config 파일에 연결 문자열을 붙여넣습니다.

## <a name="azure-relay"></a>Azure Relay
Azure Relay를 보여 주는 샘플입니다.

### <a name="getting-started"></a>시작
| 샘플 이름 | 설명 | 최소 SDK 버전 | Availability |
| --- | --- | --- | --- |
| [WCF 릴레이된 메시징: Azure](http://code.msdn.microsoft.com/Relayed-Messaging-Windows-0d2cede3) |Azure에서 서비스 버스 클라이언트 및 서비스를 실행하는 방법을 보여줍니다. 이 샘플에서는 서비스 버스를 프로그래밍 방식으로 구성합니다. 환경 및 보안 정보만 구성 파일에 저장됩니다. |1.8 |Microsoft Azure 서비스 버스 |
| [WCF 릴레이된 메시징 인증: 공유 암호](http://code.msdn.microsoft.com/Relayed-Messaging-92b04c02) |발급자 이름 및 발급자 암호를 사용하여 서비스 버스로 인증하는 방법을 보여줍니다. |1.8 |Microsoft Azure 서비스 버스 |
| [WCF 릴레이된 메시징 인증: WebNoAuth](http://code.msdn.microsoft.com/Relayed-Messaging-a4f0b831) |클라이언트 사용자 인증이 필요하지 않은 HTTP 서비스를 노출하는 방법을 보여줍니다. |1.8 |Microsoft Azure 서비스 버스 |
| [WCF 릴레이된 메시징 바인딩: WebHttp](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-a6477ba0) |웹 프로그래밍 모델을 사용하여 이진 데이터를 반환하기 위해 **WebHttpRelayBinding** 바인딩을 사용하는 방법을 보여줍니다. |1.8 |Microsoft Azure 서비스 버스 |
| [WCF 릴레이된 메시징 바인딩: 릴레이된 NetTcp](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-2dec7692) |**NetTcpRelayBinding** 바인딩을 사용하는 방법을 보여줍니다. |1.8 |Microsoft Azure 서비스 버스 |

### <a name="exploring-features"></a>기능 살펴보기
다양한 서비스 버스 릴레이 기능을 보여주는 샘플입니다.

| 샘플 이름 | 설명 | 최소 SDK 버전 | Availability |
| --- | --- | --- | --- |
| [WCF 릴레이된 메시징 인증: 간단한 WebToken](http://code.msdn.microsoft.com/Relayed-Messaging-32c74392) |간단한 웹 토큰 자격 증명을 사용하여 서비스 버스로 인증하는 방법을 보여줍니다. 샘플은 약간 변경된 Echo 샘플과 유사합니다. 특히 이 샘플은 ServiceHost(서비스) 및 ChannelFactory(클라이언트) 응용 프로그램에서 동작을 추가합니다. |1.8 |Microsoft Azure 서비스 버스 |
| [WCF 릴레이된 메시징: 부하 분산](http://code.msdn.microsoft.com/Relayed-Messaging-Load-bd76a9f8) |Microsoft Azure 서비스 버스를 사용하여 다양한 수신자에게 메지시를 라우팅하는 방법을 보여줍니다. **NetTcpRelayBinding** 바인딩을 통해 클라이언트와 통신하는 간단한 서비스의 여러 인스턴스를 보여줍니다. |1.8 |Microsoft Azure 서비스 버스 |
| [WCF 릴레이된 메시징 바인딩: Net 이벤트](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-c0176977) |Microsoft Azure 서비스 버스에서 **NetEventRelayBinding** 바인딩을 사용하여 보여줍니다. |1.8 |Microsoft Azure 서비스 버스 |
| [WCF 릴레이된 메시징 바인딩: WS2007Http 세션](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-ef1f1fcb) |사용하도록 설정된 신뢰할 수 있는 세션으로 **WS2007HttpRelayBinding** 바인딩을 사용하여 보여줍니다. 또한 프로그래밍 방식 대신 구성 파일에서 서비스 버스 자격 증명을 지정하는 방법을 보여줍니다. |1.8 |Microsoft Azure 서비스 버스 |
| [WCF 릴레이된 메시징 바인딩: WS2007Http MsgSecCertificate](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-f29c9da5) |메시지 보안으로 **WS2007HttpRelayBinding** 바인딩을 사용하여 여전히 서비스 버스를 사용하여 클라이언트에게 인증을 요구하는 동안 종단간 메시지를 확보하는 방법을 보여줍니다. |1.8 |Microsoft Azure 서비스 버스 |
| [WCF 릴레이된 메시징: 메타데이터 교환](http://code.msdn.microsoft.com/Relayed-Messaging-Metadata-f122312e) |릴레이 바인딩을 사용하는 메타데이터 끝점을 노출하는 방법을 보여줍니다. 다음 릴레이 바인딩에서 **MetadataExchange**를 지원합니다. **NetTcpRelayBinding**, **NetOnewayRelayBinding**, **BasicHttpRelayBinding** 및 **WS2007HttpRelayBinding**입니다. |1.8 |Microsoft Azure 서비스 버스 |
| [WCF 릴레이된 메시징 바인딩: NetTcp 직접](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-ca039161) |**NetTcpRelayBinding** 바인딩을 구성하여 먼저 릴레이된 연결을 설정하고 가능한 경우 클라이언트와 서비스 간을 직접 연결로 자동으로 전환하는 **하이브리드** 연결 모드를 지원하는 방법을 보여줍니다. |1.8 |Microsoft Azure 서비스 버스 |
| [WCF 릴레이된 메시징 바인딩: NetTcp MsgSec 사용자 이름](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-30542392) |메시지 보안으로 **NetTcpRelayBinding** 바인딩을 사용하는 방법을 보여줍니다. |1.8 |Microsoft Azure 서비스 버스 |
| [WCF 릴레이된 메시징 바인딩: Net 일방 통행](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-bb5b813a) |**NetOnewayRelayBinding** 바인딩을 사용하여 서비스 끝점을 노출하고 사용하는 방법을 보여줍니다. |1.8 |Microsoft Azure 서비스 버스 |
| [WCF 릴레이된 메시징 바인딩: WS2007Http 간단한](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-aa4b793a) |**WS2007HttpRelayBinding** 바인딩을 사용하여 보여줍니다. 보안 옵션을 사용하고 클라이언트를 인증할 필요가 없는 간단한 서비스를 보여줍니다. |1.8 |Microsoft Azure 서비스 버스 |

## <a name="next-steps"></a>다음 단계
Service Bus의 개념적 개요는 다음 항목을 참조하세요.

* [Azure Relay 개요](relay-what-is-it.md)
* [서비스 버스 아키텍처](../service-bus-messaging/service-bus-architecture.md)
* [서비스 버스 기본 사항](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md)


