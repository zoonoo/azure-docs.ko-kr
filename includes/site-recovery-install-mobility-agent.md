---
title: 
description: 
services: site-recovery
documentationcenter: 
author: 
manager: 
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: 
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 
ms.author: 
translationtype: Human Translation
ms.sourcegitcommit: 4871e5959483058aa957e3373ddc5121b398e1d9
ms.openlocfilehash: 1d6d595bf788fc3fdba0bbc98887614e17c791dc
ms.lasthandoff: 02/17/2017


---



### <a name="install-the-mobility-service"></a>모바일 서비스 설치
가상 컴퓨터 및 물리적 서버에 대해 보안을 설정하는 첫 번째 단계는 모바일 서비스를 설치하는 것입니다. 다음과 같은 몇 가지 방법으로 이를 수행할 수 있습니다.

* **프로세스 서버 푸시**: 컴퓨터에서 복제를 사용하도록 설정할 때 프로세스 서버에서 모바일 서비스 구성 요소를 푸시 및 설치합니다. 
*참고* 컴퓨터가 이미 최신 버전의 구성 요소를 실행 중인 경우 푸시 설치가 발생하지 않습니다.
* **엔터프라이즈 푸시**: WSUS 또는 System Center Configuration Manager 또는 [Azure Automation 및 필요한 상태 구성](site-recovery-automate-mobility-service-install.md)과 같은 엔터프라이즈 푸시 프로세스를 사용하여 구성 요소를 자동으로 설치합니다. 이렇게 하려면 먼저 구성 서버를 설정합니다.
* **수동 설치**: 복제하려는 각 컴퓨터에 구성 요소를 수동으로 설치합니다. 이렇게 하려면 먼저 구성 서버를 설정합니다.

#### <a name="prepare-for-automatic-push-on-windows-machines"></a>Windows 컴퓨터에서 자동 푸시 준비
프로세스 서버에서 모바일 서비스를 자동으로 설치할 수 있도록 Windows 컴퓨터를 준비하는 방법은 다음과 같습니다.

1. 프로세스 서버가 컴퓨터에 액세스하는 데 사용할 수 있는 계정을 만듭니다. 계정에 관리자 권한(로컬 또는 도메인)이 있어야 하고 푸시 설치에만 사용됩니다.

   > [!NOTE]
   > 도메인 계정을 사용하지 않는 경우 로컬 컴퓨터에서 원격 사용자 액세스 제어를 사용하지 않도록 설정해야 합니다. 그러려면 레지스터의 HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System에서 값이 1인 LocalAccountTokenFilterPolicy DWORD 항목을 추가합니다. CLI 형식에서 레지스트리 항목을 추가하려면 **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`**을 입력합니다.
   >
   >
2. 보호하려는 컴퓨터의 Windows 방화벽에서 **방화벽을 통해 앱 또는 기능 허용**을 선택합니다. **파일 및 프린터 공유**와 **WMI(Windows Management Instrumentation)**를 사용하도록 설정합니다. 도메인에 속하는 컴퓨터의 경우 GPO를 사용하여 방화벽 설정을 구성할 수 있습니다.

   ![방화벽 설정](./media/site-recovery-vmware-to-azure/mobility1.png)
3. 만든 계정을 추가합니다.

   * **cspsconfigtool**을 엽니다. 바탕 화면에 바로 가기로 사용 가능하며 [설치 위치]\home\svsystems\bin 폴더에 있습니다.
   * **계정 관리** 탭에서 **계정 추가**를 클릭합니다.
   * 만든 계정을 추가합니다. 계정을 추가한 후 컴퓨터에 복제를 사용하도록 설정할 때 이 자격 증명을 제공해야 합니다.

#### <a name="prepare-for-automatic-push-on-linux-servers"></a>Linux 서버에서 자동 푸시 준비
1. 보호하려는 Linux 컴퓨터가 [보호된 컴퓨터 필수 조건](#protected-machine-prerequisites)에 설명된 대로 지원되는지 확인합니다. Linux 컴퓨터와 프로세스 서버 간에 네트워크가 연결되어 있는지 확인합니다.
2. 프로세스 서버가 컴퓨터에 액세스하는 데 사용할 수 있는 계정을 만듭니다. 계정은 원본 Linux 서버의 루트 사용자여야 하며 푸시 설치에만 사용됩니다.

   * **cspsconfigtool**을 엽니다. 바탕 화면에 바로 가기로 사용 가능하며 [설치 위치]\home\svsystems\bin 폴더에 있습니다.
   * **계정 관리** 탭에서 **계정 추가**를 클릭합니다.
   * 만든 계정을 추가합니다. 계정을 추가한 후 컴퓨터에 복제를 사용하도록 설정할 때 이 자격 증명을 제공해야 합니다.
3. 원본 Linux 서버의 /etc/hosts 파일에 로컬 호스트 이름을 모든 네트워크 어댑터와 연결된 IP 주소에 매핑하는 항목이 포함되어 있는지 확인합니다.
4. 복제할 컴퓨터에 최신 openssh, openssh-server, openssl 패키지를 설치합니다.
5. 포트 22에 SSH를 사용하고 실행 중인지 확인합니다.
6. 다음과 같이 sshd_config 파일에서 SFTP 하위 시스템 및 암호 인증을 사용하도록 설정합니다.

   * 루트로 로그인합니다.
   * /etc/ssh/sshd_config 파일에서 **PasswordAuthentication**으로 시작하는 줄을 찾습니다.
   * 줄의 주석 처리를 제거하고 값을 **아니요**에서 **예**로 변경합니다.
   * **Subsystem** 으로 시작하는 줄을 찾아서 주석 처리를 제거합니다.

     ![Linux](./media/site-recovery-vmware-to-azure/mobility2.png)

### <a name="install-the-mobility-service-manually"></a>모바일 서비스 수동 설치
설치 관리자는 구성 서버의 **C:\Program Files (x86)\Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository**에서 사용할 수 있습니다.

| 원본 운영 체제 | 모바일 서비스 설치 파일 |
| --- | --- |
| Windows Server(64비트만 해당) |Microsoft-ASR_UA_9.*.0.0_Windows_* release.exe |
| CentOS 6.4, 6.5, 6.6(64비트만 해당) |Microsoft-ASR_UA_9.*.0.0_RHEL6-64_*release.tar.gz |
| SUSE Linux Enterprise Server 11 SP3(64비트만 해당) |Microsoft-ASR_UA_9.*.0.0_SLES11-SP3-64_*release.tar.gz |
| Oracle Enterprise Linux 6.4, 6.5(64비트만 해당) |Microsoft-ASR_UA_9.*.0.0_OL6-64_*release.tar.gz |

#### <a name="install-mobility-service-on-a-windows-server"></a>Windows 서버에 모바일 서비스 설치
1. 관련 설치 관리자를 다운로드 및 실행합니다.
2. **시작하기 전에**에서 **모바일 서비스**를 선택합니다.

    ![모바일 서비스](./media/site-recovery-vmware-to-azure/mobility3.png)
3. **구성 서버 세부 정보**에서 구성 서버의 IP 주소 및 통합 설치 프로그램을 실행했을 때 생성된 암호를 지정합니다. 구성 서버에서 **<SiteRecoveryInstallationFolder>\home\sysystems\bin\genpassphrase.exe –v**을 실행하여 암호를 검색할 수 있습니다.

    ![모바일 서비스](./media/site-recovery-vmware-to-azure/mobility6.png)
4. **설치 위치**에서 기본 설정을 그대로 두고 **다음**을 클릭하여 설치를 시작합니다.
5. **설치 진행률** 에서 설치를 모니터링하고 메시지가 표시되면 컴퓨터를 다시 시작합니다. 서비스를 설치한 후 포털에서 상태가 업데이트되는 데 약 15분 정도 걸릴 수 있습니다.

#### <a name="install-mobility-service-on-a-windows-server-using-the-command-prompt"></a>명령 프롬프트를 사용하여 Windows 서버에 모바일 서비스를 설치합니다.
1. 보호하려는 서버의 로컬 폴더(예: C:\Temp)에 설치 관리자를 복사합니다. 구성 서버의 **[설치 위치]\home\svsystems\pushinstallsvc\repository** 아래에서 설치 관리자를 찾을 수 있습니다. Windows 운영 체제 패키지의 이름은 Microsoft-ASR_UA_9.3.0.0_Windows_GA_17thAug2016_release.exe와 유사합니다.
2. MobilitySvcInstaller.exe로 파일 이름 바꾸기
3. 다음 명령을 실행하여 MSI 설치 관리자를 추출합니다.

    ``C:\> cd C:\Tempww
    ``C:\Temp> MobilitySvcInstaller.exe /q /xC:\Temp\Extracted``
    ``C:\Temp> cd Extracted``
    ``C:\Temp\Extracted> UnifiedAgent.exe /Role "Agent" /CSEndpoint "IP Address of Configuration Server" /PassphraseFilePath <Full path to the passphrase file>``

##### <a name="full-command-line-syntax"></a>전체 명령줄 구문
    UnifiedAgent.exe [/Role <Agent/MasterTarget>] [/InstallLocation <Installation Directory>] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>] [/LogFilePath <Log File Path>]<br/>

**매개 변수**

* **/Role:** 필수. 모바일 서비스를 설치해야 하는지 여부를 지정합니다. 입력 값 에이전트|MasterTarget
* **/InstallLocation:** 필수. 서비스를 설치할 위치를 지정합니다.
* **/PassphraseFilePath:** 필수. 구성 서버 암호입니다.
* **/LogFilePath:** 필수. 설치 로그 파일을 만들어야 하는 위치입니다.

#### <a name="uninstall-the-mobility-service-manually"></a>모바일 서비스 수동 설치 제거
제어판에서 프로그램 추가/제거를 사용하거나 다음 명령줄 명령을 사용하여 모바일 서비스를 설치 제거할 수 있습니다. MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1}

#### <a name="install-the-mobility-service-on-a-linux-server"></a>Linux 서버에 모바일 서비스 설치
1. 위 표를 기준으로 해당 tar 아카이브를 복제하려는 Linux 컴퓨터에 복사합니다.
2. 셸 프로그램을 열고 다음을 실행하여 압축된 tar 아카이브를 로컬 경로로 추출합니다: `tar -xvzf Microsoft-ASR_UA_8.5.0.0*`
3. tar 아카이브의 내용을 추출한 로컬 디렉터리에 passphrase.txt 파일을 만듭니다. 이렇게 하려면 구성 서버의 C:\ProgramData\Microsoft Azure Site Recovery\private\connection.passphrase에서 암호를 복사하고 셸에서 *`echo <passphrase> >passphrase.txt`*를 실행하여 passphrase.txt에 저장합니다.
4. *`sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i <IP address> -p <port> -s y -c https -P passphrase.txt`*를 실행하여 모바일 서비스를 설치합니다.
5. 구성 서버의 내부 IP 주소를 지정하고 포트 443이 선택되어 있는지 확인합니다. 서비스를 설치한 후 포털에서 상태가 업데이트되는 데 약 15분 정도 걸릴 수 있습니다.

**또한 명령줄에서도 설치할 수 있습니다**.

구성 서버의 C:\Program Files (x86)\InMage Systems\private\connection에서 암호를 복사하고 구성 서버에 "passphrase.txt"로 저장합니다. 그런 후에 다음 명령을 실행합니다. 예제에서는 구성 서버 IP 주소가 104.40.75.37이며 HTTPS 포트는 443이어야 합니다.


운영 서버에 설치하려면

    ./install -t both -a host -R Agent -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt

마스터 대상 서버에 설치하려면

    ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt




