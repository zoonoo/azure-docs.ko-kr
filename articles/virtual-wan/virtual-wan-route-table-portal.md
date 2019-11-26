---
title: 'Virtual WAN: Create virtual hub route table to NVA: Azure portal'
description: Virtual WAN virtual hub route table to steer traffic to a network virtual appliance using the portal.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to create a route table using the portal.
ms.openlocfilehash: 3aa5660e5b777364ef9d684debe7e06f42acee6e
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2019
ms.locfileid: "74482018"
---
# <a name="create-a-virtual-wan-hub-route-table-for-nvas-azure-portal"></a>Create a Virtual WAN hub route table for NVAs: Azure portal

This article shows you how to steer traffic from a branch (on-premises site) connected to the Virtual WAN hub to a Spoke Vnet via a Network Virtual Appliance (NVA).

![Virtual WAN 다이어그램](./media/virtual-wan-route-table/vwanroute.png)

## <a name="before-you-begin"></a>시작하기 전에

다음 기준을 충족하는지 확인합니다.

*  You have a Network Virtual Appliance (NVA). A Network Virtual Appliance is a third-party software of your choice that is typically provisioned from Azure Marketplace in a virtual network.

    * A private IP address must be assigned to the NVA network interface.

    * The NVA is not deployed in the virtual hub. 별도의 VNet에 배포해야 합니다.

    *  The NVA VNet may have one or many virtual networks connected to it. In this article, we refer to the NVA VNet as an 'indirect spoke VNet'. These VNets can be connected to the NVA VNet by using VNet peering. The Vnet Peering links are depicted by black arrows in the above figure.
*  You have created 2 VNets. They will be used as spoke VNets.

    * For this exercise, the VNet spoke address spaces are: VNet1: 10.0.2.0/24 and VNet2: 10.0.3.0/24. If you need information on how to create a VNet, see [Create a virtual network](../virtual-network/quick-create-portal.md).

    * Ensure there are no virtual network gateways in any of the VNets.
    * For this configuration, these VNets do not require a gateway subnet.

## <a name="signin"></a>1. Sign in

브라우저에서 [Azure 포털](https://portal.azure.com) 로 이동하고 Azure 계정으로 로그인합니다.

## <a name="vwan"></a>2. Create a virtual WAN

가상 WAN 만들기 For the purposes of this exercise, you can use the following values:

* **Virtual WAN name:** myVirtualWAN
* **Resource group:** testRG
* **Location:** West US

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-vwan-include.md)]

## <a name="hub"></a>3. Create a hub

Create the hub. For the purposes of this exercise, you can use the following values:

* **Location:** West US
* **Name:** westushub
* **Hub private address space:** 10.0.1.0/24

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-hub-include.md)]

## <a name="route"></a>4. Create and apply a hub route table

Update the hub with a hub route table. For the purposes of this exercise, you can use the following values:

* **Indirect spoke VNet address spaces:** (VNet1 and VNet2) 10.0.2.0/24 and 10.0.3.0/24
* **DMZ NVA network interface private IP address:** 10.0.4.5

1. Navigate to your virtual WAN.
2. Click the hub for which you want to create a route table.
3. Click the **...** , and then click **Edit virtual hub**.
4. On the **Edit virtual hub** page, scroll down and select the checkbox **Use table for routing**.
5. In the **If destination prefix is** column, add the address spaces. In the **Send to next hop** column, add the DMZ NVA network interface private IP address.
6. Click **Confirm** to update the hub resource with the route table settings.

## <a name="connections"></a>5. Create the VNet connections

Create a connection from each indirect spoke VNet (VNet1 and VNet2) to the hub. Then, create a connection from the NVA VNet to the hub. These Vnet Connections are dipicted by blue arrows in the figure above. 

 For this step, you can use the following values:

| VNet 이름| 연결 이름|
| --- | --- |
| VNet1 | testconnection1 |
| VNet2 | testconnection2 |
| NVAVNet | testconnection3 |

Repeat the following procedure for each VNet that you want to connect.

1. 가상 WAN에 대한 페이지에서 **가상 네트워크 연결**을 클릭합니다.
2. 가상 네트워크 연결 페이지에서 **+연결 추가**를 클릭합니다.
3. **연결 추가** 페이지에서 다음 필드를 채웁니다.

    * **연결 이름** - 연결의 이름을 지정합니다.
    * **허브** - 이 연결과 연결할 허브를 선택합니다.
    * **구독** - 구독을 확인합니다.
    * **가상 네트워크** - 이 허브에 연결할 가상 네트워크를 선택합니다. 가상 네트워크에는 기존의 가상 네트워크 게이트웨이를 사용할 수 없습니다.
4. **확인**을 클릭하여 연결을 만듭니다.

## <a name="next-steps"></a>다음 단계

가상 WAN에 대해 자세히 알아보려면 [가상 WAN 개요](virtual-wan-about.md) 페이지를 참조하세요.
