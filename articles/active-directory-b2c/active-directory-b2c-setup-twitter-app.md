---
title: Azure Active Directory B2C를 사용하여 Twitter 계정으로 등록 설정 및 로그인 | Microsoft Docs
description: 고객에게 Azure Active Directory B2C를 사용하여 응용 프로그램에서 Twitter 계정으로 등록 및 로그인을 제공합니다.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/09/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: bf5ae39d83fd021775fbd18cf23d2e6b9078e748
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/10/2018
ms.locfileid: "37927899"
---
# <a name="set-up-sign-up-and-sign-in-with-a-twitter-account-using-azure-active-directory-b2c"></a>Azure Active Directory B2C를 사용하여 Twitter 계정으로 등록 설정 및 로그인

## <a name="create-a-twitter-application"></a>Twitter 응용 프로그램 만들기

Azure AD(Azure Active Directory) B2C에서 Twitter 계정을 ID 공급자로 사용하려면 테넌트에 해당 계정을 나타내는 응용 프로그램을 만들어야 합니다. Twitter 계정이 없는 경우 [https://twitter.com/signup](https://twitter.com/signup)에서 얻을 수 있습니다.

1. Twitter 자격 증명을 사용하여 [Twitter Apps](https://apps.twitter.com/)에 로그인합니다.
2. **새 앱 만들기**를 선택합니다.
3. **이름**, **설명** 및 **웹 사이트**를 입력합니다.
4. **콜백 URL**에 `https://login.microsoftonline.com/te/{tenant}/{policyId}/oauth1/authresp`를 입력합니다. **{tenant}** 를 테넌트의 이름(예: contosob2c.onmicrosoft.com)으로 바꾸고 **{policyId}** 를 정책 ID(예: b2c_1_policy)로 바꿔야 합니다. Twitter 계정을 사용하는 모든 정책에 대한 콜백 URL을 추가해야 합니다. 응용 프로그램에서 사용 중인 경우 ` login.microsoftonline.com` 대신 `b2clogin.com`을 사용해야 합니다.
5. **Developer Agreement**(개발자 계약)에 동의하고 **Create your Twitter application**(Twitter 응용 프로그램 만들기)을 선택합니다.
7. **Keys and Access Tokens** 탭을 선택합니다.
8. **Consumer Key**(소비자 키) 및 **Consumer Secret**(소비자 암호)의 값을 복사합니다. 테넌트에서 Twitter 계정을 ID 공급자로 구성하려면 둘 모두가 필요합니다.

## <a name="configure-twitter-as-an-identity-provider-in-your-tenant"></a>테넌트에서 Twitter를 ID 공급자로 구성

1. Azure AD B2C 테넌트의 전역 관리자로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. Azure Portal의 오른쪽 상단 모서리에서 전환하여 Azure AD B2C 테넌트가 있는 디렉터리를 사용하고 있는지 확인합니다. 구독 정보를 선택한 다음, **디렉터리 전환**을 선택합니다. 

    ![Azure AD B2C 테넌트로 전환](./media/active-directory-b2c-setup-twitter-app/switch-directories.png)

    테넌트를 포함하는 디렉터리를 선택합니다.

    ![디렉터리 선택](./media/active-directory-b2c-setup-twitter-app/select-directory.png)

3. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
4. **ID 공급자**를 선택한 다음 **추가**를 선택합니다.
5. **이름**을 제공합니다. 예를 들어 *Twitter*를 입력합니다.
6. **ID 공급자 형식**을 선택하고 **Twitter**를 선택한 다음, **확인**을 클릭합니다.
7. **이 ID 공급자 설정**을 선택하고 **클라이언트 ID**에 소비자 키를 입력하고 **클라이언트 암호**에 **소비자 암호**를 입력합니다.
8. **확인**을 클릭한 다음 **만들기**를 클릭하여 Twitter 구성을 저장합니다.