---
title: Azure Site Recovery를 사용하여 Mobility Service 푸시 설치 문제 해결
description: Azure Site Recovery를 사용하여 재해 복구를 위해 복제를 사용하도록 설정할 때 Mobility Services 설치 오류 문제를 해결합니다.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.author: ramamill
ms.date: 04/03/2020
ms.openlocfilehash: 62c8240a4d2e50aa3b584f322baf7d2ee217c6d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98127875"
---
# <a name="troubleshoot-mobility-service-push-installation"></a>Mobility Service 푸시 설치 문제 해결

Mobility Service 설치는 복제를 사용하도록 설정하는 핵심 단계입니다. 이 단계의 성공은 필수 구성 요소 충족 및 지원되는 구성 작업에 달려 있습니다. Mobility Service를 설치하는 동안 접할 수 있는 가장 일반적인 문제의 원인은 다음과 같습니다.

* [자격 증명/권한 오류](#credentials-check-errorid-95107--95108)
* [로그인 실패](#login-failures-errorid-95519-95520-95521-95522)
* [연결 오류](#connectivity-failure-errorid-95117--97118)
* [파일 및 프린터 공유 오류](#file-and-printer-sharing-services-check-errorid-95105--95106)
* [WMI(Windows Management Instrumentation) 오류](#windows-management-instrumentation-wmi-configuration-check-error-code-95103)
* [지원되지 않는 운영 체제](#unsupported-operating-systems)
* [지원되지 않는 부트 구성](#unsupported-boot-disk-configurations-errorid-95309-95310-95311)
* [VSS(볼륨 섀도 복사본 서비스) 설치 실패](#vss-installation-failures)
* [디바이스 UUID 대신 GRUB 구성에 디바이스 이름 사용](#enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320)
* [LVM(논리 볼륨 관리자) 볼륨](#lvm-support-from-920-version)
* [다시 부팅 경고](#install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266)

복제를 사용하도록 설정하면 Azure Site Recovery가 VM(가상 머신)에 Mobility Service 에이전트를 설치하려고 시도합니다. 이 프로세스의 일부로 구성 서버는 가상 머신과 연결을 시도하고 에이전트를 복사합니다. 설치에 성공하려면 단계별 문제 해결 지침을 따르세요.

## <a name="credentials-check-errorid-95107--95108"></a>자격 증명 확인(ErrorID: 95107 및 95108)

복제를 사용하도록 설정하는 중에 선택한 사용자 계정이 유효하고 정확한지 확인합니다. 푸시 설치를 수행하려면 Azure Site Recovery에 **관리자 권한** 이 있는 **루트** 계정 또는 사용자 계정이 필요합니다. 그렇지 않으면 원본 컴퓨터에서 푸시 설치가 차단됩니다.

Windows의 경우(**오류 95107**) 사용자 계정에 로컬 계정 또는 도메인 계정을 사용하여 원본 컴퓨터에 대한 관리 액세스 권한이 있는지 확인합니다. 도메인 계정을 사용하지 않는 경우 로컬 컴퓨터에서 원격 사용자 액세스 제어를 사용하지 않도록 설정해야 합니다.

* 원격 사용자 액세스 제어를 사용하지 않도록 설정하는 레지스트리 키를 수동으로 추가하려면:

  * `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System`
  * 새 `DWORD`: `LocalAccountTokenFilterPolicy`를 추가합니다.
  * 값을 `1`로 설정합니다.

* 레지스트리 키를 추가하려면 명령 프롬프트에서 다음 명령을 실행합니다.

  `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`

Linux(**오류 95108**)의 경우 Mobility Service 에이전트를 성공적으로 설치하려면 **루트** 계정을 선택해야 합니다. 또한 SFTP(SSH 파일 전송 프로토콜) 서비스가 실행 중이어야 합니다. _sshd_config_ 파일에서 SFTP 하위 시스템 및 암호 인증을 사용하도록 설정하려면

1. **루트** 로 로그인합니다.
1. _/etc/ssh/sshd_config 파일_ 로 이동하여 `PasswordAuthentication`으로 시작하는 줄을 찾습니다.
1. 줄의 주석 처리를 제거하고 값을 `yes`로 변경합니다.
1. `Subsystem`으로 시작하는 줄을 찾아서 주석 처리를 제거합니다.
1. `sshd` 서비스를 다시 시작합니다.

선택한 사용자 계정의 자격 증명을 수정하려면 [이 지침](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)을 따릅니다.

## <a name="insufficient-privileges-failure-errorid-95517"></a>권한 부족 오류(ErrorID: 95517)

모바일 에이전트를 설치하도록 선택한 사용자에게 관리자 권한이 없는 경우 구성 서버/스케일 아웃 프로세스 서버는 모바일 에이전트 소프트웨어를 원본 컴퓨터에 복사할 수 없습니다. 이 오류는 액세스 거부 실패로 인해 발생합니다. 사용자 계정에 관리자 권한이 있는지 확인합니다.

선택한 사용자 계정의 자격 증명을 수정하려면 [이 지침](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)을 따릅니다.

## <a name="insufficient-privileges-failure-errorid-95518"></a>권한 부족 오류(ErrorID: 95518)

원본 컴퓨터에 로그인을 시도하는 동안 기본 도메인과 워크스테이션 간의 도메인 트러스트 관계 설정이 실패하면 오류 ID 95518과 함께 모바일 에이전트 설치가 실패합니다. 원본 컴퓨터의 주 도메인을 통해 로그인하려면 모바일 에이전트를 설치하는 데 사용된 사용자 계정에 관리 권한이 있는지 확인합니다.

선택한 사용자 계정의 자격 증명을 수정하려면 [이 지침](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)을 따릅니다.

## <a name="login-failures-errorid-95519-95520-95521-95522"></a>로그인 실패(ErrorID: 95519, 95520, 95521, 95522)

이 섹션에서는 자격 증명 및 로그인 오류 메시지에 대해 설명합니다.

### <a name="credentials-of-the-user-account-have-been-disabled-errorid-95519"></a>사용자 계정의 자격 증명이 사용하지 않도록 설정됨(ErrorID: 95519)

복제를 사용하도록 설정하면서 선택한 사용자 계정이 사용하지 않도록 설정되었습니다. 사용자 계정을 사용하려면 [이 문서](/powershell/module/Microsoft.PowerShell.LocalAccounts/Enable-LocalUser)를 참조하거나, _username_ 텍스트를 실제 사용자 이름으로 대체하여 다음 명령을 실행합니다.

`net user 'username' /active:yes`

### <a name="credentials-locked-out-because-of-multiple-failed-login-attempts-errorid-95520"></a>로그인 시도에 여러 번 실패하여 자격 증명이 잠김(ErrorID: 95520)

머신에 대한 액세스 시도가 여러 번 실패하면 사용자 계정이 잠깁니다. 실패 원인은 다음과 같을 수 있습니다.

* 구성 설정 중에 제공한 자격 증명이 잘못되었습니다.
* 복제를 사용하도록 설정하는 동안 선택한 사용자 계정이 잘못되었습니다.

[이 지침](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)에 따라 선택한 사용자 자격 증명을 수정하고 작업을 다시 시도합니다.

### <a name="logon-servers-arent-available-on-the-source-machine-errorid-95521"></a>원본 컴퓨터에서 로그온 서버를 사용할 수 없음(ErrorID: 95521)

이 오류는 원본 컴퓨터에서 로그온 서버를 사용할 수 없을 때 발생합니다. 로그온 서버를 사용할 수 없으면 로그인 요청이 실패하고 모바일 에이전트를 설치할 수 없습니다. 로그인에 성공하려면 원본 컴퓨터에서 로그온 서버를 사용할 수 있는지 확인하고 Netlogon 서비스를 시작합니다. 자세한 내용은 [Windows 로그온 시나리오](/windows-server/security/windows-authentication/windows-logon-scenarios)를 참조하세요.

### <a name="logon-service-isnt-running-on-the-source-machine-errorid-95522"></a>원본 컴퓨터에서 로그온 서비스가 실행되지 않음(ErrorID: 95522)

로그인 서비스가 원본 컴퓨터에서 실행되고 있지 않아 로그인 요청이 실패했습니다. 모바일 에이전트를 설치할 수 없습니다. 오류를 해결하려면 다음 방법 중 하나를 사용하여 원본 컴퓨터에서 `Netlogon` 서비스를 시작합니다.

* 명령 프롬프트에서 `Netlogon` 서비스를 시작하려면 `net start Netlogon` 명령을 실행합니다.
* 작업 관리자에서 `Netlogon` 서비스를 시작합니다.

## <a name="connectivity-failure-errorid-95117--97118"></a>연결 실패(ErrorID: 95117 및 97118)

구성 서버/스케일 아웃 프로세스 서버가 모바일 에이전트를 설치하기 위해 원본 VM에 연결하려고 시도합니다. 이 오류는 네트워크 연결 문제가 있어 원본 컴퓨터에 연결할 수 없을 때 발생합니다.

오류를 해결하려면:

* 사용자 계정에 로컬 계정 또는 도메인 계정을 사용하여 원본 컴퓨터에 대한 관리 액세스 권한이 있는지 확인합니다. 도메인 계정을 사용하지 않는 경우 로컬 컴퓨터에서 원격 사용자 액세스 제어를 사용하지 않도록 설정해야 합니다.
  * 원격 사용자 액세스 제어를 사용하지 않도록 설정하는 레지스트리 키를 수동으로 추가하려면:
    * `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System`
    * 새 `DWORD`: `LocalAccountTokenFilterPolicy`를 추가합니다.
    * 값을 `1`로 설정합니다.
  * 레지스트리 키를 추가하려면 명령 프롬프트에서 다음 명령을 실행합니다.

    `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`

* 구성 서버에서 원본 컴퓨터에 ping을 수행할 수 있는지 확인합니다. 복제를 사용하도록 설정하는 동안 스케일 아웃 프로세스 서버를 선택한 경우 프로세스 서버에서 원본 컴퓨터에 ping을 수행할 수 있어야 합니다.

* 가상 머신에서 파일 및 프린터 공유 서비스가 사용하도록 설정되어 있는지 확인합니다. [여기](vmware-azure-troubleshoot-push-install.md#file-and-printer-sharing-services-check-errorid-95105--95106)에 제공되는 단계를 확인하세요.

* 가상 머신에서 WMI 서비스가 사용하도록 설정되어 있는지 확인합니다. [여기](vmware-azure-troubleshoot-push-install.md#windows-management-instrumentation-wmi-configuration-check-error-code-95103)에 제공되는 단계를 확인하세요.

* 프로세스 서버에서 가상 머신의 네트워크 공유 폴더에 액세스할 수 있는지 확인합니다. [여기](vmware-azure-troubleshoot-push-install.md#check-access-for-network-shared-folders-on-source-machine-errorid-9510595523)에 제공되는 단계를 확인하세요.

* 원본 서버 컴퓨터 명령줄에서 `Telnet`을 사용하여 다음 명령과 같이 HTTPS 포트 135에서 구성 서버 또는 스케일 아웃 프로세스 서버를 ping합니다. 이 명령은 네트워크 연결 문제 또는 방화벽 포트 차단 문제가 있는지 확인합니다.

  `telnet <CS/ scale-out PS IP address> <135>`

* 추가로 Linux VM에서도 시작합니다.
  * 최신 OpenSSH, OpenSSH Server 및 OpenSSL 패키지가 설치되어 있는지 확인합니다.
  * SSH(Secure Shell)를 사용할 수 있고 22 포트에서 실행 중인지 확인합니다.
  * SFTP 서비스가 실행되어야 합니다. _sshd_config_ 파일에서 SFTP 하위 시스템 및 암호 인증을 사용하도록 설정하려면:

    1. **루트** 로 로그인합니다.
    1. _/etc/ssh/sshd_config_ 파일로 이동하여 `PasswordAuthentication`으로 시작하는 줄을 찾습니다.
    1. 줄의 주석 처리를 제거하고 값을 `yes`로 변경합니다.
    1. `Subsystem`으로 시작하는 줄을 찾아서 주석 처리를 제거합니다.
    1. `sshd` 서비스를 다시 시작합니다.

* 일정 기간 후에 적절한 응답이 없거나 연결된 호스트가 응답하지 못하여 기존 연결에 오류가 발생한 경우 연결 시도가 실패할 수 있습니다.
* 연결/네트워크/도메인 관련 문제일 수 있습니다. DNS 이름 확인 문제 또는 TCP 포트 소진 문제 때문일 수도 있습니다. 도메인에 이러한 알려진 문제가 있는지 확인합니다.

## <a name="connectivity-failure-errorid-95523"></a>연결 실패(ErrorID: 95523)

이 오류는 원본 컴퓨터가 있는 네트워크를 찾을 수 없거나 삭제되었거나 더 이상 사용할 수 없을 때 발생합니다. 오류를 해결하는 유일한 방법은 네트워크가 존재하는지 확인하는 것입니다.

## <a name="check-access-for-network-shared-folders-on-source-machine-errorid-9510595523"></a>원본 컴퓨터의 네트워크 공유 폴더에 대한 액세스 확인(오류 ID: 95105,95523)

가상 머신의 네트워크 공유 폴더가 지정된 자격 증명을 사용하여 원격으로 PS(프로세스 서버)에서 액세스할 수 있는지 확인합니다. 액세스를 확인하려면: 

1. 프로세스 서버 컴퓨터에 로그인합니다.
2. 파일 탐색기를 엽니다. 주소 표시줄에 `\\<SOURCE-MACHINE-IP>\C$`를 입력하고 Enter를 클릭합니다.

    ![PS에서 폴더 열기](./media/vmware-azure-troubleshoot-push-install/open-folder-process-server.PNG)

3. 파일 탐색기에서 자격 증명을 요청합니다. 사용자 이름과 암호를 입력하고 확인을 클릭합니다. <br><br/>

    ![자격 증명 제공](./media/vmware-azure-troubleshoot-push-install/provide-credentials.PNG)

    >[!NOTE]
    > 원본 컴퓨터가 도메인에 가입된 경우 사용자 이름과 도메인 이름을 `<domainName>\<username>`으로 입력합니다. 원본 컴퓨터가 작업 그룹에 있는 경우 사용자 이름만 제공합니다.

4. 연결에 성공한 경우 프로세스 서버에서 원본 컴퓨터의 폴더를 원격으로 볼 수 있습니다.

    ![원본 컴퓨터에서 볼 수 있는 폴더](./media/vmware-azure-troubleshoot-push-install/visible-folders-from-source.png)

연결에 실패한 경우 모든 필수 조건을 충족하는지 확인하세요.

## <a name="file-and-printer-sharing-services-check-errorid-95105--95106"></a>파일 및 프린터 공유 서비스 확인(ErrorID: 95105 및 95106)

연결을 모니터링한 후에는 가상 머신에 파일 및 프린터 공유 서비스가 사용하도록 설정되어 있는지 확인합니다. 이러한 설정은 모바일 에이전트를 원본 컴퓨터에 복사하기 위해 필요합니다.

**Windows 2008 R2 이전 버전** 의 경우:

* Windows 방화벽을 통해 파일 및 인쇄 공유가 가능하도록 설정하려면,
  1. **제어판** > **시스템 및 보안** > **Windows 방화벽** 을 엽니다. 왼쪽 창의 콘솔 트리에서 **고급 설정** > **인바운드 규칙** 을 선택합니다.
  1. 파일 및 프린터 공유(NB-Session-In) 및 파일 및 프린터 공유(SMB-In) 규칙을 찾습니다.
  1. 각 규칙을 마우스 오른쪽 단추로 클릭한 다음, **규칙 사용** 을 클릭합니다.

* 그룹 정책을 사용하여 파일 공유가 가능하도록 하려면:
  1. **시작** 으로 이동하여 `gpmc.msc`를 입력하고 검색합니다.
  1. 탐색 창에서 **로컬 컴퓨터 정책** > **사용자 구성** > **관리 템플릿** > **Windows 구성 요소** > **네트워크 공유 폴더** 를 엽니다.
  1. 세부 정보 창에서 **사용자 프로필 내의 파일 공유 안 함** 을 두 번 클릭합니다.

     그룹 정책 설정을 사용하지 않도록 설정하고 사용자의 파일 공유 기능을 사용하도록 설정하려면 **사용 안 함** 을 선택합니다.

  1. **확인** 을 선택하여 변경 내용을 저장합니다.

  자세한 내용은 [그룹 정책을 사용하여 파일 공유 사용 또는 사용 안 함](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754359(v=ws.10))을 참조하세요.

이후 버전의 Windows 또는 Linux에 대해 파일 및 프린터 공유를 사용하도록 설정하려면 [VMware VM 및 물리적 서버의 재해 복구를 위한 Mobility Service 설치](vmware-azure-install-mobility-service.md)의 지침을 따르세요.

## <a name="windows-management-instrumentation-wmi-configuration-check-error-code-95103"></a>WMI(Windows Management Instrumentation) 구성 확인(오류 코드: 95103)

파일 및 프린터 서비스를 확인한 후에는 방화벽을 통해 프라이빗, 공용 및 도메인 프로필에 대한 WMI 서비스를 사용하도록 설정합니다. 이 설정은 원본 머신에서 원격 실행을 완료하는 데 필요합니다.

WMI를 사용하도록 설정하려면:

1. **제어판** > **보안** 으로 이동하여 **Windows 방화벽** 을 선택합니다.
1. **설정 변경** 을 선택한 다음 **예외** 탭을 선택합니다.
1. **예외** 창에서 WMI(Windows Management Instrumentation)에 대한 확인란을 선택하여 방화벽을 통해 WMI 트래픽을 사용하도록 설정합니다.

다음 명령을 사용하여 명령 프롬프트에서 방화벽을 통해 WMI 트래픽을 사용하도록 설정할 수도 있습니다.

`netsh advfirewall firewall set rule group="windows management instrumentation (wmi)" new enable=yes`

기타 WMI 문제 해결 문서는 다음 문서에서 찾을 수 있습니다.

* [기본 WMI 테스트](https://techcommunity.microsoft.com/t5/ask-the-performance-team/bg-p/AskPerf)
* [WMI 문제 해결](/windows/win32/wmisdk/wmi-troubleshooting)
* [WMI 스크립트 및 WMI 서비스 관련 문제 해결](/previous-versions/tn-archive/ff406382(v=msdn.10))

## <a name="unsupported-operating-systems"></a>지원되지 않는 운영 체제

실패의 또 다른 일반적인 이유는 지원되지 않는 운영 체제일 수 있습니다. Mobility Service를 성공적으로 설치하려면 지원되는 운영 체제 및 커널 버전을 사용합니다. 프라이빗 패치를 사용하지 마세요.

Azure Site Recovery에서 어떤 운영 체제 및 커널 버전이 지원되는지 알아보려면 [지원 매트릭스 문서](vmware-physical-azure-support-matrix.md#replicated-machines)를 참조하세요.

## <a name="unsupported-boot-disk-configurations-errorid-95309-95310-95311"></a>지원되지 않는 부팅 디스크 구성(ErrorID: 95309, 95310, 95311)

### <a name="boot-and-system-partitions--volumes-arent-the-same-disk-errorid-95309"></a>부트 및 시스템 파티션/볼륨이 동일한 디스크가 아님(ErrorID: 95309)

9\.20 이전 버전에서는 서로 다른 디스크의 부트 및 시스템 파티션/볼륨이 지원되지 않는 구성이었습니다. [9.20 버전](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery)부터 이 구성이 지원됩니다.

### <a name="the-boot-disk-isnt-available-errorid-95310"></a>부팅 디스크를 사용할 수 없음(ErrorID: 95310)

부팅 디스크가 없는 가상 머신은 보호할 수 없습니다. 부팅 디스크는 장애 조치(failover) 작업 중에 가상 머신의 원활한 복구를 보장합니다. 부팅 디스크가 없으면 장애 조치(failover) 후 머신을 부팅하지 못합니다. 가상 머신에 부팅 디스크가 포함되어 있는지 확인하고 작업을 다시 시도합니다. 또한 동일한 시스템에 여러 부팅 디스크가 있는 것은 지원되지 않습니다.

### <a name="multiple-boot-disks-present-on-the-source-machine-errorid-95311"></a>원본 컴퓨터에 여러 개의 부팅 디스크가 있음(ErrorID: 95311)

부팅 디스크가 여러 개인 가상 머신은 [지원되는 구성](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage)이 아닙니다.

## <a name="system-partition-on-multiple-disks-errorid-95313"></a>여러 디스크에 있는 시스템 파티션(ErrorID: 95313)

9\.20 버전 이전에 여러 디스크의 루트 파티션 또는 볼륨 설정은 지원되지 않는 구성이었습니다. [9.20 버전](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery)부터 이 구성이 지원됩니다.

## <a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320"></a>UUID 대신 디바이스 이름이 GRUB 구성에 언급되어 보호를 사용하도록 설정하지 못함(ErrorID: 95320)

### <a name="possible-cause"></a>가능한 원인

GRUB(Grand Unified Bootloader) 구성 파일( _/boot/grub/menu.lst_, _/boot/grub/grub.cfg_, _/boot/grub2/grub. cfg_ 또는 _/etc/default/grub_)은 매개 변수 **root** 및 **resume** 값을 UUID(범용 고유 식별자) 대신 실제 디바이스 이름으로 포함할 수 있습니다. VM 다시 부팅 시 디바이스 이름이 변경될 수 있으므로 Site Recovery는 UUID 접근 방식을 요구합니다. 예를 들어 VM은 장애 조치(failover) 시 동일한 이름으로 온라인 상태로 전환하지 않을 수 있으며, 이로 인해 문제가 발생할 수 있습니다.

예를 들면 다음과 같습니다.

- 다음 줄은 GRUB 파일 _/boot/grub2/grub.cfg_ 에서 가져온 것입니다.

  `linux /boot/vmlinuz-3.12.49-11-default root=/dev/sda2  ${extra_cmdline} resume=/dev/sda1 splash=silent quiet showopts`

- 다음 줄은 GRUB 파일 _/boot/grub/menu.lst_ 에서 가져온 것입니다.

  `kernel /boot/vmlinuz-3.0.101-63-default root=/dev/sda2 resume=/dev/sda1 splash=silent crashkernel=256M-:128M showopts vga=0x314`

> [!NOTE]
> GRUB 행에는 UUID가 아닌 **root** 및 **resume** 매개 변수에 대한 실제 디바이스 이름이 포함됩니다.

### <a name="how-to-fix"></a>해결 방법

디바이스 이름을 해당 UUID로 바꿔야 합니다.

1. `blkid \<device name>` 명령을 실행하여 디바이스의 UUID를 찾습니다.

   예를 들면 다음과 같습니다.

   ```shell
   blkid /dev/sda1
   /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
   blkid /dev/sda2
   /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3"
   ```

1. 이제 디바이스 이름을 `root=UUID=\<UUID>`와 같은 형식의 UUID로 바꿉니다. 예를 들어, _/boot/grub2/grub.cfg_, _/boot/grub2/grub.cfg_ 또는 _/etc/default/grub_ 파일에 언급된 root 및 resume 매개 변수의 UUID로 디바이스 이름을 바꾸면 파일의 줄이 다음 줄과 같이 표시됩니다.

   `kernel /boot/vmlinuz-3.0.101-63-default root=UUID=62927e85-f7ba-40bc-9993-cc1feeb191e4 resume=UUID=6f614b44-433b-431b-9ca1-4dd2f6f74f6b splash=silent crashkernel=256M-:128M showopts vga=0x314`

1. 보호를 다시 시작합니다.

## <a name="install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266"></a>Mobility Service 설치가 완료되고 재부팅하라는 경고가 표시됨(ErrorID: 95265 & 95266)

Site Recovery Mobility Service에는 여러 구성 요소가 있으며, 그중에서 하나를 필터 드라이버라고 합니다. 필터 드라이버는 시스템 다시 부팅 중에만 시스템 메모리에 로드됩니다. 필터 드라이버 수정은 시스템 다시 부팅 시 새 필터 드라이버가 로드된 경우에만 실현할 수 있습니다.

> [!IMPORTANT]
> 이는 경고이며, 기존 복제는 새 에이전트 업데이트 후에도 작동합니다. 새 필터 드라이버의 혜택을 얻기 위해 언제든지 재부팅을 선택할 수 있지만, 재부팅하지 않을 경우 이전 필터 드라이버가 계속 작동합니다. 따라서 재부팅하지 않고 업데이트한 후에는 필터 드라이버를 제외한 **Mobility Service의 기타 개선 사항과 수정 사항의 혜택이 실현** 됩니다. 권장되지만 업그레이드할 때마다 다시 부팅해야 하는 것은 아닙니다. 다시 부팅이 필수인 경우에 대한 자세한 내용은 Azure Site Recovery의 서비스 업데이트에서 [Mobility Service 업그레이드 후 다시 부팅](service-updates-how-to.md#reboot-after-mobility-service-upgrade) 섹션을 설정하여 확인하세요.

> [!TIP]
>유지 관리 기간 동안 업그레이드 예약에 대한 모범 사례는 Azure Site Recovery의 서비스 업데이트에서 [최신 운영 체제/커널 지원](service-updates-how-to.md#support-for-latest-operating-systemskernels)을 참조하세요.

## <a name="lvm-support-from-920-version"></a>9.20 버전의 LVM 지원

9\.20 버전 이전에는 LVM(Logical Volume Manager)이 데이터 디스크에 대해서만 지원되었습니다. `/boot` 파티션은 LVM 볼륨이 아니라 디스크 파티션에 있어야 합니다.

[9.20 버전](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery)부터 [LVM의 OS 디스크](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage)가 지원됩니다.

## <a name="insufficient-space-errorid-95524"></a>공간 부족(ErrorID: 95524)

모바일 에이전트가 원본 컴퓨터에 복사될 때 최소 100MB의 여유 공간이 필요합니다. 원본 컴퓨터에 필요한 양의 여유 공간이 있는지 확인하고 작업을 다시 시도합니다.

## <a name="low-system-resources"></a>시스템 리소스 부족

이 문제에 대해 표시되는 가능한 오류 ID는 95572 및 95573입니다. 이 문제는 시스템의 사용 가능한 메모리가 부족하여 Mobility Service 설치를 위한 메모리를 할당할 수 없는 경우에 발생합니다. 설치를 진행하고 성공적으로 완료할 수 있도록 충분한 메모리가 확보되었는지 확인합니다.

## <a name="vss-installation-failures"></a>VSS 설치 오류

VSS(볼륨 섀도 복사본 서비스) 설치는 모바일 에이전트 설치의 일부입니다. 이 서비스는 애플리케이션 일치 복구 지점 생성 프로세스에 사용됩니다. VSS 설치 중 오류는 여러 이유로 인해 발생할 수 있습니다. 정확한 오류를 식별하려면 _C:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log_ 를 참조하세요. 몇 가지 일반적인 오류 및 해결 단계는 다음 섹션에서 강조 표시됩니다.

### <a name="vss-error--2147023170-0x800706be---exit-code-511"></a>VSS 오류 -2147023170 [0x800706BE] - 종료 코드 511

이 문제는 바이러스 백신 소프트웨어가 Azure Site Recovery 서비스의 작업을 차단할 때 가장 자주 나타납니다.

이 문제를 해결하려면:

1. [바이러스 백신 프로그램에서 제외되는 폴더](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) 목록을 검토하세요.
1. 바이러스 백신 공급자가 게시한 지침을 따라 Windows에서 DLL의 등록을 차단 해제합니다.

### <a name="vss-error-7-0x7---exit-code-511"></a>VSS 오류 7 [0x7] - 종료 코드 511

이 오류는 VSS를 설치할 메모리가 부족하여 발생하는 런타임 오류입니다. 이 작업을 성공적으로 완료하려면 디스크 공간을 늘립니다.

### <a name="vss-error--2147023824-0x80070430---exit-code-517"></a>VSS 오류 -2147023824 [0x80070430] - 종료 코드 517

이 오류는 Azure Site Recovery VSS 공급자 서비스가 [삭제를 위해 표시된](/previous-versions/ms838153(v=msdn.10)) 경우에 발생합니다. 다음 명령을 실행하여 원본 컴퓨터에서 수동으로 VSS를 설치해 보세요.

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

### <a name="vss-error--2147023841-0x8007041f---exit-code-512"></a>VSS 오류 -2147023841 [0x8007041F] - 종료 코드 512

이 오류는 Azure Site Recovery VSS 공급자 서비스 데이터베이스가 [잠긴](/previous-versions/ms833798(v=msdn.10)) 경우에 발생합니다. 명령 프롬프트에서 다음 명령줄을 실행하여 원본 컴퓨터에서 수동으로 VSS를 설치해 보세요.

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

오류가 발생하면 바이러스 백신 프로그램이나 기타 서비스가 **시작** 상태에서 멈추는지 확인합니다. **시작** 상태의 프로세스는 데이터베이스 서비스에 대한 잠금을 유지할 수 있습니다. VSS 공급자 설치에 실패하게 됩니다. **시작** 상태인 서비스가 없도록 확인한 다음 위의 작업을 다시 시도하세요.

### <a name="vss-exit-code-806"></a>VSS 종료 코드 806

이 오류는 설치에 사용되는 사용자 계정에 `CSScript` 명령을 실행할 수 있는 사용 권한이 없는 경우에 발생합니다. 사용자 계정에 스크립트를 실행하기 위해 필요한 권한을 제공하고 작업을 다시 시도합니다.

### <a name="other-vss-errors"></a>기타 VSS 오류

명령 프롬프트에서 다음 명령줄을 실행하여 원본 컴퓨터에서 수동으로 VSS 공급자 서비스를 설치해 보세요.

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

## <a name="vss-error---0x8004e00f"></a>VSS 오류 - 0x8004E00F

이 오류는 일반적으로 `DCOM` 및 `DCOM`의 심각한 문제로 인해 모바일 에이전트를 설치하는 동안 발생합니다.

다음 절차에 따라 오류의 원인을 확인합니다.

### <a name="examine-the-installation-logs"></a>설치 로그 검사

1. _C:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log_ 에 있는 설치 로그를 엽니다.
2. 다음 오류가 나타나면 이 문제가 있다는 것입니다.

    ```Output
    Unregistering the existing application...
    Create the catalogue object
    Get the collection of Applications

    ERROR:

    - Error code: -2147164145 [0x8004E00F]
    - Exit code: 802
    ```

문제를 해결하려면:

DCOM 문제 해결에 대한 지원을 받으려면 [Microsoft Windows 플랫폼 팀](https://aka.ms/Windows_Support)에 문의하세요.

DCOM 문제가 해결되면 명령 프롬프트에서 다음 명령을 사용하여 Azure Site Recovery VSS 공급자를 수동으로 다시 설치합니다.

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

애플리케이션 일관성이 재해 복구 요구 사항에 중요하지 않은 경우 VSS 공급자 설치를 건너뛸 수 있습니다.

Azure Site Recovery VSS 공급자 설치를 우회하고 Azure Site Recovery VSS 공급자 사후 설치를 수동으로 설치하려면:

1. 모바일 서비스를 설치합니다. **사후 설치 구성** 단계에서 설치가 실패합니다.
1. VSS 설치를 우회하려면:
   1. 다음 위치에 있는 Azure Site Recovery Mobility Service 설치 디렉터리를 엽니다.

      _C:\Program Files (x86)\Microsoft Azure Site Recovery\agent_

   1. 항상 성공하도록 다음 줄을 추가하여 Azure Site Recovery VSS 공급자 설치 스크립트 _InMageVSSProvider_Install_ 및 _InMageVSSProvider_Uninstall.cmd_ 를 수정합니다.

      ```plaintext
      rem @echo off
      setlocal
      exit /B 0
      ```

1. 모바일 에이전트를 수동으로 설치합니다.
1. 설치가 성공하고 다음 단계인 **구성** 으로 이동할 때 추가한 줄을 제거합니다.
1. VSS 공급자를 설치하려면 관리자로 명령 프롬프트를 열고 다음 명령을 실행합니다.

   `"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

1. Azure Site Recovery VSS 공급자가 Windows 서비스에서 서비스로 설치되었는지 확인합니다. 구성 요소 서비스 MMC를 열어 VSS 공급자가 나열되는지 확인합니다.
1. VSS 공급자 설치가 계속 실패하면 기술 지원과 협력하여 CAPI2(Cryptographic Application Programming Interface)의 권한 오류를 해결합니다.

## <a name="vss-provider-installation-fails-because-the-cluster-service-being-enabled-on-non-cluster-machine"></a>클러스터 서비스가 클러스터가 아닌 컴퓨터에서 사용하도록 설정되어 있기 때문에 VSS 공급자 설치가 실패합니다.

이 문제로 인해 Azure Site Recovery VSS 공급자 설치 중에 Azure Site Recovery 모바일 에이전트 설치가 실패합니다. 실패는 VSS 공급자 설치를 방해하는 `COM+` 관련 문제가 있기 때문입니다.

### <a name="to-identify-the-issue"></a>이 문제를 식별하려면

구성 서버의 _C:\ProgramData\ASRSetupLogs\UploadedLogs\<date-time>UA_InstallLogFile.log_ 에 있는 로그에서 다음 예외를 찾을 수 있습니다.

```plaintext
COM+ was unable to talk to the Microsoft Distributed Transaction Coordinator (Exception from HRESULT: 0x8004E00F)
```

문제를 해결하려면:

1. 이 시스템이 클러스터가 아닌 컴퓨터이고 클러스터 구성 요소가 사용되지 않는지 확인합니다.
1. 구성 요소가 사용되지 않는 경우 컴퓨터에서 클러스터 구성 요소를 제거합니다.

## <a name="drivers-are-missing-on-the-source-server"></a>원본 서버에 드라이버가 없습니다.

모바일 에이전트 설치에 실패하면 _C:\ProgramData\ASRSetupLogs_ 아래의 로그를 검사하여 일부 컨트롤 집합에 필요한 드라이버 중 일부가 누락되었는지 확인합니다.

문제를 해결하려면:

1. `regedit.msc`와 같은 레지스트리 편집기를 사용하여 레지스트리를 엽니다.
1. `HKEY_LOCAL_MACHINE\SYSTEM` 노드를 엽니다.
1. `SYSTEM` 노드에서 컨트롤 집합을 찾습니다.
1. 각 컨트롤 집합을 열고 다음 Windows 드라이버가 있는지 확인합니다.

   * Atapi
   * Vmbus
   * Storflt
   * Storvsc
   * Intelide

1. 누락된 드라이버를 다시 설치합니다.

## <a name="next-steps"></a>다음 단계

VMware VM에 대한 재해 복구를 설정하는 [방법을 자세히 알아봅니다](vmware-azure-tutorial.md).
