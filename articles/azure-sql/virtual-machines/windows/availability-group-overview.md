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
ms.openlocfilehash: 62dce0204f77ab65473fc1735015e41f483dddb1
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2020
ms.locfileid: "84036954"
---
# <a name="introducing-sql-server-availability-groups-on-azure-virtual-machines"></a>Azure 가상 머신의 SQL Server 가용성 그룹 소개
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

이 문서에서는 Azure Virtual Machines의 SQL Server 가용성 그룹을 소개합니다. 

Azure Virtual Machines의 Always On 가용성 그룹은 온-프레미스의 Always On 가용성 그룹과 비슷합니다. 자세한 내용은 [Always On 가용성 그룹(SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx)을 참조하세요. 

이 다이어그램에서는 Azure Virtual Machines의 전체 SQL Server 가용성 그룹 일부를 보여 줍니다.

![가용성 그룹](./media/availability-group-overview/00-EndstateSampleNoELB.png)

Azure Virtual Machines의 가용성 그룹에 대한 주요 차이점은 Azure Virtual Machines에 [부하 분산 장치](../../../load-balancer/load-balancer-overview.md)가 필요하다는 것입니다. 부하 분산 장치는 가용성 그룹 수신기의 IP 주소를 보유합니다. 가용성 그룹을 여러 개 있는 경우 그룹마다 수신기가 필요합니다. 하나의 부하 분산 장치가 여러 수신기를 지원할 수 있습니다.

또한 Azure IaaS VM 게스트 장애 조치(failover) 클러스터에서는 서버(클러스터 노드)당 단일 NIC 및 단일 서브넷이 권장됩니다. Azure 네트워킹에는 Azure IaaS VM 게스트 클러스터에서 추가 NIC 및 서브넷을 불필요하게 만드는 물리적 중복성이 있습니다. 클러스터 유효성 검사 보고서는 노드가 단일 네트워크에서만 연결 가능하다는 경고를 표시하지만, Azure IaaS VM 게스트 장애 조치(failover) 클러스터에서는 이 경고를 무시해도 됩니다. 

중복성 및 고가용성을 강화하려면 SQL Server VM이 동일한 [가용성 집합](availability-group-manually-configure-prerequisites-tutorial.md#create-availability-sets) 또는 다른 [가용성 영역](/azure/availability-zones/az-overview)에 있어야 합니다. 

|  | Windows Server 버전 | SQL Server 버전 | SQL Server 버전 | WSFC 쿼럼 구성 | 다중 지역 DR | 다중 서브넷 지원 | 기존 AD 지원 | 다중 영역 동일 지역 DR | AD 도메인 없는 배포 AG 지원 | 클러스터 없는 배포 AG 지원 |  
| :------ | :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----|
| [SQL VM CLI](availability-group-az-cli-configure.md) | 2016 | 2017 </br>2016   | Ent | 클라우드 감시 | 예 | 예 | 예 | 예 | 예 | 예 |
| [빠른 시작 템플릿](availability-group-quickstart-template-configure.md) | 2016 | 2017</br>2016  | Ent | 클라우드 감시 | 예 | 예 | 예 | 예 | 예 | 예 |
| [포털 템플릿](availability-group-azure-marketplace-template-configure.md) | 2016 </br>2012 R2 | 2016</br>2014 | Ent | 파일 공유 | 예 | 예 | 예 | 예 | 예 | 예 |
| [수동](availability-group-manually-configure-prerequisites-tutorial.md) | 모두 | 모두 | 모두 | 모두 | 예 | 예 | 예 | 예 | 예 | 예 |
| &nbsp; | &nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |

Azure Virtual Machines에서 SQL Server 가용성을 빌드할 준비가 되면 다음 자습서를 참조하세요.

## <a name="manually-with-azure-cli"></a>Azure CLI를 사용하여 수동으로
Azure CLI를 사용하여 가용성 그룹을 구성하고 배포하는 것이 좋습니다. 이 옵션은 배포 단순성 및 속도 측면에서 가장 좋습니다. Azure CLI에서 Windows 장애 조치(failover) 클러스터를 만들고, 클러스터에 SQL Server VM을 조인하고, 수신기 및 내부 부하 분산 장치를 만드는 작업을 30분 이내에 모두 마칠 수 있습니다. 이 옵션을 사용하려면 가용성 그룹을 수동으로 만들어야 하지만 다른 필요한 구성 단계를 모두 자동화합니다. 

자세한 내용은 [Azure SQL VM CLI를 사용하여 Azure VM의 SQL Server에 대한 Always On 가용성 그룹 구성](availability-group-az-cli-configure.md)을 참조하세요. 

## <a name="automatically-with-azure-quickstart-templates"></a>Azure 빠른 시작 템플릿을 사용하여 자동으로
Azure 빠른 시작 템플릿은 SQL VM 리소스 공급자를 사용하여 Windows 장애 조치(failover) 클러스터를 배포하고, 이 클러스터에 SQL Server VM을 조인하고, 수신기를 만들고, 내부 부하 분산 장치를 구성합니다. 이 옵션 역시 가용성 그룹과 ILB(내부 부하 분산 장치)를 수동으로 만들어야 하지만, 다른 필요한 구성 단계(ILB 구성 포함)를 모두 자동화하고 간소화합니다. 

자세한 내용은 [Azure 빠른 시작 템플릿을 사용하여 Azure VM에서 SQL Server에 대한 Always On 가용성 그룹 구성](availability-group-quickstart-template-configure.md)을 참조하세요.


## <a name="automatically-with-an-azure-portal-template"></a>Azure Portal 템플릿을 사용하여 자동으로

[자동으로 Azure VM의 Always On 가용성 그룹 구성 - Resource Manager](availability-group-azure-marketplace-template-configure.md)


## <a name="manually-in-azure-portal"></a>Azure Portal에서 수동으로

또한 템플릿을 사용하지 않고 가상 머신을 직접 만들 수도 있습니다. 먼저 필수 구성 요소를 완료한 다음 가용성 그룹을 만듭니다. 다음 항목을 참조하세요. 

- [Azure Virtual Machines의 SQL Server Always On 가용성 그룹에 대한 필수 구성 요소 구성](availability-group-manually-configure-prerequisites-tutorial.md)

- [Always On 가용성 그룹을 만들어 가용성 및 재해 복구 개선](availability-group-manually-configure-tutorial.md)

## <a name="next-steps"></a>다음 단계

[다른 지역의 Azure Virtual Machines에서 SQL Server Always On 가용성 그룹 구성](availability-group-manually-configure-multiple-regions.md)
