---
title: Azure Site Recovery를 사용 하 여 Azure Vm 온-프레미스 장애 조치 (failover)에 연결
description: Azure Site Recovery를 사용 하 여 온-프레미스에서 Azure로 장애 조치 (failover) 후 Azure Vm에 연결 하는 방법을 설명 합니다.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/13/2019
ms.author: mayg
ms.openlocfilehash: f222cdd315b79503b1bdea032f495c71df4682b5
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79281991"
---
# <a name="connect-to-azure-vms-after-failover-from-on-premises"></a>온-프레미스에서 장애 조치 (failover) 후 Azure Vm에 연결 


이 문서에서는 장애 조치 (failover) 후 Azure Vm에 성공적으로 연결할 수 있도록 연결을 설정 하는 방법을 설명 합니다.

온-프레미스 Vm (가상 머신) 및 물리적 서버의 재해 복구를 Azure로 설정 하는 경우 Azure에 컴퓨터를 복제 하는 작업이 시작 [Azure Site Recovery](site-recovery-overview.md) . 그런 다음 중단이 발생 하면 온-프레미스 사이트에서 Azure로 장애 조치 (failover) 할 수 있습니다. 장애 조치 (failover)가 발생 하면 Site Recovery는 복제 된 온-프레미스 데이터를 사용 하 여 Azure Vm을 만듭니다. 재해 복구 계획의 일환으로 장애 조치 (failover) 후 이러한 Azure Vm에서 실행 되는 앱에 연결 하는 방법을 파악 해야 합니다.

이 문서에서는 다음 방법을 알아봅니다.

> [!div class="checklist"]
> * 장애 조치 (failover) 전에 온-프레미스 컴퓨터를 준비 합니다.
> * 장애 조치 (failover) 후 Azure Vm 준비. 
> * 장애 조치 (failover) 후 Azure Vm의 IP 주소를 유지 합니다.
> * 장애 조치 (failover) 후 Azure Vm에 새 IP 주소를 할당 합니다.

## <a name="prepare-on-premises-machines"></a>온-프레미스 컴퓨터 준비

Azure Vm에 대 한 연결을 보장 하려면 장애 조치 (failover) 전에 온-프레미스 컴퓨터를 준비 합니다.

### <a name="prepare-windows-machines"></a>Windows 머신 준비

온-프레미스 Windows 머신에서 다음을 수행합니다.

1. Windows 설정을 구성합니다. 여기에는 정적 영구 경로 또는 WinHTTP 프록시를 제거 하 고 디스크 SAN 정책을 **OnlineAll**로 설정 하는 작업이 포함 됩니다. 다음 지침을 [따르세요](../virtual-machines/windows/prepare-for-upload-vhd-image.md#set-windows-configurations-for-azure) .

2. [이러한 서비스](../virtual-machines/windows/prepare-for-upload-vhd-image.md#check-the-windows-services)가 실행 중인지 확인합니다.

3. 원격 데스크톱(RDP)을 사용하도록 설정하여 온-프레미스 머신에 대한 원격 연결을 허용합니다. PowerShell을 통해 RDP를 사용하도록 설정하는 방법에 대해 [알아보세요](../virtual-machines/windows/prepare-for-upload-vhd-image.md#update-remote-desktop-registry-settings).

4. 장애 조치 (failover) 후 인터넷을 통해 Azure VM에 액세스 하려면 온-프레미스 컴퓨터의 Windows 방화벽에서 공용 프로필의 TCP 및 UDP를 허용 하 고 RDP를 모든 프로필에 대해 허용 되는 앱으로 설정 합니다.

5. 장애 조치 (failover) 후 사이트 간 VPN을 통해 Azure VM에 액세스 하려면 온-프레미스 컴퓨터의 Windows 방화벽에서 도메인 및 개인 프로필에 대 한 RDP를 허용 합니다. RDP 트래픽을 허용하는 방법에 대해 [알아보세요](../virtual-machines/windows/prepare-for-upload-vhd-image.md#configure-windows-firewall-rules).
6. 장애 조치 (failover)를 트리거할 때 온-프레미스 VM에 보류 중인 Windows 업데이트가 없는지 확인 합니다. 업데이트가 있으면 장애 조치 (failover) 후 업데이트가 Azure VM에 설치를 시작할 수 있으며 업데이트가 완료 될 때까지 VM에 로그인 할 수 없습니다.

### <a name="prepare-linux-machines"></a>Linux 머신 준비

온-프레미스 Linux 머신에서 다음을 수행합니다.

1. 시스템 부팅 시 Secure Shell 서비스가 자동으로 시작되도록 설정되어 있는지 확인합니다.
2. 방화벽 규칙에서 SSH 연결을 허용하는지 확인합니다.


## <a name="configure-azure-vms-after-failover"></a>장애 조치 (failover) 후 Azure Vm 구성

장애 조치 (failover) 후 생성 된 Azure Vm에서 다음을 수행 합니다.

1. 인터넷을 통해 VM에 연결하려면 공용 IP 주소를 VM에 할당합니다. 온-프레미스 머신에 사용한 것과 동일한 공용 IP 주소는 Azure VM에 사용할 수 없습니다. [자세히 알아보기](../virtual-network/virtual-network-public-ip-address.md)
2. VM의 NSG(네트워크 보안 그룹) 규칙에서 RDP 또는 SSH 포트로 들어오는 연결을 허용하는지 확인합니다.
3. [부팅 진단](../virtual-machines/troubleshooting/boot-diagnostics.md#enable-boot-diagnostics-on-existing-virtual-machine)을 확인하여 VM을 살펴봅니다.


> [!NOTE]
> Azure Bastion 서비스는 Azure VM에 대한 프라이빗 RDP 및 SSH 액세스를 제공합니다. 이 서비스에 대해 [자세히 알아보세요](../bastion/bastion-overview.md).

## <a name="set-a-public-ip-address"></a>공용 IP 주소 설정

Azure VM에 공용 IP 주소를 수동으로 할당 하는 대신, Site Recovery [복구 계획](site-recovery-create-recovery-plans.md)에서 스크립트 또는 azure automation runbook을 사용 하 여 장애 조치 (failover) 중에 주소를 할당 하거나 azure Traffic Manager를 사용 하 여 DNS 수준 라우팅을 설정할 수 있습니다. 공용 주소를 설정 하는 방법에 [대해 자세히 알아보세요](concepts-public-ip-address-with-site-recovery.md) .


## <a name="assign-an-internal-address"></a>내부 주소 할당

장애 조치 (failover) 후 Azure VM의 내부 IP 주소를 설정 하려면 다음 두 가지 옵션을 사용할 수 있습니다.

- **동일한 ip 주소 유지**: Azure VM에서 온-프레미스 컴퓨터에 할당 된 것과 동일한 ip 주소를 사용할 수 있습니다.
- **다른 ip 주소 사용**: Azure VM에 다른 ip 주소를 사용할 수 있습니다.


## <a name="retain-ip-addresses"></a>IP 주소 유지

Site Recovery를 사용 하면 Azure로 장애 조치 (failover) 할 때 동일한 IP 주소를 유지할 수 있습니다. 동일한 IP 주소를 유지 하면 장애 조치 (failover) 후 잠재적인 네트워크 문제가 방지 되지만 몇 가지 복잡성이 있습니다.

- 대상 Azure VM이 온-프레미스 사이트와 동일한 IP 주소/서브넷을 사용 하는 경우 주소가 겹치므로 사이트 간 VPN 연결 또는 Express 경로를 사용 하 여 서로 연결할 수 없습니다. 서브넷은 고유 해야 합니다.
- Azure Vm에서 앱을 사용할 수 있도록 장애 조치 (failover) 후 온-프레미스에서 Azure로 연결 해야 합니다. Azure는 확장 된 Vlan을 지원 하지 않으므로 IP 주소를 유지 하려는 경우 온-프레미스 컴퓨터 외에도 전체 서브넷을 장애 조치 (failover) 하 여 Azure로 IP 공간을 이동 해야 합니다.
- 서브넷 장애 조치 (failover)는 특정 서브넷을 온-프레미스와 Azure에서 동시에 사용할 수 없도록 합니다.

IP 주소를 유지 하려면 다음 단계를 수행 해야 합니다.

- 복제 된 항목의 Compute & 네트워크 속성에서 온-프레미스 설정을 미러링할 대상 Azure VM에 대 한 네트워크 및 IP 주소를 설정 합니다.
- 서브넷은 재해 복구 프로세스의 일부로 관리 해야 합니다. 온-프레미스 네트워크와 일치 하는 Azure VNet이 필요 합니다. 그리고 장애 조치 (failover) 후에는 서브넷이 Azure로 이동 하 고 새 IP 주소 위치로 장애 조치 (failover) 네트워크 경로를 수정 해야 합니다.  

### <a name="failover-example"></a>장애 조치 예제

예제를 살펴보겠습니다.

- 가상의 회사 Woodgrove Bank는 온-프레미스의 비즈니스 앱을 호스트 하 여 Azure에서 모바일 앱을 호스팅합니다.
- 사이트 간 VPN을 통해 온-프레미스에서 Azure로 연결 합니다. 
- Woodgrove는 Site Recovery을 사용 하 여 온-프레미스 컴퓨터를 Azure에 복제 합니다.
- 온-프레미스 앱은 하드 코드 된 IP 주소를 사용 하므로 Azure에서 동일한 IP 주소를 유지 하려고 합니다.
- 온-프레미스 앱을 실행 하는 컴퓨터는 세 개의 서브넷으로 실행 됩니다.
    - 192.168.1.0/24.
    - 192.168.2.0/24
    - 192.168.3.0/24
- Azure에서 실행 되는 앱은 Azure VNet **Azure 네트워크** 의 두 서브넷에 있습니다.
- 172.16.1.0/24
- 172.16.2.0/24.

주소를 유지 하기 위해 수행 하는 작업은 다음과 같습니다.

1. 복제를 사용 하도록 설정 하는 경우 컴퓨터가 **Azure 네트워크**에 복제 하도록 지정 합니다.
2. Azure에서 **복구 네트워크** 를 만듭니다. 이 VNet은 온-프레미스 네트워크에서 192.168.1.0/24 서브넷을 미러링합니다.
3. Woodgrove는 두 네트워크 간에 [vnet 간 연결](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) 을 설정 합니다. 

    > [!NOTE]
    > 응용 프로그램 요구 사항에 따라 장애 조치 (failover) 전에 VNet 간 연결을 설정 하거나, Site Recovery [복구 계획](site-recovery-create-recovery-plans.md)에서 수동 단계/스크립팅된 단계/a p a s e automation runbook으로 설정 하거나, 장애 조치 (failover)가 완료 된 후에 설정할 수 있습니다.

4. 장애 조치 (failover) 전에 Site Recovery의 컴퓨터 속성에서는 다음 절차에 설명 된 대로 대상 IP 주소를 온-프레미스 컴퓨터의 주소로 설정 합니다.
5. 장애 조치 (failover) 후 동일한 IP 주소를 사용 하 여 Azure Vm을 만듭니다. Woodgrove는 VNet 피어 링 (전송 연결 사용)을 사용 하 여 **Azure 네트워크** 에서 **복구 네트워크** vnet에 연결 합니다.
6. 온-프레미스에서는 192.168.1.0/24가 Azure로 이동 했음을 반영 하기 위해 경로 수정을 비롯 하 여 네트워크를 변경 해야 합니다.  

**장애 조치 전 인프라**

![서브넷 장애 조치(failover) 전](./media/site-recovery-network-design/network-design7.png)


**장애 조치 후 인프라**

![서브넷 장애 조치(failover) 후](./media/site-recovery-network-design/network-design9.png)


### <a name="set-target-network-settings"></a>대상 네트워크 설정 설정

장애 조치 (failover) 전에 대상 Azure VM의 네트워크 설정 및 IP 주소를 지정 합니다.

1.  Recovery Services 자격 증명 모음-> **복제 된 항목**에서 온-프레미스 컴퓨터를 선택 합니다.
2. 컴퓨터의 **계산 및 네트워크** 페이지에서 **편집**을 클릭 하 여 대상 Azure VM에 대 한 네트워크 및 어댑터 설정을 구성 합니다.
3. **네트워크 속성**에서 장애 조치 (failover) 후에 Azure VM을 만들 때 Azure VM이 배치 될 대상 네트워크를 선택 합니다.
4. **네트워크 인터페이스**에서 대상 네트워크에 네트워크 어댑터를 구성 합니다. 기본적으로 Site Recovery은 온-프레미스 컴퓨터에서 검색 된 모든 Nic를 표시 합니다.
    - 대상 네트워크에서 특정 NIC가 필요 하지 않은 경우 **대상 네트워크 인터페이스 유형** 에서 각 Nic를 **기본**, **보조**또는 **만들기 안 함** 으로 설정할 수 있습니다. 장애 조치 (failover)를 위해 네트워크 어댑터 하나를 기본으로 설정 해야 합니다. 대상 네트워크를 수정 하면 Azure VM의 모든 Nic에 영향을 줍니다.
    - NIC 이름을 클릭 하 여 Azure VM이 배포 될 서브넷을 지정 합니다.
    - 대상 Azure VM에 할당 하려는 개인 IP 주소를 사용 하 여 **동적** 을 덮어씁니다. IP 주소를 지정 하지 않으면 Site Recovery는 장애 조치 (failover) 시 서브넷에서 사용 가능한 다음 IP 주소를 NIC에 할당 합니다.
    - Azure로의 온-프레미스 장애 조치 (failover)에 대 한 Nic 관리에 대해 [자세히 알아보세요](site-recovery-manage-network-interfaces-on-premises-to-azure.md) .


## <a name="get-new-ip-addresses"></a>새 IP 주소 가져오기

이 시나리오에서 Azure VM은 장애 조치 (failover) 후에 새 IP 주소를 가져옵니다. 장애 조치 (failover) 된 컴퓨터에서 Azure VM의 IP 주소를 가리키도록 레코드를 업데이트 하는 DNS 업데이트입니다.



## <a name="next-steps"></a>다음 단계
온-프레미스 Active Directory 및 DNS를 Azure로 복제 하는 [방법에 대해 알아봅니다](site-recovery-active-directory.md) .


