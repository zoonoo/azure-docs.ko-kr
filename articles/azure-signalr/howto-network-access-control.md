---
title: 네트워크 액세스 제어 구성
titleSuffix: Azure SignalR Service
description: Azure SignalR Service에 대한 네트워크 액세스 제어를 구성합니다.
services: signalr
author: ArchangelSDY
ms.service: signalr
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: dayshen
ms.openlocfilehash: 24e56736e0d033420f9aaf976b0fb7d9727c1a5b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92143224"
---
# <a name="configure-network-access-control"></a>네트워크 액세스 제어 구성

Azure SignalR Service를 사용하면 사용하는 네트워크의 요청 유형 및 하위 집합을 기반으로 서비스 엔드포인트에 대한 액세스 수준을 보호하고 제어할 수 있습니다. 네트워크 규칙이 구성되면 지정된 네트워크 집합을 통해 데이터를 요청하는 애플리케이션만 Azure SignalR Service에 액세스할 수 있습니다.

Azure SignalR Service에는 인터넷을 통해 액세스할 수 있는 퍼블릭 엔드포인트가 있습니다. [에 대한 프라이빗 엔드포인트](howto-private-endpoints.md)를 만들 수도 있습니다. 프라이빗 엔드포인트는 VNet의 개인 IP 주소를 Azure SignalR Service에 할당하고 프라이빗 링크를 통해 VNet과 Azure SignalR Service 간의 모든 트래픽을 보호합니다. Azure SignalR Service 네트워크 액세스 제어는 퍼블릭 엔드포인트와 프라이빗 엔드포인트 둘 다에 대한 액세스 제어를 제공합니다.

필요에 따라 퍼블릭 엔드포인트 및 각 프라이빗 엔드포인트에 대한 특정 유형의 요청을 허용하거나 거부하도록 선택할 수 있습니다. 예를 들어 퍼블릭 엔드포인트에서의 [서버 연결](signalr-concept-internals.md#server-connections)을 모두 차단하고 특정 VNet에서 시작된 연결만 허용할 수 있습니다.

네트워크 액세스 제어 규칙이 적용되는 경우 Azure SignalR Service에 액세스하는 애플리케이션은 여전히 해당 요청에 적절한 권한 부여가 필요합니다.

## <a name="scenario-a---no-public-traffic"></a>시나리오 A - 퍼블릭 트래픽 없음

모든 퍼블릭 트래픽을 완전히 거부하려면 먼저 요청 유형을 허용하지 않도록 퍼블릭 네트워크 규칙을 구성해야 합니다. 그런 다음, 특정 VNet 트래픽에 대한 액세스를 허가하는 규칙을 구성해야 합니다. 이 구성을 사용하면 애플리케이션에 대한 보안 네트워크 경계를 구축할 수 있습니다.

## <a name="scenario-b---only-client-connections-from-public-network"></a>시나리오 B - 퍼블릭 네트워크에서의 클라이언트 연결만 허용

이 시나리오에서는 퍼블릭 네트워크에서의 [클라이언트 연결](signalr-concept-internals.md#client-connections)만 허용하도록 퍼블릭 네트워크 규칙을 구성할 수 있습니다. 그런 다음 특정 VNet에서 시작된 다른 유형의 요청을 허용하도록 프라이빗 네트워크 규칙을 구성할 수 있습니다. 이 구성은 퍼블릭 네트워크에서 앱 서버를 숨기고 앱 서버와 Azure SignalR Service 간 보안 연결을 설정합니다.

## <a name="managing-network-access-control"></a>네트워크 액세스 제어 관리

Azure Portal을 통해 Azure SignalR Service에 대한 네트워크 액세스 제어를 관리할 수 있습니다.

### <a name="azure-portal"></a>Azure portal

1. 보호할 Azure SignalR Service로 이동합니다.

1. **네트워크 액세스 제어** 라는 설정 메뉴를 클릭합니다.

    ![포털의 네트워크 ACL](media/howto-network-access-control/portal.png)

1. 기본 작업을 편집하려면 **허용/거부** 단추를 토글합니다.

    > [!TIP]
    > 기본 작업은 일치하는 ACL 규칙이 없는 경우에 수행하는 작업입니다. 예를 들어 기본 작업이 **거부** 인 경우 아래에서 명시적으로 승인되지 않은 요청 유형은 거부됩니다.

1. 퍼블릭 네트워크 규칙을 편집하려면 **퍼블릭 네트워크** 에서 허용되는 요청 유형을 선택합니다.

    ![포털에서 퍼블릭 네트워크 ACL 편집 ](media/howto-network-access-control/portal-public-network.png)

1. 프라이빗 엔드포인트 네트워크 규칙을 편집하려면 **프라이빗 엔드포인트 연결** 아래의 각 행에서 허용되는 요청 유형을 선택합니다.

    ![포털에서 프라이빗 엔드포인트 ACL 편집 ](media/howto-network-access-control/portal-private-endpoint.png)

1. **저장** 을 클릭하여 변경 내용을 적용합니다.

## <a name="next-steps"></a>다음 단계

[Azure Private Link](../private-link/private-link-overview.md)에 대해 자세히 알아봅니다.