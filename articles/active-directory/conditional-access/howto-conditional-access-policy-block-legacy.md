---
title: 조건부 액세스-레거시 인증 차단-Azure Active Directory
description: 레거시 인증 프로토콜을 차단 하는 사용자 지정 조건부 액세스 정책 만들기
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: b992973beb7cb132075e47e104733d812dc06ca0
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73151092"
---
# <a name="conditional-access-block-legacy-authentication"></a>조건부 액세스: 레거시 인증 차단

레거시 인증 프로토콜과 관련 된 위험이 증가 함에 따라 조직은 이러한 프로토콜을 사용 하 여 인증 요청을 차단 하 고 최신 인증을 요구 하는 것이 좋습니다.

## <a name="create-a-conditional-access-policy"></a>조건부 액세스 정책 만들기

다음 단계는 레거시 인증 요청을 차단 하는 조건부 액세스 정책을 만드는 데 도움이 됩니다.

1. 전역 관리자, 보안 관리자 또는 조건부 액세스 관리자 권한으로 **Azure Portal** 에 로그인 합니다.
1. **조건부 액세스**를 > **Azure Active Directory** 으로 이동 합니다.
1. **새 정책**을 선택합니다.
1. 정책에 이름을 지정 합니다. 조직에서 정책 이름에 대해 의미 있는 표준을 만드는 것이 좋습니다.
1. **할당**아래에서 **사용자 및 그룹** 을 선택 합니다.
   1. **포함**아래에서 **모든 사용자**를 선택 합니다.
   1. **제외**아래에서 **사용자 및 그룹** 을 선택 하 고 레거시 인증을 사용 하는 기능을 유지 해야 하는 계정을 선택 합니다. 
   1. **완료** 를 선택합니다.
1. **포함** > **클라우드 앱 또는 작업** 아래에서 **모든 클라우드 앱**을 선택 합니다.
   1. 특정 응용 프로그램을 정책에서 제외 해야 하는 경우 제외 된 **클라우드 앱 선택** 의 **제외** 탭에서 해당 응용 프로그램을 선택 하 고 **선택**을 선택할 수 있습니다.
   1. **완료** 를 선택합니다.
1. **클라이언트 앱 (미리 보기)**  > **조건** 에서 **구성** 을 **예**로 설정 합니다.
   1. **모바일 앱 및 데스크톱 클라이언트만** **다른 클라이언트** > 확인란을 선택 합니다.
   2. **완료** 를 선택합니다.
1. **액세스 제어** > **권한 부여**에서 **액세스 차단**을 선택 합니다.
   1. **선택**을 선택합니다.
1. 설정을 확인 하 고 **정책 사용** 을 **켜기**로 설정 합니다.
1. 만들기 **를 선택 하** 여 정책을 사용 하도록 설정 합니다.

## <a name="next-steps"></a>다음 단계

[조건부 액세스 공통 정책](concept-conditional-access-policy-common.md)

[조건부 액세스 What If 도구를 사용 하 여 로그인 동작 시뮬레이션](troubleshoot-conditional-access-what-if.md)
