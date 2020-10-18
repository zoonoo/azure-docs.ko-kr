---
title: 클러스터 구성 모범 사례
description: 지원 되는 쿼럼 또는 연결 라우팅 옵션과 같이 Azure Virtual Machines에서 SQL Server에 대 한 HADR (고가용성 및 재해 복구)을 구성할 때 지원 되는 클러스터 구성에 대해 알아봅니다.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/02/2020
ms.author: mathoma
ms.openlocfilehash: 1a2c4364337083be005c550a8859079cd3bb1218
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2020
ms.locfileid: "92167953"
---
# <a name="cluster-configuration-best-practices-sql-server-on-azure-vms"></a>클러스터 구성 모범 사례(Azure VM의 SQL Server)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

클러스터는 Azure Virtual Machines (Vm)에서 SQL Server를 사용 하 여 고가용성 및 재해 복구 (HADR)에 사용 됩니다. 

이 문서에서는 Azure Vm에서 SQL Server와 함께 사용 하는 경우 [FCIs (장애 조치 (failover) 클러스터 인스턴스)](failover-cluster-instance-overview.md) 및 [가용성 그룹](availability-group-overview.md) 에 대 한 클러스터 구성 모범 사례를 제공 합니다. 


## <a name="networking"></a>네트워킹

서버당 단일 NIC (클러스터 노드) 및 단일 서브넷을 사용 합니다. Azure 네트워킹은 물리적 중복성을 가지 며, Azure 가상 머신 게스트 클러스터에서 추가 Nic 및 서브넷을 불필요 하 게 만듭니다. 클러스터 유효성 검사 보고서는 노드가 단일 네트워크에서만 연결할 수 있다는 경고를 표시합니다. Azure 가상 컴퓨터 게스트 장애 조치 (failover) 클러스터에서이 경고를 무시할 수 있습니다.

## <a name="quorum"></a>Quorum

[쿼럼 리소스](/windows-server/storage/storage-spaces/understand-quorum)없이 두 노드 클러스터가 작동 하지만, 고객은 프로덕션 지원을 보유 하기 위해 반드시 쿼럼 리소스를 사용 해야 합니다. 클러스터 유효성 검사는 쿼럼 리소스가 없는 클러스터를 통과 하지 않습니다. 

기술적으로 3 개 노드 클러스터는 쿼럼 리소스가 없는 단일 노드 손실 (아래쪽에서 두 개 노드)로 존속 될 수 있습니다. 하지만 클러스터가 두 개의 노드로 다운 된 후에는 노드 손실이 나 통신 오류가 발생 하 여 분할 인 요소 시나리오를 방지 하기 위해 클러스터 된 리소스가 오프 라인으로 전환 될 수 있습니다.

쿼럼 리소스를 구성 하면 클러스터를 온라인으로 한 노드만 온라인으로 계속할 수 있습니다.

다음 표에서는 Azure VM과 함께 사용 하기 위해 권장 되는 순서 대로 사용 가능한 쿼럼 옵션을 보여 줍니다. 여기에는 디스크 감시를 선호 하는 옵션이 있습니다. 


||[디스크 감시](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)  |[클라우드 감시](/windows-server/failover-clustering/deploy-cloud-witness)  |[파일 공유 감시](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)  |
|---------|---------|---------|---------|
|**지원되는 OS**| 모두 |Windows Server 2016+| 모두|


### <a name="disk-witness"></a>디스크 감시

디스크 감시는 클러스터에서 사용 가능한 저장소 그룹의 작은 클러스터 된 디스크입니다. 이 디스크는 항상 사용 가능 하며 노드 간에 장애 조치 (failover) 할 수 있습니다. 이 파일에는 클러스터 데이터베이스의 복사본이 포함 되며, 기본 크기는 일반적으로 1gb 보다 낮습니다. 디스크 감시는 Azure 공유 디스크 (또는 공유 SCSI, iSCSI 또는 파이버 채널 SAN과 같은 공유 디스크 솔루션)를 사용 하는 모든 클러스터에 대 한 기본 쿼럼 옵션입니다.  클러스터 된 공유 볼륨은 디스크 감시로 사용할 수 없습니다.

Azure 공유 디스크를 디스크 감시로 구성 합니다. 

시작 하려면 [디스크 감시 구성](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)을 참조 하세요.


**지원되는 OS**: 모두   


### <a name="cloud-witness"></a>클라우드 감시

클라우드 감시는 Microsoft Azure을 사용 하 여 클러스터 쿼럼에 대 한 응답을 제공 하는 장애 조치 (failover) 클러스터 쿼럼 감시 유형입니다. 기본 크기는 약 1mb 이며 타임 스탬프도 포함 합니다. 클라우드 감시는 여러 사이트, 여러 영역 및 여러 지역에서 배포 하는 데 적합 합니다.

시작 하려면 [클라우드 감시 구성](/windows-server/failover-clustering/deploy-cloud-witness#CloudWitnessSetUp)을 참조 하세요.


**지원되는 OS**: Windows Server 2016 이상   


### <a name="file-share-witness"></a>파일 공유 감시

파일 공유 감시는 일반적으로 Windows Server를 실행 하는 파일 서버에 구성 되는 SMB 파일 공유입니다. 이는 감시 파일에 클러스터링 정보를 유지 하지만 클러스터 데이터베이스의 복사본은 저장 하지 않습니다. Azure에서 파일 공유 감시로 사용할 [azure 파일 공유](../../../storage/files/storage-how-to-create-file-share.md) 를 구성 하거나 별도의 가상 머신에서 파일 공유를 사용할 수 있습니다.

Azure 파일 공유를 사용 하려는 경우 [프리미엄 파일 공유를 탑재](failover-cluster-instance-premium-file-share-manually-configure.md#mount-premium-file-share)하는 데 사용한 것과 동일한 프로세스를 사용 하 여 탑재할 수 있습니다. 

시작 하려면 [파일 공유 감시 구성](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)을 참조 하세요.


**지원되는 OS**: Windows Server 2012 이상   

## <a name="connectivity"></a>연결

기존 온-프레미스 네트워크 환경에서 SQL Server 장애 조치 (failover) 클러스터 인스턴스는 단일 컴퓨터에서 실행 되는 SQL Server 단일 인스턴스로 표시 됩니다. 장애 조치 (failover) 클러스터 인스턴스는 노드 간 장애 조치 (failover)를 수행 하므로 인스턴스의 VNN (가상 네트워크 이름)은 통합 연결 지점을 제공 하며 응용 프로그램에서 현재 활성 상태인 노드를 몰라도 SQL Server 인스턴스에 연결할 수 있도록 합니다. 장애 조치 (failover)가 발생 하면 가상 네트워크 이름이 시작 된 후 새 활성 노드에 등록 됩니다. 이 프로세스는 SQL Server 연결 되는 클라이언트나 응용 프로그램에 투명 하며,이로 인해 클라이언트 또는 응용 프로그램에서 오류가 발생 하는 동안 발생 하는 가동 중지 시간이 최소화 됩니다. 마찬가지로 가용성 그룹 수신기는 VNN을 사용 하 여 트래픽을 적절 한 복제본으로 라우팅합니다. 

Azure Load Balancer 또는 분산 네트워크 이름 (DNN)과 함께 VNN을 사용 하 여 트래픽을 Azure Vm의 SQL Server를 사용 하 여 장애 조치 (failover) 클러스터 인스턴스의 VNN으로 라우트 하거나 가용성 그룹의 기존 VNN 수신기를 바꿉니다. 


다음 표에서는 HADR 연결 지원 가능성을 비교 합니다. 

| |**VNN(가상 네트워크 이름)**  |**DNN(분산 네트워크 이름)**  |
|---------|---------|---------|
|**최소 OS 버전**| 모두 | Windows Server 2016 |
|**최소 SQL Server 버전** |모두 |SQL Server 2019 CU2 (FCI 용)<br/> SQL Server 2019 CU8 (AG)|
|**지원 되는 HADR 솔루션** | 장애 조치(Failover) 클러스터 인스턴스 <br/> 가용성 그룹 | 장애 조치(Failover) 클러스터 인스턴스 <br/> 가용성 그룹|


### <a name="virtual-network-name-vnn"></a>VNN(가상 네트워크 이름)

가상 IP 액세스 지점은 Azure에서 다르게 작동 하므로 FCI 노드 또는 가용성 그룹 수신기의 IP 주소에 트래픽을 라우팅하도록 [Azure Load Balancer](../../../load-balancer/index.yml) 를 구성 해야 합니다. Azure virtual machines에서 부하 분산 장치는 클러스터 된 SQL Server 리소스가 사용 하는 VNN의 IP 주소를 보유 합니다. 부하 분산 장치는 프런트 엔드에 도착 하는 인바운드 흐름을 배포한 다음 해당 트래픽을 백 엔드 풀로 정의 된 인스턴스로 라우팅합니다. 부하 분산 규칙 및 상태 프로브를 사용 하 여 트래픽 흐름을 구성 합니다. SQL Server FCI를 사용 하 여 백 엔드 풀 인스턴스는 SQL Server를 실행 하는 Azure 가상 머신입니다. 

부하 분산 장치를 사용 하는 경우 장애 조치 (failover) 지연 시간이 약간 있습니다. 상태 프로브는 기본적으로 10 초 마다 연결 검사를 수행 하기 때문입니다. 

시작 하려면 [장애 조치 (failover) 클러스터 인스턴스](failover-cluster-instance-vnn-azure-load-balancer-configure.md) 또는 [가용성 그룹](availability-group-vnn-azure-load-balancer-configure.md) 에 대 한 Azure Load Balancer를 구성 하는 방법을 알아봅니다.

**지원되는 OS**: 모두   
**지원되는 SQL 버전**: 모두   
**지원 되는 HADR 솔루션**: 장애 조치 (Failover) 클러스터 인스턴스 및 가용성 그룹   


### <a name="distributed-network-name-dnn"></a>DNN(분산 네트워크 이름)

분산 네트워크 이름은 SQL Server 2019에 대 한 새로운 Azure 기능입니다. DNN는 클라이언트에서 부하 분산 장치를 사용 하지 않고 SQL Server 장애 조치 (failover) 클러스터 인스턴스 또는 가용성 그룹에 연결할 수 있는 대체 SQL Server 방법을 제공 합니다. 

DNN 리소스가 생성 되 면 클러스터는 클러스터에 있는 모든 노드의 IP 주소를 DNS 이름에 바인딩합니다. SQL 클라이언트는이 목록에 있는 각 IP 주소에 연결을 시도 하 여 연결할 리소스를 찾습니다.  연결 문자열에를 지정 하 여이 프로세스를 가속화할 수 있습니다 `MultiSubnetFailover=True` . 이 설정은 공급자가 모든 IP 주소를 병렬로 시도 하도록 지시 하므로 클라이언트는 FCI 또는 수신기에 즉시 연결할 수 있습니다. 

가능 하면 부하 분산 장치에 대해 분산 네트워크 이름이 권장 됩니다. 
- 종단 간 솔루션은 더 이상 부하 분산 장치 리소스를 유지 관리할 필요가 없기 때문에 더욱 강력해 집니다. 
- 부하 분산 장치 프로브를 제거 하면 장애 조치 (failover) 기간이 최소화 됩니다. 
- DNN는 Azure Vm의 SQL Server를 사용 하 여 장애 조치 (failover) 클러스터 인스턴스 또는 가용성 그룹 수신기의 프로 비전 및 관리를 간소화 합니다. 

DNN를 사용 하는 경우 대부분의 SQL Server 기능은 FCI 및 가용성 그룹과 투명 하 게 작동 하지만 특별 한 고려 사항이 필요할 수 있는 특정 기능이 있습니다. 자세히 알아보려면 [Fci 및 DNN 상호 운용성](failover-cluster-instance-dnn-interoperability.md) 및 [AG 및 DNN 상호 운용성](availability-group-dnn-interoperability.md) 을 참조 하세요. 

시작 하려면 [장애 조치 (failover) 클러스터 인스턴스](failover-cluster-instance-distributed-network-name-dnn-configure.md) 또는 [가용성 그룹](availability-group-distributed-network-name-dnn-listener-configure.md) 에 대 한 분산 네트워크 이름 리소스를 구성 하는 방법을 알아봅니다.

**지원되는 OS**: Windows Server 2016 이상   
**지원 되는 SQL 버전**: fci (SQL SERVER 2019 CU2) 및 AG (SQL SERVER 2019 CU8)   
**지원 되는 HADR 솔루션**: 장애 조치 (Failover) 클러스터 인스턴스 및 가용성 그룹   


## <a name="limitations"></a>제한 사항

FCI 또는 가용성 그룹을 사용 하 고 Azure Virtual Machines에서 SQL Server 하는 경우 다음 제한 사항을 고려 하세요. 

### <a name="msdtc"></a>MSDTC 

Azure Virtual Machines는 CSV(클러스터형 공유 볼륨)의 스토리지와 [Azure 표준 Load Balancer](../../../load-balancer/load-balancer-standard-overview.md)가 있는 Windows Server 2019에서 또는 Azure 공유 디스크를 사용하는 SQL Server VM에서 MSDTC(Microsoft Distributed Transaction Coordinator)를 지원합니다. 

Azure Virtual Machines는 클러스터링된 공유 볼륨이 있는 Windows Server 2016 또는 이전 버전에서 MSDTC를 지원하지 않으며, 이유는 다음과 같습니다.

- 클러스터형 MSDTC 리소스는 공유 스토리지를 사용하도록 구성할 수 없습니다. Windows Server 2016에서 MSDTC 리소스를 만드는 경우 스토리지가 사용 가능하더라도 사용 가능한 공유 스토리지가 표시되지 않습니다. 이 문제는 Windows Server 2019에서 수정되었습니다.
- 기본 부하 분산 장치는 RPC 포트를 처리하지 않습니다.


## <a name="next-steps"></a>다음 단계

솔루션에 대 한 적절 한 모범 사례를 확인 한 후에는 [Azure Portal](availability-group-azure-portal-configure.md), [Azure CLI/PowerShell](availability-group-az-cli-configure.md)또는 [Azure 빠른 시작 템플릿을](availability-group-quickstart-template-configure.md)사용 하 여 [fci에 대 한 SQL Server VM 준비](failover-cluster-instance-prepare-vm.md) 하거나 가용성 그룹을 만들어 시작 하세요. 

