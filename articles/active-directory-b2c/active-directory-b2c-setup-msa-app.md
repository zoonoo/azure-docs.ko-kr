---
title: 등록 및 로그인 Microsoft 계정-Azure Active Directory B2C를 사용 하 여 설정
description: 고객에게 Azure Active Directory B2C를 사용하여 애플리케이션에서 Microsoft 계정으로 등록 및 로그인을 제공합니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 63aa93e72d76d430fc7e026478d91e496f27eae6
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67055106"
---
# <a name="set-up-sign-up-and-sign-in-with-a-microsoft-account-using-azure-active-directory-b2c"></a>Azure Active Directory B2C를 사용하여 Microsoft 계정으로 등록 설정 및 로그인

## <a name="create-a-microsoft-account-application"></a>Microsoft 계정 애플리케이션 만들기

Azure AD(Azure Active Directory) B2C에서 Microsoft 계정을 [ID 공급자](active-directory-b2c-reference-oidc.md)로 사용하려면 테넌트에 해당 계정을 나타내는 애플리케이션을 만들어야 합니다. Microsoft 계정이 없는 경우 [https://www.live.com/](https://www.live.com/)에서 얻을 수 있습니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. 선택 **모든 서비스** Azure portal 및 다음에 대 한 검색 및 선택의 왼쪽 위 구석에 **앱 등록**합니다.
1. 선택 **새 등록**
1. 애플리케이션의 **이름**을 입력합니다. 예를 들어 *MSAapp1* 등을 입력합니다.
1. 아래 **지원 되는 계정 유형**를 선택 **모든 조직 디렉터리에 개인 Microsoft 계정 (예: Skype, Xbox, Outlook.com) 계정을**합니다. 이 옵션에는 Microsoft id의 광범위 한 집합을 대상 으로합니다.

   다른 계정 유형 선택 사항에 대 한 자세한 내용은 참조 하세요. [빠른 시작: Microsoft id 플랫폼을 사용 하 여 응용 프로그램을 등록](../active-directory/develop/quickstart-register-app.md)합니다.
1. 아래 **(선택 사항) 리디렉션 URI**를 선택 **웹** enter `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` 텍스트 상자에 합니다. 대체 `your-tenant-name` Azure AD B2C 테 넌 트 이름입니다.
1. 선택 **등록**
1. 레코드를 **응용 프로그램 (클라이언트) ID** 응용 프로그램 개요 페이지에 표시 합니다. 다음 섹션에서 id 공급자를 구성한 경우이 필요 합니다.
1. 선택 **인증서 및 암호**
1. 클릭 **새 클라이언트 암호**
1. 입력을 **설명을** 예, 비밀에 대 한 *응용 프로그램 암호 1*를 클릭 하 고 **추가**.
1. 에 표시 된 응용 프로그램 암호를 기록 합니다 **값** 열입니다. 다음 섹션에서 id 공급자를 구성한 경우이 필요 합니다.

## <a name="configure-a-microsoft-account-as-an-identity-provider"></a>Microsoft 계정을 ID 공급자로 구성

1. Azure AD B2C 테넌트의 전역 관리자로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. Azure AD B2C 테넌트를 포함하는 디렉터리를 사용하려면 위쪽 메뉴에서 **디렉터리 및 구독 필터**를 클릭하고 테넌트가 포함된 디렉터리를 선택합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
1. **ID 공급자**를 선택한 다음, **추가**를 선택합니다.
1. **이름**을 제공합니다. 예를 들어, *MSA*를 입력합니다.
1. **ID 공급자 형식**을 선택하고 **Microsoft 계정**을 선택하고 **확인**을 클릭합니다.
1. 선택 **이 id 공급자 설정** 에서 이전에 기록한 응용 프로그램 (클라이언트) ID를 입력 합니다 **클라이언트 ID** 텍스트 상자 및에 기록 된 클라이언트 암호를 입력 합니다 **클라이언트 비밀** 입력란입니다.
1. **확인**을 클릭한 다음 **만들기**를 클릭하여 Microsoft 계정 구성을 저장합니다.
