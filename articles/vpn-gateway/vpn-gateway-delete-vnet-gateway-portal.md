---
title: "가상 네트워크 게이트웨이 삭제: Azure portal: Resource Manager | Microsoft Docs"
description: "Resource Manager 배포 모델에서 PowerShell을 사용하여 가상 네트워크 게이트웨이를 삭제합니다."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: 
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/29/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: b4949db62ccd31cf6ce3d19df5459367cac99b59
ms.lasthandoff: 03/31/2017


---
# <a name="delete-a-virtual-network-gateway-using-the-portal"></a>포털을 사용하여 가상 네트워크 게이트웨이 삭제
> [!div class="op_single_selector"]
> * [Resource Manager - Azure Portal](vpn-gateway-delete-vnet-gateway-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [클래식 - PowerShell](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
>
>

VPN 게이트웨이 구성에 대한 가상 네트워크 게이트웨이 삭제하려는 경우에 몇 가지 다른 접근 방법을 사용할 수 있습니다.

- 테스트 환경의 경우처럼 모든 항목을 삭제하고 다시 시작하려면 전체 리소스 그룹을 삭제하면 됩니다. 리소스 그룹을 삭제하면 그룹 내의 모든 리소스가 삭제됩니다. 이 방법은 리소스 그룹에 리소스를 유지하지 않으려는 경우에만 권장됩니다. 이 방법을 사용할 때는 몇 가지 리소스만 선택적으로 삭제할 수가 없습니다.

- 리소스 그룹에 일부 리소스를 유지하려는 경우 가상 네트워크 게이트웨이를 삭제하는 작업이 좀 더 복잡해집니다. 가상 네트워크 게이트웨이를 삭제하려면 먼저 게이트웨이에 의존하는 모든 리소스를 삭제해야 합니다. 수행하는 단계는 만든 연결의 유형 및 각 연결에 대한 종속 리소스에 따라 달라집니다.

##<a name="deletegw"></a>VPN Gateway 삭제

가상 네트워크 게이트웨이를 삭제하려면 먼저 가상 네트워크 게이트웨이와 관련된 각 리소스를 삭제해야 합니다. 리소스는 종속성으로 인해 특정 순서로 삭제해야 합니다.

###<a name="step-1-navigate-to-the-virtual-network-gateway"></a>1단계: 가상 네트워크 게이트웨이로 이동

[Azure Portal](https://portal.azure.com)의 **모든 리소스**에서 삭제할 가상 네트워크 게이트웨이를 클릭합니다. 게이트웨이 그래픽:

![가상 네트워크 게이트웨이 찾기](./media/vpn-gateway-delete-vnet-gateway-portal/gw.png)

###<a name="step-2-delete-connections"></a>2단계: 연결 삭제

1. 가상 네트워크 게이트웨이에 대한 블레이드에서 **연결**을 클릭하여 게이트웨이에 대한 모든 연결을 봅니다.
2. 연결 이름 행의 **...**을 클릭하고 드롭다운에서 **삭제**를 선택합니다.
3. **예**를 클릭하여 연결 삭제를 확인합니다. 연결이 여러 개인 경우 각 연결을 삭제합니다.

###<a name="step-3-delete-the-local-network-gateways"></a>3단계: 로컬 네트워크 게이트웨이 삭제
1. **모든 리소스**에서 각 연결에 연결된 로컬 네트워크 게이트웨이를 찾습니다. 로컬 네트워크 게이트웨이 그래픽:

    ![로컬 네트워크 게이트웨이 찾기](./media/vpn-gateway-delete-vnet-gateway-portal/lng.png)
2. 로컬 네트워크 게이트웨이의 **개요** 블레이드에서 **삭제**를 클릭합니다.

###<a name="step-4-delete-the-virtual-network-gateway"></a>4단계: 가상 네트워크 게이트웨이 삭제
1. **모든 리소스**에서 삭제할 가상 네트워크 게이트웨이를 찾습니다.
2. **개요** 블레이드에서 **삭제**를 클릭하여 게이트웨이를 삭제합니다.

>[!NOTE]
> 이 VNet에 S2S 구성 외에 P2S 구성이 있는 경우 가상 네트워크 게이트웨이를 삭제하면 경고 없이 모든 P2S 클라이언트의 연결이 자동으로 끊어집니다.
>
>

###<a name="step-5-delete-the-public-ip-address-for-the-gateway"></a>5단계: 게이트웨이에 대한 공용 IP 주소 삭제

1. **모든 리소스**에서 게이트웨이에 할당된 공용 IP 주소를 찾습니다. 가상 네트워크 게이트웨이가 활성-활성인 경우 두 개의 공용 IP 주소가 표시됩니다. 공용 IP 주소 그래픽:

    ![공용 IP 주소](./media/vpn-gateway-delete-vnet-gateway-portal/pip.png)

2. 공용 IP 주소에 대한 **개요** 페이지에서 **삭제**, **예**를 차례로 클릭하여 확인합니다.

###<a name="step-6-delete-the-gateway-subnet"></a>6단계: 게이트웨이 서브넷 삭제

1. **모든 리소스**에서 가상 네트워크를 찾습니다. 
2. **서브넷** 블레이드에서 **게이트웨이 서브넷**, **삭제**를 차례로 클릭합니다. 
3. **예**를 클릭하여 게이트웨이 서브넷 삭제를 확인합니다.

##<a name="deleterg"></a>리소스 그룹을 삭제하여 VPN 게이트웨이 삭제

리소스 그룹에 리소스를 유지하지 않고 새로 시작하려는 경우 전체 리소스 그룹을 삭제하면 됩니다. 모든 항목을 제거하는 빠른 방법입니다. 다음 단계는 Resource Manager 배포 모델에만 적용됩니다.

1. **모든 리소스**에서 리소스 그룹을 찾고 클릭하여 블레이드를 엽니다.
2. **삭제**를 클릭합니다. 삭제 블레이드에서 영향을 받는 리소스를 봅니다. 해당 리소스를 모두 삭제할 것인지 확인합니다. 그렇지 않은 경우 이 문서의 맨 위에 있는 [VPN Gateway 삭제](#deletegw) 단계를 사용합니다.
3. 계속하려면 삭제할 리소스 그룹의 이름을 입력한 다음 **삭제**를 클릭합니다.
