---
title: 인증 관리 | 마이크로소프트 Azure 지도
description: Azure 포털을 사용하여 Microsoft Azure 지도에서 인증을 관리합니다.
author: philmea
ms.author: philmea
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: dfe73971f29ea362fdd0ddd654e705b622ab1866
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335539"
---
# <a name="manage-authentication-in-azure-maps"></a>Azure Maps의 인증 관리

Azure Maps 계정을 만든 후 Azure Active Directory(Azure AD) 인증 및 공유 키 인증을 지원하기 위해 클라이언트 ID와 키가 만들어집니다.

## <a name="view-authentication-details"></a>인증 정보 보기

Azure Maps 계정을 만든 후 기본 및 보조 키가 생성됩니다. [공유 키 인증을 사용하여 Azure Maps를 호출할](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication#shared-key-authentication)때 기본 키를 구독 키로 사용하는 것이 좋습니다. 롤링 키 변경 과 같은 시나리오에서 보조 키를 사용할 수 있습니다. 자세한 내용은 [Azure Maps의 인증을](https://aka.ms/amauth)참조하십시오.

Azure 포털에서 인증 세부 정보를 볼 수 있습니다. 계정에서 **설정** 메뉴에서 **인증을**선택합니다.

![인증 세부 정보](./media/how-to-manage-authentication/how-to-view-auth.png)


## <a name="register-and-configure-an-azure-ad-app"></a>Azure AD 앱 등록 및 구성

1. Azure 포털에서 Azure 서비스 목록에서 Azure **Active Directory** > 앱 등록**새 등록을****선택합니다.** >   

    ![앱 등록](./media/how-to-manage-authentication/app-registration.png)

1. 앱을 이미 등록한 경우 다음 단계로 계속 합니다. 앱을 등록하지 않은 경우 **이름을**입력하고 지원 **계정 유형을**선택한 다음 **등록을**선택합니다.  

    ![앱 등록 세부 정보](./media/how-to-manage-authentication/app-create.png)

1. Azure Maps에 위임된 API 권한을 할당하려면 응용 프로그램으로 이동합니다. 그런 다음 **앱 등록에서** **API 권한** > **추가 권한을**선택합니다. **API에서 조직에서 Azure** **Maps를**검색하고 선택합니다.

    ![앱 API 권한 추가](./media/how-to-manage-authentication/app-permissions.png)

1. Azure Maps 에 **액세스할**수 있는 확인란을 선택한 다음 **권한 추가를 선택합니다.**

    ![앱 API 권한 선택](./media/how-to-manage-authentication/select-app-permissions.png)

1. 인증 방법에 따라 다음 단계 중 하나를 완료합니다. 

    * 응용 프로그램에서 Azure Maps Web SDK를 사용하여 사용자 `oauth2AllowImplicitFlow`토큰 인증을 사용하는 경우 을 사용하도록 설정합니다. 이를 사용하려면 앱 등록의 **매니페스트** 섹션에서 `oauth2AllowImplicitFlow` true로 설정합니다. 
    
       ![앱 매니페스트](./media/how-to-manage-authentication/app-manifest.png)

    * 응용 프로그램이 서버 또는 응용 프로그램 인증을 사용하는 경우 앱 등록 페이지에서 **인증서 & 비밀로 이동합니다.** 그런 다음 공개 키 인증서를 업로드하거나 **새 클라이언트 암호를**선택하여 암호를 만듭니다. 
    
       ![클라이언트 비밀 만들기](./media/how-to-manage-authentication/app-keys.png)

        암호를 만든 다음 **추가를**선택한 후 암호를 복사하여 안전하게 저장합니다. 이 암호를 사용하여 Azure AD에서 토큰을 가져옵니다.

       ![클라이언트 보안 비밀 추가](./media/how-to-manage-authentication/add-key.png)


## <a name="grant-role-based-access-control-to-azure-maps"></a>Azure 지도에 역할 기반 액세스 제어 부여

Azure Maps 계정을 Azure AD 테넌트와 연결한 후 액세스 제어를 부여할 수 있습니다. 하나 이상의 Azure Maps 액세스 제어 역할에 사용자, 그룹 또는 응용 프로그램을 할당하여 역할 기반 액세스 제어(RBAC)를 부여합니다. *role-based access control* 

1. **Azure 지도 계정으로**이동합니다. **액세스 제어(IAM)** > **역할 할당을**선택합니다.

    ![RBAC 부여](./media/how-to-manage-authentication/how-to-grant-rbac.png)

1. 역할 **할당** 탭에서 **역할**에서 **Azure Maps 날짜 판독기(미리 보기)를**선택합니다. **액세스 할당**에서 **Azure AD 사용자, 그룹 또는 서비스 보안 주체**를 선택합니다. 사용자 또는 응용 프로그램을 선택합니다. 그런 다음 **저장을**선택합니다.

    ![역할 할당 추가](./media/how-to-manage-authentication/add-role-assignment.png)

## <a name="view-available-azure-maps-rbac-roles"></a>사용 가능한 Azure Maps RBAC 역할 보기

Azure Maps에서 사용할 수 있는 RBAC 역할을 보려면 **IAM(액세스 제어)으로**이동하십시오. **역할을**선택한 다음 *Azure Maps로*시작하는 역할을 검색합니다. 이러한 Azure Maps 역할은 액세스 권한을 부여할 수 있는 역할입니다.

![사용 가능한 역할 보기](./media/how-to-manage-authentication/how-to-view-avail-roles.png)


## <a name="view-azure-maps-rbac"></a>Azure 지도 RBAC 보기

RBAC는 세분화된 액세스 제어를 제공합니다.

Azure 지도에 대해 RBAC가 부여된 사용자 및 앱을 보려면 **IAM(액세스 제어)으로**이동하십시오. 거기에서 역할 할당을 선택한 다음 **Azure Maps**로 **필터링합니다.**

![RBAC가 부여된 사용자 및 앱 보기](./media/how-to-manage-authentication/how-to-view-amrbac.png)


## <a name="request-tokens-for-azure-maps"></a>Azure Maps에 대한 토큰 요청

앱을 등록하고 Azure Maps에 연결한 후 액세스 토큰을 요청할 수 있습니다.

응용 프로그램이 Azure Maps Web SDK를 사용하여 사용자 토큰 인증을 사용하는 경우 Azure Maps 클라이언트 ID 및 Azure AD 앱 ID로 HTML 페이지를 구성합니다.

응용 프로그램이 서버 또는 응용 프로그램 인증을 사용하는 경우 Azure AD 토큰 끝점에서 `https://login.microsoftonline.com`토큰을 요청합니다. 요청시 다음 세부 정보를 사용합니다. 

* Azure AD 리소스 ID`https://atlas.microsoft.com/`
* Azure Maps 클라이언트 ID
* Azure AD 앱 ID
* Azure AD 앱 등록 암호 또는 인증서

| Azure 환경   | Azure AD 토큰 끝점 | Azure 리소스 ID |
| --------------------|-------------------------|-------------------|
| Azure 퍼블릭 클라우드        | `https://login.microsoftonline.com` | `https://atlas.microsoft.com/` |
| Azure 정부 클라우드   | `https://login.microsoftonline.us`  | `https://atlas.microsoft.com/` | 

사용자 및 서비스 주체에 대한 Azure AD의 액세스 토큰 요청에 대한 자세한 내용은 [Azure AD](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)에 대한 인증 시나리오를 참조하십시오.


## <a name="next-steps"></a>다음 단계

자세한 내용은 [Azure AD 및 Azure 지도 웹 SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control)를 참조하십시오.

Azure Maps 계정에 대한 API 사용 메트릭을 찾습니다.
> [!div class="nextstepaction"] 
> [사용 메트릭 보기](how-to-view-api-usage.md)

Azure AD를 Azure 맵과 통합하는 방법을 보여 주는 샘플을 살펴봅습니다.

> [!div class="nextstepaction"]
> [Azure AD 인증 샘플](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)
