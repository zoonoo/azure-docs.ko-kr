---
title: Azure Active Directory 조건부 액세스의 위치 조건
description: 위치 조건을 사용하여 사용자의 네트워크 위치를 기준으로 클라우드 앱에 대한 액세스를 제어하는 방법을 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 11/24/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.custom: contperf-fy20q4
ms.openlocfilehash: 777fc60f76692734ea34ff3cdf8f6bc6e5e8316b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97615714"
---
# <a name="using-the-location-condition-in-a-conditional-access-policy"></a>조건부 액세스 정책에서 위치 조건 사용 

[개요 문서](overview.md)에서 설명한 것처럼 조건부 액세스 정책은 가장 기본적으로 의사 결정을 위해 신호를 결합하는 if-then 문을 사용하고 조직 정책을 적용합니다. 의사결정 프로세스에 통합될 수 있는 신호 중 하나는 네트워크 위치이다.

![개념적 조건부 신호 + 적용 여부 결정](./media/location-condition/conditional-access-signal-decision-enforcement.png)

조직에서 다음과 같은 일반적인 작업에 이 네트워크 위치가 사용될 수 있습니다. 

- 서비스에 액세스하는 사용자가 회사 네트워크 외부에 있는 경우 다단계 인증을 요구합니다.
- 특정 국가 또는 지역에서 서비스에 액세스하는 사용자에 대한 액세스 차단

네트워크 위치는 클라이언트가 Azure Active Directory에 제공하는 공용 IP 주소에 의해 결정됩니다. 조건부 액세스 정책은 기본적으로 모든 IPv4 및 IPv6 주소에 적용됩니다. 

> [!TIP]
> IPv6 범위는 **[명명된 위치(미리 보기)](#preview-features)** 인터페이스에서만 지원됩니다. 

## <a name="named-locations"></a>명명된 위치

Azure Portal에서 **Azure Active Directory** > **보안** > **조건부 액세스** > **명명된 위치** 에 지정됩니다. 이러한 명명된 네트워크 위치에는 조직의 본사 네트워크 범위, VPN 네트워크 범위 또는 차단하려는 범위와 같은 위치가 포함될 수 있습니다. 

![Azure Portal의 명명된 위치](./media/location-condition/new-named-location.png)

위치를 구성하려면 최소한 **이름** 및 IP 범위를 제공해야 합니다. 

구성할 수 있는 명명된 위치의 수는 Azure AD에서 관련된 개체의 크기에 따라 제한됩니다. 다음 제한 사항에 따라 위치를 구성할 수 있습니다.

- 최대 1200 IPv4 범위의 명명된 위치.
- 각각에 한 개의 IP 범위로 할당된 최대 90개의 명명된 위치

> [!TIP]
> IPv6 범위는 **[명명된 위치(미리 보기)](#preview-features)** 인터페이스에서만 지원됩니다. 

### <a name="trusted-locations"></a>신뢰할 수 있는 위치

네트워크 위치를 생성할 때 관리자는 위치를 신뢰할 수 있는 위치로 표시할 수 있습니다. 

![Azure Portal의 신뢰할 수 있는 위치](./media/location-condition/new-trusted-location.png)

이 옵션은 신뢰할 수 있는 네트워크 위치에서 다단계 인증을 등록해야 하는 경우 조건부 액세스 정책을 고려할 수 있습니다. 또한 Azure AD Identity Protection의 위험 계산을 고려하여 신뢰할 수 있는 것으로 표시된 위치 들어올 때 사용자의 로그인 위험을 낮출 수 있습니다.

### <a name="countries-and-regions"></a>국가 및 지역

일부 조직에서 조건부 접근 정책의 명명된 위치로 전체 국가나 지역 IP 경계를 정의할 수도 있습니다. 북한과 같은 장소에서 유효한 사용자가 오지 않을 것을 알고 불필요한 트래픽을 차단할 때 이러한 위치를 사용할 수 있습니다. 국가에 대한 이러한 IP 주소 매핑은 주기적으로 업데이트됩니다. 

> [!NOTE]
> IPv6주소 범위는 국가에 매핑될 수 없습니다. IPv4 주소만 국가에 매핑됩니다.

![Azure Portal에서 새 국가 또는 지역 기반 위치 생성](./media/location-condition/new-named-location-country-region.png)

#### <a name="include-unknown-areas"></a>알 수 없는 영역 포함

일부 IP 주소는 모든 IPv6 주소를 포함하여 특정 국가 또는 지역에 매핑되지 않습니다. 이러한 IP 위치를 캡처하려면 위치를 정의할 때 **알 수 없는 영역 포함** 확인란을 선택하세요. 이 옵션을 사용하면 이러한 IP 주소를 명명된 위치에 포함할지를 선택할 수 있습니다. 명명된 위치를 사용하는 정책이 알 수 없는 위치에 적용되어야 하는 경우 이 설정을 사용합니다.

### <a name="configure-mfa-trusted-ips"></a>MFA 신뢰할 수 있는 IP 구성

[다단계 인증 서비스 설정](https://account.activedirectory.windowsazure.com/usermanagement/mfasettings.aspx)에서 조직의 로컬 인트라넷을 나타내는 IP 주소 범위를 구성할 수도 있습니다. 이 기능을 사용하면 최대 50개의 IP 주소 범위를 구성할 수 있습니다. IP 주소 범위는 CIDR 형식입니다. 자세한 내용은 [신뢰할 수 있는 IP](../authentication/howto-mfa-mfasettings.md#trusted-ips)를 참조하세요.  

신뢰할 수 있는 IP가 구성된 경우 위치 조건의 위치 목록에서 **MFA 신뢰할 수 있는 IPS** 로 표시됩니다.

### <a name="skipping-multi-factor-authentication"></a>다단계 인증 건너뛰기

다단계 인증 서비스 설정 페이지에서 **인트라넷의 페더레이션 사용자로부터 발생한 요청인 경우 다단계 인증 건너뛰기** 를 선택하여 회사 인트라넷 사용자를 식별할 수 있습니다. 이 설정은 AD FS에서 발급한 내부 회사 네트워크 클레임이 신뢰되며, 사용자를 회사 네트워크에 있는 것으로 식별하는 데 사용됨을 나타냅니다. 자세한 내용은 [조건부 액세스를 통한 신뢰할 수 있는 IP 기능 사용](../authentication/howto-mfa-mfasettings.md#enable-the-trusted-ips-feature-by-using-conditional-access)을 참조하세요.

지정된 위치를 비롯하여 이 옵션을 선택하면 **MFA Trusted IPS** 가 선택된 옵션을 포함한 모든 정책에 적용됩니다.

세션 수명이 긴 모바일 및 데스크톱 애플리케이션의 경우 조건부 액세스가 주기적으로 재평가됩니다. 기본값은 1시간에 한 번입니다. 내부 회사 네트워크 클레임이 초기 인증 시에만 발급되면 Azure AD에 신뢰할 수 있는 IP 범위 목록이 없을 수 있습니다. 이 경우 사용자가 여전히 회사 네트워크에 있는지를 확인하는 것이 더 어렵습니다.

1. 사용자의 IP 주소가 신뢰할 수 있는 IP 범위 중 하나에 있는지 여부를 확인합니다.
1. 사용자의 IP 주소의 처음 세 바이트수가 초기 인증의 IP 주소의 처음 세 바이트수와 일치하는지 확인합니다. 내부 회사 네트워크 클레임이 처음 발급되고 사용자 위치의 유효성이 검사되었을 때 IP 주소는 초기 인증과 비교됩니다.

두 단계가 모두 실패하는 경우 사용자는 더 이상 신뢰할 수 있는 IP에 있다고 간주되지 않습니다.

## <a name="preview-features"></a>미리 보기 기능

일반적으로 사용 가능한 명명된 위치 기능 외에도 명명된 위치(미리보기)가 있습니다. 현재 명명된 위치 블레이드 상단의 배너를 사용하여 명명된 위치 미리 보기에 액세스할 수 있습니다.

![명명된 위치 미리 보기 시도](./media/location-condition/preview-features.png)

명명된 위치 미리보기를 통해 다음을 수행할 수 있습니다.

- 최대 195개의 명명된 위치 구성
- 명명된 위치당 최대 2000개의 IP 범위 구성
- IPv4 주소와 IPv6 주소 구성

또한 잘못된 구성의 변경을 줄이기 위해 몇 가지 추가 검사가 추가되었습니다.

- 개인 IP 범위를 더 이상 구성할 수 없습니다.
- 범위에 포함될 수 있는 IP 주소 수는 제한적입니다. IP 범위를 구성할 때 /8보다 큰 CIDR 마스크만 허용됩니다.

미리보기에 이제 두 가지 생성 옵션이 있습니다. 

- **국가 위치**
- **IP 범위 위치**

> [!NOTE]
> IPv6주소 범위는 국가에 매핑될 수 없습니다. IPv4 주소만 국가에 매핑됩니다.

![명명된 위치 미리보기 인터페이스](./media/location-condition/named-location-preview.png)

## <a name="location-condition-in-policy"></a>정책의 위치 조건

위치 조건을 구성할 때 다음을 구분하기 위해 옵션도 제공됩니다.

- 모든 위치
- 신뢰할 수 있는 모든 위치
- 선택한 위치

### <a name="any-location"></a>모든 위치

기본적으로 **모든 위치** 를 선택하면 정책이 모든 IP 주소(인터넷의 모든 주소)에 적용됩니다. 이 설정은 명명된 위치로 구성한 IP 주소로 제한되지 않습니다. **모든 위치** 를 선택해도 정책에서 특정 위치를 제외할 수 있습니다. 예를 들어, 신뢰할 수 있는 위치를 제외한 모든 위치에 정책을 적용하여, 회사 네트워크를 제외한 모든 위치로 범위를 설정할 수 있습니다.

### <a name="all-trusted-locations"></a>신뢰할 수 있는 모든 위치

이 옵션은 다음에 적용됩니다.

- 신뢰할 수 있는 위치로 표시된 모든 위치
- **MFA에서 신뢰할 수 있는 IP**(구성된 경우)

### <a name="selected-locations"></a>선택한 위치

이 옵션을 사용하여 하나 이상의 명명된 위치를 선택할 수 있습니다. 이 설정이 적용된 정책의 경우, 선택한 위치에서만 연결해야 합니다. **선택** 을 클릭하면 명명된 네트워크 목록을 표시하는 명명된 네트워크 선택 컨트롤이 열립니다. 이 목록은 네트워크 위치가 신뢰할 수 있는 위치로 표시된 경우에도 표시됩니다. 다단계 인증 서비스 설정 페이지에서 구성할 수 있는 IP 설정을 포함하기 위해 **MFA에서 신뢰할 수 있는 IP** 라는 명명된 위치가 사용됩니다.

## <a name="ipv6-traffic"></a>IPv6 트래픽

기본적으로 조건부 액세스 정책은 모든 IPv6 트래픽에 적용됩니다. [명명된 위치 미리 보기](#preview-features)를 사용하여 특정 IPv6 주소 범위를 조건부 액세스 정책에서 제외할 수 있습니다. 이 옵션은 특정 IPv6 범위에 정책을 적용하지 않으려는 경우 유용합니다. 예를 들어 회사 네트워크에서 사용하기 위한 정책을 적용하지 않으려는 경우 회사 네트워크가 공용 IPv6 범위에서 호스팅됩니다.  

### <a name="when-will-my-tenant-have-ipv6-traffic"></a>언제 테넌트에 IPv6 트래픽이 발생하나요?

Azure Active Directory(Azure AD)는 현재 IPv6을 사용하는 직접 네트워크 연결을 지원하지 않습니다. 그러나 인증 트래픽이 다른 서비스를 통해 프록시되는 경우도 있습니다. 이러한 경우 IPv6 주소는 정책 평가 도중 사용됩니다.

Azure AD로 프록시되는 IPv6 대부분의 트래픽은 Microsoft Exchange Online에서 제공됩니다. 가능한 경우 Exchange는 IPv6 연결을 선호합니다. **따라서 특정 IPv4 범위에 구성된 Exchange에 대한 조건부 액세스 정책이 있는 경우 조직 IPv6 범위도 추가했는지 확인해야 합니다.** IPv6 범위를 포함하지 않는다면 다음 두 가지 경우에 예기치 않은 동작이 발생합니다.

- 기존 인증으로 Exchange Online에 연결하기 위해 메일 클라이언트가 사용되는 경우 Azure AD가 IPv6 주소를 수신할 수 있습니다. 초기 인증 요청은 Exchange로 이동한 다음 Azure AD에 프록시됩니다.
- 브라우저에서 OWA(Outlook 웹 액세스)를 사용하면 모든 조건부 액세스 정책이 계속 충족되는지 주기적으로 확인할 수 있습니다. 이 검사는 사용자가 허용된 IP 주소에서 거리의 커피숍처럼 새 위치로 이동한 경우를 포착하는 데 사용됩니다. 이 경우 IPv6 주소가 사용되고 IPv6 주소가 구성된 범위에 없는 경우 사용자의 세션이 중단되고 인증을 위해 다시 Azure AD로 이동할 수 있습니다. 

이러한 이유로 명명된 위치에서 IPv6 범위를 구성해야 할 수 있습니다. 또한 Azure VNet을 사용하는 경우 IPv6 주소에서 트래픽이 수신됩니다. 조건부 액세스 정책에 의해 VNet 트래픽이 차단된 경우 Azure AD 로그인 로그를 확인하세요. 트래픽을 식별하면 사용 중인 IPv6 주소를 가져와 정책에서 제외할 수 있습니다. 

> [!NOTE]
> 단일 주소에 대한 IPCIDR 범위를 지정하려면 /128비트 마스크를 적용하세요. IPv6 주소 2607:fb90:b27a:6f69:f8d5:dea0:fb39:74a를 표시하고 해당 단일 주소를 범위로 제외하려는 경우 2607:fb90:b27a:6f69:f8d5:dea0:fb39:74a/128을 사용하세요.

### <a name="identifying-ipv6-traffic-in-the-azure-ad-sign-in-activity-reports"></a>Azure AD 로그인 활동 보고서에서 IPv6 트래픽 식별

[Azure AD 로그인 작업 보고서](../reports-monitoring/concept-sign-ins.md)로 이동하여 테넌트에서 IPv6 트래픽을 검색할 수 있습니다. 활동 보고서를 연 후 "IP 주소" 열을 추가하세요. 이 열은 IPv6 트래픽 식별에 사용됩니다.

또한 보고서에서 행을 클릭한 다음 로그인 활동 세부 정보의 "위치" 탭으로 이동하여 클라이언트 IP를 찾을 수 있습니다. 

## <a name="what-you-should-know"></a>알아야 할 사항

### <a name="when-is-a-location-evaluated"></a>위치가 평가되는 시기

조건부 액세스 정책은 다음과 같은 경우에 평가됩니다.

- 사용자가 웹앱, 모바일 또는 데스크톱 애플리케이션에 처음 로그인합니다.
- 최신 인증을 사용하는 모바일 또는 데스크톱 애플리케이션에서 새로 고침 토큰을 사용하여 새 액세스 토큰을 확보합니다. 기본적으로 이 검사는 한 시간에 한 번 수행됩니다.

이 검사는 최신 인증을 사용하는 모바일 및 데스크톱 애플리케이션의 경우 네트워크 위치 변경 후 1시간 이내의 위치 변경 검색을 의미합니다. 최신 인증을 사용하지 않는 모바일 및 데스크톱 애플리케이션의 경우 토큰 요청마다 정책이 적용됩니다. 요청 빈도는 애플리케이션에 따라 다를 수 있습니다. 마찬가지로 웹 애플리케이션의 경우 정책은 처음 로그인할 때 적용되며 웹 애플리케이션 세션 수명 기간 동안 유효합니다. 애플리케이션 간에 세션 수명이 달라서 정책 평가 사이의 시간도 달라질 수 있습니다. 애플리케이션이 새 로그인 토큰을 요청할 때마다 정책이 적용됩니다.

기본적으로 Azure AD는 시간 단위로 토큰을 발급합니다. 회사 네트워크 외부로 이동한 후 1시간 이내에 최신 인증을 사용하는 애플리케이션에 대해 정책이 적용됩니다.

### <a name="user-ip-address"></a>사용자 IP 주소

정책 평가에 사용되는 IP 주소는 사용자의 공용 IP 주소입니다. 개인 네트워크에 있는 장치의 경우 이 IP 주소는 인트라넷에 있는 사용자 장치의 클라이언트 IP가 아니라 공용 인터넷에 연결하기 위해 네트워크에서 사용되는 주소입니다.

### <a name="bulk-uploading-and-downloading-of-named-locations"></a>명명된 위치의 대량 업로드 및 다운로드

명명된 위치를 만들거나 업데이트하는 경우 대량 업데이트를 위해 IP 주소가 포함된 CSV 파일을 업로드 또는 다운로드할 수 있습니다. 업로드하면 목록의 IP 범위가 파일의 해당 범위로 변경됩니다. 파일의 각 행에는 하나의 IP 주소 범위가 CIDR 형식으로 포함되어 있습니다.

### <a name="cloud-proxies-and-vpns"></a>클라우드 프록시 및 VPN

클라우드 호스티드 프록시 또는 VPN 솔루션을 사용하면 정책을 평가하는 동안 Azure AD가 사용하는 IP 주소는 프록시의 IP 주소입니다. 사용자의 공용 IP 주소를 포함하는 XFF(X-Forwarded-For) 헤더는 신뢰할 수 있는 원본에서 가져왔다는 확인이 없어서 IP 주소를 속이는 방법을 제공할 수 있으므로 사용되지 않습니다.

클라우드 프록시가 준비된 경우 하이브리드 Azure AD 조인된 장치가 필요한 정책을 사용하거나 AD FS의 내부 corpnet 클레임을 사용할 수 있습니다.

### <a name="api-support-and-powershell"></a>API 지원 및 PowerShell

명명된 위치에 대한 Graph API의 미리보기 버전을 사용할 수 있습니다. 자세한 내용은 [명명된 위치 API](/graph/api/resources/namedlocation?view=graph-rest-beta)를 참조하세요.

> [!NOTE]
> PowerShell을 사용하여 생성한 명명된 위치는 명명된 위치(미리보기)에만 표시됩니다. 이전 보기에서 명명된 위치를 볼 수 없습니다.  

## <a name="next-steps"></a>다음 단계

- 조건부 액세스 정책 구성 방법을 알아보려면 [조건부 액세스 정책 구축](concept-conditional-access-policies.md) 문서를 참조하세요.
- 위치 조건을 사용하여 예제 정책을 찾고 있나요? [조건부 액세스: 위치별 액세스 차단](howto-conditional-access-policy-location.md) 문서를 참조하세요.
