---
title: 고가용성, 재해 복구, 비즈니스 연속성
description: Azure Vm에서 Always On 가용성 그룹, 장애 조치 (failover) 클러스터 인스턴스, 데이터베이스 미러링, 로그 전달, 백업 & Azure Storage 복원 등의 SQL Server에 사용할 수 있는 고가용성, 재해 복구 (HADR) 및 비즈니스 연속성 옵션에 대해 알아봅니다.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: 53981f7e-8370-4979-b26a-93a5988d905f
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/27/2020
ms.author: mathoma
ms.openlocfilehash: cfc3abd30fad3e86544430e5a4ecb8510e77c9e5
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92789933"
---
# <a name="business-continuity-and-hadr-for-sql-server-on-azure-virtual-machines"></a>Azure Virtual Machines에서 SQL Server에 대 한 비즈니스 연속성 및 HADR
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

비즈니스 연속성은 재해 발생 시 비즈니스를 계속 하 고, 복구를 계획 하 고, 데이터를 항상 사용할 수 있도록 합니다. Azure Virtual Machines SQL Server은 고가용성 및 재해 복구 (HADR) 데이터베이스 솔루션의 비용을 낮출 수 있습니다. 

대부분의 SQL Server HADR 솔루션은 Azure 전용 및 하이브리드 솔루션으로 Vm (가상 머신)에서 지원 됩니다. Azure 전용 솔루션에서는 HADR 시스템 전체가 Azure에서 실행됩니다. 하이브리드 구성에서는 솔루션의 일부가 Azure에서 실행되고 나머지 부분은 조직의 온-프레미스에서 실행됩니다. Azure 환경은 유연하게 제공되므로 사용 중인 SQL Server 데이터베이스 시스템의 예산과 HADR 요구 사항에 맞게 Azure로 부분적으로만 이동하거나 완전히 이동할 수도 있습니다.

이 문서에서는 Azure Vm에서 SQL Server 하는 데 사용할 수 있는 비즈니스 연속성 솔루션을 비교 하 고 대조 합니다. 

## <a name="overview"></a>개요

데이터베이스 시스템에 SLA (서비스 수준 계약)에 필요한 HADR 기능이 있는지 확인 해야 합니다. Azure에서 클라우드 서비스에 대 한 서비스 복구 및 가상 컴퓨터에 대 한 오류 복구 검색과 같은 고가용성 메커니즘을 제공 한다는 사실은 SLA를 충족할 수 있다는 보장이 없습니다. 이러한 메커니즘은 가상 컴퓨터의 고가용성을 보호 하는 데 도움이 되지만 VM 내부에서 실행 되는 SQL Server의 가용성을 보호 하지 않습니다. 

VM이 온라인 상태이 고 정상 상태인 동안에는 SQL Server 인스턴스가 실패할 수 있습니다. Azure에서 제공 하는 고가용성 메커니즘 에서도 소프트웨어 또는 하드웨어 오류 복구 및 운영 체제 업그레이드와 같은 이벤트로 인 한 Vm 가동 중지 시간이 발생할 수 있습니다.

Azure의 GRS (지역 중복 저장소)는 지역에서 복제 라는 기능을 사용 하 여 구현 됩니다. GRS는 데이터베이스에 적합 한 재해 복구 솔루션이 아닐 수 있습니다. 지역에서 복제는 데이터를 비동기식으로 보내기 때문에 재해가 발생할 경우 최신 업데이트가 손실 될 수 있습니다. 지역에서 복제 제한 사항에 대 한 자세한 내용은 [지역에서 복제 지원](#geo-replication-support) 섹션에서 다룹니다.

## <a name="deployment-architectures"></a>배포 아키텍처
Azure는 비즈니스 연속성을 위해 다음과 같은 SQL Server 기술을 지원 합니다.

* [Always On 가용성 그룹](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server)
* [Always On 장애 조치 (failover) 클러스터 인스턴스 (FCIs)](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
* [로그 전달](/sql/database-engine/log-shipping/about-log-shipping-sql-server)
* [Azure Blob storage를 사용 하 여 백업 및 복원 SQL Server](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service)
* [데이터베이스 미러링](/sql/database-engine/database-mirroring/database-mirroring-sql-server) -SQL Server 2016에서 사용 되지 않음

이 기술을 결합 하 여 고가용성 및 재해 복구 기능을 모두 포함 하는 SQL Server 솔루션을 구현할 수 있습니다. 사용 하는 기술에 따라 하이브리드 배포에는 Azure 가상 네트워크와 함께 VPN 터널이 필요할 수 있습니다. 다음 섹션에서는 몇 가지 예제 배포 아키텍처를 보여 줍니다.

## <a name="azure-only-high-availability-solutions"></a>Azure 전용: 고가용성 솔루션

Always On 가용성 그룹를 사용 하 여 데이터베이스 수준에서 SQL Server에 대 한 고가용성 솔루션을 사용할 수 있습니다. Always On 장애 조치 (failover) 클러스터 인스턴스를 사용 하 여 인스턴스 수준에서 고가용성 솔루션을 만들 수도 있습니다. 추가 보호를 위해 장애 조치 (failover) 클러스터 인스턴스에 가용성 그룹을 만들어 두 수준 모두에서 중복을 만들 수 있습니다. 

| 기술 | 예제 아키텍처 |
| --- | --- |
| **가용성 그룹** |동일한 지역에 있는 Azure VM에서 실행되는 모든 가용성 복제본은 고가용성을 제공합니다. Windows 장애 조치 클러스터링에 Active Directory 도메인이 필요하기 때문에 도메인 컨트롤러 VM을 구성해야 합니다.<br/><br/> 중복성 및 가용성을 높이기 위해 [가용성 그룹 개요](availability-group-overview.md)에 설명 된 대로 Azure vm을 다른 [가용성 영역](../../../availability-zones/az-overview.md) 에 배포할 수 있습니다. 가용성 그룹의 SQL Server Vm이 가용성 영역에 배포 된 경우 [AZURE SQL VM CLI](./availability-group-az-commandline-configure.md) 및 [azure 퀵 스타트 템플릿](availability-group-quickstart-template-configure.md) 문서에 설명 된 대로 수신기에 대해 [azure 표준 Load Balancer](../../../load-balancer/load-balancer-overview.md) 를 사용 합니다.<br/> ![가용성 그룹](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-ha-always-on.png)<br/>자세한 내용은 [Azure에서 가용성 그룹 구성(GUI)](./availability-group-quickstart-template-configure.md)을 참조하세요. |
| **장애 조치(Failover) 클러스터 인스턴스** |장애 조치 (Failover) 클러스터 인스턴스는 SQL Server Vm에서 지원 됩니다. FCI 기능에는 공유 저장소가 필요 하기 때문에 Azure Vm에서 5 개의 솔루션이 SQL Server와 함께 작동 합니다. <br/><br/> -Windows Server 2019에 대 한 [Azure 공유 디스크](failover-cluster-instance-azure-shared-disks-manually-configure.md) 사용. 공유 관리 디스크는 여러 가상 컴퓨터에 관리 디스크를 동시에 연결할 수 있도록 하는 Azure 제품입니다. 클러스터의 Vm은 scsi PR (SCSI 영구 예약)을 통해 클러스터 된 응용 프로그램에서 선택한 예약에 따라 연결 된 디스크를 읽거나 쓸 수 있습니다. SCSI PR은 SAN (저장 영역 네트워크) 온-프레미스에서 실행 되는 응용 프로그램에서 사용 되는 업계 표준 저장소 솔루션입니다. 관리 디스크에서 SCSI PR을 사용 하도록 설정 하면 이러한 응용 프로그램을 Azure로 마이그레이션할 수 있습니다. <br/><br/>- [스토리지 공간 다이렉트 \( S2D \) ](failover-cluster-instance-storage-spaces-direct-manually-configure.md) 를 사용 하 여 Windows Server 2016 이상에 대 한 소프트웨어 기반 가상 SAN을 제공 합니다.<br/><br/>-Windows Server 2012 이상에 [프리미엄 파일 공유](failover-cluster-instance-premium-file-share-manually-configure.md) 를 사용 합니다. 프리미엄 파일 공유는 SSD를 지원 하 고, 지속적으로 짧은 대기 시간을 사용 하며, FCI와 함께 사용 하기 위해 완벽 하 게 지원 됩니다.<br/><br/>-클러스터링을 위해 파트너 솔루션에서 지 원하는 저장소 사용. SIOS DataKeeper를 사용 하는 특정 예제는 블로그 항목 [장애 조치 (Failover) 클러스터링 및 SIOS DataKeeper](https://azure.microsoft.com/blog/high-availability-for-a-file-share-using-wsfc-ilb-and-3rd-party-software-sios-datakeeper/)를 참조 하세요.<br/><br/>-Azure Express 경로를 통해 원격 iSCSI 대상에 공유 블록 저장소를 사용 합니다. 예를 들어 NPS(NetApp 프라이빗 스토리지)는 Equinix와 함께 ExpressRoute를 사용하여 iSCSI 대상을 Azure VM에 공개합니다.<br/><br/>Microsoft 파트너의 공유 스토리지 및 데이터 복제 솔루션의 경우 장애 조치 시 데이터 액세스와 관련된 문제는 공급 업체에 문의하십시오.<br/><br/>||

## <a name="azure-only-disaster-recovery-solutions"></a>Azure에만 해당: 재해 복구 솔루션
가용성 그룹, 데이터베이스 미러링 또는 저장소 blob을 사용한 백업 및 복원을 사용 하 여 Azure에 SQL Server 데이터베이스에 대 한 재해 복구 솔루션을 만들 수 있습니다.

| 기술 | 예제 아키텍처 |
| --- | --- |
| **가용성 그룹** |재해 복구를 위해 가용성 복제본을 Azure VM의 여러 데이터 센터에서 실행합니다. 이 지역 간 솔루션은 전체 사이트 중단 으로부터 보호 하는 데 도움이 됩니다. <br/> ![가용성 그룹](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-dr-alwayson.png)<br/>한 지역 내에서 모든 복제본은 동일한 클라우드 서비스 및 동일한 가상 네트워크에 있어야 합니다. 각 지역에 별도의 가상 네트워크가 있으므로 이러한 솔루션은 네트워크 간 연결을 필요로 합니다. 자세한 내용은 [Azure Portal를 사용 하 여 네트워크 간 연결 구성](../../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)을 참조 하세요. 자세한 지침은 [여러 Azure 지역에서 SQL Server Always On 가용성 그룹 구성](availability-group-manually-configure-multiple-regions.md)을 참조 하세요.|
| **데이터베이스 미러링** |재해 복구를 위해 주 서버와 미러 서버를 다른 데이터 센터에서 실행합니다. 서버 인증서를 사용 하 여 배포 해야 합니다. Azure VM의 SQL Server 2008 또는 SQL Server 2008 r 2에 대해서는 SQL Server 데이터베이스 미러링이 지원 되지 않습니다. <br/>![데이터베이스 미러링](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-dr-dbmirroring.png) |
| **Azure Blob storage를 사용 하 여 백업 및 복원** |프로덕션 데이터베이스는 재해 복구를 위해 다른 데이터 센터의 Blob 저장소에 직접 백업 됩니다.<br/>![Backup 및 복원](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-dr-backup-restore.png)<br/>자세한 내용은 [Azure vm에서 SQL Server에 대 한 백업 및 복원](../../../azure-sql/virtual-machines/windows/backup-restore.md)을 참조 하세요. |
| **Azure Site Recovery를 사용 하 여 Azure에 SQL Server 복제 및 장애 조치 (failover)** |재해 복구를 위해 다른 Azure 데이터 센터의 Azure Storage에 직접 복제 된 단일 Azure 데이터 센터의 프로덕션 SQL Server 인스턴스입니다.<br/>![Azure Site Recovery를 사용하여 복제](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-dr-standalone-sqlserver-asr.png)<br/>자세한 내용은 [SQL Server 재해 복구 및 Azure Site Recovery를 사용하여 SQL Server 보호](../../../site-recovery/site-recovery-sql.md)를 참조하세요. |


## <a name="hybrid-it-disaster-recovery-solutions"></a>하이브리드 IT: 재해 복구 솔루션
가용성 그룹, 데이터베이스 미러링, 로그 전달, Azure Blob 저장소를 사용한 백업 및 복원을 사용 하 여 하이브리드 IT 환경에 SQL Server 데이터베이스에 대 한 재해 복구 솔루션을 제공할 수 있습니다.

| 기술 | 아키텍처의 예 |
| --- | --- |
| **가용성 그룹** |사이트간 재해 복구를 위해 일부 가용성 복제본은 Azure VM에서 실행되고 다른 복제본은 온-프레미스에서 실행됩니다. 프로덕션 사이트는 온-프레미스와 Azure 데이터 센터 어디에도 있을 수 있습니다.<br/>![가용성 그룹](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-alwayson.png)<br/>모든 가용성 복제본이 동일한 장애 조치 클러스터에 있어야 하므로, 장애 조치 클러스터는 두 네트워크(다중 서브넷 장애 조치 클러스터)에 걸쳐 있어야 합니다. 이 구성은 Azure와 온-프레미스 네트워크 간의 VPN 연결이 필요합니다.<br/><br/>데이터베이스의 성공적인 재해 복구를 위해서는 재해 복구 사이트에 복제 도메인 컨트롤러도 설치해야 합니다.|
| **데이터베이스 미러링** |서버 인증서를 사용 하 여 사이트 간 재해 복구를 위해 한 파트너가 Azure VM에서 실행 되 고 다른 파트너가 온-프레미스로 실행 됩니다. 파트너가 동일한 Active Directory 도메인에 있을 필요는 없으며 VPN 연결이 필요 하지 않습니다.<br/>![데이터베이스 미러링](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-dbmirroring.png)<br/>또 다른 데이터베이스 미러링 시나리오로는 사이트 간 재해 복구를 위해 한 파트너가 Azure VM에서 실행되고 다른 파트너가 동일한 Active Directory 도메인의 온-프레미스에서 실행되는 경우를 들 수 있습니다. [Azure 가상 네트워크와 온-프레미스 네트워크 간의 VPN 연결](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)이 필요합니다.<br/><br/>데이터베이스의 성공적인 재해 복구를 위해서는 재해 복구 사이트에 복제 도메인 컨트롤러도 설치해야 합니다. Azure VM의 SQL Server 2008 또는 SQL Server 2008 r 2에 대해서는 SQL Server 데이터베이스 미러링이 지원 되지 않습니다. |
| **로그 전달** |사이트 간 재해 복구를 위해 한 서버는 Azure VM에서 실행하고 다른 서버는 온-프레미스에서 실행합니다. 로그 전달은 Windows 파일 공유를 사용하므로 Azure 가상 네트워크와 온-프레미스 네트워크 간의 VPN 연결이 필요합니다.<br/>![로그 전달](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-log-shipping.png)<br/>데이터베이스의 성공적인 재해 복구를 위해서는 재해 복구 사이트에 복제 도메인 컨트롤러도 설치해야 합니다. |
| **Azure Blob storage를 사용 하 여 백업 및 복원** |재해 복구를 위해 온-프레미스 프로덕션 데이터베이스를 Azure Blob 저장소에 직접 백업 합니다.<br/>![Backup 및 복원](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-backup-restore.png)<br/>자세한 내용은 [Azure Virtual Machines에서 SQL Server에 대 한 백업 및 복원](../../../azure-sql/virtual-machines/windows/backup-restore.md)을 참조 하세요. |
| **Azure Site Recovery를 사용 하 여 Azure에 SQL Server 복제 및 장애 조치 (failover)** |재해 복구를 위해 온-프레미스 프로덕션 SQL Server 인스턴스가 Azure Storage에 직접 복제 되었습니다.<br/>![Azure Site Recovery를 사용하여 복제](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-standalone-sqlserver-asr.png)<br/>자세한 내용은 [SQL Server 재해 복구 및 Azure Site Recovery를 사용하여 SQL Server 보호](../../../site-recovery/site-recovery-sql.md)를 참조하세요. |


## <a name="free-dr-replica-in-azure"></a>Azure의 무료 DR 복제본

[소프트웨어 보증이](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot:primaryr3)있는 경우 수동 재해 복구 인스턴스에 대 한 추가 라이선스 비용 없이 SQL Server를 사용 하 여 DR (하이브리드 재해 복구) 계획을 구현할 수 있습니다.

다음 이미지에서 설치 프로그램은 12 개 코어를 사용 하는 온-프레미스 SQL Server 배포에 대 한 재해 복구 복제본으로 12 개 코어를 사용 하는 Azure 가상 머신에서 실행 되는 SQL Server을 사용 합니다. 과거에는 온-프레미스 배포 및 Azure Virtual Machines 배포에 대 한 SQL Server의 12 개 코어에 대 한 라이선스가 필요 합니다. 새 혜택은 Azure 가상 머신에서 실행 하기 위한 수동 복제본 혜택을 제공 합니다. 이제 Azure Virtual Machines의 수동 복제본에 대 한 재해 복구 기준이 충족 되는 한, 온-프레미스에서 실행 되는 SQL Server의 12 개 코어만 라이선스를 받아야 합니다.

![Azure에서 무료 재해 복구 복제본](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/free-dr-replica-azure.png)

자세한 내용은 [제품 라이선스 약관](https://www.microsoft.com/licensing/product-licensing/products)을 참조하세요. 

이 혜택을 사용 하려면 [SQL Server 가상 컴퓨터 리소스로](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource)이동 하세요. **설정** 아래에서 **구성** 을 선택 하 고 **SQL Server 라이선스** 에서 **재해 복구** 옵션을 선택 합니다. 확인란을 선택 하 여이 SQL Server VM이 수동 복제본으로 사용 됨을 확인 한 다음 **적용** 을 선택 하 여 설정을 저장 합니다. 

![Azure에서 재해 복구 복제본 구성](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/dr-replica-in-portal.png)


## <a name="important-considerations-for-sql-server-hadr-in-azure"></a>Azure에서 SQL Server HADR에 대한 중요 고려 사항
Azure VM, 스토리지 및 네트워킹은 온-프레미스, 가상화되지 않은 IT 인프라에서는 작동 특성이 달라집니다. Azure에서 HADR SQL Server 솔루션을 성공적으로 구현 하려면 이러한 차이점을 이해 하 고이에 맞게 솔루션을 설계 해야 합니다.

### <a name="high-availability-nodes-in-an-availability-set"></a>가용성 집합의 고가용성 노드
Azure의 가용성 집합을 사용 하면 고가용성 노드를 별도의 장애 도메인에 추가 하 고 도메인을 업데이트할 수 있습니다. Azure 플랫폼은 가용성 집합의 각 가상 머신에 업데이트 도메인 및 장애 도메인을 할당 합니다. 데이터 센터 내에서이 구성은 계획 된 유지 관리 또는 계획 되지 않은 유지 관리 이벤트 중에 하나 이상의 가상 머신을 사용할 수 있고 Azure SLA 99.95%를 충족 하도록 합니다. 

고가용성 설치를 구성 하려면 유지 관리 이벤트 중에 응용 프로그램이 나 데이터 손실을 방지 하기 위해 모든 참여 하는 SQL Server 가상 컴퓨터를 동일한 가용성 집합에 저장 합니다. 같은 클라우드 서비스에 있는 노드만 같은 가용성 집합에 참여할 수 있습니다. 자세한 내용은 [가상 머신의 가용성 관리](../../../virtual-machines/manage-availability.md?toc=%252fazure%252fvirtual-machines%252fwindows%252ftoc.json)를 참조하세요.

### <a name="high-availability-nodes-in-an-availability-zone"></a>가용성 영역의 고가용성 노드
가용성 영역은 Azure 지역 내의 고유한 물리적 위치입니다. 각 영역은 독립된 전원, 냉각 및 네트워킹을 갖춘 하나 이상의 데이터 센터로 구성됩니다. 지역 내에서 가용성 영역을 물리적으로 분리 하면 하나 이상의 가상 머신을 사용할 수 있고 Azure SLA 99.99%를 충족 하 여 데이터 센터 오류 로부터 응용 프로그램 및 데이터를 보호할 수 있습니다. 

고가용성을 구성 하려면 지역에서 가용성 영역에 걸쳐 분산 된 가상 컴퓨터 SQL Server 참여 합니다. 가용성 영역 간의 네트워크-네트워크 전송에 대 한 추가 요금이 부과 됩니다. 자세한 내용은 [가용성 영역](../../../availability-zones/az-overview.md)을 참조하세요. 


### <a name="failover-cluster-behavior-in-azure-networking"></a>Azure 네트워킹에서 장애 조치 클러스터의 동작
Azure에서 RFC 규격이 아닌 DHCP 서비스를 사용할 경우 특정 장애 조치 (failover) 클러스터 구성을 만들 수 없게 됩니다. 이 오류는 클러스터 네트워크 이름에 클러스터 노드 중 하 나와 같은 IP 주소와 같은 중복 IP 주소가 할당 되기 때문에 발생 합니다. Windows 장애 조치 (failover) 클러스터 기능에 따라 달라 지는 가용성 그룹을 사용 하는 경우 문제가 발생 합니다.

노드가 2개인 클러스터를 만들고 온라인 상태로 만드는 상황을 고려해 보십시오.

1. 클러스터가 온라인 상태가 되 면 NODE1은 클러스터 네트워크 이름에 대해 동적으로 할당 된 IP 주소를 요청 합니다.
2. Dhcp 서비스는 요청이 NODE1 자체에서 제공 된다는 것을 인식 하기 때문에 DHCP 서비스는 1의 자체 IP 주소 이외의 IP 주소를 제공 하지 않습니다.
3. Windows에서는 중복 된 주소가 노드 1과 장애 조치 (failover) 클러스터의 네트워크 이름에 할당 되 고 기본 클러스터 그룹이 온라인 상태가 되지 않음을 감지 합니다.
4. 기본 클러스터 그룹은 노드 2로 이동 합니다. 노드 2는 1의 IP 주소를 클러스터 IP 주소로 처리 하 고 기본 클러스터 그룹을 온라인 상태로 만듭니다.
5. 노드 2가 NODE1과의 연결을 설정 하려고 시도 하는 경우에는 1의 IP 주소를 자기 자신으로 확인 하기 때문에 NODE1에서 지정 된 패킷이 노드 2를 벗어날 노드 2는 NODE1과의 연결을 설정한 다음 쿼럼을 손실 하 고 클러스터를 종료할 수 없습니다.
6. NODE1은 1에서 2로 패킷을 보낼 수 있지만, 노드 2는 응답할 수 없습니다. NODE1도 쿼럼을 잃고 클러스터를 닫습니다.

클러스터 네트워크 이름을 온라인 상태로 전환 하기 위해 사용 하지 않는 고정 IP 주소를 클러스터 네트워크 이름에 할당 하 여이 시나리오를 방지할 수 있습니다. 예를 들어 (169.254.1.1과와 같은 링크-로컬 IP 주소를 사용할 수 있습니다. 이 절차를 간단히 수행하려면 [Azure에서 가용성 그룹에 Windows 장애 조치 클러스터 구성](https://social.technet.microsoft.com/wiki/contents/articles/14776.configuring-windows-failover-cluster-in-windows-azure-for-alwayson-availability-groups.aspx)을 참조하세요.

자세한 내용은 [Azure에서 가용성 그룹 구성(GUI)](./availability-group-quickstart-template-configure.md)을 참조하세요.

### <a name="support-for-availability-group-listeners"></a>가용성 그룹 수신기에 대 한 지원
가용성 그룹 수신기는 Windows Server 2012 이상을 실행 하는 Azure Vm에서 지원 됩니다. 가용성 그룹 노드인 Azure VM에서 부하 분산된 엔드포인트를 사용하도록 설정하면 이러한 지원이 가능해집니다. Azure에서 실행 되는 클라이언트 응용 프로그램과 온-프레미스에서 실행 되는 클라이언트 응용 프로그램 모두에 대해 작업 하려면 수신기에 대 한 특수 구성 단계를 따라야 합니다.

수신기를 설정하는 두 가지 주요 옵션(외부(공용) 및 내부)이 있습니다. 외부 (공용) 수신기는 인터넷 연결 부하 분산 장치를 사용 하며 인터넷을 통해 액세스할 수 있는 공용 가상 IP에 연결 됩니다. 내부 수신기는 내부 부하 분산 장치를 사용 하며 동일한 가상 네트워크 내에서 클라이언트만 지원 합니다. 각 부하 분산 장치 유형에 대해 Direct Server Return (DSR)을 설정해야 합니다. 

가용성 그룹이 여러 Azure 서브넷에 걸쳐 있는 경우 (예: Azure 지역을 교차 하는 배포) 클라이언트 연결 문자열에가 포함 되어야 합니다 `MultisubnetFailover=True` . 이렇게 하면 다른 서브넷에 있는 복제본에 대해 병렬 연결을 시도하게 됩니다. 수신기를 설정 하는 방법에 대 한 지침은 [Azure에서 가용성 그룹에 대 한 ILB 수신기 구성](availability-group-listener-powershell-configure.md)을 참조 하세요.


서비스 인스턴스에 직접 연결하면 각 가용성 복제본에 개별적으로 연결할 수 있습니다. 또한 가용성 그룹은 데이터베이스 미러링 클라이언트와 이전 버전과 호환 되므로 복제본이 데이터베이스 미러링과 유사 하 게 구성 되어 있는 한 데이터베이스 미러링 파트너와 같은 가용성 복제본에 연결할 수 있습니다.

* 하나의 주 복제본과 하나의 보조 복제본이 있습니다.
* 보조 복제본이 읽을 수 없는 것으로 구성 됩니다 (읽을 수 있는 **보조** 옵션은 **아니요** 로 설정 됨).

ADO.NET 또는 SQL Server Native Client를 사용 하 여이 데이터베이스 미러링 같은 구성에 해당 하는 클라이언트 연결 문자열의 예는 다음과 같습니다.

```console
Data Source=ReplicaServer1;Failover Partner=ReplicaServer2;Initial Catalog=AvailabilityDatabase;
```

클라이언트 연결에 대한 자세한 내용은 다음을 참조하세요.

* [SQL Server Native Client에서 연결 문자열 키워드 사용](/sql/relational-databases/native-client/applications/using-connection-string-keywords-with-sql-server-native-client)
* [데이터베이스 미러링 세션(SQL Server)에 클라이언트 연결](/sql/database-engine/database-mirroring/connect-clients-to-a-database-mirroring-session-sql-server)
* [하이브리드 IT 환경에서 가용성 그룹 수신기에 연결](/archive/blogs/sqlalwayson/connecting-to-availability-group-listener-in-hybrid-it)
* [가용성 그룹 수신기, 클라이언트 연결 및 애플리케이션 장애 조치(failover)(SQL Server)](/sql/database-engine/availability-groups/windows/listeners-client-connectivity-application-failover)
* [가용성 그룹과 데이터베이스 미러링 연결 문자열 사용](/sql/database-engine/availability-groups/windows/listeners-client-connectivity-application-failover)

### <a name="network-latency-in-hybrid-it"></a>하이브리드 IT 환경의 네트워크 대기 시간
온-프레미스 네트워크와 Azure 간의 네트워크 대기 시간이 길어질 수 있다고 가정 하 고 HADR 솔루션을 배포 합니다. Azure에 복제본을 배포 하는 경우 동기화 모드에 대해 동기 커밋 대신 비동기 커밋을 사용 합니다. 온-프레미스와 Azure 모두에 데이터베이스 미러링 서버를 배포 하는 경우 보안 우선 모드 대신 성능 우선 모드를 사용 합니다.

### <a name="geo-replication-support"></a>지역에서 복제 지원
Azure 디스크의 지역 복제는 동일한 데이터베이스의 로그 파일과 데이터 파일을 별도의 디스크로 저장하는 것을 지원하지 않습니다. GRS는 변경 내용을 독립적이고 비동기적으로 각 디스크에 복제합니다. 이 메커니즘은 지리적으로 복제된 복사본에서 쓰기 순서가 지켜지도록 보장하지만 여러 디스크에 지역 복제된 복사본에서는 보장하지 않습니다. 데이터 파일 및 로그 파일을 별도의 디스크에 저장 하도록 데이터베이스를 구성 하는 경우 재해 후 복구 된 디스크에는 로그 파일 보다 더 많은 데이터 파일 복사본이 포함 될 수 있으며,이로 인해 SQL Server의 미리 쓰기 로그와 트랜잭션의 ACID 속성 (원자성, 일관성, 격리 및 내구성)이 중단 됩니다. 

저장소 계정에서 지역에서 복제를 사용 하지 않도록 설정할 수 있는 옵션이 없는 경우 데이터베이스에 대 한 모든 데이터 및 로그 파일을 동일한 디스크에 보관 합니다. 데이터베이스의 크기 때문에 디스크를 두 개 이상 사용 해야 하는 경우 데이터 중복성을 위해 앞에 나열 된 재해 복구 솔루션 중 하나를 배포 합니다.

## <a name="next-steps"></a>다음 단계

[가용성 그룹](availability-group-overview.md) 또는 [장애 조치 (failover) 클러스터 인스턴스가](failover-cluster-instance-overview.md) 비즈니스에 가장 적합 한 비즈니스 연속성 솔루션 인지 결정 합니다. 그런 다음 고가용성 및 재해 복구를 위한 환경을 구성 하기 위한 [모범 사례](hadr-cluster-best-practices.md) 를 검토 합니다.