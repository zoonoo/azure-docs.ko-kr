---
title: 대화형 single sign-on을 사용 하 여 웹 응용 프로그램을 보호 하는 방법
titleSuffix: Azure Maps
description: Openid connect Connect 프로토콜을 사용 하 여 Azure Maps 웹 SDK에서 Azure AD single sign-on을 지 원하는 웹 응용 프로그램을 구성 하는 방법입니다.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: devx-track-js
ms.openlocfilehash: 50194341d1d34da4b02558461f532ae64b941b16
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91319625"
---
# <a name="secure-a-web-application-with-user-sign-in"></a>사용자 로그인을 사용 하 여 웹 응용 프로그램 보호

다음 가이드는 웹 서버에서 호스팅되는 응용 프로그램과 관련 하 여 여러 비즈니스 시나리오를 유지 관리 하 고 웹 서버에 배포 합니다. 응용 프로그램에는 Azure AD 사용자만 보호 되는 보호 된 리소스를 제공 해야 하는 요구 사항이 있습니다. 시나리오는 웹 응용 프로그램이 Azure AD에 인증 하 고 사용자를 대신 하 여 REST Api를 Azure Maps 호출할 수 있도록 하는 것입니다.

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

## <a name="create-an-application-registration-in-azure-ad"></a>Azure AD에서 응용 프로그램 등록 만들기

사용자가 로그인 하려면 Azure AD에서 웹 응용 프로그램을 만들어야 합니다. 이 웹 응용 프로그램은 Azure Maps REST Api에 대 한 사용자 액세스 권한을 위임 합니다.

1. Azure Portal의 Azure 서비스 목록에서 **Azure Active Directory**  >  **앱 등록**  >  **새 등록**을 선택 합니다.  

    > [!div class="mx-imgBorder"]
    > ![앱 등록](./media/how-to-manage-authentication/app-registration.png)

2. **이름을**입력 하 고, **지원 계정 유형을**선택 하 고, Azure AD에서 토큰을 발급 하는 URL을 나타내는 리디렉션 URI를 제공 하 고, 맵 컨트롤이 호스트 되는 url을 제공 합니다. 자세한 내용은 Azure AD [시나리오: 사용자를 로그인 하는 웹 앱을](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-overview)참조 하세요. Azure AD 시나리오에서 제공 된 단계를 완료 합니다.  

3. 응용 프로그램 등록이 완료 되 면 응용 프로그램 로그인이 사용자에 대해 작동 하는지 확인 합니다. 로그인이 작동 하면 응용 프로그램에 Azure Maps REST Api에 대 한 위임 된 액세스 권한을 부여할 수 있습니다.
    
4.  Azure Maps에 위임 된 API 권한을 할당 하려면 응용 프로그램으로 이동 합니다. 그런 다음 **API 권한**  >  **추가 권한 추가**를 선택 합니다. **내 조직에서 사용 하는 api**에서를 검색 하 고 **Azure Maps**를 선택 합니다.

    > [!div class="mx-imgBorder"]
    > ![앱 API 권한 추가](./media/how-to-manage-authentication/app-permissions.png)

5. **액세스 Azure Maps**옆의 확인란을 선택 하 고 **사용 권한 추가**를 선택 합니다.

    > [!div class="mx-imgBorder"]
    > ![앱 API 권한 선택](./media/how-to-manage-authentication/select-app-permissions.png)

6. 응용 프로그램 암호를 사용 하 여 앱 등록을 구성 하 여 웹 응용 프로그램이 Azure Maps REST Api를 호출할 수 있도록 합니다. 자세한 단계는 [Web api를 호출 하는 웹 앱: 앱 등록](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-app-registration)을 참조 하세요. 사용자를 대신 하 여 Azure AD에 인증 하려면 암호가 필요 합니다. 앱 등록 인증서 또는 암호는 웹 응용 프로그램이 Azure AD에 인증 하기 위해 검색 하기 위해 보안 저장소에 저장 되어야 합니다. 
   
   * 응용 프로그램에서 이미 Azure AD 앱 등록 및 비밀을 구성한 경우이 단계를 건너뛸 수 있습니다.

> [!Tip]
> 응용 프로그램이 Azure 환경에서 호스트 되는 경우 Azure Key Vault 된 비밀 또는 인증서에 액세스 하기 위한 [액세스 토큰을 획득](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-use-vm-token) 하 여 azure 리소스 및 Azure Key Vault 인스턴스에 [대해 관리 되는 id](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) 를 사용 하 여 암호에 액세스 하는 것이 좋습니다. Azure Key Vault에 연결 하 여 비밀을 검색 하려면 [관리 id를 통한 연결 자습서](https://docs.microsoft.com/azure/key-vault/general/tutorial-net-create-vault-azure-web-app)를 참조 하세요.
   
7. 토큰에 액세스할 Azure Maps 웹 SDK에 대 한 보안 토큰 끝점을 구현 합니다. 
   
   * 샘플 토큰 컨트롤러는 [Azure Maps AZURE AD 샘플](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples/blob/master/src/OpenIdConnect/AzureMapsOpenIdConnectv1/AzureMapsOpenIdConnect/Controllers/TokenController.cs)을 참조 하세요. 
   * AspNetCore이 아닌 구현이 나 기타 경우에는 Azure AD에서 [앱에 대 한 토큰 획득](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token) 설명서를 참조 하세요.
   * 보안 토큰 끝점은 인증 되 고 권한이 부여 된 사용자에 대 한 액세스 토큰을 반환 하 Azure Maps REST Api를 호출 해야 합니다.

8. 사용자 또는 그룹에 대 한 Azure 역할 기반 액세스 제어를 구성 합니다. [사용자에 대 한 역할 기반 액세스 권한 부여를](#grant-role-based-access-for-users-to-azure-maps)참조 하세요.

9. Azure Maps 웹 SDK를 사용 하 여 웹 응용 프로그램 페이지를 구성 하 여 보안 토큰 끝점에 액세스 합니다. 

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

[!INCLUDE [grant role access to users](./includes/grant-rbac-users.md)]

## <a name="next-steps"></a>다음 단계

웹 응용 프로그램 시나리오에 대 한 추가 이해:
> [!div class="nextstepaction"]
> [시나리오: 사용자에 게 로그인 하는 웹 앱](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-overview)

Azure Maps 계정에 대 한 API 사용 메트릭을 찾습니다.
> [!div class="nextstepaction"]
> [사용 메트릭 보기](how-to-view-api-usage.md)

Azure Maps와 Azure AD를 통합 하는 방법을 보여 주는 샘플을 탐색 합니다.
> [!div class="nextstepaction"]
> [Azure Maps Azure AD 웹 앱 샘플](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples/tree/master/src/OpenIdConnect)
