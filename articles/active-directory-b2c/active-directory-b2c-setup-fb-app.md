---
title: Facebook 계정으로 등록 및 로그인 설정 - Azure Active Directory B2C | Microsoft Docs
description: 고객에게 Azure Active Directory B2C를 사용하여 애플리케이션에서 Facebook 계정으로 등록 및 로그인을 제공합니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 06/05/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 6b90ba89f17b42f4d92c666c3f539fcad3e3227c
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66733535"
---
# <a name="set-up-sign-up-and-sign-in-with-a-facebook-account-using-azure-active-directory-b2c"></a>Azure Active Directory B2C를 사용하여 Facebook 계정으로 등록 설정 및 로그인

## <a name="create-a-facebook-application"></a>Facebook 애플리케이션 만들기

Azure AD(Azure Active Directory) B2C에서 Facebook 계정을 [ID 공급자](active-directory-b2c-reference-oauth-code.md)로 사용하려면 테넌트에 해당 계정을 나타내는 애플리케이션을 만들어야 합니다. Facebook 계정이 없는 경우 [https://www.facebook.com/](https://www.facebook.com/)에서 얻을 수 있습니다.

1. Facebook 계정 자격 증명으로 [개발자용 Facebook](https://developers.facebook.com/)에 로그인합니다.
2. 아직 등록하지 않은 경우 Facebook 개발자로 등록해야 합니다. 이 위해 선택 **시작** 페이지의 오른쪽 위 모서리에 Facebook의 정책에 적용 하 고 등록 단계를 완료 합니다.
3. 선택 **My Apps** 차례로 **새 앱 추가**합니다.
4. **표시 이름** 및 유효한 **연락처 전자 메일**을 제공합니다.
5. **앱 ID 만들기**를 클릭합니다. Facebook 플랫폼 정책을 수용하고 온라인 보안 검사를 완료해야 합니다.
6. **설정** > **기본**을 선택합니다.
7. `Business and Pages` 등의 **범주**를 선택합니다. 이 값은 Facebook의 경우 필수이지만 Azure AD B2C에서는 사용되지 않습니다.
8. 페이지의 맨 아래에서 **플랫폼 추가**를 선택한 후 **웹 사이트**를 선택합니다.
9. **사이트 URL**에 `https://your-tenant-name.b2clogin.com/`을 입력합니다. 여기서 `your-tenant-name`은 실제 테넌트의 이름으로 바꾸세요. **개인 정보 취급 방침 URL**의 URL(예: `http://www.contoso.com`)을 입력합니다. 정책 URL은 응용 프로그램에 대한 개인 정보를 제공하기 위해 유지 관리하는 페이지입니다.
10. **변경 내용 저장**을 선택합니다.
11. 페이지의 맨 위에서 **앱 ID** 값을 복사합니다.
12. **표시**를 클릭하고 **앱 비밀** 값을 복사합니다. 테넌트에서 Facebook을 ID 공급자로 구성하려면 둘 다 사용합니다. **앱 암호** 는 중요한 보안 자격 증명입니다.
13. 옆에 더하기 기호를 선택 **제품**를 선택한 후 **설정** 아래의 **Facebook 로그인**합니다.
14. 아래 **Facebook 로그인**를 선택 **설정**합니다.
15. **유효한 OAuth 리디렉션 URI**에 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`를 입력합니다. `your-tenant-name`을 테넌트 이름으로 바꿉니다. 페이지 아래쪽에 있는 **변경 내용 저장** 을 클릭합니다.
16. Azure AD B2C에서 Facebook 애플리케이션을 사용할 수 있도록 하려면 페이지 오른쪽 위에 있는 상태 선택기를 클릭한 후 **설정**하여 공용 애플리케이션으로 만들고 **확인**을 클릭합니다.  이때 상태가 **개발**에서 **라이브**로 변경됩니다.

## <a name="configure-a-facebook-account-as-an-identity-provider"></a>Facebook 계정을 ID 공급자로 구성

1. Azure AD B2C 테넌트의 전역 관리자로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. Azure AD B2C 테넌트를 포함하는 디렉터리를 사용하려면 위쪽 메뉴에서 **디렉터리 및 구독 필터**를 클릭하고 테넌트가 포함된 디렉터리를 선택합니다.
3. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
4. **ID 공급자**를 선택한 다음, **추가**를 선택합니다.
5. **이름**을 입력합니다. 예를 들어 *Facebook*을 입력합니다.
6. **ID 공급자 형식**을 선택하고 **Facebook**을 선택한 다음, **확인**을 클릭합니다.
7. **이 ID 공급자 설정**을 선택하고 이전에 기록한 앱 ID를 **클라이언트 ID**로 입력한 후, 기록한 앱 비밀을 이전에 만든 Facebook 응용 프로그램의 **클라이언트 암호**로 입력합니다.
8. **확인**, **만들기**를 차례로 클릭하여 Facebook 구성을 저장합니다.
