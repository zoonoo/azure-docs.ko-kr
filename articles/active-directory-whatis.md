<properties 
	pageTitle="Azure Active Directory란?" 
	description="Microsoft에서 뛰어난 확장성, 고가용성 및 통합 재해 복구 기능을 제공하는 클라우드에서 Active Directory를 실행하는 동안 Azure Active Directory를 사용하여 기존 온-프레미스 ID를 클라우드로 확장해 관리자 및 최종 사용자 환경을 향상시킬 수 있습니다. 또는 사용자의 조직이나 다른 조직을 위해 Azure AD 통합 응용 프로그램을 개발할 수 있습니다." 
	services="active-directory" 
	documentationCenter="" 
	authors="Justinha" 
	manager="terrylan" 
	editor="LisaToft"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="hero-article" 
	ms.date="03/03/2015" 
	ms.author="justinha"/>


# Azure Active Directory란?

Azure Active Directory는 모든 ID 및 액세스 관리 요구 사항을 조명을 켜는 것처럼 손쉽게 실현하도록 해주는 서비스입니다. Azure AD를 사용하면 클라우드에서 개인 ID 디렉터리를 만들 수 있습니다. Windows Server Active Directory와 마찬가지로 Azure AD는 응용 프로그램 및 리소스에 대한 액세스를 중앙에서 제어할 수 있도록 해줍니다. 기존 리소스(클라우드 서비스 또는 온-프레미스 응용 프로그램)를 쉽게 추가하는 것은 물론, 개발 중인 응용 프로그램을 쉽게 통합할 수 있습니다. Azure AD를 사용하면 사용자가 자신의 모든 응용 프로그램에 SSO(Single Sign-On) 액세스할 수 있습니다. 따라서 각 앱을 찾을 위치를 몰라도 되며, 개별 암호를 기억할 필요도 없습니다.

Azure AD는 독립 실행형 클라우드 디렉터리로 사용하거나, 클라우드에 있는 온-프레미스 ID 및 액세스 관리 솔루션을 확장하는 데 사용할 수 있습니다. 이 경우 기존 투자 및 온-프레미스 기능을 계속 활용하면서 Azure AD를 통해 클라우드에서 ID 및 액세스를 관리할 수 있습니다. [자세한 정보](http://msdn.microsoft.com/library/jj573653)

또한 액세스 권한이 있는 사람 및 관련 조건을 제어하는 규칙 및 정책을 설정할 수 있습니다. 예를 들어 MFA(다단계 인증)를 요구하고, 장치 또는 위치에 따라 액세스를 관리할 수 있습니다. [Azure MFA에 대한 자세한 정보](http://azure.microsoft.com/services/multi-factor-authentication/)

다시 작성하거나 인터넷에 직접 노출할 필요 없이 기본 제공 응용 프로그램 프록시를 사용하여 MFA를 비롯한 이 모든 규칙 및 정책을 클라우드 응용 프로그램 또는 기존 온-프레미스 응용 프로그램에 대한 액세스에 적용할 수 있습니다. [Azure AD 응용 프로그램 프록시에 대한 자세한 정보](https://msdn.microsoft.com/library/azure/dn768219.aspx)

마지막으로 Azure AD는 조직의 현재 상태에 대한 정보를 제공합니다. 고급 보고 및 통계를 통해 사용자 액세스에 대한 고유한 정보를 얻을 수 있습니다. 예를 들어 응용 프로그램 검색을 사용하여 조직에서 활발하게 사용되는 응용 프로그램을 확인할 수 있습니다. [Azure AD 클라우드 앱 검색에 대한 자세한 정보](https://appdiscovery.azure.com/)

Azure AD는 이처럼 포괄적인 서비스이므로 조직 내 구성원에 따라 다양한 이점을 제공합니다.

- 비즈니스 의사 결정권자의 경우 Azure AD는 모바일 인력의 약속을 이행하고, 거버넌스 요구 사항이 충족된다는 확신을 가지고 클라우드 응용 프로그램의 기능을 활용할 수 있도록 해줍니다.
- 서비스 공급자의 경우 Azure AD는 Microsoft Azure 및 Office 365 고객에게 연결할 수 있도록 하는 한편, 고객의 기존 ID 솔루션에 서비스를 연결하여 모든 ID 및 액세스 요구 사항을 쉽게 해결할 수 있도록 해줍니다. Azure AD는 기업 및 소비자가 한 곳에서 솔루션을 쉽게 조정할 수 있도록 해줍니다. 또한 Azure AD는 모든 백오피스 액세스 요구 사항을 해결할 수 있습니다. 내부 구성원이든 외부 파트너든 올바른 사람에게 적합한 액세스 권한을 부여할 수 있습니다.
- IT 전문가의 경우 Azure AD는 클라우드의 속도 및 민첩성을 실현하는 동시에 작업에 대한 제어 및 가시성을 향상시킵니다. Azure AD를 통해 사람들이 무엇을 사용하는지 확인하고 셀프 서비스로 이들의 역량을 강화할 수 있습니다.

Azure AD를 사용할 때는 모든 것을 미리 통합하는 것이 가장 좋습니다. 다단계 인증, SaaS 응용 프로그램, 하이브리드 도구 및 셀프 서비스 기능은 즉시 사용할 수 있습니다. 관리자는 Azure AD 관리 포털 및 Windows PowerShell에 액세스하여 포괄적인 관리를 수행할 수 있습니다. 개발자는 일관성 있는 RESTful API 집합을 활용하고, 게시 및 소비 응용 프로그램 인터페이스에 쉽게 액세스할 수 있습니다. 최종 사용자는 전문화된 교육 없이도 프로필, 응용 프로그램, 리소스에 대한 액세스 관리 기능 등을 한 곳에서 활용할 수 있는 단순한 환경을 즐길 수 있습니다.

Azure AD에는 응용 프로그램에 액세스할 수 있는 클라우드 디렉터리부터 포괄적인 규칙 기반 셀프 서비스 관리 솔루션에 이르기까지 다양한 기능 수준이 있습니다. Azure AD는 누구나 무료로 이용할 수 있습니다. 지금 Azure 평가판에 등록하세요. 여러분의 디렉터리가 기다리고 있습니다. 디렉터리는 구입 여부에 상관없이 여러분의 것입니다. 여기에 설명된 모든 기능을 활용하려면 Azure AD 프리미엄 평가판을 활성화하세요. [Azure AD 에디션에 대한 자세한 정보](https://msdn.microsoft.com/library/azure/dn532272.aspx)


**추가 리소스**

* [조직으로 Azure 등록](/manage/services/identity/organizational-account/)
* [Azure ID](/manage/windows/fundamentals/identity/)
* [MSDN의 Azure AD 라이브러리](http://go.microsoft.com/fwlink/?LinkId=293425)

<!--HONumber=47-->
