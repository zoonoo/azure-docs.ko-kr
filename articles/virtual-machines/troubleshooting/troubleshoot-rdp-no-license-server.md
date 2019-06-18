---
title: Azure VM에 연결할 때 원격 데스크톱 라이선스 서버를 사용할 수 없음 | Microsoft Docs
description: 원격 데스크톱 라이선스 서버를 사용할 수 없어서 RDP가 실패하는 문제 해결 방법 알아보기 | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/23/2018
ms.author: genli
ms.openlocfilehash: 550b971602d1736e0ba3981a5b7ca546862ea034
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60318955"
---
# <a name="remote-desktop-license-server-isnt-available-when-you-connect-to-an-azure-vm"></a>Azure VM에 연결할 때 원격 데스크톱 라이선스 서버를 사용할 수 없음

이 문서는 라이선스를 제공하는 데 원격 데스크톱 라이선스 서버를 사용할 수 없어서 Azure VM(가상 머신)에 연결할 수 없는 문제 해결에 유용합니다.

## <a name="symptoms"></a>증상

VM(가상 머신)에 연결하려고 시도할 때 다음과 같은 시나리오가 발생합니다.

- VM 스크린샷에 운영 체제가 완전히 로드되어 자격 증명을 기다리고 있다는 것이 표시됩니다.
- Microsoft RDP(원격 데스크톱 프로토콜) 연결을 시도하면 다음과 같은 오류 메시지가 수신됩니다.

  - 라이선스를 제공하는 데 원격 데스크톱 라이선스 서버를 사용할 수 없으므로 원격 세션이 끊겼습니다.

  - 원격 데스크톱 라이선스 서버를 사용할 수 없습니다. 이 컴퓨터의 유예 기간이 경과했으며 유효한 Windows Server 2008 이상 라이선스 서버에 접속하지 않기 때문에 원격 데스크톱 서비스의 작동이 곧 중지됩니다. 라이선스 진단을 사용하려면 이 메시지를 선택하여 RD 세션 호스트 서버 구성을 여세요.

그러나 관리 세션을 사용하여 일반적인 방법으로 VM에 연결할 수 있습니다.

```
mstsc /v:<Server>[:<Port>] /admin
```

## <a name="cause"></a>원인

원격 데스크톱 라이선스 서버를 사용하여 원격 세션을 시작하는 데 필요한 라이선스를 제공할 수 없는 경우 이 문제가 발생합니다. VM에서 원격 데스크톱 세션 호스트 역할이 설정되었더라도 여러 시나리오에서 이 문제가 발생할 수 있습니다.

- 환경에 원격 데스크톱 라이선싱 역할이 전혀 없었으며 유예 기간(180일)이 끝났습니다.
- 원격 데스크톱 라이선스가 환경에 설치되었지만 활성화되지 않았습니다.
- 환경의 원격 데스크톱 라이선스에 연결 설정을 위한 CAL(클라이언트 액세스 라이선스)이 삽입되지 않았습니다.
- 원격 데스크톱 라이선스가 환경에 설치되었습니다. 사용 가능한 CAL이 있지만 제대로 구성되지 않았습니다.
- 원격 데스크톱 라이선스에 CAL이 있고 활성화되었습니다. 그러나 원격 데스크톱 라이선스 서버의 다른 문제 때문에 환경 내에서 라이선스를 제공할 수 없습니다.

## <a name="solution"></a>해결 방법

이 문제를 해결하려면 [OS 디스크를 백업](../windows/snapshot-copy-managed-disk.md)하고 다음 단계를 수행합니다.

1. 관리 세션을 사용하여 VM에 연결합니다.

   ```
   mstsc /v:<Server>[:<Port>] /admin
   ```

    관리 세션을 사용하여 VM에 연결할 수 없는 경우 다음과 같이 [Azure의 Virtual Machine 직렬 콘솔](serial-console-windows.md)을 사용하여 VM에 액세스합니다.

    1. **지원 및 문제 해결** > **직렬 콘솔(미리 보기)** 을 선택하여 직렬 콘솔에 액세스합니다. VM에서 기능을 사용하도록 설정하면 VM을 성공적으로 연결할 수 있습니다.

    2. CMD 인스턴스에 대한 새 채널을 만듭니다. **CMD**를 입력하여 채널을 시작하고 채널 이름을 가져옵니다.

    3. CMD 인스턴스를 실행하는 채널로 전환합니다. 이 경우 채널 1이어야 합니다.

       ```
       ch -si 1
       ```

    4. **Enter** 키를 다시 선택하고 VM에 대한 유효한 사용자 이름 및 암호(로컬 또는 도메인 ID)를 입력합니다.

2. VM에서 원격 데스크톱 세션 호스트 역할을 사용하도록 설정되었는지 확인합니다. 역할이 사용하도록 설정된 경우 올바르게 작동하는지 확인합니다. 관리자 권한 CMD 인스턴스를 열고 다음 단계를 수행합니다.

    1. 다음 명령을 사용하여 원격 데스크톱 세션 호스트 역할의 상태를 확인합니다.

       ```
        reg query "HKLM\SOFTWARE\Microsoft\ServerManager\ServicingStorage\ServerComponentCache\RDS-RD-Server" /v InstallState
        ```

        이 명령이 0 값을 반환하면 역할이 사용되지 않는다는 의미이며 3단계로 이동하면 됩니다.

    2. 다음 명령을 사용하여 정책을 확인하고 필요한 경우 다시 구성합니다.

       ```
        reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\RCM\Licensing Core" /v LicensingMode reg query "HKLM\SYSTEM\CurrentControlSet\Services\TermService\Parameters" /v SpecifiedLicenseServers
       ```

        **LicensingMode** 값이 4가 아닌 다른 값으로 설정된 경우(사용자별) 값을 4로 설정합니다.

         ```
        reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\RCM\Licensing Core" /v LicensingMode /t REG_DWORD /d 4
        ```

       **SpecifiedLicenseServers** 값이 없거나 라이선스 서버 정보가 잘못된 경우 다음과 같이 변경합니다.

       ```
        reg add "HKLM\SYSTEM\CurrentControlSet\Services\TermService\Parameters" /v SpecifiedLicenseServers /t REG_MULTI_SZ /d "<FQDN / IP License server>"
       ```

    3. 레지스트리를 변경한 후 VM을 다시 시작합니다.

    4. CAL이 없는 경우 원격 데스크톱 세션 호스트 역할을 제거합니다. 그런 다음, RDP가 다시 정상으로 설정됩니다. VM에 두 개의 동시 RDP 연결이 허용됩니다.

        ```
       dism /ONLINE /Disable-feature /FeatureName:Remote-Desktop-Services
        ```

        VM에 원격 데스크톱 라이선싱 역할이 있고 사용되지 않는 경우 해당 역할을 제거할 수 있습니다.

       ```
        dism /ONLINE /Disable-feature /FeatureName:Licensing
       ```

    5. VM이 원격 데스크톱 라이선스 서버에 연결할 수 있는지 확인합니다. VM과 라이선스 서버 간에 포트 135에 대한 연결을 테스트할 수 있습니다. 

       ```
       telnet <FQDN / IP License Server> 135
       ```

3. 환경에 원격 데스크톱 라이선스 서버가 없는데 원하는 경우 [원격 데스크톱 라이선싱 역할 서비스를 설치](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731765(v=ws.11))할 수 있습니다. 그런 다음, [RDS 라이선싱을 구성](https://blogs.technet.microsoft.com/askperf/2013/09/20/rd-licensing-configuration-on-windows-server-2012/)합니다.

4. 원격 데스크톱 라이선스 서버가 구성되었고 정상 상태이면 CAL을 사용하여 원격 데스크톱 라이선스 서버가 활성화되었는지 확인합니다.

## <a name="need-help-contact-support"></a>도움 필요 시 지원 문의

추가 도움이 필요한 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 해결하세요.
