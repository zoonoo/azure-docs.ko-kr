---
title: 포함 파일
description: 포함 파일
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/15/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c3236f9c60cb359349d96e93f674c3e278e44f1e
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93375900"
---
## <a name="1-create-the-azure-ad-tenant"></a><a name="tenant"></a>1. Azure AD 테넌트 만들기

[새 테넌트 만들기](../articles/active-directory/fundamentals/active-directory-access-create-new-tenant.md) 문서의 단계를 사용하여 Azure AD 테넌트를 만듭니다.

* 조직 구성 이름
* 초기 도메인 이름

  예제:

   ![새 Azure AD 테넌트](./media/openvpn-azure-ad-tenant-multi-app/new-tenant.png)

## <a name="2-create-tenant-users"></a><a name="users"></a>2. 테넌트 사용자 만들기

이 단계에서는 두 Azure AD 테넌트 사용자를 만듭니다. 하나의 전역 관리자 계정과 하나의 마스터 사용자 계정. 마스터 사용자 계정은 마스터 임베딩 계정(서비스 계정)으로 사용됩니다. Azure AD 테넌트 사용자 계정을 만들 때 만들려는 사용자 유형에 대한 디렉터리 역할을 조정합니다. [이 문서](../articles/active-directory/fundamentals/add-users-azure-active-directory.md)의 단계를 사용하여 Azure AD 테넌트에 대해 두 명 이상의 사용자를 만듭니다. 계정 유형을 만들려면 **디렉터리 역할** 을 변경해야 합니다.

* 전역 관리자
* 사용자

## <a name="3-register-the-vpn-client"></a><a name="register-client"></a>3. VPN 클라이언트 등록

Azure AD 테넌트에 VPN 클라이언트를 등록합니다.

1. 인증에 사용할 디렉터리의 디렉터리 ID를 찾습니다. Active Directory 페이지의 속성 섹션에 나열되어 있습니다.

    ![디렉터리 ID](./media/openvpn-azure-ad-tenant-multi-app/directory-id.png)

2. 디렉터리 ID를 복사합니다.

3. **전역 관리자** 역할이 할당된 사용자로 Azure Portal에 로그인합니다.

4. 다음으로, 관리자 동의를 제공합니다. 브라우저의 주소 표시줄에서 배포 위치와 관련된 URL을 복사하여 붙여넣습니다.

    공용

    ```
    https://login.microsoftonline.com/common/oauth2/authorize?client_id=41b23e61-6c1e-4545-b367-cd054e0ed4b4&response_type=code&redirect_uri=https://portal.azure.com&nonce=1234&prompt=admin_consent
    ````

    Azure Government

    ```
    https://login.microsoftonline.us/common/oauth2/authorize?client_id=51bb15d4-3a4f-4ebf-9dca-40096fe32426&response_type=code&redirect_uri=https://portal.azure.us&nonce=1234&prompt=admin_consent
    ````

    Microsoft Cloud 독일

    ```
    https://login-us.microsoftonline.de/common/oauth2/authorize?client_id=538ee9e6-310a-468d-afef-ea97365856a9&response_type=code&redirect_uri=https://portal.microsoftazure.de&nonce=1234&prompt=admin_consent
    ````

    Azure China 21Vianet

    ```
    https://https://login.chinacloudapi.cn/common/oauth2/authorize?client_id=49f817b6-84ae-4cc0-928c-73f27289b3aa&response_type=code&redirect_uri=https://portal.azure.cn&nonce=1234&prompt=admin_consent
    ```

> [!NOTE]
> 동의를 제공 하기 위해 Azure AD 테 넌 트의 기본이 아닌 전역 관리자 계정을 사용 하는 경우 "common"을 URL의 Azure AD 디렉터리 id로 바꿉니다. 특정 한 경우에도 "common"을 디렉터리 id로 바꾸어야 할 수도 있습니다.
>

5. 메시지가 표시되면 **전역 관리자** 계정을 선택합니다.

    ![디렉터리 ID 2](./media/openvpn-azure-ad-tenant-multi-app/pick.png)

6. 메시지가 표시되면 **수락** 을 선택합니다.

    ![스크린샷에는 요청한 메시지 권한 및 요청에 대 한 정보가 포함 된 창이 표시 됩니다.](./media/openvpn-azure-ad-tenant-multi-app/accept.jpg)

7. Azure AD 아래의 **엔터프라이즈 애플리케이션** 에서 **Azure VPN** 이 표시됩니다.

     ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/azure-vpn.png)

## <a name="4-register-additional-applications"></a><a name="register-apps"></a>4. 추가 애플리케이션 등록

이 단계에서는 다양한 사용자 및 그룹에 대한 추가 애플리케이션을 등록합니다.

1. Azure Active Directory에서 **앱 등록** 을 클릭한 다음, **+ 새 등록** 을 클릭합니다.

    ![Azure VPN 2](./media/openvpn-azure-ad-tenant-multi-app/app1.png)

2. **애플리케이션 등록** 페이지에서 **이름** 을 입력합니다. 원하는 **지원 계정 유형** 을 선택한 다음, **등록** 을 클릭합니다.

    ![Azure VPN 3](./media/openvpn-azure-ad-tenant-multi-app/app2.png)

3. 새 앱이 등록되면 앱 블레이드에서 **API 표시** 를 선택합니다.

4. **+ 범위 추가** 를 클릭합니다.

5. 기본 **애플리케이션 ID URI** 를 그대로 둡니다. **저장 후 계속** 을 클릭합니다.

    ![Azure VPN 4](./media/openvpn-azure-ad-tenant-multi-app/app3.png)

6. 필수 필드를 입력하고 **상태** 가 **활성화됨** 인지 확인합니다. **범위 추가** 를 클릭합니다.

    ![Azure VPN 5](./media/openvpn-azure-ad-tenant-multi-app/app4.png)

7. **API 표시** 를 클릭한 다음, **+ 클라이언트 애플리케이션 추가** 를 클릭합니다.  **클라이언트 ID** 의 경우 클라우드에 따라 다음 값을 입력합니다.

    - Azure **Public** 에 **41b23e61-6c1e-4545-b367-cd054e0ed4b4** 입력
    - Azure **Government** 에 **51bb15d4-3a4f-4ebf-9dca-40096fe32426** 입력
    - Azure **독일** 에 **538ee9e6-310a-468d-afef-ea97365856a9** 입력
    - Azure **중국 21Vianet** 에 **49f817b6-84ae-4cc0-928c-73f27289b3aa** 입력

8. **애플리케이션 추가** 를 클릭합니다.

    ![Azure VPN 6](./media/openvpn-azure-ad-tenant-multi-app/app5.png)

9. **개요** 페이지에서 **애플리케이션(클라이언트) ID** 를 복사합니다. VPN 게이트웨이를 구성하려면 이 정보가 필요합니다.

    ![Azure VPN 7](./media/openvpn-azure-ad-tenant-multi-app/app6.png)

10. 이 [추가 애플리케이션 등록](#register-apps) 섹션의 단계를 반복하여 보안 요구 사항에 필요한 만큼의 애플리케이션을 만듭니다. 각 애플리케이션은 VPN 게이트웨이에 연결되며 다른 사용자 세트를 가질 수 있습니다. 하나의 애플리케이션만 게이트웨이에 연결할 수 있습니다.

## <a name="5-assign-users-to-applications"></a><a name="assign-users"></a>5. 애플리케이션에 사용자 할당

애플리케이션에 사용자를 할당합니다.

1. **Azure AD -> 엔터프라이즈 애플리케이션** 에서 새로 등록된 애플리케이션을 선택하고 **속성** 을 클릭합니다. **사용자 할당이 필요한가요?** 가 **예** 로 설정되어 있는지 확인합니다. **저장** 을 클릭합니다.

    ![Azure VPN 8](./media/openvpn-azure-ad-tenant-multi-app/user2.png)

2. 앱 페이지에서 **사용자 및 그룹** 을 클릭한 다음, **+사용자 추가** 를 클릭합니다.

    ![Azure VPN 9](./media/openvpn-azure-ad-tenant-multi-app/user3.png)

3. **할당 추가** 에서 **사용자 및 그룹** 을 클릭합니다. 이 VPN 애플리케이션에 액세스할 수 있는 사용자를 선택합니다. **선택** 을 클릭합니다.

    ![Azure VPN 10](./media/openvpn-azure-ad-tenant-multi-app/user4.png)
