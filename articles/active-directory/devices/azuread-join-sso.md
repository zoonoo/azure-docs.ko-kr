---
title: 온-프레미스 리소스에 대한 SSO가 Azure AD 조인 디바이스에서 작동하는 방식 | Microsoft Docs
description: 하이브리드 Azure Active Directory 연결 장치를 구성 하 여 SSO 환경을 확장 하는 방법을 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: ba802cb86d68298cd4dfff94162069590744833c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91256465"
---
# <a name="how-sso-to-on-premises-resources-works-on-azure-ad-joined-devices"></a>온-프레미스 리소스에 대한 SSO가 Azure AD 조인 디바이스에서 작동하는 방식

Azure AD(Azure Active Directory) 조인 디바이스에서 SSO(Single Sign-On) 환경을 테넌트의 클라우드 앱에 제공하는 것은 놀랄 일이 아닙니다. 사용자 환경에 온-프레미스 ad (Active Directory)가 있는 경우 온-프레미스 AD를 사용 하는 리소스 및 응용 프로그램에 대해 이러한 장치의 SSO 환경을 확장할 수 있습니다. 

이 문서에서는 이러한 작동 방식에 대해 설명합니다.

## <a name="prerequisites"></a>필수 구성 요소

 Azure AD 조인 컴퓨터가 조직의 네트워크에 연결 되어 있지 않으면 VPN 또는 다른 네트워크 인프라가 필요 합니다. 온-프레미스 SSO에는 온-프레미스 AD DS 도메인 컨트롤러와의 시야 통신이 필요 합니다.

## <a name="how-it-works"></a>작동 방법 

Azure AD 조인 디바이스를 사용하면 사용자는 이미 자신의 환경에서 클라우드 앱에 대한 SSO 환경을 갖추고 있습니다. 환경에 Azure AD와 온-프레미스 AD가 있는 경우 온-프레미스 LOB (기간 업무) 앱, 파일 공유 및 프린터에 대 한 SSO 환경의 범위를 확장할 수 있습니다.

Azure AD 조인 디바이스는 온-프레미스 AD 환경에 조인되어 있지 않으므로 해당 환경에 대한 지식이 없습니다. 그러나 Azure AD Connect를 사용하여 온-프레미스 AD에 대한 추가 정보를 이러한 디바이스에 제공할 수 있습니다.

Azure AD와 온-프레미스 AD를 모두 갖춘 환경은 하이브리드 환경에 있다고도 알려져 있습니다. 하이브리드 환경을 사용하는 경우 온-프레미스 ID 정보를 클라우드에 동기화하기 위해 Azure AD Connect를 이미 배포했을 가능성이 높습니다. 동기화 프로세스의 일부로 온-프레미스 사용자 정보를 Azure AD에 동기화 Azure AD Connect. 사용자가 하이브리드 환경에서 Azure AD 조인 디바이스에 로그인하는 경우 다음을 수행합니다.

1. Azure AD는 사용자의 온-프레미스 도메인의 세부 정보를 [기본 새로 고침 토큰과](concept-primary-refresh-token.md) 함께 장치에 다시 보냅니다.
1. LSA (로컬 보안 기관) 서비스는 장치에서 Kerberos 및 NTLM 인증을 사용 하도록 설정 합니다.

사용자의 온-프레미스 환경에서 Kerberos 또는 NTLM을 요청 하는 리소스에 대 한 액세스를 시도 하는 동안 장치는 다음과 같습니다.

1. 온-프레미스 도메인 정보와 사용자 자격 증명을 찾아낸 DC로 보내 사용자를 인증합니다.
1. 온-프레미스 리소스 또는 응용 프로그램이 지 원하는 프로토콜을 기반으로 하는 Kerberos [TGT (허용 티켓)](/windows/desktop/secauthn/ticket-granting-tickets) 또는 NTLM 토큰을 받습니다. 도메인에 대 한 Kerberos TGT 또는 NTLM 토큰을 가져오지 못한 경우 (관련 DCLocator 시간 제한으로 인해 지연이 발생할 수 있음) 자격 증명 관리자 항목이 시도 되거나 사용자가 대상 리소스에 대 한 자격 증명을 요청 하는 인증 팝업을 받을 수 있습니다.

사용자가 액세스하려고 시도하면 **Windows 통합 인증**에 구성된 모든 앱에서 SSO를 원활하게 받습니다.

Azure AD 조인 디바이스에서 온-프레미스 SSO를 사용하도록 설정하려면 비즈니스용 Windows Hello를 추가로 구성해야 합니다. 자세한 내용은 [비즈니스용 Windows Hello를 사용하여 온-프레미스 Single Sign-On에 대한 Azure AD 조인 디바이스 구성](/windows/security/identity-protection/hello-for-business/hello-hybrid-aadj-sso-base)을 참조하세요. 

## <a name="what-you-get"></a>수신 항목

SSO를 사용하면 Azure AD 조인 디바이스에서 다음을 수행할 수 있습니다. 

- AD 멤버 서버의 UNC 경로에 액세스
- Windows 통합 보안에 구성된 AD 멤버 웹 서버에 액세스 

Windows 디바이스에서 온-프레미스 AD를 관리하려면 [Windows 10용 원격 서버 관리 도구](https://www.microsoft.com/download/details.aspx?id=45520)를 설치하세요.

다음을 사용할 수 있습니다.

- ADUC(Active Directory 사용자 및 컴퓨터) 스냅인을 사용하여 모든 AD 개체를 관리합니다. 그러나 수동으로 연결하려는 도메인을 지정해야 합니다.
- DHCP 스냅인을 사용하여 AD 조인 DHCP 서버를 관리합니다. 그러나 DHCP 서버 이름 또는 주소를 지정해야 할 수 있습니다.
 
## <a name="what-you-should-know"></a>알아야 할 사항

필요한 도메인에 대한 데이터가 동기화되도록 Azure AD Connect에서 [도메인 기반 필터링](../hybrid/how-to-connect-sync-configure-filtering.md#domain-based-filtering)을 조정해야 할 수 있습니다.

Azure AD 조인 디바이스에는 AD의 컴퓨터 개체가 없으므로 Active Directory 머신 인증을 사용하는 앱과 리소스가 작동하지 않습니다. 

Azure AD 조인 디바이스에서 다른 사용자와 파일을 공유할 수 없습니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 [Azure Active Directory에서 디바이스 관리란?](overview.md)을 참조하세요. 
