---
title: 2단계 인증 문제 해결 - Azure AD | Microsoft Docs
description: Azure Multi-Factor Authentication 및 2단계 인증에 문제가 발생할 경우, 수행할 작업에 대한 지침을 사용자에게 제공합니다.
services: multi-factor-authentication
documentationcenter: ''
author: eross-msft
manager: mtillman
ms.assetid: 8f3aef42-7f66-4656-a7cd-d25a971cb9eb
ms.service: multi-factor-authentication
ms.workload: identity
ms.topic: conceptual
ms.date: 07/16/2018
ms.author: lizross
ms.reviewer: kexia
ms.openlocfilehash: ec79adcaf7cbef778b8c9e572ae932d5ceb47aa6
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/17/2018
ms.locfileid: "39090380"
---
# <a name="get-help-with-two-step-verification"></a>2단계 인증에 관한 도움말 얻기

2단계 인증은 조직이 계정을 보호하는 데 사용하는 보안 기능입니다. 2단계 인증은 사용자가 알고 있는 것과 휴대하는 것, 두 가지 형태의 인증을 사용하기 때문에 단순 암호에 비해 더 안전합니다. 알고 있는 것은 암호이고 휴대하는 것은 휴대폰이나 장치입니다. 2단계 인증을 사용하면 악의적인 해커가 사용자의 암호를 얻더라도 사용자로 로그인하지 못하게 차단할 수 있습니다.

2단계 인증을 제공하는 것은 Microsoft이지만, 기능을 사용하도록 결정하는 것은 사용자의 조직입니다. 계정을 보호하기 위한 암호 사용을 거부할 수 없는 것처럼 조직에서 요구하는 경우, 사용자는 이를 거부할 수 없습니다.

>[!Note]
>개인 Microsoft 계정에 2단계 인증을 사용하는 방법에 대한 자세한 내용은 [2단계 인증 정보](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification) 문서를 참조하세요.

## <a name="why-do-i-need-to-use-another-verification-method"></a>또 다른 인증 방법을 사용해야 하는 이유는 무엇인가요?

계정에 대체 인증 방법을 사용해야 하는 몇 가지 이유가 있습니다. 예: 

- **휴대폰이나 장치를 기기를 잊은 경우** 전화기를 집에 두고 온 날에도, 직장에서는 여전히 로그인해야 합니다. 먼저 휴대폰이 필요하지 않은 다른 방법을 사용하여 로그인을 시도해야 합니다.

- **휴대폰을 분실한 경우 또는 전화 번호가 바뀐 경우** 휴대폰을 분실했거나 번호가 바뀐 경우, 다른 방법으로 로그인하거나 관리자에게 요청하여 설정을 지울 수 있습니다. 휴대폰을 분실하거나 도난 당한 경우, 계정에 적절한 업데이트를 적용할 수 있도록 관리자에게 알리는 것이 좋습니다. 설정이 지워진 후 다음에 로그인할 때 [2단계 인증 등록](multi-factor-authentication-end-user-first-time.md) 메시지가 표시됩니다.

- **인증 문자 또는 전화 통화를 받을 수 없는 경우** 문자나 전화 통화를 받을 수 없는 몇 가지 이유가 있습니다. 과거에 문자나 전화 통화를 성공적으로 받은 경우에는 계정이 아니라 휴대폰 공급자의 문제일 수 있습니다. 종종 잘못된 신호로 인해 지연된 경우, 모바일 장치에서 [Microsoft Authenticator 앱](microsoft-authenticator-app-how-to.md)을 사용하는 것이 좋습니다. 이 앱은 셀 신호 또는 인터넷 연결이 없어도 로그인용으로 임의 보안 코드를 생성할 수 있습니다.<br><br>문자 메시지를 받으려는 경우, 친구에게 테스트로 문자를 보내도록 부탁하여 문자를 받을 수 있는지 확인하고, 여러 개의 메시지를 받은 경우에는 가장 최근 문자의 코드를 사용하고 있는지 확인합니다.

- **앱 암호가 작동하지 않는 경우** 2단계 인증을 지원하지 않는 이전 데스크톱 응용 프로그램의 경우, 앱 암호가 사용자의 일반 암호를 대체합니다. 먼저 암호를 올바르게 입력했는지 확인합니다. 그래도 문제가 해결되지 않으면 로그인을 시도하여 [새 앱 암호를 만들](multi-factor-authentication-end-user-app-passwords.md)거나 관리자에게 문의하여 새 암호를 만들 수 있도록 [기존 앱 암호를 삭제](../authentication/howto-mfa-userdevicesettings.md)하도록 요청합니다.

## <a name="sign-in-using-another-verification-method"></a>또 다른 인증 방법을 사용하여 로그인

1. 계정에 정상적으로 로그인하고 **2단계 인증** 페이지에서 **다른 방법으로 로그인** 링크를 선택합니다.

    ![로그인 인증 방법 변경](./media/multi-factor-authentication-end-user-troubleshoot/two-factor-auth-signin-another-way.png)

    >[!Note]
    >**다른 방법으로 로그인** 링크가 표시되지 않으면 다른 인증 방법을 설정하지 않은 것입니다. 계정에 로그인하는 데 도움이 필요하면 관리자에게 문의해야 합니다. 로그인한 후 다른 인증 방법을 추가해야 합니다. 인증 방법 추가에 대한 자세한 내용은 [2단계 인증을 위한 설정 관리](multi-factor-authentication-end-user-manage-settings.md) 문서를 참조하세요.<br><br>링크가 표시되지만 여전히 다른 인증 방법이 표시되지 않는 경우, 계정에 로그인하는 데 도움이 필요하면 관리자에게 문의해야 합니다.

2. 대체 인증 방법을 선택하고 2단계 인증 프로세스를 계속합니다.

3. 계정에 다시 로그인한 후 인증 방법을 업데이트할 수 있습니다(필요한 경우). 방법 추가 또는 변경에 대한 자세한 내용은 [2단계 인증을 위한 설정 관리](multi-factor-authentication-end-user-manage-settings.md) 문서를 참조하세요.

## <a name="i-didnt-find-an-answer-to-my-problem"></a>문제에 대한 답변을 찾을 수 없는 경우

이러한 단계를 시도했지만 여전히 문제가 계속되면 관리자에게 문의하여 도움을 받으세요.

## <a name="related-topics"></a>관련된 항목

* [2단계 인증을 위한 설정 관리](multi-factor-authentication-end-user-manage-settings.md)

* [Microsoft Authenticator 응용 프로그램 FAQ](microsoft-authenticator-app-faq.md)
