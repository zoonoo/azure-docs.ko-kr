---
title: 인증 방법 작업 - Azure Active Directory
description: 사용자가 로그인하고 암호를 재설정하기 위해 등록하는 인증 방법에 대한 개요입니다.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 03/16/2021
ms.author: justinha
author: sopand
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5a1cb71917fdb30ffccda21bedffe1c7f2a428c1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105557958"
---
# <a name="authentication-methods-activity"></a>인증 방법 작업 

새 인증 방법 작업 대시보드를 통해 관리자는 조직 전체에서 인증 방법 등록 및 사용 현황을 모니터링할 수 있습니다. 이 보고 기능은 사용자의 조직에 등록되는 방법 및 사용 방법을 이해할 수 있는 수단을 제공합니다.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="permissions-and-licenses"></a>권한 및 라이선스

다음 권한이 있는 기본 제공 및 사용자 지정 역할은 인증 방법 작업 블레이드 및 API에 액세스할 수 있습니다.

- Microsoft.directory/auditLogs/allProperties/read
- Microsoft.directory/signInReports/allProperties/read

다음 역할에는 필요한 권한이 있어야 합니다.

- 보고서 구독자
- 보안 Reader
- 전역 Reader
- 보안 운영자
- 보안 관리자
- 전역 관리자

 사용량 및 인사이트에 액세스하려면 Azure AD Premium P1 또는 P2 라이선스가 필요합니다. Azure AD Multi-Factor Authentication 및 SSPR(셀프 서비스 암호 재설정) 라이선스 정보는 [Azure Active Directory 가격 책정 사이트](https://azure.microsoft.com/pricing/details/active-directory/)에서 찾을 수 있습니다.

## <a name="how-it-works"></a>작동 방법

인증 방법 사용 및 인사이트에 액세스하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Azure Active Directory**  >  **보안**  >  **인증 방법**  >  **작업** 을 클릭합니다.
1. 보고서에는 **등록** 및 **사용** 이라는 두 개의 탭이 있습니다.

   ![인증 방법 작업 개요](media/how-to-authentication-methods-usage-insights/registration-usage-tabs.png)

## <a name="registration-details"></a>등록 세부 정보

[**등록 탭**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade)에 액세스하여 다단계 인증을 사용할 수 있는 사용자 수, 인증되지 않은 인증 및 셀프 서비스 암호 재설정을 표시할 수 있습니다. 

다음 옵션 중 하나를 클릭하여 사용자 등록 정보 목록을 사전 필터링합니다.

- **Azure Multi-Factor Authentication을 사용할 수 있는 사용자** 는 다음과 같은 사용자의 분석을 보여 줍니다.
  - 강력한 인증 방법에 등록됨 
  - 정책에서 MFA에 해당 메서드를 사용하도록 설정 
  
  이 번호는 Azure AD 외부에서 MFA에 등록된 사용자를 반영하지 않습니다. 
- **암호 없는 인증을 사용할 수 있는 사용자** 는 FIDO2, 비즈니스용 Windows Hello 또는 Microsoft Authenticator 앱을 사용하여 암호 없는 휴대폰 로그인을 통해 암호 없이 로그인하도록 등록된 사용자에 대한 분석을 보여 줍니다. 
- **셀프 서비스 암호 재설정을 사용할 수 있는 사용자** 는 자신의 암호를 재설정할 수 있는 사용자의 분석을 보여 줍니다. 다음 조건 충족 시 사용자는 암호를 재설정할 수 있습니다.
  - 조직의 셀프 서비스 암호 재설정 정책을 이행하기에 충분한 방법에 등록되어 있음 
  - 사용자가 암호를 재설정하도록 설정되어 있음 

  ![등록할 수 있는 사용자의 스크린샷](media/how-to-authentication-methods-usage-insights/users-capable.png)

**인증 방법으로 등록된 사용자** 는 각 인증 방법에 등록된 사용자 수를 보여 줍니다. 인증 방법을 클릭하여 해당 메서드에 등록된 사용자를 확인합니다.

![등록된 사용자의 스크린샷](media/how-to-authentication-methods-usage-insights/users-registered.png)

**인증 방법별 최근 등록** 에서는 인증 방법에 따라 정렬된 등록 수와 실패한 등록 수를 표시합니다. 인증 방법을 클릭하여 해당 방법에 대한 최근 등록 이벤트를 확인합니다.

![최근에 등록된 사항의 스크린샷](media/how-to-authentication-methods-usage-insights/recently-registered.png)

## <a name="usage-details"></a>사용량 세부 정보

**사용** 보고서에는 로그인하고 암호를 재설정하는 데 사용되는 인증 방법을 보여 줍니다.

![사용 페이지의 스크린샷](media/how-to-authentication-methods-usage-insights/usage-page.png)

**인증 요구 사항별 로그인** 은 Azure AD에서 단일 인증 대 다단계 인증 비교에 필요한 성공적인 사용자 대화형 로그인 수를 표시합니다. 타사 MFA 공급자가 MFA를 적용한 로그인은 포함되지 않습니다.

![인증 요구 사항별 로그인의 스크린샷](media/how-to-authentication-methods-usage-insights/sign-ins-protected.png)

**인증 방법별 로그인** 은 사용한 인증 방법에 따라 사용자 대화형 로그인(성공 및 실패)의 수를 표시합니다. 토큰의 클레임에 의해 인증 요구 사항이 충족된 로그인이 포함되지 않습니다.

![방법별 로그인 스크린샷](media/how-to-authentication-methods-usage-insights/sign-ins-by-method.png)

**암호 재설정 및 계정 잠금 해제 수** 는 시간이 지남에 따라 성공한 암호 변경 및 암호 재설정(셀프 서비스 및 관리자별) 수를 표시합니다.

![재설정 및 잠금 해제 스크린샷](media/how-to-authentication-methods-usage-insights/password-changes.png)

**인증 방법별 암호 재설정** 은 인증 방법에 따라 암호 재설정 흐름 중 성공 및 실패한 인증 수를 표시합니다.

![방법별 재설정 스크린샷](media/how-to-authentication-methods-usage-insights/resets-by-method.png)

## <a name="user-registration-details"></a>사용자 등록 세부 정보 

목록의 맨 위에 있는 컨트롤을 사용하여 사용자를 검색하고 표시된 열을 기준으로 사용자 목록을 필터링할 수 있습니다.

등록 세부 정보 보고서에는 각 사용자에 대해 다음 정보가 표시됩니다.

- 사용자 계정 이름
- 이름
- MFA 사용 가능(사용 가능, 사용 불가능)
- Passwordless 가능(사용 가능, 사용 불가능)
- SSPR 등록됨(등록됨, 등록되지 않음)
- SSPR 사용(사용, 사용 안 함)
- SSPR 사용 가능(사용 가능, 사용 불가능) 
- 등록된 방법(메일, 휴대폰, 대체 휴대폰, 사무실 전화, Microsoft Authenticator 푸시, 소프트웨어 일회용 암호, FIDO2, 보안 키, 보안 질문)

  ![사용자 등록 세부 정보 스크린샷](media/how-to-authentication-methods-usage-insights/registration-details.png)

## <a name="registration-and-reset-events"></a>등록 및 다시 설정 이벤트 

**등록 및 다시 설정 이벤트** 는 최근 24시간, 최근 7일 또는 최근 30일 동안의 등록 및 다시 설정 이벤트를 표시합니다.

- 날짜
- 사용자 이름
- 사용자 
- 기능(등록, 다시 설정)
- 사용되는 방법(앱 알림, 앱 코드, 전화 통화, Office 통화, 대체 모바일 통화, SMS, 메일, 보안 질문)
- 상태(성공, 실패)
- 실패 이유(설명)

  ![등록 및 다시 설정 이벤트의 스크린샷](media/how-to-authentication-methods-usage-insights/registration-and-reset-logs.png)

## <a name="limitations"></a>제한 사항

- 보고서의 데이터는 실시간으로 업데이트되지 않으며 최대 몇 시간의 대기 시간을 반영할 수 있습니다.
- 임시 액세스 패스 등록은 짧은 기간 동안만 유효하기 때문에 보고서의 등록 탭에 반영되지 않습니다.
- 사용자가 구성한 **PhoneAppNotification** 또는 **PhoneAppOTP** 메서드는 대시보드에 표시되지 않습니다. 

## <a name="next-steps"></a>다음 단계

- [인증 방법 사용 보고서 API로 작업](/graph/api/resources/authenticationmethods-usage-insights-overview?view=graph-rest-beta)
- [조직에 대한 인증 방법 선택](concept-authentication-methods.md)
- [결합된 등록 환경](concept-registration-mfa-sspr-combined.md)
