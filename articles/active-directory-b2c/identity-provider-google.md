---
title: Google 계정으로 가입 및 로그인 설정
titleSuffix: Azure AD B2C
description: 고객에게 Azure Active Directory B2C를 사용하여 애플리케이션에서 Google 계정으로 등록 및 로그인을 제공합니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 48955caddb64069f897078f5e47066d9f11d119b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188143"
---
# <a name="set-up-sign-up-and-sign-in-with-a-google-account-using-azure-active-directory-b2c"></a>Azure Active Directory B2C를 사용하여 Google 계정으로 등록 설정 및 로그인

## <a name="create-a-google-application"></a>Google 애플리케이션 만들기

Azure Active Directory B2C(Azure AD B2C)에서 Google 계정을 [ID 공급자로](authorization-code-flow.md) 사용하려면 Google 개발자 콘솔에서 응용 프로그램을 만들어야 합니다. Google 계정이 아직 없는 경우 에서 [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp)가입할 수 있습니다.

1. Google 계정 자격 증명을 사용하여 [Google 개발자 콘솔](https://console.developers.google.com/)에 로그인합니다.
1. 페이지의 왼쪽 위 모서리에서 프로젝트 목록을 선택한 다음 **새 프로젝트를**선택합니다.
1. 프로젝트 **이름을**입력하고 **을**선택합니다.
1. 화면 왼쪽 상단에 있는 프로젝트 드롭다운을 선택하고 이름을 따라 프로젝트를 선택한 다음 **열기를**선택하여 새 프로젝트를 사용하고 있는지 확인합니다.
1. 왼쪽 메뉴에서 **OAuth 동의 화면을** 선택하고 **외부**를 선택한 다음 **에서 만들기를**선택합니다.
응용 프로그램의 **이름을** 입력합니다. **공인 도메인** 섹션에 *b2clogin.com* 입력하고 **저장을**선택합니다.
1. 왼쪽 메뉴에서 **자격 증명**을 선택하고 **자격 증명 만들기** > **Oauth 클라이언트 ID**를 선택합니다.
1. **애플리케이션 형식**에서 **웹 애플리케이션**을 선택합니다.
1. 애플리케이션에 대한 **이름**을 입력하고 **권한이 부여된 JavaScript 원본**에 `https://your-tenant-name.b2clogin.com`을 입력하고 **권한이 부여된 리디렉션 URI**에 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`를 입력합니다. `your-tenant-name`을 테넌트 이름으로 바꿉니다. Azure AD B2C에서 테넌트가 대문자로 정의되어 있더라도 테넌트 이름을 입력할 때는 소문자만 사용해야 합니다.
1. **만들기**를 클릭합니다.
1. **클라이언트 ID** 및 클라이언트 보안 의 값을 **복사합니다.** 테넌트에서 Google을 ID 공급자로 구성하려면 둘 모두가 필요합니다. **클라이언트 보안은** 중요한 보안 자격 증명입니다.

## <a name="configure-a-google-account-as-an-identity-provider"></a>Google 계정을 ID 공급자로 구성

1. Azure AD B2C 테넌트의 전역 관리자로 Azure [포털에](https://portal.azure.com/) 로그인합니다.
1. Azure AD B2C 테넌트를 포함하는 디렉터리를 사용하려면 위쪽 메뉴에서 **디렉터리 + 구독** 필터를 선택하고, 테넌트가 포함된 디렉터리를 선택합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
1. **ID 공급자를**선택한 다음 **Google**을 선택합니다.
1. **이름**을 입력합니다. 예를 들어, *구글*.
1. 클라이언트 **ID의**경우 이전에 만든 Google 응용 프로그램의 클라이언트 ID를 입력합니다.
1. 클라이언트 **보안**에 대 한 기록 된 클라이언트 비밀을 입력 합니다.
1. **저장**을 선택합니다.
