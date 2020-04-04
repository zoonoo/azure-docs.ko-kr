---
title: Azure 사이트 복구를 통해 모빌리티 서비스 푸시 설치 문제 해결
description: Azure 사이트 복구를 사용 하 고 재해 복구에 대 한 복제를 사용 하는 경우 이동성 서비스 설치 오류 문제를 해결 합니다.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.author: ramamill
ms.date: 04/03/2020
ms.openlocfilehash: 1afd931249d4dbeda2b4b25f822837e2a564f959
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656317"
---
# <a name="troubleshoot-mobility-service-push-installation"></a>트러블슈팅 모빌리티 서비스 푸시 설치

모빌리티 서비스 설치는 복제를 활성화하는 핵심 단계입니다. 이 단계의 성공 여부는 필수 구성을 충족하고 지원되는 구성으로 작업하는 데 달려 있습니다. 모빌리티 서비스 설치 중에 발생할 수 있는 가장 일반적인 오류는 다음과 같습니다.

* [자격 증명/권한 오류](#credentials-check-errorid-95107--95108)
* [로그인 실패](#login-failures-errorid-95519-95520-95521-95522)
* [연결 오류](#connectivity-failure-errorid-95117--97118)
* [파일 및 프린터 공유 오류](#file-and-printer-sharing-services-check-errorid-95105--95106)
* [WMI(Windows 관리 계측) 오류](#windows-management-instrumentation-wmi-configuration-check-error-code-95103)
* [지원되지 않는 운영 체제](#unsupported-operating-systems)
* [지원되지 않는 부팅 구성](#unsupported-boot-disk-configurations-errorid-95309-95310-95311)
* [VSS(볼륨 섀도우 복사 서비스) 설치 실패](#vss-installation-failures)
* [디바이스 UUID 대신 GRUB 구성에 디바이스 이름 사용](#enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320)
* [논리 볼륨 관리자(LVM) 볼륨](#lvm-support-from-920-version)
* [다시 부팅 경고](#install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266)

복제를 사용하도록 설정하면 Azure 사이트 복구가 가상 시스템(VM)에 Mobility 서비스 에이전트를 설치하려고 시도합니다. 이 프로세스의 일부로 구성 서버는 가상 컴퓨터와 연결하고 에이전트를 복사하려고 시도합니다. 성공적인 설치를 지원하려면 단계별 문제 해결 지침을 따르십시오.

## <a name="credentials-check-errorid-95107--95108"></a>자격 증명 확인(ErrorID: 95107 및 95108)

사용 복제 중에 선택한 사용자 계정이 유효하고 정확한지 확인합니다. Azure 사이트 복구에는 푸시 설치를 수행하기 위해 **관리자 권한이** 있는 **루트** 계정 또는 사용자 계정이 필요합니다. 그렇지 않으면 푸시 설치가 소스 컴퓨터에서 차단됩니다.

**Windows(오류 95107)의**경우 로컬 계정 또는 도메인 계정으로 사용자 계정이 원본 컴퓨터에서 관리 액세스 권한이 있는지 확인합니다. 도메인 계정을 사용하지 않는 경우 로컬 컴퓨터에서 원격 사용자 액세스 제어를 사용하지 않도록 설정해야 합니다.

* 원격 사용자 액세스 제어를 사용하지 않도록 설정하는 레지스트리 키를 수동으로 추가하려면 다음을 수행하십시오.

  * `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System`
  * 새 `DWORD`추가 :`LocalAccountTokenFilterPolicy`
  * 값을 다음으로 설정합니다.`1`

* 명령 프롬프트에서 레지스트리 키를 추가하려면 다음 명령을 실행합니다.

  `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`

리눅스 **(오류 95108)의**경우, 당신은 이동성 서비스 에이전트의 성공적인 설치를위한 **루트** 계정을 선택해야합니다. 또한 SSH 파일 전송 프로토콜(SFTP) 서비스가 실행되어야 합니다. _sshd_config_ 파일에서 SFTP 하위 시스템 및 암호 인증을 사용하려면 다음을 수행하십시오.

1. **루트로**로그인합니다.
1. _/etc/ssh/sshd_config 파일로_이동하여 로 시작하는 `PasswordAuthentication`줄을 찾으십시오.
1. 줄의 주석을 주석 을 `yes`해제하고 값을 로 변경합니다.
1. 로 시작하는 `Subsystem`줄을 찾아 줄의 주석을 주석을 해제합니다.
1. `sshd` 서비스를 다시 시작합니다.

선택한 사용자 계정의 자격 증명을 수정하려면 [다음 지침을](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)따르십시오.

## <a name="insufficient-privileges-failure-errorid-95517"></a>불충분한 권한 오류(ErrorID: 95517)

Mobility 에이전트를 설치하도록 선택한 사용자에게 관리자 권한이 없는 경우 구성 서버/확장 프로세스 서버는 Mobility 에이전트 소프트웨어를 소스 컴퓨터에 복사할 수 없습니다. 이 오류는 액세스 거부 오류의 결과입니다. 사용자 계정에 관리자 권한이 있는지 확인합니다.

선택한 사용자 계정의 자격 증명을 수정하려면 [다음 지침을](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)따르십시오.

## <a name="insufficient-privileges-failure-errorid-95518"></a>불충분한 권한 오류(ErrorID: 95518)

원본 컴퓨터에 로그인하는 동안 기본 도메인과 워크스테이션 간의 도메인 신뢰 관계 수립이 실패하면 Mobility 에이전트 설치가 오류 ID 95518로 실패합니다. Mobility 에이전트를 설치하는 데 사용된 사용자 계정에 원본 컴퓨터의 기본 도메인을 통해 로그인할 수 있는 관리 권한이 있는지 확인합니다.

선택한 사용자 계정의 자격 증명을 수정하려면 [다음 지침을](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)따르십시오.

## <a name="login-failures-errorid-95519-95520-95521-95522"></a>로그인 오류(ErrorID: 95519, 95520, 95521, 95522)

이 섹션에서는 자격 증명 및 로그인 오류 메시지에 대해 설명합니다.

### <a name="credentials-of-the-user-account-have-been-disabled-errorid-95519"></a>사용자 계정의 자격 증명이 비활성화되었습니다(ErrorID: 95519).

사용 중에 선택한 사용자 계정이 사용 되지 않도록 설정 되었습니다. 사용자 계정을 사용하려면 [이 문서를](/powershell/module/Microsoft.PowerShell.LocalAccounts/Enable-LocalUser) 참조하거나 텍스트 _사용자 이름을_ 실제 사용자 이름으로 대체하여 다음 명령을 실행합니다.

`net user 'username' /active:yes`

### <a name="credentials-locked-out-because-of-multiple-failed-login-attempts-errorid-95520"></a>여러 번 실패한 로그인 시도로 인해 자격 증명이 잠겼습니다(ErrorID: 95520).

머신에 대한 액세스 시도가 여러 번 실패하면 사용자 계정이 잠깁니다. 실패 원인은 다음과 같을 수 있습니다.

* 구성 설정 중에 제공된 자격 증명이 올바르지 않습니다.
* 복제를 활성화하는 동안 선택한 사용자 계정이 잘못되었습니다.

[다음 지침에](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation) 따라 선택한 자격 증명을 수정하고 작업을 다시 시도합니다.

### <a name="logon-servers-arent-available-on-the-source-machine-errorid-95521"></a>원본 컴퓨터에서 로그온 서버를 사용할 수 없습니다(ErrorID: 95521).

이 오류는 원본 컴퓨터에서 로그온 서버를 사용할 수 없는 경우에 발생합니다. 로그온 서버를 사용할 수 없는 경우 로그인 요청이 실패하고 Mobility 에이전트를 설치할 수 없습니다. 성공적인 로그인을 위해 원본 컴퓨터에서 로그온 서버를 사용할 수 있는지 확인하고 Netlogon 서비스를 시작합니다. 자세한 내용은 [Windows 로그온 시나리오를](/windows-server/security/windows-authentication/windows-logon-scenarios)참조하십시오.

### <a name="logon-service-isnt-running-on-the-source-machine-errorid-95522"></a>원본 컴퓨터에서 로그온 서비스가 실행되고 있지 않습니다(ErrorID: 95522).

원본 컴퓨터에서 로그인 서비스가 실행되고 있지 않아 로그인 요청이 실패했습니다. 이동 에이전트를 설치할 수 없습니다. 오류를 해결하려면 다음 방법 중 하나를 사용하여 `Netlogon` 원본 컴퓨터에서 서비스를 시작합니다.

* 명령 프롬프트에서 `Netlogon` 서비스를 시작하려면 `net start Netlogon`명령을 실행합니다.
* 작업 관리자에서 `Netlogon` 서비스를 시작합니다.

## <a name="connectivity-failure-errorid-95117--97118"></a>연결 오류(오류 ID: 95117 & 97118)

구성 서버/확장 프로세스 서버는 소스 VM에 연결하여 이동성 에이전트를 설치하려고 합니다. 이 오류는 네트워크 연결 문제가 있기 때문에 원본 컴퓨터에 연결할 수 없는 경우에 발생합니다.

오류를 해결하려면:

* 구성 서버에서 원본 컴퓨터를 ping할 수 있는지 확인합니다. 사용 중 확장 프로세스 서버를 선택한 경우 프로세스 서버에서 원본 컴퓨터를 ping할 수 있는지 확인합니다.

* 소스 서버 시스템 명령줄에서 `Telnet` 다음 명령과 같이 HTTPS 포트 135에서 구성 서버 또는 스케일 아웃 프로세스 서버를 ping하는 데 사용합니다. 이 명령은 네트워크 연결 문제 또는 방화벽 포트 차단 문제가 있는지 확인합니다.

  `telnet <CS/ scale-out PS IP address> <135>`

* 또한 Linux VM의 경우:
  * 최신 OpenSSH, OpenSSH 서버 및 OpenSSL 패키지가 설치되어 있는지 확인합니다.
  * SSH(Secure Shell)를 사용할 수 있고 22 포트에서 실행 중인지 확인합니다.
  * SFTP 서비스가 실행되어야 합니다. _sshd_config_ 파일에서 SFTP 하위 시스템 및 암호 인증을 사용하려면 다음을 수행하십시오.

    1. **루트로**로그인합니다.
    1. _/etc/ssh/sshd_config_ 파일로 이동하여 로 시작하는 `PasswordAuthentication`줄을 찾으십시오.
    1. 줄의 주석을 주석 을 `yes`해제하고 값을 로 변경합니다.
    1. 로 시작하는 `Subsystem`줄을 찾고 줄의 주석 처리 취소
    1. `sshd` 서비스를 다시 시작합니다.

* 기간 이후에 적절한 응답이 없거나 연결된 호스트가 응답하지 않아 설정된 연결이 실패한 경우 연결 시도가 실패할 수 있습니다.
* 연결/네트워크/도메인 관련 문제일 수 있습니다. DNS 이름 해결 문제 또는 TCP 포트 소모 문제 때문일 수도 있습니다. 도메인에 이러한 알려진 문제가 있는지 확인합니다.

## <a name="connectivity-failure-errorid-95523"></a>연결 오류(오류 ID: 95523)

이 오류는 원본 컴퓨터가 있는 네트워크를 찾을 수 없거나 삭제되었거나 더 이상 사용할 수 없는 경우에 발생합니다. 오류를 해결하는 유일한 방법은 네트워크가 있는지 확인하는 것입니다.

## <a name="file-and-printer-sharing-services-check-errorid-95105--95106"></a>파일 및 프린터 공유 서비스 확인(ErrorID: 95105 및 95106)

연결 확인 후 가상 컴퓨터에서 파일 및 프린터 공유 서비스가 활성화되어 있는지 확인합니다. 이러한 설정은 모빌리티 에이전트를 소스 컴퓨터에 복사하는 데 필요합니다.

**윈도우 2008 R2 및 이전 버전의**경우 :

* Windows 방화벽을 통해 파일 및 인쇄 공유가 가능하도록 설정하려면,
  1. 오픈 **제어판** > **시스템 및 보안** > **윈도우 방화벽**. 왼쪽 창에서 콘솔 트리에서 **고급 설정** > **인바운드 규칙을** 선택합니다.
  1. 파일 및 프린터 공유(NB-Session-In) 및 파일 및 프린터 공유(SMB-In) 규칙을 찾습니다.
  1. 각 규칙을 마우스 오른쪽 단추로 클릭한 다음, **규칙 사용**을 클릭합니다.

* 그룹 정책으로 파일 공유를 사용하려면 다음을 수행합니다.
  1. **시작,** 입력 `gpmc.msc` 및 검색으로 이동합니다.
  1. 탐색 창에서 다음 폴더를 엽니다: **로컬 컴퓨터 정책** > **사용자 구성** > **관리 템플릿** > **Windows 구성 요소** > **네트워크 공유**.
  1. 세부 정보 창에서 **사용자 프로필 내의 파일 공유 안 함**을 두 번 클릭합니다.

     그룹 정책 설정을 사용하지 않도록 설정하고 사용자가 파일을 공유할 수 있도록 하려면 **사용 안 함**을 선택합니다.

  1. **확인** 을 선택하여 변경 내용을 저장합니다.

  자세한 내용은 [그룹 정책으로 파일 공유 사용 또는 비활성화를](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754359(v=ws.10))참조하십시오.

이후 버전의 Windows 또는 Linux에 대한 파일 및 프린터 공유를 사용하려면 [VMware VM 및 물리적 서버의 재해 복구를 위한 이동성 서비스 설치의 지침을 따릅니다.](vmware-azure-install-mobility-service.md)

## <a name="windows-management-instrumentation-wmi-configuration-check-error-code-95103"></a>WMI(Windows 관리 계측) 구성 검사(오류 코드: 95103)

파일 및 프린터 서비스를 확인한 후 방화벽을 통해 개인, 공용 및 도메인 프로필에 대해 WMI 서비스를 사용하도록 설정합니다. 이 설정은 원본 머신에서 원격 실행을 완료하는 데 필요합니다.

WMI를 사용하려면 다음을 수행하십시오.

1. **제어판** > **보안으로** 이동하여 **Windows 방화벽을**선택합니다.
1. **설정 변경을** 선택한 다음 **예외 탭을 선택합니다.**
1. **예외** 창에서 WMI(Windows 관리 계측)의 확인란을 선택하여 방화벽을 통해 WMI 트래픽을 사용하도록 설정합니다.

다음 명령을 사용하여 명령 프롬프트에서 방화벽을 통해 WMI 트래픽을 활성화할 수도 있습니다.

`netsh advfirewall firewall set rule group="windows management instrumentation (wmi)" new enable=yes`

기타 WMI 문제 해결 문서는 다음 문서에서 찾을 수 있습니다.

* [기본 WMI 테스트](https://blogs.technet.microsoft.com/askperf/2007/06/22/basic-wmi-testing/)
* [WMI 문제 해결](/windows/win32/wmisdk/wmi-troubleshooting)
* [WMI 스크립트 및 WMI 서비스 관련 문제 해결](/previous-versions/tn-archive/ff406382(v=msdn.10))

## <a name="unsupported-operating-systems"></a>지원되지 않는 운영 체제

실패의 또 다른 일반적인 이유는 지원되지 않는 운영 체제 때문일 수 있습니다. Mobility 서비스를 성공적으로 설치할 때 지원되는 운영 체제 및 커널 버전을 사용합니다. 개인 패치를 사용하지 마십시오.

Azure 사이트 복구에서 지원하는 운영 체제 및 커널 버전 목록을 보려면 [지원 매트릭스 문서를](vmware-physical-azure-support-matrix.md#replicated-machines)참조하십시오.

## <a name="unsupported-boot-disk-configurations-errorid-95309-95310-95311"></a>지원되지 않는 부팅 디스크 구성(ErrorID: 95309, 95310, 95311)

### <a name="boot-and-system-partitions--volumes-arent-the-same-disk-errorid-95309"></a>부팅 및 시스템 파티션 / 볼륨이 동일한 디스크가 아닙니다(ErrorID: 95309)

9.20 버전 이전에는 다른 디스크의 부팅 및 시스템 파티션/볼륨이 지원되지 않는 구성이었습니다. [9.20 버전부터](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery)이 구성이 지원됩니다.

### <a name="the-boot-disk-isnt-available-errorid-95310"></a>부팅 디스크를 사용할 수 없습니다(ErrorID: 95310)

부팅 디스크가 없는 가상 시스템은 보호할 수 없습니다. 부팅 디스크는 장애 조치 작업 중에 가상 시스템을 원활하게 복구할 수 있도록 합니다. 부팅 디스크가 없으면 장애 조치 후 컴퓨터를 부팅하지 못합니다. 가상 시스템에 부팅 디스크가 포함되어 있는지 확인하고 작업을 다시 시도합니다. 또한 동일한 컴퓨터의 여러 부팅 디스크는 지원되지 않습니다.

### <a name="multiple-boot-disks-present-on-the-source-machine-errorid-95311"></a>원본 컴퓨터에 여러 부팅 디스크가 있음(ErrorID: 95311)

부팅 디스크가 여러 개인 가상 시스템은 [지원되는 구성이](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage)아닙니다.

## <a name="system-partition-on-multiple-disks-errorid-95313"></a>여러 디스크의 시스템 파티션(ErrorID: 95313)

9.20 버전 이전에는 여러 디스크의 루트 파티션 또는 볼륨 설정이 지원되지 않는 구성이었습니다. [9.20 버전부터](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery)이 구성이 지원됩니다.

## <a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320"></a>UUID 대신 GRUB 구성에 언급된 장치 이름으로 보호 가 실패했습니다(ErrorID: 95320).

### <a name="possible-cause"></a>가능한 원인

그랜드 통합 부트 로더 (GRUB) 구성 파일 _(/boot/grub/menu.lst,_ _/boot/grub/grub.cfg,_ _/boot/grub2/grub.cfg,_ 또는 _/etc/default/grub)는_매개 변수 **루트에** 대한 값을 포함하고 보편적으로 고유한 식별자(UUID) 대신 실제 장치 이름으로 **다시 시작할** 수 있습니다. VM의 재부팅을 통해 장치 이름이 변경될 수 있기 때문에 사이트 복구는 UUID 접근 방식을 요구합니다. 예를 들어 VM은 장애 조치(failover)에서 이름이 같을 수 없으며 이로 인해 문제가 발생할 수 있습니다.

다음은 그 예입니다.

- 다음 줄은 GRUB 파일 _/boot/grub2/grub.cfg에서_가져옵니다.

  `linux /boot/vmlinuz-3.12.49-11-default root=/dev/sda2  ${extra_cmdline} resume=/dev/sda1 splash=silent quiet showopts`

- 다음 줄은 GRUB 파일 _/boot/grub/menu.lst에서 가져옵니다._

  `kernel /boot/vmlinuz-3.0.101-63-default root=/dev/sda2 resume=/dev/sda1 splash=silent crashkernel=256M-:128M showopts vga=0x314`

> [!NOTE]
> GRUB 줄에는 UUID가 아닌 매개 변수 **루트** 및 **다시 시작에** 대한 실제 장치 이름이 포함됩니다.

### <a name="how-to-fix"></a>해결 방법

디바이스 이름을 해당 UUID로 바꿔야 합니다.

1. 명령을 `blkid \<device name>`실행하여 장치의 UUID를 찾습니다.

   다음은 그 예입니다.

   ```shell
   blkid /dev/sda1
   /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
   blkid /dev/sda2
   /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3"
   ```

1. 이제 장치 이름을 와 같은 `root=UUID=\<UUID>`형식으로 UUID로 바꿉니다. 예를 들어 장치 이름을 루트 및 재개 매개 변수에 대한 UUID로 바꾸면 파일 _/boot/grub2/grub.cfg,_ _/boot/grub2/grub.cfg,_ 또는 _/etc/default/grub에_ 언급된 다음 파일의 줄은 다음과 같습니다.

   `kernel /boot/vmlinuz-3.0.101-63-default root=UUID=62927e85-f7ba-40bc-9993-cc1feeb191e4 resume=UUID=6f614b44-433b-431b-9ca1-4dd2f6f74f6b splash=silent crashkernel=256M-:128M showopts vga=0x314`

1. 보호를 다시 시작합니다.

## <a name="install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266"></a>재부팅 경고와 함께 완료된 설치 이동성 서비스(ErrorID: 95265 & 95266)

사이트 복구 이동성 서비스에는 필터 드라이버라고 하는 많은 구성 요소가 있습니다. 필터 드라이버는 시스템을 재부팅하는 동안에만 시스템 메모리에 로드됩니다. 필터 드라이버 수정은 시스템을 다시 부팅할 때 새 필터 드라이버가 로드된 경우에만 실현할 수 있습니다.

> [!IMPORTANT]
> 이는 경고이며 새 에이전트 업데이트 후에도 기존 복제가 작동합니다. 새 필터 드라이버의 이점을 얻으려면 언제든지 재부팅하도록 선택할 수 있지만 재부팅하지 않으면 이전 필터 드라이버가 계속 작동합니다. 따라서 필터 드라이버를 제외하고 재부팅하지 않고 업데이트 한 후 **모빌리티 서비스의 다른 향상된 기능 및 수정 사항의 이점이 실현됩니다.** 권장되지만 업그레이드 할 때마다 반드시 다시 부팅하는 것은 아닙니다. 재부팅이 필수인 경우에 대한 자세한 내용은 Azure 사이트 복구의 서비스 업데이트에서 [이동성 서비스 업그레이드 후 재부팅](service-updates-how-to.md#reboot-after-mobility-service-upgrade) 섹션을 설정합니다.

> [!TIP]
>유지 관리 기간 동안 업그레이드 를 예약하는 방법에 대한 모범 사례는 Azure 사이트 복구의 서비스 업데이트에서 [최신 운영 체제/커널에 대한 지원을](service-updates-how-to.md#support-for-latest-operating-systemskernels) 참조하십시오.

## <a name="lvm-support-from-920-version"></a>9.20 버전의 LVM 지원

9.20 버전 이전에는 LVM(논리 볼륨 관리자)이 데이터 디스크에만 지원되었습니다. 파티션은 `/boot` LVM 볼륨이 아닌 디스크 파티션에 있어야 합니다.

[9.20 버전부터](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery) [LVM의 OS 디스크가](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) 지원됩니다.

## <a name="insufficient-space-errorid-95524"></a>공간이 부족함(오류 ID: 95524)

모빌리티 에이전트를 소스 컴퓨터에 복사하면 최소 100MB의 여유 공간이 필요합니다. 원본 컴퓨터에 필요한 여유 공간이 있는지 확인하고 작업을 다시 시도하십시오.

## <a name="vss-installation-failures"></a>VSS 설치 오류

VSS(볼륨 섀도우 복사 서비스) 설치는 모빌리티 에이전트 설치의 일부입니다. 이 서비스는 응용 프로그램 일관된 복구 지점을 생성하는 프로세스에 사용됩니다. VSS 설치 중 오류는 여러 이유로 인해 발생할 수 있습니다. 정확한 오류를 식별하려면 _C:\ProgramData\ASRSetuplogs\ASRUnifiedAgentInstaller.log를 참조하십시오._ 일반적인 오류 및 해결 단계 중 일부는 다음 섹션에서 강조 표시되어 있습니다.

### <a name="vss-error--2147023170-0x800706be---exit-code-511"></a>VSS 오류 -2147023170 [0x800706BE] - 종료 코드 511

이 문제는 바이러스 백신 소프트웨어가 Azure 사이트 복구 서비스의 작업을 차단할 때 가장 자주 발생합니다.

이 문제를 해결하려면:

1. 바이러스 백신 프로그램에서 폴더 제외 목록을 [검토합니다.](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program)
1. 바이러스 백신 공급자가 게시한 지침에 따라 Windows에서 DLL 등록을 차단 해제합니다.

### <a name="vss-error-7-0x7---exit-code-511"></a>VSS 오류 7 [0x7] - 종료 코드 511

이 오류는 VSS를 설치할 메모리가 부족하여 발생하는 런타임 오류입니다. 이 작업을 성공적으로 완료하려면 디스크 공간을 늘립니다.

### <a name="vss-error--2147023824-0x80070430---exit-code-517"></a>VSS 오류 -2147023824 [0x80070430] - 종료 코드 517

이 오류는 Azure Site Recovery VSS 공급자 서비스가 [삭제를 위해 표시된](/previous-versions/ms838153(v=msdn.10)) 경우에 발생합니다. 다음 명령을 실행하여 소스 컴퓨터에 VSS를 수동으로 설치해 보십시오.

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

### <a name="vss-error--2147023841-0x8007041f---exit-code-512"></a>VSS 오류 -2147023841 [0x8007041F] - 종료 코드 512

이 오류는 Azure 사이트 복구 VSS 공급자 서비스 데이터베이스가 [잠겨](/previous-versions/ms833798(v=msdn.10))있을 때 발생합니다. 명령 프롬프트에서 다음 명령을 실행하여 소스 컴퓨터에 VSS를 수동으로 설치해 보십시오.

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

오류가 발생하면 바이러스 백신 프로그램이나 기타 서비스가 **시작** 상태에 있는지 확인합니다. **시작** 상태의 프로세스는 데이터베이스 서비스에 대한 잠금을 유지할 수 있습니다. VSS 공급자를 설치하는 데 실패하게 됩니다. **서비스가 시작** 상태에 있지 않은지 확인하고 위의 작업을 다시 시도합니다.

### <a name="vss-exit-code-806"></a>VSS 종료 코드 806

이 오류는 설치에 사용된 사용자 계정에 `CSScript` 명령을 실행할 수 있는 권한이 없는 경우에 발생합니다. 사용자 계정에 스크립트를 실행하기 위해 필요한 권한을 제공하고 작업을 다시 시도합니다.

### <a name="other-vss-errors"></a>기타 VSS 오류

명령 프롬프트에서 다음 명령을 실행하여 소스 컴퓨터에 VSS 공급자 서비스를 수동으로 설치해 보십시오.

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

## <a name="vss-error---0x8004e00f"></a>VSS 오류 - 0x8004E00F

이 오류는 일반적으로 문제 로 인해 Mobility `DCOM` 에이전트를 설치 하는 동안 발생 하 고 `DCOM` 중요 한 상태에.

다음 절차를 사용하여 오류의 원인을 확인합니다.

### <a name="examine-the-installation-logs"></a>설치 로그 검사

1. _C:\ProgramData\ASRSetuplogs\ASRUnifiedAgentInstaller.log에_있는 설치 로그를 엽니다.
2. 다음 오류가 있으면 이 문제가 있음을 나타냅니다.

    ```Output
    Unregistering the existing application...
    Create the catalogue object
    Get the collection of Applications

    ERROR:

    - Error code: -2147164145 [0x8004E00F]
    - Exit code: 802
    ```

이 문제를 해결하려면

DCOM 문제 해결에 대한 도움을 받으려면 [Microsoft Windows 플랫폼 팀에](https://aka.ms/Windows_Support) 문의하십시오.

DCOM 문제가 해결되면 명령 프롬프트에서 다음 명령을 사용하여 Azure 사이트 복구 VSS 공급자를 수동으로 다시 설치합니다.

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

응용 프로그램 일관성이 재해 복구 요구 사항에 중요하지 않은 경우 VSS 공급자 설치를 우회할 수 있습니다.

Azure 사이트 복구 VSS 공급자 설치를 우회하고 Azure 사이트 복구 VSS 공급자 사후 설치를 수동으로 설치하려면 다음을 수행하십시오.

1. 모바일 서비스를 설치합니다. 설치 단계: **설치 후 구성에서**설치가 실패합니다.
1. VSS 설치를 우회하려면 다음을 수행하십시오.
   1. 다음 에 있는 Azure 사이트 복구 모빌리티 서비스 설치 디렉터리 열기

      _C:\프로그램 파일(x86)\마이크로소프트 Azure 사이트 복구\에이전트_

   1. Azure 사이트 복구 VSS 공급자 설치 _스크립트InMageVSSProvider_Install_ 수정 하고 _InMageVSSProvider_Uninstall.cmd_ 다음 줄을 추가 하 여 항상 성공 하려면:

      ```plaintext
      rem @echo off
      setlocal
      exit /B 0
      ```

1. 이동 에이전트를 수동으로 설치합니다.
1. 설치가 성공하고 다음 단계로 이동하면 **구성**, 추가한 줄을 제거합니다.
1. VSS 공급자를 설치하려면 명령 프롬프트를 관리자로 열고 다음 명령을 실행합니다.

   `"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

1. Azure 사이트 복구 VSS 공급자가 Windows 서비스에서 서비스로 설치되어 있는지 확인합니다. 구성 요소 서비스 MMC를 열어 VSS 공급자가 나열되어 있는지 확인합니다.
1. VSS 공급자 설치가 계속 실패하면 기술 지원팀에서 작업하여 암호화 응용 프로그램 프로그래밍 인터페이스(CAPI2)의 사용 권한 오류를 해결합니다.

## <a name="vss-provider-installation-fails-because-the-cluster-service-being-enabled-on-non-cluster-machine"></a>VSS 공급자 설치가 클러스터가 아닌 컴퓨터에서 사용하도록 설정되어 있기 때문에 실패합니다.

이 문제로 인해 Azure 사이트 복구 이동성 에이전트 설치가 Azure 사이트 복구 VSS 공급자 설치 중에 실패합니다. VSS 공급자 설치를 방해하는 `COM+` 문제가 있기 때문입니다.

### <a name="to-identify-the-issue"></a>문제를 식별하려면

_C:\ProgramData\ASRSetupLogs\UploadededLogs\<날짜 시간>UA_InstallLogFile.log의_ 구성 서버에 있는 로그에서 다음과 같은 예외를 찾을 수 있습니다.

```plaintext
COM+ was unable to talk to the Microsoft Distributed Transaction Coordinator (Exception from HRESULT: 0x8004E00F)
```

이 문제를 해결하려면

1. 이 컴퓨터가 클러스터가 아닌 컴퓨터이고 클러스터 구성 요소가 사용되지 않는지 확인합니다.
1. 구성 요소를 사용하지 않는 경우 컴퓨터에서 클러스터 구성 요소를 제거합니다.

## <a name="drivers-are-missing-on-the-source-server"></a>원본 서버에서 드라이버가 누락되었습니다.

모빌리티 에이전트 설치가 실패하면 _C:\ProgramData\ASRSetuplogs_ 아래의 로그를 검사하여 일부 제어 세트에서 필요한 드라이버 중 일부가 누락되었는지 확인합니다.

이 문제를 해결하려면

1. 와 같은 `regedit.msc`레지스트리 편집기에서 레지스트리를 엽니다.
1. 노드를 `HKEY_LOCAL_MACHINE\SYSTEM` 엽니다.
1. `SYSTEM` 노드에서 컨트롤 세트를 찾습니다.
1. 각 컨트롤 집합을 열고 다음 Windows 드라이버가 있는지 확인합니다.

   * Atapi
   * Vmbus
   * 스토르트 (동음이의)
   * 스토브스크
   * 인텔리에이드 (정보)

1. 누락된 드라이버를 다시 설치합니다.

## <a name="next-steps"></a>다음 단계

VMware VM에 대한 재해 복구를 설정하는 방법에 대해 [자세히 알아봅니다.](vmware-azure-tutorial.md)
