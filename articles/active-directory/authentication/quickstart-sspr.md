---
title: 빠른 시작 Azure AD 셀프 서비스 암호 재설정
description: 이 빠른 시작에서는 사용자가 자신의 암호를 재설정할 수 있도록 Azure AD 셀프 서비스 암호 재설정을 신속하게 구성합니다.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: quickstart
ms.date: 07/17/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: eabc30b28ec6c32295c5eeff0f19b31fba622056
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58095622"
---
# <a name="quickstart-self-service-password-reset"></a>빠른 시작: 셀프 서비스 암호 재설정

이 자습서에서는 사용자에게 해당 암호를 재설정하거나 계정을 잠금 해제할 수 있도록 할 수 있도록 IT 관리자가 SSPR(셀프 서비스 암호 재설정)을 구성하는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 조건

* 적어도 평가판 라이선스가 설정된 작동 중인 Azure AD 테넌트
* 전역 관리자 권한이 있는 계정
* 암호를 알고 있는 비 관리자 테스트 사용자. 사용자를 만들어야 하는 경우 [빠른 시작: Azure Active Directory에 새 사용자 추가](../add-users-azure-active-directory.md) 문서를 참조하세요.
* 비 관리자 테스트 사용자가 멤버인지 테스트하는 데 사용할 파일럿 그룹. 그룹을 만들어야 하는 경우 [Azure Active Directory에서 그룹 만들기 및 멤버 추가](../active-directory-groups-create-azure-portal.md) 문서를 참조하세요.

## <a name="enable-self-service-password-reset"></a>셀프 서비스 암호 재설정 사용

> [!VIDEO https://www.youtube.com/embed/Pa0eyqjEjvQ]

1. 기존 Azure AD 테넌트의 **Azure Portal**에 있는 **Azure Active Directory** 아래에서 **암호 재설정**을 선택합니다.

2. **속성** 페이지의 **셀프 서비스 암호 재설정이 사용하도록 설정됨** 옵션에서 **선택함**을 선택합니다.
    * **그룹 선택**에서 이 문서의 필수 구성 요소 섹션에서 만든 파일럿 그룹을 선택합니다.
    * **저장**을 클릭합니다.

3. **인증 방법** 페이지에서 다음을 선택합니다.
   * 재설정에 필요한 방법 수: **1**
   * 사용자가 제공되는 방법:
      * **Email**
      * **모바일 앱 코드(미리 보기)**
   * **저장**을 클릭합니다.

     ![인증][Authentication]

4. **등록** 페이지에서 다음을 선택합니다.
   * 사용자가 로그인 시 등록하도록 요구: **예**
   * 사용자가 인증 정보를 다시 확인하도록 요청받을 때까지의 기간(일) 설정: **365**

## <a name="test-self-service-password-reset"></a>셀프 서비스 암호 재설정 테스트

이제 테스트 사용자를 사용하여 SSPR 구성을 테스트해봅니다. Microsoft가 Azure 관리자 계정에 강력한 인증 요구 사항을 적용하므로 관리자 계정을 사용하는 테스트는 결과를 변경할 수 있습니다. 관리자 암호 정책에 대한 자세한 내용은 [암호 정책](concept-sspr-policy.md) 문서를 참조하세요.

1. InPrivate 또는 incognito 모드에서 새 브라우저 창을 열고 [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)으로 이동합니다.
2. 관리자가 아닌 테스트 사용자로 로그인하고, 인증 전화를 등록합니다.
3. 완료되면 **정상**으로 표시된 단추를 클릭하고 브라우저 창을 닫습니다.
4. InPrivate 또는 incognito 모드에서 새 브라우저 창을 열고 [https://aka.ms/sspr](https://aka.ms/sspr)으로 이동합니다.
5. 관리자가 아닌 테스트 사용자의 사용자 ID를 입력하고, CAPTCHA에서 문자를 입력한 다음, **다음**을 클릭합니다.
6. 암호를 재설정하는 확인 단계 따르기

## <a name="clean-up-resources"></a>리소스 정리

셀프 서비스 암호 재설정을 사용하지 않게 설정하는 방법은 간단합니다. Azure AD 테넌트를 열고 **속성** > **암호 재설정**으로 이동한 다음, **셀프 서비스 암호 재설정이 사용하도록 설정됨** 아래에서 **없음**을 선택합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 클라우드 전용 사용자를 위한 셀프 서비스 암호 재설정을 빠르게 구성하는 방법을 알아보았습니다. 더욱 자세한 롤아웃을 완료하는 방법을 알아보려면 이 롤아웃 가이드를 계속합니다.

> [!div class="nextstepaction"]
> [셀프 서비스 암호 재설정 롤아웃](howto-sspr-deployment.md)

[Authentication]: ./media/quickstart-sspr/sspr-authentication-methods.png "사용 가능한 Azure AD 인증 방법 및 필요한 수량"
