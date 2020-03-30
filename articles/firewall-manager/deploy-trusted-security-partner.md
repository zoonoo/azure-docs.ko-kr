---
title: Azure 방화벽 관리자 신뢰할 수 있는 보안 파트너 배포
description: Azure 포털을 사용하여 Azure 방화벽 관리자가 신뢰할 수 있는 보안을 배포하는 방법을 알아봅니다.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: victorh
ms.openlocfilehash: bcea9a8674e4b1979698b7d28eb4192172b0dc11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73931315"
---
# <a name="deploy-a-trusted-security-partner-preview"></a>신뢰할 수 있는 보안 파트너 배포(미리 보기)

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Azure 방화벽 관리자의 *신뢰할 수 있는 보안 파트너를* 사용하면 친숙한 동종 최고의 SECaaS(서비스형 보안) 오퍼링을 사용하여 사용자의 인터넷 액세스를 보호할 수 있습니다.

지원되는 시나리오 및 모범 사례 지침에 대한 자세한 내용은 [신뢰할 수 있는 보안 파트너(미리 보기)를 참조하십시오.](trusted-security-partners.md)

지원되는 보안 파트너는 이 미리 보기에 대한 **ZScaler** 및 **iboss입니다.** 지원되는 지역은 서중부, 북중부, 서부, 웨스트US2 및 이스트US입니다.

## <a name="prerequisites"></a>사전 요구 사항

> [!IMPORTANT]
> `Register-AzProviderFeature` PowerShell 명령을 사용하여 Azure Firewall Manager Preview를 명시적으로 사용하도록 설정해야 합니다.

PowerShell 명령 프롬프트에서 다음 명령을 실행합니다.

```azure-powershell
connect-azaccount
Register-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network
```
기능 등록이 완료될 때까지 최대 30분이 걸립니다. 다음 명령을 실행하여 등록 상태를 확인합니다.

`Get-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network`

## <a name="deploy-a-third-party-security-provider-in-a-new-hub"></a>새 허브에 타사 보안 공급자 배포

1. [https://portal.azure.com](https://portal.azure.com) 에서 Azure Portal에 로그인합니다.
2. **검색에서** **방화벽 관리자를** 입력하고 **서비스에서**선택합니다.
3. **시작으로**이동합니다. **보안 가상 허브 만들기를 선택합니다.** 
4. 구독 및 리소스 그룹을 입력하고 지원되는 지역을 선택하고 허브 및 가상 WAN 정보를 추가합니다. 
5. **VPN 게이트웨이 배포는** 기본적으로 활성화되어 있습니다. 허브에 신뢰할 수 있는 보안 파트너를 배포하려면 VPN 게이트웨이가 필요합니다. 
6. **다음 선택: Azure 방화벽**
   > [!NOTE]
   > 신뢰할 수 있는 보안 파트너는 VPN 게이트웨이 터널을 사용하여 허브에 연결합니다. VPN 게이트웨이를 삭제하면 신뢰할 수 있는 보안 파트너에 대한 연결이 손실됩니다.
7. Azure 방화벽을 배포하여 타사 서비스 공급자와 함께 개인 트래픽을 필터링하여 인터넷 트래픽을 필터링하려면 Azure 방화벽에 대한 정책을 선택합니다. [지원되는 시나리오를](trusted-security-partners.md#key-scenarios)참조하십시오.
8. 허브에 타사 보안 공급자만 배포하려면 **Azure 방화벽: 사용 안 함/사용 안 함에서** 설정 하지 **않도록**설정 합니다. 
9. **다음 을 선택합니다: 신뢰할 수 있는 보안 파트너**.
10. **신뢰할 수 있는 보안 파트너를** 선택하여 사용 **설정으로**설정합니다. 파트너를 선택합니다. 
11. **다음**을 선택합니다. 
12. 컨텐더를 검토한 다음 **을**선택합니다.

VPN 게이트웨이 배포는 30분 이상 걸릴 수 있습니다.

허브가 만들어졌는지 확인하려면 Azure 방화벽 관리자 >보안 허브로 이동합니다. 허브 >개요 페이지를 선택하여 파트너 이름과 상태를 **보안 연결 보류 중으로**표시합니다.

허브가 만들어지고 보안 파트너가 설정되면 보안 공급자를 허브에 계속 연결합니다.

## <a name="deploy-a-third-party-security-provider-in-an-existing-hub"></a>기존 허브에 타사 보안 공급자 배포

가상 WAN에서 기존 허브를 선택하고 이를 보안 *가상 허브로*변환할 수도 있습니다.

1. **시작하기에서**기존 **허브 변환을 선택합니다.**
2. 구독 및 기존 허브를 선택합니다. 나머지 단계에 따라 타사 공급자를 새 허브에 배포합니다.

타사 공급자를 사용하여 기존 허브를 보안 허브로 변환하려면 VPN 게이트웨이를 배포해야 합니다.

## <a name="configure-third-party-security-providers-to-connect-to-a-secured-hub"></a>보안 허브에 연결하도록 타사 보안 공급자 구성

가상 허브의 VPN 게이트웨이에 터널을 설정하려면 타사 공급자가 허브에 대한 액세스 권한이 필요합니다. 이렇게 하려면 서비스 주체를 구독 또는 리소스 그룹에 연결하고 액세스 권한을 부여합니다. 그런 다음 포털을 사용하여 타사에 이러한 자격 증명을 제공해야 합니다.

1. AD(Azure Active Directory) 서비스 주체 만들기: 리디렉션 URL을 건너뛸 수 있습니다. 

   [방법: 포털을 사용하여 리소스에 액세스할 수 있는 Azure AD 애플리케이션 및 서비스 주체 만들기](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)
2. 서비스 주체에 대한 액세스 권한 및 범위를 추가합니다.
   [방법: 포털을 사용하여 리소스에 액세스할 수 있는 Azure AD 애플리케이션 및 서비스 주체 만들기](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)

   > [!NOTE]
   > 보다 세부적인 제어를 위해 리소스 그룹에만 대한 액세스를 제한할 수 있습니다.
3. [ZScaler: Microsoft Azure 가상 WAN 통합](https://help.zscaler.com/zia/configuring-microsoft-azure-virtual-wan-integration) 지침을 구성하여 다음을 수행합니다.

   - 파트너 포털에 로그인하고 자격 증명을 추가하여 신뢰할 수 있는 파트너가 보안 허브에 액세스할 수 있도록 합니다.
   - 파트너 포털에서 가상 허브를 동기화하고 터널을 가상 허브에 설정합니다. Azure AD 인증 자격 증명의 유효성이 검사되면 이 작업을 수행할 수 있습니다.
   
4. Azure의 Azure 가상 WAN 포털에서 터널 생성 상태를 확인할 수 있습니다. 터널이 Azure 및 파트너 포털에 모두 **연결되어** 있는 것으로 나타나면 다음 단계를 계속하여 파트너를 위해 인터넷 트래픽을 보낼 분기와 VNet을 선택하는 경로를 설정합니다.

## <a name="configure-route-settings"></a>경로 설정 구성

1. 보안 허브를 > Azure 방화벽 관리자로 이동합니다. 
2. 허브를 선택합니다. 이제 보안 연결 **보류 중**대신 **프로비저닝된** 상태로 Hub 상태가 표시됩니다.

   타사 공급자가 허브에 연결할 수 있는지 확인합니다. VPN 게이트웨이의 터널은 **연결된** 상태여야 합니다. 이 상태는 이전 상태에 비해 허브와 타사 파트너 간의 연결 상태를 더 많이 반영합니다.
3. 허브를 선택하고 경로 **설정으로**이동합니다.

   타사 공급자를 허브에 배포하면 허브를 *보안 가상 허브로*변환합니다. 이렇게 하면 타사 공급자가 허브에 0.0.0.0/0(기본) 경로를 광고할 수 있습니다. 그러나 허브에 연결된 VNet 연결 및 사이트는 이 기본 경로를 받아야 하는 연결을 옵트인하지 않는 한 이 경로를 얻지 못합니다.
4. **인터넷 트래픽에서** **VNet-to-Internet** 또는 **분기-인터넷** 또는 둘 다 를 선택하여 경로가 타사를 통해 전송하도록 구성됩니다.

   이는 허브로 라우팅해야 하는 트래픽 유형을 나타내지만 아직 VNet 또는 분기의 경로에는 영향을 미치지 않습니다. 이러한 경로는 기본적으로 허브에 연결된 모든 VNet/브랜치에 전파되지 않습니다.
5. **보안 연결을** 선택하고 이러한 경로를 설정해야 하는 연결을 선택해야 합니다. 이는 타사 공급자에게 인터넷 트래픽을 전송하기 시작할 수 있는 VNet/분기를 나타냅니다.
6. **경로 설정에서**인터넷 트래픽에서 **보안 연결을** 선택한 다음 보안할 VNet 또는 분기(가상 WAN의*사이트)를* 선택합니다. **인터넷 트래픽 보안을 선택합니다.**
   ![안전한 인터넷 트래픽](media/deploy-trusted-security-partner/secure-internet-traffic.png)
7. 허브 페이지로 돌아갑니다. 이제 허브의 **신뢰할 수 있는 보안 파트너** 상태가 **보호됩니다.**

## <a name="branch-or-vnet-internet-traffic-via-third-party-service"></a>타사 서비스를 통한 지점 또는 VNet 인터넷 트래픽

그런 다음 VNet 가상 시스템 또는 분기 사이트가 인터넷에 액세스할 수 있는지 확인하고 트래픽이 타사 서비스로 흐르는지 확인할 수 있습니다.

경로 설정 단계를 완료하면 VNet 가상 컴퓨터와 분기 사이트가 0/0을 타사 서비스 경로로 전송합니다. 이러한 가상 시스템에 는 RDP 또는 SSH를 사용할 수 없습니다. 로그인하려면 [피어있는](../bastion/bastion-overview.md) VNet에 Azure 요새 서비스를 배포할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [자습서: Azure 포털을 사용하여 Azure 방화벽 관리자 미리 보기로 클라우드 네트워크 보호](secure-cloud-network.md)




