---
title: 내 앱 포털 - Azure Active Directory | 에 대한 도움말 받기 마이크로 소프트 문서
description: 내 앱 포털에서 로그인하고 일반적인 작업을 수행하는 데 도움이 됩니다.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: curtand
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: f72dd5595d67ae989cec5681d22def9a2f929adf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253157"
---
# <a name="troubleshoot-problems-with-the-my-apps-portal"></a>내 앱 포털 문제 해결

**내 앱** 포털에 로그인하거나 사용하는 데 문제가 있는 경우 헬프 데스크 나 관리자에게 도움을 요청하기 전에 다음 문제 해결 팁을 시도해 보십시오.

## <a name="im-having-trouble-installing-the-my-apps-secure-sign-in-extension"></a>내 앱 보안 로그인 확장 프로그램을 설치하는 데 문제가 있습니다.

내 앱 보안 로그인 확장 프로그램을 설치하는 데 문제가 있는 경우:

- 지원되는 브라우저를 사용하고 있는지 확인합니다.

    - **마이크로 소프트 에지.** 윈도우에서 실행 10 주년 기념 에디션 이상.

    - **구글 크롬.** 윈도우에서 실행 7 이상, 맥 OS X 이상에서.

    - **모질라 파이어 폭스 26.0 이상.** 윈도우 XP SP2 이상에서 실행, 맥 OS X에서 10.6 이상.

    - **인터넷 익스플로러 11.** Windows 7 이상에서 실행 중(지원 제한).

- 브라우저 확장 설정이 켜져 있는지 확인합니다.

- 브라우저를 다시 시작하고 **내 앱** 포털에 다시 로그인해 봅을 사용해 봅을 사용해 봅을 사용해 보세요.

- 브라우저의 쿠키를 삭제한 다음 다시 시작하고 내 **앱** 포털에 다시 로그인합니다.

## <a name="i-cant-sign-in-to-the-my-apps-portal"></a>**내 앱** 포털에 로그인할 수 없습니다.

**내 앱** 포털에 로그인하는 데 문제가 있는 경우 다음을 시도해 볼 수 있습니다.

- 올바른 URL을 사용하고 있는지 확인합니다. 또는 조직(예: https://myapps.microsoft.com https://myapps.microsoft.com/contoso.com)에 대한 사용자 지정 페이지여야 합니다.

- 암호가 올바른지, 만료되지 않았는지 확인합니다. 자세한 내용은 [직장 또는 학교 비밀번호 재설정을](active-directory-passwords-update-your-own-password.md)참조하십시오.

- 확인 정보가 최신이고 정확한지 확인합니다. 자세한 내용은 [Azure 다단계 인증이 무엇을 의미합니까?](multi-factor-authentication-end-user.md) 또는 [보안 정보 방법 및 정보 변경을](security-info-add-update-methods-overview.md)참조하십시오.

- 인터넷 속성 > 보안 **> 신뢰할 수 있는 사이트** 설정에 내 **앱** 포털 URL을 추가합니다.

- 브라우저의 캐시를 지우고 다시 로그인해 봅슬입니다.

## <a name="my-password-isnt-working"></a>비밀번호가 작동하지 않습니다.

암호를 잊어버렸거나, 조직에서 암호를 받지 못했거나, 계정에서 잠겨 있거나, 암호를 변경하려는 경우 도움말을 참조하는 경우 [Azure AD 암호를 잊어버렸습니다.](active-directory-passwords-update-your-own-password.md)

## <a name="i-want-to-be-able-to-reset-my-own-password"></a>내 암호를 재설정할 수 있기를 원합니다.

자신의 암호를 재설정하려면 관리자가 먼저 조직에 대한 기능을 켜야 하며 필요한 확인 방법을 업데이트하고 확인해야 합니다. 확인 방법을 업데이트하는 방법에 대한 자세한 내용은 [셀프 서비스 암호 재설정 등록을](active-directory-passwords-reset-register.md)참조하십시오.

## <a name="im-getting-an-access-denied-message-when-i-start-an-app"></a>앱을 시작할 때 액세스 거부 메시지가 표시됩니다.

**내 앱** 포털에서 앱을 시작한 후 **액세스 거부** 메시지가 있는 경우 다음을 시도해 볼 수 있습니다.

- [내 앱 보안 로그인 확장을](my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension) 설치했는지, [지원되는 브라우저를](my-apps-portal-end-user-access.md#supported-browsers)사용하고 있는지 확인합니다.

- 앱에 적합한 URL을 사용하고 있고 **URL이 인터넷 속성 > 신뢰할 수 있는 사이트** 목록에 있는지 >.

- 암호가 올바른지, 만료되지 않았는지 확인합니다. 자세한 내용은 [직장 또는 학교 비밀번호 재설정을](active-directory-passwords-update-your-own-password.md)참조하십시오.

- 확인 정보가 최신이고 정확한지 확인합니다. 자세한 내용은 [Azure 다단계 인증이 무엇을 의미합니까?](multi-factor-authentication-end-user.md) 또는 [보안 정보 방법 및 정보 변경을](security-info-add-update-methods-overview.md)참조하십시오.

- 브라우저의 캐시를 지우고 다시 로그인해 봅슬입니다.

이러한 작업을 시도한 후에도 앱에 액세스할 수 없는 경우 조직의 헬프 데스크에 문의하여 도움을 받아야 합니다.

## <a name="next-steps"></a>다음 단계

**내 앱** 포털에 로그인한 후 프로필 및 계정 정보, 그룹 정보 및 검토 정보(권한이 있는 경우)를 업데이트할 수도 있습니다.

- [내 앱 포털에서 앱에 액세스하고 사용합니다.](my-apps-portal-end-user-access.md)

- [프로필 정보를 변경합니다.](my-apps-portal-end-user-update-profile.md)

- [그룹 관련 정보를 보고 업데이트합니다.](my-apps-portal-end-user-groups.md)

- [사용자 고유의 액세스 검토를 수행합니다.](my-apps-portal-end-user-access-reviews.md)
