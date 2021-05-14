---
title: Azure Bastion에서 VM 및 NSGs 사용하기
description: Azure Bastion으로 네트워크 보안 그룹을 사용할 수 있습니다. 이 구성에 필요한 서브넷에 대하여 알아봅니다.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 12/09/2020
ms.author: cherylmc
ms.openlocfilehash: b6a0dee4c3fef1be4f4b9f910b4c6256b4924a2d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101700221"
---
# <a name="working-with-nsg-access-and-azure-bastion"></a>NSG 액세스 및 Azure Bastion을 통하여 작업하기

Azure Bastion을 통하여 작업을 하면, NSGs(네트워크 보안 그룹)을 사용할 수 있습니다. 자세한 내용은 [보안 그룹](../virtual-network/network-security-groups-overview.md)을 참조하세요.

:::image type="content" source="./media/bastion-nsg/figure-1.png" alt-text="NSG":::

이 다이어그램에서

* 베스천 호스트는 가상 네트워크로 배포됩니다.
* 사용자는 HTML5 브라우저를 사용하여 Azure Portal에 연결합니다.
* 사용자가 RDP/SSH를 위하여 Azure 가상 머신으로 이동합니다.
* 브라우저 내에 통합 - 단일 클릭 RDP/SSH 세션을 연결합니다.
* Azure VM에 공용 IP가 필요하지 않습니다.

## <a name="network-security-groups"></a><a name="nsg"></a>네트워크 보안 그룹

본 섹션은 사용자와 Azure Bastion 간의 네트워크 트래픽을 보여 주며 가상 네트워크 내의 대상 VM을 통과하는 네트워크 트래픽을 보여 줍니다.

> [!IMPORTANT]
> Azure Bastion 리소스를 가지고 NSG를 사용할 경우, **반드시** 다음의 송수신 트래픽 규칙을 모두 만들어야 합니다. 다음 규칙 가운데 NSG에서 빠진 것이 하나라도 있으면 이후에 Azure Bastion 리소스가 필요한 업데이트를 받을 수 없게 차단되어 리소스가 이후의 보안 취약점에 노출됩니다.
> 

### <a name="azurebastionsubnet"></a><a name="apply"></a>AzureBastionSubnet

Azure Bastion은 특히 ***AzureBastionSubnet*** 에 배포됩니다.

* **수신 트래픽:**

   * **퍼블릭 인터넷의 수신 트래픽:** Azure Bastion은 수신 트래픽에 대한 공용 IP에서 443 포트를 사용하도록 설정하여야 하는 공용 IP를 만듭니다. AzureBastionSubnet에서는 3389/22 포트를 열 필요가 없습니다.
   * **Azure Bastion 컨트롤 플레인의 수신 트래픽:** 컨트롤 플레인 연결의 경우 **GatewayManager** 서비스 태그에서 443 포트 인바운드를 사용하도록 설정합니다. 이를 통하여 게이트웨이 관리자인 컨트롤 플레인이 Azure Bastion과 통신할 수 있게 됩니다.
   * **Azure Bastion 데이터 플레인으로부터의 수신 트래픽:** Azure Bastion의 기본 구성 요소 간의 데이터 플레인의 경우, **VirtualNetwork** 서비스 태그로부터의 포트 8080, 5701 인바운드를 **VirtualNetwork** 서비스 태그로 사용하도록 설정합니다. Azure Bastion의 구성 요소끼리 통신할 수 있게 됩니다.
   * **Azure Load Balancer로부터의 수신 트래픽:** 상태 프로브의 경우, **AzureLoadBalancer** 서비스 태그로부터의 포트 443 인바운드를 사용하도록 설정합니다. Azure Load Balancer의 연결을 검색할 수 있게 됩니다.


   :::image type="content" source="./media/bastion-nsg/inbound.png" alt-text="Azure Bastion 연결 관련 인바운드 보안 규칙을 보여주는 스크린샷입니다.":::

* **송신 트래픽:**

   * **대상 VM으로의 송신 트래픽:** Azure Bastion은 개인 IP를 통하여 대상 VM에 도달합니다. NSGs는 포트 3389와 포트 22의 다른 대상 VM 서브넷으로 향하는 송신 트래픽을 허용하여야 합니다.
   * **Azure Bastion 데이터 플레인으로 가는 송신 트래픽:** Azure Bastion의 기본 구성 요소 간의 데이터 플레인의 경우, **VirtualNetwork** 서비스 태그로부터의 포트 8080, 5701 아웃바운드를 **VirtualNetwork** 서비스 태그로 사용하도록 설정합니다. Azure Bastion의 구성 요소끼리 통신할 수 있게 됩니다.
   * **Azure의 다른 퍼블릭 엔드포인트에 대한 송신 트래픽:** Azure Bastion은 Azure 내의 다양한 퍼블릭 엔드포인트(예: 진단 로그 및 측광 로그 저장용)에 연결할 수 있어야 합니다. 따라서 Azure Bastion에는 **AzureCloud** 서비스 태그로 아웃바운드 443이 필요합니다.
   * **인터넷으로 가는 송신 트래픽:** Azure Bastion이 세션 및 인증서 유효성 검사를 위하여 인터넷과 통신할 수 있어야 합니다. 따라서 **인터넷** 으로 가는 포트 80 아웃바운드를 사용하도록 설정하는 것이 좋습니다.


   :::image type="content" source="./media/bastion-nsg/outbound.png" alt-text="Azure Bastion 연결 관련 아웃바운드 보안 규칙을 보여주는 스크린샷입니다.":::

### <a name="target-vm-subnet"></a>대상 VM 서브넷
RDP/SSH를 원하는 대상 가상 머신이 들어 있는 서브넷입니다.

   * **Azure Bastion으로부터의 수신 트래픽:** Azure Bastion은 개인 IP를 통하여 대상 VM에 도달합니다. RDP/SSH 포트(각각 포트 3389와 포트 22)는 개인 IP를 통하여 대상 VM 측에서 열려야 합니다. 모범 사례로 이러한 규칙에 Azure Bastion Subnet IP 주소 범위를 추가하여 대상 VM의 이들 포트를 대상 VM 서브넷에서 열 수 있습니다.


## <a name="next-steps"></a>다음 단계

Azure Bastion 관련 자세한 내용은 [FAQ](bastion-faq.md)를 참조하세요.
