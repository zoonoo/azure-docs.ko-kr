---
title: Azure Firewall을 사용하여 Windows Virtual Desktop 보호
description: Azure Firewall을 사용하여 Windows Virtual Desktop 배포를 보호하는 방법 알아보기
author: vhorne
ms.service: firewall
services: firewall
ms.topic: how-to
ms.date: 05/06/2020
ms.author: victorh
ms.openlocfilehash: 7b9de22a3209a75cec680ae3ea04d2e1f54c956c
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110453270"
---
# <a name="use-azure-firewall-to-protect-window-virtual-desktop-deployments"></a>Azure Firewall을 사용하여 Windows 가상 데스크톱 배포 보호

Windows Virtual Desktop은 Azure에서 실행되는 데스크톱 및 앱 가상화 서비스입니다. 최종 사용자가 Windows Virtual Desktop에 연결하는 경우 세션은 호스트 풀에 의해 실행됩니다. 호스트 풀은 세션 호스트로 Windows Virtual Desktop에 등록한 Azure 가상 머신의 컬렉션입니다. 이러한 가상 머신은 가상 네트워크에서 실행되고 가상 네트워크 보안 제어 적용 대상입니다. 원활한 작동을 위해 Windows Virtual Desktop 서비스에 대한 아웃바운드 인터넷 액세스가 필요하며, 최종 사용자에 대한 아웃바운드 인터넷 액세스가 필요할 수도 있습니다. Azure Firewall을 통해 사용자 환경을 잠그고 아웃바운드 트래픽을 필터링할 수 있습니다.

[ ![Windows Virtual Desktop 아키텍처](media/protect-windows-virtual-desktop/windows-virtual-desktop-architecture-diagram.png) ](media/protect-windows-virtual-desktop/windows-virtual-desktop-architecture-diagram.png#lightbox)

이 문서의 지침에 따라 Azure Firewall을 사용하여 Windows Virtual Desktop 호스트 풀에 대한 추가 보호를 제공하세요.

## <a name="prerequisites"></a>필수 구성 요소


 - 배포된 Windows Virtual Desktop 환경 및 호스트 풀.

   자세한 내용은 [자습서: Azure Marketplace를 사용하여 호스트 풀 만들기](../virtual-desktop/create-host-pools-azure-marketplace.md)와 [Azure Resource Manager 템플릿으로 호스트 풀 만들기](../virtual-desktop/virtual-desktop-fall-2019/create-host-pools-arm-template.md)를 참조하세요.

Windows Virtual Desktop 환경에 대해 자세히 알아보려면 [Windows Virtual Desktop 환경](../virtual-desktop/environment-setup.md)을 참조하세요.

## <a name="host-pool-outbound-access-to-windows-virtual-desktop"></a>Windows Virtual Desktop에 대한 호스트 풀 아웃바운드 액세스

Windows Virtual Desktop에 대해 만든 Azure 가상 머신이 제대로 작동하려면 여러 FQDN(정규화된 도메인 이름)에 액세스할 수 있어야 합니다. Azure Firewall은 이 구성을 간소화하기 위해 Windows Virtual Desktop FQDN 태그를 제공합니다. 다음 단계를 사용하여 아웃바운드 Windows Virtual Desktop 플랫폼 트래픽을 허용합니다.

- Azure Firewall을 배포하고 Azure Firewall을 통해 기본 트래픽(0.0.0.0/0)을 라우팅하도록 Windows Virtual Desktop 호스트 풀 서브넷 UDR(사용자 정의 경로)을 구성합니다. 이제 기본 경로가 방화벽을 가리킵니다.
- 애플리케이션 규칙 컬렉션을 만들고 *WindowsVirtualDesktop* FQDN 태그를 사용하도록 설정하는 규칙을 추가합니다. 원본 IP 주소 범위는 호스트 풀 가상 네트워크이고, 프로토콜은 **https** 이고, 대상은 **WindowsVirtualDesktop** 입니다.

- Windows Virtual Desktop 호스트 풀에 필요한 스토리지 및 서비스 버스 계정 집합은 배포에 따라 다르지만, 아직 WindowsVirtualDesktop FQDN 태그에 캡처되지 않습니다. 다음 방법 중 하나를 사용하여 이 문제를 해결할 수 있습니다.

   - 호스트 풀 서브넷에서 *xt.blob.core.windows.net, *eh.servicebus.windows.net에 대한 https 액세스를 허용합니다. 이러한 와일드카드 FQDN은 필요한 액세스를 지원하지만 덜 제한적입니다.
   - 다음 로그 분석 쿼리를 사용하여 호스트 풀의 WVD를 배포한 후 정확하게 일치하는 필수 FQDN을 나열하고, 방화벽 애플리케이션 규칙에서 명시적으로 허용합니다.
   ```
   AzureDiagnostics
   | where Category == "AzureFirewallApplicationRule"
   | search "Deny"
   | search "gsm*eh.servicebus.windows.net" or "gsm*xt.blob.core.windows.net"
   | parse msg_s with Protocol " request from " SourceIP ":" SourcePort:int " to " FQDN ":" *
   | project TimeGenerated,Protocol,FQDN
   ```

- 네트워크 규칙 컬렉션을 만들고 다음 규칙을 추가합니다.

   - DNS 허용 – TCP 및 UDP 포트 53에 대해 ADDS 개인 IP 주소의 트래픽을 *로 허용합니다.
   - KMS 허용 - Windows Virtual Desktop 가상 머신에서 Windows Activation Service TCP 포트 1688로 트래픽을 허용합니다. 대상 IP 주소에 대한 자세한 내용은 [강제 터널링 시나리오에서 Windows 정품 인증 실패](/troubleshoot/azure/virtual-machines/custom-routes-enable-kms-activation#solution)를 참조하세요.

> [!NOTE]
> 일부 배포의 경우 DNS 규칙이 필요하지 않을 수 있습니다. 예를 들어 Azure Active Directory 도메인 컨트롤러가 168.63.129.16에서 Azure DNS에 DNS 쿼리를 전달할 수 있습니다.

## <a name="host-pool-outbound-access-to-the-internet"></a>인터넷에 대한 호스트 풀 아웃바운드 액세스

조직 요구 사항에 따라 최종 사용자에게 보안 아웃바운드 인터넷 액세스를 사용하도록 설정할 수 있습니다. 허용되는 대상 목록이 잘 정의된 경우(예: [Microsoft 365 액세스](/microsoft-365/enterprise/microsoft-365-ip-web-service)) Azure Firewall 애플리케이션 및 네트워크 규칙을 사용하여 필요한 액세스를 구성할 수 있습니다. 그러면 최상의 성능을 위해 최종 사용자 트래픽을 인터넷으로 직접 라우팅합니다.

기존 온-프레미스 보안 웹 게이트웨이를 사용하여 아웃바운드 사용자 인터넷 트래픽을 필터링하려는 경우 명시적 프록시 구성을 사용하여 Windows Virtual Desktop 호스트 풀에서 실행되는 웹 브라우저 또는 다른 애플리케이션을 구성할 수 있습니다. 예를 들어 [Microsoft Edge 명령줄 옵션을 사용하여 프록시 설정을 구성하는 방법](/deployedge/edge-learnmore-cmdline-options-proxy-settings)을 참조하세요. 이러한 프록시 설정은 최종 사용자 인터넷 액세스에만 영향을 미치며, Azure Firewall을 통해 직접 Windows Virtual Desktop 플랫폼 아웃바운드 트래픽을 허용합니다.

## <a name="additional-considerations"></a>기타 고려 사항

요구 사항에 따라 추가 방화벽 규칙을 구성해야 할 수도 있습니다.

- NTP 서버 액세스

   기본적으로 Windows를 실행 하는 가상 머신은 시간 동기화를 위해 UDP 포트 123을 통해 time.windows.com에 연결합니다. 이 액세스를 허용하는 네트워크 규칙 또는 사용자 환경에서 사용하는 시간 서버를 만듭니다.


## <a name="next-steps"></a>다음 단계

- Windows Virtual Desktop 자세히 알아보기: [Windows Virtual Desktop이란?](../virtual-desktop/overview.md)
