---
title: 비대화형 로그인을 사용하여 단일 페이지 애플리케이션을 보호하는 방법
titleSuffix: Azure Maps
description: 비대화형 Azure RBAC(Azure 역할 기반 액세스 제어) 및 Azure Maps Web SDK를 사용하여 단일 페이지 애플리케이션을 구성하는 방법입니다.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: devx-track-js
ms.openlocfilehash: 9d2af0bf731ab069a8512cb10feccf5ba18d3fa0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101092721"
---
# <a name="how-to-secure-a-single-page-application-with-non-interactive-sign-in"></a>비대화형 로그인을 사용하여 단일 페이지 애플리케이션을 보호하는 방법

다음 가이드에서는 Azure Active Directory(Azure AD)를 사용하여 사용자가 Azure AD에 로그인할 수 없는 경우 Azure Maps 애플리케이션에 액세스 토큰을 제공하는 애플리케이션에 관련된 지침을 제공합니다. 이 흐름에서는 단일 페이지 웹 애플리케이션에서만 액세스할 수 있도록 보안을 설정해야 하는 웹 서비스를 호스트해야 합니다. Azure AD에 대한 인증을 수행할 수 있는 여러 구현이 있습니다. 이 가이드는 제품, Azure Function를 활용하여 액세스 토큰을 획득합니다.

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

> [!Tip]
> Azure 맵은 사용자 로그온/대화형 흐름에서 액세스 토큰을 지원할 수 있습니다. 대화형 흐름을 사용하면 액세스 해지 및 비밀 관리를 보다 제한된 범위로 사용할 수 있습니다.

## <a name="create-azure-function"></a>Azure 함수 만들기

Azure AD에 대한 인증을 담당하는 보안 웹 서비스 애플리케이션을 만듭니다. 

1. Azure Portal에서 함수 만들기 자세한 내용은 [Azure 함수 만들기](../azure-functions/functions-get-started.md)를 참조하세요.

2. 단일 페이지 웹 애플리케이션에서 액세스할 수 있도록 Azure 함수에서 CORS 정책을 구성합니다. 그러면 브라우저 클라이언트가 허용된 웹 애플리케이션 원본으로 보호됩니다. [CORS 기능 추가](../app-service/app-service-web-tutorial-rest-api.md#add-cors-functionality)를 봅니다.

3. Azure 함수에 [시스템 할당 ID를 추가](../app-service/overview-managed-identity.md?tabs=dotnet#add-a-system-assigned-identity)하여 Azure AD에 인증하는 서비스 사용자를 만들 수 있습니다.  

4. 시스템 할당 ID에 대한 역할 기반 액세스를 Azure Maps 계정에 부여합니다. 세부 정보는 [역할 기반 액세스 권한 부여](#grant-role-based-access)를 참조하세요.

5. 지원되는 메커니즘 중 하나 또는 REST 프로토콜과 함께 시스템 할당 ID를 사용하여 Azure Maps 액세스 토큰을 가져오기 위해 Azure 함수용 코드를 씁니다. [Azure 리소스 토큰 가져오기](../app-service/overview-managed-identity.md?tabs=dotnet#add-a-system-assigned-identity)를 봅니다.

    샘플 REST 프로토콜 예:

    ```http
    GET /MSI/token?resource=https://atlas.microsoft.com/&api-version=2019-08-01 HTTP/1.1
    Host: localhost:4141
    ```

    샘플 응답:

    ```http
    HTTP/1.1 200 OK
    Content-Type: application/json

    {
        "access_token": "eyJ0eXAi…",
        "expires_on": "1586984735",
        "resource": "https://atlas.microsoft.com/",
        "token_type": "Bearer",
        "client_id": "..."
    }
    ```

6. Azure 함수 HttpTrigger에 대한 보안 구성

   * [함수 액세스 키 만들기](../azure-functions/functions-bindings-http-webhook-trigger.md?tabs=csharp#authorization-keys)
   * 프로덕션 환경에서 Azure 함수에 대한 [HTTP 엔드포인트를 보호](../azure-functions/functions-bindings-http-webhook-trigger.md?tabs=csharp#secure-an-http-endpoint-in-production)합니다.
   
7. 웹 애플리케이션 Azure Maps Web SDK를 구성합니다. 

    ```javascript
    //URL to custom endpoint to fetch Access token
    var url = 'https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?code=<API_KEY>';

    var map = new atlas.Map('myMap', {
                center: [-122.33, 47.6],
                zoom: 12,
                language: 'en-US',
                view: "Auto",
            authOptions: {
                authType: "anonymous",
                clientId: "<insert>", // azure map account client id
                getToken: function(resolve, reject, map) {
                    fetch(url).then(function(response) {
                        return response.text();
                    }).then(function(token) {
                        resolve(token);
                    });
                }
            }
        });

        // use the following events to debug, you can remove them at any time.
        map.events.add("tokenacquired", function () {
            console.log("token acquired");
        });
        map.events.add("error", function (err) {
            console.log(JSON.stringify(err.error));
        });
    ```

## <a name="grant-role-based-access"></a>역할 기반 액세스 권한 부여

하나 이상의 Azure 역할 정의에 시스템 할당 ID를 할당하여 ’Azure RBAC(Azure 역할 기반 액세스 제어)’ 액세스 권한을 부여합니다. Azure Maps에 사용할 수 있는 Azure 역할 정의를 보려면 **Access control(IAM)** 로 이동합니다. **역할** 을 선택한 다음 *Azure Maps* 로 시작하는 역할을 검색합니다.

1. **Azure Maps 계정** 으로 이동합니다. **액세스 제어(IAM)**  > **역할 할당** 을 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![Azure RBAC를 사용하여 액세스 권한 부여](./media/how-to-manage-authentication/how-to-grant-rbac.png)

2. **역할 할당** 탭의 **역할** 에서 **Azure Maps Data Reader** 또는 **Azure Maps Data Contributor** 와 같은 기본 제공 Azure Maps 역할 정의를 선택합니다. **액세스 할당 대상** 에서 **함수 앱** 을 선택합니다. 이름으로 보안 주체를 선택합니다. 그런 다음 **저장** 을 선택합니다.

   * [Azure 역할 할당](../role-based-access-control/role-assignments-portal.md)에 대한 세부 정보를 참조하세요.

> [!WARNING]
> Azure Maps 기본 제공 역할 정의는 많은 Azure Maps REST API에 강력한 액세스 권한을 부여합니다. API 액세스를 최소한으로 제한하려면 [사용자 지정 역할 정의 만들기를 참조하고 시스템 할당 ID](../role-based-access-control/custom-roles.md)를 사용자 지정 역할 정의에 할당합니다. 이렇게 하면 애플리케이션에서 Azure Maps에 액세스하는 데 필요한 최소 권한을 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

단일 페이지 애플리케이션 시나리오에 대한 추가 이해:
> [!div class="nextstepaction"]
> [단일 페이지 애플리케이션](../active-directory/develop/scenario-spa-overview.md)

Azure Maps 계정에 대한 API 사용 현황 메트릭을 확인하는 방법을 알아봅니다.
> [!div class="nextstepaction"]
> [사용 메트릭 보기](how-to-view-api-usage.md)

Azure Maps와 Azure AD를 통합하는 방법을 보여 주는 다른 샘플을 살펴보세요.
> [!div class="nextstepaction"]
> [Azure Maps 샘플](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples/tree/master/src/ClientGrant)