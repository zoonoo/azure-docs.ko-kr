---
title: Google 계정으로 등록 및 로그인 설정 - Azure Active Directory B2C | Microsoft Docs
description: 고객에게 Azure Active Directory B2C를 사용하여 애플리케이션에서 Google 계정으로 등록 및 로그인을 제공합니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: f7e092e27f73901810a9f7edd210e3513c54095e
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66508512"
---
# <a name="set-up-sign-up-and-sign-in-with-a-google-account-using-azure-active-directory-b2c"></a>Azure Active Directory B2C를 사용하여 Google 계정으로 등록 설정 및 로그인

## <a name="create-a-google-application"></a>Google 애플리케이션 만들기

Azure AD(Azure Active Directory) B2C에서 Google 계정을 [ID 공급자](active-directory-b2c-reference-oauth-code.md)로 사용하려면 테넌트에 해당 계정을 나타내는 애플리케이션을 만들어야 합니다. Google 계정이 없는 경우 [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp)에서 얻을 수 있습니다.

1. Google 계정 자격 증명을 사용하여 [Google 개발자 콘솔](https://console.developers.google.com/)에 로그인합니다.
2. 페이지의 왼쪽 위 모퉁이에서 프로젝트 목록에서 선택 하 고 선택한 **새 프로젝트**합니다.
3. **프로젝트 이름**을 입력하고 **만들기**를 클릭한 다음 새 프로젝트를 사용 중인지 확인합니다.
4. 왼쪽 메뉴에서 **자격 증명**을 선택하고 **자격 증명 만들기** > **Oauth 클라이언트 ID**를 선택합니다.
5. **애플리케이션 형식**에서 **웹 애플리케이션**을 선택합니다.
6. 애플리케이션에 대한 **이름**을 입력하고 **권한이 부여된 JavaScript 원본**에 `https://your-tenant-name.b2clogin.com`을 입력하고 **권한이 부여된 리디렉션 URI**에 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`를 입력합니다. `your-tenant-name`을 테넌트 이름으로 바꿉니다. Azure AD B2C에서 테넌트가 대문자로 정의되어 있더라도 테넌트 이름을 입력할 때는 소문자만 사용해야 합니다.
7. **만들기**를 클릭합니다.
8. **클라이언트 ID** 및 **클라이언트 비밀** 값을 복사합니다. 테넌트에서 Google을 ID 공급자로 구성하려면 둘 모두가 필요합니다. **클라이언트 암호** 는 중요한 보안 자격 증명입니다.

## <a name="configure-a-google-account-as-an-identity-provider"></a>Google 계정을 ID 공급자로 구성

1. Azure AD B2C 테넌트의 전역 관리자로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. Azure AD B2C 테넌트를 포함하는 디렉터리를 사용하려면 위쪽 메뉴에서 **디렉터리 및 구독 필터**를 클릭하고 테넌트가 포함된 디렉터리를 선택합니다.
3. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
4. **ID 공급자**를 선택한 다음, **추가**를 선택합니다.
5. **이름**을 입력합니다. 예를 들어, *Google*을 입력합니다.
6. **ID 공급자 형식**을 선택하고 **Google**을 선택한 다음, **확인**을 클릭합니다.
7. **이 ID 공급자 설정**을 선택하고 이전에 기록한 클라이언트 ID를 **클라이언트 ID**로 입력한 후, 기록한 클라이언트 암호를 이전에 만든 Google 애플리케이션의 **클라이언트 암호**로 입력합니다.
8. **확인**을 클릭한 다음 **만들기**를 클릭하여 Google 구성을 저장합니다.

