---
title: 네트워크 액세스 제어 구성
titleSuffix: Azure SignalR Service
description: Azure SignalR 서비스에 대 한 네트워크 액세스 제어를 구성 합니다.
services: signalr
author: ArchangelSDY
ms.service: signalr
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: dayshen
ms.openlocfilehash: 72532029b2d9258dba7dea82bb5c5fc8b2673300
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91536225"
---
# <a name="configure-network-access-control"></a>네트워크 액세스 제어 구성

Azure SignalR 서비스를 사용 하면 사용 하는 네트워크의 요청 유형 및 하위 집합을 기반으로 서비스 끝점에 대 한 액세스 수준을 보호 하 고 제어할 수 있습니다. 네트워크 규칙이 구성 된 경우 지정 된 네트워크 집합을 통해 데이터를 요청 하는 응용 프로그램만 Azure SignalR 서비스에 액세스할 수 있습니다.

Azure SignalR Service에는 인터넷을 통해 액세스할 수 있는 공용 끝점이 있습니다. [Azure SignalR 서비스에 대 한 개인 끝점](howto-private-endpoints.md)을 만들 수도 있습니다. 개인 끝점은 VNet의 개인 IP 주소를 Azure SignalR 서비스에 할당 하 고 개인 링크를 통해 VNet과 Azure SignalR 서비스 간의 모든 트래픽을 보호 합니다. Azure SignalR Service network access control은 공용 끝점과 개인 끝점 모두에 대 한 액세스 제어를 제공 합니다.

필요에 따라 공용 끝점 및 각 개인 끝점에 대 한 특정 형식의 요청을 허용 하거나 거부 하도록 선택할 수 있습니다. 예를 들어 공용 끝점에서 모든 [서버 연결](signalr-concept-internals.md#server-connections) 을 차단 하 고 특정 VNet 에서만 생성 되도록 할 수 있습니다.

네트워크 액세스 제어 규칙이 적용 되는 경우 Azure SignalR 서비스에 액세스 하는 응용 프로그램은 여전히 해당 요청에 대 한 적절 한 권한 부여가 필요 합니다.

## <a name="scenario-a---no-public-traffic"></a>시나리오 A-공용 트래픽 없음

모든 공용 트래픽을 완전히 거부 하려면 먼저 요청 유형을 허용 하지 않도록 공용 네트워크 규칙을 구성 해야 합니다. 그런 다음, 특정 VNet 트래픽에 대한 액세스를 허가하는 규칙을 구성해야 합니다. 이 구성을 사용하면 애플리케이션에 대한 보안 네트워크 경계를 구축할 수 있습니다.

## <a name="scenario-b---only-client-connections-from-public-network"></a>시나리오 B-공용 네트워크의 클라이언트 연결만

이 시나리오에서는 공용 네트워크에서 [클라이언트 연결만](signalr-concept-internals.md#client-connections) 허용 하도록 공용 네트워크 규칙을 구성할 수 있습니다. 그런 다음 특정 VNet에서 시작 하는 다른 유형의 요청을 허용 하도록 개인 네트워크 규칙을 구성할 수 있습니다. 이 구성은 공용 네트워크에서 앱 서버를 숨기고 앱 서버와 Azure SignalR 서비스 간에 보안 연결을 설정 합니다.

## <a name="managing-network-access-control"></a>네트워크 액세스 제어 관리

Azure Portal를 통해 Azure SignalR Service에 대 한 네트워크 액세스 제어를 관리할 수 있습니다.

### <a name="azure-portal"></a>Azure portal

1. 보안을 유지 하려는 Azure SignalR 서비스로 이동 합니다.

1. **네트워크 액세스 제어**라고 하는 설정 메뉴를 클릭 합니다.

    ![포털의 네트워크 ACL](media/howto-network-access-control/portal.png)

1. 기본 작업을 편집 하려면 **허용/거부** 단추를 설정/해제 합니다.

    > [!TIP]
    > 기본 작업은 ACL 규칙에 일치 하는 항목이 없을 때 수행 하는 동작입니다. 예를 들어 기본 작업이 **거부**인 경우 아래에서 명시적으로 승인 되지 않은 요청 유형은 거부 됩니다.

1. 공용 네트워크 규칙을 편집 하려면 **공용 네트워크**에서 허용 되는 요청 유형을 선택 합니다.

    ![포털에서 공용 네트워크 ACL 편집 ](media/howto-network-access-control/portal-public-network.png)

1. 개인 끝점 네트워크 규칙을 편집 하려면 **개인 끝점 연결**아래의 각 행에서 허용 되는 요청 유형을 선택 합니다.

    ![포털에서 개인 끝점 ACL 편집 ](media/howto-network-access-control/portal-private-endpoint.png)

1. **저장**을 클릭하여 변경 내용을 적용합니다.

## <a name="next-steps"></a>다음 단계

[Azure Private Link](/azure/private-link/private-link-overview)에 대해 자세히 알아봅니다.
