---
title: Microsoft 계정을 사용 하 여 등록 및 로그인 설정-Azure Active Directory B2C
description: Azure Active Directory B2C를 사용 하 여 응용 프로그램에서 Microsoft 계정을 사용 하 여 고객에 게 등록 및 로그인을 제공 합니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 134ca92671a41eb53201101a3622215466a60c09
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71065104"
---
# <a name="set-up-sign-up-and-sign-in-with-a-microsoft-account-using-azure-active-directory-b2c"></a>Azure Active Directory B2C를 사용하여 Microsoft 계정으로 등록 설정 및 로그인

## <a name="create-a-microsoft-account-application"></a>Microsoft 계정 애플리케이션 만들기

Azure Active Directory B2C (Azure AD B2C)에서 Microsoft 계정를 [id 공급자로](active-directory-b2c-reference-oidc.md) 사용 하려면 Azure AD 테 넌 트에서 응용 프로그램을 만들어야 합니다. Azure AD 테넌트는 Azure AD B2C 테넌트와 다릅니다. Microsoft 계정 아직 없는 경우에서 [https://www.live.com/](https://www.live.com/)가져올 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택 하 고 azure ad 테 넌 트가 포함 된 디렉터리를 선택 하 여 azure ad 테 넌 트를 포함 하는 디렉터리를 사용 하 고 있는지 확인 합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택한 다음, **앱 등록**을 검색하여 선택합니다.
1. **새 등록**을 선택합니다.
1. 애플리케이션의 **이름**을 입력합니다. 예를 들어 *MSAapp1* 등을 입력합니다.
1. **지원 되는 계정 유형**에서 **조직 디렉터리와 개인 Microsoft 계정 (예: Skype, Xbox, Outlook.com)의 계정을**선택 합니다. 이 옵션은 가장 넓은 Microsoft id 집합을 대상으로 합니다.

   다른 계정 유형 선택 [에 대 한 자세한 내용은 빠른 시작: Microsoft id 플랫폼](../active-directory/develop/quickstart-register-app.md)에 응용 프로그램을 등록 합니다.
1. **URI 리디렉션 (선택 사항)** 에서 **웹** 을 선택 하 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` 고 텍스트 상자에을 입력 합니다. Azure AD B2C `your-tenant-name` 테 넌 트 이름으로 대체 합니다.
1. **등록** 선택
1. 응용 프로그램 개요 페이지에 표시 된 **응용 프로그램 (클라이언트) ID** 를 기록 합니다. 다음 섹션에서 id 공급자를 구성할 때 필요 합니다.
1. **인증서 & 암호** 선택
1. **새 클라이언트 암호** 를 클릭 합니다.
1. 비밀에 대 한 **설명** (예: *응용 프로그램 암호 1)* 을 입력 한 다음 **추가**를 클릭 합니다.
1. **값** 열에 표시 된 응용 프로그램 암호를 기록 합니다. 다음 섹션에서 id 공급자를 구성할 때 필요 합니다.

## <a name="configure-a-microsoft-account-as-an-identity-provider"></a>Microsoft 계정을 ID 공급자로 구성

1. Azure AD B2C 테넌트의 전역 관리자로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택 하 고 테 넌 트가 포함 된 디렉터리를 선택 하 여 Azure AD B2C 테 넌 트를 포함 하는 디렉터리를 사용 하 고 있는지 확인 합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
1. **Id 공급자**를 선택한 다음 **Microsoft 계정**을 선택 합니다.
1. **이름**을 입력합니다. 예: *MSA*.
1. **클라이언트 id**에 대해 이전에 만든 Azure AD 응용 프로그램의 응용 프로그램 (클라이언트) id를 입력 합니다.
1. **클라이언트 암호**에 대해 기록한 클라이언트 암호를 입력 합니다.
1. **저장**을 선택합니다.
