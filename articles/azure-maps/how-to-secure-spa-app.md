---
title: 비 대화형 로그인을 사용 하 여 단일 페이지 응용 프로그램을 보호 하는 방법
titleSuffix: Azure Maps
description: 비 대화형 Azure AD 역할 기반 액세스 제어 및 Azure Maps 웹 SDK를 사용 하 여 단일 페이지 응용 프로그램을 구성 하는 방법입니다.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: devx-track-javascript
ms.openlocfilehash: 5b7f26a03c117620be7c16abaf689763e370e5ba
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87285699"
---
# <a name="how-to-secure-a-single-page-application-with-non-interactive-sign-in"></a>비 대화형 로그인을 사용 하 여 단일 페이지 응용 프로그램을 보호 하는 방법

다음 가이드에서는 azure ad (Azure Active Directory)를 사용 하 여 사용자가 Azure AD에 로그인 할 수 없는 경우 Azure Maps 응용 프로그램에 액세스 토큰을 제공 하는 응용 프로그램에 관련 된 지침을 제공 합니다. 이 흐름에서는 단일 페이지 웹 응용 프로그램 에서만 액세스할 수 있도록 보안을 설정 해야 하는 웹 서비스의 호스팅을 요구 합니다. Azure AD에 대 한 인증을 수행할 수 있는 여러 구현이 있습니다. 이 가이드는 제품, Azure 함수를 활용 하 여 액세스 토큰을 획득 합니다.

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

> [!Tip]
> Azure maps는 사용자 로그온/대화형 흐름에서 액세스 토큰을 지원할 수 있습니다. 대화형 흐름을 사용 하면 보다 제한 된 액세스 해지 및 비밀 관리 범위를 사용할 수 있습니다.

## <a name="create-azure-function"></a>Azure 함수 만들기

Azure AD에 대 한 인증을 담당 하는 보안 웹 서비스 응용 프로그램을 만듭니다. 

1. Azure Portal에서 함수를 만듭니다. 자세한 내용은 [Azure Function 만들기](https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function)를 참조 하세요.

2. 단일 페이지 웹 응용 프로그램에서 액세스할 수 있도록 Azure 함수에서 CORS 정책을 구성 합니다. 그러면 브라우저 클라이언트가 허용 된 웹 응용 프로그램 원본으로 보호 됩니다. [CORS 기능 추가](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-rest-api#add-cors-functionality)를 참조 하세요.

3. Azure 함수에 [시스템 할당 id를 추가](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet#add-a-system-assigned-identity) 하 여 azure AD에 인증 하는 서비스 주체를 만들 수 있습니다.  

4. 시스템 할당 id에 대 한 역할 기반 액세스를 Azure Maps 계정에 부여 합니다. 자세한 내용은 [역할 기반 액세스 권한 부여](#grant-role-based-access) 를 참조 하세요.

5. 지원 되는 메커니즘 또는 REST 프로토콜 중 하나를 사용 하 여 시스템 할당 id를 사용 하 여 Azure Maps 액세스 토큰을 가져오기 위해 Azure function에 대 한 코드를 작성 합니다. [Azure 리소스에 대 한 토큰 가져오기를](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet#add-a-system-assigned-identity) 참조 하세요.

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

6. Azure function HttpTrigger의 보안 구성

   * [함수 액세스 키 만들기](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook-trigger?tabs=csharp#authorization-keys)
   * 프로덕션 환경에서 Azure function에 대 한 [HTTP 끝점을 보호](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook-trigger?tabs=csharp#secure-an-http-endpoint-in-production) 합니다.
   
7. 웹 응용 프로그램 Azure Maps 웹 SDK를 구성 합니다. 

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

하나 이상의 Azure 역할 정의에 시스템 할당 id를 할당 하 여 RBAC ( *역할 기반 액세스 제어* )를 부여 합니다. Azure Maps 사용할 수 있는 RBAC 역할 정의를 보려면 **Access control (IAM)** 로 이동 합니다. **역할**을 선택한 다음 *Azure Maps*로 시작 하는 역할을 검색 합니다.

1. **Azure Maps 계정**으로 이동 합니다. **액세스 제어 (IAM)**  >  **역할 할당**을 선택 합니다.

    > [!div class="mx-imgBorder"]
    > ![RBAC 부여](./media/how-to-manage-authentication/how-to-grant-rbac.png)

2. **역할 할당** 탭의 **역할**에서 **Azure Maps 데이터 판독기** 또는 **Azure Maps 데이터 참가자**와 같은 기본 제공 Azure Maps 역할 정의를 선택 합니다. **액세스 할당**대상에서 **함수 앱**을 선택 합니다. 이름으로 보안 주체를 선택 합니다. 그런 다음, **저장**을 선택합니다.

   * [역할 할당 추가 또는 제거](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)에 대 한 세부 정보를 참조 하세요.

> [!WARNING]
> Azure Maps 기본 제공 역할 정의는 많은 Azure Maps REST Api에 대 한 매우 큰 권한 부여 액세스를 제공 합니다. Api 액세스를 최소한으로 제한 하려면 [사용자 지정 역할 정의 만들기를 참조 하 고 시스템 할당 id](https://docs.microsoft.com/azure/role-based-access-control/custom-roles) 를 사용자 지정 역할 정의에 할당 합니다. 이렇게 하면 응용 프로그램에서 Azure Maps 액세스 하는 데 필요한 최소 권한을 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

단일 페이지 응용 프로그램 시나리오에 대 한 추가 이해:
> [!div class="nextstepaction"]
> [단일 페이지 애플리케이션](https://docs.microsoft.com/azure/active-directory/develop/scenario-spa-overview)

Azure Maps 계정에 대 한 API 사용 메트릭을 찾습니다.
> [!div class="nextstepaction"]
> [사용 메트릭 보기](how-to-view-api-usage.md)

Azure Maps와 Azure AD를 통합 하는 방법을 보여 주는 다른 샘플을 살펴보세요.
> [!div class="nextstepaction"]
> [Azure Maps 샘플](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples/tree/master/src/ClientGrant)
