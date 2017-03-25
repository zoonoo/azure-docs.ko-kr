---
title: "Azure Active Directory를 사용하여 고유한 암호를 업데이트하는 방법 | Microsoft Docs"
description: "암호 재설정을 위해 등록할 수 있는 방법, 암호를 변경하는 방법 및 잊어버린 경우 고유한 암호를 재설정하는 방법을 알아봅니다."
services: active-directory
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
editor: curtand
ms.assetid: 7ba69b18-317a-4a62-afa3-924c4ea8fb49
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2017
ms.author: joflore
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: f0a27b909c4f41300e877a70760db71ca0d36270
ms.lasthandoff: 03/15/2017


---
# <a name="how-to-update-your-own-password"></a>고유한 암호를 업데이트하는 방법
회사 또는 학교 계정 암호를 관리하는 방법을 잘 모를 경우 잘 찾아 오셨습니다. 암호 변경, 암호 재설정 또는 암호 재설정을 위한 등록과 같은 일반적인 단계를 수행하는 방법을 알아보려면 아래를 읽어보세요.

* [**계정에 대한 액세스를 손실하지 마세요!**](#dont-lose-access-to-your-account)
* [**Office 365에서 암호를 변경하는 방법**](#how-to-change-your-password-from-o365)
* [**액세스 패널에서 암호를 변경하는 방법**](#how-to-change-your-password-from-the-access-panel)
* [**암호를 재설정하는 방법**](#how-to-reset-your-password)
* [**사용자 계정의 잠금을 해제하는 방법**](#how-to-unlock-your-account)
* [**일반적인 문제 및 해결 방법**](#common-problems-and-their-solutions)

## <a name="dont-lose-access-to-your-account"></a>계정에 대한 액세스를 손실하지 마세요!
> [!IMPORTANT]
> **왜 이것이 표시되나요?** 링크를 따라 여기에 왔다면 관리자는 사용자에게 암호 재설정을 위해 등록하여 앱에 대한 액세스 권한을 얻도록 요구하기 때문에 이것을 보게 될 수도 있습니다. 보안 질문을 설정하거나, 전화 또는 전자 메일 정보를 요청받을 수도 있습니다.  걱정하지 마세요! 사용자 계정을 더 안전하게 유지하기 위해서만 이 정보를 사용합니다. 여기에 제시된 단계는 목표에 도달하는 데 도움이 됩니다.
>
>

암호 재설정을 위해 등록하는 가장 빠른 방법은 [http://aka.ms/ssprsetup](http://aka.ms/ssprsetup)으로 이동하는 것입니다.  

1. [http://aka.ms/ssprsetup](http://aka.ms/ssprsetup)으로 이동합니다.
2. 사용자 이름 및 암호를 입력합니다.
3. **지금 설정**을 클릭하여 등록에 옵션을 선택합니다. 이 경우 **인증 전화**를 등록하는 방법을 보여 줍니다. 관리자는 둘 이상의 옵션을 등록하도록 요구할 수 있습니다. 

   ![][101]
4. 드롭다운에서 국가 코드를 선택하고, **지역 번호를 포함한 전체 전화 번호**를 입력하고, 코드를 제공하는 **문자 메시지 인증** 또는 **전화 인증** 옵션 중 하나를 선택합니다. 여기서는 문자 메시지 인증을 선택했습니다.

   ![][102]
5. 코드가 도착하면 입력 상자에 입력한 다음 "확인"을 클릭합니다. 

   ![][103]
6. **마침**이 표시되면 이것으로 끝입니다! 언제든지 [https://passwordreset.microsoftonline.com](https://passwordreset.microsoftonline.com)으로 이동하여 암호를 재설정하기 위해 등록한 옵션을 사용할 수 있습니다.

   ![][104]

   > [!IMPORTANT]
   > 관리자가 둘 이상의 옵션에 등록하도록 하는 경우 휴대폰 또는 전자 메일 액세스를 분실할 경우를 대비하여 백업 옵션을 등록하는 것이 좋습니다.
   >
   >

## <a name="how-to-change-your-password-from-o365"></a>O365에서 암호를 변경하는 방법
Office 365에서 회사 또는 학교 계정 암호를 변경하려면 아래 단계를 수행합니다. 암호를 잊어서 다시 설정 하려는 경우 [여기](#how-to-reset-your-password)단계를 수행합니다.

1. 회사 또는 학교 계정을 사용하여 Office 365에 로그인합니다.
2. **설정** > **Office 365 설정** > **암호** > **암호 변경**으로 이동합니다.
3. 이전 암호를 입력하고 새 암호를 입력한 다음 확인합니다.
4. **Save**를 클릭합니다.

자세한 내용은 [Office 365 설명서 센터](https://support.office.com/article/Change-my-password-in-Office-365-for-business-d1efbaee-63a7-4c08-ab1d-71bf932bbb5d)에서 읽을 수 있습니다.

## <a name="how-to-change-your-password-from-the-access-panel"></a>액세스 패널에서 암호를 변경하는 방법
[액세스 패널](https://myapps.microsoft.com)에서 회사 또는 학교 계정 암호를 변경하려면 아래 단계를 수행합니다. 암호를 잊어서 다시 설정 하려는 경우 [여기](#how-to-reset-your-password)단계를 수행합니다.

1. 회사 또는 학교 계정으로 https://myapps.microsoft.com에 로그인합니다.
2. **프로필** 탭을 클릭합니다.
3. 화면 오른쪽에서 **암호 변경** 타일을 클릭합니다.
4. 이전 암호를 입력하고 새 암호를 입력한 다음 확인합니다.
5. **제출**을 클릭합니다.

   암호를 변경하는 문제에 직면한 경우  [일반적인 문제 및 해결 방법](#common-problems-and-their-solutions)을 읽습니다.

## <a name="how-to-reset-your-password"></a>암호를 재설정하는 방법
회사 또는 학교 계정 로그인 화면에서 회사 또는 학교 계정 암호를 재설정하려면 아래 단계를 따릅니다.

> [!IMPORTANT]
> 이 기능은 관리자가 설정한 경우에 사용할 수 있습니다. 설정되지 않은 경우 계정에 이 기능을 사용할 수 없다는 메시지가 표시됩니다.  이 경우 "관리자에게 문의" 링크를 사용하여 계정의 잠금을 해제하기 위해 관리자에게 연락할 수 있습니다.
>
> 관리자가 이 기능을 사용하도록 설정한 경우 사용하기 전에 먼저 등록해야 합니다. http://aka.ms/ssprsetup에서 수행할 수 있습니다.
>
>

1. 회사 또는 학교 계정 로그인 페이지에서 "계정에 액세스할 수 없습니까?"링크를 클릭하거나 https://passwordreset.microsoftonline.com으로 직접 이동합니다. link, or navigate to https://passwordreset.microsoftonline.com directly.

   ![][110]
2. "누구십니까?" 페이지에서 회사 또는 학교 계정 ID를 입력하고 그림 안의 문자를 전달하여 로봇이 아님을 입증합니다.

   ![][111]
3. "다음" 단추를 클릭합니다.
4. 옵션을 선택하여 암호를 재설정합니다. 관리자가 시스템을 구성한 방법에 따라 다음 선택 항목 중 하나 이상이 표시될 수 있습니다.

   * **내 암호 확인용 대체 전자 메일 보내기** - **대체 전자 메일** 또는 **인증 전자 메일**(선택) 중 하나에 6자리 코드가 포함된 메일을 보냅니다.
   * **내 휴대폰으로 문자 메시지 보내기** - **휴대폰** 또는 **인증 전자 메일**(선택) 중 하나에 6자리 코드가 포함된 문자 메시지를 보냅니다.
   * **휴대폰 전화걸기** - **휴대폰** 또는 **인증 전화**(선택)에 전화를 겁니다 - *#* 키를 눌러서 전화를 확인합니다.
   * **사무실 전화 전화걸기** - **사무실 전화**로 전화를 겁니다 - *#* 키를 눌러서 전화를 확인합니다.
   * **보안 질문 답변** - 대답할 미리 등록된 보안 질문을 표시합니다.

   ![][109]
5. 예로 "휴대폰 문자 보내기" 옵션을 사용합니다. 휴대폰 기반 옵션을 사용하는 경우 문자 메시지를 보내기 전에 전화 번호를 확인해야 합니다. 전체 전화 번호를 입력한 다음 **문자 메시지**를 클릭하여 올바른 전화 번호인지 확인하고 문자 메시지를 보냅니다.

6. 문자를 받으면 보낸 번호가 아닌 본문의 확인 코드를 사용하도록 합니다. 문자 메시지를 가져오는 데 몇 분이 걸릴 수 있습니다.

7. 이제 휴대폰에서 방금 받은 코드를 페이지의 입력 상자에 입력하고 **다음**을 선택합니다.

8. 관리자가 추가 확인 단계를 요구할 수 있으며, 이 경우 4단계를 반복하여 다른 옵션을 선택합니다.
9. "새 암호 선택" 화면에서 새 암호를 선택하고 선택을 확인한 다음 **마침**을 클릭합니다.

   ![][107]

10. 암호가 수락되면 새 암호로 로그인할 수 있습니다.

    ![][108]

암호를 재설정하는 문제에 직면한 경우 [일반적인 문제 및 해결 방법](#common-problems-and-their-solutions)을 읽습니다.

## <a name="how-to-unlock-your-account"></a>사용자 계정의 잠금을 해제하는 방법
회사 또는 학교 계정 로그인 화면에서 로컬 계정의 잠금을 해제하려면 아래 단계를 따릅니다. 

> [!NOTE]
> 온-프레미스에서 잠긴 계정인 경우에만 계정 잠금을 해제할 수 있습니다.

> [!IMPORTANT]
> 이 기능은 관리자가 설정한 경우에 사용할 수 있습니다. 설정되지 않은 경우 사용자 계정이 이 기능에 대해 사용 불가능하다는 내용의 메시지가 표시됩니다. 이 경우 "관리자에게 문의" 링크를 사용하여 계정의 잠금을 해제하기 위해 관리자에게 연락할 수 있습니다.
>
> 관리자가 이 기능을 활성화한 경우 사용하기 전에 먼저 등록해야 합니다. http://aka.ms/ssprsetup에서 수행할 수 있습니다.
>
>

1. 회사 또는 학교 계정 로그인 페이지에서 "계정에 액세스할 수 없습니까?" 링크를 클릭하거나 https://passwordreset.microsoftonline.com으로 직접 이동합니다.

   ![][110]
2. "누구십니까?" 페이지에서 회사 또는 학교 계정 ID를 입력하고 그림 안의 문자를 전달하여 로봇이 아님을 입증합니다.

   ![][111]
3. "다음" 단추를 클릭합니다.
4. 옵션을 선택하여 계정의 잠금을 해제합니다. 관리자가 시스템을 구성한 방법에 따라 다음 선택 항목 중 하나 이상이 표시될 수 있습니다.

   * **내 암호 확인용 대체 전자 메일 보내기** - **대체 전자 메일** 또는 **인증 전자 메일**(선택) 중 하나에 6자리 코드가 포함된 메일을 보냅니다.
   * **내 휴대폰으로 문자 메시지 보내기** - **휴대폰** 또는 **인증 전자 메일**(선택) 중 하나에 6자리 코드가 포함된 문자 메시지를 보냅니다.
   * **휴대폰 전화걸기** - **휴대폰** 또는 **인증 전화**(선택)에 전화를 겁니다 - *#* 키를 눌러서 전화를 확인합니다.
   * **사무실 전화 전화걸기** - **사무실 전화**로 전화를 겁니다 - *#* 키를 눌러서 전화를 확인합니다.
   * **보안 질문 답변** - 대답할 미리 등록된 보안 질문을 표시합니다.

   ![][112]
5. 예를 들어 "보안 질문에 답변" 옵션을 사용합니다. 보안 질문에 대한 답변을 입력하고 **다음**을 선택하여 본인 여부를 확인합니다.

6. 관리자가 추가 확인 단계를 요구할 수 있으며, 이 경우 4단계를 반복하여 다른 옵션을 선택해야 합니다.
7. 성공 페이지가 표시되면 이동해도 됩니다! 온-프레미스 계정의 잠금이 해제되었고 이제 다시 로그인할 수 있습니다.

   ![][113]

   > [!IMPORTANT]
   > 종종 예전 암호를 사용하는 악의적인 앱(예: 전화 전자 메일 클라이언트)이 애초에 계정이 잠긴 원인일 수 있기 때문에 모든 장치를 최신 암호로 업데이트해야 합니다.
   >
   >

계정의 잠금을 해제하는 문제에 직면한 경우 [일반적인 문제 및 해결 방법](#common-problems-and-their-solutions)을 읽습니다.

## <a name="common-problems-and-their-solutions"></a>일반적인 문제 및 해결 방법
다음은 일반적인 오류 사례 및 해결 방법입니다.

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>오류 사례</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>어떤 오류가 보입니까?</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>해결 방법</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>내 사용자 ID를 입력한 후에 "관리자에게 문의하세요" 페이지가 보입니다.</p>
            </td>
            <td>
              <p>관리자에게 문의하세요. <br><br>Microsoft에서 사용자 계정 암호가 관리되지 않는 것이 감지되었습니다. 결과적으로 자동으로 암호를 재설정할 수 없습니다. <br><br>추가 지원에 대해 관리자 또는 기술 지원팀에 문의해야 합니다. </p>
            </td>
            <td>
              <p>관리자가 온-프레미스 환경에서 암호를 관리하고 <b>계정 링크에 액세스할 수 없음</b>에서 암호를 다시 설정할 수 없기 때문에 이 메시지가 표시됩니다. <br><br> 암호를 재설정하려면 도움을 얻기 위해 직접 관리자에게 문의하고 Office 365에서 암호를 재설정하려고 한다고 알려서 사용자가 이 기능을 사용할 수 있도록 합니다.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>내 사용자 ID를 입력한 후에 "계정은 암호 재설정에 사용할 수 없습니다." 오류가 발생합니다.</p>
            </td>
            <td>
              <p>암호 재설정을 위해 계정을 사용할 수 없습니다.<br><br>죄송하지만 관리자가 계정을 이 서비스와 함께 사용하도록 설정하지 않습니다.<br><br> 원하는 경우 사용자의 조직에서 관리자에게 연락하여 사용자용 암호를 재설정할 수 있습니다.</p>
            </td>
            <td>
              <p>관리자가 <b>계정에 액세스할 수 없음</b> 링크에서 조직에 대해 암호 재설정을 활성화하지 않았거나 기능을 사용하도록 허가하지 않았기 때문에 이 메시지가 표시됩니다. <br><br> 암호를 재설정하려면 도움을 얻기 위해 직접 <b>관리자에게 문의</b> 링크를 클릭하여 회사의 관리자에게 메일을 보내고 Office 365에서 암호를 재설정하려고 한다고 알려서 사용자가 이 기능을 사용할 수 있도록 합니다.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>내 사용자 ID를 입력한 후에 "계정을 확인하지 못했습니다" 오류가 발생합니다.</p>
            </td>
            <td>
              <p>계정을 확인하지 못했습니다.<br><br>원하는 경우 사용자의 조직에서 관리자에게 연락하여 사용자용 암호를 재설정할 수 있습니다. </p>
            </td>
            <td>
              <p>암호 재설정에 사용할 수 있지만 서비스를 사용하도록 등록하지 않았기 때문에 이 메시지가 표시됩니다.  암호 재설정을 등록하려면 계정에 대한 액세스를 회복한 후에 http://aka.ms/ssprsetup로 이동합니다. <br><br> 암호를 재설정하려면 <b>관리자에게 문의</b> 링크를 클릭하여 회사의 관리자에게 전자 메일을 보냅니다.</p>
            </td>
          </tr>
        </tbody></table>

## <a name="next-steps"></a>다음 단계
SSPR(셀프 서비스 암호 다시 설정)에 대해 추가 질문이 있으면 관리자에게 문의하거나 아래 링크를 따릅니다.

* [SSPR 정보를 등록해야 하나요?](http://aka.ms/ssprsetup)
* [계정에 액세스할 수 없으세요?](https://passwordreset.microsoftonline.com)
* [Office 365 암호 다시 설정 정보](https://support.office.com/en-us/article/Reset-user-passwords-in-Office-365-3254c031-04c9-44f1-8fda-2563847a6b31?ui=en-US&rs=en-US&ad=US)
* [액세스 패널](https://myapps.microsoft.com)

[101]: ./media/active-directory-passwords-update-your-own-password/password-1-dont-lose-access.png "password-1-dont-lose-access.png"
[102]: ./media/active-directory-passwords-update-your-own-password/password-2-verification-response.png "password-2-verification-response.png"
[103]: ./media/active-directory-passwords-update-your-own-password/password-2-verification-text.png "password-2-verification-text.png"
[104]: ./media/active-directory-passwords-update-your-own-password/password-3-registration-complete.png "password-3-registration-complete.png"
[105]: ./media/active-directory-passwords-update-your-own-password/password-4-reset-cant-access.png "password-4-reset-cant-access.png"
[106]: ./media/active-directory-passwords-update-your-own-password/password-4-reset-captcha.png "password-4-reset-captcha.png"
[107]: ./media/active-directory-passwords-update-your-own-password/password-4-reset-change.png "password-4-reset-change.png"
[108]: ./media/active-directory-passwords-update-your-own-password/password-4-reset-finished.png "password-4-reset-finished.png"
[109]: ./media/active-directory-passwords-update-your-own-password/password-4-reset-verification.png "password-4-reset-verification.png"
[110]: ./media/active-directory-passwords-update-your-own-password/password-5-unlock-cant-access.png "password-5-unlock-cant-access.png"
[111]: ./media/active-directory-passwords-update-your-own-password/password-5-unlock-captcha.png "password-5-unlock-captcha.png"
[112]: ./media/active-directory-passwords-update-your-own-password/password-5-unlock-verification.png "password-5-unlock-verification.png"
[113]: ./media/active-directory-passwords-update-your-own-password/password-5-unlock-finished.png "password-5-unlock-finished.png"

