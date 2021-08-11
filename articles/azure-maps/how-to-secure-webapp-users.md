---
title: 대화형 Single Sign-In을 사용하여 웹 애플리케이션을 보호하는 방법
titleSuffix: Azure Maps
description: OpenID Connect 프로토콜을 사용하여 Azure Maps Web SDK에서 Azure AD Single Sign-On을 지원하는 웹 애플리케이션을 구성하는 방법입니다.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: devx-track-js
ms.openlocfilehash: ebdc4b219e0840c18e6bef8ebfe9b8eefa8faf3b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92895585"
---
# <a name="secure-a-web-application-with-user-sign-in"></a>사용자 로그인을 통해 웹 애플리케이션 보호

다음 가이드에서는 웹 서버에서 호스트되고, 여러 비즈니스 시나리오를 유지 관리하고, 웹 서버에 배포되는 애플리케이션에 대해 설명합니다. 애플리케이션은 Azure AD 사용자에게만 보호된 리소스를 제공해야 합니다. 이 시나리오의 목적은 웹 애플리케이션이 Azure AD에 인증하고 사용자를 대신하여 Azure Maps REST API를 호출할 수 있도록 하는 것입니다.

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

## <a name="create-an-application-registration-in-azure-ad"></a>Azure AD에서 애플리케이션 등록 만들기

사용자가 로그인할 수 있도록 Azure AD에서 웹 애플리케이션을 만들어야 합니다. 그러면 이 웹 애플리케이션은 사용자에게 Azure Maps REST API에 대한 액세스 권한을 위임합니다.

1. Azure Portal에 있는 Azure 서비스의 목록에서 **Azure Active Directory** > **앱 등록** > **새 등록** 을 선택합니다.  

    > [!div class="mx-imgBorder"]
    > ![앱 등록](./media/how-to-manage-authentication/app-registration.png)

2. **이름** 을 입력하고, **계정 유형 지원** 을 선택하고, Azure AD가 토큰을 발행할 URL과 맵 컨트롤이 호스트되는 URL을 나타내는 리디렉션 URI를 제공합니다. 자세한 내용은 Azure AD [시나리오: 사용자를 서명하는 웹앱](../active-directory/develop/scenario-web-app-sign-user-overview.md)을 참조하세요. Azure AD 시나리오의 제공된 단계를 완료합니다.  

3. 애플리케이션 등록이 완료되면 애플리케이션 로그인이 사용자에 대해 작동하는지 확인합니다. 로그인이 작동하면 Azure Maps REST API에 대한 위임된 액세스 권한을 애플리케이션에 부여할 수 있습니다.
    
4.  위임된 API 권한을 Azure Maps에 할당하려면 애플리케이션으로 이동합니다. 그런 다음, **API 권한** > **권한 추가** 를선택합니다. **내 조직에서 사용하는 API** 에서 **Azure Maps** 를 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![앱 API 권한 추가](./media/how-to-manage-authentication/app-permissions.png)

5. **Azure Maps에 액세스** 옆의 확인란을 선택한 다음 **권한 추가** 를 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![앱 API 권한 선택](./media/how-to-manage-authentication/select-app-permissions.png)

6. 앱 등록을 애플리케이션 비밀로 구성하여 Azure Maps REST API를 호출하도록 웹 애플리케이션을 설정합니다. 자세한 단계는 [웹 API를 호출하는 웹앱: 앱 등록](../active-directory/develop/scenario-web-app-call-api-app-registration.md)을 참조하세요. 사용자를 대신하여 Azure AD에서 인증을 받으려면 비밀이 필요합니다. 앱 등록 인증서 또는 비밀은 웹 애플리케이션이 Azure AD에서 인증을 받기 위해 검색할 수 있는 보안 저장소에 저장되어야 합니다. 
   
   * 애플리케이션에서 Azure AD 앱 등록 및 비밀을 이미 구성한 경우 이 단계를 건너뛰어도 됩니다.

> [!Tip]
> 애플리케이션이 Azure 환경에서 호스트되는 경우 Azure Key Vault 비밀 또는 인증서에 액세스하기 위한 [액세스 토큰을 획득](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)하여 [Azure 리소스용 관리 ID](../active-directory/managed-identities-azure-resources/overview.md) 및 Azure Key Vault 인스턴스를 통해 비밀에 액세스하는 것이 좋습니다. Azure Key Vault에 연결하여 비밀을 검색하려면 [관리 ID를 통해 연결 자습서](../key-vault/general/tutorial-net-create-vault-azure-web-app.md)를 참조하세요.
   
7. 토큰에 액세스하도록 Azure Maps Web SDK에 대한 보안 토큰 엔드포인트를 구현합니다. 
   
   * 샘플 토큰 컨트롤러는 [Azure Maps Azure AD 샘플](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples/blob/master/src/OpenIdConnect/AzureMapsOpenIdConnectv1/AzureMapsOpenIdConnect/Controllers/TokenController.cs)을 참조하세요. 
   * AspNetCore가 아닌 구현이나 기타의 경우에는 Azure AD에서 [앱용 토큰 획득](../active-directory/develop/scenario-web-app-call-api-acquire-token.md) 설명서를 참조 하세요.
   * 보안 토큰 엔드포인트는 인증되고 권한이 부여된 사용자가 Azure Maps REST API를 호출하기 위한 액세스 토큰을 반환해야 합니다.

8. 사용자 또는 그룹에 대해 Azure RBAC(Azure 역할 기반 액세스 제어)를 구성합니다. [사용자에 대한 역할 기반 액세스 권한 부여](#grant-role-based-access-for-users-to-azure-maps)를 참조하세요.

9. 보안 토큰 엔드포인트에 액세스하도록 Azure Maps Web SDK로 웹 애플리케이션 페이지를 구성합니다. 

```javascript
var map = new atlas.Map("map", {
        center: [-122.33, 47.64],
        zoom: 12,
        language: "en-US",
        authOptions: {
            authType: "anonymous",
            clientId: "<insert>",  // azure map account client id
            getToken: function (resolve, reject, map) {
                var xhttp = new XMLHttpRequest();
                xhttp.open("GET", "/api/token", true); // the url path maps to the token endpoint.
                xhttp.onreadystatechange = function () {
                    if (this.readyState === 4 && this.status === 200) {
                        resolve(this.responseText);
                    } else if (this.status !== 200) {
                        reject(this.responseText);
                    }
                };

                xhttp.send();
            }
        }
    });
    map.events.add("tokenacquired", function () {
        console.log("token acquired");
    });
    map.events.add("error", function (err) {
        console.log(JSON.stringify(err.error));
    });
```

[!INCLUDE [grant role-based access to users](./includes/grant-rbac-users.md)]

## <a name="next-steps"></a>다음 단계

웹 애플리케이션 시나리오에 대한 추가 해석:
> [!div class="nextstepaction"]
> [시나리오: 사용자를 로그인하는 웹앱](../active-directory/develop/scenario-web-app-sign-user-overview.md)

Azure Maps 계정에 대한 API 사용 현황 메트릭을 확인하는 방법을 알아봅니다.
> [!div class="nextstepaction"]
> [사용 메트릭 보기](how-to-view-api-usage.md)

Azure Maps와 Azure AD를 통합하는 방법을 보여 주는 샘플을 살펴보세요.
> [!div class="nextstepaction"]
> [Azure Maps Azure AD 웹앱 샘플](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples/tree/master/src/OpenIdConnect)