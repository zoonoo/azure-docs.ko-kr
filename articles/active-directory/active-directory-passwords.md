<properties
	pageTitle="Azure AD 암호 재설정 | Microsoft Azure"
	description="암호 재설정, 변경, 암호 관리 보고 및 로컬 온-프레미스 Active Directory에 쓰기 저장을 포함한 Azure AD의 암호 관리 기능 설명."
	services="active-directory"
	documentationCenter=""
	authors="asteen"
	manager="femila"
	editor="curtand"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/12/2016"
	ms.author="asteen"/>


# 사용자와 관리자에 대한 Azure AD 암호 재설정

  >[AZURE.IMPORTANT] Azure 또는 O365 암호를 재설정하려고 하나요? 그렇다면 [이 섹션으로 건너뛰십시오](#users-how-to-manage-your-own-password).

셀프 서비스는 오랫동안 비용 절감 및 노동 절감 수단으로 전세계 IT 부서에서의 주요 목표가 되어 왔습니다. 실제로, 시장은 클라우드 또는 온-프레미스에서 온-프레미스 그룹, 암호 또는 사용자 프로필을 관리할 수 있는 제품으로 넘쳐납니다. Azure AD는 사용하기 가장 쉬운 방법 및 오늘날 가능한 가장 강력한 셀프서비스 기능을 제공하여 이들 제품과 차별을 두었습니다.

**Azure AD 암호 관리**는 정의한 보안 정책을 준수하는 동안 언제, 언제 어디에서든 모든 장치에서 암호를 관리할 수 있는 기능들입니다.

##사용자: 고유한 암호를 관리하는 방법
Office 365 또는 Microsoft 계정을 사용하는 조직에 있는 사용자(관리자 아님)가 작업 리소스에 액세스하려는 경우 암호 관련 일반적인 문제를 해결하는 방법을 알아보려면 아래 링크를 클릭합니다.

| 항목 | |
| --------- | --------- |
| 암호 재설정을 위해 등록하려는 경우 | [암호 재설정을 위해 등록하는 방법](active-directory-passwords-update-your-own-password.md#how-to-register-for-password-reset) |
| O365에서 암호를 변경하려는 경우 | [Office365에서 암호를 변경하는 방법](active-directory-passwords-update-your-own-password.md#how-to-change-your-password-from-o365) |
| Myapps.microsoft.com에서 암호를 변경하려는 경우 | [액세스 패널에서 암호를 변경하는 방법](active-directory-passwords-update-your-own-password.md#how-to-change-your-password-from-the-access-panel) |
| 내 암호를 잊어서 재설정하려는 경우 | [암호를 재설정하는 방법](active-directory-passwords-update-your-own-password.md#how-to-reset-your-password) |
| 로그인할 수 없어서 계정 잠금을 해제하려는 경우 | [온-프레미스 계정의 잠금을 해제하는 방법](active-directory-passwords-update-your-own-password.md#how-to-unlock-your-account) |
| 실패한 암호 재설정 문제를 해결하려는 경우 | [일반적인 문제 및 해결 방법](active-directory-passwords-update-your-own-password.md#common-problems-and-their-solutions) |

##관리자: Azure AD 암호 재설정을 시작하는 방법을 알아봅니다.
Azure AD 암호 재설정을 사용하도록 설정하려는 관리자이거나 더 알아보려는 경우 관심이 있는 내용을 보려면 아래 링크를 시작합니다.

| 항목 | |
| --------- | --------- |
| 지원되는 시나리오 | [Azure AD 암호 재설정으로 가능한 작업은 무엇입니까?](#what-is-possible-with-azure-ad-password-reset) |
| 사용하는 이유 | [Azure AD 암호 재설정을 사용하는 이유](#why-use-azure-ad-password-reset) |
| 가격 책정 및 가용성 | [가격 책정 및 가용성](#pricing-and-availability) |
| 암호 재설정 사용 | [사용자에 대해 암호 재설정 사용](#enable-password-reset-for-your-users) |
| 작동 방식을 사용자 지정 | [암호 재설정 동작 사용자 지정](#customize-password-reset-behavior) |
| 내 사용자에게 롤아웃 | [사용자를 구성하여 암호 재설정 사용](#configure-your-users-to-use-password-reset) |
| 보고서 보기 | [통합된 보고서를 사용하여 암호 재설정 작업 보기](#view-password-reset-activity-with-integrated-reports) |
| 사용자의 암호 다시 설정 | [사용자의 암호 관리](#manage-your-users-passwords) |
| 내 조직 암호 정책 설정 | [암호 정책 설정](#set-password-policies) |
| 문제 해결 | [문제 해결](#troubleshoot-a-problem) |
| FAQ | [FAQ 읽기](#read-a-faq) |
| 기술 세부 정보 | [기술 세부 정보 이해](#understand-the-technical-details) |
| 새로 출시된 기능 | [최근 서비스 업데이트](#recent-service-updates) |
| 기타 문서에 대한 링크 | [암호 재설정 설명서에 대한 링크](#links-to-password-reset-documentation) |

### Azure AD 암호 재설정으로 가능한 작업은 무엇입니까?
다음은 Azure AD의 암호 관리 기능으로 수행할 수 있는 몇가지 작업입니다.

- **셀프 서비스 암호 변경** 기능을 사용하여 최종 사용자나 관리자는 관리자에게 전화를 하거나 지원 센터에 문의하지 않고 만료되거나 만료되지 않은 암호를 변경할 수 있습니다.
- **셀프 서비스 암호 재설정** 기능을 사용하여 최종 사용자나 관리자는 관리자에게 전화를 하거나 지원 센터에 문의하지 않고도 자동으로 암호를 재설정할 수 있습니다. 셀프 서비스 암호 재설정 기능을 사용하려면 Azure AD Premium 또는 Basic이 필요합니다. 자세한 내용은 Azure Active Directory 버전을 참조하세요.
- **관리자가 시작한 암호 재설정** 기능을 사용하여 관리자는 [Azure 관리 포털](https://manage.windowsazure.com) 내에서 최종 사용자나 다른 관리자의 암호를 재설정할 수 있습니다.
- **암호 관리 작업 보고서**는 조직에서 발생하는 암호 재설정 및 등록 작업에서의 관리자 이해도를 높여줍니다.
- **암호 쓰기 저장** 기능을 사용하여 클라우드에서 온-프레미스 암호를 관리할 수 있으므로 위의 모든 시나리오에서 수행되거나 페더레이션 및 암호가 동기화된 사용자가 대신 수행할 수 있습니다. 암호 쓰기 저장 기능을 사용하려면 Azure AD Premium이 필요합니다. 자세한 내용은 Azure AD Premium 시작을 참조하세요.

### Azure AD 암호 재설정을 사용하는 이유
다음은 Azure AD의 암호 관리 기능을 사용해야 하는 몇가지 이유입니다.

- **비용 절감** -지원 기반 암호 재설정은 일반적으로 조직 IT 지출의 20%입니다.
- **사용자 환경 개선** -사용자가 자신의 암호를 잊어버릴 때마다 기술 지원팀과의 통화에 1시간을 보내고 싶어하지 않습니다.
- **적은 기술 지원팀** -암호 관리는 대부분의 조직에서 가장 큰 단일 기술 지원팀을 필요로 합니다.
- **이동성 사용** -사용자가 어디에서든 암호를 재설정할 수 있습니다

### 가격 책정 및 가용성
Azure AD 암호 재설정은 구독에 따라 3가지 계층으로 제공됩니다.

- **Azure AD Free** - 클라우드 전용 관리자는 해당하는 고유한 암호를 다시 설정할 수 있습니다
- **Azure AD 기본 또는 유료 O365 구독** - 클라우드 전용 사용자 및 클라우드 전용 관리자는 해당하는 고유한 암호를 다시 설정할 수 있습니다
- **Azure AD Premium** - 클라우드 전용, 페더레이션된 또는 암호 동기화된 사용자를 포함하여 사용자 또는 관리자는 해당하는 고유한 암호를 다시 설정할 수 있습니다.([암호 쓰기 저장 사용하도록 설정](active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords) 필요)

Azure AD Premium 또는 기본 가격에 대한 자세한 내용은 [Active Directory 가격 책정 세부 정보](https://azure.microsoft.com/pricing/details/active-directory/) 페이지를 방문합니다.

##사용자에 대해 암호 재설정 사용
| 항목 | |
| --------- | --------- |
| 클라우드 사용자를 위해 암호 재설정하려면 어떻게 해야 하나요? | [사용자가 클라우드 Azure Active Directory 암호를 재설정하도록 설정](active-directory-passwords-getting-started.md#enable-users-to-reset-their-azure-ad-passwords) |
| 온-프레미스 사용자에 대한 암호 재설정및 변경은 어떻게 해야 하나요? | [사용자가 온-프레미스 Active Directory 암호를 재설정하거나 변경하도록 설정](active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords) |
| 암호 재설정을 특정 사용자 집합으로 범위를 지정하려면 어떻게 해야 하나요? | [암호 재설정을 특정 사용자로 제한](active-directory-passwords-customize.md#restrict-access-to-password-reset) |
| 클라우드 암호 재설정을 테스트하려면 어떻게 해야 하나요? | [사용자로 Azure AD 암호 재설정](active-directory-passwords-getting-started.md#step-3-reset-your-azure-ad-password-as-a-user) |
| 온-프레미스 암호 재설정을 테스트하려면 어떻게 해야 하나요? | [사용자로 온-프레미스 AD 암호 재설정](active-directory-passwords-getting-started.md#step-5-reset-your-ad-password-as-a-user) |
| 나중에 암호 재설정을 비활성화하려면 어떻게 해야 하나요? | [설정: 암호 재설정이 설정된 사용자](active-directory-passwords-customize.md#users-enabled-for-password-reset) |


##암호 재설정 동작 사용자 지정
| 항목 | |
| --------- | --------- |
| 지원되는 인증 방법을 변경하려면 어떻게 해야 하나요? | [설정: 사용자에게 제공하는 인증 방법](active-directory-passwords-customize.md#authentication-methods-available-to-users) |
| 필수 인증 방법의 수를 변경하려면 어떻게 해야 하나요? | [설정: 필수 인증 방법의 수](active-directory-passwords-customize.md#number-of-authentication-methods-required) |
| 사용자 지정 보안 질문을 설정하려면 어떻게 해야 하나요? | [설정: 사용자 지정 보안 질문](active-directory-passwords-customize.md#custom-security-questions) |
| 미리 만든 보안 질문을 지역화하여 설정하려면 어떻게 해야 하나요? | [설정: 지식 기반 보안 질문](active-directory-passwords-customize.md#knowledge-based-security-questions) |
| 필요한 보안 질문 수를 변경하려면 어떻게 해야 하나요? | [설정: 등록 또는 재설정을 위한 보안 질문 수 ](active-directory-passwords-customize.md#number-of-questions-required-to-register) |
| 사용자가 관리자와 연락하는 방법을 사용자 지정하려면 어떻게 해야 하나요? | [설정: "관리자에게 문의" 링크 사용자 지정](active-directory-passwords-customize.md#customize-the-contact-your-administrator-link) |
| 사용자가 암호를 재설정하지 않고 AD 계정의 잠금을 해제할 수 있도록 하려면 어떻게 해야 하나요? | [설정: 사용자가 암호를 재설정하지 않고 해당 AD 계정의 잠금을 해제하도록 설정](active-directory-passwords-customize.md#allow-users-to-unlock-accounts-without-resetting-their-password) |
| 사용자에 대한 암호 재설정 알림을 사용하려면 어떻게 해야 하나요? | [설정: 암호가 재설정된 경우 사용자에게 알림](active-directory-passwords-customize.md#notify-users-and-admins-when-their-own-password-has-been-reset) |
| 관리자에 대한 암호 재설정 알림을 사용하려면 어떻게 해야 하나요? | [설정: 관리자가 자신의 암호를 재설정하는 경우 다른 관리자에게 알림](active-directory-passwords-customize.md#notify-admins-when-other-admins-reset-their-own-passwords) |
| 암호 재설정 모양과 느낌을 사용자 지정하려면 어떻게 해야 하나요? | [설정: 회사 이름, 브랜딩 및 로고 ](active-directory-passwords-customize.md#password-managment-look-and-feel) |


##사용자를 구성하여 암호 재설정 사용
| 항목 | |
| --------- | --------- |
| 암호 재설정을 위해 계정을 구성한 경우 어떻게 알 수 있나요? | [암호 재설정을 위해 계정을 구성하는 이유](active-directory-passwords-best-practices.md#what-makes-an-account-configured) |
| 사용자에게 암호 재설정을 구성하려면 어떻게 해야 하나요? | [사용자에게 암호 재설정 인증 데이터를 채우는 방법](active-directory-passwords-best-practices.md#ways-to-populate-authentication-data) |
| 사용자에게 데이터를 수동으로 업로드하려면 어떻게 해야 하나요? | [암호 재설정 데이터를 직접 업로드](active-directory-passwords-best-practices.md#uploading-data-yourself) |
| 사용자를 위해 데이터를 읽거나 설정하는 데 PowerShell을 사용하려면 어떻게 해야 하나요? | [사용자의 암호 재설정 데이터에 액세스하는 방법](active-directory-passwords-learn-more.md#how-to-access-password-reset-data-for-your-users) |
| 온-프레미스에서 암호 재설정 데이터를 동기화하려면 어떻게 해야 하나요? | [암호 재설정에서 사용되는 데이터](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset) |
| 등록할 사용자를 가져오고 암호 재설정을 사용하는 데 전자 메일 캠페인을 사용하려면 어떻게 해야 하나요? | [암호 재설정의 전자 메일 기반 롤아웃](active-directory-passwords-best-practices.md#email-based-rollout) |
| 로그인 시 사용자가 등록하도록 강제 적용하려면 어떻게 해야 하나요? | [암호 재설정의 강제 적용 등록 기반 롤아웃](active-directory-passwords-customize.md#require-users-to-register-when-signing-in) |
| 사용자가 등록을 주기적으로 다시 확인하도록 강제 적용하려면 어떻게 해야 하나요? | [설정: 사용자가 해당 인증 데이터를 다시 확인해야 하기 전의 일 수](active-directory-passwords-customize.md#number-of-days-before-users-must-confirm-their-contact-data) |
| 최종 사용자에게 암호 재설정을 통신하는 데 관한 모범 사례는 어떤 것인가요? | [사용자가 사용할 고유한 암호 포털 만들기](active-directory-passwords-best-practices.md#creating-your-own-password-portal) |


##통합된 보고서를 사용하여 암호 재설정 작업 보기
| 항목 | |
| --------- | --------- |
| 암호 재설정 보고서를 보려면 어디로 가야 하나요? | [암호 관리 보고서 개요](active-directory-passwords-get-insights.md#overview-of-password-management-reports) |
| 조직 내에서 사용자가 암호 재설정을 사용하는 방법을 어디서 확인할 수 있나요? | [암호 재설정 활동 보기](active-directory-passwords-get-insights.md#view-password-reset-activity) |
| 등록하는 사용자가 얼마나 많은지 및 등록한 것이 무엇인지를 어디서 확인할 수 있나요? | [암호 재설정 등록 활동 보기](active-directory-passwords-get-insights.md#view-password-reset-registration-activity) |
| API에서 암호 재설정 보고서는 어떻게 얻을 수 있나요? | [Azure AD 응용 프로그램을 만들어 보고 API에 액세스](active-directory-reporting-api-getting-started.md#creating-an-azure-ad-application-to-access-the-api) |
| API를 통해 어떤 종류의 암호 재설정 보고 정보를 사용할 수 있나요? | [보고 API에서 사용할 수 있는 암호 재설정 및 등록 이벤트](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-reports-and-events-preview#SsprActivityEvent) |


##사용자의 암호 관리
| 항목 | |
| --------- | --------- |
| O365 관리 포털에서 사용자의 암호를 재설정하려면 어떻게 해야 하나요? | [Office 365에서 사용자의 암호 다시 설정](https://support.office.com/article/Reset-a-user-s-password-7A5D073B-7FAE-4AA5-8F96-9ECD041ABA9C) |
| PowerShell을 사용하여 사용자의 암호를 재설정하려면 어떻게 해야 하나요? | [Set-MsolUserPassword를 사용하여 사용자의 암호 재설정](https://msdn.microsoft.com/library/azure/dn194140.aspx) |


##암호 정책 설정
| 항목 | |
| --------- | --------- |
| Office 365에서 조직 암호 만료 정책을 설정하려면 어떻게 해야 하나요? | [암호 만료 정책 설정](https://support.office.com/article/Set-a-user-s-password-expiration-policy-0f54736f-eb22-414c-8273-498a0918678f) |
| PowerShell을 사용하여 특정 사용자의 암호를 만료되지 않도록 설정하려면 어떻게 해야 하나요? | [PowerShell을 사용하여 개별 사용자의 암호를 만료되지 않도록 설정](https://support.office.com/article/Set-an-individual-user-s-password-to-never-expire-f493e3af-e1d8-4668-9211-230c245a0466) |
| PowerShell을 사용하여 사용자의 암호가 만료되지 않도록 설정되었는지 확인하려면 어떻게 해야 하나요? | [PowerShell을 사용하여 개별 사용자의 암호 만료 상태 확인](https://support.office.com/article/Set-an-individual-user-s-password-to-never-expire-f493e3af-e1d8-4668-9211-230c245a0466#__toc378845827) |


##문제 해결
| 항목 | |
| --------- | --------- |
| 도움이 필요한 경우 지원받기 위해 어떤 정보를 제공해야 하나요? | [도움이 필요한 경우 포함할 정보](active-directory-passwords-troubleshoot.md#information-to-include-when-you-need-help) |
| 암호 재설정 관련 문제를 해결하려면 어떻게 할 수 있나요? | [암호 재설정 포털에서 문제 해결](active-directory-passwords-troubleshoot.md#troubleshoot-the-password-reset-portal) |
| 비밀번호 쓰기 저장 관련 문제를 해결하려면 어떻게 할 수 있나요? | [비밀번호 쓰기 저장 문제 해결](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback) |
| 비밀번호 쓰기 저장 연결 관련 문제를 해결하려면 어떻게 할 수 있나요? | [비밀번호 쓰기 저장 연결 문제 해결](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity) |
| 암호 재설정 구성 관련 문제를 해결하려면 어떻게 할 수 있나요? | [Azure 관리 포털에서 암호 재설정 구성 문제 해결](active-directory-passwords-troubleshoot.md#troubleshoot-password-reset-configuration-in-the-azure-management-portal) |
| 암호 재설정 보고 관련 문제를 해결하려면 어떻게 할 수 있나요? | [Azure 관리 포털에서 암호 관리 보고서에 문제 해결](active-directory-passwords-troubleshoot.md#troubleshoot-password-management-reports-in-the-azure-management-portal) |
| 암호 재설정 등록 관련 문제를 해결하려면 어떻게 할 수 있나요? | [암호 재설정 등록 포털에서 문제 해결](active-directory-passwords-troubleshoot.md#troubleshoot-the-password-reset-registration-portal) |
| 비밀번호 쓰기 저장 이벤트 로그 오류 코드 | [비밀번호 쓰기 저장 이벤트 로그 오류 코드](active-directory-passwords-troubleshoot.md#password-writeback-event-log-error-codes) |


##FAQ 읽기
| 항목 | |
| --------- | --------- |
| 암호 재설정 등록에 대한 FAQ를 읽으려는 경우 | [암호 재설정 등록 FAQ](active-directory-passwords-faq.md#password-reset-registration) |
| 암호 재설정에 대한 FAQ를 읽으려는 경우 | [암호 재설정 FAQ](active-directory-passwords-faq.md#password-reset) |
| 암호 재설정 보고에 대한 FAQ를 읽으려는 경우 | [암호 관리 보고서 FAQ](active-directory-passwords-faq.md#password-management-reports) |
| 비밀번호 쓰기 저장에 대한 FAQ를 읽으려는 경우 | [암호 쓰기 저장 FAQ](active-directory-passwords-faq.md#password-writeback) |


##기술 세부 정보 이해

| 항목 | |
| --------- | --------- |
| 비밀번호 쓰기 저장의 정의에 대해 알아보려는 경우 | [암호 쓰기 저장 개요](active-directory-passwords-learn-more.md#password-writeback-overview) |
| 비밀번호 쓰기 저장의 작동 원리에 대해 알아보려는 경우 | [암호 쓰기 저장의 작동 원리](active-directory-passwords-learn-more.md#how-password-writeback-works) |
| 비밀번호 쓰기 저장이 어떤 시나리오를 지원하는지 알아보려는 경우 | [암호 쓰기 저장에 지원되는 시나리오](active-directory-passwords-learn-more.md#scenarios-supported-for-password-writeback) |
| 비밀번호 쓰기 저장의 보호 원리에 대해 알아보려는 경우 | [암호 쓰기 저장 보안 모델](active-directory-passwords-learn-more.md#password-writeback-security-model) |
| 암호 재설정 포털의 작동 원리에 대해 알아보려는 경우 | [암호 재설정 포털의 작동 원리](active-directory-passwords-learn-more.md#how-does-the-password-reset-portal-work) |
| 암호 재설정에서 사용되는 데이터에 대해 알아보려는 경우 | [암호 재설정에서 사용되는 데이터](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset) |

## 최근 서비스 업데이트

####Office 365 앱에 로그인 시 암호 재설정 등록 적용 - 2015년 11월

- 이제 [적용된 등록](active-directory-passwords-customize.md#require-users-to-register-when-signing-in) 기능을 사용하도록 설정한 후에 사용자는 회사 또는 학교 계정을 사용하여 로그인하는 위치를 등록해야 합니다. 이는 대부분의 조직에서 암호 재설정에 등록할 수 있는 속도를 크게 증가시킵니다. 이 새로운 기능을 사용하여 2주 만에 대규모 조직의 등록을 확인했습니다.

####암호 재설정 없이 잠금 해제한 Active Directory 계정에 대한 지원 - 2015년 11월

- 요즘은 (재설정 없이) 잠금 해제만해도 엄청난 기술 지원 드라이버입니다. 사실 대부분의 조직에서는 계정을 잠금 해제하는 데 암호 재설정 예산의 최대 70%를 소비합니다. 이러한 요구를 충족하려면 이제 사용자가 Azure AD 암호 재설정을 사용하여 암호 재설정에서 별도로 AD 계정을 잠금 해제하는 기능을 설정할 수 있습니다. 다음에서 설정하는 방법을 확인합니다. [설정: 사용자가 암호를 재설정하지 않고 해당 AD 계정의 잠금을 해제하도록 설정](active-directory-passwords-customize.md#allow-users-to-unlock-accounts-without-resetting-their-password)

####등록 페이지에 사용 편의성 업데이트 - 2015년 10월

- 이제 사용자가 이미 등록된 데이터를 가진 경 "올바름"을 클릭하여 메일 또는 전화 통화를 다시 보낼 필요 없이 데이터를 업데이트할 수 있습니다.

####비밀번호 쓰기 저장의 안정성 향상 - 2015년 9월

- Azure AD Connect의 9월 버전으로 비밀번호 쓰기 저장 에이전트는 이제 자세한 연결 및 추가적으로 더욱 강력해진 장애 조치 기능을 더 공격적으로 다시 시도합니다.

####암호 재설정 보고 데이터를 검색하기 위한 API - 2015년 8월

- 이제 암호 재설정 보고 관련 데이터는 [Azure AD 보고서 및 이벤트 API](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent)에서 직접 검색할 수 있습니다.

####클라우드 도메인 가입 시에 Azure AD 암호 재설정에 대한 지원 - 2015년 8월

- 이제 클라우드 사용자는 클라우드 도메인 가입 온보딩 환경 동안 Windows 10 로그인 화면에서 암호를 재설정할 수 있습니다. Windows 10 로그인 화면에서 아직 노출되지 않습니다.

####Azure 및 페더레이션된 앱에 로그인 시 암호 재설정 등록 적용 - 2015년 7월

- 이제 myapps.microsoft.com에 로그인할 때 등록을 적용하는 것 외에도 Azure 관리 포털 및 응용 프로그램에서 페더레이션된 Single Sign-On에 로그인하는 동안 등록을 적용하도록 지원합니다.

####보안 질문 지역화 지원 - 2015년 5월

- 이제 암호 재설정을 위해 보안 질문을 구성할 때 설정한 전체 O365 언어로 지역화된 미리 정의된 보안 질문을 선택하는 옵션이 있습니다.

####암호 재설정 시 계정 잠금 해제 지원 - 2015년 6월

- 비밀번호 쓰기 저장을 사용하고 계정이 잠겼을 때 암호를 재설정하는 경우 Active Directory 계정의 잠금을 자동으로 해제합니다.

####SSPR 등록 브랜드 - 2015년 4월

- 암호 재설정 등록 페이지는 이제 회사 로고로 브랜드 지정됩니다!

####보안 질문 - 2015년 3월

- GA에 대한 보안 질문이 출시되었습니다!

####계정 잠금해제 - 2015년 3월

- 이제 암호가 재설정되면 사용자는 계정을 잠금 해제할 수 있습니다.

## 서비스 예정

지금 작업 중인 멋진 기능의 일부는 다음과 같습니다.

**사용자를 상기시켜 로그인 시 등록된 해당 데이터를 업데이트하도록 지원** - 진행 중인 작업

- 오늘날 myapps.microsoft.com에 액세스할 때 등록된 해당 데이터를 업데이트하도록 사용자를 상기하도록 지원하지만 모든 로그인에 해당 작업을 수행할 수 있는 기능을 위해 최선을 다하고 있습니다.

## 암호 재설정 설명서에 대한 링크
다음은 모든 Azure AD 암호 재설정 설명서 페이지에 대한 링크입니다.

* **로그인하는 데 문제가 있나요?** 그렇다면 [암호를 변경하고 재설정하는 방법은 다음과 같습니다](active-directory-passwords-update-your-own-password.md).
* [**작동 방식**](active-directory-passwords-how-it-works.md) - 6개의 다양한 구성 요소 서비스 및 기능에 대해 알아봅니다.
* [**시작**](active-directory-passwords-getting-started.md) -사용자가 클라우드 또는 온-프레미스 암호를 다시 설정하고 변경할 수 있는 방법에 대해 알아봅니다.
* [**사용자 지정**](active-directory-passwords-customize.md) - 모양과 느낌 및 조직의 요구에 맞게 서비스의 동작을 사용자 지정하는 방법에 대해 알아봅니다
* [**모범 사례**](active-directory-passwords-best-practices.md) - 사용자의 조직에서 신속하게 배포하고 효과적으로 암호를 관리하는 방법에 대해 알아봅니다.
* [**정보 활용**](active-directory-passwords-get-insights.md) -우리의 통합된 보고 기능에 대해 알아봅니다
* [**FAQ**](active-directory-passwords-faq.md) -자주 묻는 질문에 답변합니다.
* [**문제해결**](active-directory-passwords-troubleshoot.md) -신속하게 서비스와의 문제를 해결하는 방법에 대해 알아봅니다.
* [**자세히 알아보기**](active-directory-passwords-learn-more.md) -서비스의 작동 원리 방식의 기술적 측면을 자세히 알아봅니다.

<!---HONumber=AcomDC_0713_2016-->