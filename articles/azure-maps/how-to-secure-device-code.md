---
title: Azure AD 및 Azure Maps REST API를 사용하여 입력 제한 디바이스를 보호하는 방법
titleSuffix: Azure Maps
description: Azure AD에 대한 로그인을 지원하고 Azure Maps REST API를 호출하는 브라우저가 없는 애플리케이션을 구성하는 방법입니다.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 50e3ce1ef83c33900895e6aa3e5fc925b2004d7d
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/02/2021
ms.locfileid: "110791351"
---
# <a name="secure-an-input-constrained-device-with-azure-ad-and-azure-maps-rest-apis"></a>Azure AD 및 Azure Maps REST API를 사용하여 입력 제한 디바이스 보호

이 가이드에서는 비밀을 안전하게 저장하거나 브라우저 입력을 수락할 수 없는 퍼블릭 애플리케이션이나 디바이스를 보호하는 방법을 설명합니다. 이 유형의 애플리케이션은 IoT 또는 사물 인터넷 범주에 속합니다. 이 애플리케이션의 몇 가지 예제에는 스마트 TV 디바이스나 센서 데이터 내보내기 애플리케이션이 포함될 수 있습니다. 

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

## <a name="create-an-application-registration-in-azure-ad"></a>Azure AD에서 애플리케이션 등록 만들기

> [!NOTE]
> * **필수 조건 읽기:** [시나리오: 웹 API를 호출하는 데스크톱 앱](../active-directory/develop/scenario-desktop-overview.md)
> * 다음 시나리오는 토큰을 획득하기 위해 웹 브라우저가 포함되지 않은 디바이스 코드 흐름을 사용합니다.

Azure AD에서 디바이스 기반 애플리케이션을 만들어 Azure AD 로그인을 사용하도록 설정합니다. 이 애플리케이션에는 Azure Maps REST API에 대한 액세스 권한이 부여됩니다.

1. Azure Portal에 있는 Azure 서비스의 목록에서 **Azure Active Directory** > **앱 등록** > **새 등록** 을 선택합니다.  

    > [!div class="mx-imgBorder"]
    > ![앱 등록](./media/how-to-manage-authentication/app-registration.png)

2. **이름** 을 입력하고 **지원되는 계정 유형** 으로 **이 조직 디렉터리의 계정만** 을 선택합니다. **리디렉션 URI** 에서 **퍼블릭 클라이언트/기본(모바일 및 데스크톱)** 을 지정한 다음, `https://login.microsoftonline.com/common/oauth2/nativeclient`를 값에 추가합니다. 자세한 내용은 [웹 API를 호출하는 Azure AD 데스크톱 앱: 앱 등록](../active-directory/develop/scenario-desktop-app-registration.md)을 참조하세요. 그런 다음, 애플리케이션을 **등록** 합니다.

    > [!div class="mx-imgBorder"]
    > ![이름 및 리디렉션 URI의 앱 등록 세부 정보 추가](./media/azure-maps-authentication/devicecode-app-registration.png)

3. **인증** 으로 이동하고 **애플리케이션을 퍼블릭 클라이언트로 처리** 를 사용하도록 설정합니다. 그러면 Azure AD에서 디바이스 코드 인증이 사용됩니다.

    > [!div class="mx-imgBorder"]
    > ![앱 등록을 퍼블릭 클라이언트로 사용](./media/azure-maps-authentication/devicecode-public-client.png)

4. 위임된 API 권한을 Azure Maps에 할당하려면 애플리케이션으로 이동합니다. 그런 다음, **API 권한** > **권한 추가** 를 선택합니다. **내 조직에서 사용하는 API** 에서 **Azure Maps** 를 검색하고 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![앱 API 권한 추가](./media/how-to-manage-authentication/app-permissions.png)

5. **Azure Maps에 액세스** 옆의 확인란을 선택한 다음 **권한 추가** 를 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![앱 API 권한 선택](./media/how-to-manage-authentication/select-app-permissions.png)

6. 사용자 또는 그룹에 대해 Azure RBAC(Azure 역할 기반 액세스 제어)를 구성합니다. [사용자에게 Azure Maps에 대한 역할 기반 액세스 권한 부여](#grant-role-based-access-for-users-to-azure-maps)를 참조하세요.

7. 애플리케이션에서 토큰 흐름을 획득하기 위한 코드를 추가합니다. 구현 세부 정보는 [디바이스 코드 흐름](../active-directory/develop/scenario-desktop-acquire-token.md#device-code-flow)을 참조하세요. 토큰을 획득할 때 이전 단계에서 선택한 범위인 `user_impersonation`을 참조합니다.

    > [!Tip]
    > MSAL(Microsoft 인증 라이브러리)을 사용하여 액세스 토큰을 획득합니다.
    > [웹 API를 호출하는 데스크톱 앱: 코드 구성](../active-directory/develop/scenario-desktop-app-configuration.md)의 권장 사항을 참조하세요.

8. Azure AD에서 획득한 토큰을 사용하여 HTTP 요청을 작성하고 유효한 HTTP 클라이언트를 사용하여 전송된 요청을 보냅니다.

### <a name="sample-request"></a>샘플 요청

다음은 중심점과 반경을 사용하여 원 기하 도형으로 표시된 단순 지오펜스를 업로드하기 위한 샘플 요청 본문입니다.

```http
POST /mapData?api-version=2.0&dataFormat=geojson
Host: us.atlas.microsoft.com
x-ms-client-id: 30d7cc….9f55
Authorization: Bearer eyJ0e….HNIVN
```

 아래 샘플 요청 본문은 GeoJSON에 있습니다.

```json
{
    "type": "FeatureCollection",
    "features": [{
        "type": "Feature",
        "geometry": {
            "type": "Point",
            "coordinates": [-122.126986, 47.639754]
        },
        "properties": {
            "geometryId": "001",
            "radius": 500
        }
    }]
}
```

### <a name="sample-response-header"></a>응답 헤더 샘플

```http
Operation-Location: https://us.atlas.microsoft.com/mapData/operations/{udid}?api-version=2.0
Access-Control-Expose-Headers: Operation-Location
```


[!INCLUDE [grant role-based access to users](./includes/grant-rbac-users.md)]

## <a name="next-steps"></a>다음 단계

Azure Maps 계정에 대한 API 사용 현황 메트릭을 확인하는 방법을 알아봅니다.
> [!div class="nextstepaction"]
> [사용 메트릭 보기](how-to-view-api-usage.md)