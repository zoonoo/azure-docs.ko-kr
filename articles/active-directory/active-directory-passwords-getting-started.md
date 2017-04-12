---
title: "시작: Azure AD 암호 관리 | Microsoft Docs"
description: "사용자가 자신의 암호를 재설정하고, 암호 재설정을 위한 사전 필수 구성 요소를 검색하고 Active Directory에서 온-프레미스 암호를 관리하는 비밀번호 쓰기 저장을 사용할 수 있도록 합니다."
services: active-directory
keywords: "Active directory 암호 관리, 암호 관리, Azure AD 암호 재설정"
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
editor: curtand
ms.assetid: bde8799f-0b42-446a-ad95-7ebb374c3bec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/08/2017
ms.author: joflore
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: c2c46637ccccd01c1c3056d6a25ef605cfd68f2d
ms.lasthandoff: 03/29/2017


---
# <a name="getting-started-with-password-management"></a>암호 관리 시작
> [!IMPORTANT]
> **로그인하는 데 문제가 있나요?** 그렇다면 [암호를 변경하고 재설정하는 방법은 다음과 같습니다](active-directory-passwords-update-your-own-password.md#reset-your-password).
>
>

사용자가 자신의 클라우드 Azure Active Directory 또는 온-프레미스 Active Directory 암호를 사용하려면 간단한 몇 단계를 거치면 됩니다. 몇 가지 간단한 필수 구성 요소를 충족한 후, 전체 조직에 대해 암호 변경 및 재설정을 사용할 수 있도록 설정해야 합니다. 이 문서는 다음 개념을 설명합니다.

* [**시작하기 전에 읽을 고객의 최고의 팁**](#top-tips-from-our-customers-to-read-before-you-begin)
 * [**최고의 팁: 설명서 탐색** - 목차 및 브라우저의 찾기 기능을 사용하여 답변 찾기](#top-tip-documentation-navigation---use-our-table-of-contents-and-your-browsers-find-feature-to-find-answers)
 * [**팁 1: 라이선스** - 라이선스 요구 사항 이해](#tip-1-licensing---make-sure-you-understand-the-licensing-requirements)
 * [**팁 2: 테스트** - 관리자가 아닌 최종 사용자로 테스트, 작은 사용자 집합으로 파일럿](#tip-2-testing---test-with-an-end-user-not-an-administrator-and-pilot-with-a-small-set-of-users)
 * [**팁 3: 배포** - 등록할 필요가 없도록 사용자에 대한 데이터 미리 채우기](#tip-3-deployment---pre-populate-data-for-your-users-so-they-dont-have-to-register)
 * [**팁 4: 배포** - 암호 재설정을 사용하여 임시 암호 통신의 필요성 제거](#tip-4-deployment---use-password-reset-to-obviate-the-need-to-communicate-temporary-passwords)
 * [**팁 5: 쓰기 저장** - AAD Connect 컴퓨터의 응용 프로그램 이벤트 로그를 검토하여 비밀번호 쓰기 저장 문제 해결](#tip-5-writeback---look-at-the-application-event-log-on-your-aad-connect-machine-to-troubleshoot-password-writeback)
 * [**팁 6: 쓰기 저장** - 올바른 권한, 방화벽 규칙 및 비밀번호 쓰기 저장에 대한 연결 설정 활성화](#tip-6-writeback---ensure-you-enable-the-correct-permissions-firewall-rules-and-connection-settings-for-password-writeback)
 * [**팁 7: 보고** - Azure AD SSPR 감사 로그로 암호를 등록 또는 재설정하는 사용자 확인](#tip-7-reporting---see-who-is-registering-or-resetting-passwords-with-the-azure-ad-sspr-audit-logs)
 * [**팁 8: 문제 해결** - 문제 해결 가이드 및 FAQ를 참조하여 다수의 문제 해결](#tip-8-troubleshoot---read-our-troubleshooting-guide-and-faq-to-solve-many-issues)
 * [**팁 9: 문제 해결** - 여전히 도움이 필요한 경우 사용자를 지원할 충분한 정보 포함](#tip-9-troubleshoot---if-you-still-need-help-include-enough-information-for-us-to-assist-you)
* [**사용자가 Azure Active Directory 암호를 재설정할 수 있도록 하는 방법**](#enable-users-to-reset-their-azure-ad-passwords)
 * [셀프 서비스 암호 재설정 사전 요구 사항](#prerequisites)
 * [1단계: 암호 재설정 정책 구성](#step-1-configure-password-reset-policy)
 * [2단계: 테스트 사용자에 대한 연락처 데이터 추가](#step-2-add-contact-data-for-your-test-user)
 * [3단계: 사용자로 암호 재설정](#step-3-reset-your-azure-ad-password-as-a-user)
* [**사용자가 온-프레미스 Active Directory 암호를 재설정하거나 변경할 수 있도록 하는 방법**](#enable-users-to-reset-or-change-their-ad-passwords)
 * [암호 쓰기 저장 필수 구성 요소](#writeback-prerequisites)
 * [1단계: 최신 버전의 Azure AD Connect 다운로드](#step-1-download-the-latest-version-of-azure-ad-connect)
 * [2단계: UI 또는 PowerShell을 통해 Azure AD Connect에서 비밀번호 쓰기 저장을 사용하도록 설정](#step-2-enable-password-writeback-in-azure-ad-connect)
 * [3단계: 방화벽 구성](#step-3-configure-your-firewall)
 * [4단계: 적절한 사용 권한 설정](#step-4-set-up-the-appropriate-active-directory-permissions)
 * [5단계: 사용자로 AD 암호 재설정 및 확인](#step-5-reset-your-ad-password-as-a-user)

## <a name="top-tips-from-our-customers-to-read-before-you-begin"></a>시작하기 전에 읽을 고객의 최고의 팁
아래는 조직에서 암호 관리를 배포하는 고객에게 유용한 일부 최고의 팁입니다.

* [**최고의 팁: 설명서 탐색** - 목차 및 브라우저의 찾기 기능을 사용하여 답변 찾기](#top-tip-documentation-navigation---use-our-table-of-contents-and-your-browsers-find-feature-to-find-answers)
* [**팁 1: 라이선스** - 라이선스 요구 사항 이해](#tip-1-licensing---make-sure-you-understand-the-licensing-requirements)
* [**팁 2: 테스트** - 관리자가 아닌 최종 사용자로 테스트, 작은 사용자 집합으로 파일럿](#tip-2-testing---test-with-an-end-user-not-an-administrator-and-pilot-with-a-small-set-of-users)
* [**팁 3: 배포** - 등록할 필요가 없도록 사용자에 대한 데이터 미리 채우기](#tip-3-deployment---pre-populate-data-for-your-users-so-they-dont-have-to-register)
* [**팁 4: 배포** - 암호 재설정을 사용하여 임시 암호 통신의 필요성 제거](#tip-4-deployment---use-password-reset-to-obviate-the-need-to-communicate-temporary-passwords)
* [**팁 5: 쓰기 저장** - AAD Connect 컴퓨터의 응용 프로그램 이벤트 로그를 검토하여 비밀번호 쓰기 저장 문제 해결](#tip-5-writeback---look-at-the-application-event-log-on-your-aad-connect-machine-to-troubleshoot-password-writeback)
* [**팁 6: 쓰기 저장** - 올바른 권한, 방화벽 규칙 및 비밀번호 쓰기 저장에 대한 연결 설정 활성화](#tip-6-writeback---ensure-you-enable-the-correct-permissions-firewall-rules-and-connection-settings-for-password-writeback)
* [**팁 7: 보고** - Azure AD SSPR 감사 로그로 암호를 등록 또는 재설정하는 사용자 확인](#tip-7-reporting---see-who-is-registering-or-resetting-passwords-with-the-azure-ad-sspr-audit-logs)
* [**팁 8: 문제 해결** - 문제 해결 가이드 및 FAQ를 참조하여 다수의 문제 해결](#tip-8-troubleshoot---read-our-troubleshooting-guide-and-faq-to-solve-many-issues)
* [**팁 9: 문제 해결** - 여전히 도움이 필요한 경우 사용자를 지원할 충분한 정보 포함](#tip-9-troubleshoot---if-you-still-need-help-include-enough-information-for-us-to-assist-you)

### <a name="top-tip-documentation-navigation---use-our-table-of-contents-and-your-browsers-find-feature-to-find-answers"></a>최고의 팁: 설명서 탐색 - 목차 및 브라우저의 찾기 기능을 사용하여 답변 찾기
설명서 중 하나를 사용하는 경우 관리자가 목차에서 학습하도록 모든 흥미로운 위치에 대한 빠른 링크를 제공하기 위해 노력해 왔습니다. 

아래 목차를 확인해 보세요. 
* [Azure AD 암호 재설정: 설명서 목차](https://docs.microsoft.com/azure/active-directory/active-directory-passwords)

### <a name="tip-1-licensing---make-sure-you-understand-the-licensing-requirements"></a>팁 1: 라이선스 - 라이선스 요구 사항 이해
Azure AD 암호 재설정의 기능을 작동하려면 조직에서 할당된 하나 이상의 라이선스가 있어야 합니다. 암호 재설정 환경 자체에 사용자당 라이선스를 적용하지 않지만 사용자에게 할당된 라이선스 없이 기능을 사용하는 경우 Microsoft 라이선스 규약 준수에서 벗어나 있는 것으로 간주되고 해당 사용자에게 라이선스를 할당해야 합니다.

다음은 암호 재설정에 필요한 라이선스를 이해하는 데 유용한 몇 가지 문서입니다.
* [일반적인 암호 재설정 라이선스 정보](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-customize#what-customization-options-are-available)
* [기능별 암호 재설정 라이선스 정보](https://docs.microsoft.com/azure/active-directory/active-directory-passwords#pricing-and-availability)
* [암호 쓰기 저장에 지원되는 시나리오](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-learn-more#scenarios-supported-for-password-writeback)

### <a name="tip-2-testing---test-with-an-end-user-not-an-administrator-and-pilot-with-a-small-set-of-users"></a>팁 2: 테스트 - 관리자가 아닌 최종 사용자로 테스트, 작은 사용자 집합으로 파일럿
관리자로 테스트하는 경우 아래에 정의되어 있는 관리자 암호 재설정 정책을 적용합니다.  즉, 최종 사용자에 대해 구성한 정책의 예상된 결과가 표시되지 않습니다.

관리 UX에서 구성된 정책은 관리자가 아닌 최종 사용자에게만 적용됩니다. Microsoft는 관리자에 대해 강력한 기본 암호 재설정 정책을 적용하며 이는 조직의 보안을 유지하기 위해 최종 사용자에 대해 설정하는 정책과 다를 수 있습니다.

#### <a name="administrator-password-reset-policy"></a>관리자 암호 재설정 정책
* **적용 대상** - 모든 관리자 역할(전역 관리자, 기술 지원팀 관리자, 암호 관리자 등)
* **한 개의 게이트 정책이 적용됩니다.**
 * ...평가판이 만들어지기 시작한 후 처음 30일 동안 **또는**
 * ...베니티 도메인이 없는 경우 **및** Azure AD Connect가 ID를 동기화하지 않는 경우
 * **_필수_**: 값이 있는 인증 전자 메일, 대체 전자 메일, 인증 전화, 휴대폰 또는 사무실 전화 중 **하나**
* **두 개의 게이트 정책이 적용됩니다.** 
 * ...평가판의 처음 30일이 경과된 후 **또는**
 * ....베니티 도메인이 있는 경우 **또는** 
 * ...온-프레미스 환경에서 ID를 동기화하도록 Azure AD Connect를 활성화한 경우
 * _**필수**_: 값이 있는 인증 전자 메일, 대체 전자 메일, 인증 전화, 휴대폰 또는 사무실 전화 중 **두 개**

### <a name="tip-3-deployment---pre-populate-data-for-your-users-so-they-dont-have-to-register"></a>팁 3: 배포 - 등록할 필요가 없도록 사용자에 대한 데이터 미리 채우기
대다수의 사람들은 사용자가 기능을 사용하기 위해 암호 재설정을 등록할 필요가 없는 것을 잘 알지 못합니다.  사용자를 위해 전화 또는 전자 메일 속성을 미리 설정하여 **사용자에게 작업 수행을 요구하지 않고** 전체 조직에 암호 재설정을 즉시 롤아웃할 수 있습니다.

API, PowerShell 또는 Azure AD Connect를 사용하여 이를 수행하는 방법을 알아보려면 아래 설명서를 읽어보세요.
* [사용자가 등록할 필요 없이 암호 재설정 배포](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-learn-more#deploying-password-reset-without-requiring-end-user-registration)
* [암호 재설정에서 사용되는 데이터](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-learn-more#what-data-is-used-by-password-reset)

### <a name="tip-4-deployment---use-password-reset-to-obviate-the-need-to-communicate-temporary-passwords"></a>팁 4: 배포 - 암호 재설정을 사용하여 임시 암호 통신의 필요성 제거
이는 팁 3에 대한 덧붙임입니다. 암호 재설정을 위해 사용자를 미리 구성한 후 직원이 처음으로 회사에 조인하는 시나리오를 생각해보십시오. 직원에게 임시 암호로 통신하는 대신 이제 직원이 [Azure AD 암호 재설정 포털](https://passwordreset.microsoftonline.com)로 이동하여 직원의 암호를 재설정하도록 할 수 있습니다.

사용자가 [Windows 10 Azure AD 도메인에 가입된 장치](https://docs.microsoft.com/azure/active-directory/active-directory-azureadjoin-devices-group-policy)를 사용하고 있는 경우 아무런 수고 없이 새 PC에 대한 액세스 권한을 갖도록 허용하여 Windows 10 기본 로그인 화면에서 바로 이를 수행할 수 있습니다.

API, PowerShell 또는 Azure AD Connect를 사용하여 이를 수행하는 방법을 알아보려면 아래 설명서를 읽어보세요. 이 데이터를 미리 채우면 사용자가 자신의 암호를 다시 설정하도록 보내고 사용자는 즉시 자신의 계정에 액세스할 수 있습니다.
* [사용자가 등록할 필요 없이 암호 재설정 배포](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-learn-more#deploying-password-reset-without-requiring-end-user-registration)
* [암호 재설정에서 사용되는 데이터](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-learn-more#what-data-is-used-by-password-reset)

### <a name="tip-5-writeback---look-at-the-application-event-log-on-your-aad-connect-machine-to-troubleshoot-password-writeback"></a>팁 5: 쓰기 저장 - AAD Connect 컴퓨터의 응용 프로그램 이벤트 로그를 검토하여 비밀번호 쓰기 저장 문제 해결
Azure AD Connect 응용 프로그램 이벤트 로그에는 실시간으로 비밀번호 쓰기 저장 서비스와 함께 발생하는 대부분의 문제를 설명하는 다양한 로깅 정보 집합이 포함되어 있습니다. 이 로그에 대한 액세스를 가져오려면 다음 단계를 수행합니다.

1. **Azure AD Connect** 컴퓨터에 로그인
2. **시작** 키를 누르고 **"이벤트 뷰어"**를 입력하여 **Windows 이벤트 뷰어** 열기
3. **응용 프로그램** 이벤트 로그 열기
4. 다음 원본 **PasswordResetService** 또는 **ADSync**에서 이벤트를 찾아 발생할 수 있는 문제에 대해 알아보기

이 로그에 나타날 수 있는 전체 이벤트 목록 및 비밀번호 쓰기 저장에 대한 자세한 문제 해결 지침은 다음을 참조하세요.
* [문제 해결: 비밀번호 쓰기 저장](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback)
* [쓰기 저장 이벤트 로그 오류 코드](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#password-writeback-event-log-error-codes)
* [문제 해결: 비밀번호 쓰기 저장 연결](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback-connectivity)
* [쓰기 저장 배포 - 3단계: 방화벽 구성](#step-3-configure-your-firewall)
* [쓰기 저장 배포 - 4단계: 적절한 사용 권한 설정](#step-4-set-up-the-appropriate-active-directory-permissions)

### <a name="tip-6-writeback---ensure-you-enable-the-correct-permissions-firewall-rules-and-connection-settings-for-password-writeback"></a>팁 6: 쓰기 저장 - 올바른 권한, 방화벽 규칙 및 비밀번호 쓰기 저장에 대한 연결 설정 활성화
쓰기 저장이 제대로 작동하려면 다음을 확인해야 합니다.

1. AD에서 자신의 암호 및 계정 잠금해제 플래그를 수정하는 권한을 갖는 비밀번호 쓰기 저장 기능을 사용하는 사용자에 대해 적절한 **Active Directory 사용 권한**이 설정되었습니다.
2. 비밀번호 쓰기 저장 서비스가 아웃바운드 연결을 사용하는 외부 세계와 안전하게 통신하도록 허용하는 적절한 **방화벽 포트**가 공개되었습니다.
3. Service Bus와 같은 키 암호 재설정 서비스 URL에 대해 적절한 **방화벽 예외**가 만들어졌습니다.
4. **프록시 및 방화벽이 유휴 아웃바운드 연결을 중단하지 않습니다**. 10분 이상이 좋습니다.

문제 해결 지침의 전체 목록 및 비밀번호 쓰기 저장에 대한 사용 권한 및 방화벽 규칙 구성에 대한 특정 지침은 다음을 참조하세요.
* [문제 해결: 비밀번호 쓰기 저장](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback)
* [쓰기 저장 이벤트 로그 오류 코드](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#password-writeback-event-log-error-codes)
* [문제 해결: 비밀번호 쓰기 저장 연결](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback-connectivity)
* [쓰기 저장 배포 - 3단계: 방화벽 구성](#step-3-configure-your-firewall)
* [쓰기 저장 배포 - 4단계: 적절한 사용 권한 설정](#step-4-set-up-the-appropriate-active-directory-permissions)

### <a name="tip-7-reporting---see-who-is-registering-or-resetting-passwords-with-the-azure-ad-sspr-audit-logs"></a>팁 7: 보고 - Azure AD SSPR 감사 로그로 암호를 등록 또는 재설정하는 사용자 확인 
암호 재설정이 배포되고 작동하면 다음 논리적 단계는 작동하는 것을 확인하고 여전히 등록이 필요한 사용자, 재설정 시 사용자가 직면하는 일반적인 문제 및 기능에 대한 투자 수익을 분석하는 것입니다.

Azure AD 암호 재설정 감사 로그를 사용하여 Azure Portal, PowerBI, Azure AD 보고 이벤트 API, PowerShell에서 이 작업 등을 수행할 수 있습니다.  이 보고 기능을 사용하는 방법에 대한 자세한 내용은 다음을 참조하세요.
* [암호 관리 보고서 개요](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-get-insights#overview-of-password-management-reports)
* [Azure Portal에서 암호 관리 보고서를 보는 방법](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-get-insights#how-to-view-password-management-reports)
* [Azure Portal의 셀프 서비스 암호 관리 활동 유형](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-get-insights#self-service-password-management-activity-types-in-the-new-azure-portal)
* [Azure AD 보고서 및 이벤트 API에서 암호 관리 이벤트를 검색하는 방법](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-get-insights#how-to-retrieve-password-management-events-from-the-azure-ad-reports-and-events-api)
* [PowerShell을 사용하여 암호 재설정 등록 이벤트를 빠르게 다운로드하는 방법](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-get-insights#how-to-download-password-reset-registration-events-quickly-with-powershell)

### <a name="tip-8-troubleshoot---read-our-troubleshooting-guide-and-faq-to-solve-many-issues"></a>팁 8: 문제 해결 - 문제 해결 가이드 및 FAQ를 참조하여 다수의 문제 해결
해당 암호 재설정에 다양한 문제 해결 지침 및 FAQ가 있는 것을 알고 계십니까? 질문이 있는 경우 아래 링크에서 이에 대한 답변을 찾을 수 있습니다.

이외에도 [Azure Portal](https://portal.azure.com)에서 **"지원 및 문제 해결"** 블레이드를 사용하여 탐색 창 왼쪽의 **Azure Active Directory** -> **사용자 및 그룹** -> **암호 재설정** -> **지원 및 문제 해결** 아래에서 찾을 수 있는 암호 관리 관리자 환경에서 다양한 문제 해결 정보를 가져올 수도 있습니다.

암호 재설정 문제 해결 지침 및 FAQ에 대한 링크:
* [암호 관리 문제 해결](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot)
* [암호 관리 FAQ](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-faq)

### <a name="tip-9-troubleshoot---if-you-still-need-help-include-enough-information-for-us-to-assist-you"></a>팁 9: 문제 해결 - 여전히 도움이 필요한 경우 사용자를 지원할 충분한 정보 포함
문제 해결에 여전히 도움이 필요한 경우 도움을 드리겠습니다. 지원 사례를 열거나 계정 관리 팀에 연락하여 직접 접촉할 수도 있습니다. 많은 의견 부탁드립니다.

하지만 연락하기 전에 신속하게 도울 수 있도록 **아래 요청한 모든 정보를 수집했는지** 확인하세요.
* [도움이 필요한 경우 포함할 정보](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#information-to-include-when-you-need-help)

#### <a name="ways-to-provide-password-reset-feedback"></a>암호 재설정 피드백을 제공하는 방법
* [기능 요청 또는 문제 해결 - Azure AD MSDN 포럼에 게시](https://social.msdn.microsoft.com/Forums/azure/home?forum=WindowsAzureAD)
* [기능 요청 또는 문제 해결 - StackOverflow에 게시](http://stackoverflow.com/questions/tagged/azure-active-directory)
* [기능 요청 또는 문제 해결 - 트윗 @azuread!](https://twitter.com/azuread)
* [기능 요청 - UserVoice에 메모 남기기](https://feedback.azure.com/forums/169401-azure-active-directory)

## <a name="enable-users-to-reset-their-azure-ad-passwords"></a>사용자가 Azure AD 암호를 재설정할 수 있도록 설정
이 섹션에서는 AAD 클라우드 디렉터리에 대한 셀프 서비스 암호 재설정 사용, 셀프 서비스 암호 재설정을 위한 사용자 등록 및 마지막으로 사용자로 테스트 셀프 서비스 암호 재설정 수행을 안내합니다.

* [셀프 서비스 암호 재설정 사전 요구 사항](#prerequisites)
* [1단계: 암호 재설정 정책 구성](#step-1-configure-password-reset-policy)
* [2단계: 테스트 사용자에 대한 연락처 데이터 추가](#step-2-add-contact-data-for-your-test-user)
* [3단계: 사용자로 암호 재설정](#step-3-reset-your-azure-ad-password-as-a-user)

### <a name="prerequisites"></a>선행 조건
셀프 서비스 암호 재설정을 사용하도록 설정하고 사용하기 전에, 다음 필수 조건을 완료해야 합니다.

* AAD 테넌트를 만듭니다. 자세한 내용은 [Azure AD Premium 시작](https://azure.microsoft.com/trial/get-started-active-directory/)
* Azure 구독. 자세한 내용은 [Azure AD 테넌트 정의](active-directory-administer.md#what-is-an-azure-ad-tenant)를 참조하세요.
* AAD 테넌트를 Azure 구독과 연결합니다. 자세한 내용은 [Azure 구독과 Azure AD의 연관 관계](https://msdn.microsoft.com/library/azure/dn629581.aspx)를 참조하세요.
* Azure AD Premium, Basic을 업그레이드하거나 O365 유료 라이선스를 사용합니다. 자세한 내용은 [Azure Active Directory 버전](https://azure.microsoft.com/pricing/details/active-directory/)을 참조하세요.

  > [!NOTE]
  > 클라우드 사용자를 위해 셀프 서비스 암호 재설정을 사용하려면 Azure AD Premium, Azure AD Basic 또는 유료 O365 라이선스로 업그레이드해야 합니다.  온-프레미스 사용자를 위해 셀프 서비스 암호 재설정을 사용하려면 Azure AD Premium으로 업그레이드해야 합니다. 자세한 내용은 [Azure Active Directory 버전](https://azure.microsoft.com/pricing/details/active-directory/)을 참조하세요. 이 정보에는 Azure AD Premium 또는 Basic을 등록하는 방법, 라이선스 계획을 활성화하고 Azure AD 액세스를 활성화하는 방법 및 관리자와 사용자 계정에 대한 액세스를 할당하는 방법에 대한 지침이 포함됩니다.
  >
  >
* AAD 디렉터리에 하나 이상의 관리자 계정 및 사용자 계정 하나를 만듭니다.
* 만든 관리자 및 사용자 계정에 AAD Premium, Basic 또는 O365 유료 라이선스를 할당합니다.

### <a name="step-1-configure-password-reset-policy"></a>1단계: 암호 재설정 정책 구성
사용자 암호 재설정 정책을 구성하려면 다음 단계를 완료합니다.

1. 선택한 브라우저를 열고 [Azure 클래식 포털](https://manage.windowsazure.com)로 이동합니다.
2. [Azure 클래식 포털](https://manage.windowsazure.com)의 왼쪽 탐색 모음에서 **Active Directory 확장**을 찾습니다.

   ![Azure AD에서 암호 관리][001]
3. **디렉터리** 탭에서 사용자 암호 재설정 정책을 구성할 디렉터리를 클릭합니다(예: Wingtip Toys).

    ![][002]
4. **구성** 탭을 클릭합니다.

   ![][003]

5. **구성** 탭에서 **사용자 암호 재설정 정책** 섹션으로 아래로 스크롤합니다.  지정된 디렉터리에 대한 사용자 암호 재설정 정책의 모든 측면을 구성하는 곳입니다. *구성 탭이 보이지 않으면 Azure Active Directory Premium 또는 Basic에 등록했는지 확인하고 이 기능을 구성 중인 관리자 계정에 __라이선스를 할당했는지__ 확인합니다.*  

   > [!NOTE]
   > **사용자가 설정한 정책은 관리자가 아닌 조직의 최종 사용자에게만 적용됩니다**. 보안상의 이유로 Microsoft는 관리자에 대 한 암호 재설정 정책을 제어합니다. 관리자에 대한 현재 정책에는 휴대 전화 및 전자 메일 주소가 필요합니다.

   ![][004]
6. 사용자 암호 재설정 정책을 구성하려면 **암호 재설정을 위해 사용할 수 있는 사용자** 토글을 **예**로 밀어 설정합니다.  디렉터리에서 이 기능의 작동 방식을 구성할 수 있는 여러 제어를 표시합니다.  필요에 따라 자유롭게 암호 재설정을 사용자 지정할 수 있습니다.  각 암호 재설정 정책 컨트롤이 수행하는 내용을 자세히 알려면, [사용자 지정: Azure AD 암호 관리](active-directory-passwords-customize.md)를 참조하세요.

   ![][005]
7. 테넌트에 대해 원하는 대로 사용자 암호 재설정 정책을 구성한 후, 화면 아래에서 **저장** 단추를 클릭합니다.

   > [!NOTE]
   > 가장 복잡한 경우에 기능이 동작하는 방법을 볼 수 있도록 두 개의 인증 질문 사용자 암호 재설정 정책을 권장합니다.
   >
   >

   ![][006]

   > [!NOTE]
   > **사용자가 설정한 정책은 관리자가 아닌 조직의 최종 사용자에게만 적용됩니다**. 보안상의 이유로 Microsoft는 관리자에 대 한 암호 재설정 정책을 제어합니다. 관리자에 대한 현재 정책에는 휴대 전화 및 전자 메일 주소가 필요합니다.
   >
   >

### <a name="step-2-add-contact-data-for-your-test-user"></a>2단계: 테스트 사용자에 대한 연락처 데이터 추가
암호 재설정에 사용할 사용자의 조직에서 사용자에 대한 데이터를 지정하는 방법에 대한 몇 가지 옵션이 있습니다.

* [Azure 클래식 포털](https://manage.windowsazure.com) 또는 [Office 365 관리자 포털](https://portal.microsoftonline.com)에서 사용자를 편집합니다.
* AAD Connect를 사용하여 온-프레미스 Active Directory 도메인에서 Azure AD로 사용자 속성을 동기화합니다.
* Windows PowerShell을 사용하여 사용자 속성을 편집합니다.
* [Http://aka.ms/ssprsetup](http://aka.ms/ssprsetup)의 등록 포털로 안내하여 사용자는 사용자 자신의 데이터를 등록할 수 있습니다.
* **사용자가 액세스 패널에 로그인할 때 등록해야 함** SSPR 구성 옵션을 **예**로 설정하여 [http://myapps.microsoft.com](http://myapps.microsoft.com)의 액세스 패널에 로그인할 때 암호 재설정을 위해 사용자를 등록해야 합니다.

암호 재설정으로 사용되는 데이터 및 이 데이터에 대한 형식 요구사항에 대해 자세히 알아보려면 [암호 재설정에서 사용되는 데이터](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset)를 참조하세요.

#### <a name="to-add-user-contact-data-via-the-user-registration-portal"></a>사용자 등록 포털을 통해 사용자 연락처 데이터를 추가하려면
1. 암호 재설정 등록 포털을 사용하려면, 이 페이지에([http://aka.ms/ssprsetup](http://aka.ms/ssprsetup)) 대한 링크를 조직 내의 사용자에게 제공하거나 사용자를 자동으로 등록하는 옵션을 설정합니다.  이 링크를 클릭하면 자신의 조직 계정을 사용하여 로그인해야 합니다.  로그인한 후 다음 페이지를 참조합니다.

   ![][007]
2. 여기에서 사용자는 휴대폰, 대체 전자 메일 주소 또는 보안 질문을 제공하고 확인할 수 있습니다.  다음은 휴대폰을 확인하는 모습입니다.

   ![][008]
3. 사용자가 이 정보를 지정한 후, 페이지가 업데이트되어 정보가 유효함을 나타냅니다(아래에서는 애매했음).  **마침** 또는 **취소** 단추를 클릭하면, 액세스 패널이 사용자에게 표시됩니다.

   ![][009]
4. 사용자가 이 정보를 확인하면, 프로필은 자신이 제공하는 데이터로 업데이트됩니다.  이 예제에서, **사무실 전화** 번호가 수동으로 지정되었으므로, 사용자는 자신의 암호를 재설정하기 위한 연락 방법으로 이를 사용할 수도 있습니다.

   ![][010]

### <a name="step-3-reset-your-azure-ad-password-as-a-user"></a>3단계: 사용자로 Azure AD 암호 재설정
사용자 재설정 정책을 구성하고 사용자에 대한 연락처 정보를 지정하였으므로 이 사용자는 셀프 서비스 암호 재설정을 수행할 수 있습니다.

#### <a name="to-perform-a-self-service-password-reset"></a>셀프 서비스 암호 재설정을 수행하려면
1. [**portal.microsoftonline.com**](http://portal.microsoftonline.com)과 같은 사이트로 이동하는 경우, 아래와 같은 로그인 화면이 표시됩니다.  **계정에 액세스할 수 없습니까?** 링크를 클릭하여 암호 재설정 UI를 테스트합니다.

   ![][011]
2. **계정에 액세스할 수 없습니까?**를 클릭하면, 암호를 재설정할 **사용자 ID**를 묻는 새 페이지로 이동합니다.  테스트 **사용자 ID**를 여기에 입력하고 captcha를 전달하고 **다음**을 클릭합니다.

   ![][012]
3. 사용자가 **사무실 전화**, **휴대폰** 및 **대체 전자 메일**을 지정했으므로 이 경우에 첫 번째 인증을 통과하기 위해 제공한 모든 옵션을 표시합니다.

   ![][013]
4. 이 경우, 먼저 **사무실 전화**로 **통화**를 선택합니다.  전화 기반 방법을 선택하는 경우 사용자가 **전화번호 확인** 을 완료해야 암호를 재설정할 수 있습니다.  이렇게 하면 악의적인 사람이 사용자 조직에서 사용자의 전화번호를 스팸 발송하지 못합니다.

   ![][014]
5. 사용자가 전화 번호를 확인한 후 전화를 클릭하면 스피너가 표시되고 전화 벨이 울립니다.  수화기를 들면 **사용자가 "#"를 눌러야** 계정을 확인할 수 있다는 메시지가 한 번 재생됩니다.  이 키를 누르면 사용자는 첫 번째 질문을 소유하고 두 번째 확인 단계로의 UI를 진행함을 자동으로 확인합니다.

   ![][015]
6. 첫 번째 질문을 통과하면, UI가 자동으로 업데이트되어 사용자가 소유한 선택 항목 목록에서 제거됩니다.  이 경우 먼저 **사무실 전화**를 사용했기 때문에 **휴대폰** 및 **대체 전자 메일**은 두 번째 확인 단계에 대한 질문으로 사용할 수 있는 유효한 옵션으로 유지됩니다.  **내 대체 전자 메일로 메일 전송** 옵션을 클릭합니다.  완료한 후 전자 메일을 누르면 파일의 대체 전자 메일로 메일을 전송합니다.

   ![][016]
7. 다음은 사용자가 볼 전자 메일의 샘플, 테넌트 브랜드 공지입니다.

   ![][017]
8. 전자 메일이 도착하면 페이지가 업데이트되며, 아래에 표시된 입력 상자의 전자 메일에서 찾은 확인을 입력할 수 있습니다.  적절한 코드를 입력한 후 다음 단추가 켜지고 두 번째 확인 단계를 통해 전달할 수 있습니다.

   ![][018]
9. 조직 정책의 요구를 충족한 후 새 암호를 선택할 수 있습니다.  암호는 이를 기반으로 유효성을 검사하며 AAD "강력한" 암호 요구 사항을 충족하고( [Azure AD의 암호 정책](https://msdn.microsoft.com/library/azure/jj943764.aspx)참조), 강도 유효성 검사기는 입력한 암호가 해당 정책을 충족하는지 여부를 나타냅니다.

   ![][019]
10. 조직 정책을 충족하는 일치하는 암호를 제공하면 사용자 암호가 재설정되고 새 암호를 사용하여 즉시 로그인할 수 있습니다.

    ![][020]

## <a name="enable-users-to-reset-or-change-their-ad-passwords"></a>사용자가 AD 암호를 재설정하거나 변경할 수 있도록 설정
이 섹션에서는 암호를 온-프레미스 Active Directory에 쓰기 저장하도록 구성하는 과정을 안내합니다.

* [암호 쓰기 저장 필수 구성 요소](#writeback-prerequisites)
* [1단계: 최신 버전의 Azure AD Connect 다운로드](#step-1-download-the-latest-version-of-azure-ad-connect)
* [2단계: UI 또는 PowerShell을 통해 Azure AD Connect에서 비밀번호 쓰기 저장을 사용하도록 설정](#step-2-enable-password-writeback-in-azure-ad-connect)
* [3단계: 방화벽 구성](#step-3-configure-your-firewall)
* [4단계: 적절한 사용 권한 설정](#step-4-set-up-the-appropriate-active-directory-permissions)
* [5단계: 사용자로 AD 암호 재설정 및 확인](#step-5-reset-your-ad-password-as-a-user)

### <a name="writeback-prerequisites"></a>쓰기 저장 필수 구성 요소
암호 쓰기 저장을 사용하도록 설정하고 사용하기 전에, 다음 필수 조건을 완료해야 합니다.

* Azure AD Premium을 사용하도록 설정한 Azure AD 테넌트가 있습니다.  자세한 내용은 [Azure Active Directory 버전](active-directory-editions.md)을 참조하세요.
* 비밀번호 쓰기 저장이 작동하려면 Azure AD Connect를 주 도메인 컨트롤러 에뮬레이터에 연결해야 합니다.  필요한 경우 Active Directory 동기화 커넥터의 **속성**을 마우스 오른쪽 단추로 클릭하고 **디렉터리 파티션 구성**을 선택하여 주 도메인 컨트롤러를 사용하도록 Azure AD Connect를 구성할 수 있습니다. 여기에서 **도메인 컨트롤러 연결 설정** 섹션을 찾고 **기본 설정 도메인 컨트롤러만 사용**이라는 상자를 선택합니다.  참고: 기본 설정된 DC가 PDC 에뮬레이터가 아닌 경우 Azure AD Connect는 비밀번호 쓰기 저장의 PDC에 연결됩니다.
* 암호 재설정이 구성되고 테넌트에서 사용하도록 설정합니다.  자세한 내용은 [사용자가 Azure AD 암호를 재설정할 수 있도록 설정](#enable-users-to-reset-their-azure-ad-passwords)
* 하나 이상의 관리자 계정이 있고 이 기능을 테스트하는데 사용할 수 있는 Azure AD Premium 라이선스가 있는 테스트 사용자 계정이 하나 있습니다.  자세한 내용은 [Azure Active Directory 버전](active-directory-editions.md)을 참조하세요.

  > [!NOTE]
  > 암호 쓰기 저장을 설정하는 데 사용하는 관리자 계정이 페더레이션된 계정(온-프레미스 AD에서 만들고 Azure AD로 동기화된)이 아닌 클라우드 관리자 계정(Azure AD에서 만든)인지 확인합니다.
  >
  >
* Windows Server 2008, Windows Server 2008 R2, Windows Server 2012 또는 최신 서비스 팩이 설치된 Windows Server 2012 R2를 실행 중인 단일 또는 다중 포리스트 AD 온-프레미스 배포가 있습니다.

  > [!NOTE]
  > 이전 버전의 Windows Server 2008 또는 2008 R2를 실행 중인 경우, 이 기능을 여전히 사용할 수 있지만 클라우드에서 로컬 AD 암호 정책을 적용할 수 있으려면 [KB 2386717를 다운로드하고 설치](https://support.microsoft.com/kb/2386717) 해야 합니다.
  >
  >
* Azure AD Connect 도구가 설치되어 있고 클라우드로 동기화할 AD 환경을 준비합니다.  자세한 내용은 [클라우드에서 온-프레미스 ID 인프라 사용](connect/active-directory-aadconnect.md)을 참조하세요.

  > [!NOTE]
  > 비밀번호 쓰기 저장을 테스트하기 전에 먼저 Azure AD Connect의 AD와 Azure AD에서 모두 전체 가져오기 및 전체 동기화를 완료하도록 합니다.
  >
  >
* Azure AD Sync 또는 Azure AD Connect를 사용하는 경우, **TCP 443** 아웃바운드(및 경우에 따라 **TCP 9350-9354**)가 열려 있어야 합니다.  자세한 내용은 [3단계: 방화벽 구성](#step-3-configure-your-firewall) 을 참조하세요. 이 시나리오에서 DirSync 사용은 더 이상 지원되지 않습니다.  DirSync를 아직 사용 중인 경우, 비밀번호 쓰기 저장을 배포하기 전에 최신 버전의 Azure AD Connect로 업그레이드하십시오.

  > [!NOTE]
  > Azure AD Sync 또는 디렉터리 동기화 도구를 사용하는 사람은 최신 버전의 Azure AD Connect로 업그레이드하여 가능한 최상의 경험 및 릴리스될 때의 새로운 기능을 확인하는 것이 좋습니다.
  >
  >

### <a name="step-1-download-the-latest-version-of-azure-ad-connect"></a>1단계: 최신 버전의 Azure AD Connect 다운로드
암호 쓰기 저장은 Azure AD Connect 또는 **1.0.0419.0911** 이상의 버전이 있는 Azure AD Sync 도구의 릴리스에서 사용 가능합니다.  자동 계정 잠금 해제가 있는 암호 쓰기 저장은 Azure AD Connect 또는 **1.0.0485.0222** 이상의 버전이 있는 Azure AD Sync 도구의 릴리스에서 사용 가능합니다. 이전 버전을 실행하는 경우 진행하기 전에 적어도 이 버전으로 업그레이드합니다. [여기를 클릭하여 최신 버전의 Azure AD Connect를 다운로드합니다](connect/active-directory-aadconnect.md#install-azure-ad-connect).

#### <a name="to-check-the-version-of-azure-ad-sync"></a>Azure AD Sync의 버전을 확인하려면
1. **%ProgramFiles%\Azure Active Directory Sync\**로 이동합니다.
2. **ConfigWizard.exe** 실행 파일을 찾습니다.
3. 실행 파일을 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **속성** 옵션을 선택합니다.
4. **세부 정보** 탭을 클릭합니다.
5. **파일 버전** 필드를 찾습니다.

   ![][021]

이 버전 번호가 **1.0.0419.0911**보다 크거나 같거나, 또는 Azure AD Connect를 설치하면, [2단계: UI 또는 PowerShell을 통해 Azure AD Connect에서 비밀번호 쓰기 저장을 사용하도록 설정 및 확인](#step-2-enable-password-writeback-in-azure-ad-connect)으로 건너뛸 수 있습니다.

> [!NOTE]
> 처음으로 Azure AD Connect 도구를 설치한 경우, 디렉터리 동기화를 위한 환경을 준비하는 몇 가지 모범 사례를 따르는 것이 좋습니다.  Azure AD Connect 도구를 설치하기 전에 [Office 365 관리자 포털](https://portal.microsoftonline.com) 또는 [Azure 클래식 포털](https://manage.windowsazure.com)에서 디렉터리 동기화를 활성화해야 합니다.  자세한 내용은 [Azure AD Connect 관리](active-directory-aadconnect-whats-next.md)를 참조하세요.
>
>

### <a name="step-2-enable-password-writeback-in-azure-ad-connect"></a>2단계: Azure AD Connect에서 비밀번호 쓰기 저장 사용
이제 Azure AD Connect 도구를 다운로드 했으므로 암호 쓰기 저장 사용 준비가 된 것입니다.  이 두 방법 중 하나를 수행할 수 있습니다.  Azure AD Connect 설치 마법사의 선택적 기능 화면에서 암호 쓰기 저장을 사용하거나 Windows PowerShell을 통해 설정할 수 있습니다.

#### <a name="to-enable-password-writeback-in-the-configuration-wizard"></a>구성 마법사에서 암호 쓰기 저장을 사용 하도록 설정하려면
1. **디렉터리 동기화 컴퓨터**에서 **Azure AD Connect** 구성 마법사를 엽니다.
2. **선택적 기능** 구성 화면에 도달할 때까지 단계를 클릭합니다.
3. **암호 쓰기 저장** 옵션을 선택합니다.

   ![][022]
4. 마법사를 완료하면, 마지막 페이지는 변경 내용을 요약하고 암호 쓰기 저장 구성 변경 내용이 포함됩니다.

> [!NOTE]
> 이 마법사를 다시 실행하고 해당 기능을 선택 취소하거나, 또는 [Azure 클래식 포털](https://manage.windowsazure.com)에서 사용자 디렉터리의 **구성** 탭의 **사용자 암호 재설정 정책** 섹션에서 **온-프레미스 디렉터리로 비밀번호 쓰기 저장** 설정을 **아니오**로 설정하여 언제든지 비밀번호 쓰기 저장을 사용하지 않도록 설정할 수 있습니다.  사용자 암호 재설정 환경 사용자 지정에 관한 자세한 내용은 [사용자 지정: Azure AD 암호 관리](active-directory-passwords-customize.md)를 확인합니다.
>
>

#### <a name="to-enable-password-writeback-using-windows-powershell"></a>Windows PowerShell을 사용하여 암호 쓰기 저장을 사용하도록 설정하려면
1. 사용자 **디렉터리 동기화 컴퓨터**에서 새 **관리자 권한 Windows PowerShell 창**을 엽니다.
2. 모듈이 아직 로드되지 않은 경우, `import-module ADSync` 명령에 입력하여 현재 세션으로 Azure AD Connect cmdlet을 로드합니다.
3. `$aadConnectorName = Get-ADSyncConnector|where-object {$_.name -like "*AAD"}`과 같이 `Get-ADSyncConnector` cmdlet을 실행하고 결과를 `$aadConnectorName`에 저장하여 시스템에서 Azure AD Connector의 목록을 가져옵니다.
4. 다음 cmdlet을 실행하여 현재 커넥터에 대한 쓰기 저장의 현재 상태를 가져오려면: `Get-ADSyncAADPasswordResetConfiguration –Connector $aadConnectorName.name`
5. 이 cmdlet을 실행하여 암호 쓰기 저장을 활성화합니다. `Set-ADSyncAADPasswordResetConfiguration –Connector $aadConnectorName.name –Enable $true`

> [!NOTE]
> 자격 증명을 입력하라는 메시지가 표시되면, AzureADCredential에 대해 지정되는 관리자 계정이 페더레이션된 계정(온-프레미스 AD에서 만들고 Azure AD로 동기화된)이 아닌 **클라우드 관리자 계정(Azure AD에서 만든)**입니다.
>
> [!NOTE]
> 위의 동일한 지침을 반복하지만 단계에서 `$false`을 전달하거나, 또는 [Azure 클래식 포털](https://manage.windowsazure.com)에서 사용자 디렉터리의 **구성** 탭의 **사용자 암호 재설정 정책 섹션**에서 **온-프레미스 디렉터리로 비밀번호 쓰기 저장** 설정을 **아니오**로 설정하여 언제든지 비밀번호 쓰기 저장을 사용하지 않도록 설정하여 PowerShell을 통해 비밀번호 쓰기 저장을 사용하지 않도록 설정할 수 있습니다.
>
>

#### <a name="verify-that-the-configuration-was-successful"></a>구성이 성공되었는지 확인합니다.
구성이 성공하면 Windows PowerShell 창에서 암호 재설정 쓰기 저장을 사용하도록 설정되었다는 메시지를 보거나, 구성 UI에서 성공 메시지를 볼 수 있습니다.

이벤트 뷰어를 열고 응용 프로그램 이벤트 로그로 이동하고 **PasswordResetService**에서 **31005 - OnboardingEventSuccess** 이벤트를 찾아 서비스가 올바르게 설치되었는지 확인할 수도 있습니다.

  ![][023]

### <a name="step-3-configure-your-firewall"></a>3단계: 방화벽 구성
비밀번호 쓰기 저장을 사용하도록 설정한 후에 Azure AD Connect를 실행하는 컴퓨터에서 비밀번호 쓰기 저장 요청을 수신하도록 Microsoft 클라우드 서비스에 연결할 수 있는지 확인해야 합니다. 이 단계에서는 [Microsoft에서 소유한 특정 URL 및 특정 네트워크 포트를 통한 IP 주소](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2?ui=en-US&rs=en-US&ad=US)에 대한 아웃바운드 연결을 허용하도록 네트워크 어플라이언스(프록시 서버, 방화벽 등)에서의 연결 규칙을 업데이트하는 것을 포함합니다. 이러한 변경은 Azure AD Connect 도구 버전에 따라 달라질 수도 있습니다. 자세한 컨텍스트는 [비밀번호 쓰기 저장의 작동 원리](active-directory-passwords-learn-more.md#how-password-writeback-works) 및 [비밀번호 쓰기 저장 보안 모델](active-directory-passwords-learn-more.md#password-writeback-security-model)에서 자세히 알아볼 수 있습니다.

#### <a name="why-do-i-need-to-do-this"></a>이렇게 하는 것이 왜 필요한가요?

비밀번호 쓰기 저장이 제대로 작동하기 위해 Azure AD Connect를 실행하는 컴퓨터는 Azure Service Bus뿐만 아니라 암호 재설정 서비스를 사용할 수 있어야 합니다.

Azure AD Connect 도구 **1.1.443.0** 이상의 경우:

- 최신 버전의 Azure AD Connect 도구에서 다음에 대한 **아웃바운드 HTTPS** 액세스가 필요합니다.
    - *passwordreset.microsoftonline.com*
    - *servicebus.windows.net*

Azure AD Connect의 경우 도구 버전 **1.0.8667.0** ~ **1.1.380.0**:

- **옵션 1:** 포트 443 통해 모든 아웃바운드 HTTPS 연결을 허용합니다.(URL 또는 IP 주소 사용)
    - 이것을 사용해야 하는 경우:
        - Azure Datacenter IP 범위가 시간이 지남에 따라 변경될 때 업데이트해야 할 필요가 없는 가장 간단한 구성을 원할 경우 이 옵션을 사용합니다.
    - 필요한 단계:
        - URL 또는 IP 주소 사용하여 포트 443 통해 모든 아웃바운드 HTTPS 연결을 허용합니다.
<br><br>
- **옵션 2:** 특정 IP 범위 및 URL에 대한 아웃바운드 HTTPS 연결을 허용합니다.
    - 이것을 사용해야 하는 경우:
        - 네트워크 환경이 제한적이거나 다음 아웃바운드 연결을 허용하는 것에 대해 불편하게 느껴질 경우 이 옵션을 사용합니다.
        - 이 구성에서 비밀번호 쓰기 저장이 정상적으로 계속 작동하게 하려면 네트워킹 어플라이언스가 매주 Microsoft Azure Datacenter IP Ranges 목록에서 최신 IP를 받아 업데이트된 상태를 유지하는지 확인해야 합니다. 이러한 IP 범위는 XML 파일 형태로 제공되며 매주 수요일(태평양 표준시)에 업데이트되고 다음 월요일(태평양 표준시)에 발효됩니다.
    - 필요한 단계:
        - *. Servicebus.windows.net에 모든 아웃바운드 HTTPS 연결을 허용합니다.
        - Microsoft Azure Datacenter IP Ranges 목록에 있는 모든 IP에 대한 모든 아웃바운드 HTTPS 연결을 허용하며 이 구성은 매주 업데이트됩니다. 이 목록은 [여기](https://www.microsoft.com/download/details.aspx?id=41653)에서 다운로드하는 데 사용할 수 있습니다.

> [!NOTE]
> 위의 지침에 따라 비밀번호 쓰기 저장을 구성했고 Azure AD Connect 이벤트 로그에 오류가 보이지 않으나, 테스트할 때 연결 오류가 발생한다면 사용자 환경에 있는 네트워킹 어플라이언스가 IP 주소에 대한 HTTPS 연결을 차단하는 경우일 수도 있습니다. 예를 들어, *https://*. servicebus.windows.net*에 대한 연결이 허용되는 반면에 해당 범위 내의 특정 IP 주소에 대한 연결이 차단될 수도 있습니다. 이 문제를 해결하려면 포트 443 통해 모든 URL 또는 IP 주소에 대한 모든 아웃바운드 HTTPS 연결을 허용 하거나(위의 옵션 1), 네트워크 팀과 함께 특정 IP 주소에 대한 HTTPS 연결을 명시적으로 허용하도록(위의 옵션 2) 네트워킹 환경을 구성할 필요가 있습니다.

**이전 버전:**

- 포트 443, 9350-9354 및 5671을 통한 아웃바운드 TCP 연결 허용
- *https://ssprsbprodncu-sb.accesscontrol.windows.net/*에 대한 아웃바운드 연결을 허용합니다.

> [!NOTE]
> Azure AD Connect 버전이 1.0.8667.0 이전이라면 구성을 쉽게 할 수 있도록 여러 비밀번호 쓰기 저장 네트워킹 기능을 향상시킨 [Azure AD Connect 최신 버전](https://www.microsoft.com/download/details.aspx?id=47594)으로 업그레이드하는 것이 좋습니다.

네트워크 어플라이언스를 구성한 후 Azure AD Connect 도구를 실행하는 컴퓨터를 다시 부팅합니다.

#### <a name="idle-connections-on-azure-ad-connect-114430-and-up"></a>Azure AD Connect의 유휴 연결(1.1.443.0 이상)
Azure AD Connect 도구는 ServiceBus 끝점에 주기적인 ping/keepalive를 보내서 연결된 상태를 유지하는지 확인합니다. 도구가 너무 많은 연결이 끊기는 것을 감지하면 끝점에 대한 ping 빈도를 자동으로 높입니다. 최저 'ping 간격'은 60초마다 ping 1회로 떨어지지만 **적어도 2~3분간 유휴 연결이 지속되도록 프록시/방화벽을 허용하는 것이 좋습니다.** \*오래된 버전의 경우 4분 이상이 좋습니다.

### <a name="step-4-set-up-the-appropriate-active-directory-permissions"></a>4단계: 적절한 Active Directory 사용 권한 설정
암호를 재설정한 사용자를 포함한 모든 포리스트의 경우, X가 구성 마법사에서 해당 포리스트에 대해 지정된 계정이면(초기 구성 중), X는 **암호 재설정**, **암호 변경**, `lockoutTime`에서 **쓰기 권한**, `pwdLastSet`에서 **쓰기 권한**을 제공하며 해당 포리스트에서 각 도메인의 루트 개체 또는 SSPR용 범위에 두려는 사용자 OU에 대한 권한을 확장합니다.  특정 사용자 개체의 집합에만 재설정 사용 권한을 지정하려는 경우 후자의 옵션을 사용할 수 있습니다. 도메인 루트에 대해 이렇게 하는 경우 도메인은 허용되지 않습니다. 오른쪽은 모든 사용자 개체에서 상속된 것으로 표시되어야 합니다.  

위에서 참조되는 계정을 모르는 경우, Azure Active Directory Connect 구성 UI를 열고 **솔루션 검토** 옵션을 클릭합니다.  권한을 추가해야 하는 계정은 아래 스크린샷에서 빨간색 밑줄이 그어집니다.

**<font color="red">시스템에 있는 각 포리스트의 도메인 각각에 이 사용 권한을 설정하고, 그렇지 않은 경우 비밀번호 쓰기 저장이 제대로 작동하지 않습니다.</font>**

  ![][032]

  이러한 사용 권한을 설정하면 각 포리스트에 대한 MA 서비스 계정이 해당 포리스트 내에서 사용자 계정을 대신하여 암호를 관리할 수 있습니다. 이러한 사용 권한을 할당하는 것을 잊은 경우, 쓰기 저장이 올바르게 구성된 것으로 표시되면, 클라우드에서 온-프레미스 암호 관리를 시도하면 오류가 발생합니다. 다음은 **Active Directory 사용자 및 컴퓨터** 관리 스냅인을 사용하여 수행할 수 있는 방법에 대한 자세한 단계입니다.

> [!NOTE]
> 이 사용 권한이 디렉터리의 모든 개체를 복제하려면 한 시간이 걸릴 수 있습니다.
>
>

#### <a name="to-set-up-the-right-permissions-for-writeback-to-occur"></a>쓰기 저장이 이루어지도록 올바른 권한을 설정하려면
1. 적절한 도메인 관리 권한이 있는 계정으로 **Active Directory 사용자 및 컴퓨터** 를 엽니다.
2. **보기 메뉴** 옵션에서 **고급 기능**이 켜져 있어야 합니다.
3. 왼쪽 패널에서, 도메인의 루트를 나타내는 개체를 마우스 오른쪽 단추로 클릭합니다.
4. **보안** 탭을 클릭합니다.
5. **고급**을 클릭합니다.

   ![][024]
6. **권한** 탭에서 **추가**를 클릭합니다.

   ![][025]
7. 권한을 부여하려는 계정을 선택합니다(해당 포리스트에 대한 동기화를 설정하는 동안 지정된 동일한 계정임).
8. 위쪽의 드롭다운 목록에서 **하위 사용자 개체**를 선택합니다.
9. 나타나는 **권한 항목** 대화 상자에서 **암호 재설정**, **암호 변경**, `lockoutTime`에서 **쓰기 권한** 및 `pwdLastSet`에서 **쓰기 권한** 상자를 선택합니다.

   ![][026]
   ![][027]
   ![][028]
10. 열린 모든 대화 상자를 통해 **적용/확인** 을 클릭합니다.

### <a name="step-5-reset-your-ad-password-as-a-user"></a>5단계: 사용자로 AD 암호 재설정
이제 암호 쓰기 저장을 사용하도록 설정했으므로, 클라우드 테넌트로 동기화된 계정자의 사용자 암호를 재설정하여 작동하는지 테스트할 수 있습니다.

#### <a name="to-verify-password-writeback-is-working-properly"></a>암호 쓰기 저장이 제대로 작동하는 지 확인하려면
1. [https://passwordreset.microsoftonline.com](https://passwordreset.microsoftonline.com) 으로 이동하거나 임의의 조직 ID 로그인 화면으로 이동하고 **계정에 액세스할 수 없습니까?** 링크를 클릭합니다.

   ![][029]
2. 이제 암호를 재설정하려는 사용자 ID에 대해 묻는 새 페이지가 나타나야 합니다. 테스트 사용자 ID를 입력하고 암호 재설정 작업을 진행합니다.
3. 암호를 재설정한 후 다음과 비슷한 화면이 표시됩니다. 사용자가 온-프레미스 및/또는 클라우드 디렉터리에서 암호를 성공적으로 재설정했음을 의미합니다.

   ![][030]
4. 작업이 성공했음을 확인하거나 모든 오류를 진단하려면 **디렉터리 동기화 컴퓨터**로 이동하고 **이벤트 뷰어**를 열고 **응용 프로그램 이벤트 로그**로 이동하고 테스트 사용자의 **PasswordResetService** 원본에서 **31002 - PasswordResetSuccess** 이벤트를 찾습니다.

   ![][031]



## <a name="next-steps"></a>다음 단계
다음은 모든 Azure AD 암호 재설정 설명서 페이지에 대한 링크입니다.

* **로그인하는 데 문제가 있나요?** 그렇다면 [암호를 변경하고 재설정하는 방법은 다음과 같습니다](active-directory-passwords-update-your-own-password.md#reset-your-password).
* [**작동 방식**](active-directory-passwords-how-it-works.md) - 6개의 다양한 구성 요소 서비스 및 기능에 대해 알아봅니다.
* [**사용자 지정**](active-directory-passwords-customize.md) - 모양과 느낌 및 조직의 요구에 맞게 서비스의 동작을 사용자 지정하는 방법에 대해 알아봅니다
* [**모범 사례**](active-directory-passwords-best-practices.md) - 사용자의 조직에서 신속하게 배포하고 효과적으로 암호를 관리하는 방법에 대해 알아봅니다.
* [**정보 활용**](active-directory-passwords-get-insights.md) -우리의 통합된 보고 기능에 대해 알아봅니다
* [**FAQ**](active-directory-passwords-faq.md) -자주 묻는 질문에 답변합니다.
* [**문제해결**](active-directory-passwords-troubleshoot.md) -신속하게 서비스와의 문제를 해결하는 방법에 대해 알아봅니다.
* [**자세히 알아보기**](active-directory-passwords-learn-more.md) -서비스의 작동 원리 방식의 기술적 측면을 자세히 알아봅니다.

[001]: ./media/active-directory-passwords-getting-started/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-getting-started/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-getting-started/003.jpg "Image_003.jpg"
[004]: ./media/active-directory-passwords-getting-started/004.jpg "Image_004.jpg"
[005]: ./media/active-directory-passwords-getting-started/005.jpg "Image_005.jpg"
[006]: ./media/active-directory-passwords-getting-started/006.jpg "Image_006.jpg"
[007]: ./media/active-directory-passwords-getting-started/007.jpg "Image_007.jpg"
[008]: ./media/active-directory-passwords-getting-started/008.jpg "Image_008.jpg"
[009]: ./media/active-directory-passwords-getting-started/009.jpg "Image_009.jpg"
[010]: ./media/active-directory-passwords-getting-started/010.jpg "Image_010.jpg"
[011]: ./media/active-directory-passwords-getting-started/011.jpg "Image_011.jpg"
[012]: ./media/active-directory-passwords-getting-started/012.jpg "Image_012.jpg"
[013]: ./media/active-directory-passwords-getting-started/013.jpg "Image_013.jpg"
[014]: ./media/active-directory-passwords-getting-started/014.jpg "Image_014.jpg"
[015]: ./media/active-directory-passwords-getting-started/015.jpg "Image_015.jpg"
[016]: ./media/active-directory-passwords-getting-started/016.jpg "Image_016.jpg"
[017]: ./media/active-directory-passwords-getting-started/017.jpg "Image_017.jpg"
[018]: ./media/active-directory-passwords-getting-started/018.jpg "Image_018.jpg"
[019]: ./media/active-directory-passwords-getting-started/019.jpg "Image_019.jpg"
[020]: ./media/active-directory-passwords-getting-started/020.jpg "Image_020.jpg"
[021]: ./media/active-directory-passwords-getting-started/021.jpg "Image_021.jpg"
[022]: ./media/active-directory-passwords-getting-started/022.jpg "Image_022.jpg"
[023]: ./media/active-directory-passwords-getting-started/023.jpg "Image_023.jpg"
[024]: ./media/active-directory-passwords-getting-started/024.jpg "Image_024.jpg"
[025]: ./media/active-directory-passwords-getting-started/025.jpg "Image_025.jpg"
[026]: ./media/active-directory-passwords-getting-started/026.jpg "Image_026.jpg"
[027]: ./media/active-directory-passwords-getting-started/027.jpg "Image_027.jpg"
[028]: ./media/active-directory-passwords-getting-started/028.jpg "Image_028.jpg"
[029]: ./media/active-directory-passwords-getting-started/029.jpg "Image_029.jpg"
[030]: ./media/active-directory-passwords-getting-started/030.jpg "Image_030.jpg"
[031]: ./media/active-directory-passwords-getting-started/031.jpg "Image_031.jpg"
[032]: ./media/active-directory-passwords-getting-started/032.jpg "Image_032.jpg"

