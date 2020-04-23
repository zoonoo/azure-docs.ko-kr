---
title: 클라우드심플에 의한 Azure VMware 솔루션 관리를 위한 주요 개념
titleSuffix: Azure VMware Solution by CloudSimple
description: CloudSimple을 통해 Azure VMware 솔루션 관리를 위한 주요 개념설명
author: sharaths-cs
ms.author: b-shsury
ms.date: 04/24/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 410215550f728d6b11b02b3ef108d429481e42a0
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869021"
---
# <a name="key-concepts-for-administration-of-azure-vmware-solutions-by-cloudsimple"></a>클라우드심플에 의한 Azure VMware 솔루션 관리를 위한 주요 개념

CloudSimple으로 Azure VMware 솔루션을 관리하려면 다음 개념을 이해해야 합니다.

* 클라우드 단순 - 서비스에 의해 Azure VMware 솔루션으로 표시되는 클라우드 심플 서비스
* CloudSimple - 노드에 의해 Azure VMware 솔루션으로 표시되는 클라우드심플 노드
* 클라우드심플 프라이빗 클라우드
* 서비스 네트워킹
* CloudSimple - 가상 머신에 의해 Azure VMware 솔루션으로 표시되는 CloudSimple 가상 머신

## <a name="cloudsimple-service"></a>클라우드 심플 서비스

CloudSimple 서비스를 사용하면 Azure 포털에서 CloudSimple별 VMware 솔루션과 관련된 모든 리소스를 만들고 관리할 수 있습니다. 서비스를 사용하려는 모든 리전에서 서비스 리소스를 만듭니다.

[CloudSimple 서비스에](cloudsimple-service.md)대해 자세히 알아보십시오.

## <a name="cloudsimple-node"></a>클라우드심플 노드

CloudSimple 노드는 VMware ESXi 하이퍼바이저를 배포하는 전용 베어메탈 하이퍼컨버지드 컴퓨팅 및 스토리지 호스트입니다. 그런 다음 이 노드는 VMware vSphere, vCenter, vSAN 및 NSX 플랫폼에 통합됩니다. CloudSimple 네트워킹 서비스와 에지 네트워킹 서비스도 사용할 수 있습니다. 각 노드는 [CloudSimple 프라이빗 클라우드를](cloudsimple-private-cloud.md)만들기 위해 프로비전할 수 있는 컴퓨팅 및 스토리지 용량의 단위로 사용됩니다. CloudSimple 서비스를 사용할 수 있는 리전에서 노드를 프로비전하거나 예약합니다.

[CloudSimple 노드에](cloudsimple-node.md)대해 자세히 알아보십시오.

## <a name="cloudsimple-private-cloud"></a>클라우드심플 프라이빗 클라우드

CloudSimple 프라이빗 클라우드는 자체 관리 도메인의 vCenter 서버에서 관리하는 격리된 VMware 스택 환경입니다. VMware 스택에는 ESXi 호스트, vSphere, vCenter, vSAN 및 NSX가 포함됩니다. 스택은 전용 노드(전용 및 격리된 베어 메탈 하드웨어)에서 실행되며 vCenter 및 NSX Manager를 포함하는 기본 VMware 도구를 통해 사용자가 사용합니다. 전용 노드는 Azure 위치에 배포되며 Azure에서 관리합니다. 각 프라이빗 클라우드는 VLAN 및 서브넷 및 방화벽 테이블과 같은 네트워킹 서비스를 사용하여 분할 및 보호할 수 있습니다. 온-프레미스 환경 및 Azure 네트워크에 대한 연결은 보안 개인 VPN 및 Azure ExpressRoute 연결을 사용하여 만들어집니다.

[CloudSimple 프라이빗 클라우드에](cloudsimple-private-cloud.md)대해 자세히 알아보십시오.

## <a name="service-networking"></a>서비스 네트워킹

CloudSimple 서비스는 CloudSimple 서비스가 배포된 지역별 네트워크를 제공합니다. 네트워크는 라우팅을 기본적으로 사용하도록 설정한 단일 TCP 계층 3 주소 공간입니다. 이 지역에서 만든 모든 프라이빗 클라우드와 서브넷은 추가 구성 없이 서로 통신합니다. VLAN을 사용하여 vCenter에서 분산 포트 그룹을 만듭니다. 다음 네트워크 기능을 사용하여 사설 클라우드에서 워크로드 리소스를 구성하고 보호할 수 있습니다.

* [VLAN 및 서브넷](cloudsimple-vlans-subnets.md)
* [방화벽 테이블](cloudsimple-firewall-tables.md)
* [VPN 게이트웨이](cloudsimple-vpn-gateways.md)
* [공용 IP](cloudsimple-public-ip-address.md)
* [Azure 네트워크 연결](cloudsimple-azure-network-connection.md)

## <a name="cloudsimple-virtual-machine"></a>클라우드간단한 가상 머신

CloudSimple 서비스를 사용하면 Azure 포털에서 VMware 가상 컴퓨터를 관리할 수 있습니다. vSphere 환경에서 하나 이상의 클러스터 또는 리소스 풀을 서비스가 생성되는 구독에 매핑할 수 있습니다.

다음에 대해 자세히 알아봅니다.

* [클라우드단순 가상 머신](cloudsimple-virtual-machines.md)
* [Azure 구독 매핑](https://docs.microsoft.com/azure/vmware-cloudsimple/azure-subscription-mapping/)
