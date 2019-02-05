---
title: 웹 API에 액세스하는 애플리케이션 구성 | Azure
description: 리디렉션 URI, 자격 증명 또는 웹 API에 대한 액세스 권한을 포함하도록 Microsoft 플랫폼에 등록된 애플리케이션을 구성하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/25/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: lenalepa, sureshja
ms.openlocfilehash: a2f0d97d6b7040f874fc03ffe19f247cdc742c77
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/28/2019
ms.locfileid: "55103652"
---
# <a name="quickstart-configure-a-client-application-to-access-web-apis-preview"></a>빠른 시작: 웹 API에 액세스하는 클라이언트 애플리케이션 구성(미리 보기)

웹/기밀 클라이언트 애플리케이션이 인증을 요구하고 액세스 토큰을 얻는 권한 부여 흐름에 참여하려면 보안 자격 증명을 설정해야 합니다. Azure Portal에서 지원하는 기본 인증 방법은 클라이언트 ID + 비밀 키입니다.

또한 클라이언트가 리소스 애플리케이션(예: Microsoft Graph API)에서 공개한 웹 API에 액세스하기 전에 동의 프레임워크는 클라이언트에서 요청된 권한에 기반하여 필요한 권한 부여를 얻도록 합니다. 기본적으로 모든 애플리케이션은 Microsoft Graph API에서 사용 권한을 선택할 수 있습니다. [Graph API "로그인 및 읽기 사용자 프로필" 권한](https://developer.microsoft.com/graph/docs/concepts/permissions_reference#user-permissions)은 기본적으로 선택됩니다. 원하는 웹 API 각각에서 [두 가지 형식의 사용 권한](developer-glossary.md#permissions) 중에 선택할 수 있습니다.

* **응용 프로그램 권한** -클라이언트 응용 프로그램이 직접 웹 API에 액세스해야 합니다(사용자 컨텍스트 없음). 이 유형의 권한은 관리자의 동의가 필요하며 공용(데스크톱 및 모바일) 클라이언트 애플리케이션에 대해 사용할 수 없습니다.
* **위임된 권한** - 클라이언트 응용 프로그램이 로그인된 사용자로 웹 API에 액세스해야 하지만 이 액세스는 선택한 권한에 따라 제한됩니다. 이 형식의 사용 권한은 관리자의 동의를 필요로 하지 않는 한 사용자가 부여할 수 있습니다.

  > [!NOTE]
  > 위임된 권한을 애플리케이션에 추가할 경우 테넌트 내의 사용자에게 자동으로 동의를 부여하지 않습니다. 관리자가 모든 사용자를 대신하여 동의를 허락하지 않는 한 사용자는 런타임에 추가로 위임된 사용 권한에 대해 수동으로 동의해야 합니다.

이 빠른 시작에서는 다음을 위해 앱을 구성하는 방법을 확인합니다.

* [리디렉션 URL을 애플리케이션에 추가](#add-redirect-uris-to-your-application)
* [웹 애플리케이션에 자격 증명 추가](#add-credentials-to-your-web-application)
* [웹 API에 액세스 권한 추가](#add-permissions-to-access-web-apis)

## <a name="prerequisites"></a>필수 조건

시작하려면 다음과 같은 필수 구성을 완료했는지 확인합니다.

* 지원되는 [권한 및 동의](v2-permissions-and-consent.md)를 살펴봅니다. 여기서는 다른 사용자 또는 애플리케이션에서 사용해야 하는 애플리케이션을 빌드하는 경우를 이해하는 것이 중요합니다.
* 애플리케이션이 등록된 테넌트가 있습니다.
  * 앱이 등록되지 않았다면 [Microsoft ID 플랫폼에 애플리케이션을 등록하는 방법](quickstart-register-app.md)을 살펴봅니다.
* Azure Portal에서 앱 등록 미리 보기 환경을 옵트인합니다. 이 빠른 시작의 단계는 새 UI에 해당하며 미리 보기 환경을 옵트인한 경우에만 작동합니다.

## <a name="sign-in-to-the-azure-portal-and-select-the-app"></a>Azure Portal에 로그인하고 앱 선택

앱을 구성하려면 먼저 다음 단계를 따릅니다.

1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 계정이 둘 이상의 테넌트에 대해 액세스를 제공하는 경우 오른쪽 위 모서리에 있는 계정을 선택하여 원하는 Azure AD 테넌트로 포털 세션을 설정합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스, **앱 등록(미리 보기)** 을 차례로 선택합니다.
1. 구성하려는 애플리케이션을 찾아 선택합니다. 앱을 선택하면 볼 **개요** 또는 기본 등록 페이지가 나타납니다.
1. 웹 API에 액세스하도록 애플리케이션을 구성하는 단계를 따릅니다. 
    * [리디렉션 URL을 애플리케이션에 추가](#add-redirect-uris-to-your-application)
    * [웹 애플리케이션에 자격 증명 추가](#add-credentials-to-your-web-application)
    * [웹 API에 액세스 권한 추가](#add-permissions-to-access-web-apis)

## <a name="add-redirect-uris-to-your-application"></a>애플리케이션에 리디렉션 URI 추가

[![웹 앱 및 공용 클라이언트 앱에 대한 사용자 지정 리디렉션 URI 추가](./media/quickstart-update-azure-ad-app-preview/authentication-redirect-uris-expanded.png)](./media/quickstart-update-azure-ad-app-preview/authentication-redirect-uris-expanded.png#lightbox)

리디렉션 URI를 애플리케이션에 추가하려면

1. 앱의 **개요** 페이지에서 **인증** 섹션을 선택합니다.

1. 웹 및 공용 클라이언트 애플리케이션에 대한 사용자 지정 리디렉션 URI를 추가하려면 다음 단계를 따릅니다.

    1. **리디렉션 URI** 섹션을 찾습니다.
    1. 빌드하는 애플리케이션 유형을 **웹** 또는 **공용 클라이언트(모바일 및 데스크톱)** 로 선택합니다.
    1. 애플리케이션의 리디렉션 URI를 입력합니다.
        * 웹 애플리케이션의 경우 애플리케이션의 기준 URL을 제공합니다. 예를 들어 http://localhost:31544은 로컬 머신에서 실행 중인 웹 애플리케이션의 URL일 수 있습니다. 사용자는 이 URL을 사용하여 웹 클라이언트 애플리케이션에 로그인합니다.
        * 공용 애플리케이션의 경우 Azure AD에서 토큰 응답을 반환하는 데 사용하는 URI를 제공합니다. 애플리케이션에 고유하게 해당되는 값을 입력합니다(예: https://MyFirstApp).

1. 공용 클라이언트(모바일, 데스크톱)에 대해 제안된 리디렉션 URI에서 선택하려면 다음 단계를 따릅니다.

    1. **공용 클라이언트(모바일, 데스크톱)에 대해 제안된 리디렉션 URI** 섹션을 찾습니다.
    1. 확인란을 사용하여 애플리케이션에 적합한 리디렉션 URI를 선택합니다.

## <a name="add-credentials-to-your-web-application"></a>웹 애플리케이션에 자격 증명 추가

[![인증서 및 클라이언트 비밀 추가](./media/quickstart-update-azure-ad-app-preview/credentials-certificates-secrets-expanded.png)](./media/quickstart-update-azure-ad-app-preview/credentials-certificates-secrets-expanded.png#lightbox)

웹 애플리케이션에 자격 증명을 추가하려면

1. 앱의 **개요** 페이지에서 **인증서 및 비밀** 섹션을 선택합니다.

1. 인증서를 설치하려면 다음 단계를 따릅니다.

    1. **인증서 업로드**를 선택합니다.
    1. 업로드할 파일을 선택합니다. cer, .pem, .crt 중 한 가지 파일 형식이어야 합니다.
    1. **추가**를 선택합니다.

1. 클라이언트 비밀을 추가하려면 다음 단계를 따릅니다.

    1. **새 클라이언트 비밀**을 선택합니다.
    1. 클라이언트 비밀에 대한 설명을 추가합니다.
    1. 기간을 선택합니다.
    1. **추가**를 선택합니다.

> [!NOTE]
> 구성 변경 사항을 저장하면 맨 오른쪽 열에 클라이언트 비밀 값이 포함됩니다. 이 페이지를 벗어나면 액세스할 수 없으므로 클라이언트 애플리케이션 코드에서 사용할 **값을 복사해야 합니다**.

## <a name="add-permissions-to-access-web-apis"></a>웹 API 액세스 권한 추가

[![API 권한 추가](./media/quickstart-update-azure-ad-app-preview/api-permissions-expanded.png)](./media/quickstart-update-azure-ad-app-preview/api-permissions-expanded.png#lightbox)

클라이언트에서 리소스 API에 액세스하는 권한을 추가하려면

1. 앱 **개요** 페이지에서 **API 권한**을 선택합니다.
1. **권한 추가**를 선택합니다.
1. 기본적으로 보기에서는 **Microsoft API**로부터 선택할 수 있습니다. 관심 있는 API 섹션을 선택합니다.
    * **Microsoft API** - Microsoft Graph 등, Microsoft API에 대한 권한을 선택할 수 있습니다.
    * **내 조직에서 사용하는 API** - 조직에서 공개한 API나, 조직이 통합된 API에 대한 권한을 선택할 수 있습니다.
    * **내 API** - 내가 공개한 API에 대한 권한을 선택할 수 있습니다.
1. API를 선택한 후에는 **요청된 API 권한** 페이지가 표시됩니다. API가 위임된 권한과 애플리케이션 권한 모두를 공개할 경우 애플리케이션에 필요한 권한 유형을 선택합니다.
1. 완료되면 **권한 추가**를 선택합니다. 그러면 권한이 테이블에 저장 및 추가된 **API 권한** 페이지로 돌아갑니다.

## <a name="next-steps"></a>다음 단계

앱에 대한 다른 관련 앱 관리 빠른 시작에 대해 알아봅니다.

* [Microsoft ID 플랫폼을 사용하여 애플리케이션 등록](quickstart-register-app.md)
* [웹 API를 공개하는 애플리케이션 구성](quickstart-configure-app-expose-web-apis.md)
* [애플리케이션에서 지원되는 계정 수정](quickstart-modify-supported-accounts.md)
* [Microsoft ID 플랫폼을 사용하여 등록된 응용 프로그램 제거](quickstart-remove-app.md)

등록된 애플리케이션 및 이들 간의 관계를 나타내는 두 개의 Azure AD 개체에 대한 자세한 내용은 [애플리케이션 개체 및 서비스 주체 개체](app-objects-and-service-principals.md)를 참조하세요.

Azure Active Directory를 사용해 애플리케이션을 개발할 때 사용해야 하는 브랜딩 지침에 대해 자세히 알아보려면 [애플리케이션에 대한 브랜딩 지침](howto-add-branding-in-azure-ad-apps.md)을 참조하세요.
