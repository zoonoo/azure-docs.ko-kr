---
title: Azure MFA에 대한 액세스 및 사용 보고서 | Microsoft Docs
description: Azure Multi-Factor Authentication 기능 - 보고서를 사용하는 방법을 설명합니다.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: michmcla
ms.openlocfilehash: dc4cd28fe61c422f65f47c74c7cbc4686d73ab77
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/08/2018
ms.locfileid: "39628837"
---
# <a name="reports-in-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication에서 보고서

Azure Multi-Factor Authentication은 사용자 및 사용자의 조직에서 Azure Portal을 통해 액세스하고 사용할 수 있는 다양한 보고서를 제공합니다. 다음 표에는 사용 가능한 보고서가 나와 있습니다.

| 보고서 | 위치 | 설명 |
|:--- |:--- |:--- |
| 차단된 사용자 기록 | Azure AD > MFA 서버 > 사용자 차단/차단 해제 | 사용자를 차단 또는 차단 해제하도록 요청한 기록이 표시됩니다. |
| 사용량 및 사기 행위 경고 | Azure AD > 로그인 | 지정된 날짜 범위 동안 제출된 사기 행위 경고의 기록을 비롯한 전체 사용량, 사용자 요약 및 사용자 세부 정보에 대한 정보를 제공합니다. |
| 온-프레미스 구성 요소의 사용량 | Azure AD > MFA 서버 > 작업 보고서 | NPS 확장, ADFS 및 MFA 서버를 통해 MFA의 전체 사용량에 대한 정보를 제공합니다. |
| 무시된 사용자 기록 | Azure AD > MFA 서버 > 일회성 바이패스 | 사용자의 Multi-Factor Authentication을 바이패스하는 요청 기록을 제공합니다. |
| 서버 상태 | Azure AD > MFA 서버 > 서버 상태 | 계정에 연결된 Multi-Factor Authentication 서버의 상태가 표시됩니다. |

## <a name="view-mfa-reports"></a>MFA 보고서 보기

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 왼쪽에서 **Azure Active Directory** > **MFA 서버**를 선택합니다.
3. 보려는 보고서를 선택합니다.

   <center>![클라우드](./media/howto-mfa-reporting/report.png)</center>

## <a name="azure-ad-sign-ins-report"></a>Azure AD 로그인 보고서

[Azure Portal](https://portal.azure.com)에서 **로그인 작업 보고서**를 통해 사용자 환경의 상태를 파악하는 데 필요한 모든 정보를 얻을 수 있습니다.

로그인 보고서는 MFA(Multi-Factor Authentication) 사용에 대한 정보가 포함된 사용자 로그인 활동 및 관리되는 애플리케이션에 대한 정보를 제공할 수 있습니다. MFA 데이터는 MFA가 조직에서 어떻게 작동하는지에 대한 정보를 제공합니다. 이를 통해 다음과 같은 질문에 답할 수 있습니다.

- 로그인에 MFA가 요구되었나요?
- 사용자가 MFA를 어떻게 완료했나요?
- 사용자가 MFA를 완료할 수 없었던 이유는 무엇인가요?
- MFA에 어려움을 겪은 사용자가 얼마나 되나요?
- MFA 챌린지를 완료할 수 없었던 사용자가 얼마나 되나요?
- 최종 사용자가 겪은 일반적인 MFA 문제는 무엇인가요?

이 데이터는 [Azure Portal](https://portal.azure.com) 및 [보고서 API](../reports-monitoring/concept-reporting-api.md)를 통해 제공됩니다.

![클라우드](./media/howto-mfa-reporting/sign-in-report.png)

### <a name="sign-ins-report-structure"></a>로그인 보고서 구조

MFA의 로그인 활동 보고서를 통해 다음 정보에 액세스 할 수 있습니다.

**MFA 필요:** 로그인에 MFA가 필요한지 여부. MFA는 사용자별 MFA, 조건부 액세스 또는 기타 이유로 필요할 수 있습니다. 가능한 값은 **예** 또는 **아니요**입니다.

**MFA 결과:** MFA 충족 여부에 대한 추가 정보.

- MFA가 충족되면 MFA가 어떻게 충족되었는지에 대한 자세한 정보가 이 열에 제공합니다.
   - Azure Multi-Factor Authentication
      - 클라우드에서 완료
      - 테넌트에 구성된 정책으로 인해 만료
      - 등록 메시지가 표시됨
      - 토큰 클레임으로 충족
      - 외부 공급자가 제공한 클레임으로 충족
      - 강력한 인증으로 충족
      - 실행된 흐름이 Windows 브로커 로그온 흐름이어서 건너뜀
      - 앱 암호로 인해 건너뜀
      - 위치로 인해 건너뜀
      - 등록된 디바이스로 인해 건너뜀
      - 기억된 디바이스로 인해 건너뜀
      - 성공적으로 완료됨
   - 다단계 인증을 위해 외부 공급자로 리디렉션됨

- MFA가 거부된 경우 거부 이유가 이 열에 제공됩니다.
   - Azure Multi-Factor Authentication 거부됨;
      - 인증 진행 중
      - 중복된 인증 시도
      - 잘못된 코드를 너무 많이 입력함
      - 잘못된 인증
      - 잘못된 모바일 앱 확인 코드
      - 잘못된 구성
      - 전화 통화가 음성 사서함으로 연결됨
      - 전화 번호 형식이 잘못됨
      - 서비스 오류
      - 사용자의 전화에 도달할 수 없음
      - 모바일 앱 알림을 디바이스에 보낼 수 없음
      - 모바일 앱 알림을 보낼 수 없음
      - 사용자가 인증을 거부했음
      - 사용자가 모바일 앱 알림에 응답하지 않음
      - 사용자에게 등록된 인증 방법이 없음
      - 사용자가 잘못된 코드를 입력했음
      - 사용자가 잘못된 PIN을 입력했음
      - 인증을 성공하지 않은 상태에서 사용자가 전화를 끊음
      - 사용자가 차단됨
      - 사용자가 확인 코드를 입력하지 않음
      - 사용자를 찾을 수 없음
      - 확인 코드가 이미 한 번 사용됨

**MFA 인증 방법:** 사용자가 MFA를 완료하는 데 사용한 인증 방법. 가능한 값은 다음과 같습니다.

- 문자 메시지
- 모바일 앱 알림
- 전화 통화(인증 전화)
- 모바일 앱 확인 코드
- 전화 통화(사무실 전화)
- 전화 통화(대체 인증 전화)

**MFA 인증 세부 정보:** 전화 번호의 삭제된 버전(예: +X XXXXXXXX64).

**조건부 액세스** 다음을 포함하여 로그인 시도에 영향을 미친 조건부 액세스 정책에 대한 정보를 찾아보세요.

- 정책 이름
- 권한 부여 컨트롤
- 세션 컨트롤
- 결과

## <a name="powershell-reporting"></a>PowerShell 보고

다음에 나오는 PowerShell을 사용하여 MFA에 등록한 사용자를 식별합니다.

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName```

다음에 나오는 PowerShell을 사용하여 MFA에 등록하지 않은 사용자를 식별합니다.

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName```

## <a name="next-steps"></a>다음 단계

* [사용자](../user-help/multi-factor-authentication-end-user.md)
* [배포할 위치](concept-mfa-whichversion.md)
