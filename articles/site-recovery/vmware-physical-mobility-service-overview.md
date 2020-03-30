---
title: Azure Site Recovery를 사용한 VMware VM 및 물리적 서버 재해 복구를 위한 Mobility 서비스 정보 | Microsoft Docs
description: Azure Site Recovery 서비스를 사용한 VMware VM 및 물리적 서버와 Azure 간 재해 복구를 위한 Mobility 서비스 에이전트 설치에 대해 알아봅니다.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: c5acc9637fe5afe8f7dd32d23fbdbb80373b4f61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256836"
---
# <a name="about-the-mobility-service-for-vmware-vms-and-physical-servers"></a>VMware VM 및 물리적 서버의 재해 복구를 위한 Mobility 서비스

[Azure Site Recovery](site-recovery-overview.md)를 사용하여 VMware VM 및 물리적 서버에 대한 재해 복구를 설정할 경우 각 온-프레미스 VMware VM 및 물리적 서버에 Site Recovery Mobility 서비스를 설치합니다.  Mobility 서비스는 머신에 기록된 데이터를 캡처하고 이를 Site Recovery 프로세스 서버에 전달합니다. 다음 메서드를 사용하여 Mobility 서비스를 설치할 수 있습니다.

- [푸시 설치](#push-installation): 사이트 복구는 Azure 포털을 통해 보호가 활성화되면 서버에 이동성 에이전트를 설치합니다.
- 수동으로 설치: [UI](#install-mobility-agent-through-ui) 또는 [명령 프롬프트를](#install-mobility-agent-through-command-prompt)통해 각 컴퓨터에 수동으로 Mobility 서비스를 설치할 수 있습니다.
- [자동 배포](vmware-azure-mobility-install-configuration-mgr.md): Configuration Manager와 같은 소프트웨어 배포 도구를 사용하여 설치를 자동화할 수 있습니다.

> [!NOTE]
> 모빌리티 에이전트는 VMware VM 또는 물리적 컴퓨터의 소스 컴퓨터에서 약 6%-10%의 메모리를 사용합니다.

## <a name="anti-virus-on-replicated-machines"></a>복제된 머신에서 바이러스 백신

복제하려는 머신에 활성 바이러스 백신 소프트웨어가 실행 중이면 바이러스 백신 작업에서 Mobility 서비스 설치 폴더를 제외해야 합니다(*C:\ProgramData\ASR\agent*). 이렇게 하면 복제가 예상대로 작동합니다.

## <a name="push-installation"></a>푸시 설치

푸시 설치는 포털에서 트리거된["복제 활성화"](vmware-azure-enable-replication.md#enable-replication)작업의 필수적인 부분입니다. 보호하려는 가상 컴퓨터 집합을 선택하고 "복제 사용"을 트리거한 구성 서버는 모빌리티 에이전트를 서버에 푸시하고 에이전트를 설치하고 구성 서버를 사용하여 에이전트를 완전히 등록합니다. 이 작업을 성공적으로 완료하려면

- 모든 푸시 설치 [전제 조건이](vmware-azure-install-mobility-service.md) 충족되었는지 확인합니다.
- 서버의 모든 구성이 [VMware에서 Azure DR 시나리오에 대한 지원 매트릭스에](vmware-physical-azure-support-matrix.md)속하는지 확인합니다.

푸시 설치 워크플로우에 대한 자세한 내용은 다음 섹션에 설명되어 있습니다.

### <a name="from-923-version-onwards"></a>[이후 9.23 버전에서](https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery)

이동성 에이전트를 푸시 설치하는 동안 다음 단계가 수행됩니다.

1. 에이전트를 소스 컴퓨터에 푸시합니다. 여러 환경 오류로 인해 에이전트를 소스 컴퓨터에 복사하는 데 실패할 수 있습니다. 푸시 설치 실패 문제를 해결하려면 [지침을](vmware-azure-troubleshoot-push-install.md) 방문하십시오.
2. 에이전트가 서버에 성공적으로 복사된 후 서버에서 필수 구성 조건 검사가 수행됩니다. 하나 이상의 필수 구성 [조건이](vmware-physical-azure-support-matrix.md) 충족되지 않으면 설치가 실패합니다. 모든 필수 구성 조건이 충족되면 설치가 트리거됩니다.
3. Azure 사이트 복구 VSS 공급자는 이동성 에이전트 설치의 일부로 서버에 설치됩니다. 이 공급자는 응용 프로그램 일관된 지점을 생성하는 데 사용됩니다. VSS 공급자 설치에 실패하면 이 단계를 건너뛰고 에이전트 설치가 계속됩니다.
4. 에이전트 설치가 성공했지만 VSS 공급자 설치가 실패하면 작업 상태가 "경고"로 표시됩니다. 이는 크래시 일관성 포인트 생성에 영향을 미치지 않습니다.

    a. 응용 프로그램 일관된 포인트를 생성하려면 사이트 복구 VSS 공급자를 수동으로 설치하려면 [지침을](vmware-physical-manage-mobility-service.md#install-site-recovery-vss-provider-on-source-machine) 참조하십시오. </br>
    b.  응용 프로그램 일관된 포인트가 생성되지 않으려면 [복제 정책을 수정하여](vmware-azure-set-up-replication.md#create-a-policy) 응용 프로그램 일관된 점을 해제합니다.

### <a name="before-922-versions"></a>9.22 버전 이전

1. 에이전트를 소스 컴퓨터에 푸시합니다. 여러 환경 오류로 인해 에이전트를 소스 컴퓨터에 복사하는 데 실패할 수 있습니다. 푸시 설치 실패 문제를 해결하려면 [지침을](vmware-azure-troubleshoot-push-install.md) 방문하십시오.
2. 에이전트가 서버에 성공적으로 복사된 후 서버에서 필수 구성 조건 검사가 수행됩니다. 하나 이상의 필수 구성 [조건이](vmware-physical-azure-support-matrix.md) 충족되지 않으면 설치가 실패합니다. 모든 필수 구성 조건이 충족되면 설치가 트리거됩니다.
3. Azure 사이트 복구 VSS 공급자는 이동성 에이전트 설치의 일부로 서버에 설치됩니다. 이 공급자는 응용 프로그램 일관된 지점을 생성하는 데 사용됩니다. VSS 공급자설치에 실패하면 에이전트 설치가 실패합니다. 이동성 에이전트 설치 실패를 방지하려면 [9.23 버전](https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery) 이상에서 충돌 일관된 지점을 생성하고 VSS 공급자를 수동으로 설치하십시오.

## <a name="install-mobility-agent-through-ui"></a>UI를 통한 모빌리티 에이전트 설치

### <a name="prerequisite"></a>필수 요소

- 서버의 모든 구성이 [VMware에서 Azure DR 시나리오에 대한 지원 매트릭스에](vmware-physical-azure-support-matrix.md)속하는지 확인합니다.
- 서버의 운영 체제에 따라 [설치 관리자를 찾습니다.](#locate-installer-files)

>[!IMPORTANT]
> Azure IaaS VM을 한 Azure 리전에서 다른 Azure 리전으로 복제하는 경우 이 메서드를 사용하지 마십시오. 대신 명령줄 기반 설치 방법을 사용합니다.

1. 머신에 설치 파일을 복사하고 실행합니다.
2. **설치 옵션**에서 **Mobility 서비스 설치**를 선택합니다.
3. 설치 위치 > **설치**를 선택합니다.

    ![Mobility Service 설치 옵션 페이지](./media/vmware-physical-mobility-service-install-manual/mobility1.png)

4. **설치 진행률**에서 설치를 모니터링합니다. 설치가 완료되면 **구성으로 계속 진행**을 선택하여 Mobility 서비스를 구성 서버에 등록합니다.

    ![Mobility Service 등록 페이지](./media/vmware-physical-mobility-service-install-manual/mobility3.png)

5. **구성 서버 세부 정보에서**구성한 IP 주소와 암호를 지정합니다.

    ![Mobility Service 등록 페이지](./media/vmware-physical-mobility-service-install-manual/mobility4.png)

6. **등록**을 선택하여 등록을 완료합니다.

    ![Mobility Service 등록 최종 페이지](./media/vmware-physical-mobility-service-install-manual/mobility5.png)

## <a name="install-mobility-agent-through-command-prompt"></a>명령 프롬프트를 통해 이동성 에이전트 설치

### <a name="prerequisite"></a>필수 요소

- 서버의 모든 구성이 [VMware에서 Azure DR 시나리오에 대한 지원 매트릭스에](vmware-physical-azure-support-matrix.md)속하는지 확인합니다.
- 서버의 운영 체제에 따라 [설치 관리자를 찾습니다.](#locate-installer-files)

### <a name="on-a-windows-machine"></a>Windows 머신

- 보호하려는 서버의 로컬 폴더(예: C:\Temp)에 설치 관리자를 복사합니다.

    ```
    cd C:\Temp
    ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
    MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
    cd C:\Temp\Extracted
    ```

- 다음과 같이 설치합니다.

    ```
    UnifiedAgent.exe /Role "MS" /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
    ```

- 구성 서버에 에이전트를 등록합니다.

    ```
    cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
    UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
    ```

#### <a name="installation-settings"></a>설치 설정
**설정** | **세부 정보**
--- | ---
사용 | UnifiedAgent.exe /역할 \<MS/MT> \</설치 위치 설치 위치> /플랫폼 "VmWare" /침묵
설정 로그 | %ProgramData%\ASRSetupLogs\ASRUnifiedAgentInstaller.log에 있습니다.
/Role | 필수 설치 매개 변수입니다. MS(Mobility 서비스) 또는 MT(마스터 대상) 설치 여부를 지정합니다.
/InstallLocation| 선택적 매개 변수. Mobility 서비스 설치 위치(모든 폴더)를 지정합니다.
/Platform | 필수. Mobility Service가 설치되는 플랫폼을 지정합니다. VMware VM/물리적 서버에 **VMware**, Azure VM에 **Azure**입니다.<br/><br/> Azure VM을 물리적 컴퓨터로 처리하는 경우 **VMware를**지정합니다.
/Silent| (선택 사항) 자동 모드에서의 설치 관리자 실행 여부를 지정합니다.

#### <a name="registration-settings"></a>등록 설정
**설정** | **세부 정보**
--- | ---
사용 | 통합에이전트구성기자/CSEndPoint \<CSIP> /패스프레이즈FilePath \<패스프레이즈파일패스>
에이전트 구성 로그 | %ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log에 있습니다.
/CSEndPoint | 필수 매개 변수입니다. 구성 서버의 IP 주소를 지정합니다. 유효한 IP 주소를 사용합니다.
/PassphraseFilePath |  필수. 암호의 위치입니다. 유효한 UNC 또는 로컬 파일 경로를 사용합니다.

### <a name="on-a-linux-machine"></a>Linux 머신

1. 보호하려는 서버의 로컬 폴더(예: /tmp)에 설치 관리자를 복사합니다. 터미널에서 다음 명령을 실행합니다.

    ```
    cd /tmp ;
    tar -xvf Microsoft-ASR_UA*release.tar.gz
    ```

2. 다음과 같이 설치합니다.

    ```
    sudo ./install -d <Install Location> -r MS -v VmWare -q
    ```

3. 설치가 완료되면 Mobility Service를 구성 서버에 등록해야 합니다. 다음 명령을 실행하여 Mobility Service를 구성 서버에 등록합니다.

    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
    ```

#### <a name="installation-settings"></a>설치 설정
**설정** | **세부 정보**
--- | ---
사용 | ./설치 -d \<설치 위치 \<> -r MS/MT> -vmWare -q
-r | 필수 설치 매개 변수입니다. MS(Mobility 서비스) 또는 MT(마스터 대상) 설치 여부를 지정합니다.
-d | 선택적 매개 변수. Mobility 서비스 설치 위치(/usr/local/ASR)를 지정합니다.
-v | 필수. Mobility Service가 설치되는 플랫폼을 지정합니다. VMware VM/물리적 서버에 **VMware**, Azure VM에 **Azure**입니다.
-Q | (선택 사항) 자동 모드에서의 설치 관리자 실행 여부를 지정합니다.

#### <a name="registration-settings"></a>등록 설정
**설정** | **세부 정보**
--- | ---
사용 | cd /usr/local/ASR/Vx/bin<br/><br/> UnifiedAgentConfigurator.sh -i \<CSIP \<> -P 암호 파일 패스>
-i | 필수 매개 변수입니다. 구성 서버의 IP 주소를 지정합니다. 유효한 IP 주소를 사용합니다.
-P |  필수. 암호가 저장되는 파일의 전체 파일 경로입니다. 유효한 폴더를 사용합니다.

## <a name="azure-virtual-machine-agent"></a>Azure Virtual Machine 에이전트

- **Windows VM**: Mobility 서비스 버전 9.7.0.0부터 [Azure VM 에이전트](../virtual-machines/extensions/features-windows.md#azure-vm-agent)를 Mobility 서비스 설치 관리자가 설치합니다. 이렇게 하면 머신이 Azure로 장애 조치되는 경우에는 Azure VM이 VM 확장 사용을 위한 에이전트 설치 필수 조건을 충족하게 됩니다.
- **Linux VM**: 장애 조치(failover) 후 [WALinuxAgent](https://docs.microsoft.com/azure/virtual-machines/extensions/update-linux-agent)가 Azure VM에 수동으로 설치되어야 합니다.

## <a name="locate-installer-files"></a>설치 프로그램 파일 찾기

구성 서버에서 %ProgramData%\ASR\home\svsystems\pushinstallsvc\저장소 폴더로 이동합니다. 운영 체제에 따라 필요한 설치 프로그램을 확인합니다. 다음 표에는 각 VMware VM 및 물리적 서버 운영 체제에 대한 설치 관리자 파일이 요약되어 있습니다. 시작하기 전에 [지원되는 운영 체제](vmware-physical-azure-support-matrix.md#replicated-machines)를 검토할 수 있습니다.

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

## <a name="next-steps"></a>다음 단계

[Mobility 서비스에 대한 푸시 설치를 설정합니다](vmware-azure-install-mobility-service.md).
