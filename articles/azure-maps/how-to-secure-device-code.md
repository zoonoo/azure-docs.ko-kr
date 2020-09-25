---
title: Azure AD 및 Azure Maps REST Api를 사용 하 여 입력 제한 장치를 보호 하는 방법
titleSuffix: Azure Maps
description: Azure AD에 대 한 로그인을 지원 하 고 REST Api를 Azure Maps 호출 하는 브라우저 없는 응용 프로그램을 구성 하는 방법입니다.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 7e61a881470b206981b65e175c1f7f40b161ebf8
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91319761"
---
# <a name="secure-an-input-constrained-device-with-azure-ad-and-azure-maps-rest-apis"></a>Azure AD 및 Azure Maps REST Api를 사용 하 여 입력 제한 장치 보호

이 가이드에서는 비밀을 안전 하 게 저장 하거나 브라우저 입력을 수락할 수 없는 공용 응용 프로그램 또는 장치를 보호 하는 방법을 설명 합니다. 이러한 유형의 응용 프로그램은 IoT 또는 사물 인터넷 범주에 속합니다. 이러한 응용 프로그램의 몇 가지 예로는 스마트 TV 장치 또는 응용 프로그램을 내보내는 센서 데이터가 포함 될 수 있습니다. 

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

## <a name="create-an-application-registration-in-azure-ad"></a>Azure AD에서 응용 프로그램 등록 만들기

> [!NOTE]
> * **필수 조건 읽기:** [시나리오: 웹 api를 호출 하는 데스크톱 앱](https://docs.microsoft.com/azure/active-directory/develop/scenario-desktop-overview)
> * 다음 시나리오는 토큰을 획득 하기 위해 웹 브라우저가 포함 되지 않는 장치 코드 흐름을 사용 합니다.

Azure ad에서 Azure AD 로그인을 사용 하도록 설정 하려면 Azure AD에서 장치 기반 응용 프로그램을 만듭니다. 이 응용 프로그램에 Azure Maps REST Api에 대 한 액세스 권한이 부여 됩니다.

1. Azure Portal의 Azure 서비스 목록에서 **Azure Active Directory**  >  **앱 등록**  >  **새 등록**을 선택 합니다.  

    > [!div class="mx-imgBorder"]
    > ![앱 등록](./media/how-to-manage-authentication/app-registration.png)

2. **이름을**입력 하 고 **이 조직 디렉터리의 계정만** **지원 되는 계정 유형**으로 선택 합니다. **리디렉션 uri**에서 **공용 클라이언트/네이티브 (모바일 & 데스크톱)** 를 지정 하 고 `https://login.microsoftonline.com/common/oauth2/nativeclient` 값에을 추가 합니다. 자세한 내용은 [웹 api를 호출 하는 AZURE AD 데스크톱 앱: 앱 등록](https://docs.microsoft.com/azure/active-directory/develop/scenario-desktop-app-registration)을 참조 하세요. 그런 다음 응용 프로그램을 **등록** 합니다.

    > [!div class="mx-imgBorder"]
    > ![이름 및 리디렉션 uri에 대 한 앱 등록 정보 추가](./media/azure-maps-authentication/devicecode-app-registration.png)

3. **인증** 으로 이동 하 고 **응용 프로그램을 공용 클라이언트로 처리**를 사용 하도록 설정 합니다. 그러면 Azure AD에서 장치 코드 인증을 사용할 수 있습니다.
    
    > [!div class="mx-imgBorder"]
    > ![앱 등록을 공용 클라이언트로 사용](./media/azure-maps-authentication/devicecode-public-client.png)

4.  Azure Maps에 위임 된 API 권한을 할당 하려면 응용 프로그램으로 이동 합니다. 그런 다음 **API 권한**  >  **추가 권한 추가**를 선택 합니다. **내 조직에서 사용 하는 api**에서를 검색 하 고 **Azure Maps**를 선택 합니다.

    > [!div class="mx-imgBorder"]
    > ![앱 API 권한 추가](./media/how-to-manage-authentication/app-permissions.png)

5. **액세스 Azure Maps**옆의 확인란을 선택 하 고 **사용 권한 추가**를 선택 합니다.

    > [!div class="mx-imgBorder"]
    > ![앱 API 권한 선택](./media/how-to-manage-authentication/select-app-permissions.png)

6. 사용자 또는 그룹에 대 한 Azure 역할 기반 액세스 제어를 구성 합니다. [Azure Maps에 대 한 역할 기반 액세스 권한을 사용자에 게 부여를](#grant-role-based-access-for-users-to-azure-maps)참조 하세요.

7. 응용 프로그램에서 토큰 흐름을 획득 하는 코드를 추가 합니다. 구현에 대 한 자세한 내용은 [장치 코드 흐름](https://docs.microsoft.com/azure/active-directory/develop/scenario-desktop-acquire-token#device-code-flow)을 참조 하세요. 토큰을 획득 하는 경우 `user_impersonation` 이전 단계에서 선택한 범위를 참조 합니다.

> [!Tip]
> MSAL (Microsoft 인증 라이브러리)을 사용 하 여 액세스 토큰을 획득 합니다. [웹 api를 호출 하는 데스크톱 앱](https://docs.microsoft.com/azure/active-directory/develop/scenario-desktop-app-configuration) 의 권장 사항 참조: 코드 구성

8. Azure AD에서 획득 한 토큰을 사용 하 여 HTTP 요청을 작성 하 고 유효한 HTTP 클라이언트를 사용 하 여 요청을 보냅니다.

### <a name="sample-request"></a>샘플 요청
다음은 중심점 및 반지름을 사용 하 여 원 기 하 도형으로 표시 되는 간단한 지 오를 업로드 하기 위한 샘플 요청 본문입니다.

```http
POST /mapData/upload?api-version=1.0&dataFormat=geojson
Host: atlas.microsoft.com
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

### <a name="sample-response"></a>샘플 응답:

Headers:
```http
Location: https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0
Access-Control-Expose-Headers: Location
```

본문:
```json
{
  "operationId": "{operationId}",
  "status": "Succeeded",
  "created": "2020-01-02 1:02:03 AM +00:00",
  "resourceLocation": "https://atlas.microsoft.com/mapData/metadata/{resourceId}?api-version=1.0"
}
```

[!INCLUDE [grant role access to users](./includes/grant-rbac-users.md)]

## <a name="next-steps"></a>다음 단계

Azure Maps 계정에 대 한 API 사용 메트릭을 찾습니다.
> [!div class="nextstepaction"]
> [사용 메트릭 보기](how-to-view-api-usage.md)
