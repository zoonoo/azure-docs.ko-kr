---
title: Windows 가상 데스크톱 RDP Shortpath (미리 보기)
titleSuffix: Azure
description: Windows 가상 데스크톱에 대 한 RDP Shortpath (미리 보기)를 설정 하는 방법입니다.
author: gundarev
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: denisgun
ms.openlocfilehash: 6ffe631dc237e7efaf1d6bfd9ac79ab7431c7371
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/21/2020
ms.locfileid: "95023142"
---
# <a name="windows-virtual-desktop-rdp-shortpath-preview"></a>Windows 가상 데스크톱 RDP Shortpath (미리 보기)

> [!IMPORTANT]
> RDP Shortpath는 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기는 서비스 수준 계약 없이 제공 되며 프로덕션 워크 로드에 사용 하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

RDP Shortpath 원격 데스크톱 클라이언트와 세션 호스트 간에 직접 UDP 기반 전송을 설정 하는 Windows 가상 데스크톱의 기능입니다. RDP는이 전송을 사용 하 여 원격 데스크톱과 RemoteApp을 제공 하 고 더 나은 안정성과 일관성 있는 대기 시간을 제공 합니다.

## <a name="key-benefits"></a>주요 이점

* RDP Shortpath 전송은 매우 효율적인  [URCP (Universal Rate Control Protocol)](https://www.microsoft.com/en-us/research/publication/urcp-universal-rate-control-protocol-for-real-time-communication-applications/)를 기반으로 합니다. URCP는 네트워크 상태에 대 한 활성 모니터링으로 UDP를 개선 하 고 공평 하 고 완전 한 링크 사용률을 제공 합니다. URCP는 원격 데스크톱에서 필요에 따라 낮은 지연 및 손실 수준으로 작동 합니다. URCP는 네트워크 매개 변수를 동적으로 학습 하 고 속도 제어 메커니즘을 제공 하는 프로토콜을 제공 하 여 최상의 성능을 제공 합니다.
* RDP Shortpath는 원격 데스크톱 클라이언트와 세션 호스트 간의 직접 연결을 설정 합니다. 직접 연결은 Windows 가상 데스크톱 게이트웨이에 대 한 종속성을 줄이고, 연결의 안정성을 향상 시키고, 각 사용자 세션에 사용 가능한 대역폭을 증가 시킵니다.
* 추가 릴레이를 제거 하면 대기 시간이 중요 한 응용 프로그램 및 입력 방법으로 사용자 환경을 개선 하는 왕복 시간이 줄어듭니다.
* RDP Shortpath는 DSCP (차등화 서비스 코드 포인트) 표시를 통해 RDP 연결에 대 한 [QoS (서비스 품질)](./rdp-quality-of-service-qos.md) 우선 순위를 구성할 수 있도록 지원 합니다.
* RDP Shortpath transport를 사용 하면 각 세션에 대해 스로틀 률을 지정 하 여 [아웃 바운드 네트워크 트래픽을 제한할](./rdp-bandwidth.md#limit-network-bandwidth-use-with-throttle-rate) 수 있습니다.

## <a name="connection-security"></a>연결 보안

RDP Shortpath는 RDP 다중 전송 기능을 확장 하 고 있습니다. 역방향 연결 전송을 대체 하지는 않지만이를 보완 합니다. 모든 초기 세션 중개는 Windows 가상 데스크톱 인프라를 통해 관리 됩니다.

UDP 포트 3390는 역방향 연결 전송을 통해 인증 된 들어오는 Shortpath 트래픽에만 사용 됩니다. RDP Shortpath listener는 역방향 연결 세션과 일치 하지 않는 한 수신기에 대 한 모든 연결 시도를 무시 합니다.

RDP Shortpath는 세션 호스트의 인증서를 사용 하 여 클라이언트와 세션 호스트 간의 TLS 연결을 사용 합니다. 기본적으로 RDP 암호화에 사용 되는 인증서는 배포 하는 동안 OS에 의해 자체 생성 됩니다. 원할 경우 고객은 엔터프라이즈 인증 기관에서 발급 한 중앙에서 관리 되는 인증서를 배포할 수 있습니다. 인증서 구성에 대 한 자세한 내용은 [Windows Server 설명서](/troubleshoot/windows-server/remote/remote-desktop-listener-certificate-configurations)를 참조 하십시오.

## <a name="rdp-shortpath-connection-sequence"></a>RDP Shortpath 연결 시퀀스

[역방향 연결 전송을](./network-connectivity.md)설치한 후에는 클라이언트 및 세션 호스트가 RDP 연결을 설정 하 고 다중 전송 기능을 협상 합니다. 아래에 설명 된 추가 단계:

1. 세션 호스트는 개인 및 공용 IPv4 및 IPv6 주소 목록을 클라이언트에 보냅니다.
2. 클라이언트는 백그라운드 스레드를 시작 하 여 호스트의 IP 주소 중 하나에 직접 병렬 UDP 기반 전송을 설정 합니다.
3. 클라이언트는 제공 된 IP 주소를 검색 하는 동안 역방향 연결 전송에 대 한 초기 연결 설정을 계속 하 여 사용자 연결 지연 시간을 유지 합니다.
4. 클라이언트에 직접적인 시야를 사용 하 고 방화벽 구성이 올바른 경우 클라이언트는 세션 호스트와의 보안 TLS 연결을 설정 합니다.
5. Shortpath 전송을 설정 하면 RDP는 원격 그래픽, 입력 및 장치 리디렉션을 포함 하 여 모든 동적 가상 채널 (DVCs)을 새 전송으로 이동 합니다.
6. 방화벽이 나 네트워크 토폴로지에서 클라이언트가 직접 UDP 연결을 설정 하지 못하는 경우 RDP는 역방향 연결 전송을 계속 합니다.

아래 다이어그램은 RDP Shortpath 네트워크 연결에 대 한 개략적인 개요를 제공 합니다.

:::image type="content" source="media/rdp-shortpath-connections.svg" alt-text="RDP Shortpath 네트워크 연결 다이어그램" lightbox="media/rdp-shortpath-connections.svg":::

## <a name="requirements"></a>요구 사항

RDP Shortpath을 지원 하려면 Windows 가상 데스크톱 클라이언트에서 세션 호스트에 직접 이동 해야 합니다. 다음 기술 중 하나를 사용 하 여 직접적인 시야를 얻을 수 있습니다.

* [Express 경로 개인 피어 링](../expressroute/expressroute-circuit-peerings.md)
* [사이트 간 VPN (IPsec 기반)](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [지점 및 사이트 간 VPN (IPsec 기반)](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)
* [공용 IP 주소 할당](../virtual-network/virtual-network-public-ip-address.md)

다른 VPN 유형을 사용 하 여 Azure 가상 네트워크에 연결 하는 경우 최상의 결과를 위해 UDP 기반 VPN을 사용 하는 것이 좋습니다. 대부분의 TCP 기반 VPN 솔루션은 UDP를 비롯 한 모든 IP 패킷을 캡슐화 하지만 RDP 성능 저하를 초래 하는 TCP 정체 제어의 상속 된 오버 헤드를 추가 합니다.

직접적인 시야는 방화벽에서 UDP 포트 3390을 차단 하지 않고 클라이언트에서 세션 호스트에 직접 연결할 수 있음을 의미 합니다.

## <a name="enabling-rdp-shortpath-preview"></a>RDP Shortpath 미리 보기 사용

RDP Shortpath의 미리 보기에 참여 하려면 세션 호스트에서 RDP Shortpath 수신기를 사용 하도록 설정 해야 합니다. 사용자 환경에서 사용 되는 여러 세션 호스트에 RDP Shortpath을 사용 하도록 설정할 수 있습니다. 풀의 모든 호스트에서 RDP Shortpath를 사용 하도록 설정 해야 하는 요구 사항은 없습니다.
Shortpath 수신기를 사용 하도록 설정 하려면 다음 레지스트리 값을 구성 해야 합니다.

> [!WARNING]
> 레지스트리 편집기를 사용 하거나 다른 방법을 사용 하 여 레지스트리를 잘못 수정 하면 심각한 문제가 발생할 수 있습니다. 이러한 문제로 인해 운영 체제를 다시 설치 해야 할 수 있습니다. Microsoft에서는 이러한 문제를 해결할 수 있다고 보장하지 않습니다. 따라서 레지스트리를 수정할 때는 주의하세요.

1. 세션 호스트에서 Regedit.exe를 시작 하 고 다음 위치로 이동 합니다.

    ```cmd
    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations
    ```

2. **FUseUdpPortRedirector** 라는 새 **DWORD** 값을 만들고 **1** (10 진수)로 설정 합니다.
3. **UdpPortNumber** 라는 새 **DWORD** 값을 만들고 **3390** (10 진수)로 설정 합니다.
4. 레지스트리 편집기를 종료합니다.
5. 세션 호스트 다시 시작

관리자 권한 PowerShell 창에서 다음 cmdlet을 실행 하 여 이러한 레지스트리 값을 설정할 수도 있습니다.

```powershell
$WinstationsKey = 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations'
New-ItemProperty -Path $WinstationsKey -Name 'fUseUdpPortRedirector' -ErrorAction:SilentlyContinue -PropertyType:dword -Value 1 -Force
New-ItemProperty -Path $WinstationsKey -Name 'UdpPortNumber' -ErrorAction:SilentlyContinue -PropertyType:dword -Value 3390 -Force
```

PowerShell을 사용 하 여 그룹 정책을 구성할 수도 있습니다.

```powershell
# Replace $domainName value with the name of your Active Directory domain
# Replace $policyName value with the name of existing Group Policy Object
$domainName = "contoso.com"
$policyName = "RDP Shortpath Policy"
Set-GPPrefRegistryValue -Key 'HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations' -ValueName 'fUseUdpPortRedirector' -Value 1 -Type:DWord  -Action:Create -Context:Computer -Name $policyName -Domain $domainName
Set-GPPrefRegistryValue -Key 'HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations' -ValueName 'UdpPortNumber' -Value 3390 -Type:DWord  -Action:Create -Context:Computer -Name $policyName -Domain $domainName
```

## <a name="configure-windows-defender-firewall-with-advanced-security"></a>고급 보안이 포함 된 Windows Defender 방화벽 구성

RDP Shortpath 인바운드 네트워크 트래픽을 허용 하려면 그룹 정책 관리 MMC 스냅인에서 고급 보안이 포함 된 Windows Defender 방화벽 노드를 사용 하 여 방화벽 규칙을 만듭니다.

1. [고급 보안이 포함 된 Windows Defender 방화벽](/windows/security/threat-protection/windows-firewall/open-the-group-policy-management-console-to-windows-firewall-with-advanced-security)에 대 한 그룹 정책 관리 콘솔를 엽니다.
2. 탐색 창에서 **인바운드 규칙** 을 선택 합니다.
3. **작업** 을 선택 하 고 **새 규칙** 을 선택 합니다.
4. 새 인바운드 규칙 마법사의 **규칙 유형** 페이지에서 **사용자 지정** 을 선택 하 고 **다음** 을 선택 합니다.
5. **프로그램** 페이지에서 **이 프로그램 경로** 를 선택 하 고 "% SystemRoot% \system32\svchost.exe '을 입력 한 후 **다음** 을 선택 합니다.
6. **프로토콜 및 포트** 페이지에서 UDP 프로토콜 종류를 선택 합니다. **로컬 포트** 에서 "특정 포트"를 선택 하 고 3390을 입력 합니다.
7. **범위** 페이지에서 이 페이지에 입력된 IP 주소에서 들어오고 나가는 네트워크 트래픽에만 규칙이 적용되도록 지정할 수 있습니다. 디자인에 맞게 적절히 구성 하 고 **다음** 을 선택 합니다.
8. **작업** 페이지에서 **연결 허용** 을 선택 하 고 **다음** 을 선택 합니다.
9. **프로필** 페이지에서이 규칙이 적용 되는 네트워크 위치 유형을 선택 하 고 **다음** 을 선택 합니다.
10. **이름** 페이지에서 규칙의 이름 및 설명을 입력 한 다음 **마침** 을 선택 합니다.

새 규칙이 아래 스크린샷에 일치 하는지 확인할 수 있습니다. :::image type="content" source="media/rdp-shortpath-firewall-general-tab.png" alt-text="RDP Shortpath 네트워크 연결에 대 한 방화벽 구성에 대 한 일반 탭의 스크린샷" lightbox="media/rdp-shortpath-firewall-general-tab.png":::

:::image type="content" source="media/rdp-shortpath-firewall-service-settings.png" alt-text="RDP Shortpath 네트워크 연결에 대 한 방화벽 구성에 대 한 프로그램 및 서비스 탭의 스크린샷" lightbox="media/rdp-shortpath-firewall-service-settings.png":::

:::image type="content" source="media/rdp-shortpath-firewall-protocol-and-ports.png" alt-text="RDP Shortpath 네트워크 연결에 대 한 방화벽 구성에 대 한 프로토콜 및 포트 탭의 스크린샷" lightbox="media/rdp-shortpath-firewall-protocol-and-ports.png":::

PowerShell을 사용 하 여 Windows 방화벽을 구성할 수도 있습니다.

```powershell
New-NetFirewallRule -DisplayName 'Remote Desktop - Shortpath (UDP-In)'  -Action Allow -Description 'Inbound rule for the Remote Desktop service to allow RDP traffic. [UDP 3390]' -Group '@FirewallAPI.dll,-28752' -Name 'RemoteDesktop-UserMode-In-Shortpath-UDP'  -PolicyStore PersistentStore -Profile Domain, Private -Service TermService -Protocol udp -LocalPort 3390 -Program '%SystemRoot%\system32\svchost.exe' -Enabled:True
```

### <a name="using-powershell-to-configure-windows-defender-firewall"></a>PowerShell을 사용 하 여 Windows Defender 방화벽 구성

PowerShell을 사용 하 여 그룹 정책을 구성할 수도 있습니다.

```powershell
# Replace $domainName value with the name of your Active Directory domain
# Replace $policyName value with the name of existing Group Policy Object
$domainName = "contoso.com"
$policyName = "RDP Shortpath Policy"
$gpoSession = Open-NetGPO -PolicyStore "$domainName\$policyName"
New-NetFirewallRule -DisplayName 'Remote Desktop - Shortpath (UDP-In)'  -Action Allow -Description 'Inbound rule for the Remote Desktop service to allow RDP traffic. [UDP 3390]' -Group '@FirewallAPI.dll,-28752' -Name 'RemoteDesktop-UserMode-In-Shortpath-UDP'  -PolicyStore PersistentStore -Profile Domain, Private -Service TermService -Protocol udp -LocalPort 3390 -Program '%SystemRoot%\system32\svchost.exe' -Enabled:True
```

## <a name="configuring-azure-network-security-group"></a>Azure 네트워크 보안 그룹 구성

네트워크 보안 경계에서 RDP Shortpath 수신기에 대 한 액세스를 허용 하려면 인바운드 UDP 포트 3390을 허용 하도록 Azure 네트워크 보안 그룹을 구성 해야 합니다.
[네트워크 보안 그룹 설명서](../virtual-machines/windows/nsg-quickstart-portal.md) 에 따라 다음 매개 변수를 사용 하 여 트래픽을 허용 하는 인바운드 보안 규칙을 만듭니다.

* **원본**  -  클라이언트가 있는 **모든** 또는 IP 범위
* **원본 포트 범위** -* *\** _ _ **대상**  -  **Any**
* **대상 포트 범위**  -  **3390**
* **프로토콜**  -  **UDP**
* **작업**  -  **허용**
* 필요에 따라 **우선 순위** 를 변경 합니다. 이 우선 순위는 규칙이 적용되는 순서에 영향을 줍니다. 숫자 값이 적을수록 규칙이 먼저 적용됩니다.
* **이름** -- **RDP Shortpath**

### <a name="disabling-rdp-shortpath-for-a-specific-subnet"></a>특정 서브넷에 대해 RDP Shortpath 사용 안 함

RDP Shortpath 전송을 사용 하 여에서 특정 서브넷을 차단 해야 하는 경우 원본 IP 범위를 지정 하는 추가 네트워크 보안 그룹을 구성할 수 있습니다.

## <a name="verifying-the-connectivity"></a>연결 확인

### <a name="using-connection-information-dialog"></a>연결 정보 대화 상자 사용

연결이 RDP Shortpath를 사용 하 고 있는지 확인 하려면 연결 도구 모음에서 안테나 아이콘을 클릭 하 여 "연결 정보" 대화 상자를 엽니다.

:::image type="content" source="media/rdp-shortpath-connection-bar.png" alt-text="원격 데스크톱 연결 막대 이미지":::

:::image type="content" source="media/rdp-shortpath-connection-info.png" alt-text="원격 데스크톱 연결 정보 대화 상자의 이미지":::

### <a name="using-event-logs"></a>이벤트 로그 사용

세션에서 RDP Shortpath 전송을 사용 중인지 확인 하려면 다음을 수행 합니다.

1. Windows Virtual Desktop 클라이언트를 사용하여 VM의 데스크톱에 연결합니다.
2. 이벤트 뷰어를 시작 하 고 다음 노드로 이동 합니다. **응용 프로그램 및 서비스 로그 > microsoft > Windows > RemoteDesktopServices-RdpCoreCDV > RdpCoreCDV/Operational**
3. RDP Shortpath 전송이 사용 되는지 확인 하려면 이벤트 ID 131를 확인 합니다.

### <a name="using-log-analytics-to-verify-shortpath-connectivity"></a>Log Analytics를 사용 하 여 Shortpath 연결 확인

[Azure Log Analytics](./diagnostics-log-analytics.md)을 사용 하는 경우 [WVDConnections 테이블](/azure/azure-monitor/reference/tables/wvdconnections)을 쿼리하여 연결을 모니터링할 수 있습니다. UdpUse 이라는 열은 Windows 가상 데스크톱 RDP 스택에서 현재 사용자 연결에 UDP 프로토콜을 사용 하는지 여부를 나타냅니다.
가능한 값은 다음과 같습니다.

* **0** -사용자 연결에서 RDP Shortpath를 사용 하지 않음
* **1** -사용자 연결이 RDP Shortpath를 사용 하 고 있습니다.
  
다음 쿼리 목록에서 연결 정보를 검토할 수 있습니다. [Log Analytics 쿼리 편집기](../azure-monitor/log-query/log-analytics-tutorial.md#write-a-query)에서이 쿼리를 실행할 수 있습니다. 각 쿼리에 대해을 `userupn` 조회할 사용자의 UPN으로 바꿉니다.

```kusto
let Events = WVDConnections | where UserName == "userupn" ;
Events
| where State == "Connected"
| project CorrelationId , UserName, ResourceAlias , StartTime=TimeGenerated, UdpUse, SessionHostName, SessionHostSxSStackVersion
| join (Events
| where State == "Completed"
| project EndTime=TimeGenerated, CorrelationId, UdpUse)
on CorrelationId
| project StartTime, Duration = EndTime - StartTime, ResourceAlias, UdpUse,  SessionHostName, SessionHostSxSStackVersion
| sort by StartTime asc
```

## <a name="troubleshooting"></a>문제 해결

### <a name="verify-shortpath-listener"></a>Shortpath 수신기 확인

UDP 수신기를 사용 하도록 설정 되었는지 확인 하려면 세션 호스트에서 다음 PowerShell 명령을 사용 합니다.

```powershell
Get-NetUDPEndpoint -OwningProcess ((Get-WmiObject win32_service -Filter "name = 'TermService'").ProcessId)  -LocalPort 3390
```

사용 하도록 설정 하면 다음과 같은 출력이 표시 됩니다.

```dos
LocalAddress                             LocalPort
------------                             ---------
::                                       3390
0.0.0.0                                  3390
```

충돌이 있는 경우 다음 명령을 사용 하 여 포트를 사용 하는 프로세스를 식별할 수 있습니다.

```powershell
Get-Process -id (Get-NetUDPEndpoint  -LocalPort 3390 -LocalAddress 0.0.0.0).OwningProcess
```

## <a name="disabling-rdp-shortpath"></a>RDP Shortpath 사용 안 함

RDP Shortpath 전송을 사용 하지 않도록 설정 해야 하는 경우도 있습니다. 그룹 정책을 사용 하 여 RDP Shortpath을 사용 하지 않도록 설정할 수 있습니다.

### <a name="disabling-rdp-shortpath-on-the-client"></a>클라이언트에서 RDP Shortpath 사용 안 함

특정 클라이언트에 대해 RDP Shortpath를 사용 하지 않도록 설정 하려면 다음 그룹 정책 사용 하 여 UDP 지원을 사용 하지 않도록 설정 합니다.

1. 클라이언트에서 **gpedit.msc** 를 실행 합니다.
2. **컴퓨터 구성 > 관리 템플릿 > Windows 구성 요소 > 원격 데스크톱 서비스 > 원격 데스크톱 연결 클라이언트** 로 이동 합니다.
3. **"클라이언트에서 UDP 끄기"** 설정을 **사용** 으로 설정 합니다.

### <a name="disabling-rdp-shortpath-on-the-session-host"></a>세션 호스트에서 RDP Shortpath 사용 안 함

특정 세션 호스트에 대해 RDP Shortpath를 사용 하지 않도록 설정 하려면 다음 그룹 정책 사용 하 여 UDP 지원을 사용 하지 않도록 설정 합니다.

1. 세션 호스트에서 **gpedit.msc** 를 실행 합니다.
2. **컴퓨터 구성 > 관리 템플릿 > Windows 구성 요소 > 원격 데스크톱 서비스 >** 원격 데스크톱 연결 > 연결로 이동 합니다.
3. **"RDP 전송 프로토콜 선택"** 설정을 **TCP로만** 설정

## <a name="public-preview-feedback"></a>공개 미리 보기 피드백

이 공개 미리 보기를 사용 하 여 사용자의 경험에 대 한 의견을 듣고 싶습니다.
* 질문, 요청, 의견 및 기타 피드백은 [이 사용자 의견 양식을 사용](https://aka.ms/RDPShortpathFeedback)합니다.

## <a name="next-steps"></a>다음 단계

* Windows 가상 데스크톱 네트워크 연결에 대 한 자세한 내용은 [Windows 가상 데스크톱 네트워크 연결 이해](network-connectivity.md)를 참조 하세요.
* Windows 가상 데스크톱의 QoS (서비스 품질)를 시작 하려면 [Windows 가상 데스크톱에 대 한 qos (서비스 품질) 구현](rdp-quality-of-service-qos.md)을 참조 하세요.