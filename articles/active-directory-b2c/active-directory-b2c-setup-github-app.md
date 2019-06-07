---
title: GitHub 계정으로 등록 및 로그인 설정 - Azure Active Directory B2C | Microsoft Docs
description: 고객에게 Azure Active Directory B2C를 사용하여 애플리케이션에서 GitHub 계정으로 등록 및 로그인을 제공합니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 88d92e081a7b852035dd2b7d3bc9e4e29fefaddd
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66508572"
---
# <a name="set-up-sign-up-and-sign-in-with-a-github-account-using-azure-active-directory-b2c"></a>Azure Active Directory B2C를 사용하여 GitHub 계정으로 등록 설정 및 로그인

> [!NOTE]
> 이 기능은 미리 보기 상태입니다.
> 

Azure AD(Azure Active Directory) B2C에서 GitHub 계정을 [ID 공급자](active-directory-b2c-reference-oauth-code.md)로 사용하려면 테넌트에 해당 계정을 나타내는 애플리케이션을 만들어야 합니다. GitHub 계정이 아직 없는 경우 [https://www.github.com/](https://www.github.com/)에서 구할 수 있습니다.

## <a name="create-a-github-oauth-application"></a>GitHub OAuth 애플리케이션 만들기

1. GitHub 자격 증명을 사용하여 [GitHub Developer](https://github.com/settings/developers) 웹 사이트에 로그인합니다.
2. **OAuth 앱**, **새 OAuth 앱**을 차례로 선택합니다.
3. **애플리케이션 이름** 및 **홈페이지 URL**을 입력합니다.
4. **권한 부여 콜백 URL**에 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`를 입력합니다. `your-tenant-name`은 Azure AD B2C 테넌트의 이름으로 바꿉니다. Azure AD B2C에서 테넌트가 대문자로 정의되어 있더라도 테넌트 이름을 입력할 때는 소문자만 사용합니다.
5. **응용 프로그램 등록**을 클릭합니다.
6. **클라이언트 ID** 및 **클라이언트 비밀** 값을 복사합니다. 테넌트에 ID 공급자를 추가하려면 둘 다 필요합니다.

## <a name="configure-a-github-account-as-an-identity-provider"></a>GitHub 계정을 ID 공급자로 구성

1. Azure AD B2C 테넌트의 전역 관리자로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. Azure AD B2C 테넌트를 포함하는 디렉터리를 사용하려면 위쪽 메뉴에서 **디렉터리 및 구독 필터**를 클릭하고 테넌트가 포함된 디렉터리를 선택합니다.
3. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
4. **ID 공급자**를 선택한 다음, **추가**를 선택합니다.
5. **이름**을 제공합니다. 예를 들어 *GitHub*를 입력합니다.
6. **ID 공급자 형식**을 선택하고 **GitHub(미리 보기)** 를 선택한 다음, **확인**을 클릭합니다.
7. **이 ID 공급자 설정**을 선택하고 이전에 기록한 클라이언트 ID를 **클라이언트 ID**로 입력한 후, 기록한 클라이언트 비밀을 이전에 만든 GitHub 계정 애플리케이션의 **클라이언트 비밀**로 입력합니다.
8. **확인**을 클릭하고 **만들기**를 클릭하여 GitHub 계정 구성을 저장합니다.
