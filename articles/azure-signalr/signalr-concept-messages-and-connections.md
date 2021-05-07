---
title: Azure SignalR Service의 메시지 및 연결
description: Azure SignalR Service의 메시지와 연결에 대한 주요 개념을 설명합니다.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 08/05/2020
ms.author: zhshang
ms.openlocfilehash: 3c4d28addac0ecfc9605678582562550a1c96b8d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103491948"
---
# <a name="messages-and-connections-in-azure-signalr-service"></a>Azure SignalR Service의 메시지 및 연결

Azure SignalR Service의 청구 모델은 연결 수와 메시지 수를 기반으로 합니다. 이 문서에서는 요금을 청구하기 위해 메시지 및 연결을 정의하고 계산하는 방식을 설명합니다.


## <a name="message-formats"></a>메시지 형식 

Azure SignalR Service는 ASP.NET Core SignalR과 동일하게 [JSON](https://www.json.org/)과 [MessagePack](/aspnet/core/signalr/messagepackhubprotocol) 형식을 지원합니다.

## <a name="message-size"></a>메시지 크기

Azure SignalR Service는 메시지 크기에 제한이 없습니다.

큰 메시지는 2KB 이하의 작은 메시지로 분할되어 별도로 전송됩니다. SDK는 메시지 분할 및 어셈블리를 처리합니다. 개발자의 수고가 필요하지 않습니다.

큰 메시지는 메시지 성능에 부정적인 영향을 줍니다. 되도록이면 작은 메시지를 사용하고, 테스트를 통해 각 사용 사례 시나리오에 맞는 최적의 메시지 크기를 확인하세요.

## <a name="how-messages-are-counted-for-billing"></a>요금을 청구할 메시지를 계산하는 방법

Azure SignalR Service에서 나가는 아웃바운드 메시지만 청구 대상에 포함됩니다. 클라이언트와 서버 간의 ping 메시지는 무시됩니다.

2KB를 초과하는 메시지는 2KB 크기의 메시지 여러 개로 계산됩니다. Azure Portal의 메시지 수 차트는 허브당 메시지 100개마다 업데이트됩니다.

예를 들어 하나의 애플리케이션 서버와 세 개의 클라이언트가 있다고 가정해 봅시다.

앱 서버는 모든 연결된 클라이언트에 1KB 메시지를 브로드캐스트합니다. 앱 서버에서 서비스로 보내는 메시지는 무료 인바운드 메시지로 간주됩니다. 서비스에서 각 클라이언트로 보내는 세 개의 메시지만 아웃바운드 메시지로 청구됩니다.

클라이언트 A는 앱 서버를 거치지 않고 1KB 메시지를 다른 클라이언트 B로 보냅니다. 클라이언트 A에서 서비스로 보내는 메시지는 무료 인바운드 메시지입니다. 서비스에서 클라이언트 B로 보내는 메시지는 아웃바운드 메시지로 청구됩니다.

세 개의 클라이언트와 하나의 애플리케이션 서버가 있는 경우, 한 클라이언트는 서버에서 모든 클라이언트에 브로드캐스트할 수 있는 4KB 메시지를 보냅니다. 청구되는 메시지 수는 8개입니다. 메시지 1개는 서비스에서 애플리케이션 서버로 전송되고 3개는 서버에서 클라이언트로 전송됩니다. 각 메시지는 2KB 메시지 2개로 계산됩니다.

## <a name="how-connections-are-counted"></a>연결 수를 계산하는 방법

Azure SignalR Service에는 서버 연결 및 클라이언트 연결이 있습니다. 기본적으로 각 애플리케이션 서버에는 허브당 5개의 연결로 시작하고 각 클라이언트에는 1개의 클라이언트 연결이 있습니다.

예를 들어 고객에게 2개의 애플리케이션 서버가 있고 코드로 허브 5개를 정의한다고 가정해 봅시다. 앱 서버 2개 * 허브 5개 * 허브당 연결 5개로, 서버 연결 수는 50개입니다.

Azure Portal에 표시되는 연결 수에는 서버 연결, 클라이언트 연결, 진단 연결, 라이브 추적 연결이 포함됩니다. 연결 형식은 아래 목록에 정의되어 있습니다.

- **서버 연결**: Azure SignalR Service와 앱 서버를 연결합니다.
- **클라이언트 연결**: Azure SignalR Service와 클라이언트 앱을 연결합니다.
- **진단 연결**: 더 자세한 로그를 생성할 수 있는 특별한 유형의 클라이언트 연결로, 성능에 영향을 줄 수 있습니다. 해당 유형의 클라이언트는 문제 해결을 위해 설계되었습니다.
- **라이브 추적 연결**: 라이브 추적 엔드포인트에 연결하고 Azure SignalR Service의 라이브 추적을 수신합니다. 
 
라이브 추적 연결은 클라이언트 연결이나 서버 연결로 계산되지 않습니다. 

ASP.NET SignalR은 다른 방법으로 서버 연결 수를 계산합니다. 고객이 정의하는 허브 외에도 기본 허브 하나가 포함됩니다. 기본적으로 각 애플리케이션 서버에는 초기 서버 연결 5개가 더 필요합니다. 기본 허브의 초기 연결 수는 다른 허브와 동일하게 유지됩니다.

서비스와 애플리케이션 서버는 연결 상태를 계속 동기화하고 서버 연결을 조정하여 더 나은 성능 및 서비스 안정성을 확보합니다.  따라서 서버 연결 수가 시간에서 변경되는 것을 볼 수 있습니다.

## <a name="how-inboundoutbound-traffic-is-counted"></a>인바운드/아웃바운드 트래픽을 계산하는 방법

서비스로 전송된 메시지는 인바운드 메시지입니다. 서비스에서 전송된 메시지는 아웃바운드 메시지입니다. 트래픽은 바이트 단위로 계산됩니다.

## <a name="related-resources"></a>관련 참고 자료

- [Azure Monitor의 집계 유형](../azure-monitor/essentials/metrics-supported.md#microsoftsignalrservicesignalr )
- [ASP.NET Core SignalR 구성](/aspnet/core/signalr/configuration)
- [JSON](https://www.json.org/)
- [MessagePack](/aspnet/core/signalr/messagepackhubprotocol)
