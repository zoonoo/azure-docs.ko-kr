---
title: Azure 사이트 복구를 사용하여 Azure VM 온-프레미스 오버에 연결
description: Azure 사이트 복구를 사용하여 온-프레미스에서 Azure로 장애 조치 후 Azure VM에 연결하는 방법을 설명합니다.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/13/2019
ms.author: mayg
ms.openlocfilehash: f222cdd315b79503b1bdea032f495c71df4682b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281991"
---
# <a name="connect-to-azure-vms-after-failover-from-on-premises"></a>온-프레미스에서 장애 조치 후 Azure VM에 연결 


이 문서에서는 장애 조치 후 Azure VM에 성공적으로 연결할 수 있도록 연결을 설정하는 방법에 대해 설명합니다.

온-프레미스 가상 시스템(VM) 및 물리적 서버의 재해 복구를 Azure에 설정하면 [Azure 사이트 복구가](site-recovery-overview.md) Azure에 대한 컴퓨터 복제를 시작합니다. 그런 다음 가동 중단이 발생하면 온-프레미스 사이트에서 Azure로 장애 조치할 수 있습니다. 장애 조치(failover)가 발생하면 사이트 복구에서 복제된 온-프레미스 데이터를 사용하여 Azure VM을 만듭니다. 재해 복구 계획의 일환으로 장애 조치 후 이러한 Azure VM에서 실행 중인 앱에 연결하는 방법을 파악해야 합니다.

이 문서에서는 다음 방법을 알아봅니다.

> [!div class="checklist"]
> * 장애 조치 전에 온-프레미스 컴퓨터를 준비합니다.
> * 장애 조치 후 Azure VM을 준비합니다. 
> * 장애 조치 후 Azure VM에서 IP 주소를 유지합니다.
> * 장애 조치 후 Azure VM에 새 IP 주소를 할당합니다.

## <a name="prepare-on-premises-machines"></a>온-프레미스 기계 준비

Azure VM에 대한 연결을 보장하려면 장애 조치 전에 온-프레미스 컴퓨터를 준비합니다.

### <a name="prepare-windows-machines"></a>Windows 머신 준비

온-프레미스 Windows 머신에서 다음을 수행합니다.

1. Windows 설정을 구성합니다. 여기에는 정적 영구 경로 또는 WinHTTP 프록시를 제거하고 디스크 SAN 정책을 **OnlineAll로**설정하는 것이 포함됩니다. 다음 지침을 [따르십시오.](../virtual-machines/windows/prepare-for-upload-vhd-image.md#set-windows-configurations-for-azure)

2. [이러한 서비스](../virtual-machines/windows/prepare-for-upload-vhd-image.md#check-the-windows-services)가 실행 중인지 확인합니다.

3. 원격 데스크톱(RDP)을 사용하도록 설정하여 온-프레미스 머신에 대한 원격 연결을 허용합니다. PowerShell을 통해 RDP를 사용하도록 설정하는 방법에 대해 [알아보세요](../virtual-machines/windows/prepare-for-upload-vhd-image.md#update-remote-desktop-registry-settings).

4. 장애 조치 후 온-프레미스 컴퓨터의 Windows 방화벽에서 장애 조치 후 인터넷을 통해 Azure VM에 액세스하려면 공용 프로필에서 TCP 및 UDP를 허용하고 RDP를 모든 프로필에 대해 허용된 앱으로 설정합니다.

5. 장애 조치 후 사이트 간 VPN을 통해 Azure VM에 액세스하려면 온-프레미스 컴퓨터의 Windows 방화벽에서 도메인 및 개인 프로필에 대한 RDP를 허용합니다. RDP 트래픽을 허용하는 방법에 대해 [알아보세요](../virtual-machines/windows/prepare-for-upload-vhd-image.md#configure-windows-firewall-rules).
6. 장애 조치(failover)를 트리거할 때 온-프레미스 VM에 보류 중인 Windows 업데이트가 없는지 확인합니다. 있는 경우 장애 조치 후 Azure VM에 업데이트를 설치하기 시작할 수 있으며 업데이트가 완료될 때까지 VM에 로그인할 수 없습니다.

### <a name="prepare-linux-machines"></a>Linux 머신 준비

온-프레미스 Linux 머신에서 다음을 수행합니다.

1. 시스템 부팅 시 Secure Shell 서비스가 자동으로 시작되도록 설정되어 있는지 확인합니다.
2. 방화벽 규칙에서 SSH 연결을 허용하는지 확인합니다.


## <a name="configure-azure-vms-after-failover"></a>장애 조치 후 Azure VM 구성

장애 조치 후 생성된 Azure VM에서 다음을 수행합니다.

1. 인터넷을 통해 VM에 연결하려면 공용 IP 주소를 VM에 할당합니다. 온-프레미스 머신에 사용한 것과 동일한 공용 IP 주소는 Azure VM에 사용할 수 없습니다. [자세히 알아보기](../virtual-network/virtual-network-public-ip-address.md)
2. VM의 NSG(네트워크 보안 그룹) 규칙에서 RDP 또는 SSH 포트로 들어오는 연결을 허용하는지 확인합니다.
3. [부팅 진단](../virtual-machines/troubleshooting/boot-diagnostics.md#enable-boot-diagnostics-on-existing-virtual-machine)을 확인하여 VM을 살펴봅니다.


> [!NOTE]
> Azure Bastion 서비스는 Azure VM에 대한 프라이빗 RDP 및 SSH 액세스를 제공합니다. 이 서비스에 대해 [자세히 알아보세요](../bastion/bastion-overview.md).

## <a name="set-a-public-ip-address"></a>공용 IP 주소 설정

Azure VM에 공용 IP 주소를 수동으로 할당하는 대신 사이트 복구 [복구 계획에서](site-recovery-create-recovery-plans.md)스크립트 또는 Azure 자동화 Runbook을 사용하여 장애 조치 중에 주소를 할당하거나 Azure 트래픽 관리자를 사용하여 DNS 수준 라우팅을 설정할 수 있습니다. 공개 주소 설정에 대해 [자세히 알아보세요.](concepts-public-ip-address-with-site-recovery.md)


## <a name="assign-an-internal-address"></a>내부 주소 할당

장애 조치 후 Azure VM의 내부 IP 주소를 설정하려면 다음과 같은 몇 가지 옵션이 있습니다.

- **동일한 IP 주소 유지**: Azure VM에서 온-프레미스 컴퓨터에 할당된 것과 동일한 IP 주소를 사용할 수 있습니다.
- **다른 IP 주소 사용**: Azure VM에 대해 다른 IP 주소를 사용할 수 있습니다.


## <a name="retain-ip-addresses"></a>IP 주소 유지

사이트 복구를 사용하면 Azure에 장애 복구할 때 동일한 IP 주소를 유지할 수 있습니다. 동일한 IP 주소를 유지하면 장애 조치 후 잠재적인 네트워크 문제를 방지할 수 있지만 몇 가지 복잡성이 발생합니다.

- 대상 Azure VM이 온-프레미스 사이트와 동일한 IP 주소/서브넷을 사용하는 경우 주소가 겹치기 때문에 사이트 간 VPN 연결 또는 ExpressRoute를 사용하여 해당 주소 간에 연결할 수 없습니다. 서브넷은 고유해야 합니다.
- Azure VM에서 앱을 사용할 수 있도록 장애 조치 후 온-프레미스에서 Azure로 연결해야 합니다. Azure는 확장된 VLAN을 지원하지 않으므로 IP 주소를 유지하려면 온-프레미스 컴퓨터 외에 전체 서브넷에 실패하여 IP 공간을 Azure로 가져가야 합니다.
- 서브넷 장애 조치(failover)는 특정 서브넷을 온-프레미스 및 Azure에서 동시에 사용할 수 없도록 합니다.

IP 주소를 유지하려면 다음 단계가 필요합니다.

- 복제된 항목의 계산 & 네트워크 속성에서 대상 Azure VM에 대한 네트워크 및 IP 주소를 설정하여 온-프레미스 설정을 미러링합니다.
- 서브넷은 재해 복구 프로세스의 일부로 관리되어야 합니다. 온-프레미스 네트워크와 일치하도록 Azure VNet이 필요하며 장애 조치 네트워크 경로후 서브넷이 Azure로 이동되었음을 반영하고 새 IP 주소 위치를 반영하도록 수정해야 합니다.  

### <a name="failover-example"></a>장애 조치 예제

예제를 살펴보겠습니다.

- 가상의 회사 인 Woodgrove Bank는 비즈니스 앱을 온-프레미스에서 호스팅하며 Azure에서 모바일 앱을 호스팅합니다.
- 사이트 간 VPN을 통해 온-프레미스에서 Azure로 연결됩니다. 
- Woodgrove는 사이트 복구를 사용하여 온-프레미스 컴퓨터를 Azure에 복제합니다.
- 온-프레미스 앱은 하드 코딩된 IP 주소를 사용하므로 Azure에서 동일한 IP 주소를 유지하려고 합니다.
- 온-프레미스 에서 앱을 실행하는 컴퓨터가 세 개의 서브넷에서 실행되고 있습니다.
    - 192.168.1.0/24.
    - 192.168.2.0/24
    - 192.168.3.0/24
- Azure에서 실행되는 앱은 두 개의 서브넷에 있는 Azure VNet **Azure 네트워크에** 있습니다.
- 172.16.1.0/24
- 172.16.2.0/24.

주소를 유지하기 위해 주소가 수행하는 방법은 다음과 같습니다.

1. 복제를 사용하도록 설정하면 컴퓨터가 **Azure Network**에 복제되도록 지정합니다.
2. Azure에서 **복구 네트워크를** 만듭니다. 이 VNet은 온-프레미스 네트워크의 192.168.1.0/24 서브넷을 미러합니다.
3. Woodgrove는 두 네트워크 간에 [VNet-VNet 연결을](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) 설정합니다. 

    > [!NOTE]
    > 응용 프로그램 요구 사항에 따라 사이트 복구 [복구 계획의](site-recovery-create-recovery-plans.md)수동 단계/스크립팅 단계/Azure 자동화 실행책또는 장애 조치 완료 후 장애 조치 전에 VNet-VNet 연결을 설정할 수 있습니다.

4. 장애 조치 전에 사이트 복구의 컴퓨터 속성에서 다음 절차에 설명된 대로 대상 IP 주소를 온-프레미스 컴퓨터의 주소로 설정합니다.
5. 장애 조치 후 Azure VM은 동일한 IP 주소로 만들어집니다. Woodgrove는 VNet 피어링을 사용하여 **Azure 네트워크에서** **복구 네트워크** VNet으로 연결합니다(전송 연결이 활성화된 경우).
6. 온-프레미스에서 Woodgrove는 192.168.1.0/24가 Azure로 이동되었음을 반영하기 위해 경로를 수정하는 등 네트워크를 변경해야 합니다.  

**장애 조치 전 인프라**

![서브넷 장애 조치(failover) 전](./media/site-recovery-network-design/network-design7.png)


**장애 조치 후 인프라**

![서브넷 장애 조치(failover) 후](./media/site-recovery-network-design/network-design9.png)


### <a name="set-target-network-settings"></a>대상 네트워크 설정 설정

장애 조치 전에 대상 Azure VM에 대한 네트워크 설정 및 IP 주소를 지정합니다.

1.  복구 서비스 자격 증명 모음에서 **복제된 항목>** 온-프레미스 컴퓨터를 선택합니다.
2. 컴퓨터의 **계산 및 네트워크** 페이지에서 **편집을**클릭하여 대상 Azure VM에 대한 네트워크 및 어댑터 설정을 구성합니다.
3. **네트워크 속성에서**장애 조치 후 Azure VM이 생성될 때 위치할 대상 네트워크를 선택합니다.
4. **네트워크 인터페이스에서**대상 네트워크에서 네트워크 어댑터를 구성합니다. 기본적으로 사이트 복구는 온-프레미스 컴퓨터에서 검색된 모든 NIC를 표시합니다.
    - **대상 네트워크 인터페이스 유형에서** 각 NIC를 **기본,** **보조로**설정하거나 대상 네트워크에서 특정 NIC가 필요하지 않은 경우 **만들지 마십시오.** 장애 조치의 경우 하나의 네트워크 어댑터를 기본 네트워크 어댑터로 설정해야 합니다. 대상 네트워크를 수정하면 Azure VM의 모든 NIC에 영향을 줍니다.
    - NIC 이름을 클릭하여 Azure VM을 배포할 서브넷을 지정합니다.
    - 대상 Azure VM에 할당할 개인 IP 주소로 **동적을** 덮어씁니다. IP 주소가 지정되지 않은 경우 사이트 복구는 장애 조치 시 NIC에 서브넷에서 사용 가능한 다음 IP 주소를 할당합니다.
    - Azure에 대한 온-프레미스 장애 조치(failover)에 대한 NIC 관리에 대해 [자세히 알아봅니다.](site-recovery-manage-network-interfaces-on-premises-to-azure.md)


## <a name="get-new-ip-addresses"></a>새 IP 주소 받기

이 시나리오에서 Azure VM 장애 조치 후 새 IP 주소를 가져옵니다. Azure VM의 IP 주소를 가리키도록 컴퓨터에 실패한 레코드를 업데이트하는 DNS 업데이트입니다.



## <a name="next-steps"></a>다음 단계
온-프레미스 Active Directory 및 DNS를 Azure로 복제하는 [방법에 대해 알아봅니다.](site-recovery-active-directory.md)


