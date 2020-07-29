---
title: 사용자 로그인을 사용 하 여 단일 페이지 응용 프로그램을 보호 하는 방법
titleSuffix: Azure Maps
description: Azure Maps 웹 SDK에서 Azure AD single sign-on을 지 원하는 단일 페이지 응용 프로그램을 구성 하는 방법입니다.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: devx-track-javascript
ms.openlocfilehash: 72fe4d897c9c202e0c4cd5861525093760036d26
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87285665"
---
# <a name="secure-a-single-page-application-with-user-sign-in"></a>사용자 로그인을 사용 하 여 단일 페이지 응용 프로그램 보호

다음 가이드는 콘텐츠 서버에서 호스트 되는 응용 프로그램 또는 최소한의 웹 서버 종속성이 있는 응용 프로그램에 관련 된 것입니다. 응용 프로그램은 Azure AD 사용자로만 보호 되는 보호 된 리소스를 제공 합니다. 시나리오는 웹 응용 프로그램이 Azure AD에 인증 하 고 사용자를 대신 하 여 REST Api를 Azure Maps 호출할 수 있도록 하는 것입니다.

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

## <a name="create-an-application-registration-in-azure-ad"></a>Azure AD에서 응용 프로그램 등록 만들기

사용자가 로그인 할 수 있도록 Azure AD에서 웹 응용 프로그램을 만듭니다. 웹 응용 프로그램은 Azure Maps REST Api에 대 한 사용자 액세스를 위임 합니다.

1. Azure Portal의 Azure 서비스 목록에서 **Azure Active Directory**  >  **앱 등록**  >  **새 등록**을 선택 합니다.  

    > [!div class="mx-imgBorder"]
    > ![앱 등록](./media/how-to-manage-authentication/app-registration.png)

2. **이름을**입력 하 고, **지원 계정 유형을**선택 하 고, Azure AD에서 토큰을 발급 하는 URL을 나타내는 리디렉션 URI를 제공 하 고, 맵 컨트롤이 호스트 되는 url을 제공 합니다. 자세한 샘플은 [Azure Maps AZURE AD 샘플](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples/tree/master/src/ImplicitGrant)을 참조 하세요. 그런 다음, **등록**을 선택합니다.  

3. Azure Maps에 위임 된 API 권한을 할당 하려면 응용 프로그램으로 이동 합니다. 그런 다음 **앱 등록**에서 **API 권한**  >  **추가 권한 추가**를 선택 합니다. **내 조직에서 사용 하는 api**에서를 검색 하 고 **Azure Maps**를 선택 합니다.

    > [!div class="mx-imgBorder"]
    > ![앱 API 권한 추가](./media/how-to-manage-authentication/app-permissions.png)

4. **액세스 Azure Maps**옆의 확인란을 선택 하 고 **사용 권한 추가**를 선택 합니다.

    > [!div class="mx-imgBorder"]
    > ![앱 API 권한 선택](./media/how-to-manage-authentication/select-app-permissions.png)

5. `oauth2AllowImplicitFlow`을 사용하도록 설정합니다. 이 기능을 사용 하도록 설정 하려면 앱 등록의 **매니페스트** 섹션에서 `oauth2AllowImplicitFlow` 를로 설정 `true` 합니다.

6. 웹 SDK에서 사용할 Azure AD 앱 ID와 Azure AD 테 넌 트 ID를 앱 등록에서 복사 합니다. Azure AD 앱 등록 세부 정보 및 `x-ms-client-id` Azure Map 계정의를 웹 SDK에 추가 합니다.

    ```javascript
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js" />
        <script>
            var map = new atlas.Map("map", {
                center: [-122.33, 47.64],
                zoom: 12,
                language: "en-US",
                authOptions: {
                    authType: "aad",
                    clientId: "<insert>",  // azure map account client id
                    aadAppId: "<insert>",  // azure ad app registration id
                    aadTenant: "<insert>", // azure ad tenant id
                    aadInstance: "https://login.microsoftonline.com/"
                }
            });
        </script>   
    ```

7. 사용자 또는 그룹에 대 한 Azure 역할 기반 액세스 제어를 구성 합니다. [RBAC를 사용 하도록 설정 하려면 다음 섹션](#grant-role-based-access-for-users-to-azure-maps)을 참조 하세요.
   
[!INCLUDE [grant role access to users](./includes/grant-rbac-users.md)]

## <a name="next-steps"></a>다음 단계

단일 페이지 응용 프로그램 시나리오에 대 한 추가 이해:
> [!div class="nextstepaction"]
> [단일 페이지 애플리케이션](https://docs.microsoft.com/azure/active-directory/develop/scenario-spa-overview)

Azure Maps 계정에 대 한 API 사용 메트릭을 찾습니다.
> [!div class="nextstepaction"]
> [사용 메트릭 보기](how-to-view-api-usage.md)

Azure Maps와 Azure AD를 통합 하는 방법을 보여 주는 샘플을 탐색 합니다.
> [!div class="nextstepaction"]
> [Azure Maps 샘플](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples/tree/master/src/ImplicitGrant)
