---
title: "소프트웨어 배포 도구를 사용하여 Azure Site Recovery에 대한 모바일 서비스 설치 자동화 | Microsoft Docs"
description: "이 문서에서는 System Center Configuration Manager와 같은 소프트웨어 배포 도구를 사용한 모바일 서비스를 설치할 수 있습니다."
services: site-recovery
documentationcenter: 
author: AnoopVasudavan
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/10/2017
ms.author: anoopkv
translationtype: Human Translation
ms.sourcegitcommit: 1e6ae31b3ef2d9baf578b199233e61936aa3528e
ms.openlocfilehash: 310f2a2fe793601d22952bf516a812bf4867bbec
ms.lasthandoff: 03/03/2017

---
# <a name="automate-mobility-service-installation-using-software-deployment-tools"></a>소프트웨어 배포 도구를 사용하여 모바일 서비스 설치 자동화

이 문서에서는 SCCM(System Center Configuration Manager)를 사용하여 데이터 센터에서 Azure Site Recovery 모바일 서비스를 배포하는 방법의 예제를 제공합니다. SCCM와 같은 소프트웨어 배포 도구를 사용하면 다음과 같은 장점이 있습니다.
* 배포 예약 - 소프트웨어 업데이트에 대한 계획된 유지 관리 창에서 새로 설치하고 업그레이드합니다.
* 동시에 수백 대의 서버에 대규모로 배포


> [!NOTE]
> 이 문서에서는 System Center Configuration Manager 2012 R2를 사용하여 배포 작업을 설명합니다. [Azure Automation 및 필요한 상태 구성](site-recovery-automate-mobility-service-install.md)을 사용하여 모바일 서비스 설치를 자동화할 수도 있습니다.

## <a name="prerequisites"></a>필수 조건
1. 사용자 환경에 이미 배포된 SCCM(System Center Configuration Manager)과 같은 소프트웨어 배포 도구입니다.
  * Azure Site Recovery를 사용하여 보호하려는 모든 **Windows Servers**와 모든 **Linux Servers**에 각각 하나씩, 두 개의 [장치 컬렉션](https://technet.microsoft.com/library/gg682169.aspx)을 만듭니다.
3. Azure Site Recovery에 이미 등록된 구성 서버입니다.
4. SCCM 서버에서 액세스할 수 있는 SMB 공유(보안 네트워크 파일 공유)입니다.

## <a name="deploy-mobility-service-on-computers-running-microsoft-windows-operating-systems"></a>Microsoft Windows 운영 체제를 실행하는 컴퓨터에 모바일 서비스 배포
> [!NOTE]
> 이 문서에서는 다음 항목을 가정합니다.
> 1. 구성 서버의 IP 주소가 192.168.3.121입니다.
> 2. 보안 네트워크 파일 공유가 \\\ContosoSecureFS\MobilityServiceInstallers입니다.

### <a name="step-1-prepare-for-deployment"></a>1단계: 배포 준비
1. 네트워크 공유에 폴더를 만들고 이름을 **MobSvcWindows**로 지정합니다.
2. 구성 서버에 로그온하고 관리 명령 프롬프트를 엽니다.
3. 다음 명령을 실행하여 암호 파일을 생성합니다.

    `cd %ProgramData%\ASR\home\svsystems\bin`

    `genpassphrase.exe -v > MobSvc.passphrase`
6. 네트워크 공유에서 MobSvc.passphrase 파일을 MobSvcWindows 폴더로 복사합니다.
5. 다음으로 명령을 실행하여 구성 서버에서 설치 관리자 리포지토리로 이동합니다.

  `cd %ProgramData%\ASR\home\svsystems\puhsinstallsvc\repository`
6. 네트워크 공유에서 **Microsoft-ASR\_UA\_*version*\_Windows\_GA\_*date*\_Release.exe**를 **MobSvcWindows** 폴더로 복사합니다.
7. 아래에 나열된 코드를 복사하고 **MobSvcWindows** 폴더에 **install.bat**로 저장합니다.
> [!NOTE]
> 아래 스크립트에서 [CSIP] 자리 표시자를 구성 서버 IP 주소의 실제 값으로 바꿔야 합니다.

  [!INCLUDE [site-recovery-sccm-windows-script](../../includes/site-recovery-sccm-windows-script.md)]

### <a name="step-2-create-a-package"></a>2단계: 패키지 만들기

1. System Center Configuration Manager 콘솔에 로그인합니다.
2. **소프트웨어 라이브러리** > **응용 프로그램 관리** > **패키지**로 이동합니다.
3. **패키지**를 마우스 오른쪽 단추로 클릭하고 **패키지 만들기**를 선택합니다.
4. 이름, 설명, 제조업체, 언어, 버전에 대한 값을 제공합니다.
5. **이 패키지는 원본 파일을 포함합니다** 확인란을 선택합니다.
6. **찾아보기** 단추를 클릭하고 설치 관리자가 저장된 네트워크 공유를 선택합니다(\\\ContosoSecureFS\MobilityServiceInstaller\MobSvcWindows).

  ![create-sccm-package](./media/site-recovery-install-mobility-service-using-sccm/create_sccm_package.png)

7. **만들려는 프로그램 유형 선택** 페이지에서 **표준 프로그램**을 선택하고 **다음**을 클릭합니다.

  ![create-sccm-package](./media/site-recovery-install-mobility-service-using-sccm/sccm-standard-program.png)
8. **이 표준 프로그램에 대한 정보 지정** 페이지에서 다음 입력을 제공하고 **다음**을 클릭합니다. (다른 입력은 해당 기본값으로 남겨 둡니다.)

  ![sccm-package-properties](./media/site-recovery-install-mobility-service-using-sccm/sccm-program-properties.png)   
| **매개 변수 이름** | **값** |
|--|--|
| 이름 | Microsoft Azure Mobility Service(Windows) 설치 |
| 명령 줄 | install.bat |
| 프로그램을 실행할 수 있습니다. | 사용자 로그온 여부 |
9. 다음 페이지에서 대상 운영 체제를 선택합니다. Mobility Service는 Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2에만 설치할 수 있습니다.

  ![sccm-package-properties-page2](./media/site-recovery-install-mobility-service-using-sccm/sccm-program-properties-page2.png)   
10. 마법사를 완료하려면 "다음"을 두 번 완료합니다.

> [!NOTE]
> 스크립트는 모바일 서비스 에이전트의 새로운 설치 및 이미 설치된 에이전트의 업그레이드/업데이트를 모두 지원합니다.

### <a name="step-3-deploy-the-package"></a>3단계: 패키지 배포
1. SCCM 콘솔에서 패키지를 마우스 오른쪽 단추로 클릭하고 **콘텐츠 배포**
  ![distribute-sccm-package](./media/site-recovery-install-mobility-service-using-sccm/sccm_distribute.png)를 선택합니다.
2. 패키지를 복사해야 하는 위치에 **[배포 지점](https://technet.microsoft.com/library/gg712321.aspx#BKMK_PlanForDistributionPoints)**을 선택합니다.
3. 마법사를 완료하면 패키지는 지정된 배포 지점에 복제하기 시작합니다.
4. 패키지 배포 작업이 완료되면 패키지를 마우스 오른쪽 단추로 클릭하고 **배포**
  ![deploy-sccm-package](./media/site-recovery-install-mobility-service-using-sccm/sccm_deploy.png)를 선택합니다.
5. 전제 조건 섹션에서 만든 Widows Server 장치 컬렉션을 배포의 대상 컬렉션으로 선택합니다.

  ![sccm-select-target-collection](./media/site-recovery-install-mobility-service-using-sccm/sccm-select-target-collection.png)
6. **콘텐츠 대상 지정** 페이지에서 **배포 지점**을 선택합니다.
7. **이 소프트웨어를 배포하는 방법을 제어하는 설정 지정** 페이지에서 요청한 대로 목적이 선택되었는지 확인합니다.

  ![sccm-deploy-select-purpose](./media/site-recovery-install-mobility-service-using-sccm/sccm-deploy-select-purpose.png)
8. **이 배포에 대한 일정 지정**에서 일정을 지정합니다. [패키지 일정 예약](https://technet.microsoft.com/library/gg682178.aspx)에 대해 자세히 알아봅니다.
9. 데이터 센터의 요구 사항에 따라 **배포 지점** 페이지의 속성을 구성하고 마법사를 완료합니다.

> [!TIP]
> 불필요한 재부팅을 방지하려면 매월 유지 관리 창 또는 소프트웨어 업데이트 창에서 패키지 설치를 예약합니다.

**모니터링** > **배포** > *[사용자의 패키지 이름]*
  ![monitor-sccm](./media/site-recovery-install-mobility-service-using-sccm/report.PNG)으로 이동하여 SCCM 콘솔을 사용한 배포 진행률을 모니터링할 수 있습니다.

## <a name="deploy-mobility-service-on-computers-running-linux-operating-systems"></a>Linux 운영 체제를 실행하는 컴퓨터에 모바일 서비스를 배포합니다.
> [!NOTE]
> 이 문서에서는 다음 항목을 가정합니다.
> 1. 구성 서버의 IP 주소가 192.168.3.121입니다.
> 2. 보안 네트워크 파일 공유가 \\\ContosoSecureFS\MobilityServiceInstallers입니다.

### <a name="step-1-prepare-for-deployment"></a>1단계: 배포 준비
1. 네트워크 공유에 폴더를 만들고 이름을 **MobSvcLinux**로 지정합니다.
2. 구성 서버에 로그온하고 관리 명령 프롬프트를 엽니다.
3. 다음 명령을 실행하여 암호 파일을 생성합니다.

    `cd %ProgramData%\ASR\home\svsystems\bin`

    `genpassphrase.exe -v > MobSvc.passphrase`
6. 네트워크 공유에서 MobSvc.passphrase 파일을 MobSvcWindows 폴더로 복사합니다.
5. 다음으로 명령을 실행하여 구성 서버에서 설치 관리자 리포지토리로 이동합니다.

  `cd %ProgramData%\ASR\home\svsystems\puhsinstallsvc\repository`
6. 네트워크 공유에서 다음 파일을 **MobSvcLinux** 폴더로 복사합니다.
  * Microsoft-ASR\_UA\_*version*\_OEL-64\_GA\_*date*\_Release.tar.gz
  * Microsoft-ASR\_UA\_*version*\_RHEL6-64\_GA\_*date*\_Release.tar.gz
  * Microsoft-ASR\_UA\_*version*\_RHEL7-64\_GA\_*date*\_Release.tar.gz
  * Microsoft-ASR\_UA\_*version*\_SLES11-SP3-64\_GA\_*date*\_Release.tar.gz

7. 아래에 나열된 코드를 복사하고 **MobSvcLinux** 폴더에 **install_linux.sh**로 저장합니다.
> [!NOTE]
> 아래 스크립트에서 [CSIP] 자리 표시자를 구성 서버 IP 주소의 실제 값으로 바꿔야 합니다.

  [!INCLUDE [site-recovery-sccm-linux-script](../../includes/site-recovery-sccm-linux-script.md)]

### <a name="step-2-create-a-package"></a>2단계: 패키지 만들기

1. System Center Configuration Manager 콘솔에 로그인합니다.
2. **소프트웨어 라이브러리** > **응용 프로그램 관리** > **패키지**로 이동합니다.
3. **패키지**를 마우스 오른쪽 단추로 클릭하고 **패키지 만들기**를 선택합니다.
4. 이름, 설명, 제조업체, 언어, 버전에 대한 값을 제공합니다.
5. **이 패키지는 원본 파일을 포함합니다** 확인란을 선택합니다.
6. **찾아보기** 단추를 클릭하고 설치 관리자가 저장된 네트워크 공유를 선택합니다(\\\ContosoSecureFS\MobilityServiceInstaller\MobSvcLinux).

  ![create-sccm-package](./media/site-recovery-install-mobility-service-using-sccm/create_sccm_package-linux.png)

7. **만들려는 프로그램 유형 선택** 페이지에서 **표준 프로그램**을 선택하고 **다음**을 클릭합니다.

  ![create-sccm-package](./media/site-recovery-install-mobility-service-using-sccm/sccm-standard-program.png)
8. **이 표준 프로그램에 대한 정보 지정** 페이지에서 다음 입력을 제공하고 **다음**을 클릭합니다. (다른 입력은 해당 기본값으로 남겨 둡니다.)

  ![sccm-package-properties](./media/site-recovery-install-mobility-service-using-sccm/sccm-program-properties-linux.png)   
| **매개 변수 이름** | **값** |
|--|--|
| 이름 | Microsoft Azure Mobility Service(Linux) 설치 |
| 명령 줄 | ./install_linux.sh |
| 프로그램을 실행할 수 있습니다. | 사용자 로그온 여부 |

9. 다음 페이지에서 **이 프로그램은 모든 플랫폼에서 실행할 수 있습니다**
  ![sccm-package-properties-page2](./media/site-recovery-install-mobility-service-using-sccm/sccm-program-properties-page2-linux.png)를 선택합니다.   

10. 마법사를 완료하려면 **다음**을 두 번 클릭합니다.
> [!NOTE]
> 스크립트는 모바일 서비스 에이전트의 새로운 설치 및 이미 설치된 에이전트의 업그레이드/업데이트를 모두 지원합니다.

### <a name="step-3-deploy-the-package"></a>3단계: 패키지 배포
1. SCCM 콘솔에서 패키지를 마우스 오른쪽 단추로 클릭하고 **콘텐츠 배포**
  ![distribute-sccm-package](./media/site-recovery-install-mobility-service-using-sccm/sccm_distribute.png)를 선택합니다.
2. 패키지를 복사해야 하는 위치에 **[배포 지점](https://technet.microsoft.com/library/gg712321.aspx#BKMK_PlanForDistributionPoints)**을 선택합니다.
3. 마법사를 완료하면 패키지는 지정된 배포 지점에 복제하기 시작합니다.
4. 패키지 배포 작업이 완료되면 패키지를 마우스 오른쪽 단추로 클릭하고 **배포**
  ![deploy-sccm-package](./media/site-recovery-install-mobility-service-using-sccm/sccm_deploy.png)를 선택합니다.
5. 전제 조건 섹션에서 만든 Linux Server 장치 컬렉션을 배포의 대상 컬렉션으로 선택합니다.

  ![sccm-select-target-collection](./media/site-recovery-install-mobility-service-using-sccm/sccm-select-target-collection-linux.png)
6. **콘텐츠 대상 지정** 페이지에서 **배포 지점**을 선택합니다.
7. **이 소프트웨어를 배포하는 방법을 제어하는 설정 지정** 페이지에서 요청한 대로 목적이 선택되었는지 확인합니다.

  ![sccm-deploy-select-purpose](./media/site-recovery-install-mobility-service-using-sccm/sccm-deploy-select-purpose.png)
8. **이 배포에 대한 일정 지정**에서 일정을 지정합니다. [패키지 일정 예약](https://technet.microsoft.com/library/gg682178.aspx)에 대해 자세히 알아봅니다.
9. 데이터 센터의 요구 사항에 따라 **배포 지점** 페이지의 속성을 구성하고 마법사를 완료합니다.

Mobility Service를 구성한 일정에 따라 Linux Server 장치 컬렉션에 설치합니다.

## <a name="other-methods-to-install-mobility-services"></a>모바일 서비스를 설치하는 다른 메서드
모바일 서비스를 설치하는 다른 방법에 대한 자세한 내용을 참고하세요.
* [GUI를 사용한 수동 설치](http://aka.ms/mobsvcmanualinstall)
* [명령줄을 사용한 수동 설치](http://aka.ms/mobsvcmanualinstallcli)
* [구성 서버를 사용한 푸시 설치](http://aka.ms/pushinstall)
* [Azure Automation 및 필요한 상태 구성을 사용한 자동화된 설치](http://aka.ms/mobsvcdscinstall)

## <a name="uninstall-mobility-service"></a>모바일 서비스 제거
설치와 마찬가지로 모바일 서비스를 제거하기 위해 SCCM 패키지를 만들 수 있습니다. 아래 스크립트를 사용하여 모바일 서비스를 제거합니다.

```
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
이제 가상 컴퓨터의 [보호를 활성화](https://docs.microsoft.com/en-us/azure/site-recovery/site-recovery-vmware-to-azure#step-6-replicate-applications)할 준비가 되었습니다.

