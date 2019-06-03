---
title: Azure Active Directory 조건부 액세스의 위치 조건이란? | Microsoft Docs
description: 위치 조건을 사용하여 사용자의 네트워크 위치를 기준으로 클라우드 앱에 대한 액세스를 제어하는 방법을 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.workload: identity
ms.date: 04/12/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 98588e0c25439fd4988fe39e06e7042cfa9113cb
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66305673"
---
# <a name="what-is-the-location-condition-in-azure-active-directory-conditional-access"></a>Azure Active Directory 조건부 액세스의 위치 조건이란? 

[Azure AD(Azure Active Directory) 조건부 액세스](../active-directory-conditional-access-azure-portal.md)를 사용하여 권한 있는 사용자가 클라우드 앱에 액세스하는 방법을 제어할 수 있습니다. 조건부 액세스 정책의 위치 조건을 사용하여 사용자의 네트워크 위치에 액세스 제어 설정을 연결할 수 있습니다.

이 문서에서는 위치 조건을 구성하는 데 필요한 정보를 제공합니다.

## <a name="locations"></a>위치

Azure AD는 장치에 앱에서 single sign-on을 사용 하도록 설정 하 고 공용 인터넷에서 임의의 위치에서 서비스. 위치 조건을 사용하여 사용자의 네트워크 위치를 기준으로 클라우드 앱에 대한 액세스를 제어할 수 있습니다. 위치 조건에 대한 일반적인 사용 사례는 다음과 같습니다.

- 서비스에 액세스하는 사용자가 회사 네트워크 외부에 있는 경우 다단계 인증을 요구합니다.
- 특정 국가 또는 지역에서 서비스에 액세스하는 사용자에 대한 액세스 차단

위치는 명명 된 위치 또는 multi-factor authentication 신뢰할 수 있는 Ip 나타내는 네트워크 위치에 대 한 레이블입니다.

## <a name="named-locations"></a>명명된 위치

명명 된 위치를 사용 하 여 IP 주소 범위 또는 국가 및 지역의 논리적 그룹을 만들 수 있습니다.

조건부 액세스 페이지의 **관리** 섹션에서 명명된 위치에 액세스할 수 있습니다.

![조건부 액세스의 명명 된 위치](./media/location-condition/02.png)

명명된 위치의 구성 요소는 다음과 같습니다.

![새 명명 위치 만들기](./media/location-condition/42.png)

- **이름** - 명명된 위치의 표시 이름입니다.
- **IP 범위** - CIDR 형식으로 나타낸 하나 이상의 IPv4 주소 범위입니다. IPv6 주소 범위를 지정 하는 것은 지원 되지 않습니다.

   > [!NOTE]
   > IPv6 주소 rangess 명명 된 위치에 현재 포함할 수 없습니다. 조건부 액세스 정책에서이 measn IPv6 범위를 제외할 수 없습니다.

- **신뢰할 수 있는 위치로 표시** - 신뢰할 수 있는 위치를 나타내기 위해 명명된 위치에 대해 설정할 수 있는 플래그입니다. 일반적으로 신뢰할 수 있는 위치는 IT 부서에서 제어하는 네트워크 영역입니다. 신뢰할 수 있는 명명된 위치는 조건부 액세스뿐만 아니라, Azure ID 보호 및 Azure AD 보안 보고서에서 [거짓 긍정](../reports-monitoring/concept-risk-events.md#impossible-travel-to-atypical-locations-1)을 줄이는 데도 사용됩니다.
- **국가/지역** - 이 옵션을 사용하여 명명된 위치를 정의할 하나 이상의 국가를 선택할 수 있습니다.
- **알 수 없는 영역 포함** -일부 IP 주소는 특정 국가 또는 지역에 매핑되지 않습니다. 이 옵션을 사용하면 이러한 IP 주소를 명명된 위치에 포함할지를 선택할 수 있습니다. 명명된 위치를 사용하는 정책이 알 수 없는 위치에 적용되어야 하는 경우 이 설정을 사용합니다.

구성할 수 있는 명명된 위치의 수는 Azure AD에서 관련된 개체의 크기에 따라 제한됩니다. 조직에서는 최대 90 명명 된 위치를 구성할 수 있습니다, 그리고 각 최대 1,200 개 IP 범위로 구성 됩니다.

IPv4 및 IPv6 트래픽에 조건부 액세스 정책이 적용 됩니다. 현재 명명 된 위치에서 구성 하는 IPv6 범위를 허용 하지 않습니다. 이 제한은 다음과 같은 상황을 발생 시킵니다.

- IPv6 범위를 특정 조건부 액세스 정책의 대상이 될 수 없습니다.
- 조건부 액세스 정책에서 특정 IPV6 범위를 제외할 수 없습니다.

"위치"에 적용할 정책이 구성 된, IPv4 및 IPv6 트래픽에 적용 됩니다. 지정 된 국가 및 지역에 대해 구성 된 명명 된 위치는 IPv4 주소만 지원 합니다. IPv6 트래픽은 "알 수 없는 영역 포함" 옵션을 선택 하는 경우를 포함 합니다.

## <a name="trusted-ips"></a>신뢰할 수 있는 IP

[다단계 인증 서비스 설정](https://account.activedirectory.windowsazure.com/usermanagement/mfasettings.aspx)에서 조직의 로컬 인트라넷을 나타내는 IP 주소 범위를 구성할 수도 있습니다. 이 기능을 사용하면 최대 50개의 IP 주소 범위를 구성할 수 있습니다. IP 주소 범위는 CIDR 형식입니다. 자세한 내용은 [신뢰할 수 있는 Ip](../authentication/howto-mfa-mfasettings.md#trusted-ips)입니다.  

신뢰할 수 있는 Ip 구성에 있는 경우로 표시 됩니다 **MFA에서 신뢰할 수 있는 IP** 위치 조건에 대 한 위치 목록에 있습니다.

### <a name="skipping-multi-factor-authentication"></a>다단계 인증 건너뛰기

다단계 인증 서비스 설정 페이지에서 **인트라넷의 페더레이션 사용자로부터 발생한 요청인 경우 다단계 인증 건너뛰기**를 선택하여 회사 인트라넷 사용자를 식별할 수 있습니다. 이 설정은 AD FS에서 발급한 내부 회사 네트워크 클레임이 신뢰되며, 사용자를 회사 네트워크에 있는 것으로 식별하는 데 사용됨을 나타냅니다. 자세한 내용은 [조건부 액세스를 사용하여 신뢰할 수 있는 IP 기능을 사용하도록 설정](../authentication/howto-mfa-mfasettings.md#enable-the-trusted-ips-feature-by-using-conditional-access)을 참조하세요.

이 옵션을 확인 한 후 명명된 된 위치를 포함 하 여 **MFA에서 신뢰할 수 있는 IP** 이 옵션이 선택 된 모든 정책에 적용 됩니다.

세션 수명 수명이 있는, 하는 모바일 및 데스크톱 응용 프로그램에 대 한 조건부 액세스는 주기적으로 다시 평가 합니다. 기본값은 1시간에 한 번입니다. 내부 회사 네트워크 클레임이 초기 인증 시에만 발급되면 Azure AD에 신뢰할 수 있는 IP 범위 목록이 없을 수 있습니다. 이 경우 사용자가 여전히 회사 네트워크에 있는지를 확인하는 것이 더 어렵습니다.

1. 사용자의 IP 주소가 신뢰할 수 있는 IP 범위 중 하나에 있는지 여부를 확인합니다.
2. 사용자의 IP 주소의 처음 3 개 8 진수는 초기 인증 IP 주소의 처음 3 개 8 진수를 일치 하는지 여부를 확인 합니다. IP 주소는 초기 비교할 인증과 내부 회사 네트워크 클레임이 처음에 발급 되었고 사용자 위치 유효성을 검사 합니다.

두 단계가 모두 실패하는 경우 사용자는 더 이상 신뢰할 수 있는 IP에 있다고 간주되지 않습니다.

## <a name="location-condition-configuration"></a>위치 조건 구성

위치 조건을 구성할 때 다음을 구분하기 위해 옵션도 제공됩니다.

- 임의의 위치
- 신뢰할 수 있는 모든 위치
- 선택한 위치

![위치 조건 구성](./media/location-condition/01.png)

### <a name="any-location"></a>임의의 위치

기본적으로 **모든 위치**를 선택하면 정책이 모든 IP 주소(인터넷의 모든 주소)에 적용됩니다. 이 설정은 명명된 위치로 구성한 IP 주소로 제한되지 않습니다. **모든 위치**를 선택해도 정책에서 특정 위치를 제외할 수 있습니다. 예를 들어, 신뢰할 수 있는 위치를 제외한 모든 위치에 정책을 적용하여, 회사 네트워크를 제외한 모든 위치로 범위를 설정할 수 있습니다.

### <a name="all-trusted-locations"></a>신뢰할 수 있는 모든 위치

이 옵션은 다음에 적용됩니다.

- 신뢰할 수 있는 위치로 표시된 모든 위치
- **MFA에서 신뢰할 수 있는 IP**(구성된 경우)

### <a name="selected-locations"></a>선택한 위치

이 옵션을 사용하여 하나 이상의 명명된 위치를 선택할 수 있습니다. 이 설정이 적용된 정책의 경우, 선택한 위치에서만 연결해야 합니다. **선택**을 클릭하면 명명된 네트워크 목록을 표시하는 명명된 네트워크 선택 컨트롤이 열립니다. 이 목록은 네트워크 위치가 신뢰할 수 있는 위치로 표시된 경우에도 표시됩니다. 다단계 인증 서비스 설정 페이지에서 구성할 수 있는 IP 설정을 포함하기 위해 **MFA에서 신뢰할 수 있는 IP**라는 명명된 위치가 사용됩니다.

## <a name="what-you-should-know"></a>알아야 할 사항

### <a name="when-is-a-location-evaluated"></a>위치가 평가되는 시기

조건부 액세스 정책은 다음 경우에 평가됩니다.

- 사용자가 웹앱, 모바일 또는 데스크톱 애플리케이션에 처음 로그인합니다.
- 최신 인증을 사용하는 모바일 또는 데스크톱 애플리케이션에서 새로 고침 토큰을 사용하여 새 액세스 토큰을 확보합니다. 기본적으로이 확인은 한 번는 시간입니다.

최신 인증을 사용 하 여 데스크톱 응용 프로그램에 위치 변경이 감지 됩니다 네트워크 위치를 변경 하는 1 시간 이내 이며이 검사 mobile에 대 한 의미 합니다. 최신 인증을 사용하지 않는 모바일 및 데스크톱 애플리케이션의 경우 토큰 요청마다 정책이 적용됩니다. 요청 빈도는 애플리케이션에 따라 다를 수 있습니다. 마찬가지로 웹 애플리케이션의 경우 정책은 처음 로그인할 때 적용되며 웹 애플리케이션 세션 수명 기간 동안 유효합니다. 애플리케이션 간에 세션 수명이 달라서 정책 평가 사이의 시간도 달라질 수 있습니다. 애플리케이션이 새 로그인 토큰을 요청할 때마다 정책이 적용됩니다.

기본적으로 Azure AD는 시간 단위로 토큰을 발급합니다. 회사 네트워크 외부로 이동한 후 1시간 이내에 최신 인증을 사용하는 애플리케이션에 대해 정책이 적용됩니다.

### <a name="user-ip-address"></a>사용자 IP 주소

정책 평가에 사용되는 IP 주소는 사용자의 공용 IP 주소입니다. 개인 네트워크의 장치에 대 한이 IP 주소 아니며 사용자의 장치는 인트라넷에서 클라이언트 IP, 공용 인터넷에 연결 하는 네트워크에서 사용 하는 주소입니다.

> [!WARNING]
> 장치에 IPv6 주소만 있으면 위치 조건을 구성 지원 되지 않습니다.

### <a name="bulk-uploading-and-downloading-of-named-locations"></a>명명된 위치의 대량 업로드 및 다운로드

명명된 위치를 만들거나 업데이트하는 경우 대량 업데이트를 위해 IP 주소가 포함된 CSV 파일을 업로드 또는 다운로드할 수 있습니다. 업로드는 목록의 IP 범위를 파일의 IP 범위와 바꿉니다. 파일의 각 행에는 하나의 IP 주소 범위가 CIDR 형식으로 포함되어 있습니다.

### <a name="cloud-proxies-and-vpns"></a>클라우드 프록시 및 VPN

클라우드 호스티드 프록시 또는 VPN 솔루션을 사용하면 정책을 평가하는 동안 Azure AD가 사용하는 IP 주소는 프록시의 IP 주소입니다. 사용자의 공용 IP 주소를 포함하는 XFF(X-Forwarded-For) 헤더는 신뢰할 수 있는 원본에서 가져왔다는 확인이 없어서 IP 주소를 속이는 방법을 제공할 수 있으므로 사용되지 않습니다.

클라우드 프록시가 있는 경우 도메인 가입 디바이스를 요구하는 데 사용되는 정책이나 AD FS의 내부 회사 네트워크 클레임이 사용될 수 있습니다.

### <a name="api-support-and-powershell"></a>API 지원 및 PowerShell

API 및 PowerShell은 명명된 위치 또는 조건부 액세스 정책에 대해 아직 지원되지 않습니다.

## <a name="next-steps"></a>다음 단계

- 조건부 액세스 정책을 구성하는 방법을 알아보려면 [Azure Active Directory 조건부 액세스를 사용하는 특정 앱에 MFA 요구](app-based-mfa.md)를 참조하세요.
- 사용자 환경에 대한 조건부 액세스 정책을 구성할 준비가 완료된 경우 [Azure Active Directory의 조건부 액세스 모범 사례](best-practices.md)를 참조하세요.
