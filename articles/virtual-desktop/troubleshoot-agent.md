---
title: Windows 가상 데스크톱 에이전트 문제 해결-Azure
description: 일반적인 에이전트 및 연결 문제를 해결 하는 방법입니다.
author: Sefriend
ms.topic: troubleshooting
ms.date: 12/16/2020
ms.author: sefriend
manager: clarkn
ms.openlocfilehash: 5f9d772a4cc5722201891450707a68fe487acc3a
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/17/2021
ms.locfileid: "98540646"
---
# <a name="troubleshoot-common-windows-virtual-desktop-agent-issues"></a>일반적인 Windows 가상 데스크톱 에이전트 문제 해결

Windows 가상 데스크톱 에이전트는 여러 요소로 인해 연결 문제가 발생할 수 있습니다.
   - 에이전트에서 서비스를 중지 하도록 하는 브로커에 오류가 발생 했습니다.
   - 업데이트에 문제가 있습니다.
   - 에이전트 설치 중에를 설치 하는 데 문제가 발생 하 여 세션 호스트에 대 한 연결이 중단 됩니다.

이 문서에서는 이러한 일반적인 시나리오에 대 한 솔루션 및 연결 문제를 해결 하는 방법을 안내 합니다.

## <a name="error-the-rdagentbootloader-andor-remote-desktop-agent-loader-has-stopped-running"></a>오류: RDAgentBootLoader 및/또는 원격 데스크톱 에이전트 로더에서 실행이 중지 되었습니다.

다음과 같은 문제가 발생 하는 경우 에이전트를 로드 하는 부팅 로더에서 에이전트를 제대로 설치할 수 없고 에이전트 서비스가 실행 되 고 있지 않음을 의미 합니다.
- **Rdagentbootloader** 의 작동이 중지 되었거나 실행 되 고 있지 않습니다.
- **원격 데스크톱 에이전트 로더의** 상태가 없습니다.

이 문제를 해결 하려면 Ragent 부팅 로더를 시작 합니다.

1. 서비스 창에서 **원격 데스크톱 에이전트 로더** 를 마우스 오른쪽 단추로 클릭 합니다.
2. **시작** 을 선택합니다. 이 옵션이 회색으로 표시 되 면 관리자 권한이 없어 서비스를 시작 해야 합니다.
3. 10 초 후 **원격 데스크톱 에이전트 로더** 를 마우스 오른쪽 단추로 클릭 합니다.
4. **새로 고침** 을 선택합니다.
5. 서비스를 시작 하 고 새로 고친 후에 서비스를 중지 하면 등록에 실패할 수 있습니다. 자세한 내용은 [INVALID_REGISTRATION_TOKEN](#error-invalid_registration_token)를 참조 하세요.

## <a name="error-invalid_registration_token"></a>오류: INVALID_REGISTRATION_TOKEN

**이벤트 뷰어**  >  **Windows Logs**  >  **응용 프로그램** 으로 이동 합니다. 설명에 **INVALID_REGISTRATION_TOKEN** 표시 되는 ID가 3277 인 이벤트가 표시 되는 경우 해당 등록 토큰은 유효한 것으로 인식 되지 않습니다.

이 문제를 해결 하려면 유효한 등록 토큰을 만듭니다.

1. 새 등록 토큰을 만들려면 [VM에 대 한 새 등록 키 생성](#step-3-generate-a-new-registration-key-for-the-vm) 섹션의 단계를 따르세요.
2. 레지스트리 편집기를 엽니다. 
3. **HKEY_LOCAL_MACHINE**  >  **SOFTWARE**  >  **Microsoft**  >  **RDInfraAgent** 로 이동 합니다.
4. **Isregistered** 을 선택 합니다. 
5. **값 데이터:** entry 상자에 **0** 을 입력 하 고 **확인** 을 선택 합니다. 
6. **Registrationtoken** 을 선택 합니다. 
7. **값 데이터:** entry 상자에 1 단계의 등록 토큰을 붙여넣습니다. 

   > [!div class="mx-imgBorder"]
   > ![IsRegistered 0의 스크린샷](media/isregistered-token.png)

8. 관리자 권한으로 명령 프롬프트를 엽니다.
9. **Net Stop RDAgentBootLoader** 을 입력 합니다. 
10. **Net Start RDAgentBootLoader** 이름을 입력 합니다. 
11. 레지스트리 편집기를 엽니다.
12. **HKEY_LOCAL_MACHINE**  >  **SOFTWARE**  >  **Microsoft**  >  **RDInfraAgent** 로 이동 합니다.
13. **Isregistered** 됨이 1로 설정 되어 있는지 확인 하 고, **registrationtoken** 의 데이터 열에는 아무 것도 없습니다. 

   > [!div class="mx-imgBorder"]
   > ![IsRegistered 1의 스크린샷](media/isregistered-registry.png)

## <a name="error-agent-cannot-connect-to-broker-with-invalid_form-or-not_found-url"></a>오류: 에이전트는 INVALID_FORM 또는 NOT_FOUND를 사용 하 여 broker에 연결할 수 없습니다. URL

**이벤트 뷰어**  >  **Windows Logs**  >  **응용 프로그램** 으로 이동 합니다. ID가 3277 인 이벤트가 표시 되는 경우 **INVALID_FORM** 또는 **NOT_FOUND.** 설명의 URL입니다. 에이전트와 broker 간의 통신에서 오류가 발생 했습니다. 에이전트가 broker에 연결할 수 없고 특정 URL에 연결할 수 없습니다. 방화벽 또는 DNS 설정 때문일 수 있습니다.

이 문제를 해결 하려면 BrokerURI 및 BrokerURIGlobal에 도달할 수 있는지 확인 합니다.
1. 레지스트리 편집기를 엽니다. 
2. **HKEY_LOCAL_MACHINE**  >  **SOFTWARE**  >  **Microsoft**  >  **RDInfraAgent** 로 이동 합니다. 
3. **BrokerURI** 및 **BrokerURIGlobal** 에 대 한 값을 기록해 둡니다.

   > [!div class="mx-imgBorder"]
   > ![Broker uri 및 broker uri global의 스크린샷](media/broker-uri.png)

 
4. 브라우저를 열고 *\<BrokerURI\> api/상태* 로 이동 합니다. 
   - **BrokerURI** 에서 3 단계의 값을 사용 하는지 확인 합니다. 이 섹션의 예제에서는 <https://rdbroker-g-us-r0.wvd.microsoft.com/api/health> 입니다.
5. 브라우저에서 다른 탭을 열고 *\<BrokerURIGlobal\> api/상태* 로 이동 합니다. 
   - **BrokerURIGlobal** 링크에서 3 단계의 값을 사용 하는지 확인 합니다. 이 섹션의 예제에서는 <https://rdbroker.wvd.microsoft.com/api/health> 입니다.
6. 네트워크에서 broker 연결이 차단 되 고 있지 않으면 두 페이지가 모두 로드 되 고 다음 스크린샷에 표시 된 것 처럼 **"RD broker가 정상입니다."** 라는 메시지가 표시 됩니다. 

   > [!div class="mx-imgBorder"]
   > ![성공적으로 로드 된 broker uri 액세스의 스크린샷](media/broker-uri-web.png)

   > [!div class="mx-imgBorder"]
   > ![성공적으로 로드 된 broker 전역 uri 액세스의 스크린샷](media/broker-global.png)
 

7. 네트워크에서 broker 연결을 차단 하는 경우 다음 스크린샷에 표시 된 것 처럼 페이지가 로드 되지 않습니다. 

   > [!div class="mx-imgBorder"]
   > ![실패 한 로드 된 broker 액세스의 스크린샷](media/unsuccessful-broker-uri.png)

   > [!div class="mx-imgBorder"]
   > ![실패 한 로드 된 broker 전역 액세스의 스크린샷](media/unsuccessful-broker-global.png)

8. 네트워크에서 이러한 Url을 차단 하는 경우 필요한 Url을 차단 해제 해야 합니다. 자세한 내용은 [필수 URL 목록](safe-url-list.md)을 참조 하세요.
9. 그래도 문제가 해결 되지 않으면 에이전트에서 broker로 연결을 차단 하는 암호를 사용 하는 그룹 정책이 없는지 확인 합니다. Windows 가상 데스크톱은 [Azure Front 문과](../frontdoor/front-door-faq.MD#what-are-the-current-cipher-suites-supported-by-azure-front-door)동일한 TLS 1.2 암호화를 사용 합니다. 자세한 내용은 [연결 보안](network-connectivity.md#connection-security)을 참조 하세요.

## <a name="error-3703-or-3019"></a>오류: 3703 또는 3019

**이벤트 뷰어**  >  **Windows Logs**  >  **응용 프로그램** 으로 이동 합니다. ID가 3703 인 이벤트가 표시 되는 경우 **RD 게이트웨이 Url:에 액세스할** 수 없거나 설명에 id 3019이 있는 이벤트가 표시 되 면 에이전트에서 게이트웨이 url 또는 웹 소켓 전송 url에 연결할 수 없습니다. 세션 호스트에 성공적으로 연결 하 고 이러한 끝점에 대 한 네트워크 트래픽이 제한을 무시 하도록 허용 하려면 [필요한 Url 목록](safe-url-list.md)에서 url을 차단 해제 해야 합니다. 또한 방화벽 또는 프록시 설정이 이러한 Url을 차단 하지 않는지 확인 합니다. Windows 가상 데스크톱을 사용 하려면 이러한 Url의 차단을 해제 해야 합니다.

이 문제를 해결 하려면 방화벽 및/또는 DNS 설정에서 다음 Url을 차단 하지 않는지 확인 합니다.
1. [Azure 방화벽을 사용 하 여 Windows 가상 데스크톱 배포를 보호 합니다](../firewall/protect-windows-virtual-desktop.md).
2. [Azure 방화벽 DNS 설정을](../firewall/dns-settings.md)구성 합니다.

## <a name="error-installmsiexception"></a>오류: InstallMsiException

**이벤트 뷰어**  >  **Windows Logs**  >  **응용 프로그램** 으로 이동 합니다. 설명에서 **Installmsiexception** 이라는 ID 3277 이벤트가 표시 되는 경우 에이전트를 설치 하는 동안 다른 응용 프로그램에 대해 설치 관리자가 이미 실행 중이 고, 정책에서 msiexec.exe 프로그램이 실행 되지 않도록 차단 하 고 있는 것입니다.

이 문제를 해결 하려면 다음 정책을 사용 하지 않도록 설정 하십시오.
   - Windows Installer 해제  
      - 범주 경로: 컴퓨터 구성 \ 관리 템플릿 \ 설치 관리자
   
>[!NOTE]
>이는 현재 인식 중인 정책의 포괄적인 목록이 아닙니다.

정책을 사용 하지 않도록 설정 하려면:
1. 관리자 권한으로 명령 프롬프트를 엽니다.
2. Rsop.msc를 입력 하 **고 실행 합니다**.
3. 표시 되는 **정책 결과 집합** 창의 범주 경로로 이동 합니다.
4. 정책을 선택합니다.
5. **사용 안 함** 을 선택합니다.
6. **적용** 을 선택합니다.   

   > [!div class="mx-imgBorder"]
   > ![정책 결과 집합에서 Windows Installer 정책의 스크린샷](media/gpo-policy.png)

## <a name="error-win32exception"></a>오류: System.componentmodel.win32exception

**이벤트 뷰어**  >  **Windows Logs**  >  **응용 프로그램** 으로 이동 합니다. 설명에서 **Installmsiexception 이라는 오류가** 발생 하 여 ID가 3277 인 이벤트가 표시 되는 경우 정책에서 시작 cmd.exe를 차단 합니다. 이 프로그램을 차단 하면 에이전트가 업데이트 될 때마다 서비스를 다시 시작 하는 데 사용 해야 하는 콘솔 창이 실행 되지 않습니다.

이 문제를 해결 하려면 다음 정책을 사용 하지 않도록 설정 하십시오.
   - 명령 프롬프트에 대한 액세스 방지   
      - 범주 경로: 사용자 구성 \ 사용자 템플릿 \ 시스템
    
>[!NOTE]
>이는 현재 인식 중인 정책의 포괄적인 목록이 아닙니다.

정책을 사용 하지 않도록 설정 하려면:
1. 관리자 권한으로 명령 프롬프트를 엽니다.
2. Rsop.msc를 입력 하 **고 실행 합니다**.
3. 표시 되는 **정책 결과 집합** 창의 범주 경로로 이동 합니다.
4. 정책을 선택합니다.
5. **사용 안 함** 을 선택합니다.
6. **적용** 을 선택합니다.   

## <a name="error-stack-listener-isnt-working-on-windows-10-2004-vm"></a>오류: Windows 10 2004 VM에서 스택 수신기가 작동 하지 않습니다.

명령 프롬프트에서 **qwinsta** 를 실행 하 고 **rdp-sxs** 옆에 표시 되는 버전 번호를 적어둡니다. **Qwinsta** 를 실행 한 후에 **수신 대기** 하는 **rdp-tcp** 및 **rdp-sxs** 구성 요소가 표시 되지 않거나, 이러한 구성 요소가 모두 표시 되지 않는 경우 스택 문제가 있음을 의미 합니다. 스택 업데이트는 에이전트 업데이트와 함께 설치 되며,이 설치가 awry 되 면 Windows 가상 데스크톱 수신기가 작동 하지 않습니다.

이 문제를 해결하려면:
1. 레지스트리 편집기를 엽니다.
2. **HKEY_LOCAL_MACHINE**  >  **SYSTEM**  >  **CurrentControlSet**  >  **Control**  >  **Terminal Server**  >  **WinStations** 로 이동 합니다.
3. **WinStations** 아래에서 스택 버전 마다 여러 폴더가 표시 될 수 있습니다. 명령 프롬프트에서 **qwinsta** 를 실행할 때 표시 되는 버전 정보와 일치 하는 폴더를 선택 합니다.
4. **FReverseConnectMode** 를 찾고 해당 데이터 값이 **1** 인지 확인 합니다. 또한 **fEnableWinStation** 가 **1** 로 설정 되어 있는지 확인 합니다.

   > [!div class="mx-imgBorder"]
   > ![FReverseConnectMode의 스크린샷](media/fenable-2.png)

5. **FReverseConnectMode** 가 **1** 로 설정 되지 않은 경우 **fReverseConnectMode** 를 선택 하 고 값 필드에 **1** 을 입력 합니다. 
6. **FEnableWinStation** 가 **1** 로 설정 되지 않은 경우 **fEnableWinStation** 를 선택 하 고 값 필드에 **1** 을 입력 합니다.
7. VM이 다시 시작됩니다. 

>[!NOTE]
>한 번에 여러 Vm에 대 한 **fReverseConnectMode** 또는 **fEnableWinStation** 모드를 변경 하려면 다음 두 가지 작업 중 하나를 수행 하면 됩니다.
>
>- 이미 작업 하 고 있는 컴퓨터에서 레지스트리 키를 내보내이 변경 작업을 수행 해야 하는 다른 모든 컴퓨터로 가져옵니다.
>- 변경 해야 하는 컴퓨터에 대 한 레지스트리 키 값을 설정 하는 일반 GPO (정책 개체)를 만듭니다.

7. **HKEY_LOCAL_MACHINE**  >  **SYSTEM**  >  **CurrentControlSet**  >  **Control**  >  **Terminal Server**  >  **clustersettings** 로 이동 합니다.
8. **Clustersettings** 에서 **sessiondirectorylistener** 를 찾고 해당 데이터 값이 **rdp-sxs** 인지 확인 합니다.
9. **Sessiondirectorylistener** 를 **rdp-tcp** 로 설정 하지 않은 경우 에이전트 [및 부팅 로더 제거](#step-1-uninstall-all-agent-boot-loader-and-stack-component-programs) 섹션의 단계에 따라 에이전트, 부팅 로더 및 스택 구성 요소를 먼저 제거한 다음 [에이전트와 부팅 로더를 다시 설치](#step-4-reinstall-the-agent-and-boot-loader)해야 합니다. 그러면 side-by-side 스택이 다시 설치 됩니다.

## <a name="error-users-keep-getting-disconnected-from-session-hosts"></a>오류: 사용자가 세션 호스트와의 연결을 계속 유지할 수 있습니다.

**이벤트 뷰어**  >  **Windows Logs**  >  **응용 프로그램** 으로 이동 합니다. ID가 0 인 이벤트가 표시 되는 경우 설명에 **CheckSessionHostDomainIsReachableAsync** 가 표시 되 고, 사용자가 해당 세션 호스트와의 연결을 계속 유지 하면 서버는 Windows 가상 데스크톱 서비스에서 하트 비트를 선택 하지 않습니다.

이 문제를 해결 하려면 하트 비트 임계값을 변경 합니다.
1. 관리자 권한으로 명령 프롬프트를 엽니다.
2. **Qwinsta** 명령을 입력 하 고 실행 합니다.
3. 표시 되는 두 가지 스택 구성 요소는 **rdp-tcp** 및 **rdp-sxs** 입니다. 
   - 사용 중인 OS의 버전에 따라 다음 스크린샷에 표시 된 것 처럼 **rdp-sxs** 뒤에 빌드 번호가 올 수 있습니다. 표시 되는 경우 나중에이 숫자를 기록해 두어야 합니다.
4. 레지스트리 편집기를 엽니다.
5. **HKEY_LOCAL_MACHINE**  >  **SYSTEM**  >  **CurrentControlSet**  >  **Control**  >  **Terminal Server**  >  **WinStations** 로 이동 합니다.
6. **WinStations** 아래에는 다양 한 스택 버전에 대 한 여러 폴더가 표시 될 수 있습니다. 3 단계의 버전 번호와 일치 하는 폴더를 선택 합니다.
7. 레지스트리 편집기를 마우스 오른쪽 단추로 클릭 한 다음 **새**  >  **dword (32 비트) 값** 을 선택 하 여 새 레지스트리 dword를 만듭니다. DWORD를 만들 때 다음 값을 입력 합니다.
   - HeartbeatInterval: 1만
   - HeartbeatWarnCount: 30 
   - HeartbeatDropCount: 60 
8. VM이 다시 시작됩니다.

## <a name="error-downloadmsiexception"></a>오류: DownloadMsiException

**이벤트 뷰어**  >  **Windows Logs**  >  **응용 프로그램** 으로 이동 합니다. 설명에 **Downloadmsiexception** 이라는 ID 3277이 포함 된 이벤트가 표시 되 면 디스크에 rdagent에 대 한 공간이 부족 합니다.

이 문제를 해결 하려면 다음을 수행 하 여 디스크 공간을 확보 합니다.
   - 더 이상 사용자에 게 없는 파일 삭제
   - VM의 저장소 용량을 늘립니다.

## <a name="error-vms-are-stuck-in-unavailable-or-upgrading-state"></a>오류: Vm을 사용할 수 없거나 상태가 업그레이드 되지 않습니다.

관리자 권한으로 PowerShell 창을 열고 다음 cmdlet을 실행 합니다.

```powershell
Get-AzWvdSessionHost -TenantName <tenantname> -HostPoolName <hostpoolname>|Select-Object*
```

호스트 풀에서 세션 호스트 또는 호스트에 대해 나열 된 상태가 항상 **사용할 수 없음** 또는 **업그레이드** 인 경우 에이전트 또는 스택 설치가 실패 했을 수 있습니다.

이 문제를 해결 하려면 side-by-side 스택을 다시 설치 합니다.
1. 관리자 권한으로 명령 프롬프트를 엽니다.
2. **Net Stop RDAgentBootLoader** 을 입력 합니다. 
3. **제어판** > **프로그램** > **프로그램 및 기능** 으로 이동합니다.
4. 최신 버전의 **원격 데스크톱 서비스 SxS 네트워크 스택** 또는 ReverseConnectListener의 **HKEY_LOCAL_MACHINE**  >  **SYSTEM**  >  **CurrentControlSet**  >  **Control**  >  **Terminal Server**  >  **WinStations** 에 나열 된 버전을 제거 합니다.
5. 관리자 권한으로 콘솔 창을 열고 **Program Files**  >  **Microsoft RDInfra** 로 이동 합니다.
6. **SxSStack** 구성 요소를 선택 하거나 **msiexec/i SxSStack <version> 명령을** 실행 하 여 msi를 설치 합니다.
8. VM이 다시 시작됩니다.
9. 명령 프롬프트로 돌아가서 **qwinsta** 명령을 실행 합니다.
10. 6 단계에서 설치한 스택 구성 요소가 그 옆에 **수신 대기** 하는지 확인 합니다.
   - 그렇다면 명령 프롬프트에서 **net Start RDAgentBootLoader** 을 입력 하 고 VM을 다시 시작 합니다.
   - 그렇지 않은 경우 [VM을 다시 등록 하 고 에이전트](#your-issue-isnt-listed-here-or-wasnt-resolved) 구성 요소를 다시 설치 해야 합니다.

## <a name="error-connection-not-found-rdagent-does-not-have-an-active-connection-to-the-broker"></a>오류: 연결을 찾을 수 없음: RDAgent에 broker에 대 한 활성 연결이 없습니다.

Vm이 연결 제한에 있을 수 있으므로 VM은 새 연결을 수락할 수 없습니다.

이 문제를 해결하려면:
   - 최대 세션 제한을 줄입니다. 이렇게 하면 리소스가 세션 호스트 간에 균등 하 게 분산 되 고 리소스가 고갈 되지 않습니다.
   - Vm의 리소스 용량을 늘립니다.

## <a name="error-operating-a-pro-vm-or-other-unsupported-os"></a>오류: Pro VM 또는 지원 되지 않는 다른 OS가 작동 합니다.

Side-by-side 스택은 Windows Enterprise 또는 Windows Server Sku 에서만 지원 됩니다. 즉, Pro VM과 같은 운영 체제는 그렇지 않습니다. 엔터프라이즈 또는 서버 SKU가 없는 경우 스택은 VM에 설치 되지만 활성화 되지 않습니다. 그러면 명령줄에서 **qwinsta** 를 실행할 때 표시 되지 않습니다.

이 문제를 해결 하려면 Windows Enterprise 또는 Windows Server 인 VM을 만듭니다.
1. [가상 머신 세부 정보](create-host-pools-azure-marketplace.md#virtual-machine-details) 로 이동 하 고 1-12 단계를 수행 하 여 다음 권장 이미지 중 하나를 설정 합니다.
   - Windows 10 Enterprise 다중 세션 버전 1909
   - Windows 10 Enterprise 다중 세션 버전 1909 + Microsoft 365 앱
   - Windows Server 2019 Datacenter
   - Windows 10 Enterprise 다중 세션 버전 2004
   - Windows 10 Enterprise 다중 세션 버전 2004 + Microsoft 365 앱
2. **검토 및 만들기를** 선택 합니다.

## <a name="error-name_already_registered"></a>오류: NAME_ALREADY_REGISTERED

VM 이름이 이미 등록 되어 있는 것 같습니다.

이 문제를 해결하려면:
1. [호스트 풀에서 세션 호스트 제거](#step-2-remove-the-session-host-from-the-host-pool) 섹션의 단계를 따릅니다.
2. [다른 VM을 만듭니다](expand-existing-host-pool.md#add-virtual-machines-with-the-azure-portal). 이 VM에 대 한 고유한 이름을 선택 해야 합니다.
3. Azure Portal]로 이동 하 https://portal.azure.com) 고 VM이 있는 호스트 풀의 **개요** 페이지를 엽니다. 
4. **세션 호스트** 탭을 열고 모든 세션 호스트가 해당 호스트 풀에 있는지 확인 합니다.
5. 세션 호스트 상태를 **사용할 수 있을** 때까지 5-10 분 동안 기다립니다.

   > [!div class="mx-imgBorder"]
   > ![사용 가능한 세션 호스트의 스크린샷](media/hostpool-portal.png)

## <a name="your-issue-isnt-listed-here-or-wasnt-resolved"></a>문제가 여기에 나열 되지 않았거나 해결 되지 않았습니다.

이 문서에서 문제를 찾을 수 없거나 지침이 도움이 되지 않는 경우 Windows 가상 데스크톱 에이전트를 제거, 다시 설치 및 다시 등록 하는 것이 좋습니다. 이 섹션의 지침에서는 모든 에이전트, 부팅 로더 및 스택 구성 요소를 제거 하 고, 호스트 풀에서 세션 호스트를 제거 하 고, VM에 대 한 새 등록 키를 생성 하 고, 에이전트와 부팅 로더를 다시 설치 하 여 VM을 Windows 가상 데스크톱 서비스에 등록 하는 방법을 보여 줍니다. 다음 시나리오 중 하나 이상이 사용자에 게 적용 되는 경우 다음 지침을 따르세요.
- VM이 **업그레이드** 또는 **사용할 수 없는** 상태입니다.
- 스택 수신기가 작동 하지 않고 Windows 10 1809, 1903 또는 1904에서 실행 중입니다.
- **EXPIRED_REGISTRATION_TOKEN** 오류를 수신 하 고 있습니다.
- 세션 호스트 목록에 Vm이 표시 되지 않는 것을 볼 수 없습니다.
- 서비스 창에 **원격 데스크톱 에이전트 로더가** 표시 되지 않습니다.
- 작업 관리자에 **Rdagentbootloader** 구성 요소가 표시 되지 않습니다.
- 이 문서의 지침에 따라 문제가 해결 되지 않았습니다.

### <a name="step-1-uninstall-all-agent-boot-loader-and-stack-component-programs"></a>1 단계: 모든 에이전트, 부팅 로더 및 스택 구성 요소 프로그램 제거

에이전트, 부팅 로더 및 스택을 다시 설치 하기 전에 VM에서 기존 구성 요소 프로그램을 모두 제거 해야 합니다. 모든 에이전트, 부팅 로더 및 스택 구성 요소 프로그램을 제거 하려면 다음을 수행 합니다.
1. 관리자 권한으로 VM에 로그인 합니다. 
2. **제어판** > **프로그램** > **프로그램 및 기능** 으로 이동합니다.
3. 다음 프로그램을 제거 합니다.
   - 원격 데스크톱 에이전트 부팅 로더
   - 원격 데스크톱 서비스 인프라 에이전트
   - 원격 데스크톱 서비스 인프라 Geneva 에이전트
   - 원격 데스크톱 서비스 SxS 네트워크 스택
   
>[!NOTE]
>이러한 프로그램의 여러 인스턴스가 표시 될 수 있습니다. 이러한 모든 항목을 제거 해야 합니다.

   > [!div class="mx-imgBorder"]
   > ![프로그램 제거 스크린샷](media/uninstall-program.png)

### <a name="step-2-remove-the-session-host-from-the-host-pool"></a>2 단계: 호스트 풀에서 세션 호스트 제거

호스트 풀에서 세션 호스트를 제거 하면 해당 호스트 풀에 세션 호스트가 더 이상 등록 되지 않습니다. 이는 세션 호스트 등록을 다시 설정 하는 역할을 합니다. 호스트 풀에서 세션 호스트를 제거 하려면 다음을 수행 합니다.
1. [Azure Portal](https://portal.azure.com)에서 VM이 있는 호스트 풀의 **개요** 페이지로 이동 합니다. 
2. [ **세션 호스트** ] 탭으로 이동 하 여 해당 호스트 풀의 모든 세션 호스트 목록을 확인 하십시오.
3. 세션 호스트 목록을 확인 하 고 제거 하려는 VM을 선택 합니다.
4. **제거** 를 선택합니다.  

   > [!div class="mx-imgBorder"]
   > ![호스트 풀에서 VM을 제거 하는 스크린샷](media/remove-sh.png)

### <a name="step-3-generate-a-new-registration-key-for-the-vm"></a>3 단계: VM에 대 한 새 등록 키 생성

VM을 호스트 풀 및 서비스에 다시 등록 하는 데 사용 되는 새 등록 키를 생성 해야 합니다. VM에 대 한 새 등록 키를 생성 하려면:
1. [Azure Portal](https://portal.azure.com) 를 열고 편집 하려는 VM의 호스트 풀에 대 한 **개요** 페이지로 이동 합니다.
2. **등록 키** 를 선택 합니다.

   > [!div class="mx-imgBorder"]
   > ![포털의 등록 키 스크린샷](media/reg-key.png)

3. **등록 키** 탭을 열고 **새 키 생성** 을 선택 합니다.
4. 만료 날짜를 입력 한 다음 **확인을** 선택 합니다.  

>[!NOTE]
>만료 날짜는 1 시간 이내이 고, 생성 시간 및 날짜 로부터 27 일 보다 길면 안 됩니다. 만료 날짜를 최대 27 일로 설정 하는 것이 좋습니다.

5. 새로 생성 된 키를 클립보드에 복사 합니다. 나중에이 키가 필요 합니다.

### <a name="step-4-reinstall-the-agent-and-boot-loader"></a>4 단계: 에이전트 및 부팅 로더 다시 설치

최신 버전의 에이전트와 부팅 로더를 다시 설치 하면 side-by-side stack 및 Geneva monitoring agent도 자동으로 설치 됩니다. 에이전트 및 부팅 로더를 다시 설치 하려면:
1. 관리자로 VM에 로그인 하 고 [가상 머신 등록](create-host-pools-powershell.md#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool) 의 지침에 따라 **Windows 가상 데스크톱 에이전트** 및 **Windows 가상 데스크톱 에이전트 부팅 로더** 를 다운로드 합니다.

   > [!div class="mx-imgBorder"]
   > ![에이전트 및 부팅 로더 다운로드 페이지의 스크린샷](media/download-agent.png)

2. 방금 다운로드 한 에이전트 및 부팅 로더 설치 관리자를 마우스 오른쪽 단추로 클릭 합니다.
3. **속성** 을 선택합니다.
4. **차단 해제** 를 선택합니다.
5. **확인** 을 선택합니다.
6. 에이전트 설치 관리자를 실행합니다.
7. 설치 관리자가 등록 토큰을 요청 하는 경우 클립보드의 등록 키를 붙여 넣습니다. 

   > [!div class="mx-imgBorder"]
   > ![붙여넣은 등록 토큰의 스크린샷](media/pasted-agent-token.png)

8. 부팅 로더 설치 관리자를 실행 합니다.
9. VM이 다시 시작됩니다. 
10. [Azure Portal](https://portal.azure.com) 로 이동 하 고 VM이 속한 호스트 풀의 **개요** 페이지를 엽니다.
11. [ **세션 호스트** ] 탭으로 이동 하 여 해당 호스트 풀의 모든 세션 호스트 목록을 확인 하십시오.
12. 이제 상태를 **사용** 하 여 호스트 풀에 등록 된 세션 호스트가 표시 됩니다. 

   > [!div class="mx-imgBorder"]
   > ![사용 가능한 세션 호스트의 스크린샷](media/hostpool-portal.png)

## <a name="next-steps"></a>다음 단계

문제가 계속 되 면 지원 사례를 만들고 발생 한 문제에 대 한 자세한 정보와 해결을 시도 하기 위해 수행한 작업을 포함 합니다. 다음 목록에는 Windows 가상 데스크톱 배포에서 발생 하는 문제를 해결 하는 데 사용할 수 있는 기타 리소스가 포함 되어 있습니다.

- Windows Virtual Desktop 및 에스컬레이션 트랙 문제 해결에 대한 개요는 [문제 해결 개요, 피드백 및 지원](troubleshoot-set-up-overview.md)을 참조하세요.
- Windows 가상 데스크톱 환경에서 호스트 풀을 만드는 동안 발생 하는 문제를 해결 하려면 [환경 및 호스트 풀 만들기](troubleshoot-set-up-issues.md)를 참조 하세요.
- Windows Virtual Desktop에서 VM(가상 머신)을 구성하면서 생기는 문제를 해결하려면 [세션 호스트 가상 머신 구성](troubleshoot-vm-configuration.md)을 참조하세요.
- Windows 가상 데스크톱 클라이언트 연결 문제를 해결 하려면 [Windows 가상 데스크톱 서비스 연결](troubleshoot-service-connection.md)을 참조 하세요.
- 원격 데스크톱 클라이언트와 관련 된 문제를 해결 하려면 [원격 데스크톱 클라이언트 문제 해결](troubleshoot-client.md)을 참조 하세요.
- Windows Virtual Desktop과 함께 PowerShell을 사용할 때 발생하는 문제를 해결하려면 [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md)을 참조하세요.
- 서비스에 대 한 자세한 내용은 [Windows 가상 데스크톱 환경](environment-setup.md)을 참조 하세요.
- 문제 해결 자습서를 진행하려면 [자습서: Resource Manager 템플릿 배포 문제 해결](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)을 참조하세요.
- 감사 작업에 대해 알아보려면 [리소스 관리자로 작업 감사](../azure-resource-manager/management/view-activity-logs.md)를 참조하세요.
- 배포 중 오류를 확인하는 작업에 대해 알아보려면 [배포 작업 보기](../azure-resource-manager/templates/deployment-history.md)를 참조하세요.
