---
title: 'VPN 사용자에 대해 MFA 사용: Azure AD 인증 | Microsoft Docs'
description: VPN 사용자에 대해 multi-factor authentication 사용
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: alzam
ms.openlocfilehash: 1b506f8439deabd110daaefab36442140e20d0d4
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185365"
---
# <a name="enable-multi-factor-authentication-mfa-for-vpn-users"></a>VPN 사용자에 대해 Multi-Factor Authentication (MFA) 사용

액세스 권한을 부여 하기 전에 사용자에 게 두 번째 단계 인증을 요구 하는 메시지가 표시 되도록 Azure AD 테 넌 트의 사용자에 대해 MFA를 사용 하도록 설정할 수 있습니다.

> [!NOTE]
> 필수 조건: "테 넌 트 구성" 문서에 설명 된 대로 Azure AD 테 넌 트를 구성 했습니다.
>

### <a name="tenant"></a>1. Azure Portal 로그인 하 여 **Azure Active Directory** , **모든 사용자** 로 이동 하 고 **Multi-Factor Authentication** 를 클릭 합니다.


   ![새 Azure AD 테 넌 트](./media/openvpn-azure-ad-mfa/mfa1.jpg)

### <a name="users"></a>2. MFA를 사용 하도록 설정 하려는 사용자를 선택 하 고 **사용** 을 클릭 합니다.

   ![새 Azure AD 테 넌 트](./media/openvpn-azure-ad-mfa/mfa2.jpg)

### <a name="enable-authentication"></a>3. **Azure Active Directory** , **엔터프라이즈 응용 프로그램**, **모든 응용 프로그램** 으로 이동 하 여 **Azure VPN** 을 클릭 합니다.


   ![디렉터리 ID](./media/openvpn-azure-ad-mfa/user1.jpg)

### <a name="users"></a>4. **사용자가 로그인 할 수 있습니까?** 가 예로 설정 되어 있는지 확인 합니다. Azure VPN에 대 한 권한이 있는 사용자만 로그인 할 수 있도록 하려면 **사용자 할당 필요?** 를 예로 설정 합니다. 그렇지 않으면 AD 테 넌 트의 모든 사용자가 VPN에 성공적으로 연결할 수 있습니다.

   ![권한](./media/openvpn-azure-ad-mfa/user2.jpg)


## <a name="next-steps"></a>다음 단계

가상 네트워크에 연결 하려면 VPN 클라이언트 프로필을 만들고 구성 해야 합니다. [P2S vpn 연결에 대 한 vpn 클라이언트 구성](openvpn-azure-ad-client.md)을 참조 하세요.
