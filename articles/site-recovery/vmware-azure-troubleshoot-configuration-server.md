---
title: Azure Site Recovery를 사용한 VMware VM 및 물리적 서버와 Azure 간 재해 복구 중 구성 서버의 문제 해결 | Microsoft Docs
description: 이 문서에서는 Azure Site Recovery를 사용한 VMware VM 및 물리적 서버와 Azure 간 재해 복구를 위해 구성 서버를 배포하는 문제를 해결하는 방법을 제공합니다.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 12/17/2018
ms.author: ramamill
ms.openlocfilehash: f5c8241907459a06f0a6206ae6865cdf3fe9ab89
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/03/2019
ms.locfileid: "53998968"
---
# <a name="troubleshoot-configuration-server-issues"></a>구성 서버 문제 해결

이 문서는 [Azure Site Recovery](site-recovery-overview.md) 구성 서버를 배포 및 관리할 때 문제를 해결하는 데 도움이 됩니다. 구성 서버는 관리 서버의 역할을 담당하며, Site Recovery를 사용하여 Azure에 대한 온-프레미스 VMware VM 및 물리적 서버의 재해 복구를 설정하기 위해 사용됩니다. 다음 섹션에서는 새 구성 서버를 추가하고 구성 서버를 관리하는 동안 표시되는 가장 일반적인 오류를 설명합니다.

## <a name="registration-failures"></a>등록 오류

모바일 에이전트를 설치하는 동안 원본 머신은 구성 서버에 등록됩니다. 아래 제공된 지침에 따라 이 단계에서 발생한 모든 오류를 디버그할 수 있습니다.

1. C:\ProgramData\ASR\home\svsystems\var\configurator_register_host_static_info.log file로 이동하세요. ProgramData는 숨겨진 폴더일 수 있습니다. 찾을 수 없다면 폴더 숨기기를 취소해보세요. 오류는 여러 문제로 인해 발생할 수 있습니다.
2. "유효한 IP 주소를 찾을 수 없음" 문자열을 검색합니다. 문자열을 찾았다면
    - 요청된 호스트 ID가 원본 머신과 동일한지를 확인합니다.
    - 원본 머신이 성공하려면 CS에서 에이전트를 등록하기 위해 실제 NIC에 할당된 하나 이상의 IP 주소가 있어야 합니다.
    - 원본 머신 `> ipconfig /all`(예: Windows OS) 및 `# ifconfig -a`(Linux OS)에서 명령을 실행하여 원본 머신의 모든 IP 주소를 가져옵니다.
    - 에이전트를 등록하려면 실제 NIC에 할당된 유효한 IP v4 주소가 필요합니다.
3. 위의 문자열을 찾을 수 없으면 문자열 "Reason"=>"NULL"을 검색하세요. 찾으면
    - 원본 머신이 구성 서버에 등록하기 위해 빈 호스트를 사용하면 이 오류가 발생합니다.
    - 문제를 해결하면 [여기](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server)에서 제공된 다음 지침에 따라 등록을 수동으로 다시 시도하세요.
4. 위의 문자열을 찾을 수 없으면 원본 머신으로 이동하고 C:\ProgramData\ASRSetupLogs\UploadedLogs\* ASRUnifiedAgentInstaller.log 로그를 검사하세요. ProgramData는 숨겨진 폴더일 수 있습니다. 찾을 수 없다면 폴더 숨기기를 취소해보세요. 오류는 여러 문제로 인해 발생할 수 있습니다. 문자열 “post request: (7) - 서버에 연결할 수 없음”을 검색하세요. 찾으면
    - 원본 머신 및 구성 서버 간의 네트워크 문제를 해결합니다. ping, traceroute, 웹 브라우저 등과 같은 네트워크 도구를 사용하여 원본 머신에서 구성 서버에 연결할 수 있는지를 확인합니다. 원본 머신이 포트 443을 통해 구성 서버에 연결할 수 있어야 합니다.
    - 원본 머신의 방화벽 규칙이 원본 머신과 구성 서버 간의 연결을 차단하고 있는지 검사합니다. 연결 문제의 차단을 해제하려면 네트워크 관리자 권한으로 작업합니다.
    - [여기](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program)에서 언급한 폴더가 바이러스 백신 소프트웨어에서 제외되었는지 확인합니다.
    - 네트워크 문제를 해결하면 [여기](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server)에서 제공된 다음 지침에 따라 등록을 다시 시도하세요.
5. 찾을 수 없는 경우 동일한 로그에서 문자열 "request: (60) - 피어 인증서는 지정된 CA 인증서를 사용하여 인증할 수 없습니다."를 찾아보세요. 찾으면 
    - 이 오류는 구성 서버 인증서가 만료되거나, 원본 머신이 TLS 1.0 및 위의 SSL 프로토콜을 지원하지 않거나, 원본 머신과 구성 서버 간의 SSL 통신을 차단하는 방화벽이 있기 때문에 발생할 수 있습니다.
    - 이 오류를 해결하기 위해 https://<CSIPADDRESS>:443/이라는 URI에서 원본 머신의 웹 브라우저를 사용하여 구성 서버 IP 주소에 연결합니다. 원본 머신이 포트 443을 통해 구성 서버에 연결할 수 있는지 확인합니다.
    - 원본 머신의 방화벽 규칙이 원본 머신과 구성 서버 간의 연결을 차단하고 있는지 검사합니다. 연결 문제의 차단을 해제하려면 네트워크 관리자 권한으로 작업합니다.
    - [여기](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program)에서 언급한 폴더가 바이러스 백신 소프트웨어에서 제외되었는지 확인합니다.  
    - 문제를 해결하면 [여기](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server)에서 제공된 다음 지침에 따라 등록을 다시 시도하세요.
6. Linux에서 <INSTALLATION_DIR>/etc/drscout.conf에서 플랫폼 값이 손상된 경우 등록에 실패합니다. 이를 식별하려면 /var/log/ua_install.log 로그로 이동하세요. 문자열 "VM_PLATFORM 값이 null이거나 VmWare/Azure이 아니므로 구성 중단"을 찾을 수 있습니다. 플랫폼은 "VmWare" 또는 "Azure"로 설정되어야 합니다. drscout.conf 파일이 손상되면 모바일 에이전트를 [제거](vmware-physical-mobility-service-overview.md#uninstall-the-mobility-service)하고 다시 설치하는 것이 좋습니다. 제거에 실패하는 경우 다음 단계에 따르세요.
    - Installation_Directory/uninstall.sh 파일을 열고 *StopServices* 함수에 대한 호출을 주석으로 처리
    - Installation_Directory/Vx/bin/uninstall 파일을 열고 `stop_services` 함수에 대한 호출을 주석으로 처리
    - Installation_Directory/Fx/uninstall 파일을 열고 Fx 서비스를 중지하는 전체 섹션을 주석으로 처리
    - 이제 모바일 에이전트를 [제거](vmware-physical-mobility-service-overview.md#uninstall-the-mobility-service)해보세요. 성공적으로 제거하면 시스템을 다시 부팅하고 에이전트를 다시 설치해보세요.

## <a name="installation-failure---failed-to-load-accounts"></a>설치 오류 - 계정을 로드하지 못했습니다.

이 오류는 모바일 에이전트를 설치하고 구성 서버에 등록하는 동안 서비스가 전송 연결의 데이터를 읽을 수 없을 때 발생합니다. 이 오류를 해결하려면 TLS 1.0을 원본 머신에서 사용하도록 설정했는지 확인합니다.

## <a name="change-ip-address-of-configuration-server"></a>구성 서버의 IP 주소 변경

구성 서버의 IP 주소는 변경하지 않는 것이 좋습니다. 구성 서버에 할당된 모든 IP가 DHCP IP가 아닌 정적 IP인지 확인합니다.

## <a name="acs50008-saml-token-is-invalid"></a>ACS50008: SAML 토큰이 잘못되었습니다.

이 오류를 방지하려면 시스템 시계 시간이 현지 시간과 15분 넘게 차이나지 않도록 합니다. 설치 관리자를 다시 실행하여 등록을 완료합니다.

## <a name="failed-to-create-certificate"></a>인증서를 만들지 못했습니다.

Site Recovery를 인증하는 데 필요한 인증서를 만들 수 없습니다. 로컬 관리자로 설치 프로그램을 실행하고 있는지 확인한 후에 설치 프로그램을 다시 실행합니다.

## <a name="register-source-machine-with-configuration-server"></a>구성 서버에 원본 머신 등록

### <a name="if-source-machine-has-windows-os"></a>원본 머신에 Windows OS가 설치된 경우

원본 머신에서 다음 명령 실행

```
  cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
  UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
  ```
**설정** | **세부 정보**
--- | ---
사용 현황 | UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
에이전트 구성 로그 | %ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log에 있습니다.
/CSEndPoint | 필수 매개 변수입니다. 구성 서버의 IP 주소를 지정합니다. 유효한 IP 주소를 사용합니다.
/PassphraseFilePath |  필수. 암호의 위치입니다. 유효한 UNC 또는 로컬 파일 경로를 사용합니다.

### <a name="if-source-machine-has-linux-os"></a>원본 머신에 Linux OS가 설치된 경우

원본 머신에서 다음 명령 실행

```
  /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
  ```
**설정** | **세부 정보**
--- | ---
사용 현황 | cd /usr/local/ASR/Vx/bin<br/><br/> UnifiedAgentConfigurator.sh -i <CSIP> -P <PassphraseFilePath>
-i | 필수 매개 변수입니다. 구성 서버의 IP 주소를 지정합니다. 유효한 IP 주소를 사용합니다.
-P |  필수. 암호가 저장되는 파일의 전체 파일 경로입니다. 유효한 폴더 사용