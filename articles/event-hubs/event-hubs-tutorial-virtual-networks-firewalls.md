---
title: 자습서 - Event Hubs에서 Virtual Networks 통합 및 방화벽 사용 | Microsoft Docs
description: 이 자습서에서는 안전하게 액세스할 수 있도록 가상 네트워크 및 방화벽을 사용하여 Event Hubs를 통합하는 방법을 알아봅니다.
services: event-hubs
author: axisc
manager: darosa
ms.author: aschhab
ms.date: 11/28/2018
ms.topic: tutorial
ms.service: event-hubs-messaging
ms.custom: mvc
ms.openlocfilehash: 9eea40a8ad2f08099b2662a0e7539c326d4d143e
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/12/2019
ms.locfileid: "57779048"
---
# <a name="tutorial-enable-virtual-networks-integration-and-firewalls-on-event-hubs-namespace"></a>자습서: Event Hubs 네임스페이스에서 Virtual Networks 통합 및 방화벽 사용

[VNet(Virtual Network) 서비스 엔드포인트](../virtual-network/virtual-network-service-endpoints-overview.md)는 직접 연결을 통해 가상 네트워크 개인 주소 공간 및 Azure 서비스에 대한 VNet의 ID를 확장합니다. 엔드포인트를 사용하면 가상 네트워크에 대해 중요한 Azure 서비스 리소스를 보호할 수 있습니다. VNet에서 Azure 서비스에 대한 트래픽은 Microsoft Azure 백본 네트워크에 항상 유지됩니다.

방화벽을 사용하면 특정 IP 주소(또는 IP 주소 범위)에서 Event Hubs 네임 스페이스에 대한 액세스를 제한할 수 있습니다.

이 자습서에서는 포털을 사용하여 기존 Azure Event Hubs 네임스페이스와 Virtual Networks 서비스 엔드포인트를 통합하고 방화벽(IP 필터링)을 설정하는 방법을 보여줍니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.
> [!div class="checklist"]
> * Event Hubs 네임스페이스와 Virtual Networks 서비스 엔드포인트를 통합하는 방법
> * Event Hubs 네임스페이스로 방화벽(IP 필터링)을 설정하는 방법

>[!WARNING]
> Virtual Networks 통합을 구현하면 다른 Azure 서비스가 Event Hubs와 상호 작용하지 않도록 방지할 수 있습니다.
>
> Virtual Networks를 사용하도록 설정되면 첫 번째 파티 통합이 지원되지 않습니다.
> Virtual Networks를 사용하지 않는 일반적인 Azure 시나리오 -
> * Azure 진단 및 로깅
> * Azure Stream Analytics
> * Event Grid 통합
> * Web Apps 및 Functions는 가상 네트워크에 위치해야 합니다.
> * IoT Hub 경로
> * IoT Device Explorer


> [!IMPORTANT]
> 가상 네트워크는 Event Hubs의 **표준** 및 **전용** 계층에서 지원되며 기본 계층에서는 지원되지 않습니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정][]을 만듭니다.

## <a name="prerequisites"></a>필수 조건

기존 Event Hubs 네임 스페이스를 활용하므로 Event Hubs 네임스페이스를 사용할 수 있는지 확인하세요. 그렇지 않으면 [이 자습서](./event-hubs-create.md)를 참조하세요.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

먼저 [Azure Portal][Azure portal]로 이동하고 Azure 구독을 사용하여 로그인합니다.

## <a name="select-event-hubs-namespace"></a>Event Hubs 네임스페이스 선택

이 자습서에서는 Event Hubs 네임스페이스를 만들고 해당 항목으로 이동합니다.

## <a name="navigate-to-firewalls-and-virtual-networks-experience"></a>방화벽 및 Virtual Network 환경으로 이동

포털의 왼쪽 창에서 탐색 메뉴를 사용하여 **'방화벽 및 Virtual Network'** 옵션을 선택합니다.

  ![메뉴로 이동](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-landing-page.png)

  처음으로 이 페이지를 방문한 경우 **모든 네트워크** 라디오 단추를 선택해야 합니다. 즉, 이 Event Hubs 네임스페이스는 들어오는 모든 연결을 허용합니다.

## <a name="add-virtual-network-service-endpoint"></a>Virtual Network 서비스 엔드포인트 추가

액세스를 제한하려면 이 Event Hubs 네임스페이스에 대한 Virtual Network 서비스 엔드포인트를 통합해야 합니다.

1. 페이지 맨 위에 있는 **선택한 네트워크** 라디오 단추를 클릭하여 메뉴 옵션이 있는 페이지의 나머지 부분을 사용하도록 설정합니다.
  ![선택한 네트워크](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-selecting-selected-networks.png)
2. 페이지의 가상 네트워크 섹션에서 ***+ 기존 가상 네트워크 추가*** 옵션을 선택합니다. 그러면 이미 생성된 가상 네트워크를 선택할 수 있는 창에 밀어넣습니다.
  ![기존 가상 네트워크 추가](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-vnet-from-portal-slide-in-pane.png)
3. 목록에서 가상 네트워크를 선택하고 서브넷을 선택합니다.
   ![서브넷 선택](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-vnet-from-portal-slide-in-pane-with-subnet-query.png)
4. 가상 네트워크 목록에 추가하기 전에 서비스 엔드포인트를 사용하도록 설정해야 합니다. 서비스 엔드포인트를 사용하지 않는 경우 포털에서는 설정하라는 메시지가 나타납니다.
  ![서브넷 선택 및 엔드포인트 설정](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-vnet-from-portal-slide-in-pane-after-enabling.png)
    > [!NOTE]
    > 서비스 엔드포인트를 사용하도록 설정할 수 없는 경우 ARM 템플릿을 사용하여 누락된 가상 네트워크 서비스 엔드포인트를 무시할 수 있습니다. 이 기능은 포털에서 사용할 수 없습니다.

5. 선택한 서브넷에서 서비스 엔드포인트를 사용하도록 설정한 후에 허용된 가상 네트워크 목록에 추가하도록 진행할 수 있습니다.
  ![엔드포인트를 설정한 후에 서브넷 추가](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-vnet-from-portal-slide-in-pane-after-adding.png)

6. 위쪽 리본에서 **저장** 단추를 계속 눌러서 서비스에서 가상 네트워크 구성을 저장합니다. 포털 알림이 표시되는지 확인하기 위해 잠시 기다리세요.

## <a name="add-firewall-for-specified-ip"></a>지정된 IP에 대한 방화벽 추가

방화벽 규칙을 사용하여 제한된 범위의 IP 주소 또는 특정 IP 주소에서 Event Hubs 네임스페이스에 대한 액세스를 제한할 수 있습니다.

1. 페이지 맨 위에 있는 **선택한 네트워크** 라디오 단추를 클릭하여 메뉴 옵션이 있는 페이지의 나머지 부분을 사용하도록 설정합니다.
  ![선택한 네트워크](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-selecting-selected-networks.png)
2. **방화벽** 섹션의 ***주소 범위*** 그리드에서 하나 또는 여러 개의 특정 IP 주소 또는 IP 주소 범위를 추가할 수 있습니다.
  ![IP 주소 추가](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-firewall.png)
3. 여러 IP 주소(또는 IP 주소 범위)를 추가하면 위쪽 리본에서 **저장**을 눌러서 서비스 쪽에서 구성이 저장되는지 확인합니다. 포털 알림이 표시되는지 확인하기 위해 잠시 기다리세요.
  ![IP 주소 추가 및 저장 누르기](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-firewall-hitting-save.png)

## <a name="adding-your-current-ip-address-to-the-firewall-rules"></a>방화벽 규칙에 현재 IP 주소 추가

1. ***주소 범위*** 그리드의 바로 위에 있는 ***클라이언트 IP 주소(현재 IP 주소) 추가*** 확인란을 검사하여 현재 IP 주소를 신속하게 추가할 수도 있습니다.
  ![현재 IP 주소 추가](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-current-ip-hitting-save.png)
2. 현재 IP 주소를 방화벽 규칙에 추가하면 위쪽 리본에서 **저장**을 눌러서 서비스 쪽에서 구성이 저장되는지 확인합니다. 포털 알림이 표시되는지 확인하기 위해 잠시 기다리세요.
  ![현재 IP 주소 추가 및 저장 누르기](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-current-ip-hitting-save-after-saving.png)

## <a name="conclusion"></a>결론

이 자습서에서는 기존 Event Hubs 네임스페이스와 Virtual Network 엔드포인트 및 방화벽 규칙을 통합했습니다. 다음 방법을 알아보았습니다.
> [!div class="checklist"]
> * Event Hubs 네임스페이스와 Virtual Networks 서비스 엔드포인트를 통합하는 방법
> * Event Hubs 네임스페이스로 방화벽(IP 필터링)을 설정하는 방법


[Azure portal]: https://portal.azure.com/
