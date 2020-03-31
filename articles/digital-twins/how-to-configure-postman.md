---
title: 우체부 구성 방법 - Azure 디지털 트윈스 | 마이크로 소프트 문서
description: Postman을 구성하고 사용하여 Azure 디지털 Twins API를 테스트하는 방법을 알아봅니다.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: ffcfb4f6ec5f6c654d0b243af85034ab575e0d88
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297165"
---
# <a name="how-to-configure-postman-for-azure-digital-twins"></a>Azure Digital Twins용 Postman을 구성하는 방법

이 문서에서는 Azure Digital Twins 관리 API와 상호 작용하고 테스트하기 위해 Postman REST 클라이언트를 구성하는 방법을 설명합니다. 구체적으로 다음을 설명합니다.

* OAuth 2.0 암시적 허용 흐름을 사용하도록 Azure Active Directory 애플리케이션을 구성하는 방법
* Postman REST 클라이언트를 사용하여 관리 API에 토큰-관련 HTTP 요청을 수행하는 방법
* Postman을 사용하여 관리 API에 대해 다중 파트 POST 요청을 수행하는 방법

## <a name="postman-summary"></a>Postman 요약

[Postman](https://www.getpostman.com/)과 같은 REST 클라이언트 도구를 사용하여 Azure Digital Twins를 시작하여 로컬 테스트 환경을 준비합니다. Postman 클라이언트를 통해 복잡한 HTTP 요청을 신속하게 만들 수 있습니다. [www.getpostman.com/apps](https://www.getpostman.com/apps)로 이동하여 Postman 클라이언트의 데스크톱 버전을 다운로드합니다.

[Postman](https://www.getpostman.com/)은 유용한 데스크톱 및 플러그 인 기반 GUI에 대한 주요 HTTP 요청을 찾는 REST 테스트 도구입니다.

Postman 클라이언트를 통해 솔루션 개발자는 HTTP*요청(POST,* *GET*, *UPDATE,* *PATCH*및 *DELETE),* 호출할 API 끝점 및 TLS 사용의 종류를 지정할 수 있습니다. Postman은 또한 HTTP 요청 헤더, 매개 변수, 양식 데이터 및 본문 추가를 지원합니다.

## <a name="configure-azure-active-directory-to-use-the-oauth-20-implicit-grant-flow"></a>OAuth 2.0 암시적 허용 흐름을 사용하도록 Azure Active Directory를 구성합니다.

1. [빠른 시작의](quickstart-view-occupancy-dotnet.md#set-permissions-for-your-app) 단계를 수행하여 Azure Active Directory 응용 프로그램을 만들고 구성합니다. 또는 기존 앱 등록을 다시 사용할 수 있습니다.

    [![새 우체부 리디렉션 URI 구성](media/how-to-configure-postman/authentication-redirect-uri.png)](media/how-to-configure-postman/authentication-redirect-uri.png#lightbox)

1. 이제 URI **리디렉션을** 에 `https://www.getpostman.com/oauth2/callback`추가합니다.

1. OAuth 2.0 암시적 권한 부여 흐름을 사용할 수 있도록 **암시적 권한 부여** > **Access 토큰** 확인란을 선택합니다. **구성을**선택한 다음 **저장합니다.**

1. Azure Active Directory 앱의 **클라이언트 ID를** 복사합니다.

## <a name="obtain-an-oauth-20-token"></a>OAuth 2.0 토큰 가져오기

[!INCLUDE [digital-twins-management-api](../../includes/digital-twins-management-api.md)]

Azure Active Directory 토큰을 가져오도록 Postman을 설정하고 구성합니다. 그런 다음, 획득한 토큰을 사용하여 Azure Digital Twins로 인증된 HTTP 요청을 만듭니다.

1. **권한 부여 URL**이 올바른지 확인합니다. 다음과 같은 형식이어야 합니다.

    ```plaintext
    https://login.microsoftonline.com/YOUR_AZURE_TENANT.onmicrosoft.com/oauth2/authorize?resource=0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    ```

    | 이름  | 다음 항목으로 교체 | 예제 |
    |---------|---------|---------|
    | YOUR_AZURE_TENANT | 테넌트 또는 조직의 이름입니다. Azure Active Directory 앱 등록의 영숫자 **테넌트 ID** 대신 사용자 친화적인 이름을 사용합니다. | `microsoft` |

1. [www.getpostman.com](https://www.getpostman.com/)으로 이동하여 앱을 다운로드합니다.

1. GET 요청을 하고 싶습니다. 권한 **부여** 탭을 선택하고 OAuth 2.0을 선택한 다음 **새 액세스 토큰 받기를**선택합니다.

    | 필드  | 값 |
    |---------|---------|
    | 권한 부여 유형 | `Implicit` |
    | 콜백 URL | `https://www.getpostman.com/oauth2/callback` |
    | 인증 URL | **1단계에서** **권한 부여 URL** 사용 |
    | 클라이언트 ID | 이전 섹션에서 만들거나 다시 사용한 Azure Active Directory 앱에 **응용 프로그램 ID** 사용 |
    | Scope | 비워 둠 |
    | 시스템 상태 | 비워 둠 |
    | 클라이언트 인증 | `Send as Basic Auth header` |

1. 클라이언트는 이제 다음과 같아야 합니다.

    [![우체부 클라이언트 토큰 예제](media/how-to-configure-postman/configure-postman-oauth-token.png)](media/how-to-configure-postman/configure-postman-oauth-token.png#lightbox)

1. **토큰 요청**을 선택합니다.
  
1. 아래로 스크롤하고 **토큰 사용**을 선택합니다.

## <a name="make-a-multipart-post-request"></a>다중 파트 POST 요청 수행

이전 단계를 완료한 후 인증된 HTTP 다중 파트 POST 요청을 수행하도록 Postman을 구성합니다.

1. **헤더** 탭에서 HTTP 요청 헤더 키 **콘텐츠 유형값을** 추가합니다. `multipart/mixed`

   [![콘텐츠 유형 다중 분할/혼합 지정](media/how-to-configure-postman/configure-postman-content-type.png)](media/how-to-configure-postman/configure-postman-content-type.png#lightbox)

1. 텍스트가 아닌 데이터를 파일로 직렬화합니다. JSON 데이터는 JSON 파일로 저장됩니다.
1. **본문** 탭에서 `form-data`을 선택합니다. 
1. **키** 이름을 할당하고 을 선택하여 `File`각 파일을 추가합니다.
1. 그런 후 **파일 선택** 단추를 사용하여 각 파일을 선택합니다.

   [![우체부 클라이언트 양식 본문 예제](media/how-to-configure-postman/configure-postman-form-body.png)](media/how-to-configure-postman/configure-postman-form-body.png#lightbox)

   >[!NOTE]
   > * Postman 클라이언트에서는 다중 파트 청크에 **Content-Type** 또는 **Content-Disposition**을 수동으로 할당할 필요가 없습니다.
   > * 각 파트에 대해 해당 헤더를 지정할 필요가 없습니다.
   > * 전체 요청에 대해서는 `multipart/mixed` 또는 다른 적절한 **Content-Type**을 선택해야 합니다.

1. 마지막으로 **보내기를** 선택하여 다중 부분 HTTP POST 요청을 제출합니다. 요청의 `200` 상태 `201` 코드 또는 성공한 요청을 나타냅니다. 적절한 응답 메시지가 클라이언트 인터페이스에 나타납니다.

1. API 끝점을 호출하여 HTTP POST 요청 데이터의 유효성을 검사합니다. 

   ```URL
   YOUR_MANAGEMENT_API_URL/spaces/blobs?includes=description
   ```

## <a name="next-steps"></a>다음 단계

- Digital Twins 관리 API 및 이를 사용하는 방법에 대해 자세히 알아보려면 [Azure Digital Twins 관리 API 사용 방법](how-to-navigate-apis.md)을 읽어보세요.

- 다중 파트 요청을 사용하여 [Azure Digital Twins 엔터티에 blob을 추가](./how-to-add-blobs.md)합니다.

- Management API를 사용하여 인증에 대해 알아보려면 [API를 사용하여 인증](./security-authenticating-apis.md)을 참조하세요.
