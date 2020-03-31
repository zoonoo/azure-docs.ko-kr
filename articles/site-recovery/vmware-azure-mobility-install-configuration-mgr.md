---
title: Azure 사이트 복구에서 설치의 재해 복구를 위한 모빌리티 서비스 자동화
description: Azure 사이트 복구를 사용하여 VMware/물리적 서버 재해 복구에 대한 이동성 서비스를 자동으로 설치하는 방법
author: Rajeswari-Mamilla
ms.topic: how-to
ms.date: 2/5/2020
ms.author: ramamill
ms.openlocfilehash: f24d321e882024d324435498adf11694037547f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77252230"
---
# <a name="automate-mobility-service-installation"></a>모빌리티 서비스 설치 자동화

이 문서에서는 [Azure 사이트 복구에서](site-recovery-overview.md)이동성 서비스 에이전트에 대한 설치 및 업데이트를 자동화하는 방법에 대해 설명합니다.

온-프레미스 VMware VM 및 물리적 서버의 재해 복구를 위해 사이트 복구를 Azure에 배포할 때 복제하려는 각 컴퓨터에 모빌리티 서비스 에이전트를 설치합니다. 모빌리티 서비스는 컴퓨터에서 데이터를 캡처하고 복제를 위해 사이트 복구 프로세스 서버로 전달합니다. 다음과 같은 몇 가지 방법으로 모빌리티 서비스를 배포할 수 있습니다.

- **푸시 설치**: Azure 포털에서 컴퓨터에 대한 복제를 사용하도록 설정할 때 사이트 복구에서 Mobility 서비스 에이전트를 설치합니다.
- **수동 설치**: 각 기계에 수동으로 모빌리티 서비스를 설치합니다. 푸시 및 수동 설치에 대해 [자세히 알아보세요.](vmware-physical-mobility-service-overview.md)
- **자동 배포**: Microsoft 엔드포인트 구성 관리자와 같은 소프트웨어 배포 도구 또는 JetPatch와 같은 타사 도구를 사용하여 설치를 자동화합니다.

다음과 같은 경우 자동 설치 및 업데이트가 솔루션을 제공합니다.

- 조직에서 보호된 서버에 푸시 설치를 허용하지 않습니다.
- 회사 정책에 따라 암호를 주기적으로 변경해야 합니다. 푸시 설치를 위한 암호를 지정해야 합니다.
- 보안 정책에서 특정 컴퓨터에 대한 방화벽 예외를 추가하는 것을 허용하지 않습니다.
- 호스팅 서비스 공급자 역할을 하며 사이트 복구를 사용하여 푸시 설치에 필요한 고객 컴퓨터 자격 증명을 제공하지 않으려고 합니다.
- 에이전트 설치를 동시에 많은 서버로 확장해야 합니다.
- 계획된 유지 관리 기간 동안 설치 및 업그레이드를 예약하려고 합니다.

## <a name="prerequisites"></a>사전 요구 사항

설치를 자동화하려면 다음 항목이 필요합니다.

- [구성 관리자](/configmgr/) 또는 [JetPatch와](https://jetpatch.com/microsoft-azure-site-recovery/)같은 배포된 소프트웨어 설치 솔루션입니다.
- VMware 재해 복구 또는 [물리적 서버](physical-azure-disaster-recovery.md) 재해 복구를 위해 [Azure](tutorial-prepare-azure.md) 및 [온-프레미스에](vmware-azure-tutorial-prepare-on-premises.md) 배치된 전제 조건입니다. 재해 복구에 대한 [지원 요구 사항을](vmware-physical-azure-support-matrix.md) 검토합니다.

## <a name="prepare-for-automated-deployment"></a>자동화된 배포 준비

다음 표에는 모빌리티 서비스 배포 자동화를 위한 도구와 프로세스가 요약되어 있습니다.

**도구** | **세부 정보** | **지침**
--- | --- | ---
**구성 관리자** | 1. 위에 나열된 [필수 구성 조건이](#prerequisites) 있는지 확인합니다. <br/><br/> 2. OVF 템플릿을 사용하여 사이트 복구 구성 서버를 VM웨어 VM으로 배포하기 위해 OVA 파일을 다운로드하는 등 소스 환경을 설정하여 재해 복구를 배포합니다.<br/><br/> 3. 사이트 복구 서비스에 구성 서버를 등록하고 대상 Azure 환경을 설정하고 복제 정책을 구성합니다.<br/><br/> 4. 자동화된 모빌리티 서비스 배포의 경우 구성 서버 암호 및 모빌리티 서비스 설치 파일이 포함된 네트워크 공유를 만듭니다.<br/><br/> 5. 설치 또는 업데이트가 포함된 Configuration Manager 패키지를 만들고 모빌리티 서비스 배포를 준비합니다.<br/><br/> 6. 그런 다음 Mobility 서비스가 설치된 컴퓨터에 대해 Azure에 대한 복제를 활성화할 수 있습니다. | [구성 관리자로 자동화](#automate-with-configuration-manager)
**제트 패치** | 1. 위에 나열된 [필수 구성 조건이](#prerequisites) 있는지 확인합니다. <br/><br/> 2. OVF 템플릿을 사용하여 사이트 복구 환경에서 Azure 사이트 복구용 JetPatch 에이전트 관리자를 다운로드하고 배포하는 등 소스 환경을 설정하여 재해 복구를 배포합니다.<br/><br/> 3. 사이트 복구에 구성 서버를 등록하고 대상 Azure 환경을 설정하고 복제 정책을 구성합니다.<br/><br/> 4. 자동화된 배포의 경우 JetPatch 에이전트 관리자 구성을 초기화하고 완료합니다.<br/><br/> 5. JetPatch에서 사이트 복구 정책을 만들어 모빌리티 서비스 에이전트의 배포 및 업그레이드를 자동화할 수 있습니다. <br/><br/> 6. 그런 다음 Mobility 서비스가 설치된 컴퓨터에 대해 Azure에 대한 복제를 활성화할 수 있습니다. | [제트패치 에이전트 관리자로 자동화](https://jetpatch.com/microsoft-azure-site-recovery-deployment-guide/)<br/><br/> [JetPatch의 문제 해결 에이전트 설치](https://kc.jetpatch.com/hc/articles/360035981812)

## <a name="automate-with-configuration-manager"></a>구성 관리자로 자동화

### <a name="prepare-the-installation-files"></a>설치 파일 준비

1. 전제 조건이 있는지 확인합니다.
1. 구성 서버를 실행하는 컴퓨터에서 액세스할 수 있는 SMB 공유(SMB 공유)를 만듭니다.
1. 구성 관리자에서 설치하거나 모빌리티 서비스를 업데이트할 [서버를 분류합니다.](/sccm/core/clients/manage/collections/automatically-categorize-devices-into-collections) 하나의 컬렉션은 모든 Windows 서버, 다른 모든 Linux 서버를 포함해야 합니다.
1. 네트워크 공유에서 폴더를 만듭니다.

   - 윈도우 머신에 설치의 경우, _MobSvcWindows라는 폴더를_만듭니다.
   - 리눅스 컴퓨터에 설치, _MobSvcLinux라는_폴더를 만듭니다 .

1. 구성 서버 컴퓨터에 로그인합니다.
1. 구성 서버 컴퓨터에서 관리 명령 프롬프트를 엽니다.
1. 암호 파일을 생성하려면 다음 명령을 실행합니다.

    ```Console
    cd %ProgramData%\ASR\home\svsystems\bin
    genpassphrase.exe -v > MobSvc.passphrase
    ```

1. _MobSvc.passphrase_ 파일을 Windows 폴더와 Linux 폴더에 복사합니다.
1. 설치 파일이 포함된 폴더를 찾아보려고 다음 명령을 실행합니다.

    ```Console
    cd %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository
    ```

1. 이러한 설치 파일을 네트워크 공유에 복사합니다.

   - 윈도우에 대 한, 복사 _마이크로소프트-ASR_UA_version_Windows_GA_date_Release.exe_ _MobSvcWindows_.
   - 리눅스의 경우, _MobSvcLinux에_다음 파일을 복사 :
     - _마이크로소프트-ASR_UARHEL6-64release.tar.gz_
     - _마이크로소프트-ASR_UARHEL7-64release.tar.gz_
     - _마이크로소프트-ASR_UASLES11-SP3-64release.tar.gz_
     - _마이크로소프트-ASR_UASLES11-SP4-64release.tar.gz_
     - _마이크로소프트-ASR_UAOL6-64release.tar.gz_
     - _마이크로 소프트 ASR_UAUBUNTU-14.04-64release.tar.gz_

1. 다음 절차에 설명된 대로 코드를 Windows 또는 Linux 폴더에 복사합니다. 우리는 다음과 같은 것을 가정하고 있습니다.

   - 구성 서버의 IP 주소는 . `192.168.3.121`
   - 보안 네트워크 파일 `\\ContosoSecureFS\MobilityServiceInstallers`공유는 .

### <a name="copy-code-to-the-windows-folder"></a>Windows 폴더에 코드 복사

다음 코드를 복사합니다.

- _설치.bat로_ _MobSvcWindows_ 폴더에 코드를 저장합니다.
- 이 `[CSIP]` 스크립트의 자리 표시자를 구성 서버의 IP 주소의 실제 값으로 바꿉니다.
- 스크립트는 모빌리티 서비스 에이전트의 새 설치와 이미 설치된 에이전트에 대한 업데이트를 지원합니다.

```DOS
Time /t >> C:\Temp\logfile.log
REM ==================================================
REM ==== Clean up the folders ========================
RMDIR /S /q %temp%\MobSvc
MKDIR %Temp%\MobSvc
MKDIR C:\Temp
REM ==================================================

REM ==== Copy new files ==============================
COPY M*.* %Temp%\MobSvc
CD %Temp%\MobSvc
REN Micro*.exe MobSvcInstaller.exe
REM ==================================================

REM ==== Extract the installer =======================
MobSvcInstaller.exe /q /x:%Temp%\MobSvc\Extracted
REM ==== Wait 10s for extraction to complete =========
TIMEOUT /t 10
REM =================================================

REM ==== Perform installation =======================
REM =================================================

CD %Temp%\MobSvc\Extracted
whoami >> C:\Temp\logfile.log
SET PRODKEY=HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall
REG QUERY %PRODKEY%\{275197FC-14FD-4560-A5EB-38217F80CBD1}
IF NOT %ERRORLEVEL% EQU 0 (
    echo "Product is not installed. Goto INSTALL." >> C:\Temp\logfile.log
    GOTO :INSTALL
) ELSE (
    echo "Product is installed." >> C:\Temp\logfile.log

    echo "Checking for Post-install action status." >> C:\Temp\logfile.log
    GOTO :POSTINSTALLCHECK
)

:POSTINSTALLCHECK
    REG QUERY "HKLM\SOFTWARE\Wow6432Node\InMage Systems\Installed Products\5" /v "PostInstallActions" | Find "Succeeded"
    If %ERRORLEVEL% EQU 0 (
        echo "Post-install actions succeeded. Checking for Configuration status." >> C:\Temp\logfile.log
        GOTO :CONFIGURATIONCHECK
    ) ELSE (
        echo "Post-install actions didn't succeed. Goto INSTALL." >> C:\Temp\logfile.log
        GOTO :INSTALL
    )

:CONFIGURATIONCHECK
    REG QUERY "HKLM\SOFTWARE\Wow6432Node\InMage Systems\Installed Products\5" /v "AgentConfigurationStatus" | Find "Succeeded"
    If %ERRORLEVEL% EQU 0 (
        echo "Configuration has succeeded. Goto UPGRADE." >> C:\Temp\logfile.log
        GOTO :UPGRADE
    ) ELSE (
        echo "Configuration didn't succeed. Goto CONFIGURE." >> C:\Temp\logfile.log
        GOTO :CONFIGURE
    )


:INSTALL
    echo "Perform installation." >> C:\Temp\logfile.log
    UnifiedAgent.exe /Role MS /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
    IF %ERRORLEVEL% EQU 0 (
        echo "Installation has succeeded." >> C:\Temp\logfile.log
        (GOTO :CONFIGURE)
    ) ELSE (
        echo "Installation has failed." >> C:\Temp\logfile.log
        GOTO :ENDSCRIPT
    )

:CONFIGURE
    echo "Perform configuration." >> C:\Temp\logfile.log
    cd "C:\Program Files (x86)\Microsoft Azure Site Recovery\agent"
    UnifiedAgentConfigurator.exe  /CSEndPoint "[CSIP]" /PassphraseFilePath %Temp%\MobSvc\MobSvc.passphrase
    IF %ERRORLEVEL% EQU 0 (
        echo "Configuration has succeeded." >> C:\Temp\logfile.log
    ) ELSE (
        echo "Configuration has failed." >> C:\Temp\logfile.log
    )
    GOTO :ENDSCRIPT

:UPGRADE
    echo "Perform upgrade." >> C:\Temp\logfile.log
    UnifiedAgent.exe /Platform "VmWare" /Silent
    IF %ERRORLEVEL% EQU 0 (
        echo "Upgrade has succeeded." >> C:\Temp\logfile.log
    ) ELSE (
        echo "Upgrade has failed." >> C:\Temp\logfile.log
    )
    GOTO :ENDSCRIPT

:ENDSCRIPT
    echo "End of script." >> C:\Temp\logfile.log
```

### <a name="copy-code-to-the-linux-folder"></a>리눅스 폴더에 코드 복사

다음 코드를 복사합니다.

- _mobSvcLinux_ 폴더에 코드를 _install_linux.sh로_저장합니다.
- 이 `[CSIP]` 스크립트의 자리 표시자를 구성 서버의 IP 주소의 실제 값으로 바꿉니다.
- 스크립트는 모빌리티 서비스 에이전트의 새 설치와 이미 설치된 에이전트에 대한 업데이트를 지원합니다.

```Bash
#!/usr/bin/env bash

rm -rf /tmp/MobSvc
mkdir -p /tmp/MobSvc
INSTALL_DIR='/usr/local/ASR'
VX_VERSION_FILE='/usr/local/.vx_version'

echo "=============================" >> /tmp/MobSvc/sccm.log
echo `date` >> /tmp/MobSvc/sccm.log
echo "=============================" >> /tmp/MobSvc/sccm.log

if [ -f /etc/oracle-release ] && [ -f /etc/redhat-release ]; then
    if grep -q 'Oracle Linux Server release 6.*' /etc/oracle-release; then
        if uname -a | grep -q x86_64; then
            OS="OL6-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *OL6*.tar.gz /tmp/MobSvc
        fi
    fi
elif [ -f /etc/redhat-release ]; then
    if grep -q 'Red Hat Enterprise Linux Server release 6.* (Santiago)' /etc/redhat-release || \
        grep -q 'CentOS Linux release 6.* (Final)' /etc/redhat-release || \
        grep -q 'CentOS release 6.* (Final)' /etc/redhat-release; then
        if uname -a | grep -q x86_64; then
            OS="RHEL6-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *RHEL6*.tar.gz /tmp/MobSvc
        fi
    elif grep -q 'Red Hat Enterprise Linux Server release 7.* (Maipo)' /etc/redhat-release || \
        grep -q 'CentOS Linux release 7.* (Core)' /etc/redhat-release; then
        if uname -a | grep -q x86_64; then
            OS="RHEL7-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *RHEL7*.tar.gz /tmp/MobSvc
                fi
    fi
elif [ -f /etc/SuSE-release ] && grep -q 'VERSION = 11' /etc/SuSE-release; then
    if grep -q "SUSE Linux Enterprise Server 11" /etc/SuSE-release && grep -q 'PATCHLEVEL = 3' /etc/SuSE-release; then
        if uname -a | grep -q x86_64; then
            OS="SLES11-SP3-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *SLES11-SP3*.tar.gz /tmp/MobSvc
        fi
    elif grep -q "SUSE Linux Enterprise Server 11" /etc/SuSE-release && grep -q 'PATCHLEVEL = 4' /etc/SuSE-release; then
        if uname -a | grep -q x86_64; then
            OS="SLES11-SP4-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *SLES11-SP4*.tar.gz /tmp/MobSvc
        fi
    fi
elif [ -f /etc/lsb-release ] ; then
    if grep -q 'DISTRIB_RELEASE=14.04' /etc/lsb-release ; then
       if uname -a | grep -q x86_64; then
           OS="UBUNTU-14.04-64"
           echo $OS >> /tmp/MobSvc/sccm.log
           cp *UBUNTU-14*.tar.gz /tmp/MobSvc
       fi
    fi
else
    exit 1
fi

if [ -z "$OS" ]; then
    exit 1
fi

Install()
{
    echo "Perform Installation." >> /tmp/MobSvc/sccm.log
    ./install -q -d ${INSTALL_DIR} -r MS -v VmWare
    RET_VAL=$?
    echo "Installation Returncode: $RET_VAL" >> /tmp/MobSvc/sccm.log
    if [ $RET_VAL -eq 0 ]; then
        echo "Installation has succeeded. Proceed to configuration." >> /tmp/MobSvc/sccm.log
        Configure
    else
        echo "Installation has failed." >> /tmp/MobSvc/sccm.log
        exit $RET_VAL
    fi
}

Configure()
{
    echo "Perform configuration." >> /tmp/MobSvc/sccm.log
    ${INSTALL_DIR}/Vx/bin/UnifiedAgentConfigurator.sh -i [CSIP] -P MobSvc.passphrase
    RET_VAL=$?
    echo "Configuration Returncode: $RET_VAL" >> /tmp/MobSvc/sccm.log
    if [ $RET_VAL -eq 0 ]; then
        echo "Configuration has succeeded." >> /tmp/MobSvc/sccm.log
    else
        echo "Configuration has failed." >> /tmp/MobSvc/sccm.log
        exit $RET_VAL
    fi
}

Upgrade()
{
    echo "Perform Upgrade." >> /tmp/MobSvc/sccm.log
    ./install -q -v VmWare
    RET_VAL=$?
    echo "Upgrade Returncode: $RET_VAL" >> /tmp/MobSvc/sccm.log
    if [ $RET_VAL -eq 0 ]; then
        echo "Upgrade has succeeded." >> /tmp/MobSvc/sccm.log
    else
        echo "Upgrade has failed." >> /tmp/MobSvc/sccm.log
        exit $RET_VAL
    fi
}

cp MobSvc.passphrase /tmp/MobSvc
cd /tmp/MobSvc

tar -zxvf *.tar.gz

if [ -e ${VX_VERSION_FILE} ]; then
    echo "${VX_VERSION_FILE} exists. Checking for configuration status." >> /tmp/MobSvc/sccm.log
    agent_configuration=$(grep ^AGENT_CONFIGURATION_STATUS "${VX_VERSION_FILE}" | cut -d"=" -f2 | tr -d " ")
    echo "agent_configuration=$agent_configuration" >> /tmp/MobSvc/sccm.log
     if [ "$agent_configuration" == "Succeeded" ]; then
        echo "Agent is already configured. Proceed to Upgrade." >> /tmp/MobSvc/sccm.log
        Upgrade
    else
        echo "Agent is not configured. Proceed to Configure." >> /tmp/MobSvc/sccm.log
        Configure
    fi
else
    Install
fi

cd /tmp

```

### <a name="create-a-package"></a>패키지 만들기

1. 구성 관리자 콘솔에 로그인하고 **소프트웨어 라이브러리** > **응용 프로그램 관리** > **패키지로 이동합니다.**
1. **패키지** > **만들기 를**마우스 오른쪽 단추로 클릭합니다.
1. 이름, 설명, 제조업체, 언어 및 버전을 포함한 패키지 세부 정보를 제공합니다.
1. **이 패키지에는 원본 파일이 포함되어 있습니다.**
1. **찾아보기를**클릭하고 관련 설치 프로그램 _(MobSvcWindows_ 또는 _MobSvcLinux)가_포함 된 네트워크 공유 및 폴더를 선택합니다. 그런 다음 **을 선택합니다.**

   ![패키지 및 프로그램 만들기 마법사의 스크린샷](./media/vmware-azure-mobility-install-configuration-mgr/create_sccm_package.png)

1. 페이지를 **만들 프로그램 유형을 선택하여** **다음** **표준 프로그램** > 다음을 선택합니다.

   ![패키지 및 프로그램 만들기 마법사의 스크린샷](./media/vmware-azure-mobility-install-configuration-mgr/sccm-standard-program.png)

1. 이 표준 프로그램 페이지에 **대한 정보를 지정할** 때 다음 값을 지정합니다.

    **매개 변수** | **윈도우 값** | **리눅스 값**
    --- | --- | ---
    **이름** | Microsoft Azure Mobility Service(Windows) 설치 | 마이크로 소프트 Azure 이동성 서비스 (리눅스)를 설치합니다.
    **명령줄** | install.bat | ./install_linux.sh
    **프로그램을 실행할 수 있습니다.** | 사용자 로그온 여부 | 사용자 로그온 여부
    **기타 매개 변수** | 기본 설정 사용 | 기본 설정 사용

   ![패키지 및 프로그램 만들기 마법사의 스크린샷](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties.png)

1. **이 표준 프로그램에 대한 요구 사항 지정에서**다음 작업을 수행합니다.

   - Windows 컴퓨터의 경우 **이 프로그램은 지정된 플랫폼에서만 실행할 수 있습니다.** 그런 다음 [지원되는 Windows 운영 체제를](vmware-physical-azure-support-matrix.md#replicated-machines) 선택하고 다음 을 **선택합니다.**
   - Linux 컴퓨터의 경우 **이 프로그램은 모든 플랫폼에서 실행할 수 있습니다.** 그런 다음 을 **선택합니다.**

1. 마법사를 마칩니다.

### <a name="deploy-the-package"></a>패키지 배포

1. 구성 관리자 콘솔에서 패키지를 마우스 오른쪽 단추로 클릭하고 **콘텐츠 배포를**선택합니다.

   ![Configuration Manager 콘솔의 스크린샷](./media/vmware-azure-mobility-install-configuration-mgr/sccm_distribute.png)

1. 패키지를 복사할 위치에 배포 지점을 선택합니다. [자세히 알아봅니다](/sccm/core/servers/deploy/configure/install-and-configure-distribution-points).
1. 마법사를 완료합니다. 그러면 패키지가 지정된 배포 지점에 복제를 시작합니다.
1. 패키지 배포가 완료되면 패키지를 오른쪽 > **배포합니다.**

   ![Configuration Manager 콘솔의 스크린샷](./media/vmware-azure-mobility-install-configuration-mgr/sccm_deploy.png)

1. 이전에 만든 Windows 또는 Linux 장치 컬렉션을 선택합니다.
1. 콘텐츠 **대상 지정** 페이지에서 **배포 지점을 선택합니다.**
1. 이 소프트웨어가 페이지를 **배포하는 방법을 제어하는 설정을 지정할** 때 **Purpose를** **필수로**설정합니다.

   ![소프트웨어 배포 마법사의 스크린샷](./media/vmware-azure-mobility-install-configuration-mgr/sccm-deploy-select-purpose.png)

1. **이 배포의 일정을 지정할**때 일정을 설정합니다. [자세히 알아봅니다](/sccm/apps/deploy-use/deploy-applications#bkmk_deploy-sched).

   - 모빌리티 서비스는 지정한 일정에 따라 설치됩니다.
   - 불필요한 재부팅을 방지하려면 매월 유지 관리 시간 또는 소프트웨어 업데이트 시간에 패키지 설치를 예약합니다.

1. 배포 **지점** 페이지에서 설정을 구성하고 마법사를 완료합니다.
1. 구성 관리자 콘솔에서 배포 진행 상황을 모니터링합니다. 배포 **모니터링로** > 이동하여_\<패키지\>이름을 지정합니다._**Deployments** > 

### <a name="uninstall-the-mobility-service"></a>모빌리티 서비스 제거

구성 관리자 패키지를 만들어 모빌리티 서비스를 제거할 수 있습니다. 예를 들어 다음 스크립트에서는 모빌리티 서비스를 제거합니다.

```DOS
Time /t >> C:\logfile.log
REM ==================================================
REM ==== Check if Mob Svc is already installed =======
REM ==== If not installed no operation required ========
REM ==== Else run uninstall command =====================
REM ==== {275197FC-14FD-4560-A5EB-38217F80CBD1} is ====
REM ==== guid for Mob Svc Installer ====================
whoami >> C:\logfile.log
NET START | FIND "InMage Scout Application Service"
IF  %ERRORLEVEL% EQU 1 (GOTO :INSTALL) ELSE GOTO :UNINSTALL
:NOOPERATION
                echo "No Operation Required." >> c:\logfile.log
                GOTO :ENDSCRIPT
:UNINSTALL
                echo "Uninstall" >> C:\logfile.log
                MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
:ENDSCRIPT
```

## <a name="next-steps"></a>다음 단계

VM에 대한 [복제를 사용하도록 설정합니다.](vmware-azure-enable-replication.md)
