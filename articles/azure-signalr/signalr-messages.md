---
title: Azure SignalR의 메시지 및 연결
description: Azure SignalR Service의 메시지와 연결에 대한 주요 개념의 개요입니다.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.date: 09/13/2018
ms.author: zhshang
ms.openlocfilehash: 5a0430e9ad124319147342c49fc51e11472ac8ff
ms.sourcegitcommit: 9f87a992c77bf8e3927486f8d7d1ca46aa13e849
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/28/2018
ms.locfileid: "53812830"
---
# <a name="message-and-connection-in-azure-signalr-service"></a>Azure SignalR Service의 메시지와 연결

Azure SignalR Service에는 연결 수와 메시지 수에 기반한 청구 모델이 있습니다. 청구를 위해 메시지와 연결이 정의되고 계산되는 방식은 아래에 설명되어 있습니다.

## <a name="message-formats-supported"></a>지원되는 메시지 형식

Azure SignalR Service는 ASP.NET Core SignalR이 지원하는 형식과 동일한 형식을 지원합니다. [JSON](https://www.json.org/) 및 [MessagePack](/aspnet/core/signalr/messagepackhubprotocol)

## <a name="message-size"></a>메시지 크기

Azure SignalR Service는 메시지 크기에 제한이 없습니다.

실제로 큰 메시지는 2KB 이하의 작은 메시지로 분할되어 별도의 메시지로 전송됩니다. 메시지 분할 및 어셈블리는 SDK에서 처리됩니다. 개발자의 수고가 필요하지 않습니다.

하지만 큰 메시지는 메시징 성능에 부정적인 영향을 줍니다. 가능하면 작은 메시지를 사용하고 테스트를 통해 사용 사례 시나리오마다 최적의 메시지 크기를 선택하십시오.

## <a name="how-to-count-messages-for-billing-purpose"></a>청구 목적으로 메시지를 계산하는 방법

SignalR Service의 아웃바운드 메시지만 계산되고 클라이언트와 서버 간의 ping 메시지는 무시됩니다.

2KB를 초과하는 메시지는 각각 2KB의 여러 메시지로 계산됩니다. Azure Portal의 메시지 수 차트는 허브당 100개마다 업데이트됩니다.

예를 들어, 사용자에게 클라이언트 3개와 애플리케이션 서버 1개가 있습니다. 하나의 클라이언트가 4KB 메시지를 보내서 서버가 모든 클라이언트에 브로드캐스트할 수 있도록 합니다. 이 경우 메시지 수는 8이 됩니다. 서비스에서 애플리케이션 서버로 보내는 메시지 1, 서비스에서 클라이언트로 보내는 메시지가 3, 각 메시지는 2KB 메시지 2개로 계산됩니다.

Azure Portal에 표시되는 메시지 수는 100을 초과하도록 누적될 때까지 0입니다.

## <a name="how-to-count-connections"></a>연결을 계산하는 방법

연결에는 서버 연결과 클라이언트 연결이 있습니다. 기본적으로 각 애플리케이션 서버에는 SignalR Service를 사용하는 허브당 5개의 연결이 있고 각 클라이언트에는 SignalR Service를 사용하는 클라이언트 연결이 1개 있습니다.

Azure Portal에 표시되는 연결 수에는 서버 연결과 클라이언트 연결이 모두 포함됩니다.

예를 들어 한 명의 사용자에게는 2개의 애플리케이션 서버가 있고 코드로 5개의 허브를 정의합니다. Azure Portal에 표시되는 서버 연결 수는 앱 서버 2개 * 허브 5개 * 허브당 연결 5개 = 서버 연결 50개가 됩니다.

## <a name="related-resources"></a>관련 리소스

- [ASP.NET Core SignalR 구성](/aspnet/core/signalr/configuration)
- [JSON](https://www.json.org/)
- [MessagePack](/aspnet/core/signalr/messagepackhubprotocol)