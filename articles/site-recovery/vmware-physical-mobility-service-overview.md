---
title: Azure Site Recovery를 사용한 VMware VM 및 물리적 서버 재해 복구를 위한 Mobility 서비스 정보 | Microsoft Docs
description: Azure Site Recovery 서비스를 사용한 VMware VM 및 물리적 서버와 Azure 간 재해 복구를 위한 Mobility 서비스 에이전트 설치에 대해 알아봅니다.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: ramamill
ms.openlocfilehash: e3967319cd41399209bd50886bce88efc8ba6ba6
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/06/2018
ms.locfileid: "52956519"
---
# <a name="about-the-mobility-service-for-vmware-vms-and-physical-servers"></a>VMware VM 및 물리적 서버의 재해 복구를 위한 Mobility 서비스

[Azure Site Recovery](site-recovery-overview.md)를 사용하여 VMware VM 및 물리적 서버에 대한 재해 복구를 설정할 경우 각 온-프레미스 VMware VM 및 물리적 서버에 Site Recovery Mobility 서비스를 설치합니다.  Mobility 서비스는 머신에 기록된 데이터를 캡처하고 이를 Site Recovery 프로세스 서버에 전달합니다. 다음 메서드를 사용하여 Mobility 서비스를 설치할 수 있습니다.

[푸시 설치](vmware-azure-install-mobility-service.md): Site Recovery를 구성하여 Mobility 서비스의 푸시 설치를 수행합니다. 이를 위해 재해 복구를 설정할 때 Site Recovery 프로세스 서버가 서비스 설치를 위해 VM 또는 물리적 서버에 액세스하는 데 사용할 수 있는 계정도 설정합니다.
[수동으로 설치](vmware-physical-mobility-service-install-manual.md): UI 또는 명령 프롬프트를 사용하여 각 머신에서 수동으로 Mobility 서비스를 설치할 수 있습니다.
[자동화된 배포](vmware-azure-mobility-install-configuration-mgr.md): System Center Configuration Manager 같은 소프트웨어 배포 도구를 사용하여 설치를 자동화할 수 있습니다.

## <a name="azure-virtual-machine-agent"></a>Azure Virtual Machine 에이전트

- **Windows VM**: Mobility 서비스 버전 9.7.0.0부터 Mobility 서비스 설치 관리자가 [Azure VM 에이전트](../virtual-machines/extensions/features-windows.md#azure-vm-agent)를 설치합니다. 이렇게 하면 머신이 Azure로 장애 조치되는 경우에는 Azure VM이 VM 확장 사용을 위한 에이전트 설치 필수 조건을 충족하게 됩니다.
- **Linux VM**: 장애 조치(failover) 후 [WALinuxAgent](https://docs.microsoft.com/azure/virtual-machines/extensions/update-linux-agent)를 Azure VM에 수동으로 설치해야 합니다.

## <a name="installer-files"></a>설치 관리자 파일

표에서는 각 VMware VM 및 물리적 서버 운영 체제에 대한 설치 관리자 파일을 요약합니다. 시작하기 전에 [지원되는 운영 체제](vmware-physical-azure-support-matrix.md#replicated-machines)를 검토할 수 있습니다.


**설치 관리자 파일** | **운영 체제(64비트만 해당)** 
--- | ---
Microsoft-ASR\_UA\*Windows\*release.exe | Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1 
Microsoft-ASR\_UA\*RHEL6-64\*release.tar.gz | RHEL(Red Hat Enterprise Linux) 6.* </br> CentOS 6.*
Microsoft-ASR\_UA\*RHEL7-64\*release.tar.gz | RHEL(Red Hat Enterprise Linux) 7.* </br> CentOS 7.* 
Microsoft-ASR\_UA\*SLES12-64\*release.tar.gz | SUSE Linux Enterprise Server 12 SP1, SP2, SP3 
Microsoft-ASR\_UA\*SLES11-SP3-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP3 
Microsoft-ASR\_UA\*SLES11-SP4-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP4 
Microsoft-ASR\_UA\*OL6-64\*release.tar.gz | Oracle Enterprise Linux 6.4, 6.5
Microsoft-ASR\_UA\*UBUNTU-14.04-64\*release.tar.gz | Ubuntu Linux 14.04
Microsoft-ASR\_UA\*UBUNTU-16.04-64\*release.tar.gz | Ubuntu Linux 16.04 LTS 서버
Microsoft-ASR_UA\*DEBIAN7-64\*release.tar.gz | Debian 7 
Microsoft-ASR_UA\*DEBIAN8-64\*release.tar.gz | Debian 8

## <a name="anti-virus-on-replicated-machines"></a>복제된 머신에서 바이러스 백신

복제하려는 머신에 활성 바이러스 백신 소프트웨어가 실행 중이면 바이러스 백신 작업에서 Mobility 서비스 설치 폴더를 제외해야 합니다(*C:\ProgramData\ASR\agent*). 이렇게 하면 복제가 예상대로 작동합니다.

## <a name="update-the-mobility-service"></a>Mobility 서비스 업데이트

1. 시작에 앞서, 보호되는 머신에서 Mobility 서비스를 업데이트하기 전에 배포의 일부인 구성 서버, 확장 프로세스 서버 및 마스터 대상 서버를 업데이트했는지 확인하세요.
2. 포털에서 자격 증명 모음 > **복제된 항목**을 엽니다.
3. 구성 서버를 최신 버전으로 업데이트한 경우 “새 Site Recovery 복제 에이전트 업데이트를 사용할 수 있습니다. 설치하려면 클릭하세요.”라는 알림이 표시됩니다.

     ![복제된 항목 창](./media/vmware-azure-install-mobility-service/replicated-item-notif.png)

4. 알림을 클릭하고 **에이전트 업데이트**에서 Mobility 서비스를 업그레이드하려는 머신을 선택합니다. 그런 후 **OK**를 클릭합니다.

     ![복제된 항목 VM 목록](./media/vmware-azure-install-mobility-service/update-okpng.png)

5. 선택한 머신 각각에 대한 Mobility Service 업데이트 작업이 시작됩니다.

## <a name="update-the-acount-used-for-push-installation-of-the-mobility-service"></a>Mobility 서비스의 푸시 설치에 사용되는 계정 업데이트

Site Recovery를 배포하여 Mobility 서비스의 푸시 설치를 사용하도록 설정한 경우, 복제가 머신에 대해 사용하도록 설정되었을 때 Site Recovery 프로세스 서버가 머신에 액세스하여 서비스를 설치하는 데 사용하는 계정을 지정했습니다. 이 계정에 대한 자격 증명을 업데이트하려면 [이 지침](vmware-azure-manage-configuration-server.md)에 따릅니다.

## <a name="uninstall-the-mobility-service"></a>모바일 서비스 설치 제거

### <a name="on-a-windows-machine"></a>Windows 머신

UI 또는 명령 프롬프트에서 제거합니다.

- **UI에서**: 머신의 제어판에서 **프로그램**을 선택합니다. **Microsoft Azure Site Recovery Mobility 서비스/마스터 대상 서버** > **제거**를 선택합니다.
- **명령 프롬프트에서**: 머신에서 명령 프롬프트 창을 관리자로 엽니다. 다음 명령 실행: 
    ```
    MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
    ```

## <a name="on-a-linux-machine"></a>Linux 머신
1. Linux 머신에서 **루트** 사용자로 로그인합니다.
2. 터미널에서 /user/local/ASR로 이동합니다.
3. 다음 명령 실행:

    ```
    uninstall.sh -Y
    ```

## <a name="next-steps"></a>다음 단계

[Mobility 서비스에 대한 푸시 설치를 설정합니다](vmware-azure-install-mobility-service.md).
