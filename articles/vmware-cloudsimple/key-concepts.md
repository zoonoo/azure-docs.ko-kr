---
title: Azure VMware 솔루션 (AVS) 관리를 위한 주요 개념
description: Azure VMware 솔루션 (AVS) 관리의 주요 개념을 설명 합니다.
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: b-shsury
ms.date: 04/24/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c581ffbba7fbaefc8fbd0ffc7e92350bd69802b8
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025234"
---
# <a name="key-concepts-for-administration-of-azure-vmware-solutions-avs"></a>Azure VMware 솔루션 (AVS) 관리를 위한 주요 개념

Azure VMware 솔루션 (AVS)을 관리 하려면 다음과 같은 개념을 이해 해야 합니다.

* Avs 서비스는 Azure VMware 솔루션 (AVS) 서비스로 표시 됩니다.
* Avs 노드-Azure VMware 솔루션 (AVS) 노드로 표시 됩니다.
* AVS 사설 클라우드
* 서비스 네트워킹
* Avs (Azure VMware Solutions)로 표시 되는 AVS 가상 머신-가상 머신

## <a name="avs-service"></a>AVS 서비스

AVS 서비스를 사용 하 여 Azure Portal에서 VMware 솔루션 (AVS)과 관련 된 모든 리소스를 만들고 관리할 수 있습니다. 서비스를 사용 하려는 모든 지역에 서비스 리소스를 만듭니다.

[AVS 서비스](cloudsimple-service.md)에 대해 자세히 알아보세요.

## <a name="avs-node"></a>AVS 노드

AVS 노드는 VMware ESXi 하이퍼바이저가 배포 되는 전용 운영 체제 미 설치 계산 및 저장소 호스트입니다. 그런 다음이 노드는 VMware vSphere, vCenter, vSAN 및 NSX 플랫폼에 통합 됩니다. 또한 AVS 네트워킹 서비스 및에 지 네트워킹 서비스를 사용할 수 있습니다. 각 노드는 [AVS 사설 클라우드](cloudsimple-private-cloud.md)를 만들기 위해 프로 비전 할 수 있는 계산 및 저장소 용량 단위로 사용 됩니다. AVS 서비스를 사용할 수 있는 지역에서 노드를 프로 비전 하거나 예약 합니다.

[AVS 노드에](cloudsimple-node.md)대해 자세히 알아보세요.

## <a name="avs-private-cloud"></a>AVS 사설 클라우드

AVS 사설 클라우드는 자체 관리 도메인의 vCenter 서버에서 관리 하는 격리 된 VMware stack 환경입니다. VMware 스택에는 ESXi 호스트, vSphere, vCenter, Vsphere 및 NSX가 포함 됩니다. 스택은 전용 노드 (전용 및 격리 된 운영 체제 미 설치 하드웨어)에서 실행 되며 vCenter 및 NSX Manager를 포함 하는 네이티브 VMware 도구를 통해 사용자가 사용 합니다. 전용 노드는 Azure 위치에 배포 되 고 Azure에서 관리 됩니다. 각 AVS 사설 클라우드는 Vlan 및 서브넷 및 방화벽 테이블과 같은 네트워킹 서비스를 사용 하 여 분할 하 고 보호할 수 있습니다. 온-프레미스 환경 및 Azure 네트워크에 대 한 연결은 보안, 개인 VPN 및 Azure Express 경로 연결을 사용 하 여 생성 됩니다.

[AVS 사설 클라우드에](cloudsimple-private-cloud.md)대해 자세히 알아보세요.

## <a name="service-networking"></a>서비스 네트워킹

AVS 서비스는 사용자의 AVS 서비스가 배포 된 지역 당 네트워크를 제공 합니다. 네트워크는 기본적으로 라우팅이 활성화 된 단일 TCP 계층 3 주소 공간입니다. 이 지역에서 만든 모든 AVS 사설 클라우드 및 서브넷은 추가 구성 없이 서로 통신 합니다. Vlan을 사용 하 여 vCenter에서 분산 포트 그룹을 만듭니다. 다음 네트워크 기능을 사용 하 여 AVS 사설 클라우드에서 워크 로드 리소스를 구성 하 고 보호할 수 있습니다.

* [Vlan 및 서브넷](cloudsimple-vlans-subnets.md)
* [방화벽 테이블](cloudsimple-firewall-tables.md)
* [VPN 게이트웨이](cloudsimple-vpn-gateways.md)
* [공용 IP](cloudsimple-public-ip-address.md)
* [Azure 네트워크 연결](cloudsimple-azure-network-connection.md)

## <a name="avs-virtual-machine"></a>AVS 가상 컴퓨터

AVS 서비스를 사용 하 여 Azure Portal에서 VMware 가상 컴퓨터를 관리할 수 있습니다. VSphere 환경에서 하나 이상의 클러스터 또는 리소스 풀을 서비스가 만들어진 구독에 매핑할 수 있습니다.

다음에 대해 자세히 알아봅니다.

* [AVS 가상 컴퓨터](cloudsimple-virtual-machines.md)
* [Azure 구독 매핑](https://docs.azure.cloudsimple.com/azure-subscription-mapping/)
