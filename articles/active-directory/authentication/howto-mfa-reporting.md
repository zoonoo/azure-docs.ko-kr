---
title: Azure MFA에 대 한 액세스 및 사용 보고서-Azure Active Directory
description: Azure Multi-Factor Authentication 기능 - 보고서를 사용하는 방법을 설명합니다.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25cd36b6b3e8a6974618189985152e55c2676999
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69874294"
---
# <a name="reports-in-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication에서 보고서

Azure Multi-Factor Authentication은 사용자 및 사용자의 조직에서 Azure Portal을 통해 액세스하고 사용할 수 있는 다양한 보고서를 제공합니다. 다음 표에는 사용 가능한 보고서가 나와 있습니다.

| 보고서 | 위치 | Description |
|:--- |:--- |:--- |
| 차단된 사용자 기록 | Azure AD > MFA 서버 > 사용자 차단/차단 해제 | 사용자를 차단 또는 차단 해제하도록 요청한 기록이 표시됩니다. |
| 사용량 및 사기 행위 경고 | Azure AD > 로그인 | 지정된 날짜 범위 동안 제출된 사기 행위 경고의 기록을 비롯한 전체 사용량, 사용자 요약 및 사용자 세부 정보에 대한 정보를 제공합니다. |
| 온-프레미스 구성 요소의 사용량 | Azure AD > MFA 서버 > 작업 보고서 | NPS 확장, ADFS 및 MFA 서버를 통해 MFA의 전체 사용량에 대한 정보를 제공합니다. |
| 바이패스된 사용자 기록 | Azure AD > MFA 서버 > 일회성 바이패스 | 사용자의 Multi-Factor Authentication을 바이패스하는 요청 기록을 제공합니다. |
| 서버 상태 | Azure AD > MFA 서버 > 서버 상태 | 계정에 연결된 Multi-Factor Authentication 서버의 상태가 표시됩니다. |

## <a name="view-mfa-reports"></a>MFA 보고서 보기

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 왼쪽에서 **Azure Active Directory** > **MFA 서버**를 선택합니다.
3. 보려는 보고서를 선택합니다.

   ![Azure Portal의 MFA 서버 서버 상태 보고서](./media/howto-mfa-reporting/report.png)

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

![Azure Portal의 Azure AD 로그인 보고서](./media/howto-mfa-reporting/sign-in-report.png)

### <a name="sign-ins-report-structure"></a>로그인 보고서 구조

MFA의 로그인 활동 보고서를 통해 다음 정보에 액세스 할 수 있습니다.

**MFA 필요:** 로그인에 MFA가 필요한지 여부입니다. MFA는 사용자별 MFA, 조건부 액세스 또는 기타 이유로 인해 필요할 수 있습니다. 가능한 값은 **예** 또는 **아니요**입니다.

**MFA 결과:** MFA 충족 여부에 대한 추가 정보입니다.

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

**MFA 인증 방법:** 사용자가 MFA를 완료하는 데 사용한 인증 방법입니다. 가능한 값은 다음과 같습니다.

- 문자 메시지
- 모바일 앱 알림
- 전화 통화(인증 전화)
- 모바일 앱 확인 코드
- 전화 통화(사무실 전화)
- 전화 통화(대체 인증 전화)

**MFA 인증 세부 정보:** 전화 번호의 삭제된 버전(예: +X XXXXXXXX64)입니다.

**조건부 액세스** 다음을 포함 하 여 로그인 시도에 영향을 준 조건부 액세스 정책에 대 한 정보를 찾습니다.

- 정책 이름
- 컨트롤 권한 부여
- 세션 컨트롤
- 결과

## <a name="powershell-reporting-on-users-registered-for-mfa"></a>MFA에 등록 된 사용자에 대 한 PowerShell 보고

먼저 [Msonline V1 PowerShell 모듈이](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-1.0) 설치 되어 있는지 확인 합니다.

다음에 나오는 PowerShell을 사용하여 MFA에 등록한 사용자를 식별합니다.

```Get-MsolUser -All | Where-Object {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName```

다음에 나오는 PowerShell을 사용하여 MFA에 등록하지 않은 사용자를 식별합니다.

```Get-MsolUser -All | Where-Object {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName```

## <a name="possible-results-in-activity-reports"></a>활동 보고서에서 가능한 결과

다음 표는 다운로드 된 버전의 multi-factor authentication 활동 보고서를 사용 하 여 다단계 인증 문제를 해결 하는 데 사용할 수 있습니다. Azure Portal에 직접 표시 되지 않습니다.

| 통화 결과 | Description | 광범위 한 설명 |
| --- | --- | --- |
| SUCCESS_WITH_PIN | PIN 입력됨 | 사용자가 PIN을 입력 했습니다.  인증에 성공 하면 올바른 PIN을 입력 한 것입니다.  인증이 거부 된 경우 잘못 된 PIN을 입력 하거나 사용자가 표준 모드로 설정 됩니다. |
| SUCCESS_NO_PIN | #만 입력 됨 | 사용자가 PIN 모드로 설정 되어 있고 인증이 거부 된 경우 사용자가 PIN을 입력 하지 않고 #만 입력 했음을 의미 합니다.  사용자를 표준 모드로 설정 하 고 인증에 성공 하면 사용자가 표준 모드에서 수행 해야 하는 올바른 것으로 #을 입력 한 것입니다. |
| SUCCESS_WITH_PIN_BUT_TIMEOUT | # 입력 후 누르지 않음 | #을 입력 하지 않았기 때문에 사용자가 DTMF 번호를 전송 하지 않았습니다.  입력 된 다른 숫자는 입력 완료를 나타내는 #을 입력 하지 않으면 전송 되지 않습니다. |
|SUCCESS_NO_PIN_BUT_TIMEOUT | 폰 입력 없음-시간 초과 됨 | 호출에 응답 했지만 응답이 없습니다.  이는 일반적으로 전화가 음성 메일로 선택 되었음을 나타냅니다. |
| SUCCESS_PIN_EXPIRED | PIN이 만료된 후 변경 안 됨 | 사용자의 PIN이 만료 되어 사용자의 PIN을 변경 하 라는 메시지가 표시 되었지만 PIN 변경이 성공적으로 완료 되지 않았습니다. |
| SUCCESS_USED_CACHE | 사용된 캐시 | 구성 된 캐시 기간 내에 동일한 사용자 이름에 대해 이전에 성공한 인증이 발생 했으므로 Multi-factor Authentication 통화 없이 인증에 성공 했습니다. |
| SUCCESS_BYPASSED_AUTH | 바이패스된 인증 | 사용자에 대해 일회성 바이패스를 시작 하 여 인증에 성공 했습니다.  바이패스에 대 한 자세한 내용은 바이패스 된 사용자 기록 보고서를 참조 하세요. |
| SUCCESS_USED_IP_BASED_CACHE | 사용 되는 IP 기반 캐시 | 구성 된 캐시 기간 내에 동일한 사용자 이름, 인증 유형, 응용 프로그램 이름 및 IP에 대해 이전에 성공한 인증을 사용 하 여 Multi-factor Authentication 통화 없이 인증에 성공 했습니다. |
| SUCCESS_USED_APP_BASED_CACHE | 사용 되는 앱 기반 캐시 | 구성 된 캐시 기간 내에 동일한 사용자 이름, 인증 유형 및 응용 프로그램 이름에 대해 이전에 성공한 인증 이후 Multi-factor Authentication 통화 없이 인증에 성공 했습니다. |
| SUCCESS_INVALID_INPUT | 잘못된 폰 입력 | 휴대폰에서 보낸 응답이 잘못 되었습니다.  팩스 시스템이 나 모뎀이 나 사용자가 PIN의 일부로 *를 입력 했을 수 있습니다. |
| SUCCESS_USER_BLOCKED | 사용자가 차단됨 | 사용자의 전화 번호가 차단 됩니다.  차단 된 번호는 인증 통화 중에 사용자가 시작 하거나 관리자가 Azure Portal를 사용 하 여 시작할 수 있습니다. <br> 참고:   차단 된 번호는 사기 행위 경고의 과정 이기도 합니다. |
| SUCCESS_SMS_AUTHENTICATED | 문자 메시지 인증됨 | 양방향 테스트 메시지의 경우 사용자는 OTP (일회성 암호) 또는 OTP + PIN을 사용 하 여 올바르게 회신 합니다. |
| SUCCESS_SMS_SENT | 문자 메시지 전송됨 | 문자 메시지의 경우 OTP (일회성 암호)를 포함 하는 문자 메시지를 성공적으로 보냈습니다.  사용자는 응용 프로그램에 OTP 또는 OTP + PIN을 입력 하 여 인증을 완료 합니다. |
| SUCCESS_PHONE_APP_AUTHENTICATED | 모바일 앱 인증됨 | 사용자가 모바일 앱을 통해 성공적으로 인증 되었습니다. |
| SUCCESS_OATH_CODE_PENDING | OATH 코드가 보류됨 | 사용자에 게 OATH 코드를 입력 하 라는 메시지가 표시 되었지만 응답 하지 않았습니다. |
| SUCCESS_OATH_CODE_VERIFIED | OATH 코드가 확인됨 | 메시지가 표시 되 면 사용자가 올바른 OATH 코드를 입력 했습니다. |
| SUCCESS_FALLBACK_OATH_CODE_VERIFIED | 대체 OATH 코드 확인됨 | 사용자가 기본 Multi-factor Authentication 방법을 사용 하 여 인증을 거부 한 후 대체에 유효한 OATH 코드를 제공 했습니다. |
| SUCCESS_FALLBACK_SECURITY_QUESTIONS_ANSWERED | 대체 보안 질문이 답변됨 | 사용자가 기본 Multi-factor Authentication 방법을 사용 하 여 인증을 거부 한 후 대체에 대해 보안 질문에 대 한 답변을 올바르게 받았습니다. |
| FAILED_PHONE_BUSY | 인증이 이미 진행 중 | Multi-factor Authentication에서 이미이 사용자에 대 한 인증을 처리 하 고 있습니다.  이는 일반적으로 동일한 로그온 중에 여러 인증 요청을 보내는 RADIUS 클라이언트에 의해 발생 합니다. |
| CONFIG_ISSUE | 폰에 연결할 수 없음 | 호출을 시도 했지만, 찾을 수 없거나 응답 하지 않았습니다.  여기에는 사용 중인 신호, 고속 사용 신호 (연결 끊김), 3 톤 (서비스에서 더 이상 사용 되지 않음), 벨 울림 등의 시간이 포함 됩니다. |
| FAILED_INVALID_PHONENUMBER | 잘못된 전화 번호 형식 | 전화 번호 형식이 잘못 되었습니다.  전화 번호는 숫자 여야 하며 국가 코드 + 1 (미국 & 캐나다)의 경우 10 자리 여야 합니다. |
| FAILED_USER_HUNGUP_ON_US | 사용자가 전화 끊음 | 사용자가 전화에 응답 했지만 단추를 누르지 않고 중단 되었습니다. |
| FAILED_INVALID_EXTENSION | 잘못된 내선 번호 | 확장에 잘못 된 문자가 포함 되어 있습니다.  숫자, 쉼표, * 및 #만 사용할 수 있습니다.  @ 접두사를 사용할 수도 있습니다. |
| FAILED_FRAUD_CODE_ENTERED | 사기 코드가 입력됨 | 사용자가 통화 중에 사기 행위를 보고 하 여 인증 및 차단 된 전화 번호를 거부 했습니다.| 
| FAILED_SERVER_ERROR | 전화 걸 수 없음 | Multi-factor Authentication 서비스에서 호출을 수행할 수 없습니다. |
| FAILED_SMS_NOT_SENT | 문자 메시지를 보낼 수 없습니다. | 문자 메시지를 보낼 수 없습니다.  인증이 거부 되었습니다. |
| FAILED_SMS_OTP_INCORRECT | 문자 메시지의 OTP가 잘못됨 | 사용자가 받은 문자 메시지에서 잘못 된 OTP (일회성 암호)를 입력 했습니다.  인증이 거부 되었습니다. |
| FAILED_SMS_OTP_PIN_INCORRECT | 문자 메시지 OTP + PIN이 잘못 됨 | 사용자가 잘못 된 OTP (일회용 암호) 및/또는 잘못 된 사용자 PIN을 입력 했습니다.  인증이 거부 되었습니다. |
| FAILED_SMS_MAX_OTP_RETRY_REACHED | 최대 문자 메시지 OTP 시도 횟수 초과 | 사용자가 최대 OTP (일회용 암호) 시도 횟수를 초과 했습니다. |
| FAILED_PHONE_APP_DENIED | 모바일 앱 거부됨 | 사용자가 거부 단추를 눌러 모바일 앱에서 인증을 거부 했습니다. |
| FAILED_PHONE_APP_INVALID_PIN | 모바일 앱의 잘못된 PIN | 사용자가 모바일 앱에서 인증할 때 잘못 된 PIN을 입력 했습니다. |
| FAILED_PHONE_APP_PIN_NOT_CHANGED | 모바일 앱 PIN이 변경 안 됨 | 사용자가 모바일 앱에서 필요한 PIN 변경을 성공적으로 완료 하지 못했습니다. |
| FAILED_FRAUD_REPORTED | 사기 행위가 보고됨 | 사용자가 모바일 앱에서 사기 행위를 보고 했습니다. |
| FAILED_PHONE_APP_NO_RESPONSE | 모바일 앱 응답 없음 | 사용자가 모바일 앱 인증 요청에 응답 하지 않았습니다. |
| FAILED_PHONE_APP_ALL_DEVICES_BLOCKED | 모바일 앱의 모든 장치가 차단됨 | 이 사용자에 대 한 모바일 앱 장치가 더 이상 알림에 응답 하지 않으며 차단 되었습니다. |
| FAILED_PHONE_APP_NOTIFICATION_FAILED | 모바일 앱 알림 실패 | 사용자 장치의 모바일 앱에 알림을 보내는 동안 오류가 발생 했습니다. |
| FAILED_PHONE_APP_INVALID_RESULT | 모바일 앱의 잘못된 결과 | 모바일 앱에서 잘못 된 결과를 반환 했습니다. |
| FAILED_OATH_CODE_INCORRECT | OATH 코드가 잘못됨 | 사용자가 잘못 된 OATH 코드를 입력 했습니다.  인증이 거부 되었습니다. |
| FAILED_OATH_CODE_PIN_INCORRECT | OATH 코드 + PIN이 잘못 됨 | 사용자가 잘못 된 OATH 코드 및/또는 잘못 된 사용자 PIN을 입력 했습니다.  인증이 거부 되었습니다. |
| FAILED_OATH_CODE_DUPLICATE | 중복된 OATH 코드 | 사용자가 이전에 사용 된 OATH 코드를 입력 했습니다.  인증이 거부 되었습니다. |
| FAILED_OATH_CODE_OLD | OATH 코드가 만료됨 | 사용자가 이전에 사용 된 OATH 코드 앞에 나오는 OATH 코드를 입력 했습니다.  인증이 거부 되었습니다. |
| FAILED_OATH_TOKEN_TIMEOUT | OATH 코드 결과 시간 제한 | 사용자가 OATH 코드를 입력 하는 데 너무 오래 걸려서 Multi-factor Authentication 시도 시간이 이미 초과 되었습니다. |
| FAILED_SECURITY_QUESTIONS_TIMEOUT | 보안 질문 결과 시간 제한 | 사용자에 게 보안 질문에 대 한 답변을 입력 하는 데 너무 오랜 시간이 걸려 Multi-factor Authentication 시도 시간이 이미 초과 되었습니다. |
| FAILED_AUTH_RESULT_TIMEOUT | 인증 결과 시간 제한 | 사용자가 Multi-factor Authentication 시도를 완료 하는 데 너무 오래 걸렸습니다. |
| FAILED_AUTHENTICATION_THROTTLED | 인증 제한 됨 | Multi-factor Authentication 시도가 서비스에 의해 제한 되었습니다. |

## <a name="next-steps"></a>다음 단계

* [SSPR 및 MFA 사용량 및 통찰력 보고](howto-authentication-methods-usage-insights.md)
* [사용자](../user-help/multi-factor-authentication-end-user.md)
* [배포할 위치](concept-mfa-whichversion.md)
