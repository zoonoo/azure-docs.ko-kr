---
title: "모바일 서비스(VMware/Azure 물리적 서버) 설치 | Microsoft Docs"
description: "온-프레미스 컴퓨터를 보호하기 위해 모바일 서비스 에이전트를 설치하는 방법을 알아봅니다."
services: site-recovery
documentationcenter: 
author: AnoopVasudavan
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: backup-recovery
ms.date: 06/29/2017
ms.author: anoopkv
ms.translationtype: HT
ms.sourcegitcommit: 469246d6cb64d6aaf995ef3b7c4070f8d24372b1
ms.openlocfilehash: 5a5fc9bac4f0ee54532f34fe957e3722123df178
ms.contentlocale: ko-kr
ms.lasthandoff: 09/27/2017

---

# <a name="install-mobility-service-vmware-or-physical-to-azure"></a>모바일 서비스(VMware/Azure 물리적 서버) 설치
Azure Site Recovery 모바일 서비스는 컴퓨터에서 데이터 쓰기를 캡처하여 프로세스 서버로 전달합니다. Azure에 복제하려는 모든 컴퓨터에 모바일 서비스(VMware VM 또는 물리적 서버)를 배포합니다. 다음 방법을 사용하여 보호하려는 서버에 모바일 서비스를 배포할 수 있습니다.


* [System Center Configuration Manager와 같은 소프트웨어 배포 도구를 사용하여 모바일 서비스 설치](site-recovery-install-mobility-service-using-sccm.md)
* [Azure Automation 및 자동화 DSC(필요한 상태 구성)를 사용하여 모바일 서비스 설치](site-recovery-automate-mobility-service-install.md)
* [GUI(그래픽 사용자 인터페이스)를 사용하여 수동으로 모바일 서비스 설치](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-manually-by-using-the-gui)
* [명령 프롬프트에서 수동으로 모바일 서비스 설치](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-manually-at-a-command-prompt)
* [Site Recovery에서 강제 설치를 사용하여 모바일 서비스 설치](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-by-push-installation-from-azure-site-recovery)


>[!IMPORTANT]
> 9.7.0.0 버전부터 모바일 서비스 설치 관리자는 Windows 가상 컴퓨터에 사용 가능한 최신 [Azure VM 에이전트](../virtual-machines/windows/extensions-features.md#azure-vm-agent)도 설치합니다. 컴퓨터가 Azure로 장애 조치되는 경우에는 VM 확장 사용에 대한 에이전트 설치 필수 조건을 충족합니다.

## <a name="prerequisites"></a>필수 조건
서버에 모바일 서비스를 수동으로 설치하기 전에 이러한 필수 조건 단계를 완료합니다.
1. 구성 서버에 로그인한 후 관리자로 명령 프롬프트 창을 엽니다.
2. 디렉터리를 Bin 폴더로 변경하고 암호 파일을 만듭니다.

    ```
    cd %ProgramData%\ASR\home\svsystems\bin
    genpassphrase.exe -v > MobSvc.passphrase
    ```
3. 암호 파일을 안전한 위치에 저장합니다. 이 파일은 모바일 서비스를 설치하는 동안 사용됩니다.
4. 지원되는 모든 운영 체제에 대한 모바일 서비스 설치 관리자는 %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository 폴더에 있습니다.

### <a name="mobility-service-installer-to-operating-system-mapping"></a>모바일 서비스 설치 관리자와 운영 체제 매핑

| 설치 관리자 파일 템플릿 이름| 운영 체제 |
|---|--|
|Microsoft-ASR\_UA\*Windows\*release.exe | Windows Server 2008 R2 SP1(64비트) </br> Windows Server 2012(64비트) </br> Windows Server 2012 R2(64비트) |
|Microsoft-ASR\_UA\*RHEL6-64*release.tar.gz| RHEL(Red Hat Enterprise Linux) 6.4, 6.5, 6.6, 6.7, 6.8(64비트만 해당) </br> CentOS 6.4, 6.5, 6.6, 6.7, 6.8(64비트만 해당) |
|Microsoft-ASR\_UA\*RHEL7-64\*release.tar.gz | RHEL(Red Hat Enterprise Linux) 7.1, 7.2(64비트만 해당) </br> CentOS 7.0, 7.1, 7.2(64비트만 해당)</br> CentOs 7.3(마이그레이션만 해당) |
|Microsoft-ASR\_UA\*SLES11-SP3-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP3(64비트만 해당)|
|Microsoft-ASR\_UA\*SLES11-SP4-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP4(64비트만 해당)|
|Microsoft-ASR\_UA\*OL6-64\*release.tar.gz | Oracle Enterprise Linux 6.4, 6.5(64비트만 해당)|
|Microsoft-ASR\_UA\*UBUNTU-14.04-64\*release.tar.gz | Ubuntu Linux 14.04(64비트만 해당)|


## <a name="install-mobility-service-manually-by-using-the-gui"></a>GUI를 사용하여 수동으로 모바일 서비스 설치

>[!IMPORTANT]
> **구성 서버**를 사용하여 Azure 구독/지역 간에 **Azure IaaS 가상 컴퓨터**를 복제하는 경우 **명령줄 기반 설치 메서드를 사용**합니다.

[!INCLUDE [site-recovery-install-mob-svc-gui](../../includes/site-recovery-install-mob-svc-gui.md)]

## <a name="install-mobility-service-manually-at-a-command-prompt"></a>명령 프롬프트에서 수동으로 모바일 서비스 설치

### <a name="command-line-installation-on-a-windows-computer"></a>Windows 컴퓨터에서 명령줄 설치
[!INCLUDE [site-recovery-install-mob-svc-win-cmd](../../includes/site-recovery-install-mob-svc-win-cmd.md)]

### <a name="command-line-installation-on-a-linux-computer"></a>Linux 컴퓨터에서 명령줄 설치
[!INCLUDE [site-recovery-install-mob-svc-lin-cmd](../../includes/site-recovery-install-mob-svc-lin-cmd.md)]


## <a name="install-mobility-service-by-push-installation-from-azure-site-recovery"></a>Azure Site Recovery에서 강제 설치를 사용하여 모바일 서비스 설치
Site Recovery를 사용하여 모바일 서비스의 강제 설치를 수행하려면 모든 대상 컴퓨터가 다음 필수 조건을 충족 해야 합니다.

[!INCLUDE [site-recovery-prepare-push-install-mob-svc-win](../../includes/site-recovery-prepare-push-install-mob-svc-win.md)]

[!INCLUDE [site-recovery-prepare-push-install-mob-svc-lin](../../includes/site-recovery-prepare-push-install-mob-svc-lin.md)]


> [!NOTE]
모바일 서비스를 설치한 후 Azure Portal에서 **+복제** 단추를 선택하여 이러한 VM 보호를 시작합니다.

## <a name="update-mobility-service"></a>모바일 서비스를 업데이트합니다.

> [!WARNING]
> 보호된 서버에서 모바일 서비스의 업데이트를 시작하기 전에 배포의 일부인 구성 서버, 확장 프로세스 서버 및 마스터 대상 서버를 업데이트했는지 확인하세요. [구성 서버를 업데이트하는 방법](site-recovery-vmware-to-azure-manage-configuration-server.md#updating-a-configuration-server) 및 [확장 프로세스 서버를 업데이트하는 방법](site-recovery-vmware-to-azure-manage-scaleout-process-server.md#upgrading-a-scale-out-process-server)에 대해 자세히 알아봅니다.

1. Azure Portal에서 <Your Vault> -> 복제된 항목 보기로 이동합니다.
2. **구성 서버**를 최신 버전으로 업데이트한 경우 읽을 수 있는 *새 Site recovery 복제 에이전트 업데이트를 사용할 수 있습니다.라는 알림이 표시됩니다. 클릭하여 설치합니다.*
3. 알림을 클릭하여 가상 컴퓨터 선택 페이지를 엽니다.
4. 모바일 서비스를 업그레이드하려는 가상 컴퓨터를 선택하고 확인 단추를 클릭합니다.
5. 선택한 가상 컴퓨터 각각에 대한 모바일 서비스 업데이트 작업을 시작합니다.

> [!NOTE]
> 모바일 서비스를 설치하는 데 사용되는 계정의 암호를 업데이트하는 방법에 대해 [자세히 알아봅니다](site-recovery-vmware-to-azure-manage-configuration-server.md). 

## <a name="uninstall-mobility-service-on-a-windows-server-computer"></a>Windows Server 컴퓨터에서 모바일 서비스 제거
Windows Server 컴퓨터에서 모바일 서비스를 제거하려면 다음 방법 중 하나를 사용합니다.

### <a name="uninstall-by-using-the-gui"></a>GUI를 사용하여 제거
1. 제어판에서 **프로그램**을 선택합니다.
2. **Microsoft Azure Site Recovery 모바일 서비스/마스터 대상 서버**를 선택한 다음 **제거**를 선택합니다.

### <a name="uninstall-at-a-command-prompt"></a>명령 프롬프트에서 제거
1. 관리자로 명령 프롬프트 창을 엽니다.
2. 모바일 서비스를 제거하려면 다음 명령을 실행합니다.

```
MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
```

## <a name="uninstall-mobility-service-on-a-linux-computer"></a>Linux 컴퓨터에서 모바일 서비스 제거
1. Linux 서버에서 **루트** 사용자로 로그인합니다.
2. 터미널에서 /user/local/ASR로 이동합니다.
3. 모바일 서비스를 제거하려면 다음 명령을 실행합니다.

```
uninstall.sh -Y
```

