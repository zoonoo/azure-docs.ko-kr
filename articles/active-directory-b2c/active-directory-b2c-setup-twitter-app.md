---
title: 'Azure Active Directory B2C: Twitter 구성 | Microsoft 문서'
description: 소비자에게 Azure Active Directory B2C를 사용하여 보안이 유지되는 응용 프로그램에서 Twitter 계정으로 등록 및 로그인을 제공합니다.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 4/06/2017
ms.author: davidmu
ms.openlocfilehash: ee2d82f8c90b88a898428973a1febaa21034a14f
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-twitter-accounts"></a>Azure Active Directory B2C: 고객에게 Twitter 계정으로 등록 및 로그인 제공

## <a name="create-a-twitter-application"></a>Twitter 응용 프로그램 만들기
Azure AD(Azure Active Directory) B2C에서 Twitter를 ID 공급자로 사용하려면 Twitter 응용 프로그램을 만들고 올바른 매개 변수를 제공해야 합니다. 이렇게 하려면 Twitter 개발자 계정이 필요합니다. 계정이 없는 경우 [https://dev.twitter.com/](https://dev.twitter.com/)에서 가져올 수 있습니다.

1. [Twitter developer's website](https://dev.twitter.com/)(Twitter 개발자 웹 사이트)로 이동하고 사용자 자격 증명으로 로그인합니다.
2. **Tools & Support**(도구 및 지원)에서 **My apps**(내 앱)를 클릭하고 **Create New App**(새 앱 만들기)을 클릭합니다. 
3. 폼에서 **Name**(이름), **Description**(설명) 및 **Website**(웹 사이트)의 값을 입력합니다.
4. **Callback URL**(콜백 URL)에 `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`를 입력합니다. **{tenant}**를 테넌트의 이름(예: contosob2c.onmicrosoft.com)으로 바꿔야 합니다.
5. 상자를 선택하여 **Developer Agreement**(개발자 계약)에 동의하고 **Create your Twitter application**(Twitter 응용 프로그램 만들기)을 클릭합니다.
6. 앱이 만들어지면 **Keys and Access Tokens**(키 및 액세스 토큰) 탭을 클릭합니다.
7. **Consumer Key**(소비자 키) 및 **Consumer Secret**(소비자 암호)의 값을 복사합니다. 테넌트에서 Twitter를 ID 공급자로 구성하려면 둘 모두가 필요합니다.

## <a name="configure-twitter-as-an-identity-provider-in-your-tenant"></a>테넌트에서 Twitter를 ID 공급자로 구성
1. 다음 단계에 따라 [Azure 포털의 B2C 기능 블레이드로 이동합니다](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) .
2. B2C 기능 블레이드에서 **ID 공급자**를 클릭합니다.
3. 블레이드의 위쪽에서 **+추가**를 클릭합니다.
4. ID 공급자 구성에 친숙한 **이름** 을 제공합니다. 예를 들어 "Twitter"를 입력합니다.
5. **ID 공급자 형식**을 클릭하고 **Twitter**를 선택한 다음 **확인**을 클릭합니다.
6. **이 ID 공급자 설정**을 클릭하고 **클라이언트 ID**에 Twitter **Consumer Key**(소비자 키)를 입력하고 **클라이언트 암호**에 Twitter **Consumer Secret**(소비자 암호)을 입력합니다.
7. **확인**을 클릭한 다음 **만들기**를 클릭하여 Twitter 구성을 저장합니다.

## <a name="next-steps"></a>다음 단계

[기본 제공 정책](active-directory-b2c-reference-policies.md)을 만들거나 편집하고 Twitter를 ID 공급자로 추가합니다.