---
title: Azure Site Recovery를 사용하는 VMware VM 및 물리적 서버의 재해 복구를 위한 Mobility Service 정보 | Microsoft Docs
description: Azure Site Recovery 서비스를 사용하는 VMware VM 및 물리적 서버와 Azure 간 재해 복구를 위한 Mobility Service 에이전트에 대해 알아봅니다.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: how-to
ms.date: 04/10/2020
ms.author: ramamill
ms.openlocfilehash: 8c90427e2dd4ecf23cb803c4f75dbee34c38f992
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98898582"
---
# <a name="about-the-mobility-service-for-vmware-vms-and-physical-servers"></a>VMware VM 및 물리적 서버의 재해 복구를 위한 Mobility 서비스

[Azure Site Recovery](site-recovery-overview.md)를 사용하여 VMware VM(가상 머신) 및 물리적 서버에 대한 재해 복구를 설정할 때 각 온-프레미스 VMware VM 및 물리적 서버에 Site Recovery Mobility Service를 설치합니다. Mobility 서비스는 머신에 기록된 데이터를 캡처하고 이를 Site Recovery 프로세스 서버에 전달합니다. 다음 방법으로 배포할 수 있는 Mobility Service 에이전트 소프트웨어를 통해 Mobility Service가 설치됩니다.

- [푸시 설치](#push-installation): Azure Portal을 통해 보호를 사용하도록 설정하면 Site Recovery에서 Mobility Service를 서버에 설치합니다.
- 수동 설치: [UI(사용자 인터페이스)](#install-the-mobility-service-using-ui) 또는 [명령 프롬프트](#install-the-mobility-service-using-command-prompt)를 사용 여 각 컴퓨터에 Mobility Service를 수동으로 설치할 수 있습니다.
- [자동 배포](vmware-azure-mobility-install-configuration-mgr.md): Configuration Manager와 같은 소프트웨어 배포 도구를 사용하여 Mobility Service 설치를 자동화할 수 있습니다.

> [!NOTE]
> Mobility Service는 VMware VM 또는 물리적 컴퓨터에 대한 원본 컴퓨터에서 약 6%-10%의 메모리를 사용합니다.

## <a name="antivirus-on-replicated-machines"></a>복제된 컴퓨터의 바이러스 백신

복제하려는 컴퓨터에서 바이러스 백신 소프트웨어를 실행 중인 경우에는 바이러스 백신 작업에서 Mobility Service의 설치 폴더(_C:\ProgramData\ASR\agent_)를 제외합니다. 그러면 복제가 예상대로 진행될 것입니다.

## <a name="push-installation"></a>푸시 설치

[복제를 사용하도록 설정](vmware-azure-enable-replication.md#enable-replication)하려면 Azure Portal에서 작업을 실행할 때 반드시 푸시 설치를 해야 합니다. 복제를 보호하고 사용하도록 설정할 VM 집합을 선택하면 구성 서버가 Mobility Service 에이전트를 서버에 푸시하고, 에이전트를 설치하고, 구성 서버에 대한 에이전트 등록을 완료합니다.

### <a name="prerequisites"></a>사전 요구 사항

- 모든 푸시 설치의 [사전 요구 사항](vmware-azure-install-mobility-service.md)이 충족되었는지 확인합니다.
- 모든 서버 구성이 [Azure에 대한 VMware VM 및 물리적 서버 재해 복구를 위한 지원 매트릭스](vmware-physical-azure-support-matrix.md)에 명시된 조건을 충족하는지 확인합니다.
- 9\.36 버전 이상, SUSE Linux Enterprise Server 11 SP3, RHEL 5, CentOS 5, Debian 7의 경우 [구성 서버 및 스케일 아웃 프로세스 서버에서 최신 설치 프로그램을 사용](#download-latest-mobility-agent-installer-for-suse-11-sp3-rhel-5-debian-7-server)할 수 있는지 확인합니다.

푸시 설치 워크플로는 다음 섹션에 설명되어 있습니다.

### <a name="mobility-service-agent-version-923-and-higher"></a>Mobility Service 에이전트 버전 9.23 이상

버전 9.23에 대한 자세한 내용은 [Azure Site Recovery - 업데이트 롤업 35](https://support.microsoft.com/help/4494485/update-rollup-35-for-azure-site-recovery)를 참조하세요.

Mobility Service를 푸시 설치하는 동안 다음 단계가 수행됩니다.

1. 에이전트가 원본 컴퓨터에 푸시됩니다. 여러 환경 오류로 인해 에이전트를 원본 컴퓨터에 복사하지 못할 수 있습니다. 푸시 설치 오류 문제를 해결하려면 [지침](vmware-azure-troubleshoot-push-install.md)을 참조하세요.
1. 에이전트가 서버에 성공적으로 복사되면 서버에서 사전 요구 사항을 확인합니다.
   - 모든 사전 요구 사항이 충족되면 설치를 시작합니다.
   - 하나 이상의 [사전 요구 사항](vmware-physical-azure-support-matrix.md)이 충족되지 않으면 설치가 진행되지 않습니다.
1. 에이전트 설치 중에는 Azure Site Recovery용 VSS(볼륨 섀도 복사본 서비스) 공급자도 설치됩니다. VSS 공급자는 애플리케이션 일관성 복구 지점을 생성하는 데 사용됩니다. VSS 공급자 설치에 실패하는 경우, 이 단계를 건너뛰고 에이전트 설치를 계속합니다.
1. 에이전트는 설치되었지만 VSS 공급자는 설치되지 않은 경우, 작업 상태가 **경고** 로 표시됩니다. 이는 크래시 일관성 복구 지점을 생성하는 데 영향을 주지 않습니다.

    - 애플리케이션 일관성 복구 지점을 생성하려면[지침](vmware-physical-manage-mobility-service.md#install-site-recovery-vss-provider-on-source-machine)을 참조하여 Site Recovery VSS 공급자의 수동 설치를 완료하세요.
    - 애플리케이션 일관성 복구 지점을 생성하지 않으려면 [복제 정책을 수정](vmware-azure-set-up-replication.md#create-a-policy)하여 애플리케이션 일관성 복구 지점을 해제합니다.

### <a name="mobility-service-agent-version-922-and-below"></a>Mobility Service 에이전트 버전 9.22 이하

1. 에이전트가 원본 컴퓨터에 푸시됩니다. 여러 환경 오류로 인해 에이전트를 원본 컴퓨터에 복사하지 못할 수 있습니다. 푸시 설치 오류 문제를 해결하려면 [지침](vmware-azure-troubleshoot-push-install.md)을 참조하세요.
1. 에이전트가 서버에 성공적으로 복사되면 서버에서 사전 요구 사항을 확인합니다.
   - 모든 사전 요구 사항이 충족되면 설치를 시작합니다.
   - 하나 이상의 [사전 요구 사항](vmware-physical-azure-support-matrix.md)이 충족되지 않으면 설치가 진행되지 않습니다.

1. 에이전트 설치 중에는 Azure Site Recovery용 VSS(볼륨 섀도 복사본 서비스) 공급자도 설치됩니다. VSS 공급자는 애플리케이션 일관성 복구 지점을 생성하는 데 사용됩니다.
   - VSS 공급자가 설치되지 않으면 에이전트도 설치되지 않습니다. 에이전트 설치 오류를 방지하려면 [버전 9.23](https://support.microsoft.com/help/4494485/update-rollup-35-for-azure-site-recovery) 이상을 사용하여 크래시 일관성 복구 지점을 생성하고 VSS 공급자를 수동으로 설치합니다.

## <a name="install-the-mobility-service-using-ui"></a>UI를 사용하여 Mobility Service 설치

### <a name="prerequisites"></a>사전 요구 사항

- 모든 서버 구성이 [Azure에 대한 VMware VM 및 물리적 서버 재해 복구를 위한 지원 매트릭스](vmware-physical-azure-support-matrix.md)에 명시된 조건을 충족하는지 확인합니다.
- 서버의 운영 체제에 대한 [설치 프로그램을 찾습니다](#locate-installer-files).

>[!IMPORTANT]
> 한 Azure 지역에서 다른 Azure 지역으로 Azure IaaS(Infrastructure as a Service) VM을 복제하는 경우 UI 설치 방법을 사용하지 않습니다. [명령 프롬프트](#install-the-mobility-service-using-command-prompt) 설치를 사용합니다.

1. 머신에 설치 파일을 복사하고 실행합니다.
1. **설치 옵션** 에서 **Mobility 서비스 설치** 를 선택합니다.
1. 설치 위치를 선택하고 **설치** 를 선택합니다.

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility1.png" alt-text="Mobility Service 설치 옵션 페이지.":::

1. **설치 진행률** 에서 설치를 모니터링합니다. 설치가 완료되면 **구성으로 계속 진행** 을 선택하여 Mobility 서비스를 구성 서버에 등록합니다.

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility3.png" alt-text="설치가 완료된 후 설치 진행률 및 활성화된 구성으로 계속 진행 단추를 보여 주는 스크린샷.":::

1. 구성한 IP 주소 및 암호를 **구성 서버 세부 정보** 에 입력합니다.

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility4.png" alt-text="Mobility Service 등록 페이지.":::

1. **등록** 을 선택하여 등록을 완료합니다.

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility5.png" alt-text="Mobility Service 등록 마지막 페이지.":::

## <a name="install-the-mobility-service-using-command-prompt"></a>명령 프롬프트를 사용하여 Mobility Service 설치

### <a name="prerequisites"></a>사전 요구 사항

- 모든 서버 구성이 [Azure에 대한 VMware VM 및 물리적 서버 재해 복구를 위한 지원 매트릭스](vmware-physical-azure-support-matrix.md)에 명시된 조건을 충족하는지 확인합니다.
- 서버의 운영 체제에 대한 [설치 프로그램을 찾습니다](#locate-installer-files).

### <a name="windows-machine"></a>Windows 컴퓨터

- 명령 프롬프트에서 다음 명령을 실행하여 보호하려는 서버의 로컬 폴더(예: _C:\Temp_)에 설치 프로그램을 복사합니다. 설치 프로그램의 파일 이름을 실제 파일 이름으로 바꿉니다.

  ```cmd
  cd C:\Temp
  ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
  MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
  cd C:\Temp\Extracted
  ```

- 다음 명령을 실행하여 에이전트를 설치합니다.

  ```cmd
  UnifiedAgent.exe /Role "MS" /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
  ```

- 다음 명령을 실행하여 에이전트를 구성 서버에 등록합니다.

  ```cmd
  cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
  UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
  ```

#### <a name="installation-settings"></a>설치 설정

Setting | 세부 정보
--- | ---
구문 | `UnifiedAgent.exe /Role \<MS/MT> /InstallLocation \<Install Location> /Platform "VmWare" /Silent`
설치 로그 | `%ProgramData%\ASRSetupLogs\ASRUnifiedAgentInstaller.log`
`/Role` | 필수 설치 매개 변수입니다. MS(Mobility 서비스) 또는 MT(마스터 대상) 설치 여부를 지정합니다.
`/InstallLocation`| 선택적 매개 변수입니다. Mobility 서비스 설치 위치(모든 폴더)를 지정합니다.
`/Platform` | 필수. Mobility Service가 설치되는 플랫폼을 지정합니다. <br/> VMware VM/물리적 서버용 **VMware**. <br/> Azure VM용 **Azure**.<br/><br/> Azure VM을 물리적 컴퓨터로 처리하는 경우 **VMware** 를 지정합니다.
`/Silent`| 선택 사항입니다. 자동 모드에서의 설치 관리자 실행 여부를 지정합니다.

#### <a name="registration-settings"></a>등록 설정

Setting | 세부 정보
--- | ---
구문 | `UnifiedAgentConfigurator.exe  /CSEndPoint \<CSIP> /PassphraseFilePath \<PassphraseFilePath>`
에이전트 구성 로그 | `%ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log`
`/CSEndPoint` | 필수 매개 변수입니다. `<CSIP>`의 경우 구성 서버의 IP 주소를 지정합니다. 유효한 IP 주소를 사용합니다.
`/PassphraseFilePath` |  필수. 암호의 위치입니다. 유효한 UNC 또는 로컬 파일 경로를 사용합니다.

### <a name="linux-machine"></a>Linux 컴퓨터

1. 터미널 세션에서 보호하려는 서버의 로컬 폴더(예: _/tmp_)에 설치 프로그램을 복사합니다. 설치 프로그램의 파일 이름을 Linux 배포의 실제 파일 이름으로 바꾸고 명령을 실행합니다.

   ```shell
   cd /tmp ;
   tar -xvf Microsoft-ASR_UA_version_LinuxVersion_GA_date_release.tar.gz
   ```

2. 다음과 같이 설치합니다.

   ```shell
   sudo ./install -d <Install Location> -r MS -v VmWare -q
   ```

3. 설치가 완료되면 Mobility Service를 구성 서버에 등록해야 합니다. 다음 명령을 실행하여 Mobility Service를 구성 서버에 등록합니다.

   ```shell
   /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
   ```

#### <a name="installation-settings"></a>설치 설정

Setting | 세부 정보
--- | ---
구문 | `./install -d \<Install Location> -r \<MS/MT> -v VmWare -q`
`-r` | 필수 설치 매개 변수입니다. MS(Mobility 서비스) 또는 MT(마스터 대상) 설치 여부를 지정합니다.
`-d` | 선택적 매개 변수입니다. Mobility Service 설치 위치(`/usr/local/ASR`)를 지정합니다.
`-v` | 필수. Mobility Service가 설치될 플랫폼을 지정합니다. <br/> VMware VM/물리적 서버용 **VMware**. <br/> Azure VM용 **Azure**.
`-q` | 선택 사항입니다. 자동 모드에서의 설치 관리자 실행 여부를 지정합니다.

#### <a name="registration-settings"></a>등록 설정

Setting | 세부 정보
--- | ---
구문 | `cd /usr/local/ASR/Vx/bin<br/><br/> UnifiedAgentConfigurator.sh -i \<CSIP> -P \<PassphraseFilePath>`
`-i` | 필수 매개 변수입니다. `<CSIP>`의 경우 구성 서버의 IP 주소를 지정합니다. 유효한 IP 주소를 사용합니다.
`-P` |  필수. 암호가 저장되는 파일의 전체 파일 경로입니다. 유효한 폴더를 사용합니다.

## <a name="azure-virtual-machine-agent"></a>Azure Virtual Machine 에이전트

- **Windows VM**: Mobility 서비스 버전 9.7.0.0부터 [Azure VM 에이전트](../virtual-machines/extensions/features-windows.md#azure-vm-agent)를 Mobility 서비스 설치 관리자가 설치합니다. 이렇게 하면 컴퓨터가 Azure로 장애 조치(failover)될 때 Azure VM이 모든 VM 확장을 사용하기 위한 에이전트 설치 사전 요구 사항을 충족합니다.
- **Linux VM**: 장애 조치(failover) 후 [WALinuxAgent](../virtual-machines/extensions/update-linux-agent.md)가 Azure VM에 수동으로 설치되어야 합니다.

## <a name="locate-installer-files"></a>설치 프로그램 파일 찾기

구성 서버에서 _%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository_ 폴더로 이동합니다. 사용 중인 운영 체제에 필요한 설치 프로그램을 확인합니다. 다음 표는 각 VMware VM 및 물리적 서버 운영 체제에 맞는 설치 프로그램 파일을 간략히 설명합니다. 시작하기 전에 [지원되는 운영 체제](vmware-physical-azure-support-matrix.md#replicated-machines)를 검토합니다.

> [!NOTE]
> 파일 이름에는 _버전_ 및 _날짜_ 를 자리 표시자로 지정하여 다음 표에 정리된 구문이 사용됩니다. 실제 파일 이름은 다음 예제와 유사하게 표시됩니다.
> - `Microsoft-ASR_UA_9.30.0.0_Windows_GA_22Oct2019_release.exe`
> - `Microsoft-ASR_UA_9.30.0.0_UBUNTU-16.04-64_GA_22Oct2019_release.tar.gz`

설치 관리자 파일 | 운영 체제(64비트만 해당)
--- | ---
`Microsoft-ASR_UA_version_Windows_GA_date_release.exe` | Windows Server 2016 </br> Windows Server 2012 R2 </br> Windows Server 2012 </br> Windows Server 2008 R2 SP1
[다운로드 후 이 폴더에 수동으로 배치](#rhel-5-or-centos-5-server) | RHEL(Red Hat Enterprise Linux) 5 </br> CentOS 5
`Microsoft-ASR_UA_version_RHEL6-64_GA_date_release.tar.gz` | RHEL(Red Hat Enterprise Linux) 6 </br> CentOS 6
`Microsoft-ASR_UA_version_RHEL7-64_GA_date_release.tar.gz` | Red Hat Enterprise Linux(RHEL) 7 </br> CentOS 7
`Microsoft-ASR_UA_version_RHEL8-64_GA_date_release.tar.gz` | RHEL(Red Hat Enterprise Linux) 8 </br> CentOS 8
`Microsoft-ASR_UA_version_SLES12-64_GA_date_release.tar.gz` | SUSE Linux Enterprise Server 12 SP1 </br> SP2 및 SP3를 포함합니다.
[다운로드 후 이 폴더에 수동으로 배치](#suse-11-sp3-server) | SUSE Linux Enterprise Server 11 SP3
`Microsoft-ASR_UA_version_SLES11-SP4-64_GA_date_release.tar.gz` | SUSE Linux Enterprise Server 11 SP4
`Microsoft-ASR_UA_version_SLES15-64_GA_date_release.tar.gz` | SUSE Linux Enterprise Server 15 
`Microsoft-ASR_UA_version_OL6-64_GA_date_release.tar.gz` | Oracle Enterprise Linux 6.4 </br> Oracle Enterprise Linux 6.5
`Microsoft-ASR_UA_version_OL7-64_GA_date_release.tar.gz` | Oracle Enterprise Linux 7 
`Microsoft-ASR_UA_version_OL8-64_GA_date_release.tar.gz` | Oracle Enterprise Linux 8
`Microsoft-ASR_UA_version_UBUNTU-14.04-64_GA_date_release.tar.gz` | Ubuntu Linux 14.04
`Microsoft-ASR_UA_version_UBUNTU-16.04-64_GA_date_release.tar.gz` | Ubuntu Linux 16.04 LTS 서버
`Microsoft-ASR_UA_version_UBUNTU-18.04-64_GA_date_release.tar.gz` | Ubuntu Linux 18.04 LTS 서버
`Microsoft-ASR_UA_version_UBUNTU-20.04-64_GA_date_release.tar.gz` | Ubuntu Linux 20.04 LTS 서버
[다운로드 후 이 폴더에 수동으로 배치](#debian-7-server) | Debian 7
`Microsoft-ASR_UA_version_DEBIAN8-64_GA_date_release.tar.gz` | Debian 8
`Microsoft-ASR_UA_version_DEBIAN9-64_GA_date_release.tar.gz` | Debian 9

## <a name="download-latest-mobility-agent-installer-for-suse-11-sp3-rhel-5-debian-7-server"></a>SUSE 11 SP3, RHEL 5, Debian 7 서버용 최신 Mobility 에이전트 설치 프로그램 다운로드

### <a name="suse-11-sp3-server"></a>SUSE 11 SP3 서버

9\.36 버전 이상에서 **SUSE Linux Enterprise Server 11 SP3 컴퓨터를 업데이트하거나 보호하기 위한 사전 요구 사항**:

1. 최신 Mobility 에이전트 설치 프로그램이 Microsoft 다운로드 센터에서 다운로드되고 구성 서버 및 모든 스케일 아웃 프로세스 서버에서 푸시 설치 프로그램 리포지토리에 배치되었는지 확인합니다.
2. 최신 SUSE Linux Enterprise Server 11 SP3 에이전트 설치 프로그램을 [다운로드](site-recovery-whats-new.md)합니다.
3. 구성 서버로 이동하여 SUSE Linux Enterprise Server 11 SP3 에이전트 설치 프로그램을 INSTALL_DIR\home\svsystems\pushinstallsvc\repository 경로에 복사합니다.
1. 최신 설치 프로그램을 복사한 후 InMage PushInstall 서비스를 다시 시작합니다. 
1. 이제 연결된 스케일 아웃 프로세스 서버로 이동하여 3단계와 4단계를 반복합니다.
1. **예를 들어**, 설치 경로가 C:\Program Files (x86)\Microsoft Azure Site Recovery인 경우 위에 언급된 디렉터리는
    1. C:\Program Files (x86)\Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository

### <a name="rhel-5-or-centos-5-server"></a>RHEL 5 또는 CentOS 5 서버

9\.36 버전 이상에서 **RHEL 5 컴퓨터를 업데이트하거나 보호하기 위한 사전 요구 사항**:

1. 최신 Mobility 에이전트 설치 프로그램이 Microsoft 다운로드 센터에서 다운로드되고 구성 서버 및 모든 스케일 아웃 프로세스 서버에서 푸시 설치 프로그램 리포지토리에 배치되었는지 확인합니다.
2. 최신 RHEL 5 또는 CentOS 5 에이전트 설치 프로그램을 [다운로드](site-recovery-whats-new.md)합니다. 
3. 구성 서버로 이동하여 RHEL 5 또는 CentOS 5 에이전트 설치 프로그램을 INSTALL_DIR\home\svsystems\pushinstallsvc\repository 경로에 복사합니다.
1. 최신 설치 프로그램을 복사한 후 InMage PushInstall 서비스를 다시 시작합니다. 
1. 이제 연결된 스케일 아웃 프로세스 서버로 이동하여 3단계와 4단계를 반복합니다.
1. **예를 들어**, 설치 경로가 C:\Program Files (x86)\Microsoft Azure Site Recovery인 경우 위에 언급된 디렉터리는
    1. C:\Program Files (x86)\Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository

## <a name="debian-7-server"></a>Debian 7 서버

9\.36 버전 이상에서 **Debian 7 컴퓨터를 업데이트하거나 보호하기 위한 사전 요구 사항**:

1. 최신 Mobility 에이전트 설치 프로그램이 Microsoft 다운로드 센터에서 다운로드되고 구성 서버 및 모든 스케일 아웃 프로세스 서버에서 푸시 설치 프로그램 리포지토리에 배치되었는지 확인합니다.
2. 최신 Debian 7 에이전트 설치 프로그램을 [다운로드](site-recovery-whats-new.md)합니다.
3. 구성 서버로 이동하여 Debian 7 에이전트 설치 프로그램을 INSTALL_DIR\home\svsystems\pushinstallsvc\repository 경로에 복사합니다.
1. 최신 설치 프로그램을 복사한 후 InMage PushInstall 서비스를 다시 시작합니다. 
1. 이제 연결된 스케일 아웃 프로세스 서버로 이동하여 3단계와 4단계를 반복합니다.
1. **예를 들어**, 설치 경로가 C:\Program Files (x86)\Microsoft Azure Site Recovery인 경우 위에 언급된 디렉터리는
    1. C:\Program Files (x86)\Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository

## <a name="next-steps"></a>다음 단계

[Mobility 서비스에 대한 푸시 설치를 설정합니다](vmware-azure-install-mobility-service.md).
