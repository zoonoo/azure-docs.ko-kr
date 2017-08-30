---
title: "Azure AD: 암호 재설정 | Microsoft Docs"
description: "셀프 서비스 암호 재설정을 사용하여 회사 또는 학교 사용자 계정에 대한 액세스 권한 다시 얻기"
services: active-directory
keywords: 
documentationcenter: 
author: barlanmsft
manager: femila
ms.reviewer: gahug
ms.assetid: 7ba69b18-317a-4a62-afa3-924c4ea8fb49
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: barlan
ms.custom: end-user
ms.translationtype: Human Translation
ms.sourcegitcommit: b1d56fcfb472e5eae9d2f01a820f72f8eab9ef08
ms.openlocfilehash: 6ee3d1319e7727b1134472a1b62a3de7a3571997
ms.contentlocale: ko-kr
ms.lasthandoff: 07/06/2017

---
# <a name="help-i-forgot-my-azure-ad-password"></a>Azure AD 암호를 잊어버렸어요. 도와주세요!

암호를 잊어버린 경우, IT 부서에서 암호를 받지 못한 경우, 계정에서 암호가 잠긴 경우 또는 암호를 변경하려는 경우 저희에게 도움을 요청할 수 있습니다. 알고 있는 암호를 변경해야 하는 경우 아래 [암호 변경](#change-my-password) 섹션으로 이동합니다.

   > [!NOTE]
   > Xbox, hotmail.com 또는 outlook.com 같은 개인 계정에 다시 액세스하려면 [이 문서의 권장 사항](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)을 시도해 보세요.
   >

## <a name="reset-or-unlock-my-password-for-a-work-or-school-account"></a>회사 또는 학교 계정의 암호 재설정 또는 잠금 해제

다음 중 하나로 인해 계정에 액세스할 수 없는 경우:

* 암호가 작동하지 않아 암호를 재설정하려고 합니다.
* 암호를 알지만 계정이 잠겨 있어 계정을 잠금 해제하려고 합니다.

다음에 나오는 단계에 따라 Azure AD SSPR(셀프 서비스 암호 재설정)에 액세스하고 계정으로 돌아갑니다.

1. 회사 또는 학교 로그인 페이지에서 **계정에 액세스할 수 없으세요?** 링크를 클릭한 다음 **회사 또는 학교 계정**을 클릭하거나 [암호 재설정 페이지](https://passwordreset.microsoftonline.com/)로 직접 이동합니다.

    ![계정에 액세스할 수 없으세요?][Login]

2. 회사 또는 학교 **사용자 ID**를 입력하고, 화면에 표시되는 문자를 입력하여 로봇이 아님을 증명하고, **다음**을 클릭합니다.

   > [!NOTE]
   > IT 담당자가 이 기능을 활성화하지 않은 경우 IT 담당자가 자체 전자 메일 또는 웹 포털을 통해 도움을 줄 수 있도록 "관리자에게 문의" 링크가 나타납니다.
   >
   > 계정 잠금을 해제해야 하는 경우 지금은 “암호를 알고 있지만, 여전히 로그인할 수 없습니다.” 옵션을 선택합니다.
   >

3. IT 담당자가 SSPR을 구성한 방법에 따라 다음 항목 중 하나 이상이 표시됩니다. 사용자 또는 IT 담당자가 [셀프 서비스 암호 재설정 등록](active-directory-passwords-reset-register.md) 문서를 사용하기 전에 이 정보 중 일부를 입력했습니다.

   * **대체 전자 메일로 메일 보내기**
   * **휴대폰으로 문자 보내기**
   * **휴대폰으로 전화 걸기**
   * **사무실 번호로 전화 걸기**
   * **보안 질문에 답하기**

   옵션을 선택하고, 올바른 응답을 입력하고, **다음**을 클릭합니다.

   ![인증 데이터 확인][Verification]

4. IT 담당자가 추가 확인이 필요할 수 있으며, 그에 따라 사용자가 다른 옵션을 선택하여 3단계를 반복해야 할 수도 있습니다.
5. **새 암호 선택** 페이지에서 새 암호를 입력하고 암호를 확인한 다음 **마침**을 클릭합니다. 대문자와 소문자, 숫자 및 특수 문자가 포함된 8-16자 암호가 좋습니다.
6. **암호가 재설정되었습니다.**가 표시되면 새 암호로 로그인할 수 있습니다.

    ![암호가 재설정되었습니다.][Complete]

이제 계정에 액세스할 수 있을 것입니다. 만약 액세스할 수 없으면 조직의 IT 담당자에게 연락하여 추가 도움을 요청해야 합니다.

계정에서 "Microsoft가 \<귀하의 조직을 대신하여>"와 같은 확인 전자 메일을 받을 수도 있습니다. 이러한 전자 메일을 받았고 계정에 대한 액세스 권한을 다시 얻기 위해 셀프 서비스 암호 재설정을 사용하지 않았으면 조직의 IT 담당자에게 문의하세요.

## <a name="change-my-password"></a>내 암호 변경

이미 알고 있는 암호를 변경해야 하는 경우 이어지는 단계에 따라 암호를 변경합니다.

### <a name="change-your-password-from-the-office-365-portal"></a>Office 365 포털에서 암호 변경

주로 Office 포털을 사용하여 응용 프로그램에 액세스하는 경우 이 방법을 사용하세요.

1. 기존 암호를 사용하여 [Office 365 계정](https://www.office.com)에 로그인
2. 오른쪽 위에서 자신의 프로필을 클릭하고 **계정 보기**를 클릭합니다.
3. **보안 및 개인 정보 보호** > **암호**를 클릭합니다.
4. 이전 암호를 입력하고, 새 암호를 입력하고 확인한 후 **제출**을 클릭합니다.

### <a name="change-your-password-from-the-azure-access-panel"></a>Azure 액세스 패널에서 암호 변경

주로 Azure Access Portal을 사용하여 응용 프로그램에 액세스하는 경우 이 방법을 사용하세요.

1. 기존 암호를 사용하여 [Azure 액세스 포털](https://myapps.microsoft.com/)에 로그인합니다.
2. 오른쪽 위에서 자신의 프로필을 클릭한 다음 **프로필**을 클릭합니다.
3. **암호 변경**을 클릭합니다.
4. 이전 암호를 입력하고, 새 암호를 입력하고 확인한 후 **제출**을 클릭합니다.

## <a name="common-problems-and-their-solutions"></a>일반적인 문제 및 해결 방법

 다음은 일반적인 오류 사례 및 해결 방법입니다.

| 오류 사례| 어떤 오류가 표시되나요?| 해결 방법 |
| --- | --- | --- |
| 내 사용자 ID를 입력한 후에 "관리자에게 문의하세요" 페이지가 보입니다. | 관리자에게 문의하세요 <br> <br> Microsoft에서 사용자 계정 암호가 관리되지 않는 것이 감지되었습니다. 결과적으로 자동으로 암호를 재설정할 수 없습니다. <br> <br> IT 직원에게 추가 지원을 요청해야 합니다. | IT 직원이 온-프레미스 환경에서 암호를 관리하고 [계정 링크에 액세스할 수 없음]에서 암호를 재설정할 수 없기 때문에 이 메시지가 표시됩니다. <br> <br> 암호를 재설정하려면 도움을 얻기 위해 직접 IT 직원에게 문의하고 암호를 재설정하려고 한다고 알려서 사용자가 이 기능을 사용할 수 있도록 합니다.|
| 내 사용자 ID를 입력한 후에 "계정은 암호 재설정에 사용할 수 없습니다." 오류가 발생합니다. | 암호 재설정을 위해 계정을 사용할 수 없습니다. <br> <br> 죄송하지만 IT 직원이 계정을 이 서비스와 함께 사용하도록 설정하지 않습니다. <br> <br> 원하는 경우 사용자의 조직에서 관리자에게 연락하여 사용자용 암호를 재설정할 수 있습니다. | IT 직원이 [계정에 액세스할 수 없음] 링크에서 조직에 대해 암호 재설정을 활성화하지 않았거나 기능을 사용하도록 허가하지 않았기 때문에 이 메시지가 표시됩니다. <br> <br> 암호를 재설정하려면 도움을 얻기 위해 직접 관리자에게 문의 링크를 클릭하여 회사의 IT 직원에게 메일을 보내고 암호를 재설정하려고 한다고 알려서 사용자가 이 기능을 사용할 수 있도록 합니다. |
| 내 사용자 ID를 입력한 후에 "계정을 확인하지 못했습니다" 오류가 발생합니다. | 계정을 확인하지 못했습니다. <br> <br> 원하는 경우 사용자의 조직에서 관리자에게 연락하여 사용자용 암호를 재설정할 수 있습니다. | 암호 재설정에 사용할 수 있지만 서비스를 사용하도록 등록하지 않았기 때문에 이 메시지가 표시됩니다. 암호 재설정을 등록하려면 계정에 대한 액세스를 회복한 후에 http://aka.ms/ssprsetup로 이동합니다. <br> <br> 암호를 재설정하려면 관리자에게 문의 링크를 클릭하여 회사의 IT 직원에게 전자 메일을 보냅니다. |

## <a name="next-steps"></a>다음 단계

* [셀프 서비스 암호 재설정을 사용하도록 등록하는 방법](active-directory-passwords-reset-register.md)
* [암호 재설정 등록 페이지](http://aka.ms/ssprsetup)
* [암호 재설정 포털](https://passwordreset.microsoftonline.com/)
* [Microsoft 계정에 로그인할 수 없는 경우](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)

[Login]: ./media/active-directory-passwords-update-your-own-password/reset-1-login.png "계정에 액세스할 수 없으세요?"
[Verification]: ./media/active-directory-passwords-update-your-own-password/reset-2-verification.png "인증 데이터 확인"
[Change]: ./media/active-directory-passwords-update-your-own-password/reset-3-change.png "암호 변경"
[Complete]: ./media/active-directory-passwords-update-your-own-password/reset-4-complete.png "암호가 재설정되었습니다."

