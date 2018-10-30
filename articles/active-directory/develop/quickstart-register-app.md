---
title: Microsoft ID 플랫폼을 사용하여 앱 등록 | Azure
description: Microsoft ID 플랫폼을 사용하여 응용 프로그램을 추가 및 등록하는 방법을 살펴봅니다.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/25/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: lenalepa, sureshja
ms.openlocfilehash: e4e667c9a9490d164b9fb1c90580ceb30989a7dc
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49988873"
---
# <a name="quickstart-register-an-application-with-the-microsoft-identity-platform-preview"></a>빠른 시작: Microsoft ID 플랫폼을 사용하여 응용 프로그램 등록(미리 보기)

엔터프라이즈 개발자 및 소프트웨어 SaaS(software-as-a-service) 공급자는 Microsoft ID 플랫폼에 통합되어 해당 서비스에 대한 보안 로그인 및 권한 부여를 제공하는 상용 클라우드 서비스 또는 업무용 응용 프로그램 제품군을 개발할 수 있습니다.

이 빠른 시작에서는 Microsoft ID 플랫폼에 통합할 수 있게 응용 프로그램을 Azure Portal에 추가 및 등록하는 방법을 살펴봅니다.

## <a name="prerequisite"></a>필수 요소

시작하려면 Azure Portal에서 앱 등록 미리 보기 환경을 옵트인해야 합니다. 이 빠른 시작의 단계는 새 UI에 해당하며 미리 보기 환경을 옵트인한 경우에만 작동합니다.

## <a name="register-a-new-application-using-the-azure-portal"></a>Azure Portal을 사용하여 새 응용 프로그램 등록

1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 계정이 둘 이상의 테넌트에 대해 액세스를 제공하는 경우 오른쪽 위 모서리에 있는 계정을 선택하여 원하는 Azure AD 테넌트로 포털 세션을 설정합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스, **앱 등록(미리 보기) > 새 등록**을 차례로 선택합니다.
1. **응용 프로그램 등록** 페이지가 표시되면 응용 프로그램의 등록 정보를 입력합니다.

    - **이름** -앱의 사용자에게 표시되는 의미 있는 응용 프로그램 이름을 입력합니다.
    - **지원되는 계정 유형** - 응용 프로그램이 지원하려는 계정을 선택합니다.

        | 지원되는 계정 유형 | 설명 |
        |-------------------------|-------------|
        | **이 조직 디렉터리의 계정만** | 업무용(LOB) 응용 프로그램을 빌드하는 경우 이 옵션을 선택합니다. 응용 프로그램이 디렉터리에 등록되지 않은 경우에는 이 옵션을 사용할 수 없습니다.<br><br>이 옵션은 Azure AD 단일 테넌트에 매핑됩니다.<br><br>디렉터리 외부에 앱을 등록하지 않은 한 기본 옵션입니다. 디렉터리 외부에 앱을 등록할 경우 기본값은 Azure AD 다중 테넌트 및 개인 Microsoft 계정입니다. |
        | **모든 조직 디렉터리의 계정** | 모든 비즈니스 및 교육용 고객을 대상으로 하려는 경우 이 옵션을 선택합니다.<br><br>이 옵션은 Azure AD 전용 다중 테넌트에 매핑됩니다.<br><br>Azure AD 전용 단일 테넌트로 앱을 등록한 경우 **인증** 블레이드에서 Azure AD 멀티 테넌트로 업데이트할 수 있고 다시 단일 테넌트로 되돌릴 수 있습니다. |
        | **모든 조직 디렉터리의 계정 및 개인 Microsoft 계정** | 가장 폭넓은 고객을 대상으로 하려면 이 옵션을 사용합니다.<br><br>이 옵션은 Azure AD 다중 테넌트 및 개인 Microsoft 계정에 매핑됩니다.<br><br>Azure AD 다중 테넌트 및 개인 Microsoft 계정으로 앱을 등록한 경우 UI에서 이 항목을 변경할 수 없습니다. 대신 응용 프로그램 매니페스트 편집기를 사용하여 지원되는 계정 유형을 변경해야 합니다. |

    - **리디렉션 URI(선택 사항)** - 빌드하는 앱의 유형을 **웹** 또는 **공용 클라이언트(모바일 및 데스크톱)** 로 선택한 다음, 응용 프로그램의 리디렉션 URI(또는 답장 URL)를 입력합니다.
        - 웹 응용 프로그램의 경우 앱의 기준 URL을 제공합니다. 예를 들어 `http://localhost:31544`은 로컬 컴퓨터에서 실행 중인 웹앱의 URL일 수 있습니다. 사용자는 이 URL을 사용하여 웹 클라이언트 응용 프로그램에 로그인합니다.
        - 공용 클라이언트 응용 프로그램의 경우 Azure AD에서 토큰 응답을 반환하는 데 사용하는 URI를 제공합니다. 응용 프로그램에 고유하게 해당되는 값을 입력합니다(예: `myapp://auth`).

    웹 응용 프로그램 또는 네이티브 응용 프로그램에 대한 구체적인 예제를 원하는 경우 [빠른 시작](https://docs.microsoft.com/azure/active-directory/develop/#quickstarts)을 확인합니다.

1. 작업을 마쳤으면 **등록**을 선택합니다.

    [![Azure Portal에서 새 응용 프로그램 등록](./media/quickstart-add-azure-ad-app-preview/new-app-registration-expanded.png)](./media/quickstart-add-azure-ad-app-preview/new-app-registration-expanded.png#lightbox)

Azure AD가 앱에 고유한 응용 프로그램 ID를 할당하면 응용 프로그램의 **개요** 페이지로 이동합니다. 응용 프로그램에 다른 기능을 추가하려면 브랜드, 인증서, 비밀, API 권한 등을 비롯한 다른 구성 옵션을 선택할 수 있습니다.

[![새로 등록된 앱의 개요 페이지](./media/quickstart-add-azure-ad-app-preview/new-app-overview-page-expanded.png)](./media/quickstart-add-azure-ad-app-preview/new-app-overview-page-expanded.png#lightbox)

## <a name="next-steps"></a>다음 단계

- [권한 및 동의](v2-permissions-and-consent.md)에 대해 살펴봅니다.
- 응용 프로그램 등록에서 추가 구성 기능(예: 자격 증명, 사용 권한, 다른 테넌트의 사용자 로그인 사용)을 사용하도록 설정하려면 다음 빠른 시작을 참조하세요.
    - [웹 API에 액세스하는 클라이언트 응용 프로그램 구성](quickstart-configure-app-access-web-apis.md)
    - [웹 API를 공개하는 응용 프로그램 구성](quickstart-configure-app-expose-web-apis.md)
    - [응용 프로그램에서 지원되는 계정 수정](quickstart-modify-supported-accounts.md)
- [빠른 시작](https://docs.microsoft.com/azure/active-directory/develop/#quickstarts)을 선택하여 신속하게 앱을 빌드하고 토큰 가져오기, 토큰 새로 고치기, 사용자 로그인, 사용자 정보 표시 등의 기능을 추가합니다.
- 등록된 응용 프로그램을 나타내는 두 개의 Azure AD 개체와 개체 간의 관계에 대한 자세한 내용은 [응용 프로그램 개체 및 서비스 주체 개체](app-objects-and-service-principals.md)를 참조하세요.
- 앱을 개발할 때 사용해야 하는 브랜딩 지침에 대한 자세한 내용은 [응용 프로그램에 대한 브랜딩 지침](howto-add-branding-in-azure-ad-apps.md)을 참조하세요.
