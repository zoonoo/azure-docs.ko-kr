---
title: FCI에 대 한 가상 머신 준비
description: FCI (장애 조치 (failover) 클러스터 인스턴스)를 사용 하 고 Azure Virtual Machines에서 SQL Server Azure virtual machines를 사용 하도록 준비 합니다.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/02/2020
ms.author: mathoma
ms.openlocfilehash: 901c090d26959950d0ffd6a96253bdc36c9331c5
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94556338"
---
# <a name="prepare-virtual-machines-for-an-fci-sql-server-on-azure-vms"></a>FCI (Azure Vm에 SQL Server)에 대 한 가상 머신 준비
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

이 문서에서는 SQL Server 장애 조치 (failover) 클러스터 인스턴스 (FCI)를 사용 하 여 Azure Vm (가상 머신)을 사용 하도록 준비 하는 방법을 설명 합니다. 구성 설정은 FCI 저장소 솔루션에 따라 달라 지므로 사용자 환경 및 비즈니스에 적합 한 구성을 선택 하 고 있는지 확인 합니다. 

자세한 내용은 [Azure vm의 SQL Server를 사용 하는 Fci](failover-cluster-instance-overview.md) 개요 및 [클러스터 모범 사례](hadr-cluster-best-practices.md)를 참조 하세요. 

## <a name="prerequisites"></a>필수 구성 요소 

- Microsoft Azure 구독. [무료로](https://azure.microsoft.com/free/)시작 하세요. 
- Azure virtual machines의 Windows 도메인 또는 가상 네트워크 페어링을 사용 하 여 Azure로 확장 된 온-프레미스 데이터 센터.
- Azure 가상 컴퓨터와 Active Directory에서 개체를 만들 수 있는 권한이 있는 계정입니다.
- 다음 구성 요소를 위해 충분한 IP 주소 공간이 있는 Azure 가상 네트워크 및 서브넷:
   - 두 가상 머신
   - Windows 장애 조치 (failover) 클러스터의 IP 주소
   - 각 FCI에 대 한 IP 주소
- 도메인 컨트롤러를 가리키는 Azure 네트워크에 구성된 DNS

## <a name="choose-an-fci-storage-option"></a>FCI 저장소 옵션 선택

가상 컴퓨터의 구성 설정은 SQL Server 장애 조치 (failover) 클러스터 인스턴스에 사용 하려는 저장소 옵션에 따라 달라 집니다. 가상 컴퓨터를 준비 하기 전에 [사용 가능한 FCI 저장소 옵션](failover-cluster-instance-overview.md#storage) 을 검토 하 고 사용자 환경 및 비즈니스 요구에 가장 적합 한 옵션을 선택 합니다. 그런 다음 저장소 선택에 따라이 문서 전체에서 적절 한 VM 구성 옵션을 신중 하 게 선택 합니다. 

## <a name="configure-vm-availability"></a>VM 가용성 구성 

장애 조치 (failover) 클러스터 기능을 사용 하려면 가상 컴퓨터를 [가용성 집합](../../../virtual-machines/linux/tutorial-availability-sets.md) 또는 [가용성 영역](../../../availability-zones/az-overview.md#availability-zones)에 배치 해야 합니다. 가용성 집합을 선택 하는 경우 [근접 배치 그룹](../../../virtual-machines/windows/co-location.md#proximity-placement-groups) 을 사용 하 여 vm을 더 가까이 배치할 수 있습니다. 실제로 근접 배치 그룹은 Azure 공유 디스크를 사용 하기 위한 필수 구성 요소입니다. 

원하는 클러스터 구성과 일치 하는 VM 가용성 옵션을 신중 하 게 선택 합니다. 

 - **Azure 공유 디스크** : 장애 도메인 및 업데이트 도메인을 1로 설정 하 고 [근접 배치 그룹](../../../virtual-machines/windows/proximity-placement-groups-portal.md)내에 배치 하 여 [가용성 집합](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set) 을 구성 합니다.
 - **프리미엄 파일 공유** : [가용성 집합](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set) 또는 [가용성 영역](../../../virtual-machines/windows/create-portal-availability-zone.md#confirm-zone-for-managed-disk-and-ip-address). 가용성 영역을 Vm에 대 한 가용성 구성으로 선택 하는 경우 프리미엄 파일 공유는 유일한 공유 저장소 옵션입니다. 
 - **스토리지 공간 다이렉트** : [가용성 집합](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set)입니다.

>[!IMPORTANT]
>가상 머신을 만든 후에는 가용성 집합을 설정하거나 변경할 수 없습니다.

## <a name="create-the-virtual-machines"></a>가상 머신 만들기

VM 가용성을 구성한 후에는 가상 머신을 만들 준비가 되었습니다. 이미 설치 되어 SQL Server 있거나 설치 되어 있지 않은 Azure Marketplace 이미지를 사용 하도록 선택할 수 있습니다. 그러나 Azure Vm에서 SQL Server 이미지를 선택 하는 경우 장애 조치 (failover) 클러스터 인스턴스를 구성 하기 전에 가상 머신에서 SQL Server을 제거 해야 합니다. 


두 가상 머신을 다음에 배치합니다.

- 가용성 집합을 사용 하는 경우 가용성 집합과 동일한 Azure 리소스 그룹에 있습니다.
- 도메인 컨트롤러와 동일한 가상 네트워크에 있습니다.
- 최종적으로 클러스터에서 사용할 수 있는 가상 머신 및 모든 FCI에 충분한 IP 주소 공간이 있는 서브넷에.
- Azure 가용성 집합 또는 가용성 영역에 있습니다.

[SQL Server 사전](../../../virtual-machines/windows/quick-create-portal.md) 설치 [된](sql-vm-create-portal-quickstart.md) 이미지를 사용 하 여 Azure 가상 컴퓨터를 만들 수 있습니다. SQL Server 이미지를 선택 하는 경우 장애 조치 (failover) 클러스터 인스턴스를 설치 하기 전에 SQL Server 인스턴스를 수동으로 제거 해야 합니다. 


## <a name="uninstall-sql-server"></a>SQL Server 제거

FCI 생성 프로세스의 일부로 장애 조치 (failover) 클러스터에 클러스터 된 인스턴스로 SQL Server를 설치 합니다. *SQL Server 하지 않고 Azure Marketplace 이미지를 사용 하 여 가상 컴퓨터를 배포한 경우이 단계를 건너뛸 수 있습니다.* SQL Server 사전 설치 된 이미지를 배포한 경우 SQL IaaS 에이전트 확장에서 SQL Server VM 등록을 취소 한 다음 SQL Server을 제거 해야 합니다. 

### <a name="unregister-from-the-sql-iaas-agent-extension"></a>SQL IaaS 에이전트 확장에서 등록 취소

Azure Marketplace의 SQL Server VM 이미지는 SQL IaaS 에이전트 확장에 자동으로 등록 됩니다. 사전 설치 된 SQL Server 인스턴스를 제거 하기 전에 먼저 [SQL IaaS 에이전트 확장에서 각 SQL Server VM의 등록을 취소](sql-agent-extension-manually-register-single-vm.md#unregister-from-extension)해야 합니다. 

### <a name="uninstall-sql-server"></a>SQL Server 제거

확장에서 등록을 취소 한 후 SQL Server를 제거할 수 있습니다. 각 가상 머신에서 다음 단계를 수행 합니다. 

1. RDP를 사용 하 여 가상 머신에 연결 합니다.

   RDP를 사용하여 가상 머신에 처음 연결하는 경우 네트워크에서 PC를 검색할 수 있도록 허용할지 묻는 메시지가 표시됩니다. **예** 를 선택합니다.

1. SQL Server 기반 가상 머신 이미지 중 하나를 사용 하는 경우 SQL Server 인스턴스를 제거 합니다.

   1. **프로그램 및 기능** 에서 **Microsoft SQL Server 201_(64비트)** 을 마우스 오른쪽 단추로 클릭하고 **제거/변경** 을 선택합니다.
   1. **제거** 를 선택합니다.
   1. 기본 인스턴스를 선택합니다.
   1. **데이터베이스 엔진 서비스** 에서 모든 기능을 제거합니다. **공유 기능** 에서 아무것도 제거 하지 마세요. 다음 스크린샷과 같은 출력이 표시됩니다.

      ![기능 선택](./media/failover-cluster-instance-prepare-vm/03-remove-features.png)

   1. **다음** 을 선택한 다음, **제거** 를 선택합니다.
   1. 인스턴스가 성공적으로 제거 되 면 가상 머신을 다시 시작 합니다. 

## <a name="open-the-firewall"></a>방화벽을 엽니다. 

각 가상 컴퓨터에서 SQL Server 사용 하는 Windows 방화벽 TCP 포트를 엽니다. 기본적으로 포트 1433입니다. 하지만 Azure VM 배포에서 SQL Server 포트를 변경할 수 있으므로 사용자 환경에서 사용 하 SQL Server 포트를 엽니다. 이 포트는 Azure Marketplace에서 배포 된 SQL Server 이미지에서 자동으로 열립니다. 

[부하 분산 장치](failover-cluster-instance-vnn-azure-load-balancer-configure.md)를 사용 하는 경우 상태 프로브에서 사용 하는 포트도 열어야 합니다. 기본적으로 포트 59999입니다. 그러나 부하 분산 장치를 만들 때 지정 하는 모든 TCP 포트를 사용할 수 있습니다. 

다음 표에서는 FCI 구성에 따라 열어야 할 수 있는 포트에 대해 자세히 설명 합니다. 

   | 목적 | 포트 | 메모
   | ------ | ------ | ------
   | SQL Server | TCP 1433 | SQL Server의 기본 인스턴스에 대한 표준 포트입니다. 갤러리에서 이미지를 사용한 경우 이 포트는 자동으로 열립니다. </br> </br> **사용** : 모든 fci 구성. |
   | 상태 프로브 | TCP 59999 | 모든 공개 TCP 포트입니다. 이 포트를 사용 하도록 부하 분산 장치 [상태 프로브](failover-cluster-instance-vnn-azure-load-balancer-configure.md#configure-health-probe) 및 클러스터를 구성 합니다. </br> </br> **사용** : 부하 분산 장치를 사용 하는 fci |
   | 파일 공유 | UDP 445 | 파일 공유 서비스에서 사용 하는 포트입니다. </br> </br> Premium 파일 공유를 사용 하는 fci **에서 사용** 됩니다. |

## <a name="join-the-domain"></a>도메인 가입

또한 가상 컴퓨터를 도메인에 가입 시켜야 합니다. [빠른 시작 템플릿을](../../../active-directory-domain-services/join-windows-vm-template.md#join-an-existing-windows-server-vm-to-a-managed-domain)사용 하 여이 작업을 수행할 수 있습니다. 

## <a name="review-storage-configuration"></a>저장소 구성 검토

Azure Marketplace에서 만든 가상 머신은 연결 된 저장소와 함께 제공 됩니다. 프리미엄 파일 공유 또는 Azure 공유 디스크를 사용 하 여 FCI 저장소를 구성 하려는 경우 장애 조치 (failover) 클러스터 인스턴스에 로컬 저장소가 사용 되지 않기 때문에 연결 된 저장소를 제거 하 여 비용을 절감할 수 있습니다. 그러나 스토리지 공간 다이렉트 FCI 솔루션에 연결 된 저장소를 사용할 수 있으므로이 경우에는 제거 하는 것이 좋습니다. FCI 저장소 솔루션을 검토 하 여 연결 된 저장소 제거가 비용 절감에 가장 적합 한 것인지 확인 합니다. 


## <a name="next-steps"></a>다음 단계

이제 가상 머신 환경을 준비 했으므로 장애 조치 (failover) 클러스터 인스턴스를 구성할 준비가 되었습니다. 

다음 가이드 중 하나를 선택 하 여 비즈니스에 적합 한 FCI 환경을 구성 합니다. 
- [Azure 공유 디스크를 사용 하 여 FCI 구성](failover-cluster-instance-azure-shared-disks-manually-configure.md)
- [프리미엄 파일 공유를 사용 하 여 FCI 구성](failover-cluster-instance-premium-file-share-manually-configure.md)
- [스토리지 공간 다이렉트를 사용 하 여 FCI 구성](failover-cluster-instance-storage-spaces-direct-manually-configure.md)

자세한 내용은 [Azure vm의 SQL Server](failover-cluster-instance-overview.md) 및 [지원 되는 HADR 구성](hadr-cluster-best-practices.md)의 fci 개요를 참조 하세요. 

자세한 내용은 다음을 참조하세요. 
- [Windows 클러스터 기술](/windows-server/failover-clustering/failover-clustering-overview)   
- [SQL Server 장애 조치(failover) 클러스터 인스턴스](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
