---
title: VMware 가상 머신과 Azure 애플리케이션 게이트웨이 사용
description: Azure application gateway를 사용 하 여 VMware virtual machines에서 실행 되는 웹 서버에 대 한 들어오는 웹 트래픽을 관리 하는 방법에 대해 설명 합니다. CloudSimple 사설 클라우드 환경
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: b7dce0181987b7e61b243a7eb0e13b7ed687eb08
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "82185696"
---
# <a name="use-azure-application-gateway-with-vmware-virtual-machines-in-the-cloudsimple-private-cloud-environment"></a>CloudSimple 사설 클라우드 환경에서 VMware 가상 머신과 Azure 애플리케이션 게이트웨이 사용

Azure 애플리케이션 게이트웨이를 사용 하 여 CloudSimple 사설 클라우드 환경 내의 VMware 가상 머신에서 실행 되는 웹 서버에 대 한 들어오는 웹 트래픽을 관리할 수 있습니다.

공용-개인 하이브리드 배포에서 Azure 애플리케이션 게이트웨이를 활용 하 여 응용 프로그램에 대 한 웹 트래픽을 관리 하 고, 보안 프런트 엔드를 제공 하 고, VMware 환경에서 실행 되는 서비스에 대 한 TLS 처리를 오프 로드할 수 있습니다. Azure 애플리케이션 게이트웨이는 들어오는 웹 트래픽을 구성 된 규칙 및 상태 프로브에 따라 VMware 환경에 상주 하는 백 엔드 풀 인스턴스로 라우팅합니다.

이 Azure 애플리케이션 게이트웨이 솔루션을 사용 하려면 다음을 수행 해야 합니다.

* Azure 구독이 있어야 합니다.
* Azure 가상 네트워크와 가상 네트워크 내에서 서브넷을 만들고 구성 합니다.
* CloudSimple 사설 클라우드에 대해 Express 경로를 사용 하 여 NSG 규칙을 만들고 구성 하 고 vNet을 피어 링 합니다.
* 사설 클라우드를 만들기 & 구성 합니다.
* Azure 애플리케이션 게이트웨이를 만들고 & 구성 합니다.

## <a name="azure-application-gateway-deployment-scenario"></a>Azure 애플리케이션 게이트웨이 배포 시나리오

이 시나리오에서 Azure 애플리케이션 게이트웨이는 Azure 가상 네트워크에서 실행 됩니다. 가상 네트워크는 Express 경로 회로를 통해 사설 클라우드에 연결 됩니다. 사설 클라우드의 모든 서브넷은 가상 네트워크 서브넷에서 IP에 연결할 수 있습니다.

![Azure virtual network의 azure 부하 분산 장치](media/load-balancer-use-case.png)

## <a name="how-to-deploy-the-solution"></a>솔루션을 배포 하는 방법

배포 프로세스는 다음과 같은 작업으로 구성 됩니다.

1. [필수 구성 요소가 충족 되는지 확인](#1-verify-prerequisites)
2. [사설 클라우드에 Azure 가상 연결 연결](#2-connect-your-azure-virtual-network-to-your-private-cloud)
3. [Azure application gateway 배포](#3-deploy-an-azure-application-gateway)
4. [사설 클라우드에서 웹 서버 VM 풀 만들기 및 구성](#4-create-and-configure-a-web-server-vm-pool-in-your-private-cloud)

## <a name="1-verify-prerequisites"></a>1. 필수 구성 요소 확인

이러한 필수 구성 요소가 충족 되는지 확인 합니다.

* Azure Resource Manager 및 가상 네트워크가 이미 생성 되었습니다.
* Azure virtual network 내에서 전용 서브넷 (Application Gateway의 경우)이 이미 만들어졌습니다.
* CloudSimple 사설 클라우드가 이미 만들어졌습니다.
* 가상 네트워크의 IP 서브넷과 사설 클라우드의 서브넷 간에는 IP 충돌이 없습니다.

## <a name="2-connect-your-azure-virtual-network-to-your-private-cloud"></a>2. Azure virtual network를 사설 클라우드에 연결

Azure virtual network를 사설 클라우드에 연결 하려면이 프로세스를 수행 합니다.

1. [CloudSimple 포털에서 express 경로 피어 링 정보를 복사](virtual-network-connection.md)합니다.

2. [Azure virtual network에 대 한 가상 네트워크 게이트웨이를 구성](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)합니다.

3. [가상 네트워크를 CloudSimple express 경로 회로에 연결](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#connect-a-vnet-to-a-circuit---different-subscription)합니다.

4. [가상 네트워크를 express 경로 회로에 연결 하기 위해 복사한 피어 링 정보를 사용](virtual-network-connection.md)합니다.

## <a name="3-deploy-an-azure-application-gateway"></a>3. Azure application gateway 배포

이에 대 한 자세한 지침은 [Azure Portal를 사용 하 여 경로 기반 라우팅 규칙을 사용 하는 응용 프로그램 게이트웨이 만들기](../application-gateway/create-url-route-portal.md)에서 사용할 수 있습니다. 다음은 필요한 단계에 대 한 요약입니다.

1. 구독 및 리소스 그룹에 가상 네트워크를 만듭니다.
2. 가상 네트워크 내에서 전용 서브넷으로 사용할 서브넷을 만듭니다.
3. 표준 Application Gateway 만들기 (선택적으로 waf를 사용 하도록 설정): Azure Portal 홈 **Resource**  >  페이지에서 페이지 왼쪽 상단에 있는 리소스**네트워킹**  >  **Application Gateway** 을 클릭 합니다. 표준 SKU 및 크기를 선택 하 고 Azure 구독, 리소스 그룹 및 위치 정보를 제공 합니다. 필요한 경우이 응용 프로그램 게이트웨이에 대 한 새 공용 IP를 만들고 응용 프로그램 게이트웨이에 대 한 가상 네트워크 및 전용 서브넷에 대 한 세부 정보를 제공 합니다.
4. 가상 컴퓨터를 사용 하 여 백 엔드 풀을 추가 하 고 응용 프로그램 게이트웨이에 추가 합니다.

## <a name="4-create-and-configure-a-web-server-vm-pool-in-your-private-cloud"></a>4. 사설 클라우드에서 웹 서버 VM 풀 만들기 및 구성

VCenter에서 원하는 OS와 웹 서버 (예: Windows/IIS 또는 Linux/Apache)를 사용 하 여 Vm을 만듭니다. 사설 클라우드의 웹 계층에 대해 지정 된 서브넷/v l a n을 선택 합니다. 웹 서버 Vm의 vNIC 하나 이상이 웹 계층 서브넷에 있는지 확인 합니다.
