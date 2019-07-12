---
title: Oracle 클라우드 인프라를 사용 하 여 Azure ExpressRoute 연결 | Microsoft Docs
description: 클라우드 간 Oracle 응용 프로그램 솔루션을 사용 하도록 설정 하려면 FastConnect 클라우드 인프라 OCI (Oracle)를 사용 하 여 Azure ExpressRoute 연결
documentationcenter: virtual-machines
author: romitgirdhar
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/24/2019
ms.author: rogirdh
ms.openlocfilehash: 671d7c8eb9f10e346b49056e1cc117c9882bb6e8
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707654"
---
# <a name="set-up-a-direct-interconnection-between-azure-and-oracle-cloud-infrastructure"></a>Oracle 클라우드 인프라와 Azure 간에 직접 상호 연결 설정  

만들려는 [통합 된 다중 클라우드 환경](oracle-oci-overview.md) (미리 보기), Microsoft와 Oracle 클라우드 인프라 OCI (Oracle)를 통해 Azure와 직접 상호 연결을 제공 합니다. [ExpressRoute](../../../expressroute/expressroute-introduction.md) 및 [ FastConnect](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm)합니다. ExpressRoute 및 FastConnect 상호 연결을 통해 낮은 대기 시간, 높은 처리량, 두 클라우드 간의 직접 연결을 개인 고객에 게 발생할 수 있습니다.

> [!IMPORTANT]
> Microsoft Azure 및 OCI 간의 연결이 미리 보기 단계에서입니다. OCI와 Azure 간의 대기 시간이 짧은 연결을 사용 하려면 Azure 구독 및 테 넌 트 OCI 먼저 해야이 기능에 대 한 허용 목록에 추가 합니다.

다음 이미지에는 상호 연결의 대략적인 개요를 보여 줍니다.

![클라우드 간 네트워크 연결](media/configure-azure-oci-networking/azure-oci-connect.png)

## <a name="prerequisites"></a>필수 구성 요소

* Azure와 OCI 사이의 연결을 설정 하려면 활성 Azure 구독 및 활성 OCI 테 넌 트가 있어야 합니다.

* 연결은만 가능한 Azure ExpressRoute 피어 링 위치 가까이 또는 OCI FastConnect 동일한 피어 링 위치에 있습니다. 참조 [미리 보기 제한 사항](oracle-oci-overview.md#preview-limitations)합니다.

* Azure 구독에는이 미리 보기 기능에 대 한 허용 목록에 추가 해야 합니다. 구독에서이 기능을 사용 하도록 설정 하려면 Microsoft 담당자에 게 문의 합니다.

* OCI 테 넌 시에는이 미리 보기 기능에 대 한 허용 목록에 추가 해야 합니다. 자세한 내용은 Oracle 담당자에 게 문의 합니다.

## <a name="configure-direct-connectivity-between-expressroute-and-fastconnect"></a>ExpressRoute 및 FastConnect 간의 직접 연결 구성

1. 리소스 그룹에서 Azure 구독에 표준 ExpressRoute 회로를 만듭니다. 
    * ExpressRoute를 만드는 동안 선택 **Oracle 클라우드 FastConnect** 서비스 공급자로 합니다. ExpressRoute 회로 만들려면 참조 된 [단계별 가이드](../../../expressroute/expressroute-howto-circuit-portal-resource-manager.md)합니다.
    * Azure ExpressRoute 회로 1, 2, 5 또는 10gbps FastConnect 지 원하는 반면 세분화 된 대역폭 옵션을 제공 합니다. 따라서 ExpressRoute에서 다음 일치 하는 대역폭 옵션 중 하나를 선택 하는 것이 좋습니다.

    ![ExpressRoute 회로 만들기](media/configure-azure-oci-networking/exr-create-new.png)
1. ExpressRoute 적어 **서비스 키**합니다. FastConnect 회로 구성 하는 동안 키를 제공 해야 합니다.

    ![ExpressRoute 서비스 키](media/configure-azure-oci-networking/exr-service-key.png)

    > [!IMPORTANT]
    > 요금이 청구 됩니다 ExpressRoute 요금에 대 한 ExpressRoute 회로가 프로 비전 하는 즉시 (경우에 합니다 **공급자 상태** 됩니다 **프로 비전 되지**).

1. Azure 가상 네트워크 또는 OCI 가상 클라우드 네트워크 IP 주소 공간이 겹치지 않는 각/30의 두 개인 IP 주소 공간 협의 합니다. 기본 주소 공간으로 첫 번째 IP 주소 공간 및 보조 주소 공간으로 두 번째 IP 주소 공간 이라고 합니다. FastConnect 회로 구성 하는 경우 필요한 주소, note 합니다.
1. 동적 라우팅 게이트웨이 (DRG)를 만듭니다. FastConnect 회로 만들 때이 해야 합니다. 자세한 내용은 참조는 [동적 라우팅 게이트웨이](https://docs.cloud.oracle.com/iaas/Content/Network/Tasks/managingDRGs.htm) 설명서.
1. Oracle 테 넌 트 아래 FastConnect 회로를 만듭니다. 자세한 내용은 참조는 [Oracle 설명서](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/azure.htm)합니다.
  
    * FastConnect 구성에서 선택 **Microsoft Azure: ExpressRoute** 공급자로 합니다.
    * 이전 단계에서 프로 비전 하는 동적 라우팅 게이트웨이 선택 합니다.
    * 프로 비전에 대 한 대역폭을 선택 합니다. 최적의 성능을 위해서는 대역폭 ExpressRoute 회로 만들 때 선택한 대역폭을 일치 해야 합니다.
    * **공급자 서비스 키**, ExpressRoute 서비스 키를 붙여 넣습니다.
    * 사용 하 여 첫 번째/30 개인 IP 주소 공간에 대 한 이전 단계에서 획득 합니다 **기본 BGP IP 주소** 및 두 번째/30 개인 IP 주소 공간을 합니다 **보조 BGP IP** 주소입니다.
        * (FastConnect 관점) BGP IP 주소를 고객에 게 Oracle BGP IP 주소 (기본 및 보조) 및 두 번째 주소에 대 한 두 범위의 첫 번째 사용 가능한 주소를 할당 합니다. 첫 번째 사용 가능한 IP 주소는 / 30의 두 번째 ip 주소 공간 (첫 번째 IP 주소는 Microsoft에서 예약 됨).
    * **만들기**를 클릭합니다.
1. FastConnect에서 경로 테이블을 사용 하 여 동적 라우팅 게이트웨이 통해 Oracle 테 넌 트에서 가상 클라우드 네트워크에 연결을 완료 합니다.
1. Azure로 이동 하 고 있는지 확인 합니다 **공급자 상태** ExpressRoute 회로에 변경 되었습니다 **프로 비전 됨** 올바르고 형식의 피어 링 **Azure 개인** 되었습니다 프로 비전 합니다. 다음 단계에 대 한 필수입니다.

    ![ExpressRoute 공급자 상태](media/configure-azure-oci-networking/exr-provider-status.png)
1. 클릭 합니다 **Azure 개인** 피어 링 합니다. 피어 링 세부 정보를 자동으로 구성 된 FastConnect 회로를 설정할 때 입력 한 정보를 기반으로 하는 것이 나타납니다.

    ![개인 피어 링 설정](media/configure-azure-oci-networking/exr-private-peering.png)

## <a name="connect-virtual-network-to-expressroute"></a>가상 네트워크를 ExpressRoute 연결

1. 아직 없는 경우 가상 네트워크 및 가상 네트워크 게이트웨이 만듭니다. 자세한 내용은 참조는 [단계별 가이드](../../../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)합니다.
1. 실행 하 여 virtual network 게이트웨이 및 ExpressRoute 회로 간의 연결을 설정 합니다 [Terraform 스크립트](https://github.com/microsoft/azure-oracle/tree/master/InterConnect-2) 또는 PowerShell 명령을 실행 하 여 [ExpressRoute FastPath 구성](../../../expressroute/expressroute-howto-linkvnet-arm.md#configure-expressroute-fastpath)합니다.

네트워크 구성을 완료 했으면를 클릭 하 여 구성의 유효성을 확인할 수 있습니다 **ARP 레코드 가져오기** 하 고 **Get 경로 테이블** ExpressRoute 개인 피어 링 블레이드 아래에서 Azure portal

## <a name="automation"></a>자동화

Microsoft는 자동화 된 배포의 네트워크 상호 연결을 사용 하도록 Terraform 스크립트를 만들었습니다. Terraform 스크립트는 Azure 구독에 대 한 적절 한 권한이 필요 하므로 실행 하기 전에 Azure를 사용 하 여 인증 해야 합니다. 사용 하 여 인증을 수행할 수 있습니다는 [Azure Active Directory 서비스 주체](../../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) 또는 Azure CLI를 사용 합니다. 자세한 내용은 참조는 [Terraform 설명서](https://www.terraform.io/docs/providers/azurerm/auth/azure_cli.html)합니다.

Terraform 스크립트 및 관련된 문서에는 inter-connect를 배포 하려면이에서 찾을 수 있습니다 [GitHub 리포지토리](https://aka.ms/azureociinterconnecttf)합니다.

## <a name="monitoring"></a>모니터링

두 클라우드 모두에 에이전트를 설치, Azure를 활용할 수 있습니다 [네트워크 성능 모니터 (NPM)](../../../expressroute/how-to-npm.md) 종단 간 네트워크 성능을 모니터링 합니다. NPM은 네트워크 문제를 쉽게 식별할 수 있습니다 하 고 제거할 수 있습니다.

## <a name="delete-the-interconnect-link"></a>상호 연결 링크를 삭제 합니다.

상호 연결을 삭제 하려면 다음 단계를 따라야를 특정 순서로 합니다. 이렇게 하지 않으면 "실패 한 상태" ExpressRoute 회로 발생 합니다.

1. ExpressRoute 연결을 삭제 합니다. 클릭 하 여 연결을 삭제 합니다 **삭제** 연결에 대 한 페이지의 아이콘입니다. 자세한 내용은 참조는 [ExpressRoute 설명서](../../../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#delete-a-connection-to-unlink-a-vnet)합니다.
1. Oracle 클라우드 콘솔에서 Oracle FastConnect를 삭제 합니다.
1. Oracle FastConnect 회로 삭제 한 후에 Azure ExpressRoute 회로 삭제할 수 있습니다.

이 시점에서 삭제 하 고 프로세스를 프로 비전 해제 완료 되었습니다.

## <a name="next-steps"></a>다음 단계

* OCI와 Azure 간의 클라우드 간 연결에 대 한 자세한 내용은 참조는 [Oracle 설명서](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/azure.htm)합니다.
* 사용 하 여 [Terraform 스크립트](https://aka.ms/azureociinterconnecttf) Azure를 통해 대상된 Oracle 응용 프로그램에 대 한 인프라를 배포 하 고 네트워크 상호 연결을 구성 합니다. 