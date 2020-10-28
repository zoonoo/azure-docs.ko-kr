---
title: SQL Server Always On 가용성 그룹 개요
description: 이 문서에서는 Azure Virtual Machines의 SQL Server Always On 가용성 그룹을 소개합니다.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.assetid: 601eebb1-fc2c-4f5b-9c05-0e6ffd0e5334
ms.service: virtual-machines-sql
ms.topic: overview
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/07/2020
ms.author: mathoma
ms.custom: seo-lt-2019, devx-track-azurecli
ms.openlocfilehash: 4919abd29ecf10c9116257750374ef53b4bd9d16
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92789916"
---
# <a name="always-on-availability-group-on-sql-server-on-azure-vms"></a>Azure VM의 SQL Server에 대한 Always On 가용성 그룹
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

이 문서에서는 Azure VMs(Virtual Machines)의 SQL Server에 대한 Always On 가용성 그룹을 소개합니다. 

## <a name="overview"></a>개요

Azure Virtual Machines의 Always On 가용성 그룹은 [온-프레미스의 Always On 가용성 그룹](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server)과 비슷합니다. 그러나 가상 머신은 Azure에서 호스팅되므로 VM 중복성 및 Azure 네트워크의 트래픽 라우팅과 같은 몇 가지 추가 고려 사항도 있습니다. 

다음 다이어그램에서는 Azure VM의 SQL Server에 대한 가용성 그룹을 보여 줍니다.

![가용성 그룹](./media/availability-group-overview/00-EndstateSampleNoELB.png)


## <a name="vm-redundancy"></a>VM 중복성 

중복성 및 고가용성을 강화하려면 SQL Server VM이 동일한 [가용성 집합](../../../virtual-machines/windows/tutorial-availability-sets.md#availability-set-overview) 또는 다른 [가용성 영역](../../../availability-zones/az-overview.md)에 있어야 합니다.

가용성 집합은 동일한 가용성 영역에 두 개의 하위 영역이 없도록 구성된 리소스의 그룹입니다. 이렇게 하면 배포를 롤아웃하는 중에 그룹의 여러 리소스에 영향을 주지 않습니다. 


## <a name="connectivity"></a>연결 

기존 온-프레미스 배포에서 클라이언트는 VNN(가상 네트워크 이름)을 사용하여 가용성 그룹 수신기에 연결하고, 수신기는 트래픽을 가용성 그룹의 적절한 SQL Server 복제본으로 라우팅합니다. 그러나 Azure 네트워크에서 트래픽을 라우팅하기 위한 추가 요구 사항이 있습니다. 

Azure VM에서 SQL Server를 사용하면 트래픽을 가용성 그룹 수신기로 라우팅하도록 [부하 분산 장치](availability-group-vnn-azure-load-balancer-configure.md)를 구성하거나 SQL Server 2019 CU8 이상을 사용하는 경우 기존 VNN 가용성 그룹 수신기를 대체하도록 [DNN(분산 네트워크 이름) 수신기](availability-group-distributed-network-name-dnn-listener-configure.md)를 구성할 수 있습니다. 


### <a name="vnn-listener"></a>VNN 수신기 

[Azure Load Balancer](../../../load-balancer/load-balancer-overview.md)를 사용하여 트래픽을 클라이언트에서 Azure 네트워크의 기존 가용성 그룹 VNN(가상 네트워크 이름) 수신기로 라우팅합니다. 

부하 분산 장치는 VNN 수신기에 대한 IP 주소를 보유합니다. 둘 이상의 가용성 그룹이 있으면 각 그룹마다 VNN 수신기가 필요합니다. 하나의 부하 분산 장치가 여러 수신기를 지원할 수 있습니다.

시작하려면 [부하 분산 장치 구성](availability-group-vnn-azure-load-balancer-configure.md)을 참조하세요. 

### <a name="dnn-listener"></a>DNN 수신기

SQL Server 2019 CU8에는 DNN(분산 네트워크 이름) 수신기에 대한 지원이 도입되었습니다. DNN 수신기는 기존 가용성 그룹 수신기를 대체하므로 Azure 네트워크에서 트래픽을 라우팅하는 데 Azure Load Balancer가 필요하지 않습니다. 

DNN 수신기는 배포를 간소화하고, 유지 관리 및 비용을 줄이며, 장애가 발생하는 경우 장애 조치 시간을 줄이므로 Azure에서 추천되는 HADR 연결 솔루션입니다. 

DNN 수신기를 사용하여 기존 VNN 수신기를 대체하거나, 두 개의 고유한 연결 지점이 가용성 그룹에 있도록 기존 VNN 수신기와 함께 이 수신기를 사용합니다. 한 연결 지점은 VNN 수신기 이름(기본값이 아닌 경우 포트)을 사용하고 다른 하나는 DNN 수신기 및 포트를 사용합니다. 이는 부하 분산 장치 장애 조치 대기 시간을 방지하지만 분산 가용성 그룹, 서비스 broker 또는 파일 스트림과 같은 VNN 수신기에 종속된 SQL Server 기능을 활용하려는 고객에게 유용할 수 있습니다. 자세한 내용은 [DNN 수신기 및 SQL Server 기능 상호 운용성](availability-group-dnn-interoperability.md)을 참조하세요.

시작하려면 [DNN 수신기 구성](availability-group-distributed-network-name-dnn-listener-configure.md)을 참조하세요.


## <a name="deployment"></a>배포 

가용성 그룹을 Azure VM의 SQL Server에 배포하는 여러 가지 옵션이 있으며, 일부는 다른 옵션보다 더 많은 자동화를 사용합니다. 

사용 가능한 옵션을 비교한 표는 다음과 같습니다. 

| |**[Azure portal](availability-group-azure-portal-configure.md)**|**[Azure CLI/PowerShell](./availability-group-az-commandline-configure.md)**|**[빠른 시작 템플릿](availability-group-quickstart-template-configure.md)**|**[수동](availability-group-manually-configure-prerequisites-tutorial.md)** | 
|---------|---------|---------|--------- |---------|
|**SQL Server 버전** |2016 이상 |2016 이상|2016 이상|2012 이상|
|**SQL Server 에디션** |Enterprise |Enterprise |Enterprise |Enterprise, Standard|
|**Windows Server 버전**| 2016 이상 | 2016 이상 | 2016 이상 | 모두| 
|**사용자에 대한 클러스터 만들기**|예|예 | 예 |아니요|
|**사용자에 대한 가용성 그룹 만들기** |예 |아니요|아니요|예|
|**독립적으로 수신기 및 부하 분산 장치 만들기** |아니요|아니요|예|예|
|**이 메서드를 사용하여 DNN 수신기를 만들 수 있는지 여부**|아니요|아니요|아니요|예|
|**WSFC 쿼럼 구성**|클라우드 감시|클라우드 감시|클라우드 감시|모두|
|**여러 지역이 포함된 DR** |아니요|아니요|아니요|예|
|**다중 서브넷 지원** |예|예|예|예|
|**기존 AD 지원**|예|예|예|예|
|**동일한 지역의 다중 영역이 포함된 DR**|예|예|예|예|
|**AD가 없는 분산 AG**|아니요|아니요|아니요|예|
|**클러스터가 없는 분산 AG** |아니요|아니요|아니요|예|
||||||



## <a name="considerations"></a>고려 사항 

Azure IaaS VM 게스트 장애 조치 클러스터의 경우 서버(클러스터 노드)당 하나의 NIC 및 단일 서브넷을 사용하는 것이 좋습니다. Azure 네트워킹에는 Azure IaaS VM 게스트 클러스터에서 추가 NIC 및 서브넷을 불필요하게 만드는 물리적 중복성이 있습니다. 클러스터 유효성 검사 보고서는 노드가 단일 네트워크에서만 연결 가능하다는 경고를 표시하지만, Azure IaaS VM 게스트 장애 조치(failover) 클러스터에서는 이 경고를 무시해도 됩니다. 

## <a name="next-steps"></a>다음 단계

[HADR 모범 사례](hadr-cluster-best-practices.md)를 검토한 다음, [Azure Portal](availability-group-azure-portal-configure.md), [Azure CLI / PowerShell](./availability-group-az-commandline-configure.md), [빠른 시작 템플릿](availability-group-quickstart-template-configure.md)을 사용하여 가용성 그룹 배포를 시작하거나 [수동](availability-group-manually-configure-prerequisites-tutorial.md)으로 이 작업을 시작합니다.

또는 [클러스터가 없는 가용성 그룹](availability-group-clusterless-workgroup-configure.md) 또는 가용성 그룹을 [여러 지역](availability-group-manually-configure-multiple-regions.md)에 배포할 수 있습니다.