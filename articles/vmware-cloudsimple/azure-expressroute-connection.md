---
title: 클라우드간편으로 Azure VMware 솔루션 - ExpressRoute를 사용하여 프라이빗 클라우드를 Azure 네트워크에 연결
description: ExpressRoute를 사용하여 CloudSimple 프라이빗 클라우드 환경을 Azure 가상 네트워크에 연결하는 방법에 대해 설명합니다.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 9bb68ec68f4de646239477ceeaac50a7a33989fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77015238"
---
# <a name="connect-your-cloudsimple-private-cloud-environment-to-the-azure-virtual-network-using-expressroute"></a>ExpressRoute를 사용하여 클라우드단순 프라이빗 클라우드 환경을 Azure 가상 네트워크에 연결

CloudSimple 프라이빗 클라우드는 Azure ExpressRoute를 사용하여 Azure 가상 네트워크에 연결할 수 있습니다.  이 높은 대역폭, 낮은 대기 시간 연결을 사용하면 프라이빗 클라우드 환경에서 Azure 구독에서 실행 중인 서비스에 액세스할 수 있습니다.

가상 네트워크 연결을 사용하면 다음을 수행할 수 있습니다.

* Azure를 프라이빗 클라우드의 가상 시스템에 대한 백업 대상으로 사용합니다.
* Azure 구독에 KMS 서버를 배포하여 프라이빗 클라우드 vSAN 데이터 스토어를 암호화합니다.
* 응용 프로그램 및 데이터베이스 계층이 프라이빗 클라우드에서 실행되는 동안 응용 프로그램의 웹 계층이 퍼블릭 클라우드에서 실행되는 하이브리드 응용 프로그램을 사용합니다.

![Azure 익스프레스라우팅 가상 네트워크에 연결](media/cloudsimple-azure-network-connection.png)

## <a name="set-up-a-virtual-network-connection"></a>가상 네트워크 연결 설정

프라이빗 클라우드에 대한 가상 네트워크 연결을 설정하려면 권한 부여 키, 피어 회로 URI 및 Azure 구독에 대한 액세스가 필요합니다. 이 정보는 CloudSimple 포털의 가상 네트워크 연결 페이지에서 확인할 수 있습니다. 지침은 Azure [가상 네트워크에 대한 피어링 정보 얻기에서 CloudSimple 연결을](virtual-network-connection.md)참조하십시오. 정보를 얻는 데 문제가 있는 경우 <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">지원 요청을</a>제출합니다.

> [!TIP]
> Azure 가상 네트워크, 게이트웨이 서브넷 및 가상 네트워크 게이트웨이가 이미 있는 경우 4단계로 건너뛸 수 있습니다.

1. Azure 구독에서 가상 네트워크를 만들고 선택한 주소 공간이 프라이빗 클라우드의 주소 공간과 다른지 확인합니다.  Azure 가상 네트워크가 이미 있는 경우 기존 가상 네트워크를 사용할 수 있습니다.  자세한 내용은 [Azure 포털을 사용하여 가상 네트워크 만들기를](../virtual-network/quick-create-portal.md)참조하십시오.
2. Azure 가상 네트워크에서 게이트웨이 서브넷을 만듭니다.  Azure 가상 네트워크에 게이트웨이 서브넷이 이미 있는 경우 기존 서브넷을 사용할 수 있습니다. 자세한 내용은 [게이트웨이 서브넷 만들기를](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-gateway-subnet)참조하십시오.
3. 가상 네트워크에서 가상 네트워크 게이트웨이를 만듭니다.  기존 가상 네트워크 게이트웨이가 있는 경우 기존 게이트웨이를 사용할 수 있습니다. 자세한 내용은 [가상 네트워크 게이트웨이 만들기를](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-virtual-network-gateway)참조하십시오.
4. 다른 [구독 - 회로에 가상 네트워크 연결에](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#connect-a-vnet-to-a-circuit---different-subscription)설명 된 대로 권한 부여 키를 사용하여 가상 네트워크와 프라이빗 클라우드 사이의 연결을 만듭니다.

> [!WARNING]
> 기존 가상 네트워크 게이트웨이를 사용하고 있고 CloudSimple ExpressRoute 회로와 동일한 위치에 ExpressRoute 연결이 있는 경우 연결이 설정되지 않습니다.  새 가상 네트워크를 만들고 이전 단계를 따릅니다.

## <a name="test-the-virtual-network-connection"></a>가상 네트워크 연결 테스트

연결을 만든 후 **설정**에서 **속성을** 선택하여 연결 상태를 확인할 수 있습니다.  상태 및 프로비저닝 상태가 **성공으로**표시되어야 합니다.

![연결 상태](media/azure-expressroute-connection.png)

가상 네트워크 연결을 테스트하려면 다음을 수행하십시오.

1. Azure 구독에서 가상 컴퓨터를 만듭니다.
2. 프라이빗 클라우드 vCenter의 IP 주소를 찾습니다(환영 이메일 참조).
3. Azure 가상 네트워크에서 만든 가상 컴퓨터에서 클라우드 vCenter Ping
4. 프라이빗 클라우드 vCenter에서 실행되는 가상 컴퓨터에서 Azure 가상 컴퓨터를 Ping합니다.

연결을 설정하는 데 문제가 있는 경우 <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">지원 요청을</a>제출합니다.
