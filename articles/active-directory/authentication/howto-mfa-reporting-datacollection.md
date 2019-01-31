---
title: Azure Multi-Factor Authentication 사용자 데이터 컬렉션
description: Azure Multi-Factor Authentication에서 사용자를 인증하는 데 사용되는 정보
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.openlocfilehash: b96e1d6cfd476d90afaecd44eaa9861de34130ce
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/26/2019
ms.locfileid: "55081588"
---
# <a name="azure-multi-factor-authentication-user-data-collection"></a>Azure Multi-Factor Authentication 사용자 데이터 컬렉션

이 문서에서는 Azure MFA 서버(Azure Multi-Factor Authentication 서버) 및 Azure MFA(클라우드 기반)에서 수집된 사용자 정보를 찾아 제거하는 방법에 대해 설명합니다.

[!INCLUDE [gdpr-hybrid-note](../../../includes/gdpr-hybrid-note.md)]

## <a name="information-collected"></a>수집된 정보

MFA 서버, NPS 확장 및 Windows Server 2016 Azure MFA AD FS 어댑터는 다음 정보를 수집하고 90일 동안 저장합니다.

인증 시도(보고 및 문제 해결에 사용):

- 타임 스탬프
- 사용자 이름
- 이름
- 성
- 메일 주소
- 사용자 그룹
- 인증 방법(전화 통화, 문자 메시지, 모바일 앱, OATH 토큰)
- 전화 통화 모드(표준, PIN)
- 문자 메시지 방향(단방향, 양방향)
- 문자 메시지 모드(OTP, OTP + PIN)
- 모바일 앱 모드(표준, PIN)
- OATH 토큰 모드(표준, PIN)
- 인증 유형
- 애플리케이션 이름
- 기본 호출 국가 코드
- 기본 호출 전화 번호
- 기본 호출 내선 번호
- 인증된 기본 호출
- 기본 호출 결과
- 예비 호출 국가 코드
- 예비 호출 전화 번호
- 예비 호출 내선 번호
- 인증된 예비 호출
- 예비 호출 결과
- 인증된 전체
- 전체 결과
- 결과
- 인증됨
- 결과
- IP 주소 시작 중
- 디바이스
- 디바이스 토큰
- 디바이스 유형
- 모바일 앱 버전
- OS 버전
- 결과
- 사용된 알림 확인

활성화(Microsoft Authenticator 모바일 앱에서 계정을 활성화하기 위한 시도):
- 사용자 이름
- 계정 이름
- 타임 스탬프
- 활성화 코드 결과 가져오기
- 활성화 성공
- 활성화 오류
- 활성화 상태 결과
- 디바이스 이름
- 디바이스 유형
- 앱 버전
- OATH 토큰 사용

차단(차단된 상태 확인 및 보고에 사용):

- 차단 타임스탬프
- 사용자 이름으로 차단
- 사용자 이름
- 국가 코드
- 전화 번호
- 서식이 지정된 전화 번호
- 내선 번호
- 새 내선 번호
- Blocked
- 차단 이유
- 완료 타임스탬프
- 완료 이유
- 계정 잠금
- 사기 행위 경고
- 차단되지 않은 사기 행위 경고
- 언어

바이패스(보고에 사용):

- 바이패스 타임스탬프
- 바이패스 시간
- 사용자 이름으로 바이패스
- 사용자 이름
- 국가 코드
- 전화 번호
- 서식이 지정된 전화 번호
- 내선 번호
- 새 내선 번호
- 바이패스 이유
- 완료 타임스탬프
- 완료 이유
- 사용된 바이패스

변경(MFA 서버 또는 AAD로 사용자 변경 내용을 동기화하는 데 사용):

- 변경 타임스탬프
- 사용자 이름
- 새 국가 코드
- 새 전화 번호
- 새 내선 번호
- 새 예비 국가 코드
- 새 예비 전화 번호
- 새 예비 내선 번호
- 새 PIN
- PIN 변경 필요
- 이전 디바이스 토큰
- 새 디바이스 토큰

## <a name="gather-data-from-mfa-server"></a>MFA 서버에서 데이터 수집

MFA 서버 8.0 이상 버전의 경우 다음 프로세스를 통해 관리자가 사용자에 대한 모든 데이터를 내보낼 수 있습니다.

- MFA 서버에 로그인하고, **사용자** 탭으로 이동하여, 해당 사용자를 선택하고, **편집** 단추를 클릭합니다. 각 탭의 스크린샷을 만들고(Alt-PrtScn) 현재 해당 MFA 설정 사용자를 제공합니다.
- MFA 서버 명령줄에서 해당 설치에 따라 경로를 변경하여 `C:\Program Files\Multi-Factor Authentication Server\MultiFactorAuthGdpr.exe export <username>` 명령을 실행하여 JSON 형식 파일을 생성합니다.
- 또한 관리자는 웹 서비스 SDK GetUserGdpr 작업을 옵션으로 사용하여 지정된 사용자에 대해 수집된 모든 MFA 클라우드 서비스 정보를 내보내거나 더 큰 보고 솔루션에 통합할 수 있습니다.
- `C:\Program Files\Multi-Factor Authentication Server\Logs\MultiFactorAuthSvc.log` 및 "<username>"에 대한 모든 백업을 검색(검색에 따옴표 포함)하여 추가되거나 변경된 사용자 레코드의 모든 인스턴스를 찾습니다.
   - 이러한 레코드는 MFA 서버 UX, 로깅 섹션, 로그 파일 탭에서 **"사용자 변경 내용 로깅"** 을 선택 취소하여 제한할 수 있습니다(제거할 수는 없음).
   - 대신 Syslog가 구성되고 MFA 서버 UX, 로깅 섹션, Syslog 탭에서 **"사용자 변경 내용 로깅"** 이 선택된 경우에는 로그 항목을 syslog에서 수집할 수 있습니다.
- MultiFactorAuthSvc.log 및 인증 시도와 관련된 다른 MFA 서버 로그 파일에서 사용자 이름의 다른 항목은 MultiFactorAuthGdpr.exe 내보내기 또는 웹 서비스 SDK GetUserGdpr을 사용하여 제공된 정보에 대한 작동 및 중복 항목으로 간주됩니다.

## <a name="delete-data-from-mfa-server"></a>MFA 서버에서 데이터 삭제

MFA 서버 명령줄에서 해당 설치에 따라 경로를 변경하여 `C:\Program Files\Multi-Factor Authentication Server\MultiFactorAuthGdpr.exe delete <username>` 명령을 실행하여 이 사용자에 대해 수집한 모든 MFA 클라우드 서비스 정보를 삭제합니다.

- 내보내기에 포함된 데이터는 실시간으로 삭제되지만 작동 또는 중복 데이터의 경우 완전히 제거하는 데 최대 30일까지 걸릴 수 있습니다.
- 또한 관리자는 웹 서비스 SDK DeleteUserGdpr 작업을 옵션으로 사용하여 지정된 사용자에 대해 수집된 모든 MFA 클라우드 서비스 정보를 삭제하거나 더 큰 보고 솔루션에 통합할 수 있습니다.

## <a name="gather-data-from-nps-extension"></a>NPS 확장에서 데이터 수집

[Microsoft 개인 정보 포털](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview)에서 내보내기에 대한 요청을 수행합니다.

- 내보내기에 포함된 MFA 정보는 완료하는 데 몇 시간 또는 며칠이 걸릴 수 있습니다.
- AzureMfa/AuthN/AuthNOptCh, AzureMfa/AuthZ/AuthZAdminCh 및 AzureMfa/AuthZ/AuthZOptCh 이벤트 로그에서 사용자 이름의 항목은 내보내기에 제공된 정보에 대한 작동 및 중복 항목으로 간주됩니다.

## <a name="delete-data-from-nps-extension"></a>NPS 확장에서 데이터 삭제

이 사용자에 대해 수집된 모든 MFA 클라우드 서비스 정보를 삭제하려면 [Microsoft 개인 정보 포털](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview)에서 계정 닫기를 요청합니다.

- 데이터를 완전히 제거하는 데 최대 30일까지 걸릴 수 있습니다.

## <a name="gather-data-from-windows-server-2016-azure-mfa-ad-fs-adapter"></a>Windows Server 2016 Azure MFA AD FS 어댑터에서 데이터 수집

[Microsoft 개인 정보 포털](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview)에서 내보내기에 대한 요청을 수행합니다. 

- 내보내기에 포함된 MFA 정보는 완료하는 데 몇 시간 또는 며칠이 걸릴 수 있습니다.
- AD FS 추적/디버그 이벤트 로그(사용하도록 설정된 경우)에서 사용자 이름의 항목은 내보내기에 제공된 정보에 대한 작동 및 중복 항목으로 간주됩니다.

## <a name="delete-data-from-windows-server-2016-azure-mfa-ad-fs-adapter"></a>Windows Server 2016 Azure MFA AD FS 어댑터에서 데이터 삭제

이 사용자에 대해 수집된 모든 MFA 클라우드 서비스 정보를 삭제하려면 [Microsoft 개인 정보 포털](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview)에서 계정 닫기를 요청합니다.

- 데이터를 완전히 제거하는 데 최대 30일까지 걸릴 수 있습니다.

## <a name="gather-data-for-azure-mfa"></a>Azure MFA용 데이터 수집

[Microsoft 개인 정보 포털](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview)에서 내보내기에 대한 요청을 수행합니다.

- 내보내기에 포함된 MFA 정보는 완료하는 데 몇 시간 또는 며칠이 걸릴 수 있습니다.

## <a name="delete-data-for-azure-mfa"></a>Azure MFA용 데이터 삭제

이 사용자에 대해 수집된 모든 MFA 클라우드 서비스 정보를 삭제하려면 [Microsoft 개인 정보 포털](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview)에서 계정 닫기를 요청합니다.

- 데이터를 완전히 제거하는 데 최대 30일까지 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계

[MFA 서버 보고](howto-mfa-reporting.md)
