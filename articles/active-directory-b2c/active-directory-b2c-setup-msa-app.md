---
title: Azure Active Directory B2C를 사용하여 Microsoft 계정으로 등록 설정 및 로그인 | Microsoft Docs
description: 고객에게 Azure Active Directory B2C를 사용하여 응용 프로그램에서 Microsoft 계정으로 등록 및 로그인을 제공합니다.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/05/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 338c2a197cb50091c3b272e0ce590341ffda1d7f
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2018
ms.locfileid: "43341086"
---
# <a name="set-up-sign-up-and-sign-in-with-a-microsoft-account-using-azure-active-directory-b2c"></a>Azure Active Directory B2C를 사용하여 Microsoft 계정으로 등록 설정 및 로그인

## <a name="create-a-microsoft-account-application"></a>Microsoft 계정 응용 프로그램 만들기

Azure AD(Azure Active Directory) B2C에서 Microsoft 계정을 ID 공급자로 사용하려면 테넌트에 해당 계정을 나타내는 응용 프로그램을 만들어야 합니다. Microsoft 계정이 없는 경우 [https://www.live.com/](https://www.live.com/)에서 얻을 수 있습니다.

1. Microsoft 계정 자격 증명을 사용하여 [Microsoft 응용 프로그램 등록 포털](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)에 로그인합니다.
2. 오른쪽 위 모서리에서 **앱 추가**를 선택합니다.
3. 응용 프로그램에 대한 **이름**을 제공하고 **만들기**를 클릭합니다.
4. 등록 페이지에서 **응용 프로그램 ID** 값을 복사합니다. 테넌트에서 Microsoft 계정을 ID 공급자로 구성하려면 이 값이 필요합니다.
5. **플랫폼 추가**를 선택하고 **웹**을 선택합니다.
6. **리디렉션 URL**에 `https://{tenant}.b2clogin.com/te/{tenant}.onmicrosoft.com/oauth2/authresp`를 입력합니다. **{tenant}** 를 사용자의 테넌트 이름(예: contosob2c)으로 바꿉니다.
7. **응용 프로그램 암호** 아래에서 **새 암호 생성**을 선택합니다. 화면에 표시되는 새 암호를 복사합니다. 테넌트에서 Microsoft 계정을 ID 공급자로 구성하려면 이 값이 필요합니다. 이 암호는 중요한 보안 자격 증명입니다.

## <a name="configure-a-microsoft-account-as-an-identity-provider"></a>Microsoft 계정을 ID 공급자로 구성

1. Azure AD B2C 테넌트의 전역 관리자로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. Azure Portal의 오른쪽 상단 모서리에서 전환하여 Azure AD B2C 테넌트가 있는 디렉터리를 사용하고 있는지 확인합니다. 구독 정보를 선택한 다음, **디렉터리 전환**을 선택합니다. 

    ![Azure AD B2C 테넌트로 전환](./media/active-directory-b2c-setup-msa-app/switch-directories.png)

    테넌트를 포함하는 디렉터리를 선택합니다.

    ![디렉터리 선택](./media/active-directory-b2c-setup-msa-app/select-directory.png)

3. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
4. **ID 공급자**를 선택한 다음, **추가**를 선택합니다.
5. **이름**을 제공합니다. 예를 들어, *MSA*를 입력합니다.
6. **ID 공급자 형식**을 선택하고 **Microsoft 계정**을 선택하고 **확인**을 클릭합니다.
7. **이 ID 공급자 설정**을 선택하고 이전에 기록한 응용 프로그램 ID를 **클라이언트 ID**로 입력한 후, 기록한 암호를 이전에 만든 Microsoft 계정 응용 프로그램의 **클라이언트 암호**로 입력합니다.
8. **확인**을 클릭한 다음 **만들기**를 클릭하여 Microsoft 계정 구성을 저장합니다.

