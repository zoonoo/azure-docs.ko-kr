---
title: 재해 복구에 대한 복제를 사용하도록 설정할 때 Mobility Service 푸시 설치 오류 문제 해결 | Microsoft Docs
description: 재해 복구에 대한 복제를 사용하도록 설정할 때 Mobility Service 설치 오류 문제 해결
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.author: ramamill
ms.date: 10/29/2018
ms.openlocfilehash: c12683fee7022b84e097c2b71628776271611429
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/29/2018
ms.locfileid: "50210995"
---
# <a name="troubleshoot-mobility-service-push-installation-issues"></a>Mobility Service 푸시 설치 문제 해결

모바일 서비스 설치는 복제를 사용하도록 설정하는 동안 주요한 단계입니다. 이 단계의 성공은 필수 구성 요소 충족 및 지원되는 구성 작업에 달려 있습니다. 모바일 서비스를 설치하는 동안 접하는 가장 일반적인 문제의 원인은 다음과 같습니다.

* 자격 증명/권한 오류
* 연결 오류
* 지원되지 않는 운영 체제

복제를 활성화하면 Azure Site Recovery가 가상 머신에 모바일 서비스 에이전트 설치를 푸시하려고 합니다. 이러한 시도의 일환으로 구성 서버가 가상 머신에 연결하여 에이전트를 복사하려고 합니다. 설치에 성공하려면 아래에 있는 단계별 문제 해결 지침을 따르십시오.

## <a name="credentials-check-errorid-95107--95108"></a>자격 증명 확인(ErrorID: 95107 및 95108)

* 복제 활성화 중에 선택한 사용자 계정이 **유효하고 정확한지** 확인합니다.
* 푸시 설치를 수행하려면 Azure Site Recovery에 **관리자 권한**이 필요합니다.
  * Windows의 경우 사용자 계정에 원본 머신에서 로컬 도메인에 대한 관리 액세스 권한이 있는지 확인합니다.
  * 도메인 계정을 사용하지 않는 경우 로컬 컴퓨터에서 원격 사용자 액세스 제어를 사용하지 않도록 설정해야 합니다.
    * 원격 사용자 액세스 제어를 사용하지 않도록 설정하려면 HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System 레지스트리 키 아래에서 새로운 DWORD: LocalAccountTokenFilterPolicy를 추가합니다. 이 값을 1로 설정합니다. 이 단계를 실행하려면 명령 프롬프트에서 다음 명령을 실행합니다.

         `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`
  * Linux의 경우 모바일 에이전트를 성공적으로 설치하려면 루트 계정을 선택해야 합니다.

선택한 사용자 계정의 자격 증명을 수정하려면 [여기](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)에 제공된 지침을 따릅니다.

## <a name="connectivity-check-errorid-95117--97118"></a>**연결 모니터링(ErrorID: 95117 및 97118)**

* 구성 서버에서 원본 머신에 ping을 수행할 수 있는지 확인합니다. 복제를 활성화하는 동안 확장 프로세스 서버를 선택한 경우 프로세스 서버에서 원본 머신에 ping을 수행할 수 있어야 합니다.
  * 원본 서버 머신 명령줄에서 텔넷을 사용하여 아래와 같이 https 포트(기본값 9443)로 구성 서버/확장 프로세스 서버에 ping을 수행하여 네트워크 연결 문제나 방화벽 포트 차단 문제가 있는지 확인합니다.

     `telnet <CS/ scale-out PS IP address> <port>`

  * 연결할 수 없는 경우에는 구성 서버/확장 프로세스 서버에서 인바운드 포트 9443을 허용합니다.
  * **InMage Scout VX Agent – Sentinel/Outpost** 서비스의 상태를 확인합니다. 서비스가 실행 되고 있지 않으면 시작합니다.

* 추가로 **Linux VM**에서도 시작합니다.
  * 최신 openssh, openssh-server 및 openssl 패키지가 설치되어 있는지 확인합니다.
  * SSH(Secure Shell)를 사용할 수 있고 22 포트에서 실행 중인지 확인합니다.
  * SFTP 서비스가 실행되어야 합니다. sshd_config 파일에서 SFTP 하위 시스템 및 암호 인증을 사용하도록 설정하려면,
    * 루트로 로그인합니다.
    * /etc/ssh/sshd_config 파일로 이동하여 PasswordAuthentication으로 시작하는 줄을 찾습니다.
    * 줄의 주석 처리를 제거하고 값을 yes로 변경합니다.
    * Subsystem으로 시작하는 줄을 찾아서 주석 처리를 제거합니다.
    * sshd 서비스를 다시 시작합니다.
* 일정 기간 후에 적절한 응답이 없거나 연결된 호스트가 응답하지 못하여 기존 연결에 오류가 발생한 경우 연결 시도가 실패할 수 있습니다.
* 연결/네트워크/도메인 관련 문제일 수 있습니다. DNS 이름 확인 문제 또는 TCP 포트 고갈 문제가 원인일 수 있습니다. 도메인에 이러한 문제가 있는지 확인하십시오.

## <a name="file-and-printer-sharing-services-check-errorid-95105--95106"></a>파일 및 프린터 공유 서비스 확인(ErrorID: 95105 및 95106)

연결을 모니터링한 후에는 가상 머신에 파일 및 프린터 공유 서비스가 활성화되어 있는지 확인합니다.

**Windows 2008 R2 이전 버전**의 경우,

* Windows 방화벽을 통해 파일 및 인쇄 공유가 가능하도록 설정하려면,
  * 제어판 -> 시스템 및 보안 -> Windows 방화벽으로 이동하여 왼쪽 창에서 고급 설정을 클릭하고 콘솔 트리에서 인바운드 규칙을 클릭합니다.
  * 파일 및 프린터 공유(NB-Session-In) 및 파일 및 프린터 공유(SMB-In) 규칙을 찾습니다. 각 규칙을 마우스 오른쪽 단추로 클릭한 다음, **규칙 사용**을 클릭합니다.
* 그룹 정책을 사용하여 파일 공유가 가능하도록 하려면,
  * 시작으로 이동하여 gpmc.msc를 입력하고 검색합니다.
  * 탐색 창에서 로컬 컴퓨터 정책, 사용자 구성, 관리 템플릿, Windows 구성 요소 및 네트워크 공유 폴더를 엽니다.
  * 세부 정보 창에서 **사용자 프로필 내의 파일 공유 안 함**을 두 번 클릭합니다. 그룹 정책 설정을 사용하지 않도록 설정하고 사용자가 파일을 공유할 수 있도록 하려면 사용 안 함을 클릭합니다. 확인을 클릭하여 변경 내용을 저장합니다. 자세히 알아보려면 [여기](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754359(v=ws.10))를 클릭하세요.

**이후 버전**의 경우, [여기](vmware-azure-install-mobility-service.md#install-mobility-service-by-push-installation-from-azure-site-recovery)에 제공된 지침에 따라 파일 및 프린터 공유가 가능하도록 설정합니다.

## <a name="windows-management-instrumentation-wmi-configuration-check"></a>WMI(Windows Management Instrumentation) 구성 확인

파일 및 프린터 서비스를 확인한 후에는 방화벽을 통해 WMI 서비스를 사용하도록 설정합니다.

* 제어판에서 보안을 클릭한 다음, Windows 방화벽을 클릭합니다.
* 설정 변경을 클릭한 다음, 예외 탭을 클릭합니다.
* 예외 창에서 WMI(Windows Management Instrumentation)에 대한 확인란을 선택하여 방화벽을 통해 WMI 트래픽을 사용하도록 설정합니다. 

명령 프롬프트에서 방화벽을 통해 WMI 트래픽을 사용하도록 설정할 수도 있습니다. `netsh advfirewall firewall set rule group="windows management instrumentation (wmi)" new enable=yes` 명령을 사용합니다.
기타 WMI 문제 해결 문서는 다음 문서에서 찾을 수 있습니다.

* [기본 WMI 테스트](https://blogs.technet.microsoft.com/askperf/2007/06/22/basic-wmi-testing/)
* [WMI 문제 해결](https://msdn.microsoft.com/library/aa394603(v=vs.85).aspx)
* [WMI 스크립트 및 WMI 서비스 관련 문제 해결](https://technet.microsoft.com/library/ff406382.aspx#H22)

## <a name="unsupported-operating-systems"></a>지원되지 않는 운영 체제

실패에 대한 또 다른 가장 일반적인 이유는 지원되지 않는 운영 체제일 수 있습니다. 모바일 서비스를 성공적으로 설치하려면 지원되는 운영 체제/커널 버전에 설치해야 합니다.

Azure Site Recovery에서 어떤 운영 체제가 지원되는지 알아보려면 [지원 매트릭스 문서](vmware-physical-azure-support-matrix.md#replicated-machines)를 참조하세요.

## <a name="next-steps"></a>다음 단계

VMware VM에 대한 재해 복구를 설정하는 [방법을 알아봅니다](vmware-azure-tutorial.md).