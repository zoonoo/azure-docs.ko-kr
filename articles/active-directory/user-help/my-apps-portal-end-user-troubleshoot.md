---
title: My Apps 포털-Azure Active Directory 도움 받기 | Microsoft Docs
description: 에 로그인 하 고 My Apps 포털에서 일반적인 작업 수행을 사용 하 여 도움을 받으세요.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: lizross
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: a6e87ae0a8adf28176d9a97cbf1b78943179884a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60475030"
---
# <a name="troubleshoot-problems-with-the-my-apps-portal"></a>My Apps 포털을 사용 하 여 문제 해결
로그인 또는 사용 하 여 문제가 발생 하는 경우는 **My Apps** 포털 문제 해결 팁에 대 한 도움말 기술 지원팀 또는 관리자에 문의 하기 전에 이러한 시도 합니다.

## <a name="im-having-trouble-installing-the-my-apps-secure-sign-in-extension"></a>내 앱 보안 로그인 확장을 설치 하는 데 문제가 있음
내 앱 보안 로그인 확장 설치에 문제가 발생 하면:

- 지원 되는 브라우저를 사용 했는지 확인 등.

    - **Microsoft Edge.** Windows 10 Anniversary edition 이상 실행 합니다.
    - **Google Chrome.** Windows 7 이상 및 Mac OS X에서 버전 또는 나중에 실행 합니다.
    - **Mozilla Firefox 26.0 이상입니다.** Windows XP sp2 이상 및 Mac OS X 10.6 버전 또는 나중에 실행 합니다.
    - **Internet Explorer 11입니다.** Windows 7 또는 이후 (제한적된 지원)를 실행 합니다.

- 브라우저 확장 설정을 설정 되어 있는지 확인 합니다.

- 시도 브라우저를 다시 시작 하 고 로그인 합니다 **My Apps** 포털에 다시 합니다.

- 브라우저의 쿠키를 지운 다음 다시 시작 및 로그인을 **My Apps** 포털에 다시 합니다.

## <a name="i-cant-sign-in-to-the-my-apps-portal"></a>에 로그인 할 수 없습니다는 **My Apps** 포털
로그인 하는 데 문제가 있는 경우는 **My Apps** 포털을 사용해 다음:

- 올바른 URL을 사용 중인지 확인 합니다. 것 https://myapps.microsoft.com 또는 조직에 대 한 사용자 지정된 페이지와 같은 https://myapps.microsoft.com/contoso.com합니다.

- 암호가 올바르고 만료 되지 않았는지 있는지 확인 합니다. 자세한 내용은 참조 하세요. [회사 또는 학교 암호 재설정](active-directory-passwords-update-your-own-password.md)합니다.

- 확인 정보 최신 하 고 정확한 인지 확인 합니다. 자세한 내용은 [항목에서는 Azure Multi-factor Authentication은 무엇을 의미?](multi-factor-authentication-end-user.md) 또는 [보안 정보 방법과 정보를 변경](security-info-add-update-methods-overview.md)합니다.

- 추가 합니다 **내 앱** 포털에 대 한 URL를 **인터넷 속성 > 보안 > 신뢰할 수 있는 사이트** 설정 합니다.

- 브라우저의 캐시를 지우고 다시 로그인 하려고 합니다.

## <a name="my-password-isnt-working"></a>내 암호가 작동 하지 않습니다.
암호를 잊으셨나요, 되지 조직에서 하나를 수신, 계정, 잠긴 하거나 암호를 변경 하려면를 참조 [Azure AD 암호를 잊어 버린 도움말](active-directory-passwords-update-your-own-password.md)합니다.

## <a name="i-want-to-be-able-to-reset-my-own-password"></a>내 암호를 재설정 하도록 설정 하려는
사용자 고유의 암호를 재설정 되도록 관리자에 게 먼저 설정 해야 조직에 대 한 기능 및 업데이트 하 고에 필요한 인증 방법을 확인 해야 합니다. 확인 메서드를 업데이트 하는 방법에 대 한 자세한 내용은 참조 하세요. [셀프 서비스 암호 재설정 등록](active-directory-passwords-reset-register.md)합니다.

## <a name="im-getting-an-access-denied-message-when-i-start-an-app"></a>앱을 시작할 때 액세스 거부 메시지를 나타남
표시 되는 경우는 **액세스 거부** 에서 앱을 시작한 후 메시지를 **My App** 포털을 사용해 다음:

- 설치한 있는지 확인 합니다 [내 앱 보안 로그인 확장](my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension) 및 사용 중인를 [지원 되는 브라우저](my-apps-portal-end-user-access.md#supported-browsers)합니다.

- 했는지 앱에 대 한 올바른 URL을 사용 중이 고 URL에 있는 사용자 **인터넷 속성 > 보안 > 신뢰할 수 있는 사이트** 목록입니다.

- 암호가 올바르고 만료 되지 않았는지 있는지 확인 합니다. 자세한 내용은 참조 하세요. [회사 또는 학교 암호 재설정](active-directory-passwords-update-your-own-password.md)합니다.

- 확인 정보 최신 하 고 정확한 인지 확인 합니다. 자세한 내용은 [항목에서는 Azure Multi-factor Authentication은 무엇을 의미?](multi-factor-authentication-end-user.md) 또는 [보안 정보 방법과 정보를 변경](security-info-add-update-methods-overview.md)합니다.

- 브라우저의 캐시를 지우고 다시 로그인 하려고 합니다.

이러한 작업을 시도한 후 여전히 앱에 액세스할 수 없으면, 조직의 기술 지원팀에 문의 해야 있습니다.

## <a name="next-steps"></a>다음 단계
에 로그인 한 후 합니다 **My Apps** 포털 업데이트할 수도 있습니다 프로필 및 계정 정보, 그룹 정보 및 액세스 (있는 경우 사용 권한) 정보를 검토 합니다.

- [액세스 하 고 My Apps 포털에서 앱 사용](my-apps-portal-end-user-access.md)합니다.

- [프로필 정보 변경](my-apps-portal-end-user-update-profile.md)합니다.

- [보기 및 사용자 그룹 관련 정보를 업데이트](my-apps-portal-end-user-groups.md)합니다.

- [자신의 액세스 검토를 수행](my-apps-portal-end-user-access-reviews.md)합니다.