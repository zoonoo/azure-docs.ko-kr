---
title: Azure Maps의 인증 관리 | Microsoft Docs
description: Azure Portal을 사용하여 Azure Maps의 인증을 관리할 수 있습니다.
author: walsehgal
ms.author: v-musehg
ms.date: 02/14/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 68c3c8ac39f5803e01ee1038ec85ddb96ac80b30
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2019
ms.locfileid: "56242688"
---
# <a name="manage-authentication-in-azure-maps"></a>Azure Maps의 인증 관리

Azure Maps 계정을 만들면 Azure AD(Azure Active Directory) 또는 공유 키 인증을 지원하기 위해 클라이언트 ID와 키가 생성됩니다.

Azure Portal에서 **설정** 아래의 **인증** 페이지로 이동하면 인증 정보를 확인할 수 있습니다. 계정으로 이동합니다. 그런 다음, 메뉴에서 **인증**을 선택합니다.


## <a name="view-authentication-details"></a>인증 정보 보기

인증 정보를 보려면 설정 메뉴의 **인증** 옵션으로 이동합니다.

![인증 보기](./media/how-to-manage-authentication/how-to-view-auth.png)

 인증 및 Azure Maps에 대한 자세한 내용은 [Azure Maps 인증](https://aka.ms/amauth)을 참조하세요.


## <a name="configure-azure-ad-app-registration"></a>Azure AD 앱 등록 구성

Azure Maps 계정을 만들고 나면 Azure AD 테넌트와 Azure Maps Azure 리소스 간의 링크가 필요합니다. 

1. Azure AD 블레이드로 이동한 다음, 이름과 로그인 URL을 웹앱/API의 홈페이지(예: “https://localhost/”)로 사용하여 앱 등록을 만듭니다. 등록된 앱이 이미 있는 경우 2단계로 넘어갑니다.

    ![앱 등록](./media/how-to-manage-authentication/app-registration.png)

    ![앱 등록](./media/how-to-manage-authentication/app-create.png)

2. 앱 등록 아래의 애플리케이션으로 이동하여 Azure Maps에 위임된 API 권한을 할당하고 **설정**을 클릭합니다.  **필요한 권한**을 선택하고 **추가**를 선택합니다. **API 선택**에서 Azure Maps를 검색하여 선택한 다음, **선택**을 클릭합니다.

    ![앱 API 사용 권한](./media/how-to-manage-authentication/app-permissions.png)

3. 마지막으로, 사용 권한에서 Azure Maps 액세스를 선택하고 [선택]을 클릭합니다.

    ![앱 API 사용 권한 선택](./media/how-to-manage-authentication/select-app-permissions.png)

4. 인증 구현에 따라 a 또는 b 단계를 수행합니다.

    1. 애플리케이션이 Azure Maps 웹 SDK에 사용자 토큰 인증을 사용하려는 경우 앱 등록 정보 페이지의 매니페스트 섹션에서 `oauthEnableImplicitFlow`를 true로 설정하여 사용하도록 설정해야 합니다.
    
       ![앱 매니페스트](./media/how-to-manage-authentication/app-manifest.png)

    2. 애플리케이션이 서버/애플리케이션 인증을 사용하는 경우 앱 등록 내의 **키** 블레이드로 이동한 다음, 암호를 만들거나 공개 키 인증서를 앱 등록에 업로드합니다. 암호를 만든 경우 **저장**하고 나중에 사용하기 위해 복사하여 안전하게 저장하고 나면 이 암호를 사용하여 Azure AD에서 토큰을 얻을 수 있습니다.

       ![앱 키](./media/how-to-manage-authentication/app-keys.png)


## <a name="grant-rbac-to-azure-maps"></a>Azure Maps에 RBAC 부여

Azure Maps 계정이 Azure AD 테넌트와 연결되면 사용 가능한 Azure Maps 액세스 제어 역할에 사용자 또는 애플리케이션을 할당하여 액세스 제어를 부여할 수 있습니다.

1. **액세스 제어** 옵션으로 이동한 다음, **역할 할당**, **역할 할당 추가**를 차례로 클릭합니다.

    ![RBAC 부여](./media/how-to-manage-authentication/how-to-grant-rbac.png)

2. 역할 할당 팝아웃 창에서 Azure Maps Date Reader(미리 보기) **역할**, **액세스 할당**: Azure AD 사용자, 그룹 또는 서비스 주체를 차례로 선택하고 드롭다운에서 사용자 또는 애플리케이션을 **선택**한 다음, **저장**을 클릭합니다.

    ![역할 할당 추가](./media/how-to-manage-authentication/add-role-assignment.png)

## <a name="view-available-azure-maps-rbac-roles"></a>사용 가능한 Azure Maps RBAC 역할 보기

액세스 권한을 부여할 수 있는, Azure Maps에 사용 가능한 역할 기반 액세스 제어 역할을 보려면 **액세스 제어(IAM)** 옵션으로 이동하여 **역할**을 클릭한 다음, **Azure Maps**로 시작하는 역할을 검색합니다.

![사용 가능한 역할 보기](./media/how-to-manage-authentication/how-to-view-avail-roles.png)


## <a name="view-azure-maps-role-based-access-control-rbac"></a>Azure Maps RBAC(역할 기반 액세스 제어) 보기

Azure AD에서는 RBAC(역할 기반 액세스 제어)를 통해 세분화된 액세스 제어를 수행할 수 있습니다. 

Azure Maps에 대한 RBAC가 부여된 사용자나 앱을 보려면 **액세스 제어(IAM)** 옵션으로 이동하여 **역할 할당**을 선택한 다음, **Azure Maps**를 기준으로 필터링합니다. 사용 가능한 모든 역할이 아래에 표시됩니다.

![RBAC 보기](./media/how-to-manage-authentication/how-to-view-amrbac.png)


## <a name="request-tokens-for-azure-maps"></a>Azure Maps에 대한 토큰 요청

앱이 등록되어 Azure Maps와 연결되면 이제 액세스 토큰을 요청할 수 있습니다.

* 애플리케이션이 Azure Maps 웹 SDK(웹)에서 사용자 토큰 인증을 사용하려는 경우 Azure Maps 클라이언트 ID 및 Azure AD 앱 ID로 html 페이지를 구성해야 합니다.

* 서버/애플리케이션 인증을 사용하는 애플리케이션의 경우 Azure AD 로그인 엔드포인트 `https://login.microsoftonline.com`에서 Azure AD 리소스 ID `https://atlas.microsoft.com/`, Azure Maps 클라이언트 ID, Azure AD 앱 ID 및 Azure AD 앱 등록 암호 또는 인증서를 사용하여 토큰을 요청합니다.

사용자 및 서비스 주체에 대한 Azure AD의 액세스 토큰 요청 방법에 대한 자세한 내용은 [Azure AD에 대한 인증 시나리오](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)를 참조하세요.


## <a name="next-steps"></a>다음 단계

* Azure AD 인증 및 Azure Maps 웹 SDK에 대한 자세한 내용은 [Azure AD 및 Azure Maps 웹 SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control)를 참조하세요.