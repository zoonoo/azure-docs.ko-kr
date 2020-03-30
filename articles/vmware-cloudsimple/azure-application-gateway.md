---
title: VMware 가상 컴퓨터와 Azure 응용 프로그램 게이트웨이 사용
description: Azure 응용 프로그램 게이트웨이를 사용하여 VMware 가상 머신에서 실행되는 웹 서버의 들어오는 웹 트래픽을 관리하는 방법에 대해 설명합니다.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2cbfdd358fdfd5403c677c067376142169cdc6bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77015459"
---
# <a name="use-azure-application-gateway-with-vmware-virtual-machines-in-the-cloudsimple-private-cloud-environment"></a>CloudSimple 프라이빗 클라우드 환경에서 VMware 가상 머신과 함께 Azure 응용 프로그램 게이트웨이 사용

Azure 응용 프로그램 게이트웨이를 사용하여 CloudSimple 프라이빗 클라우드 환경 내에서 VMware 가상 컴퓨터에서 실행되는 웹 서버의 들어오는 웹 트래픽을 관리할 수 있습니다.

공용-개인 하이브리드 배포에서 Azure 응용 프로그램 게이트웨이를 활용하여 응용 프로그램에 대한 웹 트래픽을 관리하고, 안전한 프런트 엔드를 제공하고, VMware 환경에서 실행되는 서비스에 대한 SSL 처리를 오프로드할 수 있습니다. Azure Application Gateway는 구성된 규칙 및 상태 프로브에 따라 VMware 환경에 있는 백 엔드 풀 인스턴스에 들어오는 웹 트래픽을 라우팅합니다.

이 Azure 응용 프로그램 게이트웨이 솔루션에는 다음을 수행해야 합니다.

* Azure 구독이 있어야 합니다.
* Azure 가상 네트워크 및 가상 네트워크 내의 서브넷을 만들고 구성합니다.
* NSG 규칙을 만들고 구성하고 ExpressRoute를 사용하여 vNet을 클라우드심플 프라이빗 클라우드로 피어.
* 프라이빗 클라우드를 구성하기 & 만듭니다.
* azure 응용 프로그램 게이트웨이를 구성하기 & 만듭니다.

## <a name="azure-application-gateway-deployment-scenario"></a>Azure 응용 프로그램 게이트웨이 배포 시나리오

이 시나리오에서는 Azure 응용 프로그램 게이트웨이 Azure 가상 네트워크에서 실행 됩니다. 가상 네트워크는 ExpressRoute 회로를 통해 프라이빗 클라우드에 연결됩니다. 프라이빗 클라우드의 모든 서브넷은 가상 네트워크 서브넷에서 IP에 연결할 수 있습니다.

![Azure 가상 네트워크의 Azure 로드 밸러터](media/load-balancer-use-case.png)

## <a name="how-to-deploy-the-solution"></a>솔루션을 배포하는 방법

배포 프로세스는 다음과 같은 작업으로 구성됩니다.

1. [필수 구성 조건이 충족되는지 확인](#1-verify-prerequisites)
2. [Azure 가상 연결을 프라이빗 클라우드에 연결](#2-connect-your-azure-virtual-network-to-your-private-cloud)
3. [Azure 응용 프로그램 게이트웨이 배포](#3-deploy-an-azure-application-gateway)
4. [프라이빗 클라우드에서 웹 서버 VM 풀 생성 및 구성](#4-create-and-configure-a-web-server-vm-pool-in-your-private-cloud)

## <a name="1-verify-prerequisites"></a>1. 전제 조건 확인

이러한 필수 구성 조건이 충족되는지 확인합니다.

* Azure 리소스 관리자와 가상 네트워크가 이미 만들어졌습니다.
* Azure 가상 네트워크 내의 전용 서브넷(응용 프로그램 게이트웨이용)이 이미 만들어졌습니다.
* 클라우드심플 프라이빗 클라우드가 이미 생성되었습니다.
* 가상 네트워크의 IP 서브넷과 프라이빗 클라우드의 서브넷 간에는 IP 충돌이 없습니다.

## <a name="2-connect-your-azure-virtual-network-to-your-private-cloud"></a>2. Azure 가상 네트워크를 프라이빗 클라우드에 연결

Azure 가상 네트워크를 프라이빗 클라우드에 연결하려면 이 프로세스를 따르십시오.

1. [CloudSimple 포털에서 ExpressRoute 피어링 정보를 복사합니다.](virtual-network-connection.md)

2. [Azure 가상 네트워크에 대한 가상 네트워크 게이트웨이를 구성합니다.](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)

3. [가상 네트워크를 CloudSimple 익스프레스루트 회로에 연결합니다.](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#connect-a-vnet-to-a-circuit---different-subscription)

4. [복사한 피어링 정보를 사용하여 가상 네트워크를 ExpressRoute 회로에 연결합니다.](virtual-network-connection.md)

## <a name="3-deploy-an-azure-application-gateway"></a>3. Azure 응용 프로그램 게이트웨이 배포

이에 대한 자세한 지침은 [Azure 포털을 사용하여 경로 기반 라우팅 규칙을 사용하는 응용 프로그램 게이트웨이 만들기에서](../application-gateway/create-url-route-portal.md)사용할 수 있습니다. 다음은 필요한 단계에 대한 요약입니다.

1. 구독 및 리소스 그룹에서 가상 네트워크를 만듭니다.
2. 가상 네트워크 내에서 전용 서브넷으로 사용할 서브넷을 만듭니다.
3. 표준 응용 프로그램 게이트웨이 만들기(선택적으로 WAF 사용): Azure 포털 홈페이지에서 페이지 왼쪽 상단에서 **리소스** > **네트워킹** > **응용 프로그램 게이트웨이를** 클릭합니다. 표준 SKU 및 크기를 선택하고 Azure 구독, 리소스 그룹 및 위치 정보를 제공합니다. 필요한 경우 이 응용 프로그램 게이트웨이에 대한 새 공용 IP를 만들고 가상 네트워크 및 응용 프로그램 게이트웨이전용 서브넷에 대한 세부 정보를 제공합니다.
4. 가상 컴퓨터가 있는 백 엔드 풀을 추가하고 응용 프로그램 게이트웨이에 추가합니다.

## <a name="4-create-and-configure-a-web-server-vm-pool-in-your-private-cloud"></a>4. 프라이빗 클라우드에서 웹 서버 VM 풀 생성 및 구성

vCenter에서 선택한 OS 및 웹 서버(예: Windows/IIS 또는 Linux/아파치)로 VM을 만듭니다. 프라이빗 클라우드의 웹 계층에 대해 지정된 서브넷/VLAN을 선택합니다. 웹 서버 VM의 vNIC가 웹 계층 서브넷에 하나 이상의 vNIC에 있는지 확인합니다.
