---
title: 조건부 액세스-준수 장치 필요-Azure Active Directory
description: 규정 준수 장치를 요구 하는 사용자 지정 조건부 액세스 정책 만들기
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 08/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: da1a5f54e5e989f661770d518a6753b831b59bd4
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2019
ms.locfileid: "69990523"
---
# <a name="conditional-access-require-compliant-devices"></a>조건부 액세스: 준수 디바이스 필요

Microsoft Intune를 배포한 조직은 장치에서 반환 된 정보를 사용 하 여 다음과 같은 규정 준수 요구 사항을 충족 하는 장치를 식별할 수 있습니다.

* 잠금을 해제 하는 데 PIN 필요
* 장치 암호화 필요
* 최소 또는 최대 운영 체제 버전 필요
* 무단 해제 또는 루 팅 되지 않은 장치 요구

이 정책 준수 정보는 Azure AD에 전달 되며, 조건부 액세스에서 리소스에 대 한 액세스를 부여 하거나 차단 하는 결정을 내릴 수 있습니다.

## <a name="create-a-conditional-access-policy"></a>조건부 액세스 정책 만들기

다음 단계는 리소스에 액세스 하는 장치가 조직의 Intune 준수 정책을 준수 하는 것으로 표시 되도록 요구 하는 조건부 액세스 정책을 만드는 데 도움이 됩니다.

1. 전역 관리자, 보안 관리자 또는 조건부 액세스 관리자 권한으로 **Azure Portal** 에 로그인 합니다.
1. **Azure Active Directory** > **조건부 액세스**로 이동 합니다.
1. **새 정책**을 선택합니다.
1. 정책에 이름을 지정 합니다. 조직에서 정책 이름에 대해 의미 있는 표준을 만드는 것이 좋습니다.
1. **할당**아래에서 **사용자 및 그룹** 을 선택 합니다.
   1. **포함**아래에서 **모든 사용자**를 선택 합니다.
   1. **제외**아래에서 **사용자 및 그룹** 을 선택 하 고 조직의 응급 액세스 또는 사용 중단 계정을 선택 합니다. 
   1. **완료** 를 선택합니다.
1. **클라우드 앱 또는 작업** > **포함**아래에서 **모든 클라우드 앱**을 선택 합니다.
   1. 특정 응용 프로그램을 정책에서 제외 해야 하는 경우 제외 된 **클라우드 앱 선택** 의 **제외** 탭에서 해당 응용 프로그램을 선택 하 고 **선택**을 선택할 수 있습니다.
   1. **완료** 를 선택합니다.
1. **액세스 제어** > **권한 부여**에서 **준수 상태로 표시 된 장치 필요**를 선택 합니다.
   1. **선택**을 선택합니다.
1. 설정을 확인 하 고 **정책 사용** 을 **켜기**로 설정 합니다.
1. 만들기 를 선택 하 여 정책을 사용 하도록 설정 합니다.

## <a name="next-steps"></a>다음 단계

[조건부 액세스 공통 정책](concept-conditional-access-policy-common.md)

[조건부 액세스 What If 도구를 사용 하 여 로그인 동작 시뮬레이션](troubleshoot-conditional-access-what-if.md)

[Azure AD에서 장치 준수 정책 사용](https://docs.microsoft.com/intune/device-compliance-get-started#device-compliance-policies-work-with-azure-ad)
