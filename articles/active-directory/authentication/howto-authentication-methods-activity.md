---
title: 인증 방법 작업-Azure Active Directory
description: 사용자가 로그인 하 고 암호를 재설정 하기 위해 등록 하는 인증 방법에 대 한 개요입니다.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 02/25/2021
ms.author: justinha
author: sopand
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: fbe69967d84777091aec0bbbf1626b98f5018d0e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101693182"
---
# <a name="authentication-methods-activity"></a>인증 방법 작업 

새 인증 방법 작업 대시보드를 통해 관리자는 조직 전체에서 인증 방법 등록 및 사용 현황을 모니터링할 수 있습니다. 이 보고 기능은 사용자의 조직에 등록 되는 방법 및 사용 방법을 이해할 수 있는 수단을 제공 합니다.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="permissions-and-licenses"></a>권한 및 라이선스

다음 역할은 사용 및 정보에 액세스할 수 있습니다.

- 보고서 구독자
- 보안 Reader
- 보안 관리자
- 전역 관리자

 사용 및 정보에 액세스 하려면 Azure AD Premium P1 또는 P2 라이선스가 필요 합니다. Azure AD Multi-Factor Authentication 및 SSPR (셀프 서비스 암호 재설정) 라이선스 정보는 [Azure Active Directory 가격 책정 사이트](https://azure.microsoft.com/pricing/details/active-directory/)에서 찾을 수 있습니다.

## <a name="how-it-works"></a>작동 방식

인증 방법 사용 및 정보에 액세스 하려면:

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Azure Active Directory**  >  **보안**  >  **인증 방법**  >  **작업** 을 클릭 합니다.
1. 보고서에는 **등록** 및 **사용** 이라는 두 개의 탭이 있습니다.

   ![인증 방법 작업 개요](media/how-to-authentication-methods-usage-insights/registration-usage-tabs.png)

## <a name="registration-details"></a>등록 세부 정보

[**등록 탭**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade) 에 액세스 하 여 다단계 인증을 수행할 수 있는 사용자 수, 인증 되지 않은 인증 및 셀프 서비스 암호 재설정을 표시할 수 있습니다. 

다음 옵션 중 하나를 클릭 하 여 사용자 등록 정보 목록을 미리 필터링 합니다.

- **Azure Multi-Factor Authentication 사용할 수 있는 사용자** 는 다음과 같은 사용자의 분석을 보여 줍니다.
  - 강력한 인증 방법에 대해 등록 됨 
  - 정책에서 MFA에 해당 메서드를 사용 하도록 설정 
  
  이 번호는 Azure AD 외부에서 MFA에 등록 된 사용자를 반영 하지 않습니다. 
- 암호 없는 **인증을** 사용 하는 사용자는 FIDO2, 비즈니스용 Windows Hello 또는 Microsoft Authenticator 앱을 사용 하 여 암호 없는 휴대폰 로그인을 사용 하 여 암호 없이 로그인 하도록 등록 된 사용자에 대 한 분석을 보여 줍니다. 
- **셀프 서비스 암호 재설정을 수행할** 수 있는 사용자는 자신의 암호를 재설정할 수 있는 사용자의 분석을 보여 줍니다. 사용자는 암호를 다시 설정할 수 있습니다.
  - 조직의 셀프 서비스 암호 재설정에 대 한 조직의 정책을 충족 하기에 충분 한 방법으로 등록 
  - 암호를 재설정 하도록 설정 

  ![등록할 수 있는 사용자의 스크린샷](media/how-to-authentication-methods-usage-insights/users-capable.png)

**인증 방법으로 등록 된 사용자** 는 각 인증 방법에 대해 등록 된 사용자 수를 보여 줍니다. 인증 방법을 클릭 하 여 해당 메서드에 대해 등록 된 사용자를 확인 합니다.

![등록 된 사용자의 스크린샷](media/how-to-authentication-methods-usage-insights/users-registered.png)

**인증 방법에의 한 최근 등록 방법** 에서는 인증 방법에 따라 정렬 된 등록 수와 실패 한 등록 수를 표시 합니다. 인증 방법을 클릭 하 여 해당 메서드에 대 한 최근 등록 이벤트를 확인 합니다.

![최근에 등록 된 스크린샷](media/how-to-authentication-methods-usage-insights/recently-registered.png)

## <a name="usage-details"></a>사용량 세부 정보

**사용** 보고서에는 로그인 하 고 암호를 재설정 하는 데 사용 되는 인증 방법을 보여 줍니다.

![사용량 페이지의 스크린샷](media/how-to-authentication-methods-usage-insights/usage-page.png)

**인증 요구 사항** 에의 한 로그인은 Azure AD에서 multi-factor authentication 및 multi-factor authentication에 필요한 성공적인 사용자 대화형 로그인 수를 표시 합니다. 타사 MFA 공급자가 MFA를 적용 한 로그인은 포함 되지 않습니다.

![인증 요구 사항에의 한 로그인의 스크린샷](media/how-to-authentication-methods-usage-insights/sign-ins-protected.png)

**인증 방법으로 로그인** 은 사용한 인증 방법에 따라 사용자 대화형 로그인 (성공 및 실패)의 수를 표시 합니다. 토큰의 클레임에 의해 인증 요구 사항이 충족 된 로그인이 포함 되지 않습니다.

![메서드의 로그인 스크린샷](media/how-to-authentication-methods-usage-insights/sign-ins-by-method.png)

**암호 다시 설정 수 및 계정 잠금 해제** 는 시간이 지남에 따라 성공한 암호 변경 및 암호 재설정 (셀프 서비스 및 관리자) 수를 표시 합니다.

![다시 설정 및 잠금 해제 스크린샷](media/how-to-authentication-methods-usage-insights/password-changes.png)

**인증** 방법으로 암호 재설정은 인증 방법에의 한 암호 재설정 흐름 중 성공 및 실패 한 인증 수를 표시 합니다.

![방법으로 다시 설정의 스크린샷](media/how-to-authentication-methods-usage-insights/resets-by-method.png)

## <a name="user-registration-details"></a>사용자 등록 정보 

목록의 맨 위에 있는 컨트롤을 사용 하 여 사용자를 검색 하 고 표시 된 열을 기준으로 사용자 목록을 필터링 할 수 있습니다.

등록 세부 정보 보고서에는 각 사용자에 대 한 다음 정보가 표시 됩니다.

- 사용자 계정 이름
- 속성
- MFA 지원 가능 (지원 안 함)
- Passwordless 가능 (지원 안 함)
- SSPR 등록 (등록 됨, 등록 되지 않음)
- SSPR 사용 (사용, 사용 안 함)
- SSPR 지원 (지원 가능 안 함) 
- 등록 된 메서드 (메일, 휴대폰, 대체 휴대폰, 사무실 전화, Microsoft Authenticator 푸시, 소프트웨어 1 회 암호, FIDO2, 보안 키, 보안 질문)

  ![사용자 등록 정보 스크린샷](media/how-to-authentication-methods-usage-insights/registration-details.png)

## <a name="registration-and-reset-events"></a>등록 및 다시 설정 이벤트 

**등록 및 다시 설정 이벤트** 는 최근 24 시간, 최근 7 일 또는 최근 30 일 동안의 등록 및 다시 설정 이벤트를 표시 합니다.

- Date
- 사용자 이름
- 사용자 
- 기능 (등록, 다시 설정)
- 사용 되는 방법 (앱 알림, 앱 코드, 전화 통화, Office 통화, 대체 모바일 통화, SMS, 전자 메일, 보안 질문)
- 상태 (성공, 실패)
- 실패 이유 (설명)

  ![사용량 페이지의 스크린샷](media/how-to-authentication-methods-usage-insights/registration-and-reset-logs.png)

## <a name="limitations"></a>제한 사항

임시 액세스 통과 (탭) 등록은 짧은 기간 동안만 유효 하기 때문에 보고서의 등록 탭에 반영 되지 않습니다.

## <a name="next-steps"></a>다음 단계

- [인증 방법 사용 보고서 API 사용](/graph/api/resources/authenticationmethods-usage-insights-overview?view=graph-rest-beta)
- [조직에 대 한 인증 방법 선택](concept-authentication-methods.md)
- [결합 된 등록 환경](concept-registration-mfa-sspr-combined.md)
