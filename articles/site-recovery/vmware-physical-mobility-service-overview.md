---
title: Azure 사이트 복구를 통해 VMware VM 및 물리적 서버의 재해 복구를 위한 모빌리티 서비스 정보 | 마이크로 소프트 문서
description: Azure 사이트 복구 서비스를 사용하여 Azure에 VMware VM 및 물리적 서버의 재해 복구를 위한 모빌리티 서비스 에이전트에 대해 알아봅니다.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: how-to
ms.date: 04/10/2020
ms.author: ramamill
ms.openlocfilehash: ec4d1cfbe0c76c8245c4beeaa7c044d76d917a7a
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81259805"
---
# <a name="about-the-mobility-service-for-vmware-vms-and-physical-servers"></a>VMware VM 및 물리적 서버의 재해 복구를 위한 Mobility 서비스

[Azure 사이트](site-recovery-overview.md)복구를 사용하여 VMware 가상 시스템(VM) 및 물리적 서버에 대한 재해 복구를 설정하면 각 온-프레미스 VMware VM 및 물리적 서버에 사이트 복구 이동성 서비스를 설치합니다. Mobility 서비스는 머신에 기록된 데이터를 캡처하고 이를 Site Recovery 프로세스 서버에 전달합니다. 모빌리티 서비스는 다음 방법을 사용하여 배포할 수 있는 Mobility 서비스 에이전트 소프트웨어에 의해 설치됩니다.

- [푸시 설치](#push-installation): Azure 포털을 통해 보호가 활성화되면 사이트 복구가 서버에 이동성 서비스를 설치합니다.
- 수동 설치: [사용자 인터페이스(UI)](#install-the-mobility-service-using-ui) 또는 [명령 프롬프트를](#install-the-mobility-service-using-command-prompt)통해 각 컴퓨터에 수동으로 Mobility 서비스를 설치할 수 있습니다.
- [자동 배포](vmware-azure-mobility-install-configuration-mgr.md): Configuration Manager와 같은 소프트웨어 배포 도구를 사용하여 Mobility 서비스 설치를 자동화할 수 있습니다.

> [!NOTE]
> 모빌리티 서비스는 VMware VM 또는 물리적 컴퓨터의 소스 컴퓨터에서 약 6%-10%의 메모리를 사용합니다.

## <a name="antivirus-on-replicated-machines"></a>복제된 컴퓨터에서 바이러스 백신

복제하려는 컴퓨터가 바이러스 백신 소프트웨어를 실행하는 경우 Mobility 서비스의 설치 폴더 _C:\ProgramData\ASR\에이전트를_ 바이러스 백신 작업에서 제외합니다. 이렇게 제외하면 복제가 예상대로 작동합니다.

## <a name="push-installation"></a>푸시 설치

푸시 설치는 [복제를 사용하도록 설정하기](vmware-azure-enable-replication.md#enable-replication)위해 Azure 포털에서 실행되는 작업의 필수적인 부분입니다. 보호하려는 VM 집합을 선택하고 복제를 사용하도록 설정하면 구성 서버는 Mobility 서비스 에이전트를 서버에 푸시하고 에이전트를 설치하고 구성 서버로 에이전트 등록을 완료합니다.

### <a name="prerequisites"></a>사전 요구 사항

- 모든 푸시 설치 [전제 조건이](vmware-azure-install-mobility-service.md) 충족되었는지 확인합니다.
- 모든 서버 구성이 [VMware VM 및 물리적 서버를 Azure로 복구하기 위한 지원 매트릭스의 기준을 충족하는지 확인합니다.](vmware-physical-azure-support-matrix.md)

푸시 설치 워크플로는 다음 섹션에 설명되어 있습니다.

### <a name="mobility-service-agent-version-923-and-higher"></a>모빌리티 서비스 에이전트 버전 9.23 이상

버전 9.23에 대한 자세한 내용은 [Azure 사이트 복구에 대한 롤업 35 업데이트](https://support.microsoft.com/help/4494485/update-rollup-35-for-azure-site-recovery)를 참조하십시오.

모빌리티 서비스를 푸시 설치하는 동안 다음 단계가 수행됩니다.

1. 에이전트가 소스 컴퓨터로 푸시됩니다. 여러 환경 오류로 인해 에이전트를 소스 컴퓨터에 복사하는 데 실패할 수 있습니다. 푸시 설치 실패 문제를 해결하려면 [지침을](vmware-azure-troubleshoot-push-install.md) 방문하십시오.
1. 에이전트가 서버에 성공적으로 복사되면 서버에서 필수 구성 조건 검사가 수행됩니다.
   - 모든 필수 구성 조건이 충족되면 설치가 시작됩니다.
   - 하나 이상의 [필수 구성 조건이](vmware-physical-azure-support-matrix.md) 충족되지 않으면 설치가 실패합니다.
1. 에이전트 설치의 일부로 AZURE 사이트 복구에 대한 VSS(볼륨 섀도우 복사 서비스) 공급자가 설치됩니다. VSS 공급자는 응용 프로그램 일관된 복구 지점을 생성하는 데 사용됩니다. VSS 공급자 설치에 실패하면 이 단계를 건너뛰고 에이전트 설치가 계속됩니다.
1. 에이전트 설치가 성공했지만 VSS 공급자 설치가 실패하면 작업 상태가 **경고로**표시됩니다. 이는 크래시 일관된 복구 지점 생성에 영향을 미치지 않습니다.

    - 응용 프로그램 일관된 복구 지점을 생성하려면 사이트 복구 VSS 공급자의 수동 설치를 완료하려면 [지침을](vmware-physical-manage-mobility-service.md#install-site-recovery-vss-provider-on-source-machine) 참조하십시오.
    - 응용 프로그램 일관된 복구 지점을 생성하지 않으려면 [복제 정책을 수정하여](vmware-azure-set-up-replication.md#create-a-policy) 응용 프로그램 일관된 복구 지점을 해제합니다.

### <a name="mobility-service-agent-version-922-and-below"></a>모빌리티 서비스 에이전트 버전 9.22 이하

1. 에이전트가 소스 컴퓨터로 푸시됩니다. 여러 환경 오류로 인해 에이전트를 소스 컴퓨터에 복사하는 데 실패할 수 있습니다. 푸시 설치 실패 문제를 해결하려면 [지침을](vmware-azure-troubleshoot-push-install.md) 참조하십시오.
1. 에이전트가 서버에 성공적으로 복사되면 서버에서 필수 구성 조건 검사가 수행됩니다.
   - 모든 필수 구성 조건이 충족되면 설치가 시작됩니다.
   - 하나 이상의 [필수 구성 조건이](vmware-physical-azure-support-matrix.md) 충족되지 않으면 설치가 실패합니다.

1. 에이전트 설치의 일부로 AZURE 사이트 복구에 대한 VSS(볼륨 섀도우 복사 서비스) 공급자가 설치됩니다. VSS 공급자는 응용 프로그램 일관된 복구 지점을 생성하는 데 사용됩니다.
   - VSS 공급자 설치에 실패하면 에이전트 설치가 실패합니다. 에이전트 설치 실패를 방지하려면 [버전 9.23](https://support.microsoft.com/help/4494485/update-rollup-35-for-azure-site-recovery) 이상에서 충돌 일관된 복구 지점을 생성하고 VSS 공급자를 수동으로 설치합니다.

## <a name="install-the-mobility-service-using-ui"></a>UI를 사용하여 모빌리티 서비스 설치

### <a name="prerequisites"></a>사전 요구 사항

- 모든 서버 구성이 [VMware VM 및 물리적 서버를 Azure로 복구하기 위한 지원 매트릭스의 기준을 충족하는지 확인합니다.](vmware-physical-azure-support-matrix.md)
- 서버의 운영 체제에 대한 [설치 관리자를 찾습니다.](#locate-installer-files)

>[!IMPORTANT]
> 한 Azure 리전에서 다른 Azure 지역으로 IaaS(서비스) VM으로 Azure 인프라를 복제하는 경우 UI 설치 방법을 사용하지 마십시오. 명령 [프롬프트](#install-the-mobility-service-using-command-prompt) 설치를 사용합니다.

1. 머신에 설치 파일을 복사하고 실행합니다.
1. **설치 옵션**에서 **Mobility 서비스 설치**를 선택합니다.
1. 설치 위치를 선택하고 **설치를**선택합니다.

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility1.png" alt-text="이동성 서비스 설치 옵션 페이지.":::

1. **설치 진행률**에서 설치를 모니터링합니다. 설치가 완료되면 **구성으로 계속 진행**을 선택하여 Mobility 서비스를 구성 서버에 등록합니다.

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility3.png" alt-text="모빌리티 서비스 등록 페이지.":::

1. **구성 서버 세부 정보에서**구성한 IP 주소와 암호를 지정합니다.

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility4.png" alt-text="모빌리티 서비스 등록 페이지.":::

1. **등록**을 선택하여 등록을 완료합니다.

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility5.png" alt-text="모빌리티 서비스 등록 최종 페이지.":::

## <a name="install-the-mobility-service-using-command-prompt"></a>명령 프롬프트를 사용하여 모빌리티 서비스 설치

### <a name="prerequisites"></a>사전 요구 사항

- 모든 서버 구성이 [VMware VM 및 물리적 서버를 Azure로 복구하기 위한 지원 매트릭스의 기준을 충족하는지 확인합니다.](vmware-physical-azure-support-matrix.md)
- 서버의 운영 체제에 대한 [설치 관리자를 찾습니다.](#locate-installer-files)

### <a name="windows-machine"></a>윈도우 머신

- 명령 프롬프트에서 다음 명령을 실행하여 보호하려는 서버에서 _C:\Temp_와 같은 로컬 폴더에 설치 프로그램을 복사합니다. 설치 관리자의 파일 이름을 실제 파일 이름으로 바꿉니다.

  ```cmd
  cd C:\Temp
  ren Microsoft-ASR_UA_version_Windows_GA_date_release.exe MobilityServiceInstaller.exe
  MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
  cd C:\Temp\Extracted
  ```

- 이 명령을 실행하여 에이전트를 설치합니다.

  ```cmd
  UnifiedAgent.exe /Role "MS" /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
  ```

- 이러한 명령을 실행하여 구성 서버에 에이전트를 등록합니다.

  ```cmd
  cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
  UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
  ```

#### <a name="installation-settings"></a>설치 설정

설정 | 세부 정보
--- | ---
구문 | `UnifiedAgent.exe /Role \<MS/MT> /InstallLocation \<Install Location> /Platform "VmWare" /Silent`
설정 로그 | `%ProgramData%\ASRSetupLogs\ASRUnifiedAgentInstaller.log`
`/Role` | 필수 설치 매개 변수입니다. MS(Mobility 서비스) 또는 MT(마스터 대상) 설치 여부를 지정합니다.
`/InstallLocation`| 선택적 매개 변수. Mobility 서비스 설치 위치(모든 폴더)를 지정합니다.
`/Platform` | 필수. 모빌리티 서비스가 설치된 플랫폼을 지정합니다. <br/> **VMware VM/물리적** 서버용 VM웨어. <br/> Azure VM에 대한 **Azure입니다.**<br/><br/> Azure VM을 물리적 컴퓨터로 처리하는 경우 **VMware를**지정합니다.
`/Silent`| (선택 사항) 자동 모드에서의 설치 관리자 실행 여부를 지정합니다.

#### <a name="registration-settings"></a>등록 설정

설정 | 세부 정보
--- | ---
구문 | `UnifiedAgentConfigurator.exe  /CSEndPoint \<CSIP> /PassphraseFilePath \<PassphraseFilePath>`
에이전트 구성 로그 | `%ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log`
`/CSEndPoint` | 필수 매개 변수입니다. `<CSIP>`구성 서버의 IP 주소를 지정합니다. 유효한 IP 주소를 사용합니다.
`/PassphraseFilePath` |  필수. 암호의 위치입니다. 유효한 UNC 또는 로컬 파일 경로를 사용합니다.

### <a name="linux-machine"></a>리눅스 기계

1. 터미널 세션에서 설치 프로그램을 보호하려는 서버의 _/tmp와_ 같은 로컬 폴더에 복사합니다. 설치 관리자의 파일 이름을 Linux 배포판의 실제 파일 이름으로 바꾼 다음 명령을 실행합니다.

   ```shell
   cd /tmp ;
   tar -xvf Microsoft-ASR_UA_version_LinuxVersion_GA_date_release.tar.gz
   ```

2. 다음과 같이 설치합니다.

   ```shell
   sudo ./install -d <Install Location> -r MS -v VmWare -q
   ```

3. 설치가 완료되면 모빌리티 서비스를 구성 서버에 등록해야 합니다. 다음 명령을 실행하여 구성 서버에 Mobility 서비스를 등록합니다.

   ```shell
   /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
   ```

#### <a name="installation-settings"></a>설치 설정

설정 | 세부 정보
--- | ---
구문 | `./install -d \<Install Location> -r \<MS/MT> -v VmWare -q`
`-r` | 필수 설치 매개 변수입니다. MS(Mobility 서비스) 또는 MT(마스터 대상) 설치 여부를 지정합니다.
`-d` | 선택적 매개 변수. 모빌리티 서비스 설치 위치를 `/usr/local/ASR`지정합니다.
`-v` | 필수. 모빌리티 서비스가 설치된 플랫폼을 지정합니다. <br/> **VMware VM/물리적** 서버용 VM웨어. <br/> Azure VM에 대한 **Azure입니다.**
`-q` | (선택 사항) 자동 모드에서의 설치 관리자 실행 여부를 지정합니다.

#### <a name="registration-settings"></a>등록 설정

설정 | 세부 정보
--- | ---
구문 | `cd /usr/local/ASR/Vx/bin<br/><br/> UnifiedAgentConfigurator.sh -i \<CSIP> -P \<PassphraseFilePath>`
`-i` | 필수 매개 변수입니다. `<CSIP>`구성 서버의 IP 주소를 지정합니다. 유효한 IP 주소를 사용합니다.
`-P` |  필수. 암호가 저장되는 파일의 전체 파일 경로입니다. 유효한 폴더를 사용합니다.

## <a name="azure-virtual-machine-agent"></a>Azure Virtual Machine 에이전트

- **Windows VM**: Mobility 서비스 버전 9.7.0.0부터 [Azure VM 에이전트](/azure/virtual-machines/extensions/features-windows#azure-vm-agent)를 Mobility 서비스 설치 관리자가 설치합니다. 이렇게 하면 컴퓨터가 Azure로 장애로 장애토될 때 Azure VM이 모든 VM 확장을 사용하기 위한 에이전트 설치 전제 를 충족합니다.
- **Linux VM**: 장애 조치(failover) 후 [WALinuxAgent](/azure/virtual-machines/extensions/update-linux-agent)가 Azure VM에 수동으로 설치되어야 합니다.

## <a name="locate-installer-files"></a>설치 프로그램 파일 찾기

구성 서버에서 폴더 _%ProgramData%\ASR\홈\svsystems\pushinstallsvc\리포지토리로_이동합니다. 운영 체제에 따라 필요한 설치 프로그램을 확인합니다. 다음 표에는 각 VMware VM 및 물리적 서버 운영 체제에 대한 설치 관리자 파일이 요약되어 있습니다. 시작하기 전에 [지원되는 운영 체제를](vmware-physical-azure-support-matrix.md#replicated-machines)검토할 수 있습니다.

> [!NOTE]
> 파일 이름은 다음 표에 표시된 구문을 _버전_ 및 _날짜와_ 함께 실제 값의 자리 표시자로 사용합니다. 실제 파일 이름은 다음과 유사합니다.
> - `Microsoft-ASR_UA_9.30.0.0_Windows_GA_22Oct2019_release.exe`
> - `Microsoft-ASR_UA_9.30.0.0_UBUNTU-16.04-64_GA_22Oct2019_release.tar.gz`

설치 관리자 파일 | 운영 체제(64비트만 해당)
--- | ---
`Microsoft-ASR_UA_version_Windows_GA_date_release.exe` | Windows Server 2016 </br> Windows Server 2012 R2 </br> Windows Server 2012 </br> Windows Server 2008 R2 SP1
`Microsoft-ASR_UA_version_RHEL6-64_GA_date_release.tar.gz` | 레드 햇 엔터프라이즈 리눅스 (RHEL) 6 </br> CentOS 6
`Microsoft-ASR_UA_version_RHEL7-64_GA_date_release.tar.gz` | Red Hat Enterprise Linux(RHEL) 7 </br> CentOS 7
`Microsoft-ASR_UA_version_SLES12-64_GA_date_release.tar.gz` | SUSE Linux Enterprise Server 12 SP1 </br> SP2 및 SP3가 포함되어 있습니다.
`Microsoft-ASR_UA_version_SLES11-SP3-64_GA_date_release.tar.gz` | SUSE Linux Enterprise Server 11 SP3
`Microsoft-ASR_UA_version_SLES11-SP4-64_GA_date_release.tar.gz` | SUSE Linux Enterprise Server 11 SP4
`Microsoft-ASR_UA_version_OL6-64_GA_date_release.tar.gz` | 오라클 엔터프라이즈 리눅스 6.4 </br> 오라클 엔터프라이즈 리눅스 6.5
`Microsoft-ASR_UA_version_UBUNTU-14.04-64_GA_date_release.tar.gz` | Ubuntu Linux 14.04
`Microsoft-ASR_UA_version_UBUNTU-16.04-64_GA_date_release.tar.gz` | Ubuntu Linux 16.04 LTS 서버
`Microsoft-ASR_UA_version_DEBIAN7-64_GA_date_release.tar.gz` | Debian 7
`Microsoft-ASR_UA_version_DEBIAN8-64_GA_date_release.tar.gz` | Debian 8

## <a name="next-steps"></a>다음 단계

[Mobility 서비스에 대한 푸시 설치를 설정합니다](vmware-azure-install-mobility-service.md).
