---
title: Azure Active Directory v1.0 엔드포인트에 앱 등록
description: Azure AD(Azure Active Directory) v1.0 엔드포인트에 응용 프로그램을 추가하고 등록하는 방법을 알아봅니다.
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
ms.date: 09/24/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: lenalepa, sureshja
ms.openlocfilehash: 4608e9ec0cd67b6c0f7ac23e27761b0355a5d738
ms.sourcegitcommit: 799a4da85cf0fec54403688e88a934e6ad149001
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2018
ms.locfileid: "50911867"
---
# <a name="quickstart-register-an-app-with-the-azure-active-directory-v10-endpoint"></a>빠른 시작: Azure Active Directory v1.0 엔드포인트에 앱 등록

[!INCLUDE [active-directory-develop-applies-v1](../../../includes/active-directory-develop-applies-v1.md)]

엔터프라이즈 개발자 및 소프트웨어 SaaS(software-as-a-service) 공급자는 Azure AD(Azure Active directory)와 함께 통합되어 해당 서비스에 대한 보안 로그인 및 권한 부여를 제공하는 상용 클라우드 서비스 또는 업무용 응용 프로그램 제품군을 개발할 수 있습니다. 애플리케이션 또는 서비스를 Azure AD와 통합하려면 개발자가 먼저 Azure AD에 애플리케이션을 등록해야 합니다.

Azure AD의 기능을 사용하려는 모든 애플리케이션이 먼저 Azure AD 테넌트에 등록되어야 합니다. 이 등록 프로세스는 애플리케이션이 위치한 URL, 사용자가 인증된 후에 회신을 보낼 URL, 앱을 식별하는 URI 등과 같이 애플리케이션에 대한 Azure AD 세부 정보의 제공이 포함됩니다.

이 빠른 시작은 Azure Portal에서 기존 **앱 등록** 환경을 사용하여 Azure AD에서 응용 프로그램을 추가하고 등록하는 방법을 보여줍니다.

> [!NOTE]
> 새 앱 등록? Azure Portal에서 새 **앱 등록(미리 보기)** 환경을 사용해 보세요. 시작하려면 [앱 등록(미리 보기)](quickstart-register-app.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건

먼저 앱을 등록하는 데 사용할 수 있는 Azure AD 테넌트가 있는지 확인합니다. 테넌트가 아직 없는 경우 [얻는 방법을 알아보세요](quickstart-create-new-tenant.md).

## <a name="register-a-new-application-using-the-azure-portal"></a>Azure Portal을 사용하여 새 응용 프로그램 등록

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 계정이 둘 이상의 액세스를 제공하는 경우 오른쪽 위 모서리에 있는 계정을 선택하여 원하는 Azure AD 테넌트로 포털 세션을 설정합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **앱 등록**을 선택하고 **새 응용 프로그램 등록**을 선택합니다.

    ![새 응용 프로그램 등록](./media/quickstart-v1-integrate-apps-with-azure-ad/add-app-registration.png)

1. **만들기** 페이지가 표시되면 응용 프로그램의 등록 정보를 입력합니다. 

    - **이름:** 의미 있는 응용 프로그램 이름을 입력합니다.
    - **응용 프로그램 형식:**
      - 디바이스에 로컬로 설치된 [클라이언트 애플리케이션](developer-glossary.md#client-application)의 경우 **네이티브**를 선택합니다. 이 설정은 OAuth 공개 [네이티브 클라이언트](developer-glossary.md#native-client)에 사용됩니다.
      - 보안 서버에 설치된 [클라이언트 응용 프로그램](developer-glossary.md#client-application) 및 [리소스/API 응용 프로그램](developer-glossary.md#resource-server)의 경우 **웹앱/API**를 선택합니다. 이 설정은 OAuth 기밀 [웹 클라이언트](developer-glossary.md#web-client) 및 공용 [사용자 에이전트 기반 클라이언트](developer-glossary.md#user-agent-based-client)에 사용됩니다. 동일한 응용 프로그램이 클라이언트와 리소스/API를 모두 노출할 수도 있습니다.
    - **로그온 URL:** "웹앱/API" 응용 프로그램에서 앱의 기준 URL을 제공합니다. 예를 들어 `http://localhost:31544`은 로컬 컴퓨터에서 실행 중인 웹앱의 URL일 수 있습니다. 사용자는 이 URL을 사용하여 웹 클라이언트 응용 프로그램에 로그인합니다. 
    - **리디렉션 URI:** "네이티브" 응용 프로그램의 경우 Azure AD에서 토큰 응답을 반환하는 데 사용하는 URI를 제공합니다. 응용 프로그램에 고유하게 해당되는 값을 입력합니다(예: `http://MyFirstAADApp`).

      ![새 응용 프로그램 등록 - 만들기](./media/quickstart-v1-integrate-apps-with-azure-ad/add-app-registration-create.png)

    웹 응용 프로그램 또는 네이티브 응용 프로그램에 대한 구체적인 예제를 보려면 설명서에서 **빠른 시작**을 확인하세요.

1. 작업을 마쳤으면 **만들기**를 선택합니다.

    Azure AD가 애플리케이션에 고유한 애플리케이션 ID를 할당하면 애플리케이션의 기본 등록 페이지로 이동합니다. 애플리케이션이 웹 또는 네이티브 애플리케이션인지에 따라 애플리케이션에 기능을 추가하기 위해 다른 옵션이 제공됩니다.

      > [!NOTE]
      > 기본적으로 새로 등록된 웹 애플리케이션은 동일한 테넌트의 사용자**만** 애플리케이션에 로그인할 수 있도록 구성됩니다.

## <a name="next-steps"></a>다음 단계

- 동의에 대한 개요는 [Azure AD 동의 프레임워크](consent-framework.md)를 참조하세요.
- 응용 프로그램 등록에서 추가 구성 기능(예: 자격 증명, 사용 권한, 다른 테넌트의 사용자 로그인 사용)을 사용하도록 설정하려면 [Azure AD에서 응용 프로그램 업데이트](quickstart-v1-update-azure-ad-app.md)를 참조하세요.
- 등록된 응용 프로그램을 나타내는 두 개의 Azure AD 개체와 개체 간의 관계에 대한 자세한 내용은 [응용 프로그램 개체 및 서비스 주체 개체](app-objects-and-service-principals.md)를 참조하세요.
- Azure Active Directory를 사용하여 응용 프로그램을 개발할 때 사용해야 하는 브랜딩 지침에 대한 자세한 내용은 [응용 프로그램에 대한 브랜딩 지침](howto-add-branding-in-azure-ad-apps.md)을 참조하세요.
