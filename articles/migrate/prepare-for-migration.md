---
title: Azure Migrate를 사용하여 마이그레이션을 위한 머신 준비
description: Azure Migrate를 사용하여 마이그레이션하기 위한 온-프레미스 머신을 준비하는 방법을 알아봅니다.
ms.topic: tutorial
ms.date: 02/17/2020
ms.custom: MVC
ms.openlocfilehash: eba177a254606bb847e0866ae48281a889c53f9b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2020
ms.locfileid: "78927482"
---
# <a name="prepare-on-premises-machines-for-migration-to-azure"></a>Azure로 마이그레이션하기 위한 온-프레미스 머신 준비

이 문서에서는 Azure로의 마이그레이션을 시작하기 전에 [Azure Migrate: 서버 마이그레이션](migrate-services-overview.md#azure-migrate-server-migration-tool)에서 클릭하여 복제 상태를 모니터링할 수 있습니다.

이 문서에서는 다음 작업을 수행합니다.
> [!div class="checklist"]
> * 마이그레이션 제한 사항을 확인합니다.
> * 운영 체제 요구 사항 및 지원 제한 사항을 확인합니다.
> * 마이그레이션하려는 머신에 대한 URL 및 포트 액세스 검토를 사용하여 온-프레미스 머신을 준비하는 방법을 설명합니다.
> * 마이그레이션을 시작하기 전에 수행해야 할 변경을 검토합니다.
> * 마이그레이션 후 드라이브 문자가 유지되도록 설정을 구성합니다.
> * 마이그레이션 후 Azure VM에 연결할 수 있도록 머신을 준비합니다.

## <a name="verify-migration-limitations"></a>마이그레이션 제한 사항 확인

- Azure Migrate 서버 마이그레이션을 사용하여 단일 Azure Migrate 프로젝트에서 최대 35,000개의 VMware VM/Hyper-V VM을 평가할 수 있습니다. 프로젝트는 VMware VM과 Hyper-V VM을 각각 최대 한도까지 결합할 수 있습니다.
- 마이그레이션을 위해 한 번에 최대 10개의 VM을 선택할 수 있습니다. 더 많이 복제해야 하는 경우 10개의 그룹 단위로 복제합니다.
- VMware 에이전트리스 마이그레이션의 경우 최대 100개의 복제를 동시에 실행할 수 있습니다.

## <a name="verify-operating-system-requirements"></a>운영 체제 요구 사항 확인

- Azure에서 [Windows 운영 체제](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)가 지원되는지 확인합니다.
- Azure에서 [Linux 배포판](../virtual-machines/linux/endorsed-distros.md)이 지원되는지 확인합니다.

## <a name="see-whats-supported"></a>지원되는 기능 보기

VMware VM의 경우 서버 마이그레이션은 [에이전트 없는 또는 에이전트 기반 마이그레이션](server-migrate-overview.md)을 지원합니다.

- **VMware VM**: VMware VM에 대한 [마이그레이션 요구 사항 및 지원](migrate-support-matrix-vmware-migration.md)을 확인합니다.
- **Hyper-V VM**: Hyper-V VM에 대한 [마이그레이션 요구 사항 및 지원](migrate-support-matrix-hyper-v-migration.md)을 확인합니다.
- **물리적 머신**: 온-프레미스 물리적 머신 및 다른 가상화된 서버에 대한 [마이그레이션 요구 사항 및 지원](migrate-support-matrix-physical-migration.md)을 확인합니다. 

## <a name="review-url-and-port-access"></a>URL 및 포트 액세스 검토

마이그레이션하는 동안 머신에 인터넷 액세스가 필요할 수 있습니다.

- **Azure Migrate 어플라이언스**: 에이전트 없는 마이그레이션 중에 Azure Migrate 어플라이언스에서 액세스해야 하는 [URL](migrate-appliance.md#url-access) 및 [포트](migrate-support-matrix-vmware-migration.md#agentless-ports)를 검토합니다.
- **VMware VM 에이전트 기반 마이그레이션**: VMware VM 에이전트 기반 마이그레이션 중에 복제 어플라이언스에서 사용하는 [URL](migrate-replication-appliance.md#url-access) 및 [포트](migrate-replication-appliance.md#port-access)를 검토합니다. 
- **Hyper-V 호스트**: 마이그레이션 중에 Hyper-V 호스트에서 액세스해야 하는 [URL 및 포트](migrate-support-matrix-hyper-v-migration.md#hyper-v-hosts)를 검토합니다. 
- **실제 서버**: 물리적 서버 마이그레이션 중에 어플라이언스에서 사용하는 [URL](migrate-replication-appliance.md#url-access) 및 [포트](migrate-replication-appliance.md#port-access)를 검토합니다.

## <a name="verify-required-changes-before-migrating"></a>마이그레이션 전 필요한 변경 확인

일부 VM은 Azure에서 실행될 수 있도록 변경해야 할 수도 있습니다. Azure Migrate는 다음 운영 체제를 실행하는 VM에 대해 이러한 변경을 자동으로 수행합니다.

- Red Hat Enterprise Linux 7.0 이상, 6.5 이상
- CentOS 7.0 이상, 6.5 이상
- SUSE Linux Enterprise Server 12 SP1 이상
- Ubuntu 18.04LTS, 16.04LTS, 14.04LTS
- Debian 8, 7

다른 운영 체제의 경우 마이그레이션하기 전에 머신을 수동으로 준비해야 합니다. 

### <a name="prepare-windows-machines"></a>Windows 머신 준비

Windows 머신을 마이그레이션하는 경우 마이그레이션하기 전에 다음과 같이 변경합니다. 변경하기 전에 VM을 마이그레이션하는 경우 Azure에서 해당 VM이 부팅되지 않을 수 있습니다.

1. [Azure 직렬 콘솔을 Azure VM에 사용하도록 설정합니다](../virtual-machines/troubleshooting/serial-console-windows.md). 콘솔을 사용하도록 설정하면 문제를 해결할 수 있습니다. VM을 다시 부팅할 필요가 없습니다. Azure VM은 디스크 이미지를 사용하여 부팅됩니다. 디스크 이미지 부팅은 새 VM을 다시 부팅하는 것과 같습니다. 
2. Windows Server 2003을 실행하는 머신을 마이그레이션하는 경우 Hyper-V Guest Integration Services를 VM 운영 체제에 설치합니다. [자세히 알아보기](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#install-or-update-integration-services).

### <a name="prepare-linux-machines"></a>Linux 머신 준비

1. Hyper-V Linux Integration Services를 설치합니다. 대부분의 최신 Linux 배포 버전에는 기본적으로 Hyper-V Linux Integration Services가 포함되어 있습니다.
2. 필요한 Hyper-V 드라이버가 포함되도록 Linux 초기화 이미지를 다시 빌드합니다. 초기화 이미지가 다시 빌드되면 VM이 Azure에서 부팅됩니다(일부 배포에만 필요).
3. [Azure 직렬 콘솔 로깅을 사용하도록 설정합니다](../virtual-machines/troubleshooting/serial-console-linux.md). 콘솔 로깅을 사용하도록 설정하면 문제를 해결할 수 있습니다. VM을 다시 부팅할 필요가 없습니다. Azure VM은 디스크 이미지를 사용하여 부팅됩니다. 디스크 이미지 부팅은 새 VM을 다시 부팅하는 것과 같습니다.
4. 영구 디바이스 식별자를 사용하도록 디바이스 맵 파일을 디바이스 이름-볼륨 연결로 업데이트합니다.
5. 영구 볼륨 식별자를 사용하도록 fstab 파일의 항목을 업데이트합니다.
6. MAC 주소 등을 기준으로 인터페이스 이름을 예약하는 udev 규칙을 제거합니다.
7. DHCP에서 IP 주소를 받도록 네트워크 인터페이스를 업데이트합니다.

[Azure에서 Linux VM을 실행하는 단계](../virtual-machines/linux/create-upload-generic.md)에 대해 자세히 알아보고, 인기 있는 Linux 배포 중 일부에 대한 지침을 확인합니다.

## <a name="preserve-drive-letters-after-migration"></a>마이그레이션 후 드라이브 문자 유지

온-프레미스 머신을 Microsoft Azure로 마이그레이션하면 추가 데이터 디스크의 드라이브 문자가 원래 값에서 변경될 수 있습니다. 

Azure VM에는 기본적으로 임시 스토리지로 사용할 D 드라이브가 할당됩니다. 이 드라이브 할당으로 인해 다른 모든 스토리지 드라이브 할당이 한 문자씩 증가합니다. 예를 들어 온-프레미스 설치에서 애플리케이션 설치를 위해 D 드라이브에 할당된 데이터 디스크를 사용하는 경우 VM을 Azure로 마이그레이션하면 이 드라이브에 대한 할당이 E 드라이브로 증가합니다. 이 자동 할당을 방지하고 Azure에서 사용 가능한 다음 드라이브 문자를 임시 볼륨에 할당하도록 하려면 SAN(저장 영역 네트워크) 정책을 **OnlineAll**로 설정합니다.

1. 온-프레미스 머신(호스트 서버가 아님)에서 관리자 권한 명령 프롬프트를 엽니다.
2. **diskpart**를 입력합니다.
3. **SAN**을 입력합니다. 게스트 운영 체제의 드라이브 문자가 유지되지 않으면 **모두 오프라인** 또는 **공유 디스크만 오프라인**이 반환됩니다.
4. **DISKPART** 프롬프트에서 **SAN Policy=OnlineAll**을 입력합니다. 이 설정을 사용하면 디스크가 온라인 상태가 되고 두 디스크를 모두 읽고 쓸 수 있습니다.
5. 테스트 마이그레이션 중에 드라이브 문자가 유지되는지 확인할 수 있습니다.

## <a name="check-azure-vm-requirements"></a>Azure VM 요구 사항 확인

Azure에 복제하는 온-프레미스 머신은 운영 체제와 아키텍처, 디스크, 네트워크 설정 및 VM 명명에 대한 Azure VM 요구 사항을 준수해야 합니다. 마이그레이션하기 전에 [VMware VM, 물리적 서버](migrate-support-matrix-vmware-migration.md#azure-vm-requirements) 및 [Hyper-V VM](migrate-support-matrix-hyper-v-migration.md#azure-vm-requirements)에 대한 요구 사항을 확인합니다.

## <a name="prepare-to-connect-after-migration"></a>마이그레이션 후 연결 준비

Azure VM은 Azure로 마이그레이션하는 동안 만들어집니다. 마이그레이션 후에는 새 Azure VM에 연결할 수 있어야 합니다. 성공적으로 연결하려면 여러 단계를 수행해야 합니다.

### <a name="prepare-to-connect-to-azure-windows-vms"></a>Azure Windows VM에 대한 연결 준비

온-프레미스 Windows 머신의 경우:

1. Windows 설정을 구성합니다. 설정에는 정적 영구 경로 또는 WinHTTP 프록시를 제거하는 작업이 포함됩니다.
2. [필요한 서비스](../virtual-machines/windows/prepare-for-upload-vhd-image.md#check-the-windows-services)가 실행되고 있는지 확인합니다.
3. 원격 데스크톱(RDP)을 사용하도록 설정하여 온-프레미스 머신에 대한 원격 연결을 허용합니다. [PowerShell을 통해 RDP를 사용하도록 설정](../virtual-machines/windows/prepare-for-upload-vhd-image.md#update-remote-desktop-registry-settings)하는 방법에 대해 알아보세요.
4. 마이그레이션 후 인터넷을 통해 Azure VM에 액세스하려면 온-프레미스 머신의 Windows Firewall에서 퍼블릭 프로필의 TCP 및 UDP를 허용하고 RDP를 모든 프로필에 대해 허용되는 앱으로 설정합니다.
5. 마이그레이션 후 사이트 간 VPN을 통해 Azure VM에 액세스하려면 온-프레미스 머신의 Windows Firewall에서 도메인 및 프라이빗 프로필에 대해 RDP를 허용합니다. [RDP 트래픽을 허용](../virtual-machines/windows/prepare-for-upload-vhd-image.md#configure-windows-firewall-rules)하는 방법에 대해 알아보세요. 
6. 마이그레이션할 때 온-프레미스 VM에서 보류 중인 Windows 업데이트가 없는지 확인합니다. 업데이트가 있는 경우 마이그레이션 후에 해당 업데이트를 Azure VM에 설치할 수 있으며, 업데이트가 완료될 때까지 VM에 로그인할 수 없습니다.

### <a name="prepare-to-connect-with-linux-azure-vms"></a>Linux Azure VM에 대한 연결 준비

온-프레미스 Linux 머신의 경우:

1. 시스템 부팅 시 Secure Shell 서비스가 자동으로 시작되도록 설정되어 있는지 확인합니다.
2. 방화벽 규칙에서 SSH 연결을 허용하는지 확인합니다.

### <a name="configure-azure-vms-after-migration"></a>마이그레이션 후 Azure VM 구성

마이그레이션 후 만들어진 Azure VM에서 다음 단계를 완료합니다.

1. 인터넷을 통해 VM에 연결하려면 공용 IP 주소를 VM에 할당합니다. 온-프레미스 머신에 사용한 것과 다른 공용 IP 주소를 Azure VM에 사용해야 합니다. [자세히 알아보기](../virtual-network/virtual-network-public-ip-address.md).
2. VM의 NSG(네트워크 보안 그룹) 규칙에서 RDP 또는 SSH 포트로 들어오는 연결을 허용하는지 확인합니다.
3. [부트 진단](../virtual-machines/troubleshooting/boot-diagnostics.md#enable-boot-diagnostics-on-existing-virtual-machine)을 확인하여 VM을 살펴봅니다.

> [!NOTE]
> Azure Bastion 서비스는 Azure VM에 대한 프라이빗 RDP 및 SSH 액세스를 제공합니다. 이 서비스에 대해 [자세히 알아보세요](../bastion/bastion-overview.md).

## <a name="next-steps"></a>다음 단계

[VMware VM을 Azure로 마이그레이션](server-migrate-overview.md)하는 데 사용하려는 방법을 결정하거나, [Hyper-V VM](tutorial-migrate-hyper-v.md), [물리적 서버, 가상화된 VM 또는 클라우드 VM](tutorial-migrate-physical-virtual-machines.md)의 마이그레이션을 시작합니다.
