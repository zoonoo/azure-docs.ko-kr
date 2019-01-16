---
title: Azure Site Recovery를 사용하여 VMware VM 및 물리적 서버를 Azure로 재해 복구하는 동안 구성 서버 문제 해결 | Microsoft Docs
description: 이 문서에서는 Azure Site Recovery를 사용하여 VMware VM 및 물리적 서버를 Azure로 재해 복구하는 구성 서버 배포에 대한 문제 해결 정보를 제공합니다.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 12/17/2018
ms.author: ramamill
ms.openlocfilehash: 597b8f59ef6991f7868d3de481e98ed9a459077b
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2019
ms.locfileid: "54050798"
---
# <a name="troubleshoot-configuration-server-issues"></a>구성 서버 문제 해결

이 문서는 [Azure Site Recovery](site-recovery-overview.md) 구성 서버를 배포 및 관리할 때 문제를 해결하는 데 도움이 됩니다. 구성 서버는 관리 서버로 작동합니다. Site Recovery에서는 구성 서버를 사용하여 온-프레미스 VMware VM 및 물리적 서버의 재해 복구를 Azure로 설정합니다. 다음 섹션에서는 새 구성 서버를 추가할 때 및 구성 서버를 관리할 때 발생할 수 있는 가장 일반적인 오류에 대해 설명합니다.

## <a name="registration-failures"></a>등록 오류

원본 머신은 모바일 에이전트를 설치할 때 구성 서버에 등록됩니다. 이 단계를 수행하는 동안 오류가 발생하면 다음 지침에 따라 디버그할 수 있습니다.

1. C:\ProgramData\ASR\home\svsystems\var\configurator_register_host_static_info.log 파일을 엽니다. (ProgramData 폴더는 숨겨진 폴더일 수 있습니다. ProgramData 폴더가 보이지 않으면 파일 탐색기의 **보기** 탭에 있는 **표시/숨기기** 섹션에서 **숨긴 항목** 확인란을 선택하십시오.) 오류는 여러 가지 문제로 인해 발생할 수 있습니다.

2. **No Valid IP Address found** 문자열을 검색합니다. 문자열이 검색되면:
    1. 요청한 호스트 ID가 원본 머신의 호스트 ID와 같은지 확인합니다.
    2. 실제 NIC에 할당된 IP 주소가 원본 머신에 하나 이상 있는지 확인합니다. 에이전트가 구성 서버에 성공적으로 등록되려면, 실제 NIC에 할당된 유효한 IPv4 주소가 원본 머신에 하나 이상 있어야 합니다.
    3. 원본 머신에서 다음 명령 중 하나를 실행하여 원본 머신의 모든 IP 주소를 가져옵니다.
      - Windows의 경우: `> ipconfig /all`
      - Linux의 경우: `# ifconfig -a`

3. **No Valid IP Address found** 문자열이 검색되지 않으면 **Reason=>NULL** 문자열을 검색합니다. 이 오류는 원본 머신이 빈 호스트를 사용하여 구성 서버에 등록하는 경우 발생합니다. 문자열이 검색되면:
    - 문제를 해결한 후에는 [구성 서버에 원본 머신 등록](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server)의 지침에 따라 등록을 수동으로 다시 시도합니다.

4. **Reason=>NULL** 문자열이 검색되지 않으면, 원본 머신에서 C:\ProgramData\ASRSetupLogs\UploadedLogs\ASRUnifiedAgentInstaller.log 파일을 엽니다. (ProgramData 폴더는 숨겨진 폴더일 수 있습니다. ProgramData 폴더가 보이지 않으면 파일 탐색기의 **보기** 탭에 있는 **표시/숨기기** 섹션에서 **숨긴 항목** 확인란을 선택하십시오.) 오류는 여러 가지 문제로 인해 발생할 수 있습니다. 

5. **post request: (7) - Couldn't connect to server** 문자열을 검색합니다. 문자열이 검색되면:
    1. 원본 머신과 구성 서버 간의 네트워크 문제를 해결합니다. ping, traceroute과 같은 네트워크 도구 또는 웹 브라우저를 사용하여 원본 머신에서 구성 서버에 연결할 수 있는지 확인합니다. 원본 머신이 포트 443을 통해 구성 서버에 연결할 수 있는지 확인합니다.
    2. 원본 머신의 방화벽 규칙이 원본 머신과 구성 서버 간의 연결을 차단하는지 검사합니다. 연결 문제가 있으면 네트워크 관리자와 협력하여 차단을 해제하십시오.
    3. [Site Recovery 폴더에 나열된 폴더가 바이러스 백신 프로그램](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program)에서 제외되고 바이러스 백신 소프트웨어에서 제외되도록 해야 합니다.
    4. 네트워크 문제가 해결되면 [구성 서버에 원본 머신 등록](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server)의 지침에 따라 등록을 다시 시도합니다.

6. **post request: (7) - Couldn't connect to server** 문자열이 검색되지 않으면 동일한 로그 파일에서 **request: (60) - Peer certificate cannot be authenticated with given CA certificates** 문자열을 찾아봅니다. 이 오류는 구성 서버 인증서가 만료되었거나 원본 머신이 TLS 1.0 이상 SSL 프로토콜을 지원하지 않기 때문에 발생할 수 있습니다. 또한 방화벽이 원본 머신과 구성 서버 간의 SSL 통신을 차단하면 발생할 수 있습니다. 문자열이 검색되면: 
    1. 해결을 위해, 원본 머신의 웹 브라우저를 사용하여 구성 서버 IP 주소에 연결합니다. https:\/\/<configuration server IP address\>:443/ URI를 사용합니다. 원본 머신이 포트 443을 통해 구성 서버에 연결할 수 있는지 확인합니다.
    2. 원본 머신이 구성 서버와 통신하기 위해 원본 머신에서 방화벽 규칙을 추가하거나 제거해야 하는지 확인합니다. 사용하는 방화벽 소프트웨어가 다양하기 때문에, 필요한 방화벽 구성을 모두 나열할 수는 없습니다. 연결 문제가 있으면 네트워크 관리자와 협력하여 차단을 해제하십시오.
    3. [Site Recovery 폴더에 나열된 폴더가 바이러스 백신 프로그램](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program)에서 제외되고 바이러스 백신 소프트웨어에서 제외되도록 해야 합니다.  
    4. 문제를 해결한 후에 [구성 서버에 원본 머신 등록](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server)의 지침에 따라 등록을 다시 시도합니다.

7. Linux에서 <INSTALLATION_DIR\>/etc/drscout.conf에 있는 플랫폼 값이 손상된 경우에는 등록에 실패합니다. 이 문제를 식별하려면 /var/log/ua_install.log 파일을 엽니다. **Aborting configuration as VM_PLATFORM value is either null or it is not VmWare/Azure** 문자열을 검색합니다. 플랫폼은 **VmWare**나 **Azure**로 설정되어야 합니다. drscout.conf 파일이 손상된 경우에는 [모바일 에이전트를 제거](vmware-physical-mobility-service-overview.md#uninstall-the-mobility-service)한 다음, 모바일 에이전트를 다시 설치하는 것이 좋습니다. 제거에 실패하면 다음 단계를 완료합니다.
    1. Installation_Directory/uninstall.sh 파일을 열고 **StopServices** 함수에 대한 호출을 주석으로 처리합니다.
    2. Installation_Directory/Vx/bin/uninstall.sh 파일을 열고 **stop_services** 함수에 대한 호출을 주석으로 처리합니다.
    3. Installation_Directory/Fx/uninstall.sh 파일을 열고 Fx 서비스를 중지하려고 시도하는 전체 섹션을 주석으로 처리합니다.
    4. 모바일 에이전트를 [제거](vmware-physical-mobility-service-overview.md#uninstall-the-mobility-service)합니다. 제거가 완료되면 시스템을 다시 부팅한 다음, 모바일 에이전트를 다시 설치해봅니다.

## <a name="installation-failure-failed-to-load-accounts"></a>설치 실패: 계정을 로드하지 못함

이 오류는 모바일 에이전트를 설치하고 구성 서버에 등록하는 동안 서비스가 전송 연결에서 데이터를 읽을 수 없을 때 발생합니다. 이 문제를 해결하려면 원본 머신에 TLS 1.0이 활성화되어 있는지 확인합니다.

## <a name="change-the-ip-address-of-the-configuration-server"></a>구성 서버의 IP 주소 변경

구성 서버의 IP 주소는 변경하지 않는 것이 좋습니다. 구성 서버에 할당된 모든 IP 주소는 고정 IP 주소여야 합니다. DHCP IP 주소는 사용하지 마십시오.

## <a name="acs50008-saml-token-is-invalid"></a>ACS50008: SAML 토큰이 잘못되었습니다.

이 오류를 방지하려면, 시스템 시계의 시간이 현지 시간과 15분 넘게 차이가 나지 않도록 해야 합니다. 설치 관리자를 다시 실행하여 등록을 완료합니다.

## <a name="failed-to-create-a-certificate"></a>인증서 만들기 실패

Site Recovery를 인증하는 데 필요한 인증서를 만들 수 없습니다. 로컬 관리자로 설치 프로그램을 실행하고 있는지 확인한 후에 설치 프로그램을 다시 실행합니다.

## <a name="register-the-source-machine-with-the-configuration-server"></a>구성 서버에 원본 머신 등록

### <a name="if-the-source-machine-runs-windows"></a>원본 머신에서 Windows를 실행하는 경우

원본 머신에서 다음 명령을 실행합니다.

```
  cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
  UnifiedAgentConfigurator.exe  /CSEndPoint <configuration server IP address> /PassphraseFilePath <passphrase file path>
  ```

설정 | 세부 정보
--- | ---
사용 현황 | UnifiedAgentConfigurator.exe  /CSEndPoint <구성 서버 IP 주소\> /PassphraseFilePath <암호 파일 경로\>
에이전트 구성 로그 | %ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log 아래에 있습니다.
/CSEndPoint | 필수 매개 변수입니다. 구성 서버의 IP 주소를 지정합니다. 유효한 IP 주소를 사용합니다.
/PassphraseFilePath |  필수. 암호의 위치입니다. 유효한 UNC 또는 로컬 파일 경로를 사용합니다.

### <a name="if-the-source-machine-runs-linux"></a>원본 머신에서 Linux를 실행하는 경우

원본 머신에서 다음 명령을 실행합니다.

```
  /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <configuration server IP address> -P /var/passphrase.txt
  ```

설정 | 세부 정보
--- | ---
사용 현황 | cd /usr/local/ASR/Vx/bin<br /><br /> UnifiedAgentConfigurator.sh -i <구성 서버 IP 주소\> -P <암호 파일 경로\>
-i | 필수 매개 변수입니다. 구성 서버의 IP 주소를 지정합니다. 유효한 IP 주소를 사용합니다.
-P |  필수. 암호가 저장되는 파일의 전체 파일 경로입니다. 유효한 폴더를 사용합니다.

