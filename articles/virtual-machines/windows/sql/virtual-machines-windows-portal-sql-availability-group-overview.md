---
title: 가용성 그룹 개요
description: 이 문서에서는 Azure Virtual Machines의 SQL Server 가용성 그룹을 소개합니다.
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 601eebb1-fc2c-4f5b-9c05-0e6ffd0e5334
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/13/2017
ms.author: mikeray
ms.custom: seo-lt-2019
ms.openlocfilehash: 8119990ab4ab4a918e325976092100086a547aa4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74037488"
---
# <a name="introducing-sql-server-availability-groups-on-azure-virtual-machines"></a>Azure 가상 컴퓨터에서 SQL Server 가용성 그룹 소개

이 문서에서는 Azure Virtual Machines의 SQL Server 가용성 그룹을 소개합니다. 

Azure Virtual Machines의 Always On 가용성 그룹은 온-프레미스의 Always On 가용성 그룹과 비슷합니다. 자세한 내용은 [Always On 가용성 그룹(SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx)을 참조하세요. 

이 다이어그램에서는 Azure Virtual Machines의 전체 SQL Server 가용성 그룹 일부를 보여 줍니다.

![가용성 그룹](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

Azure Virtual Machines의 가용성 그룹에 대한 주요 차이점은 Azure Virtual Machines에 [부하 분산 장치](../../../load-balancer/load-balancer-overview.md)가 필요하다는 것입니다. 부하 분산 장치는 가용성 그룹 수신기의 IP 주소를 보유합니다. 가용성 그룹을 여러 개 있는 경우 그룹마다 수신기가 필요합니다. 하나의 부하 분산 장치가 여러 수신기를 지원할 수 있습니다.

또한 Azure IaaS VM 게스트 장애 조치 클러스터에서는 서버당 단일 NIC(클러스터 노드) 및 단일 서브넷을 권장합니다. Azure 네트워킹에는 Azure IaaS VM 게스트 클러스터에서 추가 NIC 및 서브넷을 불필요하게 만드는 물리적 중복성이 있습니다. 클러스터 유효성 검사 보고서는 노드가 단일 네트워크에서만 연결 가능하다는 경고를 표시하지만, Azure IaaS VM 게스트 장애 조치(failover) 클러스터에서는 이 경고를 무시해도 됩니다. 

중복성과 고가용성을 높이려면 SQL Server VM이 동일한 가용성 [집합에](virtual-machines-windows-portal-sql-availability-group-prereq.md#create-availability-sets)있거나 다른 [가용성 영역에](/azure/availability-zones/az-overview)있어야 합니다. 

|  | 윈도우 서버 버전 | SQL Server 버전 | SQL Server 버전 | WSFC 쿼럼 구성 | 다중 영역이 있는 DR | 멀티 서브넷 지원 | 기존 AD 지원 | 다중 영역 동일한 영역의 DR | AD 도메인이 없는 Dist-AG 지원 | 클러스터가 없는 Dist-AG 지원 |  
| :------ | :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----|
| [SQL VM CLI](virtual-machines-windows-sql-availability-group-cli.md) | 2016 | 2017 </br>2016   | Ent | 클라우드 감시 | 예 | yes | yes | yes | 예 | 예 |
| [빠른 시작 템플릿](virtual-machines-windows-sql-availability-group-quickstart-template.md) | 2016 | 2017</br>2016  | Ent | 클라우드 감시 | 예 | yes | yes | yes | 예 | 예 |
| [포털 템플릿](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) | 2016 </br>2012 R2 | 2016</br>2014 | Ent | 파일 공유 | 예 | 예 | 예 | 예 | 예 | 예 |
| [수동](virtual-machines-windows-portal-sql-availability-group-prereq.md) | 모두 | 모두 | 모두 | 모두 | yes | yes | yes | yes | yes | yes |
| &nbsp; | &nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |

Azure Virtual Machines에서 SQL Server 가용성을 빌드할 준비가 되면 다음 자습서를 참조하세요.

## <a name="manually-with-azure-cli"></a>Azure CLI를 수동으로 사용
Azure CLI를 사용하여 가용성 그룹을 구성하고 배포하는 것이 배포의 단순성과 속도 면에서 가장 좋은 선택이기 때문에 권장되는 옵션입니다. Azure CLI를 사용하면 Windows 장애 조치 클러스터를 만들고 SQL Server VM을 클러스터에 조인하고 수신기 및 내부 로드 밸런서를 만드는 데 30분 이내에 모두 수행할 수 있습니다. 이 옵션은 여전히 가용성 그룹을 수동으로 생성해야 하지만 다른 모든 필요한 구성 단계를 자동화합니다. 

자세한 내용은 [Azure SQL VM CLI 를 사용하여 Azure VM에서 SQL Server에 대한 항상 가용성 그룹을 구성합니다.](virtual-machines-windows-sql-availability-group-cli.md) 

## <a name="automatically-with-azure-quickstart-templates"></a>Azure 빠른 시작 템플릿을 사용하면 자동으로
Azure 빠른 시작 템플릿은 SQL VM 리소스 공급자를 사용하여 Windows 장애 조치 클러스터를 배포하고, SQL Server VM을 조인하고, 수신기를 만들고, 내부 로드 밸런서를 구성합니다. 이 옵션은 여전히 가용성 그룹과 ILB(내부 부하 분산)를 수동으로 생성해야 하지만 필요한 다른 모든 구성 단계(ILB 구성 포함)를 자동화하고 단순화합니다. 

자세한 내용은 [Azure 빠른 시작 템플릿 사용을 참조하여 Azure VM에서 SQL Server에 대한 항상 가용성 그룹을 구성합니다.](virtual-machines-windows-sql-availability-group-quickstart-template.md)


## <a name="automatically-with-an-azure-portal-template"></a>Azure 포털 템플릿을 사용하면 자동으로

[자동으로 Azure VM의 Always On 가용성 그룹 구성 - Resource Manager](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)


## <a name="manually-in-azure-portal"></a>Azure 포털에서 수동으로

또한 템플릿을 사용하지 않고 가상 머신을 직접 만들 수도 있습니다. 먼저 필수 구성 요소를 완료한 다음 가용성 그룹을 만듭니다. 다음 항목을 참조하세요. 

- [Azure Virtual Machines의 SQL Server Always On 가용성 그룹에 대한 필수 구성 요소 구성](virtual-machines-windows-portal-sql-availability-group-prereq.md)

- [Always On 가용성 그룹을 만들어 가용성 및 재해 복구 개선](virtual-machines-windows-portal-sql-availability-group-tutorial.md)

## <a name="next-steps"></a>다음 단계

[다른 지역의 Azure Virtual Machines에서 SQL Server Always On 가용성 그룹 구성](virtual-machines-windows-portal-sql-availability-group-dr.md)
