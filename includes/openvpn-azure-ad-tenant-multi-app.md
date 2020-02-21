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
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/20/2020
ms.locfileid: "77485681"
---
## <a name="tenant"></a>1. Azure AD 테 넌 트 만들기

[새 테 넌 트 만들기](../articles/active-directory/fundamentals/active-directory-access-create-new-tenant.md) 문서의 단계를 사용 하 여 Azure AD 테 넌 트를 만듭니다.

* 조직 이름
* 초기 도메인 이름

  예제:

   ![새 Azure AD 테 넌 트](./media/openvpn-azure-ad-tenant-multi-app/new-tenant.png)

## <a name="users"></a>2. 테 넌 트 사용자 만들기

이 단계에서는 하나의 전역 관리자 계정과 하나의 마스터 사용자 계정 이라는 두 개의 Azure AD 테 넌 트 사용자를 만듭니다. 마스터 사용자 계정은 마스터 포함 계정 (서비스 계정)으로 사용 됩니다. Azure AD 테 넌 트 사용자 계정을 만들 때 만들려는 사용자의 유형에 대 한 디렉터리 역할을 조정 합니다. [이 문서의](../articles/active-directory/fundamentals/add-users-azure-active-directory.md) 단계를 사용 하 여 Azure AD 테 넌 트에 대해 두 명 이상의 사용자를 만듭니다. 계정 유형을 만들려면 **디렉터리 역할** 을 변경 해야 합니다.

* 전역 관리자
* 사용자

## <a name="register-client"></a>3. VPN 클라이언트 등록

Azure AD 테 넌 트에서 VPN 클라이언트를 등록 합니다.

1. 인증에 사용할 디렉터리의 디렉터리 ID를 찾습니다. Active Directory 페이지의 속성 섹션에 나열 됩니다.

    ![디렉터리 ID](./media/openvpn-azure-ad-tenant-multi-app/directory-id.png)

2. 디렉터리 ID를 복사 합니다.

3. **전역 관리자** 역할이 할당 된 사용자로 Azure Portal에 로그인 합니다.

4. 다음으로, 관리자 동의를 제공 합니다. 브라우저의 주소 표시줄에서 배포 위치와 관련 된 URL을 복사 하 여 붙여 넣습니다.

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

5. 메시지가 표시 되 면 **전역 관리자** 계정을 선택 합니다.

    ![디렉터리 ID](./media/openvpn-azure-ad-tenant-multi-app/pick.png)

6. 메시지가 표시 되 면 **동의** 를 선택 합니다.

    ![수락](./media/openvpn-azure-ad-tenant-multi-app/accept.jpg)

7. Azure AD의 **엔터프라이즈 응용 프로그램**에는 **azure VPN** 나열이 표시 됩니다.

     ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/azure-vpn.png)

## <a name="register-apps"></a>4. 추가 응용 프로그램 등록

이 단계에서는 다양 한 사용자 및 그룹에 대 한 추가 응용 프로그램을 등록 합니다.

1. Azure Active Directory에서 **앱 등록** 를 클릭 한 다음 **+ 새 등록**을 클릭 합니다.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app1.png)

2. **응용 프로그램 등록** 페이지에서 **이름을**입력 합니다. 원하는 **지원 되는 계정 유형을**선택 하 고 **등록**을 클릭 합니다.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app2.png)

3. 새 앱이 등록 되 면 앱 블레이드에서 **API 노출** 을 클릭 합니다.

4. **+ 범위 추가를**클릭 합니다.

5. 기본 **응용 프로그램 ID URI**를 그대로 둡니다. **저장 후 계속**을 클릭합니다.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app3.png)

6. 필수 필드를 입력 하 고 **상태가** **사용으로 설정**되어 있는지 확인 합니다. **범위 추가**를 클릭 합니다.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app4.png)

7. **API** 표시를 클릭 한 다음 **클라이언트 응용 프로그램 추가를**클릭 합니다.  **클라이언트 ID**에 대해 클라우드에 따라 다음 값을 입력 합니다.

    - **41b23e61-6c1e-4545-b367-cd054e0ed4b4** For Azure **Public** 을 입력 합니다.
    - Azure **정부** 에 **51bb15d4-3a4f-4ebf-9dca-40096fe32426** 입력
    - Azure **독일** 에 **538ee9e6-310a-468d-afef-ea97365856a9** 입력
    - Azure **중국 21vianet** 에 대해 **49f817b6-84ae-4cc0-928c-73f27289b3aa** 입력

8. **애플리케이션 추가**를 클릭합니다.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app5.png)

9. **개요** 페이지에서 **응용 프로그램 (클라이언트) ID** 를 복사 합니다. 이 정보는 VPN gateway를 구성 하는 데 필요 합니다.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app6.png)

10. 이 [추가 응용 프로그램 등록](#register-apps) 섹션의 단계를 반복 하 여 보안 요구 사항에 필요한 만큼의 응용 프로그램을 만듭니다. 각 응용 프로그램은 VPN gateway에 연결 되며 다른 사용자 집합을 가질 수 있습니다. 하나의 응용 프로그램만 게이트웨이에 연결할 수 있습니다.

## <a name="assign-users"></a>5. 응용 프로그램에 사용자 할당

응용 프로그램에 사용자를 할당 합니다.

1. **AZURE AD-> 엔터프라이즈 응용 프로그램**에서 새로 등록 된 응용 프로그램을 선택 하 고 **속성**을 클릭 합니다. **사용자 할당 필요 여부** 를 **예**로 설정 합니다. **저장**을 클릭합니다.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/user2.png)

2. 앱 페이지에서 **사용자 및 그룹**을 클릭 한 다음 **+ 사용자 추가**를 클릭 합니다.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/user3.png)

3. **할당 추가**에서 **사용자 및 그룹**을 클릭 합니다. 이 VPN 응용 프로그램에 액세스 하려는 사용자를 선택 합니다. **선택**을 클릭합니다.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/user4.png)