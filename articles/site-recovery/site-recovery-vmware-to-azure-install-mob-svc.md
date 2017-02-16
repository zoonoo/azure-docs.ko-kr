---
title: "모바일 서비스(VMware/Azure 물리적 서버) 설치 | Microsoft Docs"
description: "이 문서에서는 온-프레미스 컴퓨터에 모바일 서비스 에이전트를 설치하여 온-프레미스 컴퓨터를 보호하는 방법을 설명합니다."
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
ms.date: 12/9/2016
ms.author: anoopkv
translationtype: Human Translation
ms.sourcegitcommit: 9dfcdeb4dc3e84089eb8031272b870f87d90b689
ms.openlocfilehash: 09a84e5afa4bd462207179c7505bc80400a5a3a5

---

# <a name="installing-mobility-service-vmwarephysical-to-azure"></a>모바일 서비스(VMware/Azure 물리적 서버) 설치
모바일 서비스는 Azure에 복제하고자 하는 모든 컴퓨터(VMware VM 또는 물리적 서버)에 배포됩니다. 컴퓨터에 기록된 데이터를 캡처하고 프로세스 서버에 전달합니다.  보호해야 하는 서버에 다음과 같은 방법으로 모바일 서비스를 배포할 수 있습니다.
1. [System Center Configuration Manager와 같은 소프트웨어 배포 도구를 사용하여 모바일 서비스 설치](site-recovery-install-mobility-service-using-sccm.md)
2. [Azure Automation 및 DSC(필요한 상태 구성)를 사용하여 모바일 서비스 설치](site-recovery-automate-mobility-service-install.md)
3. [GUI(그래픽 사용자 인터페이스)를 사용하여 수동으로 모바일 서비스 설치](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-manually-using-the-graphical-user-interface)
4. [명령줄을 사용하여 수동으로 모바일 서비스 설치](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-manually-using-command-line)
5. [Azure Site Recovery에서 강제 설치를 사용하여 모바일 서비스 설치](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-using-push-install-from-azure-site-recovery)

## <a name="prerequisites"></a>필수 조건
서버에 모바일 서비스를 수동으로 설치하기 전에 이러한 필수 조건에 나와 있는 작업을 수행합니다.
1. 구성 서버에 로그온하고 관리 권한으로 명령 프롬프트를 엽니다.
2. 디렉터리를 Bin 폴더로 변경하고 암호 파일을 만듭니다.

  ```
  cd %ProgramData%\ASR\home\svsystems\bin
  genpassphrase.exe -v > MobSvc.passphrase
  ```
3. 이 파일은 모바일 서비스 설치 중에 사용해야 하므로 안전한 위치에 저장합니다.
4. 이 디렉터리에서 지원되는 모든 운영 체제에 대한 모바일 서비스 설치 관리자를 찾을 수 있습니다.     

  `%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository`

#### <a name="mobility-service-installer-to-operating-system-mapping"></a>모바일 서비스 설치 관리자와 운영 체제 매핑

| 설치 관리자 파일 템플릿 이름| 운영 체제 |
|---|--|
|Microsoft-ASR\_UA\*Windows\*release.exe | Windows Server 2008 R2(64비트) SP1</br> Windows Server 2012(64비트) </br> Windows Server 2012 R2(64비트) |
|Microsoft-ASR\_UA\*RHEL6-64*release.tar.gz| RHEL 6.4, 6.5, 6.6(64비트만 해당) </br> CentOS 6.4, 6.5, 6.6(64비트만 해당) |
|Microsoft-ASR\_UA\*SLES11-SP3-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP3(64비트만 해당)|
|Microsoft-ASR_UA\*OL6-64\*release.tar.gz | Oracle Enterprise Linux 6.4, 6.5(64비트만 해당)|
|Microsoft-ASR_UA\*Ubuntu-14.04-64\*release.tar.gz | Ubuntu 14.04(64비트만 해당)|

## <a name="install-mobility-service-manually-using-the-graphical-user-interface"></a>그래픽 사용자 인터페이스를 사용하여 수동으로 모바일 서비스 설치

>[!NOTE]
> 그래픽 사용자 인터페이스 기반 설치는 Microsoft Windows 운영 체제에만 지원됩니다.

[!INCLUDE [site-recovery-install-mob-svc-gui](../../includes/site-recovery-install-mob-svc-gui.md)]

## <a name="install-mobility-service-manually-using-command-line"></a>명령줄을 사용하여 수동으로 모바일 서비스 설치
### <a name="command-line-based-install-on-windows-computers"></a>Windows 컴퓨터에서 명령줄 기반 설치
[!INCLUDE [site-recovery-install-mob-svc-win-cmd](../../includes/site-recovery-install-mob-svc-win-cmd.md)]

### <a name="command-line-based-install-on-linux-computers"></a>Linux 컴퓨터에서 명령줄 기반 설치
[!INCLUDE [site-recovery-install-mob-svc-lin-cmd](../../includes/site-recovery-install-mob-svc-lin-cmd.md)]


## <a name="install-mobility-service-using-push-install-from-azure-site-recovery"></a>Azure Site Recovery에서 강제 설치를 사용하여 모바일 서비스 설치
Azure Site Recovery를 사용하여 모바일 서비스의 강제 설치를 수행하려면 모든 대상 컴퓨터에서 다음과 같은 필수 조건을 충족해야 합니다.

[!INCLUDE [site-recovery-prepare-push-install-mob-svc-win](../../includes/site-recovery-prepare-push-install-mob-svc-win.md)]

[!INCLUDE [site-recovery-prepare-push-install-mob-svc-lin](../../includes/site-recovery-prepare-push-install-mob-svc-lin.md)]


## <a name="next-steps"></a>다음 단계
모바일 서비스가 설치되면 Azure Portal에서 **+복제** 단추를 사용하여 이러한 VM에 대한 보호를 사용하도록 설정할 수 있습니다.



<!--HONumber=Jan17_HO2-->


