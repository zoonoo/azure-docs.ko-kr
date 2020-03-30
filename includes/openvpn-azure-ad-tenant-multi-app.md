---
title: 포함 파일
description: 포함 파일
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/18/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e950d194ab48cec1a70c7bd17617332cb858a55d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77485681"
---
## <a name="1-create-the-azure-ad-tenant"></a><a name="tenant"></a>1. Azure AD 테넌트 만들기

[새 테넌트 만들기](../articles/active-directory/fundamentals/active-directory-access-create-new-tenant.md) 문서의 단계를 사용하여 Azure AD 테넌트를 만듭니다.

* 조직 이름
* 초기 도메인 이름

  예제:

   ![새 Azure AD 테넌트](./media/openvpn-azure-ad-tenant-multi-app/new-tenant.png)

## <a name="2-create-tenant-users"></a><a name="users"></a>2. 테넌트 사용자 만들기

이 단계에서는 두 개의 Azure AD 테넌트 사용자(글로벌 관리자 계정 1개와 마스터 사용자 계정 1개)를 만듭니다. 마스터 사용자 계정은 마스터 포함 계정(서비스 계정)으로 사용됩니다. Azure AD 테넌트 사용자 계정을 만들 때 만들려는 사용자 유형에 맞게 디렉터리 역할을 조정합니다. [이 문서의](../articles/active-directory/fundamentals/add-users-azure-active-directory.md) 단계를 사용하여 Azure AD 테넌트에 대해 두 명 이상의 사용자를 만듭니다. **디렉터리 역할을** 변경하여 계정 유형을 만들어야 합니다.

* 전역 관리자
* 사용자

## <a name="3-register-the-vpn-client"></a><a name="register-client"></a>3. VPN 클라이언트 등록

Azure AD 테넌트에 VPN 클라이언트를 등록합니다.

1. 인증에 사용할 디렉터리 ID를 찾습니다. Active Directory 페이지의 속성 섹션에 나열됩니다.

    ![디렉터리 ID](./media/openvpn-azure-ad-tenant-multi-app/directory-id.png)

2. 디렉터리 ID를 복사합니다.

3. **전역 관리자** 역할이 할당된 사용자로 Azure 포털에 로그인합니다.

4. 다음으로 관리자의 동의를 제공합니다. 브라우저의 주소 표시줄에서 배포 위치와 관련된 URL을 복사하여 붙여넣습니다.

    Public

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

5. 메시지가 표시되면 **글로벌 관리자** 계정을 선택합니다.

    ![디렉터리 ID](./media/openvpn-azure-ad-tenant-multi-app/pick.png)

6. 메시지가 표시되면 **수락을** 선택합니다.

    ![수락](./media/openvpn-azure-ad-tenant-multi-app/accept.jpg)

7. Azure AD에서 엔터프라이즈 응용 프로그램에서 Azure **VPN이** 나열된 것을 볼 수 **있습니다.**

     ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/azure-vpn.png)

## <a name="4-register-additional-applications"></a><a name="register-apps"></a>4. 추가 신청서 등록

이 단계에서는 다양한 사용자 및 그룹에 대한 추가 응용 프로그램을 등록합니다.

1. Azure Active Directory에서 앱 등록을 클릭한 다음 **+ 새 등록을** **클릭합니다.**

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app1.png)

2. 응용 **프로그램 등록** 페이지에서 **이름을**입력합니다. 원하는 **지원되는 계정 유형을**선택한 다음 **등록을**클릭합니다.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app2.png)

3. 새 앱이 등록되면 앱 블레이드 아래에 **API 노출을** 클릭합니다.

4. **+ 범위 추가를**클릭합니다.

5. 기본 **응용 프로그램 ID URI를**둡니다. **저장 후 계속**을 클릭합니다.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app3.png)

6. 필요한 필드를 채우고 **상태가** **활성화되어**있는지 확인합니다. **범위 추가를 클릭합니다.**

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app4.png)

7. **API 노출을** 클릭한 다음 **+ 클라이언트 응용 프로그램 추가.**  **클라이언트 ID의**경우 클라우드에 따라 다음 값을 입력합니다.

    - Azure **Public의** **경우 41b23e61-6c1e-4545-b367-cd054e0ed4b4b4를** 입력합니다.
    - Azure **정부에** **51bb15d4-3a4f-4ebf-9dca-40096fe32426** 입력
    - Azure **독일의** **경우 538ee9e6-310a-468d-afef-ea97365856a9를** 입력합니다.
    - **49f817b6-84ae-4cc0-928c-73f27289b3aa** Azure **중국 21Vianet에** 입력

8. **애플리케이션 추가**를 클릭합니다.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app5.png)

9. **개요** 페이지에서 **응용 프로그램(클라이언트) ID를** 복사합니다. VPN 게이트웨이를 구성하려면 이 정보가 필요합니다.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app6.png)

10. 이 [레지스터 추가 응용 프로그램](#register-apps) 섹션의 단계를 반복하여 보안 요구 사항에 필요한 응용 프로그램을 만듭니다. 각 응용 프로그램은 VPN 게이트웨이에 연결되며 다른 사용자 집합을 가질 수 있습니다. 하나의 응용 프로그램만 게이트웨이에 연결할 수 있습니다.

## <a name="5-assign-users-to-applications"></a><a name="assign-users"></a>5. 응용 프로그램에 사용자 할당

응용 프로그램에 사용자를 할당합니다.

1. **Azure AD -> 엔터프라이즈 응용 프로그램에서**새로 등록된 응용 프로그램을 선택하고 **속성을**클릭합니다. 사용자 **할당이 필요한지** 확인하려면 **예로**설정되어 있습니까? **저장**을 클릭합니다.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/user2.png)

2. 앱 페이지에서 사용자 **및 그룹을**클릭한 다음 **+사용자 추가**를 클릭합니다.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/user3.png)

3. **과제 추가에서** **사용자 및 그룹을 클릭합니다.** 이 VPN 응용 프로그램에 액세스할 수 있도록 하려는 사용자를 선택합니다. **선택**을 클릭합니다.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/user4.png)