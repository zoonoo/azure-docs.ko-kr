---
title: '사용자 VPN 연결을 위한 azure AD 테 넌 트: Azure AD 인증'
description: Azure AD 인증을 사용 하 여 VNet에 연결 하기 위해 Azure 가상 WAN 사용자 VPN (지점 및 사이트 간)을 사용할 수 있습니다.
titleSuffix: Azure Virtual WAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: alzam
ms.openlocfilehash: a54397fe7cfecef6813105645b7f2b218894875e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91367855"
---
# <a name="prepare-azure-active-directory-tenant-for-user-vpn-openvpn-protocol-connections"></a>사용자 VPN OpenVPN 프로토콜 연결에 대 한 Azure Active Directory 테 넌 트 준비

IKEv2 프로토콜을 통해 가상 허브에 연결 하는 경우 인증서 기반 인증 또는 RADIUS 인증을 사용할 수 있습니다. 그러나 OpenVPN 프로토콜을 사용 하는 경우 Azure Active Directory 인증을 사용할 수도 있습니다. 이 문서는 OpenVPN 인증을 사용 하 여 가상 WAN 사용자 VPN에 대 한 Azure AD 테 넌 트 (지점 및 사이트 간)를 설정 하는 데 도움이 됩니다.

> [!NOTE]
> Azure AD 인증은 OpenVPN 프로토콜 연결에 대해서만 지원 됩니다 &reg; .
>

## <a name="1-create-the-azure-ad-tenant"></a><a name="tenant"></a>1. Azure AD 테넌트 만들기

Azure AD 테 넌 트가 있는지 확인 합니다. Azure AD 테 넌 트가 없는 경우 [새 테 넌 트 만들기](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 문서의 단계를 사용 하 여 만들 수 있습니다.

* 조직 이름
* 초기 도메인 이름

예제:

   ![새 Azure AD 테넌트](./media/openvpn-create-azure-ad-tenant/newtenant.png)

## <a name="2-create-azure-ad-tenant-users"></a><a name="users"></a>2. Azure AD 테 넌 트 사용자 만들기

그런 다음, 새로 만든 Azure AD 테 넌 트, 하나의 전역 관리자 계정 및 하나의 사용자 계정에 두 개의 사용자 계정을 만듭니다. 사용자 계정은 OpenVPN 인증을 테스트 하는 데 사용할 수 있으며 전역 관리자 계정은 Azure VPN 앱 등록에 대 한 동의를 부여 하는 데 사용 됩니다. Azure AD 사용자 계정을 만든 후 관리 권한을 위임 하기 위해 사용자에 게 **디렉터리 역할** 을 할당 합니다.

[이 문서의](../active-directory/fundamentals/add-users-azure-active-directory.md) 단계를 사용 하 여 Azure AD 테 넌 트에 대해 두 명의 사용자를 만듭니다. 만든 계정 중 하나의 **디렉터리 역할** 을 **전역 관리자**에 게 변경 해야 합니다.

## <a name="3-grant-consent-to-the-azure-vpn-app-registration"></a><a name="enable-authentication"></a>3. Azure VPN 앱 등록에 동의를 부여 합니다.

1. **전역 관리자** 역할이 할당 된 사용자로 Azure Portal에 로그인 합니다.

2. 그런 다음 조직에 대해 관리자 동의를 부여 합니다. 이렇게 하면 Azure VPN 응용 프로그램에서 사용자 프로필에 로그인 하 고 사용자 프로필을 읽을 수 있습니다. 브라우저의 주소 표시줄에서 배포 위치와 관련된 URL을 복사하여 붙여넣습니다.

    공용

    ```
    https://login.microsoftonline.com/common/oauth2/authorize?client_id=41b23e61-6c1e-4545-b367-cd054e0ed4b4&response_type=code&redirect_uri=https://portal.azure.com&nonce=1234&prompt=admin_consent
    ````

    Azure Government

    ```
    https://login-us.microsoftonline.com/common/oauth2/authorize?client_id=51bb15d4-3a4f-4ebf-9dca-40096fe32426&response_type=code&redirect_uri=https://portal.azure.us&nonce=1234&prompt=admin_consent
    ````

    Microsoft Cloud 독일

    ```
    https://login-us.microsoftonline.de/common/oauth2/authorize?client_id=538ee9e6-310a-468d-afef-ea97365856a9&response_type=code&redirect_uri=https://portal.microsoftazure.de&nonce=1234&prompt=admin_consent
    ````

    Azure China 21Vianet

    ```
    https://https://login.chinacloudapi.cn/common/oauth2/authorize?client_id=49f817b6-84ae-4cc0-928c-73f27289b3aa&response_type=code&redirect_uri=https://portal.azure.cn&nonce=1234&prompt=admin_consent
    ```

3. 메시지가 표시 되 면 **전역 관리자** 계정을 선택 합니다.

    ![디렉터리 ID](./media/openvpn-create-azure-ad-tenant/pick.png)

4. 메시지가 표시되면 **수락**을 선택합니다.

    ![사용자의 조직에 대 한 동의를 요청한 메시지 권한이 있는 대화 상자와 추가 정보를 보여 주는 스크린샷](./media/openvpn-create-azure-ad-tenant/accept.jpg)

5. Azure AD의 **엔터프라이즈 응용 프로그램**에는 이제 **azure VPN** 이 나열 되어 있습니다.

    ![Azure VPN](./media/openvpn-create-azure-ad-tenant/azurevpn.png)

## <a name="next-steps"></a>다음 단계

Azure AD 인증을 사용 하 여 가상 네트워크에 연결 하려면 사용자 VPN 구성을 만들어 가상 허브에 연결 해야 합니다. [Azure에 대 한 지점 및 사이트 간 연결에 대 한 AZURE AD 인증 구성을](virtual-wan-point-to-site-azure-ad.md)참조 하세요.
