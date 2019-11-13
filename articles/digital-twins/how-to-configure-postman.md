---
title: Postman을 구성 하는 방법-Azure Digital Twins | Microsoft Docs
description: Postman을 구성 하 고 사용 하 여 Azure Digital Twins Api를 테스트 하는 방법을 알아봅니다.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 09/30/2019
ms.openlocfilehash: 5a357a246f2ba6c294b107e447218f386623f5c5
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74014189"
---
# <a name="how-to-configure-postman-for-azure-digital-twins"></a>Azure Digital Twins용 Postman을 구성하는 방법

이 문서에서는 Azure Digital Twins 관리 API와 상호 작용하고 테스트하기 위해 Postman REST 클라이언트를 구성하는 방법을 설명합니다. 구체적으로 다음을 설명합니다.

* OAuth 2.0 암시적 허용 흐름을 사용하도록 Azure Active Directory 애플리케이션을 구성하는 방법
* Postman REST 클라이언트를 사용하여 관리 API에 토큰-관련 HTTP 요청을 수행하는 방법
* Postman을 사용하여 관리 API에 대해 다중 파트 POST 요청을 수행하는 방법

## <a name="postman-summary"></a>Postman 요약

[Postman](https://www.getpostman.com/)과 같은 REST 클라이언트 도구를 사용하여 Azure Digital Twins를 시작하여 로컬 테스트 환경을 준비합니다. Postman 클라이언트를 통해 복잡한 HTTP 요청을 신속하게 만들 수 있습니다. [www.getpostman.com/apps](https://www.getpostman.com/apps)로 이동하여 Postman 클라이언트의 데스크톱 버전을 다운로드합니다.

[Postman](https://www.getpostman.com/)은 유용한 데스크톱 및 플러그 인 기반 GUI에 대한 주요 HTTP 요청을 찾는 REST 테스트 도구입니다.

Postman 클라이언트를 통해 솔루션 개발자는 HTTP 요청의 종류(*POST*, *GET*, *UPDATE*, *PATCH* 및 *DELETE*), 호출할 API 엔드포인트 및 SSL의 사용을 지정할 수 있습니다. Postman은 또한 HTTP 요청 헤더, 매개 변수, 양식 데이터 및 본문 추가를 지원합니다.

## <a name="configure-azure-active-directory-to-use-the-oauth-20-implicit-grant-flow"></a>OAuth 2.0 암시적 허용 흐름을 사용하도록 Azure Active Directory를 구성합니다.

OAuth 2.0 암시적 허용 흐름을 사용하도록 Azure Active Directory 앱을 구성합니다.

1. 앱 등록을 위한 **API 사용 권한** 창을 엽니다. **사용 권한 추가** 단추를 선택합니다. **API 사용 권한 요청** 창에서 **내 조직이 사용하는 API** 탭을 선택한 후, 다음을 검색합니다.
    
    1. `Azure Digital Twins`. **Azure Digital Twins** API를 선택합니다.

        [![Search API 또는 Azure Digital Twins](../../includes/media/digital-twins-permissions/aad-aap-search-api-dt.png)](../../includes/media/digital-twins-permissions/aad-aap-search-api-dt.png#lightbox)

    1. 또는 `Azure Smart Spaces Service`를 검색합니다. **Azure 스마트 공간 서비스** API를 선택합니다.

        [![Azure 스마트 공간용 Search API](../../includes/media/digital-twins-permissions/aad-app-search-api.png)](../../includes/media/digital-twins-permissions/aad-app-search-api.png#lightbox)

    > [!IMPORTANT]
    > 표시되는 Azure AD API 이름 및 ID는 테넌트에 따라 다릅니다.
    > * 테스트 테넌트 및 고객 계정은 `Azure Digital Twins`를 검색해야 합니다.
    > * 다른 Microsoft 계정은 `Azure Smart Spaces Service`를 검색해야 합니다.

1. 동일한 **API 사용 권한 요청** 창에 선택한 API가 **Azure Digital Twins**로 표시됩니다. **읽기(1)** 드롭다운을 선택한 다음, **Read.Write** 확인란을 선택합니다. **사용 권한 추가** 단추를 선택합니다.

    [![API 권한 추가](../../includes/media/digital-twins-permissions/aad-app-req-permissions.png)](../../includes/media/digital-twins-permissions/aad-app-req-permissions.png#lightbox)

1. 조직의 설정에 따라 이 API에 대한 관리자 액세스 권한을 부여하려면 추가 단계를 수행해야 할 수도 있습니다. 자세한 내용은 관리자에게 문의하세요. 관리자 액세스가 승인되면 **API 사용 권한** 창의 **관리자 동의 필요** 열이 API에 대해 다음과 유사하게 표시됩니다.

    [![관리자 동의 승인](../../includes/media/digital-twins-permissions/aad-app-admin-consent.png)](../../includes/media/digital-twins-permissions/aad-app-admin-consent.png#lightbox)


1. **매니페스트** 를 선택 하 여 앱에 대 한 응용 프로그램 매니페스트를 엽니다. *oauth2AllowImplicitFlow*를 `true`로 설정합니다.

    [암시적 흐름 ![Azure Active Directory](media/how-to-configure-postman/implicit-flow.png)](media/how-to-configure-postman/implicit-flow.png#lightbox)

1. **회신 URL**을 `https://www.getpostman.com/oauth2/callback`으로 구성합니다.

    [회신 URL ![Azure Active Directory](media/how-to-configure-postman/reply-url.png)](media/how-to-configure-postman/reply-url.png#lightbox)

1. Azure Active Directory 앱의 **애플리케이션 ID**를 복사하고 유지합니다. 이어지는 단계에서 사용됩니다.

   [![Azure Active Directory 애플리케이션 ID](../../includes/media/digital-twins-permissions/aad-app-reg-app-id.png)](../../includes/media//digital-twins-permissions/aad-app-reg-app-id.png#lightbox)


## <a name="obtain-an-oauth-20-token"></a>OAuth 2.0 토큰 가져오기

[!INCLUDE [digital-twins-management-api](../../includes/digital-twins-management-api.md)]

Azure Active Directory 토큰을 가져오기 위해 Postman을 설정 하 고 구성 합니다. 그런 다음, 획득한 토큰을 사용하여 Azure Digital Twins로 인증된 HTTP 요청을 만듭니다.

1. [www.getpostman.com](https://www.getpostman.com/)으로 이동하여 앱을 다운로드합니다.
1. **권한 부여 URL**이 올바른지 확인합니다. 다음과 같은 형식이어야 합니다.

    ```plaintext
    https://login.microsoftonline.com/YOUR_AZURE_TENANT.onmicrosoft.com/oauth2/authorize?resource=0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    ```

    | 이름  | 다음 항목으로 교체 | 예 |
    |---------|---------|---------|
    | YOUR_AZURE_TENANT | 테넌트 또는 조직의 이름 | `microsoft` |

1. **권한 부여 탭**을 선택하고, **OAuth 2.0**을 선택한 다음, **새 액세스 토큰 가져오기**를 선택합니다.

    | 필드  | 값 |
    |---------|---------|
    | 권한 부여 유형 | `Implicit` |
    | 콜백 URL | `https://www.getpostman.com/oauth2/callback` |
    | 인증 URL | **2 단계의** **권한 부여 URL** 사용 |
    | 클라이언트 ID | 이전 섹션에서 만들었거나 다시 사용한 Azure Active Directory 앱의 **응용 프로그램 ID** 를 사용 합니다. |
    | 범위 | 비워 둠 |
    | State | 비워 둠 |
    | 클라이언트 인증 | `Send as Basic Auth header` |

1. 클라이언트는 이제 다음과 같아야 합니다.

    [![Postman 클라이언트 예제](media/how-to-configure-postman/postman-oauth-token.png)](media/how-to-configure-postman/postman-oauth-token.png#lightbox)

1. **토큰 요청**을 선택합니다.

    >[!TIP]
    >"OAuth 2를 완료할 수 없습니다."라는 오류 메시지가 나타나면 다음과 같이 시도하세요.
    > * Postman을 닫은 후 다시 열고, 다시 시도합니다.
  
1. 아래로 스크롤하고 **토큰 사용**을 선택합니다.

## <a name="make-a-multipart-post-request"></a>다중 파트 POST 요청 수행

이전 단계를 완료한 후 인증된 HTTP 다중 파트 POST 요청을 수행하도록 Postman을 구성합니다.

1. **헤더** 탭 아래에서 값이 **인 HTTP 요청 헤더 키** Content-Type`multipart/mixed`을 추가합니다.

   [![콘텐츠 형식 다중 파트/혼합](media/how-to-configure-postman/content-type.png)](media/how-to-configure-postman/content-type.png#lightbox)

1. 텍스트가 아닌 데이터를 파일로 직렬화합니다. JSON 데이터는 JSON 파일로 저장됩니다.
1. **본문** 탭에서 `form-data`를 선택 합니다. 
1. **키** 이름을 할당 하 여 각 파일을 추가 하 고 `file`를 선택 합니다.
1. 그런 후 **파일 선택** 단추를 사용하여 각 파일을 선택합니다.

   [![Postman 클라이언트 예제](media/how-to-configure-postman/form-body.png)](media/how-to-configure-postman/form-body.png#lightbox)

   >[!NOTE]
   > * Postman 클라이언트에서는 다중 파트 청크에 **Content-Type** 또는 **Content-Disposition**을 수동으로 할당할 필요가 없습니다.
   > * 각 파트에 대해 해당 헤더를 지정할 필요가 없습니다.
   > * 전체 요청에 대해서는 `multipart/mixed` 또는 다른 적절한 **Content-Type**을 선택해야 합니다.

1. 마지막으로, **보내기** 를 선택 하 여 MULTIPART HTTP POST 요청을 제출 합니다.

## <a name="next-steps"></a>다음 단계

- Digital Twins 관리 API 및 이를 사용하는 방법에 대해 자세히 알아보려면 [Azure Digital Twins 관리 API 사용 방법](how-to-navigate-apis.md)을 읽어보세요.

- 다중 파트 요청을 사용하여 [Azure Digital Twins 엔터티에 blob을 추가](./how-to-add-blobs.md)합니다.

- Management API를 사용하여 인증에 대해 알아보려면 [API를 사용하여 인증](./security-authenticating-apis.md)을 참조하세요.
