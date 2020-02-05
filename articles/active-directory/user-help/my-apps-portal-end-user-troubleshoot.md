---
title: 내 앱 포털에서 도움 받기-Azure Active Directory | Microsoft Docs
description: 에 로그인 하 고 내 앱 포털에서 일반적인 작업을 수행 하는 데 도움을 받으세요.
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
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77022344"
---
# <a name="troubleshoot-problems-with-the-my-apps-portal"></a>My Apps 포털 문제 해결

**내 앱** 포털에 로그인 하거나 사용 하는 데 문제가 발생 하는 경우 기술 지원팀 또는 관리자에 게 문의 하기 전에 이러한 문제 해결 팁을 시도해 보세요.

## <a name="im-having-trouble-installing-the-my-apps-secure-sign-in-extension"></a>내 앱 보안 로그인 확장을 설치 하는 데 문제가 있음

내 앱 보안 로그인 확장을 설치 하는 데 문제가 있는 경우:

- 다음을 포함 하 여 지원 되는 브라우저를 사용 하 고 있는지 확인 합니다.

    - **Microsoft Edge.** Windows 10 기념일 버전 이상에서 실행 됩니다.

    - **Google Chrome.** Windows 7 이상 및 Mac OS X 이상에서 실행 됩니다.

    - **Mozilla Firefox 26.0 이상.** Windows XP SP2 이상 및 Mac OS X 10.6 이상에서 실행 됩니다.

    - **Internet Explorer 11.** Windows 7 이상에서 실행 (제한 된 지원)

- 브라우저 확장 설정이 켜 졌는 지 확인 합니다.

- 브라우저를 다시 시작 하 고 **My Apps** 포털에 다시 로그인 해 보세요.

- 브라우저의 쿠키를 지운 다음 다시 시작 하 고 **My Apps** 포털에 다시 로그인 하세요.

## <a name="i-cant-sign-in-to-the-my-apps-portal"></a>**My Apps** 포털에 로그인 할 수 없습니다.

**내 앱** 포털에 로그인 하는 데 문제가 있는 경우 다음을 시도할 수 있습니다.

- 올바른 URL을 사용 하 고 있는지 확인 합니다. https://myapps.microsoft.com/contoso.com 와 같이 조직의 사용자 지정 된 페이지 https://myapps.microsoft.com 또는 사용자 지정 된 페이지 여야 합니다.

- 암호가 올바르고 만료 되지 않았는지 확인 합니다. 자세한 내용은 [회사 또는 학교 암호 재설정](active-directory-passwords-update-your-own-password.md)을 참조 하세요.

- 확인 정보가 최신이 고 정확한 지 확인 합니다. 자세한 내용은 [Azure Multi-Factor Authentication 무슨 의미 입니까?](multi-factor-authentication-end-user.md) 또는 [보안 정보 방법 및 정보를 변경](security-info-add-update-methods-overview.md)하는 방법을 참조 하세요.

- **인터넷 속성 > 보안 > 신뢰할 수 있는 사이트** 설정에 **내 앱** 포털 URL을 추가 합니다.

- 브라우저 캐시를 지우고 다시 로그인 해 보세요.

## <a name="my-password-isnt-working"></a>암호가 작동 하지 않습니다.

암호를 잊은 경우 조직에서 암호를 받지 않았거나, 계정에서 잠긴 경우 또는 암호를 변경 하려는 경우에는 [AZURE AD 암호를 잊으셨나요? 도움말을](active-directory-passwords-update-your-own-password.md)참조 하세요.

## <a name="i-want-to-be-able-to-reset-my-own-password"></a>자신의 암호를 다시 설정할 수 있습니다.

자신의 암호를 다시 설정할 수 있으려면 관리자가 먼저 조직의 기능을 설정한 다음 필요한 확인 방법을 업데이트 하 고 확인 해야 합니다. 확인 방법을 업데이트 하는 방법에 대 한 자세한 내용은 [셀프 서비스 암호 재설정 등록](active-directory-passwords-reset-register.md)을 참조 하세요.

## <a name="im-getting-an-access-denied-message-when-i-start-an-app"></a>앱을 시작할 때 액세스 거부 메시지를 받고 있습니다.

**내 앱** 포털에서 앱을 시작한 후 **액세스 거부** 메시지가 표시 되는 경우 다음을 시도할 수 있습니다.

- [내 앱 보안 로그인 확장](my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension) 을 설치 하 고 [지원 되는 브라우저](my-apps-portal-end-user-access.md#supported-browsers)를 사용 하 고 있는지 확인 합니다.

- 앱에 대 한 올바른 URL을 사용 하 고 있는지 확인 하 고 URL이 **인터넷 속성 > 보안 > 신뢰할 수 있는 사이트** 목록에 있는지 확인 합니다.

- 암호가 올바르고 만료 되지 않았는지 확인 합니다. 자세한 내용은 [회사 또는 학교 암호 재설정](active-directory-passwords-update-your-own-password.md)을 참조 하세요.

- 확인 정보가 최신이 고 정확한 지 확인 합니다. 자세한 내용은 [Azure Multi-Factor Authentication 무슨 의미 입니까?](multi-factor-authentication-end-user.md) 또는 [보안 정보 방법 및 정보를 변경](security-info-add-update-methods-overview.md)하는 방법을 참조 하세요.

- 브라우저 캐시를 지우고 다시 로그인 해 보세요.

이러한 작업을 시도한 후에도 여전히 앱에 액세스할 수 없는 경우에는 조직의 지원 센터에 문의 해야 합니다.

## <a name="next-steps"></a>다음 단계

**내 앱** 포털에 로그인 한 후에는 프로필과 계정 정보, 그룹 정보 및 액세스 검토 정보 (사용 권한이 있는 경우)를 업데이트할 수도 있습니다.

- [내 앱 포털에서 앱에 액세스 하 고 앱을 사용](my-apps-portal-end-user-access.md)합니다.

- [프로필 정보를 변경](my-apps-portal-end-user-update-profile.md)합니다.

- [그룹 관련 정보를 보고 업데이트](my-apps-portal-end-user-groups.md)합니다.

- 사용자 [고유의 액세스 검토를 수행](my-apps-portal-end-user-access-reviews.md)합니다.
