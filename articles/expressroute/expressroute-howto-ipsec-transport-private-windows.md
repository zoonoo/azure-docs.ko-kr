---
title: 'Windows 호스트에 대한 IPsec 전송 모드 구성 - 프라이빗 피어링: ExpressRoute: Azure | Microsoft Docs'
description: GPO 및 OU를 사용하는 ExpressRoute 프라이빗 피어링을 통해 Azure Windows VM과 온-프레미스 Windows 호스트 간에 IPsec 전송 모드를 사용하도록 설정하는 방법입니다.
services: expressroute
author: fabferri
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/17/2018
ms.author: fabferri
ms.custom: seodec18
ms.openlocfilehash: d728980517988e2dc39be4e4b64d20157a1aef54
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60367274"
---
# <a name="configure-ipsec-transport-mode-for-expressroute-private-peering"></a>ExpressRoute 프라이빗 피어링을 위한 IPsec 전송 모드 구성

이 문서를 통해 Windows를 실행하는 Azure VM과 온-프레미스 Windows 호스트 간의 ExpressRoute 프라이빗 피어링을 통해 전송 모드에서 IPsec 터널을 만들 수 있습니다. 이 문서에서는 그룹 정책 개체를 사용하여 구성을 만드는 단계를 설명합니다. OU(조직 구성 단위) 및 GPO(그룹 정책 개체)를 사용하지 않고 이 구성을 만들 수 있지만, OU 및 GPO 조합은 보안 정책의 제어를 단순화하고 신속하게 규모를 확장할 수 있도록 합니다. 이 문서의 단계에서는 이미 Active Directory 구성이 있으며, OU 및 GPO를 사용하는 데 익숙하다고 가정합니다.

## <a name="about-this-configuration"></a>이 구성에 대한 정보

다음 단계의 구성은 ExpressRoute 프라이빗 피어링에서 단일 Azure VNet(Virtual Network)을 사용합니다. 그러나 이 구성은 더 많은 Azure VNet 및 온-프레미스 네트워크 간에 걸쳐 있을 수 있습니다. 이 문서에서는 IPsec 암호화 정책을 정의하고 이를 Azure VM 그룹과 동일한 OU에 속하는 호스트 온-프레미스에 적용할 수 있도록 합니다. Azure VM(vm1 및 vm2)과 대상 포트 8080이 있는 HTTP 트래픽 전용의 온-프레미스 host1 간에 암호화를 구성합니다. 요구 사항에 따라 다른 유형의 IPsec 정책을 만들 수 있습니다.

### <a name="working-with-ous"></a>OU 사용 

OU와 관련된 보안 정책은 GPO를 통해 컴퓨터로 푸시됩니다. 단일 호스트에 정책을 적용하지 않고 OU를 사용하면 다음과 같은 이점이 있습니다.

* 정책을 OU에 연결하면 동일한 OU에 속하는 컴퓨터에 동일한 정책이 적용됩니다.
* OU와 연결된 보안 정책을 변경하면 변경 내용이 해당 OU의 모든 호스트에 적용됩니다.

### <a name="diagrams"></a>다이어그램

다음 다이어그램은 상호 연결 및 할당된 IP 주소 공간을 보여 줍니다. Azure VM 및 온-프레미스 호스트에서 Windows 2016이 실행되고 있습니다. Azure VM 및 온-프레미스 host1은 동일한 도메인에 속합니다. Azure VM 및 온-프레미스 호스트는 DNS를 사용하여 이름을 제대로 확인할 수 있습니다.

[![1]][1]

이 다이어그램은 ExpressRoute 프라이빗 피어링을 통과하는 IPsec 터널을 보여 줍니다.

[![4]][4]

### <a name="working-with-ipsec-policy"></a>IPsec 정책 사용

Windows에서 암호화는 IPsec 정책과 연관됩니다. IPsec 정책은 보안이 유지되는 IP 패킷과 해당 IP 패킷에 적용되는 보안 메커니즘을 결정합니다.
**IPSec 정책**은 다음과 같은 항목으로 구성됩니다. **필터 목록**, **필터 작업** 및 **보안 규칙**입니다.

IPsec 정책을 구성할 때는 다음 IPsec 정책 용어를 이해하는 것이 중요합니다.

* **IPsec 정책:** 규칙 컬렉션입니다. 한 번에 하나의 정책만 활성 상태(“할당됨”)일 수 있습니다. 각 정책은 하나 이상의 규칙을 가질 수 있으며 모든 규칙을 동시에 활성화할 수 있습니다. 컴퓨터는 한 번에 하나의 활성 IPsec 정책에만 할당될 수 있습니다. 그러나 IPsec 정책 내에서 다양한 상황의 여러 작업을 정의할 수 있습니다. 각 IPsec 규칙 집합은 규칙이 적용되는 네트워크 트래픽 유형에 영향을 주는 필터 목록과 연결됩니다.

* **필터 목록:** 필터 목록은 하나 이상의 필터 번들입니다. 하나의 목록에 여러 필터가 포함될 수 있습니다. 필터는 IP 주소 범위, 프로토콜 또는 특정 프로토콜 포트에 따라 통신이 허용될지, 보안이 유지될지 또는 차단될지를 정의합니다. 각 필터는 특정 조건 집합과 일치합니다. 특정 서브넷에서 특정 대상 포트의 특정 컴퓨터로 전송한 패킷을 예로 들 수 있습니다. 네트워크 조건이 하나 이상의 필터와 일치하면 필터 목록이 활성화됩니다. 각 필터는 특정 필터 목록 안에 정의됩니다. 필터 목록 간에 필터를 공유할 수 없습니다. 그러나 지정된 필터 목록을 여러 IPsec 정책에 통합할 수 있습니다. 

* **필터 작업:** 보안 방법은 IKE 협상 중에 컴퓨터에서 제공하는 보안 알고리즘, 프로토콜 및 키의 세트를 정의합니다. 필터 작업은 보안 방법을 기본 설정 순서대로 나열합니다.  컴퓨터가 IPsec 세션을 협상하는 경우 필터 작업 목록에 저장된 보안 설정에 따라 제안을 수락하거나 전송합니다.

* **보안 규칙:** 규칙은 IPsec 정책이 통신을 보호하는 방법 및 시기를 제어합니다. **필터 목록** 및 **필터 작업**을 사용하여 IPsec 연결을 구축하기 위한 IPsec 규칙을 만듭니다. 각 정책은 하나 이상의 규칙을 가질 수 있으며 모든 규칙을 동시에 활성화할 수 있습니다. 각 규칙에는 IP 필터 목록과 해당 필터 목록과 일치할 경우 수행되는 보안 작업 컬렉션이 포함됩니다.
  * IP 필터 작업
  * 인증 방법
  * IP 터널 설정
  * 연결 형식

[![5]][5]

## <a name="before-you-begin"></a>시작하기 전에

다음 필수 조건을 충족하는지 확인합니다.

* 그룹 정책 설정을 구현하는 데 사용할 수 있는 작동하는 Active Directory 구성이 있어야 합니다. GPO에 대한 자세한 내용은 [그룹 정책 개체](https://msdn.microsoft.com/library/windows/desktop/aa374162(v=vs.85).aspx)를 참조하세요.

* 활성화된 ExpressRoute 회로가 있어야 합니다.
  * ExpressRoute 회로 생성에 대한 내용은 [ExpressRoute 회로 만들기](expressroute-howto-circuit-arm.md)를 참조하세요. 
  * 연결 제공자가 회로를 사용하도록 설정하는지 확인합니다. 
  * 회로에 대해 구성된 Azure 프라이빗 피어링이 있는지 확인합니다. 라우팅 지침에 대한 문서는 [라우팅 구성](expressroute-howto-routing-arm.md) 을 참조하세요. 
  * VNet 및 가상 네트워크 게이트웨이를 만들어서 완전히 프로비전해야 합니다. 지침에 따라 [ExpressRoute에 대한 가상 네트워크 게이트웨이를 만듭니다](expressroute-howto-add-gateway-resource-manager.md). ExpressRoute의 가상 네트워크 게이트웨이는 GatewayType으로 VPN이 아닌 'ExpressRoute'를 사용합니다.

* ExpressRoute 가상 네트워크 게이트웨이가 ExpressRoute 회로에 연결되어 있어야 합니다. 자세한 내용은 [ExpressRoute 회로에 VNet 연결](expressroute-howto-linkvnet-arm.md)을 참조하세요.

* Azure Windows VM이 VNet에 배포되어 있는지 확인합니다.

* 온-프레미스 호스트와 Azure VM 간에 연결이 있는지 확인합니다.

* Azure Windows VM 및 온-프레미스 호스트에서 DNS를 사용하여 이름을 제대로 확인할 수 있는지 검토합니다.

### <a name="workflow"></a>워크플로

1. GPO를 만들고 OU에 연결합니다.
2. IPsec **필터 작업**을 정의합니다.
3. IPsec **필터 목록**을 정의합니다.
4. **보안 규칙**을 사용하여 IPsec 정책을 만듭니다.
5. IPsec GPO를 OU에 할당합니다.

### <a name="example-values"></a>예제 값

* **도메인 이름:** ipsectest.com

* **OU:** IPSecOU

* **온-프레미스 Windows 컴퓨터:** host1

* **Azure Windows VM:** vm1, vm2

## <a name="creategpo"></a>1. GPO 만들기

1. OU에 연결된 새 GPO를 만들려면 그룹 정책 관리 스냅인을 열고 GPO를 연결할 OU를 찾습니다. 이 예제에서 OU의 이름은 **IPSecOU**입니다. 

   [![9]][9]
2. 그룹 정책 관리 스냅인에서 OU를 선택하고 마우스 오른쪽 단추를 클릭합니다. 드롭다운에서 “**이 도메인에서 GPO를 만들어 여기에 연결...** ”을 클릭합니다.

   [![10]][10]
3. 나중에 쉽게 찾을 수 있도록 GPO의 이름을 직관적인 이름으로 지정합니다. **확인**을 클릭하여 GPO를 만들고 연결합니다.

   [![11]][11]

## <a name="enablelink"></a>2. GPO 링크 사용

GPO를 OU에 적용하려면 GPO를 OU에만 연결한 후 반드시 링크를 사용하도록 설정해야 합니다.

1. 만든 GPO를 찾고 마우스 오른쪽 단추를 클릭한 다음, 드롭다운에서 **편집** 을 선택합니다.
2. GPO를 OU에 적용하려면 **연결 사용**을 선택합니다.

   [![12]][12]

## <a name="filteraction"></a>3. IP 필터 작업 정의

1. 드롭 다운에서 **Active Directory의 IP 보안 정책**을 마우스 오른쪽 단추로 클릭한 다음, **IP 필터 목록 및 필터 작업 관리...** 를 클릭합니다.

   [![15]][15]
2. “**필터 작업 관리**” 탭에서 **추가**를 클릭합니다.

   [![16]][16]

3. **IP 보안 필터 작업 마법사**에서 **다음**을 클릭합니다.

   [![17]][17]
4. 필터 작업에 나중에 쉽게 찾을 수 있는 이름을 지정합니다. 이 예제에서 필터 작업 이름은 **myEncryption**입니다. 설명을 추가할 수도 있습니다. 그런 후 **다음**을 클릭합니다.

   [![18]][18]
5. **보안 협상**을 사용하여 다른 컴퓨터와의 IPsec을 설정할 수 없는 경우 수행될 동작을 정의할 수 있습니다. **보안 협상**을 선택하고 **다음**을 클릭합니다.

   [![19]][19]
6. **Communicating with computers that do not support IPsec**(IPsec을 지원하지 않는 컴퓨터와 통신) 페이지에서 **Do not allow unsecured communication**(보안되지 않은 통신 허용 안 함)을 선택하고 **다음**을 클릭합니다.

   [![20]][20]
7. **IP 트래픽 및 보안** 페이지에서 **사용자 지정**을 선택하고 **설정...** 을 클릭합니다.

   [![21]][21]
8. **사용자 지정 보안 방법 설정** 페이지에서 **ESP(데이터 무결성 및 암호화): SHA1, 3DES**를 선택합니다. 그런 다음 **확인**을 클릭합니다.

   [![22]][22]
9. **필터 작업 관리** 페이지에서 **myEncryption** 필터가 추가되었음을 확인할 수 있습니다. **닫기**를 클릭합니다.

   [![23]][23]

## <a name="filterlist1"></a>4. IP 필터 목록 정의

대상 포트 8080을 사용하여 암호화된 HTTP 트래픽을 지정하는 필터 목록을 만듭니다.

1. 암호화해야 하는 트래픽 유형을 규정하려면 **IP 필터 목록**을 사용합니다. **IP 필터 목록 관리** 탭에서 **추가**를 클릭하여 새 IP 필터 목록을 추가합니다.

   [![24]][24]
2. **이름:** 필드에 IP 필터 목록의 이름을 입력합니다. 예: **azure-onpremises-HTTP8080** 그런 다음 **추가**를 클릭합니다.

   [![25]][25]
3. **IP Filter Description and Mirrored property**(IP 필터 설명 및 미러된 속성) 페이지에서 **Mirrored**(미러됨)을 선택합니다. 미러됨 설정은 양방향으로 이동하는 패킷을 일치시키며 양방향 통신을 허용합니다. 그런 후 **Next** 를 클릭합니다.

   [![26]][26]
4. **IP 트래픽 원본** 페이지의 **원본 주소:** 드롭다운에서 **특정 IP 주소 또는 서브넷**을 선택합니다. 

   [![27]][27]
5. IP 트래픽의 원본 주소 **IP 주소 또는 서브넷:** 을 지정하고 **다음**을 클릭합니다.

   [![28]][28]
6. **대상 주소:** IP 주소 또는 서브넷을 지정합니다. 그런 후 **다음**을 클릭합니다.

   [![29]][29]
7. **IP 프로토콜 유형** 페이지에서 **TCP**를 선택합니다. 그런 후 **다음**을 클릭합니다.

   [![30]][30]
8. **IP 프로토콜 포트** 페이지에서 **모든 포트에서** 및 **이 포트로:** 를 선택합니다. 텍스트 상자에 **8080**을 입력합니다. 이러한 설정은 대상 포트 8080의 HTTP 트래픽만 암호화되도록 지정합니다. 그런 후 **다음**을 클릭합니다.

   [![31]][31]
9. IP 필터 목록을 봅니다.  IP 필터 목록 **azure-onpremises-HTTP8080**의 구성은 다음 조건과 일치하는 모든 트래픽의 암호화를 트리거합니다.

   * 10.0.1.0/24(Azure Subnet2)의 원본 주소
   * 10.2.27.0/25(온-프레미스 서브넷)의 대상 주소
   * TCP 프로토콜
   * 대상 포트 8080

   [![32]][32]

## <a name="filterlist2"></a>5. IP 필터 목록 편집

동일한 유형의 트래픽을 반대 방향으로(온-프레미스 호스트에서 Azure VM으로) 암호화하려면 두 번째 IP 필터가 필요합니다. 새 필터를 설정하는 프로세스는 첫 번째 IP 필터를 설정하는 데 사용한 것과 동일한 프로세스입니다. 유일한 차이점은 원본 서브넷 및 대상 서브넷입니다.

1. IP 필터 목록에 새 IP 필터를 추가하려면 **편집**을 선택합니다.

   [![33]][33]
2. **IP 필터 목록** 페이지에서 **추가**를 클릭합니다.

   [![34]][34]
3. 다음 예제의 설정을 사용하여 두 번째 IP 필터를 만듭니다.

   [![35]][35]
4. 두 번째 IP 필터를 만든 후의 IP 필터 목록은 다음과 같습니다.

   [![36]][36]

애플리케이션 보호를 위해 온-프레미스 위치와 Azure 서브넷 간에 암호화가 필요한 경우 기존 IP 필터 목록을 수정하는 대신, 새 IP 필터 목록을 추가할 수 있습니다. 두 IP 필터 목록을 동일한 IPsec 정책에 연결하면 다른 IP 필터 목록에 영향을 주지 않고 특정 IP 필터 목록을 수정하거나 제거할 수 있으므로 유연성이 개선됩니다.

## <a name="ipsecpolicy"></a>6. IPsec 보안 정책 만들기 

보안 규칙을 사용하여 IPsec 정책을 만듭니다.

1. OU에 연결된 **Create an IPsec policy with security ru**(Active Directory의 IPSecurity 정책)을 선택합니다. 마우스 오른쪽 단추를 클릭하고 **IP 보안 정책 만들기**를 선택합니다.

   [![37]][37]
2. 보안 정책 이름을 지정합니다. 예: **policy-azure-onpremises**. 그런 후 **다음**을 클릭합니다.

   [![38]][38]
3. 이 확인란을 선택하지 않고 **다음**을 클릭합니다.

   [![39]][39]
4. **속성 편집** 확인란이 선택되어 있는지 확인하고 **마침**을 클릭합니다.

   [![40]][40]

## <a name="editipsec"></a>7. IPsec 보안 정책 편집

이전에 구성한 **IP 필터 목록** 및 **필터 작업**을 IPsec 정책에 추가합니다.

1. HTTP 정책 속성 **규칙** 탭에서 **추가**를 클릭합니다.

   [![41]][41]
2. Welcome 페이지에서 **다음**을 클릭합니다.

   [![42]][42]
3. 규칙은 IPsec 모드, 즉 터널 모드 또는 전송 모드를 정의하기 위한 옵션을 제공합니다.

   * 터널 모드에서 원래 패킷은 IP 헤더 집합으로 캡슐화됩니다. 터널 모드는 원래 패킷의 IP 헤더를 암호화하여 내부 라우팅 정보를 보호합니다. 터널 모드는 사이트 간 VPN 시나리오의 게이트웨이 간에 널리 구현됩니다. 터널 모드는 대부분의 경우 호스트 간의 엔드투엔드 암호화에 사용됩니다.

   * 전송 모드는 페이로드 및 ESP 트레일러만 암호화하며 원래 패킷의 IP 헤더는 암호화되지 않습니다. 전송 모드에서 패킷의 IP 원본 및 IP 대상은 변경되지 않습니다.

   **이 규칙에서는 터널 지정 안 함**을 선택하고 **다음**을 클릭합니다.

   [![43]][43]
4. **네트워크 유형**은 보안 정책과 연관된 네트워크 연결을 정의합니다. **모든 네트워크 연결**을 선택한 다음, **다음**을 클릭합니다.

   [![44]][44]
5. 이전에 만든 IP 필터 목록인 **azure-onpremises-HTTP8080**을 선택하고 **다음**을 클릭합니다.

   [![45]][45]
6. 이전에 만든 기존 필터 작업인 **myEncryption**을 선택합니다.

   [![46]][46]
7. Windows에서는 4가지 고유 유형의 인증 즉, Kerberos, 인증서, NTLMv2 및 미리 공유한 키를 지원합니다. 도메인에 가입된 호스트로 작업하고 있으므로 **Active Directory 기본값(Kerberos V5 프로토콜)** 을 선택하고 **다음**을 클릭합니다.

   [![47]][47]
8. 새 정책은 보안 규칙 **azure-onpremises-HTTP8080**을 만듭니다. **확인**을 클릭합니다.

   [![48]][48]

IPsec 정책에 따르면 대상 포트 8080의 모든 HTTP 연결이 IPsec 전송 모드를 사용해야 합니다. HTTP는 일반 텍스트 프로토콜이므로 보안 정책을 사용하도록 설정하면 ExpressRoute 프라이빗 피어링을 통해 전송되는 데이터가 암호화됩니다. Active Directory에 대한 IP 보안 정책은 고급 보안이 포함된 Windows 방화벽 구성이 복잡해지는 결과를 가져오지만 IPsec 연결의 추가 사용자 지정을 허용합니다.

## <a name="assigngpo"></a>8. OU에 IPsec GPO 할당

1. 정책을 확인합니다. 보안 그룹 정책이 정의되었지만 아직 할당되지 않았습니다.

   [![49]][49]
2. 보안 그룹 정책을 OU **IPSecOU**에 할당하려면 보안 정책을 마우스 오른쪽 단추로 클릭하고 **할당**을 선택합니다.
   OU에 속하는 모든 컴퓨터에는 보안 그룹 정책이 할당됩니다.

   [![50]][50]

## <a name="checktraffic"></a> 트래픽 암호화 확인

OU에 적용된 암호화 GPO를 확인하려면 모든 Azure VM 및 host1에 IIS를 설치합니다. 모든 IIS는 포트 8080에서 HTTP 요청에 응답하도록 사용자 지정됩니다.
암호화를 확인하려면 OU의 모든 컴퓨터에 네트워크 스니퍼(Wireshark)를 설치할 수 있습니다.
PowerShell 스크립트는 포트 8080에서 HTTP 요청을 생성하기 위한 HTTP 클라이언트로 작동합니다.

```powershell
$url = "http://10.0.1.20:8080"
while ($true) {
try {
[net.httpWebRequest]
$req = [net.webRequest]::create($url)
$req.method = "GET"
$req.ContentType = "application/x-www-form-urlencoded"
$req.TimeOut = 60000

$start = get-date
[net.httpWebResponse] $res = $req.getResponse()
$timetaken = ((get-date) - $start).TotalMilliseconds

Write-Output $res.Content
Write-Output ("{0} {1} {2}" -f (get-date), $res.StatusCode.value__, $timetaken)
$req = $null
$res.Close()
$res = $null
} catch [Exception] {
Write-Output ("{0} {1}" -f (get-date), $_.ToString())
}
$req = $null

# uncomment the line below and change the wait time to add a pause between requests
#Start-Sleep -Seconds 1
}

```
다음 네트워크 캡처는 암호화된 트래픽만 일치시키는 표시 필터 ESP를 사용한 온-프레미스 host1의 결과를 보여 줍니다.

[![51]][51]

온-프레미스에서 powershell 스크립트를 실행하는 경우(HTTP 클라이언트) Azure VM의 네트워크 캡처에 비슷한 추적 결과가 표시됩니다.

## <a name="next-steps"></a>다음 단계

ExpressRoute에 대한 자세한 내용은 [ExpressRoute FAQ](expressroute-faqs.md)를 참조하세요.

<!--Image References-->

[1]: ./media/expressroute-howto-ipsec-transport-private-windows/network-diagram.png "ExpressRoute를 통한 네트워크 다이어그램 IPsec 전송 모드"
[4]: ./media/expressroute-howto-ipsec-transport-private-windows/ipsec-interesting-traffic.png "IPsec 관심 트래픽"
[5]: ./media/expressroute-howto-ipsec-transport-private-windows/windows-ipsec.png "Windows IPsec 정책"
[9]: ./media/expressroute-howto-ipsec-transport-private-windows/ou.png "그룹 정책의 조직 구성 단위"
[10]: ./media/expressroute-howto-ipsec-transport-private-windows/create-gpo-ou.png "OU와 연관된 GPO 만들기"
[11]: ./media/expressroute-howto-ipsec-transport-private-windows/gpo-name.png "OU와 연관된 GPO에 이름 지정"
[12]: ./media/expressroute-howto-ipsec-transport-private-windows/edit-gpo.png "GPO 편집"
[15]: ./media/expressroute-howto-ipsec-transport-private-windows/manage-ip-filter-list-filter-actions.png "IP 필터 목록 및 필터 작업 관리"
[16]: ./media/expressroute-howto-ipsec-transport-private-windows/add-filter-action.png "필터 작업 추가"
[17]: ./media/expressroute-howto-ipsec-transport-private-windows/action-wizard.png "작업 마법사"
[18]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-action-name.png "필터 작업 이름"
[19]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-action.png "필터 작업"
[20]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-action-no-ipsec.png "안전하지 않은 연결이 설정된 경우의 동작 지정"
[21]: ./media/expressroute-howto-ipsec-transport-private-windows/security-method.png "보안 메커니즘"
[22]: ./media/expressroute-howto-ipsec-transport-private-windows/custom-security-method.png "사용자 지정 보안 방법"
[23]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-actions-list.png "필터 작업 목록"
[24]: ./media/expressroute-howto-ipsec-transport-private-windows/add-new-ip-filter.png "새 IP 필터 목록 추가"
[25]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-http-traffic.png "IP 필터에 HTTP 트래픽 추가"
[26]: ./media/expressroute-howto-ipsec-transport-private-windows/match-both-direction.png "양방향으로 패킷 일치"
[27]: ./media/expressroute-howto-ipsec-transport-private-windows/source-address.png "원본 서브넷 선택"
[28]: ./media/expressroute-howto-ipsec-transport-private-windows/source-network.png "원본 네트워크"
[29]: ./media/expressroute-howto-ipsec-transport-private-windows/destination-network.png "대상 네트워크"
[30]: ./media/expressroute-howto-ipsec-transport-private-windows/protocol.png "프로토콜"
[31]: ./media/expressroute-howto-ipsec-transport-private-windows/source-port-and-destination-port.png "원본 포트 및 대상 포트"
[32]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-list.png "필터 목록"
[33]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-for-http.png "HTTP 트래픽을 포함하는 IP 필터 목록"
[34]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-add-second-entry.png "두 번째 IP 필터 추가"
[35]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-second-entry.png "IP 필터 목록 - 두 번째 항목"
[36]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-list-2entries.png "IP 필터 목록 - 두 번째 항목"
[37]: ./media/expressroute-howto-ipsec-transport-private-windows/create-ip-security-policy.png "IP 보안 정책 만들기"
[38]: ./media/expressroute-howto-ipsec-transport-private-windows/ipsec-policy-name.png "IPsec 정책의 이름"
[39]: ./media/expressroute-howto-ipsec-transport-private-windows/security-policy-wizard.png "IPsec 정책 마법사"
[40]: ./media/expressroute-howto-ipsec-transport-private-windows/edit-security-policy.png "IPsec 정책 편집"
[41]: ./media/expressroute-howto-ipsec-transport-private-windows/add-new-rule.png "IPsec 정책에 새 보안 규칙 추가"
[42]: ./media/expressroute-howto-ipsec-transport-private-windows/create-security-rule.png "새 보안 규칙 만들기"
[43]: ./media/expressroute-howto-ipsec-transport-private-windows/transport-mode.png "전송 모드"
[44]: ./media/expressroute-howto-ipsec-transport-private-windows/network-type.png "네트워크 형식"
[45]: ./media/expressroute-howto-ipsec-transport-private-windows/selection-filter-list.png "기존 IP 필터 목록 선택"
[46]: ./media/expressroute-howto-ipsec-transport-private-windows/selection-filter-action.png "기존 필터 작업 선택"
[47]: ./media/expressroute-howto-ipsec-transport-private-windows/authentication-method.png "인증 방법 선택"
[48]: ./media/expressroute-howto-ipsec-transport-private-windows/security-policy-completed.png "보안 정책 생성 프로세스의 끝"
[49]: ./media/expressroute-howto-ipsec-transport-private-windows/gpo-not-assigned.png "GPO에 연결되어 있지만 할당되지 않은 IPsec 정책"
[50]: ./media/expressroute-howto-ipsec-transport-private-windows/gpo-assigned.png "GPO에 할당된 IPsec 정책"
[51]: ./media/expressroute-howto-ipsec-transport-private-windows/encrypted-traffic.png "IPsec 암호화 트래픽 캡처"
