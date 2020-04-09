---
title: Azure 익스프레스루트를 오라클 클라우드 인프라와 연결 | 마이크로 소프트 문서
description: Azure ExpressRoute를 오라클 클라우드 인프라(OCI) FastConnect와 연결하여 크로스 클라우드 오라클 애플리케이션 솔루션을 지원
documentationcenter: virtual-machines
author: romitgirdhar
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/16/2020
ms.author: rogirdh
ms.openlocfilehash: cd0b8a50d25cd8d1a66a8eb98e54ec231aa2c62f
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878717"
---
# <a name="set-up-a-direct-interconnection-between-azure-and-oracle-cloud-infrastructure"></a>Azure와 Oracle 클라우드 인프라 간의 직접 상호 연결 설정  

[통합 멀티 클라우드 환경을](oracle-oci-overview.md)만들기 위해 Microsoft와 Oracle은 [ExpressRoute](../../../expressroute/expressroute-introduction.md) 및 [FastConnect를](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm)통해 Azure와 Oracle 클라우드 인프라(OCI) 간의 직접 상호 연결을 제공합니다. ExpressRoute 및 FastConnect 상호 연결을 통해 고객은 두 클라우드 간의 낮은 대기 시간, 높은 처리량, 개인 직접 연결을 경험할 수 있습니다.

> [!IMPORTANT]
> 오라클은 2020년 5월까지 Azure/Oracle Cloud 상호 연결 솔루션을 사용할 때 이러한 응용 프로그램을 Azure에서 실행하도록 인증합니다.
> * E-비즈니스 스위트
> * JD Edwards EnterpriseOne
> * PeopleSoft
> * 오라클 리테일 애플리케이션
> * 오라클 하이페리온 재무 관리

다음 이미지는 상호 연결에 대한 상위 개요를 보여 주며,

![클라우드 간 네트워크 연결](media/configure-azure-oci-networking/azure-oci-connect.png)

## <a name="prerequisites"></a>사전 요구 사항

* Azure와 OCI 간의 연결을 설정하려면 활성 Azure 구독및 활성 OCI 테넌트가 있어야 합니다.

* Azure ExpressRoute 피어링 위치가 OCI FastConnect와 같은 피어링 위치에 근접하거나 있는 경우에만 연결이 가능합니다. [지역 가용성을](oracle-oci-overview.md#region-availability)참조하십시오.

## <a name="configure-direct-connectivity-between-expressroute-and-fastconnect"></a>익스프레스루트와 패스트커넥트 간의 직접 연결 구성

1. 리소스 그룹 에서 Azure 구독에서 표준 ExpressRoute 회로를 만듭니다. 
    * ExpressRoute를 만드는 동안 **오라클 클라우드 패스트커넥트(Oracle Cloud FastConnect)를** 서비스 공급자로 선택합니다. ExpressRoute 회로를 만들려면 [단계별 가이드를](../../../expressroute/expressroute-howto-circuit-portal-resource-manager.md)참조하십시오.
    * Azure ExpressRoute 회로는 세분화된 대역폭 옵션을 제공하는 반면 FastConnect는 1, 2, 5 또는 10Gbps를 지원합니다. 따라서 ExpressRoute에서 이러한 일치 하는 대역폭 옵션 중 하나를 선택 하는 것이 좋습니다.

    ![익스프레스루트 회로 생성](media/configure-azure-oci-networking/exr-create-new.png)
1. 익스프레스루트 서비스 **키를**적어 둡자. FastConnect 회로를 구성하는 동안 키를 제공해야 합니다.

    ![익스프레스루트 서비스 키](media/configure-azure-oci-networking/exr-service-key.png)

    > [!IMPORTANT]
    > 익스프레스루트 회로가 프로비전되는 즉시 익스프레스루트 요금이 청구됩니다(공급자 **상태가** **프로비전되지 않은**경우에도).

1. Azure 가상 네트워크 또는 OCI 가상 클라우드 네트워크 IP 주소 주소 공간과 겹치지 않는 /30각각의 두 개의 개인 IP 주소 공간을 개척합니다. 첫 번째 IP 주소 공간을 기본 주소 공간으로, 두 번째 IP 주소 공간을 보조 주소 공간으로 참조합니다. FastConnect 회로를 구성할 때 필요한 주소를 적어 둡자하십시오.
1. 동적 라우팅 게이트웨이(DRG)를 만듭니다. FastConnect 회로를 만들 때 이 필요가 있습니다. 자세한 내용은 동적 [라우팅 게이트웨이](https://docs.cloud.oracle.com/iaas/Content/Network/Tasks/managingDRGs.htm) 설명서를 참조하십시오.
1. 오라클 테넌트 아래에 FastConnect 회로를 만듭니다. 자세한 내용은 Oracle [설명서를](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/azure.htm)참조하십시오.
  
    * FastConnect 구성에서 **공급자로 Microsoft Azure: ExpressRoute를** 선택합니다.
    * 이전 단계에서 프로비전한 동적 라우팅 게이트웨이를 선택합니다.
    * 프로비저닝할 대역폭을 선택합니다. 최적의 성능을 위해 대역폭은 ExpressRoute 회로를 만들 때 선택한 대역폭과 일치해야 합니다.
    * **공급자 서비스 키에서**ExpressRoute 서비스 키를 붙여넣습니다.
    * **기본 BGP** IP 주소의 이전 단계에 새겨진 첫 번째/30 개인 IP 주소 공간과 보조 **BGP** IP 주소에 대한 두 번째 /30 개인 IP 주소 공간을 사용합니다.
        * 오라클 BGP IP 주소(기본 및 보조)에 대한 두 범위의 첫 번째 사용 가능한 주소와 두 번째 주소를 고객 BGP IP 주소(FastConnect 관점에서)에 할당합니다. 첫 번째 사용 가능한 IP 주소는 /30 주소 공간의 두 번째 IP 주소입니다(첫 번째 IP 주소는 Microsoft에서 예약됨).
    * **만들기**를 클릭합니다.
1. 경로 테이블을 사용하여 동적 라우팅 게이트웨이를 통해 오라클 테넌트 에서 FastConnect를 가상 클라우드 네트워크에 연결합니다.
1. Azure로 이동하여 ExpressRoute 회로의 **공급자 상태가** **프로비전된 프로비저닝으로** 변경되었으며 **Azure private** 형식의 피어링이 프로비전되었는지 확인합니다. 이 단계는 다음 단계에 대한 필수 조건입니다.

    ![익스프레스루트 공급자 상태](media/configure-azure-oci-networking/exr-provider-status.png)
1. **Azure 개인** 피어링을 클릭합니다. FastConnect 회로를 설정할 때 입력한 정보에 따라 피어링 세부 정보가 자동으로 구성되었음을 확인할 수 있습니다.

    ![개인 피어링 설정](media/configure-azure-oci-networking/exr-private-peering.png)

## <a name="connect-virtual-network-to-expressroute"></a>가상 네트워크를 익스프레스루트에 연결

1. 아직 가상 네트워크 및 가상 네트워크 게이트웨이를 만들지 않은 경우 가상 네트워크 및 가상 네트워크 게이트웨이를 만듭니다. 자세한 내용은 [단계별 가이드를](../../../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)참조하십시오.
1. [테라폼 스크립트를](https://github.com/microsoft/azure-oracle/tree/master/InterConnect-2) 실행하거나 PowerShell 명령을 실행하여 [익스프레스루트 FastPath 구성을](../../../expressroute/expressroute-howto-linkvnet-arm.md#configure-expressroute-fastpath)실행하여 가상 네트워크 게이트웨이와 ExpressRoute 회로 간의 연결을 설정합니다.

네트워크 구성을 완료한 후에는 Azure 포털의 ExpressRoute 개인 피어링 블레이드에서 **ARP 레코드 받기** 및 **경로 테이블 가져오기(route table)를** 클릭하여 구성의 유효성을 확인할 수 있습니다.

## <a name="automation"></a>Automation

Microsoft는 네트워크 상호 연결을 자동으로 배포할 수 있도록 Terraform 스크립트를 만들었습니다. Terraform 스크립트는 Azure 구독에 대한 적절한 권한이 필요하기 때문에 실행하기 전에 Azure를 사용하여 인증해야 합니다. [인증은 Azure Active Directory 서비스 보안 주체를](../../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) 사용하거나 Azure CLI를 사용하여 수행할 수 있습니다. 자세한 내용은 [테라폼 문서를](https://www.terraform.io/docs/providers/azurerm/auth/azure_cli.html)참조하십시오.

상호 연결을 배포하는 Terraform 스크립트 및 관련 문서는 이 [GitHub 리포지토리에서](https://aka.ms/azureociinterconnecttf)찾을 수 있습니다.

## <a name="monitoring"></a>모니터링

두 클라우드에 에이전트를 설치하면 [NPM(Azure Network 성능 모니터)을](../../../expressroute/how-to-npm.md) 활용하여 종단 간 네트워크의 성능을 모니터링할 수 있습니다. NPM은 네트워크 문제를 쉽게 식별할 수 있도록 도와주며 이를 제거하는 데 도움이 됩니다.

## <a name="delete-the-interconnect-link"></a>상호 연결 링크 삭제

상호 연결을 삭제하려면 주어진 특정 순서로 다음 단계를 따라야 합니다. 이렇게 하지 않으면 "실패한 상태" ExpressRoute 회로가 생성됩니다.

1. 익스프레스루트 연결을 삭제합니다. 연결페이지의 **삭제** 아이콘을 클릭하여 연결을 삭제합니다. 자세한 내용은 [ExpressRoute 문서를](../../../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#delete-a-connection-to-unlink-a-vnet)참조하십시오.
1. 오라클 클라우드 콘솔에서 오라클 패스트커넥트 삭제합니다.
1. Oracle FastConnect 회로가 삭제되면 Azure ExpressRoute 회로를 삭제할 수 있습니다.

이 시점에서 삭제 및 프로비저닝 해제 프로세스가 완료됩니다.

## <a name="next-steps"></a>다음 단계

* OCI와 Azure 간의 교차 클라우드 연결에 대한 자세한 내용은 [Oracle 설명서를](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/azure.htm)참조하십시오.
* [Terraform 스크립트를](https://aka.ms/azureociinterconnecttf) 사용하여 Azure를 통해 대상 Oracle 응용 프로그램에 대한 인프라를 배포하고 네트워크 상호 연결을 구성합니다. 
