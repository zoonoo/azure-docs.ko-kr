---
title: 빠른 시작 - Azure AD 셀프 서비스 암호 재설정
description: 이 빠른 시작에서는 사용자가 자신의 암호를 재설정하여 IT 부서 오버헤드를 줄일 수 있도록 Azure AD 셀프 서비스 암호 재설정을 구성하는 방법을 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: quickstart
ms.date: 12/10/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: a168f9bf58c4942fc0b76b9ffefc2b32b5bfbe5a
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/31/2019
ms.locfileid: "75549366"
---
# <a name="quickstart-configure-azure-active-directory-self-service-password-reset"></a>빠른 시작: Azure Active Directory 셀프 서비스 암호 재설정 구성

이 자습서에서는 사용자가 자신의 암호를 재설정하거나 계정을 잠금 해제할 수 있도록 Azure AD(Active Directory) SSPR(셀프 서비스 암호 재설정)을 구성합니다. SSPR을 사용하면, 기술 지원팀이나 관리자의 도움 없이 사용자가 자신의 자격 증명을 다시 설정할 수 있습니다. 이 기능을 통해, 사용자는 추가 지원을 기다리지 않고 자신의 계정에 다시 액세스할 수 있습니다.

> [!IMPORTANT]
> 이 빠른 시작에서는 관리자가 셀프 서비스 암호 재설정을 사용하도록 설정하는 방법을 보여줍니다. 셀프 서비스 암호 재설정에 이미 등록된 최종 사용자가 계정으로 다시 이동해야 하는 경우 https://aka.ms/sspr 로 이동합니다.
>
> IT 팀이 자신의 암호를 재설정 하는 기능을 사용하도록 설정하지 않은 경우, 추가 지원이 필요 하면 기술 지원팀에 문의하세요.

## <a name="prerequisites"></a>사전 요구 사항

* 적어도 평가판 라이선스가 설정된 작동 중인 Azure AD 테넌트
    * 필요한 경우, [체험 계정을 만드세요](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* 전역 관리자 권한이 있는 계정
* 알고 있는 암호가 있는 관리자가 아닌 테스트 사용자(예: *testuser*)
    * 사용자를 만들어야 하는 경우 [빠른 시작: Azure Active Directory에 새 사용자 추가](../add-users-azure-active-directory.md)의 정보를 사용할 수 있습니다.
* 관리자가 아닌 테스트 사용자가 멤버로 속해 있는 테스트할 파일럿 그룹(예: *SSPR-Test-Group*)
    * 그룹을 만들어야 하는 경우 [Azure Active Directory에서 그룹 만들기 및 멤버 추가](../active-directory-groups-create-azure-portal.md)를 참조하세요.

## <a name="enable-self-service-password-reset"></a>셀프 서비스 암호 재설정 사용

[이 프로세스를 YouTube의 비디오로 보기](https://youtu.be/Pa0eyqjEjvQ)

1. Azure Portal 메뉴나 **홈** 페이지에서 **Azure Active Directory**를 선택한 다음, **암호 재설정**을 선택합니다.

1. **속성** 페이지의 **셀프 서비스 암호 재설정이 사용하도록 설정됨** 옵션에서 **선택함**을 선택합니다.
1. **그룹 선택**을 선택한 다음, 이 문서에서 사전 요구 사항 섹션의 일부로 만든 파일럿 그룹을 선택합니다(예: *SSPR-Test-Group*). 준비되면 **저장**을 선택합니다.
1. **인증 방법** 페이지에서 다음을 선택한 다음, **저장**을 선택합니다.
    * 재설정에 필요한 방법 수: **1**
    * 사용자가 제공되는 방법:
        * **모바일 앱 코드**
        * **Email**

    > [!div class="mx-imgBorder"]
    > ![SSPR에 대한 인증 방법 선택][Authentication]

4. **등록** 페이지에서 다음을 선택한 다음, **저장**을 선택합니다.
   * 사용자가 로그인 시 등록하도록 요구: **예**
   * 사용자가 인증 정보를 다시 확인하도록 요청받을 때까지의 기간(일) 설정: **365**

## <a name="test-self-service-password-reset"></a>셀프 서비스 암호 재설정 테스트

이제 이전 섹션에서 선택한 그룹의 일원인 테스트 사용자로 SSPR 구성을 테스트하겠습니다(예: *testuser*). Microsoft가 Azure 관리자 계정에 강력한 인증 요구 사항을 적용하므로 관리자 계정을 사용하는 테스트는 결과를 변경할 수 있습니다. 관리자 암호 정책에 대한 자세한 내용은 [암호 정책](concept-sspr-policy.md) 문서를 참조하세요.

1. InPrivate 또는 incognito 모드에서 새 브라우저 창을 열고 [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)으로 이동합니다.
2. 관리자가 아닌 테스트 사용자(예: *testuser*)로 로그인하고, 인증 전화를 등록합니다.
3. 완료되면 **정상**으로 표시된 단추를 선택하고 브라우저 창을 닫습니다.
4. InPrivate 또는 incognito 모드에서 새 브라우저 창을 열고 [https://aka.ms/sspr](https://aka.ms/sspr)으로 이동합니다.
5. 관리자가 아닌 테스트 사용자의 사용자 ID(예: *testuser*)를 입력하고, CAPTCHA의 문자를 입력한 후 **다음**을 선택합니다.
6. 확인 단계를 수행하여 암호를 다시 설정합니다.

## <a name="clean-up-resources"></a>리소스 정리

셀프 서비스 암호 재설정을 사용하지 않도록 설정하려면 Azure Portal에서 **Azure Active Directory**를 검색하여 선택합니다. **속성** > **암호 재설정**을 선택한 다음, **셀프 서비스 암호 재설정이 사용하도록 설정됨** 아래에서 **없음**을 선택합니다. 준비되면 **저장**을 선택합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 클라우드 전용 사용자를 위한 셀프 서비스 암호 재설정을 구성하는 방법을 알아보았습니다. 더욱 자세한 롤아웃을 완료하는 방법을 알아보려면 이 롤아웃 가이드를 계속합니다.

> [!div class="nextstepaction"]
> [셀프 서비스 암호 재설정 롤아웃](howto-sspr-deployment.md)

[Authentication]: ./media/quickstart-sspr/sspr-authentication-methods.png "사용 가능한 Azure AD 인증 방법 및 필요한 수량"

<!-- INTERNAL LINKS -->
[register-sspr]: ../user-help/active-directory-passwords-reset-register.md
[reset-password]: ../user-help/active-directory-passwords-update-your-own-password.md
