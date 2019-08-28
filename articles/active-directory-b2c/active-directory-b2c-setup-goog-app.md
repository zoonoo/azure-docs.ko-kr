---
title: Google 계정을 사용 하 여 등록 및 로그인 설정-Azure Active Directory B2C
description: 고객에게 Azure Active Directory B2C를 사용하여 애플리케이션에서 Google 계정으로 등록 및 로그인을 제공합니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 7286bf1fd51883587aa41dc69d5dae0a3e6fb824
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2019
ms.locfileid: "69622421"
---
# <a name="set-up-sign-up-and-sign-in-with-a-google-account-using-azure-active-directory-b2c"></a>Azure Active Directory B2C를 사용하여 Google 계정으로 등록 설정 및 로그인

## <a name="create-a-google-application"></a>Google 애플리케이션 만들기

Azure AD(Azure Active Directory) B2C에서 Google 계정을 [ID 공급자](active-directory-b2c-reference-oauth-code.md)로 사용하려면 테넌트에 해당 계정을 나타내는 애플리케이션을 만들어야 합니다. Google 계정이 아직 없는 경우에서 [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp)등록할 수 있습니다.

1. Google 계정 자격 증명을 사용하여 [Google 개발자 콘솔](https://console.developers.google.com/)에 로그인합니다.
1. 페이지의 왼쪽 위 모서리에서 프로젝트 목록을 선택한 다음 **새 프로젝트**를 선택 합니다.
1. **프로젝트 이름**을 입력하고 **만들기**를 클릭한 다음 새 프로젝트를 사용 중인지 확인합니다.
1. 왼쪽 메뉴에서 **자격 증명**을 선택하고 **자격 증명 만들기** > **Oauth 클라이언트 ID**를 선택합니다.
1. **애플리케이션 형식**에서 **웹 애플리케이션**을 선택합니다.
1. 애플리케이션에 대한 **이름**을 입력하고 **권한이 부여된 JavaScript 원본**에 `https://your-tenant-name.b2clogin.com`을 입력하고 **권한이 부여된 리디렉션 URI**에 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`를 입력합니다. `your-tenant-name`을 테넌트 이름으로 바꿉니다. Azure AD B2C에서 테넌트가 대문자로 정의되어 있더라도 테넌트 이름을 입력할 때는 소문자만 사용해야 합니다.
1. **만들기**를 클릭합니다.
1. **클라이언트 ID** 및 **클라이언트 비밀** 값을 복사합니다. 테넌트에서 Google을 ID 공급자로 구성하려면 둘 모두가 필요합니다. **클라이언트 암호** 는 중요한 보안 자격 증명입니다.

## <a name="configure-a-google-account-as-an-identity-provider"></a>Google 계정을 ID 공급자로 구성

1. Azure AD B2C 테넌트의 전역 관리자로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택 하 고 테 넌 트가 포함 된 디렉터리를 선택 하 여 Azure AD B2C 테 넌 트를 포함 하는 디렉터리를 사용 하 고 있는지 확인 합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
1. **Id 공급자**를 선택한 다음 **Google**을 선택 합니다.
1. **이름**을 입력합니다. 예: *Google*.
1. **클라이언트 id**에 대해 이전에 만든 Google 응용 프로그램의 클라이언트 id를 입력 합니다.
1. **클라이언트 암호**에 대해 기록한 클라이언트 암호를 입력 합니다.
1. **저장**을 선택합니다.
