---
title: 'VPN 사용자에 대해 MFA 사용: Azure AD 인증'
description: VPN 사용자에 대해 multi-factor authentication 사용
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: alzam
ms.openlocfilehash: 7f05b850a0d886ac0df5c542de647f91fe62eb05
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74382202"
---
# <a name="enable-azure-multi-factor-authentication-mfa-for-vpn-users"></a>VPN 사용자에 대해 MFA (Azure Multi-Factor Authentication) 사용

액세스 권한을 부여 하기 전에 사용자에 게 두 번째 인증 단계를 묻는 메시지가 표시 되도록 하려면 Azure AD 테 넌 트에 대해 MFA (Azure Multi-Factor Authentication)를 구성할 수 있습니다. 이 문서의 단계는 2 단계 인증에 대 한 요구 사항을 설정 하는 데 도움이 됩니다.

## <a name="prereq"></a>인지

이 구성에 대 한 필수 구성 요소는 [테 넌 트 구성](openvpn-azure-ad-tenant.md)의 단계를 사용 하 여 구성 된 Azure AD 테 넌 트입니다.

## <a name="mfa"></a>MFA 페이지 열기

1. Azure 포털에 로그인합니다.
2. **Azure Active Directory-> 모든 사용자**로 이동 합니다.
3. **Multi-Factor Authentication** 를 선택 하 여 multi-factor Authentication 페이지를 엽니다.

   ![로그인](./media/openvpn-azure-ad-mfa/mfa1.jpg)

## <a name="users"></a>사용자 선택

1. **Multi-factor authentication** 페이지에서 MFA를 사용 하도록 설정할 사용자를 선택 합니다.
2. **사용**을 선택합니다.

   ![선택](./media/openvpn-azure-ad-mfa/mfa2.jpg)

## <a name="enableauth"></a>인증 사용

1. **Azure Active Directory-> Enterprise 응용 프로그램-모든 응용 프로그램 >** 으로 이동 합니다.
2. **엔터프라이즈 응용 프로그램-모든 응용 프로그램** 페이지에서 **Azure VPN**을 선택 합니다.

   ![디렉터리 ID](./media/openvpn-azure-ad-mfa/user1.jpg)

## <a name="enablesign"></a>로그인 설정 구성

**AZURE VPN-속성** 페이지에서 로그인 설정을 구성 합니다.

1. **사용자가 로그인 할** 수 있도록 설정 하 시겠습니까? **예**로 설정 합니다. 이렇게 하면 AD 테 넌 트의 모든 사용자가 VPN에 성공적으로 연결할 수 있습니다.
2. Azure VPN에 대 한 권한이 있는 사용자로만 로그인을 제한 하려면 **사용자 할당 필요?** 를 **예** 로 설정 합니다.
3. 변경 내용을 저장합니다.

   ![사용 권한](./media/openvpn-azure-ad-mfa/user2.jpg)

## <a name="next-steps"></a>다음 단계

가상 네트워크에 연결 하려면 VPN 클라이언트 프로필을 만들고 구성 해야 합니다. [P2S vpn 연결에 대 한 vpn 클라이언트 구성](openvpn-azure-ad-client.md)을 참조 하세요.
