---
title: Azure Site Recovery를 사용한 VMware VM 및 물리적 서버 재해 복구를 위한 Mobility 서비스 수동 설치 | Microsoft Docs
description: Azure Site Recovery 서비스를 사용한 VMware VM 및 물리적 서버와 Azure 간 재해 복구를 위해 모바일 서비스 에이전트를 설치하는 방법을 알아봅니다.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: ramamill
ms.openlocfilehash: d0180a32f2a4258cb728869e2cb9f2abd57cc545
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52845963"
---
# <a name="install-the-mobility-service-manually-on-vmware-vms-and-physical-servers"></a>VMware VM 및 물리적 서버의 재해 복구를 위한 Mobility 서비스 수동 설치

[Azure Site Recovery](site-recovery-overview.md)를 사용하여 VMware VM 및 물리적 서버에 대한 재해 복구를 설정할 경우 각 온-프레미스 VMware VM 및 물리적 서버에 [Site Recovery Mobility 서비스](vmware-physical-mobility-service-overview.md)를 설치합니다.  Mobility 서비스는 머신에 기록된 데이터를 캡처하고 이를 Site Recovery 프로세스 서버에 전달합니다.

이 문서에서는 보호할 각 머신에 수동으로 Mobility 서비스를 설치하는 방법을 설명합니다.

## <a name="create-a-passphrase"></a>암호 만들기

설치하기 전에 설치 중 사용할 암호를 만듭니다.

1. 구성 서버에 로그인합니다.
2. 관리자 권한으로 명령 프롬프트를 엽니다.
3. 디렉터리를 Bin 폴더로 변경하고 암호 파일을 만듭니다.

    ```
    cd %ProgramData%\ASR\home\svsystems\bin
    genpassphrase.exe -v > MobSvc.passphrase
    ```
3. 암호 파일을 안전한 위치에 저장합니다. 


## <a name="install-the-service-from-the-ui"></a>UI에서 서비스 설치

>[!IMPORTANT]
> 다른 Azure 지역 간에 Azure IaaS VM을 복제할 경우에는 이 메서드를 사용하지 않습니다. 명령줄 기반 설치 메서드 insteadl를을 사용합니다.

1. 머신 운영 체제에 필요한 설치 관리자 버전을 찾습니다. 설치 관리자는 %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository 폴더에 있습니다. 필요한 설치 관리자를 [확인](vmware-physical-mobility-service-overview.md#installer-files)합니다.
2. 머신에 설치 파일을 복사하고 실행합니다.
3. **설치 옵션**에서 **Mobility 서비스 설치**를 선택합니다.
4. 설치 위치 > **설치**를 선택합니다.

    ![Mobility Service 설치 옵션 페이지](./media/vmware-physical-mobility-service-install-manual/mobility1.png)

5. **설치 진행률**에서 설치를 모니터링합니다. 설치가 완료되면 **구성으로 계속 진행**을 선택하여 Mobility 서비스를 구성 서버에 등록합니다.

    ![Mobility Service 등록 페이지](./media/vmware-physical-mobility-service-install-manual/mobility3.png)

6.  **구성 서버 세부 정보**에서 구성한 IP 주소 및 암호를 지정합니다.  

    ![Mobility Service 등록 페이지](./media/vmware-physical-mobility-service-install-manual/mobility4.png)

7. **등록**을 선택하여 등록을 완료합니다.

    ![Mobility Service 등록 최종 페이지](./media/vmware-physical-mobility-service-install-manual/mobility5.png)

## <a name="install-the-service-from-the-command-prompt"></a>명령줄 프롬프트에서 서비스 설치

### <a name="on-a-windows-machine"></a>Windows 머신

1. 보호하려는 서버의 로컬 폴더(예: C:\Temp)에 설치 관리자를 복사합니다. 

  ```
  cd C:\Temp
  ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
  MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
  cd C:\Temp\Extracted.
  ```
2. 다음과 같이 설치합니다.

  ```
  UnifiedAgent.exe /Role "MS" /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
  ```

3. 구성 서버에 에이전트를 등록합니다.

  ```
  cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
  UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
  ```

#### <a name="installation-settings"></a>설치 설정
**설정** | **세부 정보**
--- | ---
사용 현황 | UnifiedAgent.exe /Role <MS|MT> /InstallLocation <Install Location> /Platform “VmWare” /Silent
설정 로그 | %ProgramData%\ASRSetupLogs\ASRUnifiedAgentInstaller.log에 있습니다.
/Role | 필수 설치 매개 변수입니다. MS(Mobility 서비스) 또는 MT(마스터 대상) 설치 여부를 지정합니다.
/InstallLocation| 선택적 매개 변수. Mobility 서비스 설치 위치(모든 폴더)를 지정합니다.
/Platform | 필수. Mobility Service가 설치되는 플랫폼을 지정합니다. Mware VM/물리적 서버에 **VMware**, Azure VM에 **Azure**입니다. 
/Silent| 선택 사항입니다. 자동 모드에서의 설치 관리자 실행 여부를 지정합니다.

#### <a name="registration-settings"></a>등록 설정
**설정** | **세부 정보**
--- | ---
사용 현황 | UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
에이전트 구성 로그 | %ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log에 있습니다.
/CSEndPoint | 필수 매개 변수입니다. 구성 서버의 IP 주소를 지정합니다. 유효한 IP 주소를 사용합니다.
/PassphraseFilePath |  필수. 암호의 위치입니다. 유효한 UNC 또는 로컬 파일 경로를 사용합니다.


### <a name="on-a-linux-machine"></a>Linux 머신

1. 보호하려는 서버의 로컬 폴더(예: /tmp)에 설치 관리자를 복사합니다. 터미널에서 다음 명령을 실행합니다.
  ```
  cd /tmp ;

  tar -xvzf Microsoft-ASR_UA*release.tar.gz
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
사용 현황 | ./install -d <Install Location> -r <MS|MT> -v VmWare -q
-r | 필수 설치 매개 변수입니다. MS(Mobility 서비스) 또는 MT(마스터 대상) 설치 여부를 지정합니다.
일시 중지되고 | 선택적 매개 변수. Mobility 서비스 설치 위치(/usr/local/ASR)를 지정합니다.
-v | 필수. Mobility Service가 설치되는 플랫폼을 지정합니다. Mware VM/물리적 서버에 **VMware**, Azure VM에 **Azure**입니다. 
-q | 선택 사항입니다. 자동 모드에서의 설치 관리자 실행 여부를 지정합니다.

#### <a name="registration-settings"></a>등록 설정
**설정** | **세부 정보**
--- | ---
사용 현황 | cd /usr/local/ASR/Vx/bin<br/><br/> UnifiedAgentConfigurator.sh -i <CSIP> -P <PassphraseFilePath>
-i | 필수 매개 변수입니다. 구성 서버의 IP 주소를 지정합니다. 유효한 IP 주소를 사용합니다.
-P |  필수. 암호가 저장되는 파일의 전체 파일 경로입니다. 유효한 폴더 사용

## <a name="next-steps"></a>다음 단계
- [VMware VM에 대한 재해 복구 설정](vmware-azure-tutorial.md)
- [물리적 서버에 대한 재해 복구 설정](physical-azure-disaster-recovery.md)
