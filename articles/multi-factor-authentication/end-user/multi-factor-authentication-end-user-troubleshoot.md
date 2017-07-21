---
title: "2단계 인증 문제 해결 | Microsoft Docs"
description: "이 문서는 Azure 다단계 인증에 문제가 있는 경우 수행할 작업에 대한 정보를 제공합니다."
services: multi-factor-authentication
keywords: "다단계 인증 클라이언트, 인증 문제, 상관관계 ID"
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 8f3aef42-7f66-4656-a7cd-d25a971cb9eb
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/15/2017
ms.author: kgremban
ms.reviewer: yossib
ms.custom: end-user
ms.translationtype: Human Translation
ms.sourcegitcommit: 6adaf7026d455210db4d7ce6e7111d13c2b75374
ms.openlocfilehash: 10b63a859c67b965734e32ec979b26a1ea6cc516
ms.contentlocale: ko-kr
ms.lasthandoff: 06/22/2017

---
# <a name="having-trouble-with-two-step-verification"></a>2단계 인증 문제 발생
이 문서에서는 2단계 인증에서 발생할 수 있는 몇 가지 문제에 대해 설명합니다. 여기에 포함되지 않은 문제가 생긴 경우 개선할 수 있도록 의견 섹션에 자세한 피드백을 보내주세요.

## <a name="i-lost-my-phone-or-it-was-stolen"></a>전화를 분실했거나 도난당한 경우
두 가지 방법으로 계정에 다시 들어올 수 있습니다. 첫 번째 방법은 대체 인증 전화 번호를 설정한 경우 이를 사용하여 로그인하는 것입니다. 두 번째 방법은 설정을 지우도록 관리자에게 문의하는 것입니다.

전화를 분실했거나 도난당한 경우 관리자에게 앱 암호를 초기화하고 기억된 장치를 삭제하도록 문의하는 것이 좋습니다. 관리자가 이 작업을 수행하는 방법을 모르는 경우 [사용자 및 장치 관리](../multi-factor-authentication-manage-users-and-devices.md) 문서를 알려 주세요.

### <a name="use-an-alternate-phone-number"></a>대체 전화 번호 사용
다른 장치에서 보조 전화 번호 또는 인증자 앱을 비롯한 여러 인증 옵션을 설정한 경우 다음 중 하나를 사용하여 로그인할 수 있습니다.

대체 전화 번호를 사용하여 로그인하려면 다음 단계를 따르세요.

1. 일반적인 방법으로 로그인합니다.
2. 추가로 계정을 확인하라는 메시지가 표시되면 **다른 인증 옵션 사용**을 선택합니다.
   
    ![다양한 확인](./media/multi-factor-authentication-end-user-manage/differentverification.png)
3. 액세스할 수 있는 전화 번호를 선택합니다.
   
    ![대체 전화](./media/multi-factor-authentication-end-user-manage/altphone2.png)
4. 계정으로 다시 돌아오면 [설정을 관리](multi-factor-authentication-end-user-manage-settings.md)하여 인증 전화 번호를 변경할 수 있습니다.

> [!IMPORTANT]
> 보조 인증 전화 번호를 구성하는 것이 중요합니다. 기본 전화 번호 및 모바일 앱이 동일한 전화인 경우, 전화를 분실하거나 도난당한 경우 세 번째 옵션이 필요합니다.   

### <a name="clear-your-settings"></a>설정 지우기
보조 인증 전화 번호를 구성하지 않은 경우 관리자에게 문의해야 합니다. 다음 사용자가 로그인하도록 설정을 지운 경우 다시 [계정을 설정](multi-factor-authentication-end-user-first-time.md)하라는 메시지가 표시됩니다.

## <a name="i-am-not-receiving-a-text-or-call-on-my-phone"></a>휴대폰에서 텍스트 메시지를 받지 못하는 경우
로그인하지만 텍스트 또는 전화 통화를 수신하지는 않으려는 여러 이유가 있습니다. 이전에 전화에 텍스트 또는 전화 통화를 성공적으로 수신한 경우에는 사용자 계정이 아니라 전화 공급자와 관련된 문제입니다. 좋은 셀 신호가 있는지 확인하고 텍스트 메시지를 수신하는 경우 전화 및 서비스 요금제가 텍스트 메시지를 지원하는지 확인합니다.

텍스트 또는 전화를 몇 분 정도 기다린 경우 빠르게 계정에 액세스하는 방법은 다른 옵션을 시도하는 것입니다.

1. 페이지에서 **다른 인증 옵션 사용**을 선택하고 인증을 기다립니다.
   
    ![다양한 확인](./media/multi-factor-authentication-end-user-troubleshoot/diff_option.png)
2. 사용하려는 전화 번호 또는 배달 방법을 선택합니다.
   
    여러 인증 코드를 받은 경우 최신 코드만 작동합니다.

구성한 다른 방법을 사용할 수 없는 경우 관리자에게 문의하고 설정을 지우도록 요청합니다. 다음 번 로그인할 때 다시 [Multi-Factor Authentication 설정](multi-factor-authentication-end-user-first-time.md)하라는 메시지가 표시됩니다.

종종 잘못된 셀 신호로 인해 지연된 경우 스마트폰에서 [Microsoft Authenticator 앱](microsoft-authenticator-app-how-to.md)을 사용하는 것이 좋습니다. 앱은 로그인하는 데 사용할 수 있는 임의의 보안 코드를 생성할 수 있고 이러한 코드는 셀 신호 또는 인터넷 연결이 필요하지 않습니다.

## <a name="app-passwords-are-not-working"></a>앱 암호가 작동하지 않는 경우
먼저 앱 암호를 올바르게 입력했는지 확인합니다. 계속 작동하지 않으면 로그인을 시도하고 [앱 암호를 생성](multi-factor-authentication-end-user-app-passwords.md)합니다.  계속 작동하지 않으면 관리자에게 [기존 앱 암호를 삭제](../multi-factor-authentication-manage-users-and-devices.md)를 요청하면 새 암호를 만들 수 있습니다.

## <a name="i-didnt-find-an-answer-to-my-problem"></a>문제에 대한 답변을 찾을 수 없는 경우
이러한 문제 해결 단계를 시도해도 여전히 문제가 발생하는 경우 관리자에게 문의하거나 사용자에 대한 Multi-Factor Authentication을 설정한 사용자에게 문의합니다. 그러면 도움을 받을 수 있습니다.

또한 [Azure AD 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=WindowsAzureAD) 또는 [지원 문의](https://support.microsoft.com/contactus)에 질문을 게시할 수 있고 가능한 한 문제에 응답합니다.

지원에 문의할 때 다음 정보를 포함합니다.

* **사용자 ID** – 로그인하려는 전자 메일 주소는 무엇인가요?
* **오류에 대한 일반적인 설명** – 사용자가 본 정확한 오류 메시지는 무엇인가요?  오류 메시지가 있는 경우 알아낸 예기치 않은 동작을 자세히 설명합니다.
* **페이지** – 오류(URL 포함)가 나타나는 경우 페이지 정의
* **ErrorCode** - 수신한 특정 오류 코드
* **SessionId** - 수신한 특정 세션 ID
* **상관관계 ID** – 사용자가 오류를 확인했을 때 생성된 상관관계 ID
* **타임스탬프** – 오류(표준 시간대 포함)가 나타나는 경우 정확한 날짜 및 시간 정의

이러한 정보는 로그인 페이지에 있습니다. 로그인 시간을 확인하지 않은 경우 **세부 정보 보기**를 선택합니다.

![로그인 오류 세부 정보](./media/multi-factor-authentication-end-user-troubleshoot/view_details.png)

이 정보를 포함하면 최대한 빨리 문제를 해결하는데 도움이 됩니다.

## <a name="related-topics"></a>관련된 항목
* [2단계 인증을 위한 설정 관리](multi-factor-authentication-end-user-manage-settings.md)  
* [Microsoft Authenticator 응용 프로그램 FAQ](microsoft-authenticator-app-faq.md)


