---
title: Azure Active Directory 조건부 액세스의 위치 조건
description: 위치 조건을 사용하여 사용자의 네트워크 위치를 기준으로 클라우드 앱에 대한 액세스를 제어하는 방법을 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 06/15/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.custom: contperfq4
ms.openlocfilehash: 7db7e64840d248b66a61ff310f9441800e1afc31
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85253225"
---
# <a name="using-the-location-condition-in-a-conditional-access-policy"></a>조건부 액세스 정책에서 위치 조건 사용 

[개요 문서](overview.md) 에 설명 된 대로 조건부 액세스 정책은 가장 기본적인 조건에 따라 신호를 결합 하 고 결정을 내리고 조직 정책을 적용 합니다. 의사 결정 프로세스에 통합 될 수 있는 이러한 신호 중 하나는 네트워크 위치입니다.

![개념적 조건부 신호 + 적용 여부 결정](./media/location-condition/conditional-access-signal-decision-enforcement.png)

조직에서는 다음과 같은 일반적인 작업을 위해이 네트워크 위치를 사용할 수 있습니다. 

- 서비스에 액세스하는 사용자가 회사 네트워크 외부에 있는 경우 다단계 인증을 요구합니다.
- 특정 국가 또는 지역에서 서비스에 액세스하는 사용자에 대한 액세스 차단

네트워크 위치는 클라이언트가 Azure Active Directory 하기 위해 제공 하는 공용 IP 주소에 의해 결정 됩니다. 기본적으로 조건부 액세스 정책은 모든 IPv4 및 IPv6 주소에 적용 됩니다. 

> [!TIP]
> IPv6 범위는 **[명명 된 위치 (미리 보기)](#preview-features)** 인터페이스 에서만 지원 됩니다. 

## <a name="named-locations"></a>명명된 위치

위치는 Azure Portal **Azure Active Directory**  >  **보안**  >  **조건부 액세스**  >  **명명 된 위치**에 지정 됩니다. 이러한 명명 된 네트워크 위치에는 조직의 본사 네트워크 범위, VPN 네트워크 범위 또는 차단 하려는 범위와 같은 위치가 포함 될 수 있습니다. 

![Azure Portal의 명명 된 위치](./media/location-condition/new-named-location.png)

위치를 구성 하려면 적어도 **이름** 및 IP 범위를 제공 해야 합니다. 

구성할 수 있는 명명된 위치의 수는 Azure AD에서 관련된 개체의 크기에 따라 제한됩니다. 다음 제한 사항에 따라 위치를 구성할 수 있습니다.

- IPv4 범위가 최대 1200 인 명명 된 위치 하나
- 각각에 한 개의 IP 범위로 할당된 최대 90개의 명명된 위치

> [!TIP]
> IPv6 범위는 **[명명 된 위치 (미리 보기)](#preview-features)** 인터페이스 에서만 지원 됩니다. 

### <a name="trusted-locations"></a>신뢰할 수 있는 위치

네트워크 위치를 만들 때 관리자는 위치를 신뢰할 수 있는 위치로 표시 하는 옵션을 사용할 수 있습니다. 

![Azure Portal의 신뢰할 수 있는 위치](./media/location-condition/new-trusted-location.png)

이 옵션은 예를 들어 신뢰할 수 있는 네트워크 위치의 multi-factor authentication에 등록 해야 하는 조건부 액세스 정책을 지정할 수 있습니다. 또한의 위험 계산을 Azure AD ID 보호 하 여 신뢰할 수 있는 것으로 표시 된 위치에서 들어오는 사용자의 로그인 위험을 낮출 수 있습니다.

### <a name="countries-and-regions"></a>국가 및 지역

일부 조직에서는 전체 국가나 지역 IP 경계를 조건부 액세스 정책의 명명 된 위치로 정의 하도록 선택할 수 있습니다. 사용자가 유효한 사용자를 알고 있을 때 이러한 위치를 사용 하 여 북미와 같은 위치에서 제공 되지 않을 수 있습니다. IP 주소와 국가의 이러한 매핑은 정기적으로 업데이트 됩니다. 

> [!NOTE]
> 국가에는 IPv6 주소 범위, 알려진 IPv4 주소 범위만 포함 되지 않으며 신뢰할 수 있는 것으로 표시할 수 없습니다.

![Azure Portal에서 새 국가 또는 지역 기반 위치를 만듭니다.](./media/location-condition/new-named-location-country-region.png)

#### <a name="include-unknown-areas"></a>알 수 없는 영역 포함

일부 IP 주소는 특정 국가나 지역에 매핑되지 않습니다. 이러한 IP 위치를 캡처하려면 위치를 정의할 때 **알 수 없는 영역 포함** 확인란을 선택 합니다. 이 옵션을 사용하면 이러한 IP 주소를 명명된 위치에 포함할지를 선택할 수 있습니다. 명명된 위치를 사용하는 정책이 알 수 없는 위치에 적용되어야 하는 경우 이 설정을 사용합니다.

### <a name="configure-mfa-trusted-ips"></a>MFA 신뢰할 수 있는 Ip 구성

[다단계 인증 서비스 설정](https://account.activedirectory.windowsazure.com/usermanagement/mfasettings.aspx)에서 조직의 로컬 인트라넷을 나타내는 IP 주소 범위를 구성할 수도 있습니다. 이 기능을 사용하면 최대 50개의 IP 주소 범위를 구성할 수 있습니다. IP 주소 범위는 CIDR 형식입니다. 자세한 내용은 [신뢰할 수 있는 ip](../authentication/howto-mfa-mfasettings.md#trusted-ips)를 참조 하세요.  

신뢰할 수 있는 Ip가 구성 된 경우 위치 조건에 대 한 위치 목록에 **MFA 신뢰할 수 있는 ip** 로 표시 됩니다.

### <a name="skipping-multi-factor-authentication"></a>다단계 인증 건너뛰기

다단계 인증 서비스 설정 페이지에서 **인트라넷의 페더레이션 사용자로부터 발생한 요청인 경우 다단계 인증 건너뛰기**를 선택하여 회사 인트라넷 사용자를 식별할 수 있습니다. 이 설정은 AD FS에서 발급한 내부 회사 네트워크 클레임이 신뢰되며, 사용자를 회사 네트워크에 있는 것으로 식별하는 데 사용됨을 나타냅니다. 자세한 내용은 [조건부 액세스를 사용 하 여 신뢰할 수 있는 ip 기능 사용](../authentication/howto-mfa-mfasettings.md#enable-the-trusted-ips-feature-by-using-conditional-access)을 참조 하세요.

이 옵션을 선택한 후에는이 옵션을 선택한 상태에서 **MFA 신뢰할 수 있는 ip** 를 포함 하는 모든 정책에이 옵션을 적용 합니다.

세션 수명이 긴 모바일 및 데스크톱 응용 프로그램의 경우 조건부 액세스가 정기적으로 다시 평가 됩니다. 기본값은 1시간에 한 번입니다. 내부 회사 네트워크 클레임이 초기 인증 시에만 발급되면 Azure AD에 신뢰할 수 있는 IP 범위 목록이 없을 수 있습니다. 이 경우 사용자가 여전히 회사 네트워크에 있는지를 확인하는 것이 더 어렵습니다.

1. 사용자의 IP 주소가 신뢰할 수 있는 IP 범위 중 하나에 있는지 여부를 확인합니다.
1. 사용자 IP 주소의 처음 3 개 8 진수가 초기 인증 IP 주소의 처음 3 개 옥텟과 일치 하는지 여부를 확인 합니다. 내부 회사 네트워크 클레임이 원래 발급 되었고 사용자 위치의 유효성이 검사 된 경우 IP 주소는 초기 인증과 비교 됩니다.

두 단계가 모두 실패하는 경우 사용자는 더 이상 신뢰할 수 있는 IP에 있다고 간주되지 않습니다.

## <a name="preview-features"></a>미리 보기 기능

일반적으로 사용 가능한 명명 된 위치 기능 외에도 명명 된 위치 (미리 보기)가 있습니다. 현재 명명 된 위치 블레이드의 맨 위에 있는 배너를 사용 하 여 명명 된 위치 미리 보기에 액세스할 수 있습니다.

![명명 된 위치 미리 보기 사용해 보기](./media/location-condition/preview-features.png)

명명 된 위치 미리 보기를 사용 하 여 다음을 수행할 수 있습니다.

- 최대 195 개의 명명 된 위치 구성
- 명명 된 위치 당 최대 2000 IP 범위 구성
- IPv4 주소와 함께 IPv6 주소 구성

또한 잘못 된 구성의 변경을 줄이기 위해 몇 가지 추가 검사를 추가 했습니다.

- 개인 IP 범위는 더 이상 구성할 수 없습니다.
- 범위에 포함 될 수 있는 IP 주소 수는 제한 되어 있습니다. IP 범위를 구성 하는 경우에는/8 보다 큰 CIDR 마스크만 사용할 수 있습니다.

미리 보기에는 이제 두 가지 만들기 옵션이 있습니다. 

- **국가 위치**
- **IP 범위 위치**

> [!NOTE]
> 국가에는 IPv6 주소 범위, 알려진 IPv4 주소 범위만 포함 되지 않으며 신뢰할 수 있는 것으로 표시할 수 없습니다.

![명명 된 위치 미리 보기 인터페이스](./media/location-condition/named-location-preview.png)

## <a name="location-condition-in-policy"></a>정책의 위치 조건

위치 조건을 구성할 때 다음을 구분하기 위해 옵션도 제공됩니다.

- 모든 위치
- 신뢰할 수 있는 모든 위치
- 선택한 위치

### <a name="any-location"></a>모든 위치

기본적으로 **모든 위치**를 선택하면 정책이 모든 IP 주소(인터넷의 모든 주소)에 적용됩니다. 이 설정은 명명된 위치로 구성한 IP 주소로 제한되지 않습니다. **모든 위치**를 선택해도 정책에서 특정 위치를 제외할 수 있습니다. 예를 들어, 신뢰할 수 있는 위치를 제외한 모든 위치에 정책을 적용하여, 회사 네트워크를 제외한 모든 위치로 범위를 설정할 수 있습니다.

### <a name="all-trusted-locations"></a>신뢰할 수 있는 모든 위치

이 옵션은 다음에 적용됩니다.

- 신뢰할 수 있는 위치로 표시된 모든 위치
- **MFA에서 신뢰할 수 있는 IP**(구성된 경우)

### <a name="selected-locations"></a>선택한 위치

이 옵션을 사용하여 하나 이상의 명명된 위치를 선택할 수 있습니다. 이 설정이 적용된 정책의 경우, 선택한 위치에서만 연결해야 합니다. **선택**을 클릭하면 명명된 네트워크 목록을 표시하는 명명된 네트워크 선택 컨트롤이 열립니다. 이 목록은 네트워크 위치가 신뢰할 수 있는 위치로 표시된 경우에도 표시됩니다. 다단계 인증 서비스 설정 페이지에서 구성할 수 있는 IP 설정을 포함하기 위해 **MFA에서 신뢰할 수 있는 IP**라는 명명된 위치가 사용됩니다.

## <a name="ipv6-traffic"></a>IPv6 트래픽

기본적으로 조건부 액세스 정책은 모든 IPv6 트래픽에 적용 됩니다. 명명 된 [위치 미리 보기](#preview-features)를 사용 하 여 조건부 액세스 정책에서 특정 IPv6 주소 범위를 제외할 수 있습니다. 이 옵션은 특정 IPv6 범위에 정책을 적용 하지 않으려는 경우에 유용 합니다. 예를 들어 회사 네트워크에서 사용 하기 위한 정책을 적용 하지 않고 회사 네트워크가 공용 IPv6 범위에서 호스트 되는 경우입니다.  

### <a name="when-will-my-tenant-have-ipv6-traffic"></a>내 테 넌 트에 IPv6 트래픽이 있는 경우는 언제 입니까?

Azure Active Directory (Azure AD)는 현재 i p v 6를 사용 하는 직접 네트워크 연결을 지원 하지 않습니다. 그러나 인증 트래픽이 다른 서비스를 통해 프록시 되는 경우도 있습니다. 이러한 경우 정책 평가 중에 IPv6 주소가 사용 됩니다.

Azure AD로 프록시를 가져오는 대부분의 IPv6 트래픽은 Microsoft Exchange Online에서 제공 됩니다. 사용 가능한 경우 Exchange는 IPv6 연결을 선호 합니다. **따라서 특정 IPv4 범위에 대해 구성 된, Exchange에 대 한 조건부 액세스 정책이 있는 경우에도 조직 IPv6 범위를 추가 했는지 확인 해야 합니다.** IPv6 범위를 포함 하지 않을 경우 다음과 같은 두 가지 경우에 예기치 않은 동작이 발생 합니다.

- 기존 인증을 사용 하 여 Exchange Online에 연결 하기 위해 메일 클라이언트를 사용 하는 경우 Azure AD가 IPv6 주소를 받을 수 있습니다. 초기 인증 요청은 Exchange로 이동한 다음 Azure AD에 프록시 됩니다.
- 브라우저에서 OWA (Outlook 웹 액세스)를 사용 하는 경우 모든 조건부 액세스 정책이 계속 충족 될 수 있는 모든 조건부 액세스 정책이 정기적으로 확인 됩니다. 이 확인은 사용자가 허용 된 IP 주소에서 새 위치로 이동 했을 수 있는 경우를 파악 하는 데 사용 됩니다. 이 경우 IPv6 주소를 사용 하 고 IPv6 주소가 구성 된 범위에 없는 경우 사용자의 세션이 중단 되 고 다시 인증을 위해 Azure AD로 다시 전송 될 수 있습니다. 

명명 된 위치에서 IPv6 범위를 구성 해야 하는 가장 일반적인 이유는 다음과 같습니다. 또한 Azure Vnet를 사용 하는 경우에는 IPv6 주소에서 들어오는 트래픽이 발생 합니다. 조건부 액세스 정책에 의해 차단 된 VNet 트래픽 인 경우 Azure AD 로그인 로그를 확인 합니다. 트래픽을 식별 한 후에는 사용 되는 IPv6 주소를 가져와서 정책에서 제외할 수 있습니다. 

> [!NOTE]
> 단일 주소의 IP CIDR 범위를 지정 하려면/32 비트 마스크를 적용 합니다. IPv6 주소 2607: fb90: b27a: 6f69: f8d5: dea0: fb39 a를 표시 하 고 해당 단일 주소를 범위로 제외 하려는 경우 74a 2607: fb90: b27a: 6f69: f8d5: dea0: fb39 a/32를 사용 합니다.

### <a name="identifying-ipv6-traffic-in-the-azure-ad-sign-in-activity-reports"></a>Azure AD 로그인 활동 보고서에서 IPv6 트래픽 식별

[AZURE AD 로그인 활동 보고서](../reports-monitoring/concept-sign-ins.md)로 이동 하 여 테 넌 트에서 IPv6 트래픽을 검색할 수 있습니다. 활동 보고서를 연 후 "IP 주소" 열을 추가 합니다. 이 열을 통해 IPv6 트래픽을 식별할 수 있습니다.

보고서에서 행을 클릭 한 다음 로그인 활동 세부 정보의 "위치" 탭으로 이동 하 여 클라이언트 IP를 찾을 수도 있습니다. 

## <a name="what-you-should-know"></a>알아야 할 사항

### <a name="when-is-a-location-evaluated"></a>위치가 평가되는 시기

조건부 액세스 정책은 다음 경우에 평가 됩니다.

- 사용자가 웹앱, 모바일 또는 데스크톱 애플리케이션에 처음 로그인합니다.
- 최신 인증을 사용하는 모바일 또는 데스크톱 애플리케이션에서 새로 고침 토큰을 사용하여 새 액세스 토큰을 확보합니다. 기본적으로이 확인은 한 시간에 한 번입니다.

이 확인은 최신 인증을 사용 하는 모바일 및 데스크톱 응용 프로그램의 경우 네트워크 위치를 변경한 후 1 시간 이내에 위치 변경을 검색 합니다. 최신 인증을 사용하지 않는 모바일 및 데스크톱 애플리케이션의 경우 토큰 요청마다 정책이 적용됩니다. 요청 빈도는 애플리케이션에 따라 다를 수 있습니다. 마찬가지로 웹 애플리케이션의 경우 정책은 처음 로그인할 때 적용되며 웹 애플리케이션 세션 수명 기간 동안 유효합니다. 애플리케이션 간에 세션 수명이 달라서 정책 평가 사이의 시간도 달라질 수 있습니다. 애플리케이션이 새 로그인 토큰을 요청할 때마다 정책이 적용됩니다.

기본적으로 Azure AD는 시간 단위로 토큰을 발급합니다. 회사 네트워크 외부로 이동한 후 1시간 이내에 최신 인증을 사용하는 애플리케이션에 대해 정책이 적용됩니다.

### <a name="user-ip-address"></a>사용자 IP 주소

정책 평가에 사용되는 IP 주소는 사용자의 공용 IP 주소입니다. 개인 네트워크에 있는 장치의 경우이 IP 주소는 인트라넷에 있는 사용자 장치의 클라이언트 IP가 아니라 네트워크에서 공용 인터넷에 연결 하는 데 사용 하는 주소입니다.

### <a name="bulk-uploading-and-downloading-of-named-locations"></a>명명된 위치의 대량 업로드 및 다운로드

명명된 위치를 만들거나 업데이트하는 경우 대량 업데이트를 위해 IP 주소가 포함된 CSV 파일을 업로드 또는 다운로드할 수 있습니다. 업로드 하면 목록에 있는 IP 범위를 파일의 해당 범위로 바꿉니다. 파일의 각 행에는 하나의 IP 주소 범위가 CIDR 형식으로 포함되어 있습니다.

### <a name="cloud-proxies-and-vpns"></a>클라우드 프록시 및 VPN

클라우드 호스티드 프록시 또는 VPN 솔루션을 사용하면 정책을 평가하는 동안 Azure AD가 사용하는 IP 주소는 프록시의 IP 주소입니다. 사용자의 공용 IP 주소를 포함하는 XFF(X-Forwarded-For) 헤더는 신뢰할 수 있는 원본에서 가져왔다는 확인이 없어서 IP 주소를 속이는 방법을 제공할 수 있으므로 사용되지 않습니다.

클라우드 프록시가 있는 경우 도메인 가입 디바이스를 요구하는 데 사용되는 정책이나 AD FS의 내부 회사 네트워크 클레임이 사용될 수 있습니다.

### <a name="api-support-and-powershell"></a>API 지원 및 PowerShell

API 및 PowerShell은 명명 된 위치에 대해 아직 지원 되지 않습니다.

## <a name="next-steps"></a>다음 단계

- 조건부 액세스 정책을 구성 하는 방법을 알아보려면 [조건부 액세스 정책 만들기](concept-conditional-access-policies.md)문서를 참조 하세요.
- 위치 조건을 사용 하 여 예제 정책을 찾고 있나요? [조건부 액세스: 위치별 액세스 차단](howto-conditional-access-policy-location.md) 문서를 참조 하세요.
