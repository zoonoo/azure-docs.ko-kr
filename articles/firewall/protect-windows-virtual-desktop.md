---
title: Azure 방화벽을 사용 하 여 Windows 가상 데스크톱 보호
description: Azure 방화벽을 사용 하 여 Windows 가상 데스크톱 배포를 보호 하는 방법을 알아봅니다.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: how-to
ms.date: 05/06/2020
ms.author: victorh
ms.openlocfilehash: ae33d763bda49756e9f90a05feda5089b63ef28b
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2020
ms.locfileid: "91400165"
---
# <a name="use-azure-firewall-to-protect-window-virtual-desktop-deployments"></a>Azure 방화벽을 사용하여 Windows 가상 데스크톱 배포 보호

Windows 가상 데스크톱은 Azure에서 실행 되는 데스크톱 및 앱 가상화 서비스입니다. 최종 사용자가 Windows 가상 데스크톱 환경에 연결 하는 경우 해당 세션은 호스트 풀에 의해 실행 됩니다. 호스트 풀은 Windows 가상 데스크톱에 세션 호스트로 등록 하는 Azure 가상 컴퓨터의 컬렉션입니다. 이러한 가상 머신은 가상 네트워크에서 실행 되 고 가상 네트워크 보안 제어에 적용 됩니다. Windows 가상 데스크톱 서비스에 대 한 아웃 바운드 인터넷 액세스가 필요 하며, 최종 사용자에 대 한 아웃 바운드 인터넷 액세스가 필요할 수도 있습니다. Azure 방화벽을 통해 사용자 환경을 잠그고 아웃 바운드 트래픽을 필터링 할 수 있습니다.

[![Windows 가상 데스크톱 아키텍처 ](media/protect-windows-virtual-desktop/windows-virtual-desktop-architecture-diagram.png)](media/protect-windows-virtual-desktop/windows-virtual-desktop-architecture-diagram.png#lightbox)

이 문서의 지침에 따라 Azure 방화벽을 사용 하 여 Windows 가상 데스크톱 호스트 풀에 대 한 추가 보호를 제공 합니다.

## <a name="prerequisites"></a>사전 요구 사항


 - 배포 된 Windows 가상 데스크톱 환경 및 호스트 풀.

   자세한 내용은 [자습서: Azure Marketplace 사용 하 여 호스트 풀 만들기](../virtual-desktop/create-host-pools-azure-marketplace.md) 및 [Azure Resource Manager 템플릿을 사용 하 여 호스트 풀](../virtual-desktop/virtual-desktop-fall-2019/create-host-pools-arm-template.md)만들기를 참조 하세요.

Windows 가상 데스크톱 환경에 대해 자세히 알아보려면 [Windows 가상 데스크톱 환경](../virtual-desktop/environment-setup.md)을 참조 하세요.

## <a name="host-pool-outbound-access-to-windows-virtual-desktop"></a>Windows 가상 데스크톱에 대 한 호스트 풀 아웃 바운드 액세스

Windows 가상 데스크톱에 대해 만든 Azure virtual machines는 제대로 작동 하기 위해 여러 Fqdn (정규화 된 도메인 이름)에 액세스할 수 있어야 합니다. Azure 방화벽은이 구성을 간소화 하기 위해 Windows 가상 데스크톱 FQDN 태그를 제공 합니다. 다음 단계를 사용 하 여 아웃 바운드 Windows 가상 데스크톱 플랫폼 트래픽을 허용 합니다.

- Azure 방화벽을 배포 하 고 Azure 방화벽을 통해 모든 트래픽을 라우팅하도록 Windows 가상 데스크톱 호스트 풀 UDR (사용자 정의 경로)을 구성 합니다. 이제 기본 경로가 방화벽을 가리킵니다.
- 응용 프로그램 규칙 컬렉션을 만들고 *Windowsvirtualdesktop* FQDN 태그를 사용 하도록 설정 하는 규칙을 추가 합니다. 원본 IP 주소 범위는 호스트 풀 가상 네트워크이 고, 프로토콜은 **https**이 고, 대상은 **windowsvirtualdesktop**입니다.

- Windows 가상 데스크톱 호스트 풀에 필요한 저장소 및 service bus 계정 집합은 배포에 따라 다르지만, 아직 WindowsVirtualDesktop FQDN 태그에 캡처되지 않습니다. 다음 방법 중 하나로 해결할 수 있습니다.

   - 호스트 풀 서브넷에서 * xt.blob.core.windows.net, * eh.servicebus.windows.net 및 * xt.table.core.windows.net로의 https 액세스를 허용 합니다. 이러한 와일드 카드 Fqdn은 필요한 액세스를 가능 하 게 하지만 덜 제한적입니다.
   - 다음 log analytics 쿼리를 사용 하 여 정확한 필수 Fqdn을 나열 하 고 방화벽 응용 프로그램 규칙에서 명시적으로 허용 합니다.
   ```
   AzureDiagnostics
   | where Category == "AzureFirewallApplicationRule"
   | search "Deny"
   | search "gsm*eh.servicebus.windows.net" or "gsm*xt.blob.core.windows.net" or "gsm*xt.table.core.windows.net"
   | parse msg_s with Protocol " request from " SourceIP ":" SourcePort:int " to " FQDN ":" *
   | project TimeGenerated,Protocol,FQDN
   ```

- 네트워크 규칙 컬렉션 만들기 다음 규칙을 추가 합니다.

   - DNS 허용-TCP 및 UDP 포트 53에 대 한 개인 IP 주소를 *에 추가 합니다.
   - KMS 허용-windows 가상 데스크톱 가상 머신에서 Windows 정품 인증 서비스 TCP 포트 1688로의 트래픽을 허용 합니다. 대상 IP 주소에 대 한 자세한 내용은 [강제 터널링 시나리오에서 Windows 정품 인증 실패](../virtual-machines/troubleshooting/custom-routes-enable-kms-activation.md#solution)를 참조 하세요.

> [!NOTE]
> 일부 배포의 경우 DNS 규칙이 필요 하지 않을 수 있습니다. 예를 들어 Azure Active Directory 도메인 컨트롤러가 168.63.129.16에서 Azure DNS에 DNS 쿼리를 전달할 수 있습니다.

## <a name="host-pool-outbound-access-to-the-internet"></a>인터넷에 대 한 호스트 풀 아웃 바운드 액세스

조직 요구 사항에 따라 최종 사용자에 게 보안 아웃 바운드 인터넷 액세스를 사용 하도록 설정할 수 있습니다. 허용 되는 대상 목록이 잘 정의 된 경우 (예: [액세스 Microsoft 365](/microsoft-365/enterprise/microsoft-365-ip-web-service)) Azure 방화벽 응용 프로그램 및 네트워크 규칙을 사용 하 여 필요한 액세스를 구성할 수 있습니다. 그러면 최상의 성능을 위해 최종 사용자 트래픽을 인터넷으로 직접 라우팅합니다.

기존 온-프레미스 보안 웹 게이트웨이를 사용 하 여 아웃 바운드 사용자 인터넷 트래픽을 필터링 하려는 경우 명시적 프록시 구성을 사용 하 여 Windows 가상 데스크톱 호스트 풀에서 실행 되는 웹 브라우저 또는 다른 응용 프로그램을 구성할 수 있습니다. 예를 들어 [Microsoft Edge 명령줄 옵션을 사용 하 여 프록시 설정을 구성 하는 방법](https://docs.microsoft.com/deployedge/edge-learnmore-cmdline-options-proxy-settings)을 참조 하세요. 이러한 프록시 설정은 최종 사용자 인터넷 액세스에만 영향을 주므로 Azure 방화벽을 통해 직접 Windows 가상 데스크톱 플랫폼 아웃 바운드 트래픽을 허용 합니다.

## <a name="additional-considerations"></a>기타 고려 사항

요구 사항에 따라 추가 방화벽 규칙을 구성 해야 할 수도 있습니다.

- NTP 서버 액세스

   기본적으로 Windows를 실행 하는 가상 컴퓨터는 시간 동기화를 위해 UDP 포트 123을 통해 time.windows.com에 연결 합니다. 이 액세스를 허용 하는 네트워크 규칙 또는 사용자 환경에서 사용 하는 시간 서버를 만듭니다.


## <a name="next-steps"></a>다음 단계

- Windows 가상 데스크톱에 대 한 자세한 정보: [Windows 가상 데스크톱 이란?](../virtual-desktop/overview.md)