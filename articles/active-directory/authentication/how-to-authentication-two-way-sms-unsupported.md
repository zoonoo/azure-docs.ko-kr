---
title: 양방향 SMS는 더 이상 지원 되지 않습니다. Azure Active Directory
description: 양방향 SMS를 계속 사용 하는 사용자에 게 다른 방법을 사용 하도록 설정 하는 방법을 설명 합니다.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 02/25/2021
ms.author: justinha
author: rhicock
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1e90a6060a9c8202f7f743a0ee433f89b9dc9fdd
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101662762"
---
# <a name="two-way-sms-unsupported"></a>양방향 SMS가 지원 되지 않음

MFA (Azure AD Multi-Factor Authentication) 서버용 양방향 SMS는 원래 2018에서 더 이상 사용 되지 않으며 2021 년 2 월 24 일 이후에는 더 이상 지원 되지 않습니다. 관리자는 양방향 SMS를 계속 사용 하는 사용자에 게 다른 방법을 사용 하도록 설정 해야 합니다.

2020, 및 2021 년 1 월 28 일에 영향을 받는 관리자에 게 전자 메일 알림 및 Azure Portal Service Health 알림 (포털 알림을)을 보냈습니다. 이 경고는 구독에 연결 된 소유자, 공동 소유자, 관리자 및 서비스 관리자 RBAC 역할로 이동 했습니다. 다음 단계를 이미 완료 한 경우에는 아무 조치도 필요 하지 않습니다.

## <a name="required-actions"></a>필요한 작업

1. 사용자가 아직 수행 하지 않은 경우 모바일 앱을 사용 하도록 설정 합니다. 자세한 내용은 [MFA 서버를 사용 하 여 모바일 앱 인증 사용](howto-mfaserver-deploy-mobileapp.md)을 참조 하세요.
1. 최종 사용자에 게 MFA 서버 [사용자 포털](howto-mfaserver-deploy-userportal.md) 을 방문 하 여 모바일 앱을 활성화 하도록 알립니다. [Microsoft Authenticator 앱](https://www.microsoft.com/account/authenticator) 은 양방향 SMS 보다 안전 하기 때문에 권장 되는 확인 옵션입니다. 자세한 내용은 [인증을 위해 전화 전송에서 중단 하는 시간을](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/it-s-time-to-hang-up-on-phone-transports-for-authentication/ba-p/1751752)참조 하세요.
1. 사용자 설정을 양방향 텍스트 메시지에서 모바일 앱으로 기본 방법으로 변경 합니다.

## <a name="faq"></a>FAQ

### <a name="what-if-i-dont-change-the-default-method-from-two-way-sms-to-the-mobile-app"></a>기본 방법을 양방향 SMS에서 모바일 앱으로 변경 하지 않으면 어떻게 되나요?
2021 년 2 월 24 일 이후 양방향 SMS가 실패 합니다. 사용자가 로그인 하 고 MFA를 전달 하려고 하면 오류가 표시 됩니다.

### <a name="how-do-i-change-the-user-settings-from-two-way-text-message-to-mobile-app"></a>사용자 설정을 양방향 텍스트 메시지에서 모바일 앱으로 변경 어떻게 할까요??

다음 단계를 수행 하 여 사용자 설정을 변경 해야 합니다.

1. MFA 서버에서 양방향 텍스트 메시지에 대 한 사용자 목록을 필터링 합니다.
1. 모든 사용자를 선택 합니다.
1. 사용자 편집 대화 상자를 엽니다.
1. 문자 메시지에서 모바일 앱으로 사용자를 변경 합니다.

   ![최종 사용자의 스크린샷](media/how-to-authentication-two-way-sms-unsupported/end-users.png)

### <a name="do-my-users-need-to-take-any-action-if-yes-how"></a>사용자가 어떤 작업을 수행 해야 하나요? 그렇다면 어떻게 하나요?
예. 최종 사용자가 아직 수행 하지 않은 경우 모바일 앱을 활성화 하려면 특정 MFA 서버 사용자 포털을 방문 해야 합니다. 3 단계를 완료 한 후에는 사용자 포털을 방문 하 여 모바일 앱을 설정 하지 않은 모든 사용자가 다시 등록할 때까지 로그인 실패가 시작 됩니다.

### <a name="what-if-my-users-cant-install-the-mobile-app-what-other-options-do-they-have"></a>사용자가 모바일 앱을 설치할 수 없는 경우는 어떻게 되나요? 다른 옵션은 무엇 인가요?
양방향 SMS 또는 모바일 앱에 대 한 대안은 전화 통화입니다. 그러나 Microsoft Authenticator 앱은 권장 되는 확인 방법입니다.

### <a name="will-one-way-sms-be-deprecated-as-well"></a>단방향 SMS도 더 이상 사용 되지 않습니다.
아니요. 양방향 SMS는 더 이상 사용 되지 않습니다. MFA 서버의 경우 단방향 SMS는 다음과 같은 시나리오의 하위 집합에 대해 작동 합니다.

- AD FS 어댑터
- IIS 인증 (사용자 포털 및 구성 필요)
- RADIUS (RADIUS 클라이언트가 액세스 챌린지를 지원 하 고 PAP 프로토콜이 사용 되어야 함)

단방향 SMS를 사용 하 여 모바일 앱을 사용 하는 경우 확인 코드 프롬프트가 필요 하지 않기 때문에이에 대 한 제한 사항이 있습니다.
일부 시나리오에서 단방향 SMS를 사용 하려는 경우에는이 확인란을 선택 된 상태로 둘 수 있지만, **회사 설정** 섹션, **일반** 탭 **사용자 기본값 텍스트 메시지** 를 **양방향** 이 아닌 **단방향** 으로 변경할 수 있습니다. 마지막으로, 기본적으로 SMS에 대 한 디렉터리 동기화를 사용 하는 경우 양방향이 아닌 One-Way로 변경 해야 합니다.

### <a name="how-can-i-check-which-users-are-still-using-two-way-sms"></a>양방향 SMS를 사용 하는 사용자를 확인 하려면 어떻게 해야 하나요?
이러한 사용자를 나열 하려면 **MFA 서버** 를 시작 하 고 **, 사용자** 섹션을 선택 하 고, **사용자 목록 필터** 를 클릭 하 고, **양방향 텍스트 메시지** 를 필터링 합니다.

### <a name="how-do-we-hide-two-way-sms-as-an-option-in-the-mfa-portal-to-prevent-users-from-selecting-it-in-the-future"></a>사용자가 나중에 선택 하는 것을 방지 하기 위해 MFA 포털에서 양방향 SMS를 옵션으로 숨기는 방법
MFA 서버 사용자 포털에서 **설정** 을 클릭 하 여 사용할 수 없도록 **문자 메시지** 를 지울 수 있습니다. 사용자 등록에 AD FS를 사용 하는 경우에도 **AD FS** 섹션에서 마찬가지입니다.

