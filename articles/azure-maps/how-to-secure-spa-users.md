---
title: 사용자 로그인을 통해 단일 페이지 애플리케이션을 보호하는 방법
titleSuffix: Azure Maps
description: Azure Maps Web SDK에서 Azure AD Single Sign-On을 지원하는 단일 페이지 애플리케이션을 구성하는 방법입니다.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: devx-track-js
ms.openlocfilehash: 60d8dc45fb26ea210b1827a6938716474faa0304
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92895616"
---
# <a name="secure-a-single-page-application-with-user-sign-in"></a>사용자 로그인을 통해 단일 페이지 애플리케이션 보호

다음 가이드는 콘텐츠 서버에서 호스트되거나 최소한의 웹 서버 종속성이 있는 애플리케이션과 관련이 있습니다. 애플리케이션은 Azure AD 사용자에게만 보안되는 보호된 리소스를 제공합니다. 이 시나리오의 목적은 웹 애플리케이션이 Azure AD에 인증하고 사용자를 대신하여 Azure Maps REST API를 호출할 수 있도록 하는 것입니다.

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

## <a name="create-an-application-registration-in-azure-ad"></a>Azure AD에서 애플리케이션 등록 만들기

사용자가 로그인할 수 있도록 Azure AD에서 웹 애플리케이션을 만듭니다. 웹 애플리케이션은 Azure Maps REST API에 대한 사용자 액세스를 위임합니다.

1. Azure Portal에 있는 Azure 서비스의 목록에서 **Azure Active Directory** > **앱 등록** > **새 등록** 을 선택합니다.  

    > [!div class="mx-imgBorder"]
    > ![앱 등록](./media/how-to-manage-authentication/app-registration.png)

2. **이름** 을 입력하고, **계정 유형 지원** 을 선택하고, Azure AD가 토큰을 발행할 URL과 맵 컨트롤이 호스트되는 URL을 나타내는 리디렉션 URI를 제공합니다. 자세한 샘플은 [Azure Maps Azure AD 샘플](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples/tree/master/src/ImplicitGrant)을 참조하세요. 그런 다음, **등록** 을 선택합니다.  

3. 위임된 API 권한을 Azure Maps에 할당하려면 애플리케이션으로 이동합니다. 그런 다음, **앱 등록** 아래에서 **API 권한** > **권한 추가** 를 선택합니다. **내 조직에서 사용하는 API** 에서 **Azure Maps** 를 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![앱 API 권한 추가](./media/how-to-manage-authentication/app-permissions.png)

4. **Azure Maps에 액세스** 옆의 확인란을 선택한 다음 **권한 추가** 를 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![앱 API 권한 선택](./media/how-to-manage-authentication/select-app-permissions.png)

5. `oauth2AllowImplicitFlow`을 사용하도록 설정합니다. 이를 사용하려면 앱 등록의 **매니페스트** 섹션에서 `oauth2AllowImplicitFlow`를 `true`로 설정합니다.

6. 웹 SDK에서 사용할 앱 등록에서 Azure AD 앱 ID 및 Azure AD 테넌트 ID를 복사합니다. Azure Map 계정의 Azure AD 앱 등록 세부 정보 및 `x-ms-client-id`를 웹 SDK로 추가합니다.

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

7. 사용자 또는 그룹에 대해 Azure RBAC(Azure 역할 기반 액세스 제어)를 구성합니다. Azure [RBAC를 사용하도록 설정하려면 다음 섹션](#grant-role-based-access-for-users-to-azure-maps)을 참조하세요.
   
[!INCLUDE [grant role access to users](./includes/grant-rbac-users.md)]

## <a name="next-steps"></a>다음 단계

단일 페이지 애플리케이션 시나리오에 대한 추가 해석:
> [!div class="nextstepaction"]
> [단일 페이지 애플리케이션](../active-directory/develop/scenario-spa-overview.md)

Azure Maps 계정에 대한 API 사용 현황 메트릭을 확인하는 방법을 알아봅니다.
> [!div class="nextstepaction"]
> [사용 메트릭 보기](how-to-view-api-usage.md)

Azure Maps와 Azure AD를 통합하는 방법을 보여 주는 샘플을 살펴보세요.
> [!div class="nextstepaction"]
> [Azure Maps 샘플](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples/tree/master/src/ImplicitGrant)