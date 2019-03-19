---
title: SQL Server 가용성 그룹 - Azure Virtual Machines - 개요 | Microsoft Docs
description: 이 문서에서는 Azure Virtual Machines의 SQL Server 가용성 그룹을 소개합니다.
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 601eebb1-fc2c-4f5b-9c05-0e6ffd0e5334
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/13/2017
ms.author: mikeray
ms.openlocfilehash: b9977965dc076ec36aa90680a1732b6640b1e41a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57861845"
---
# <a name="introducing-sql-server-always-on-availability-groups-on-azure-virtual-machines"></a>Azure Virtual Machines의 SQL Server Always On 가용성 그룹 소개 #

이 문서에서는 Azure Virtual Machines의 SQL Server 가용성 그룹을 소개합니다. 

Azure Virtual Machines의 Always On 가용성 그룹은 온-프레미스의 Always On 가용성 그룹과 비슷합니다. 자세한 내용은 [Always On 가용성 그룹(SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx)을 참조하세요. 

이 다이어그램에서는 Azure Virtual Machines의 전체 SQL Server 가용성 그룹 일부를 보여 줍니다.

![가용성 그룹](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

Azure Virtual Machines의 가용성 그룹에 대한 주요 차이점은 Azure Virtual Machines에 [부하 분산 장치](../../../load-balancer/load-balancer-overview.md)가 필요하다는 것입니다. 부하 분산 장치는 가용성 그룹 수신기의 IP 주소를 보유합니다. 가용성 그룹을 여러 개 있는 경우 그룹마다 수신기가 필요합니다. 하나의 부하 분산 장치가 여러 수신기를 지원할 수 있습니다.

또한 Azure IaaS VM 게스트 장애 조치 클러스터를 좋습니다 서버 (클러스터 노드) 당 단일 NIC 및 단일 서브넷. Azure 네트워킹에는 Azure IaaS VM 게스트 클러스터에서 추가 NIC 및 서브넷을 불필요하게 만드는 물리적 중복성이 있습니다. 클러스터 유효성 검사 보고서는 노드가 단일 네트워크에서만 연결 가능하다는 경고를 표시하지만, Azure IaaS VM 게스트 장애 조치(failover) 클러스터에서는 이 경고를 무시해도 됩니다. 

|  | Windows Server 버전 | SQL Server 버전 | SQL Server 버전 | WSFC 쿼럼 구성 | 다중 하위 지역으로 DR | 다중 서브넷 지원 | 기존 AD에 대 한 지원 | 다중 영역 동일한 지역과 DR | AD 도메인을 사용 하 여 배포-AG 지원 | 사용 가능한 클러스터를 사용 하 여 배포-AG 지원 |  
| :------ | :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----|
| [SQL VM CLI](virtual-machines-windows-sql-availability-group-cli.md) | 2016 | 2017 </br>2016   | Ent | 클라우드 감시 | 아닙니다. | 예 | 예 | 예 | 아니오 | 아닙니다. |
| [빠른 시작 템플릿](virtual-machines-windows-sql-availability-group-quickstart-template.md) | 2016 | 2017</br>2016  | Ent | 클라우드 감시 | 아닙니다. | 예 | 예 | 예 | 아니오 | 아닙니다. |
| [포털 템플릿](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) | 2016 </br>2012 R2 | 2016</br>2014 | Ent | 파일 공유 | 아닙니다. | 아니요 | 아니요 | 아니요 | 아니요 | 아닙니다. |
| [수동](virtual-machines-windows-portal-sql-availability-group-prereq.md) | 모두 | 모두 | 모두 | 모두 | 예 | 예 | 예 | 예 | 예 | 예 |
| &nbsp; | &nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |

Azure Virtual Machines에서 SQL Server 가용성을 빌드할 준비가 되면 다음 자습서를 참조하세요.

## <a name="manually-with-azure-cli"></a>Azure CLI를 사용 하 여 수동으로
Azure CLI를 사용 하 여 구성 하 고 가용성 그룹 배포 이므로 권장 되는 옵션을 간단 하 고 배포 속도 기준으로 가장 적합 합니다. Azure CLI, 클러스터에 SQL Server Vm 가입 Windows 장애 조치 클러스터를 만들 뿐만 아니라 수신기 및 내부 부하 분산 장치 만들기를 사용 하 여 모두 구현할 수 있습니다 30 분 이내입니다. 이 옵션은 여전히 수동 가용성 그룹을 만들어야 하지만 다른 모든 필요한 구성 단계를 자동화. 

자세한 내용은 [SQL Server Azure VM에 대 한 Always On 가용성 그룹을 구성 하려면 사용 하 여 Azure SQL VM CLI](virtual-machines-windows-sql-availability-group-cli.md)합니다. 

## <a name="automatically-with-azure-quickstart-templates"></a>Azure 빠른 시작 템플릿을 사용 하 여 자동으로
Azure 빠른 시작 템플릿은 Windows 장애 조치 클러스터를 배포, SQL Server Vm 가입, 수신기를 만들면 및 내부 Load Balancer를 구성 하는 SQL VM 리소스 공급자를 활용 합니다. 이 옵션 여전히 내부 부하 분산 장치 (ILB) 및 가용성 그룹의 수동 만들어야 하지만 자동화 하 고 다른 필요한 구성 단계를 모두 ILB 구성 등을 간소화 합니다. 

자세한 내용은 [SQL Server Azure VM에 대 한 Always On 가용성 그룹을 구성 하려면 Azure 빠른 시작 템플릿을 사용 하 여](virtual-machines-windows-sql-availability-group-quickstart-template.md)입니다.


## <a name="automatically-with-an-azure-portal-template"></a>Azure 포털 템플릿을 사용 하 여 자동으로

[자동으로 Azure VM의 Always On 가용성 그룹 구성 - Resource Manager](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)


## <a name="manually-in-azure-portal"></a>Azure portal에서 수동으로

또한 템플릿을 사용하지 않고 가상 머신을 직접 만들 수도 있습니다. 먼저 필수 구성 요소를 완료한 다음 가용성 그룹을 만듭니다. 또한 다음 항목을 참조하세요. 

- [Azure Virtual Machines의 SQL Server Always On 가용성 그룹에 대한 필수 구성 요소 구성](virtual-machines-windows-portal-sql-availability-group-prereq.md)

- [Always On 가용성 그룹을 만들어 가용성 및 재해 복구 개선](virtual-machines-windows-portal-sql-availability-group-tutorial.md)

## <a name="next-steps"></a>다음 단계

[다른 지역의 Azure Virtual Machines에서 SQL Server Always On 가용성 그룹 구성](virtual-machines-windows-portal-sql-availability-group-dr.md)
