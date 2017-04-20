---
title: "Azure AD: 셀프 서비스 암호 재설정 등록 | Microsoft Docs"
description: "셀프 서비스 암호 재설정을 위한 인증 데이터를 등록합니다."
services: active-directory
keywords: "Active Directory 암호 관리, 암호 관리, Azure AD 셀프 서비스 암호 재설정, SSPR"
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/16/2017
ms.author: joflore
ms.custom: end-user
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 0f10ad4904b10a9554e949e77f0192edcb8f624f
ms.lasthandoff: 04/12/2017


---
# <a name="register-for-self-service-password-reset"></a>셀프 서비스 암호 재설정 등록

최종 사용자로서 관리자가 활성화한 경우 SSPR(셀프 서비스 암호 재설정)을 사용하는 사람에게 문의하지 않고도 암호를 다시 설정하거나 계정 잠금을 해제할 수 있습니다. 이 기능을 사용하려면 먼저 인증 방법을 등록하거나 관리자가 입력한 미리 정의된 인증 방법을 확인해야 합니다.

## <a name="register-or-confirm-authentication-data-with-sspr"></a>SSPR을 사용하여 인증 데이터 등록 또는 확인

1. 장치에서 웹 브라우저를 열고 [암호 재설정 등록 페이지](http://aka.ms/ssprsetup)로 이동합니다.
2. 관리자가 제공한 사용자 이름과 암호를 입력합니다.
3. 관리자가 승인한 옵션에 따라 암호를 다시 설정해야 하는 경우 사용하도록 구성하거나 확인할 수 있는 다음 항목 중 하나 이상이 표시됩니다
    * 사무실 전화 - 이 옵션은 관리자만 설정할 수 있습니다.
    * 인증 전화 - 이 옵션은 문자 메시지 또는 전화를 받을 수 있는 휴대폰과 같이 액세스할 수 있는 다른 전화 번호로 설정해야 합니다. 해당 정보를 사용할 수 있는 권한이 이미 있는 경우 관리자가 휴대폰 번호를 사용하여 이 정보를 채웁니다.
    * 인증 전자 메일 - 이 옵션은 다시 설정해야 하는 암호 없이 액세스할 수 있는 인증 전자 메일 주소로 설정해야 합니다
    * 보안 질문 - 이 옵션은 사용자가 응답하도록 관리자가 승인한 질문 목록을 제공합니다. 둘 이상의 질문에 대해 동일한 대답을 사용할 수 없습니다.
4. 관리자가 요구하는 정보를 제공하고 확인합니다. 둘 이상의 옵션을 사용할 수 있는 경우 다른 방법을 사용할 수 없을 때 유연하게 처리할 수 있도록 여러 가지 방법을 등록하는 것이 좋습니다(예: 여행 및 사무실 전화에 액세스할 수 없음)

    ![인증 방법 등록 후 마침 클릭][Register]

5. 4단계를 완료하고 **마침**을 선택하면 나중에 필요할 때 셀프 서비스 암호 재설정을 사용할 수 있습니다.

인증 전화 또는 인증 전자 메일에 데이터를 입력하면 전역 디렉터리에 표시되지 않습니다. 이 데이터를 볼 수 있는 유일한 사람은 사용자와 관리자입니다. 보안 질문에 대한 대답만 볼 수 있습니다.

관리자는 적절한 방법을 등록했는지 확인하기 위해 일정 기간 후에 인증 방법을 확인해야 할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [셀프 서비스 암호 재설정을 사용하여 암호를 변경하는 방법](active-directory-passwords-update-your-own-password.md)
* [암호 재설정 등록 페이지](http://aka.ms/ssprsetup)
* [암호 재설정 포털](https://passwordreset.microsoftonline.com/)

[Register]: ./media/active-directory-passwords-reset-register/register-2-methods.png "등록된 방법을 보여 주는 암호 재설정 등록 페이지 및 마침 단추"

