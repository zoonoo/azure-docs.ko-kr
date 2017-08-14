---
title: "Azure AD: SSPR 등록 | Microsoft Docs"
description: "Azure AD 셀프 서비스 암호 재설정을 위한 인증 데이터를 등록합니다."
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: gahug
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/07/2017
ms.author: joflore
ms.custom: end-user
ms.translationtype: HT
ms.sourcegitcommit: caaf10d385c8df8f09a076d0a392ca0d5df64ed2
ms.openlocfilehash: b701172c2345313e236a037f5aa16939cfaac440
ms.contentlocale: ko-kr
ms.lasthandoff: 08/08/2017

---
# <a name="register-for-self-service-password-reset"></a>셀프 서비스 암호 재설정 등록

> [!IMPORTANT]
> **로그인하는 데 문제가 있나요?** 그렇다면 [암호를 변경하고 재설정하는 방법은 다음과 같습니다](active-directory-passwords-update-your-own-password.md).

최종 사용자로서 SSPR(셀프 서비스 암호 재설정)을 사용하는 사람에게 문의하지 않고도 암호를 재설정하거나 계정 잠금을 해제할 수 있습니다. 이 기능을 사용하려면 먼저 인증 방법을 등록하거나 관리자가 입력한 미리 정의된 인증 방법을 확인해야 합니다.

## <a name="register-or-confirm-authentication-data-with-sspr"></a>SSPR을 사용하여 인증 데이터 등록 또는 확인

1. 장치에서 웹 브라우저를 열고 [암호 재설정 등록 페이지](http://aka.ms/ssprsetup)로 이동합니다.
2. 관리자가 제공한 사용자 이름과 암호를 입력합니다.
3. IT 직원이 작업을 구성한 방식에 따라 구성하고 확인하는 데 다음 옵션 중 하나 이상을 사용할 수 있습니다. 관리자가 정보를 사용할 사용 권한을 가진 경우 사용자를 대신하여 일부 항목을 입력할 수 있습니다.
    * 사무실 전화는 관리자만 설정할 수 있습니다.
    * 인증 전화는 문자 또는 전화를 받을 수 있는 휴대폰과 같이 액세스하려는 다른 전화 번호를 설정해야 합니다.
    * 인증 전자 메일은 재설정해야 하는 암호 없이 액세스할 수 있는 인증 전자 메일 주소로 설정해야 합니다
    * 보안 질문은 사용자가 응답하도록 관리자가 승인한 질문 목록을 제공합니다. 두 번 이상 동일한 질문 또는 대답을 사용할 수 없습니다.
4. 관리자가 요구하는 정보를 제공하고 확인합니다. 둘 이상의 옵션을 사용할 수 있는 경우 다른 방법을 사용할 수 없을 때 유연하게 처리할 수 있도록 여러 가지 방법을 등록하는 것이 좋습니다(예: 여행 및 사무실 전화에 액세스할 수 없음)

    ![인증 방법 등록 후 마침 클릭][Register]

5. 4단계를 완료하고 **마침**을 선택하면 나중에 필요할 때 셀프 서비스 암호 재설정을 사용할 수 있습니다.

인증 전화 또는 인증 전자 메일에 데이터를 입력하면 전역 디렉터리에 표시되지 않습니다. 이 데이터를 볼 수 있는 유일한 사람은 사용자와 관리자입니다. 보안 질문에 대한 대답만 볼 수 있습니다.

관리자는 적절한 방법을 등록했는지 확인하기 위해 일정 기간 후에 인증 방법을 확인해야 할 수 있습니다.

## <a name="common-problems-and-their-solutions"></a>일반적인 문제 및 해결 방법

 다음은 일반적인 오류 사례 및 해결 방법입니다.

| 오류 사례| 어떤 오류가 표시되나요?| 해결 방법 |
| --- | --- | --- |
| 내 사용자 ID를 입력한 후에 "관리자에게 문의하세요" 페이지가 보입니다. | 관리자에게 문의하세요 <br> <br> Microsoft에서 사용자 계정 암호가 관리되지 않는 것이 감지되었습니다. 결과적으로 자동으로 암호를 재설정할 수 없습니다. <br> <br> IT 직원에게 추가 지원을 요청해야 합니다. | IT 직원이 온-프레미스 환경에서 암호를 관리하고 [계정 링크에 액세스할 수 없음]에서 암호를 재설정할 수 없기 때문에 이 메시지가 표시됩니다. <br> <br> 암호를 재설정하려면 도움을 얻기 위해 직접 IT 직원에게 문의하고 암호를 재설정하려고 한다고 알려서 사용자가 이 기능을 사용할 수 있도록 합니다.|
| 내 사용자 ID를 입력한 후에 "계정은 암호 재설정에 사용할 수 없습니다." 오류가 발생합니다. | 암호 재설정을 위해 계정을 사용할 수 없습니다. <br> <br> 죄송하지만 IT 직원이 계정을 이 서비스와 함께 사용하도록 설정하지 않습니다. <br> <br> 원하는 경우 사용자의 조직에서 관리자에게 연락하여 사용자용 암호를 재설정할 수 있습니다. | IT 직원이 [계정에 액세스할 수 없음] 링크에서 조직에 대해 암호 재설정을 활성화하지 않았거나 기능을 사용하도록 허가하지 않았기 때문에 이 메시지가 표시됩니다. <br> <br> 암호를 재설정하려면 도움을 얻기 위해 직접 관리자에게 문의 링크를 클릭하여 회사의 IT 직원에게 메일을 보내고 암호를 재설정하려고 한다고 알려서 사용자가 이 기능을 사용할 수 있도록 합니다. |
| 내 사용자 ID를 입력한 후에 "계정을 확인하지 못했습니다" 오류가 발생합니다. | 계정을 확인하지 못했습니다. <br> <br> 원하는 경우 사용자의 조직에서 관리자에게 연락하여 사용자용 암호를 재설정할 수 있습니다. | 암호 재설정에 사용할 수 있지만 서비스를 사용하도록 등록하지 않았기 때문에 이 메시지가 표시됩니다. 암호 재설정을 등록하려면 계정에 대한 액세스를 회복한 후에 http://aka.ms/ssprsetup로 이동합니다. <br> <br> 암호를 재설정하려면 관리자에게 문의 링크를 클릭하여 회사의 IT 직원에게 전자 메일을 보냅니다. |

## <a name="next-steps"></a>다음 단계

* [셀프 서비스 암호 재설정을 사용하여 암호를 변경하는 방법](active-directory-passwords-update-your-own-password.md)
* [암호 재설정 등록 페이지](http://aka.ms/ssprsetup)
* [암호 재설정 포털](https://passwordreset.microsoftonline.com/)
* [Microsoft 계정에 로그인할 수 없는 경우](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)

[Register]: ./media/active-directory-passwords-reset-register/register-2-methods.png "등록된 방법을 보여 주는 암호 재설정 등록 페이지 및 마침 단추"


