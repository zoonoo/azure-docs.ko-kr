<properties
	pageTitle="Azure Active Directory란?"
	description="Azure Active Directory를 사용하여 기존 온-프레미스 ID를 클라우드로 확장하거나 Azure AD 통합 응용 프로그램을 개발할 수 있습니다."
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="terrylan"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/11/2015"
	ms.author="curtand"/>


# Azure Active Directory란?


[작동 원리](active-directory-works.md)<br> [시작](active-directory-get-started.md)<br> [다음 단계](active-directory-next-steps.md)<br> [자세한 정보](active-directory-learn-map.md)

Azure AD(Azure Active Directory)는 기업에서 Active Directory의 유연성과 보안을 그대로 유지하면서도 온-프레미스 투자를 확장하여 클라우드 서비스를 활용할 수 있는 간편한 방법을 제공합니다. 클라우드 플랫폼이 기존의 ID 인프라를 확장하여 클라우드 시대의 문제를 해결해 주기 때문에 고객은 아무 것도 이동할 필요가 없습니다. 고객은 기존 투자 및 온-프레미스 기능을 계속 활용하는 한편 Azure AD를 활용하여 클라우드에서 ID 및 액세스를 관리할 수 있습니다.

Azure AD를 사용하면 중앙에서 응용 프로그램 및 리소스에 대한 액세스를 제어하고, 손쉽게 기존 리소스(클라우드 서비스 또는 온-프레미스 응용 프로그램)를 추가하고, 개발하는 용 프로그램을 통합할 수 있습니다. 사용자는 셀프 서비스 기능을 사용하고 모든 응용 프로그램에 Single Sign-On 액세스가 가능하므로 응용 프로그램의 위치 또는 별도의 암호를 기억할 필요가 없습니다. 현업 부서는 비즈니스 파트너, 공급업체 및 비즈니스 고객을 SharePoint 사이트로 초대하거나 이들의 SharePoint 사이트에 참가하여 클라우드에서 공동으로 작업할 수 있습니다.

Azure AD를 사용하면 한 장소에서 모든 것을 관리하고 이러한 제어 기능을 클라우드와 온-프레미스 모두에 반영할 수 있습니다. 관리 업무가 중복되지 않습니다. Azure AD는 기존 인프라에 바로 연결됩니다. [자세히 알아봅니다](active-directory-aadconnect.md).




> [AZURE.NOTE]Azure Active Directory를 사용하려면 Azure 계정이 필요합니다. 계정이 없으면 [무료 Azure 계정을 등록](http://azure.microsoft.com/pricing/free-trial/)할 수 있습니다.


## Azure Active Directory는 어떤 장점이 있나요?

Azure AD는 포괄적인 서비스이므로 조직 내 다양한 역할 구성원에게 다양한 이점을 제공합니다.

- 비즈니스 의사 결정권자의 경우 거버넌스 요구 사항이 충족된다는 확신을 가지고 Azure AD를 사용하여 클라우드 응용 프로그램 및 모바일 인력의 약속을 이행할 수 있습니다.
- 서비스 공급자의 경우 Azure AD를 사용하여 Microsoft Azure 및 Office 365 고객에게 연결하는 한편, 고객의 기존 ID 솔루션에 서비스를 연결하여 ID 및 액세스 요구 사항을 쉽게 해결할 수 있습니다. 또한 Azure AD는 모든 백오피스 액세스 요구 사항을 해결할 수 있습니다. 따라서 내부 구성원이든 외부 파트너든 올바른 사람에게 적합한 액세스 권한을 부여할 수 있습니다.
- IT 전문가의 경우 Azure AD를 사용하여 "클라우드 속도"로 작업을 제어하고 표시할 수 있습니다. Azure AD를 사용하면 사람들이 무엇을 사용하는지 확인하고 셀프 서비스로 이들의 역량을 강화할 수 있습니다.<br> <br>

![][1]

##Azure AD에 대해 알아야 할 9가지

### 어디서나 Active Directory 암호 관리

Azure Active Directory를 사용하여 어디서나 모든 장치에서 자신의 Active Directory 또는 Azure Active Directory 암호를 관리할 수 있도록 사용자의 역량을 강화할 수 있습니다. 관리자는 암호 및 알림 정책을 관리하고, 암호 재설정 작업이 발생하는 순간 암호 재설정 작업의 자세한 감사 활동을 볼 수 있습니다. [Azure AD 암호 관리에 대한 자세한 정보](http://aka.ms/ssproverview)

### 클라우드 리소스에 대한 액세스 규칙 설정

누가 어떤 조건에서 클라우드 응용 프로그램 및 리소스에 액세스할 수 있는지를 제어하는 규칙 및 정책을 설정할 수 있습니다. 예를 들어 MFA(다단계 인증)를 요구하고, 장치 또는 위치에 따라 액세스를 관리할 수 있습니다. [Azure MFA에 대해 자세히 알아보세요](../multi-factor-authentication.md).

### 모든 응용 프로그램에 Single Sign-On

Azure Active Directory는 Microsoft Office 365와 Salesforce, Workday, DocuSign, ServiceNow, Box 등의 SaaS 응용 프로그램 수천 개를 포함한 온-프레미스 응용 프로그램 및 클라우드에 대한 안전한 Single Sign-On을 제공합니다. [SaaS 앱에 대해 자세히 알아보세요](http://azure.microsoft.com/marketplace/active-directory/).

### 모든 장치에서 작동

사용자는 개인 설정된 웹 기반 액세스 패널, 모바일 앱, Office 365 및 기존 작업 자격 증명을 사용하는 사용자 지정 회사 포털에서 응용 프로그램을 실행할 수 있으며 iOS, Mac OS X, Android, Windows 장치 등 어디서 작업하든 동일한 환경이 제공됩니다.

### 외부 액세스에 대한 규칙 설정

외부 사용자는 기본 제공 응용 프로그램 프록시를 사용하여 방화벽으로 보호되는 온-프레미스 네트워크에 안전하게 연결할 수 있습니다. 다시 작성하거나 인터넷에 직접 노출할 필요 없이 기본 제공 응용 프로그램 프록시를 사용하여 다단계 인증을 비롯하여 사용자가 설정한 모든 규칙 및 정책을 클라우드 응용 프로그램 또는 기존 온-프레미스 응용 프로그램에 대한 액세스에 적용할 수 있습니다. [Azure AD 응용 프로그램 프록시에 대해 자세히 알아보세요](https://msdn.microsoft.com/library/azure/dn768219.aspx).

### 사용자의 액세스를 모니터링

마지막으로 Azure AD는 조직의 현재 상태에 대한 정보를 제공합니다. 고급 보고 및 통계를 통해 사용자 액세스에 대한 고유한 정보를 얻을 수 있습니다. 예를 들어 응용 프로그램 검색을 사용하여 조직에서 활발하게 사용되는 응용 프로그램을 확인할 수 있습니다. [Azure AD 클라우드 앱 검색에 대해 자세히 알아보세요](https://appdiscovery.azure.com/).

### 고급 보고, 감사 및 분석
Azure AD는 관리자를 위해 다양한 일일 사용 현황 및 액세스 보고서([Azure AD 보고서](active-directory-view-access-usage-reports.md))를 제공하며, 보고 API([Azure AD 보고 API](active-directory-reporting-api-getting-started.md))를 사용하여 사용자 지정 보고 및 자세한 데이터 분석이 가능합니다. Azure AD 유료 버전은 권한 있는 작업 감사([Azure AD 감사](active-directory-view-access-usage-reports.md))를 포함하여 훨씬 다양한 보고서가 제공됩니다.

### 모든 사용자를 위한 간단하고 안전한 환경

예: 최종 사용자는 전문화된 교육 없이도 프로필, 응용 프로그램, 리소스에 대한 액세스 관리 기능 등을 한 곳에서 활용할 수 있는 단순한 환경을 즐길 수 있습니다. 다단계 인증, SaaS 응용 프로그램, 하이브리드 도구 및 셀프 서비스 기능은 즉시 사용할 수 있습니다.

- 관리자는 Azure AD 관리 포털 및 Windows PowerShell에 액세스하여 포괄적인 관리를 수행할 수 있습니다.

- 개발자는 일관성 있는 RESTful API 집합을 활용하고, 게시 및 소비 응용 프로그램 인터페이스에 쉽게 액세스할 수 있습니다.

### 원하는 버전 선택

Azure AD는 세 가지 버전으로 제공됩니다.

- Free 버전은 단순한 클라우드 디렉터리 서비스입니다.
- Basic 버전은 SaaS 앱에도 액세스할 수 있는 클라우드 디렉터리 서비스입니다.
- Premium 버전은 포괄적인 규칙 기반의 셀프서비스 관리 디렉터리 서비스 솔루션입니다.

여기에 설명된 기능을 체크아웃하려면 [무료 Azure 평가판](http://azure.microsoft.com/trial/get-started-active-directory/)을 활성화하세요.

[Azure AD 버전에 대한 자세한 정보](active-directory-editions.md)


## 추가 리소스

* [조직으로 Azure 등록](sign-up-organization.md)
* [Azure ID](fundamentals-identity.md)

<!--Image references-->
[1]: ./media/active-directory-whatis/Azure_Active_Directory.png

<!---HONumber=58--> 