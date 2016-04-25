<properties
    pageTitle="Azure Active Directory란?"
    description="Azure Active Directory를 사용하여 기존 온-프레미스 ID를 클라우드로 확장하거나 Azure AD 통합 응용 프로그램을 개발할 수 있습니다."
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="stevenpo"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="04/07/2016"
    ms.author="markusvi"/>


# Azure Active Directory란?





Azure Active Directory(Azure AD)는 Microsoft의 다중 테넌트 클라우드 기반 디렉터리 및 ID 관리 서비스입니다.

Azure AD는 IT 관리자를 위해 직원 및 비즈니스 파트너에게 Office365, Salesforce.com, DropBox, Concur와 같은 [수천 개의 SaaS 응용 프로그램 클라우드](http://blogs.technet.com/b/ad/archive/2014/09/03/50-saas-apps-now-support-federation-with-azure-ad.aspx)에 대한 SSO(Single Sign-On) 액세스 권한을 부여하는 사용하기 편리한 솔루션을 제공합니다.

응용 프로그램 개발자는 Azure AD를 사용하여 전 세계 수백 만 조직에서 사용하는 클래스 ID 관리 솔루션과 빠르고 쉽게 통합할 수 있도록 함으로써 응용 프로그램을 구축하는 데 집중할 수 있습니다.

Azure AD에는 다단계 인증, 장치 등록, 셀프 서비스 암호 관리, 셀프서비스 그룹 관리, 권한 있는 계정 관리, 역할 기반 액세스 제어, 응용 프로그램 사용 모니터링, 광범위한 감사 및 보안 모니터링 및 경고를 포함하는 완전한 ID 관리 기능이 포함되어 있습니다. 이러한 기능을 통해 클라우드 기반 응용 프로그램을 보호하고, IT 프로세스를 간소화하고, 비용을 절감하고, 회사의 규정 준수 목표를 충족할 수 있도록 합니다.

또한 [네 번 클릭](http://blogs.technet.com/b/ad/archive/2014/08/04/connecting-ad-and-azure-ad-only-4-clicks-with-azure-ad-connect.aspx)으로 Azure AD를 기존 Windows Server Active Directory와 통합하여 기존 온-프레미스 ID 투자를 활용하여 클라우드를 기반으로 하는 SaaS 응용 프로그램에 대한 액세스를 관리하는 기능을 조직에 제공할 수 있습니다.

Office365, Azure 또는 Dynamics CRM Online 고객인 경우 이미 Azure AD를 사용하고 있다는 것을 알지 못할 수도 있습니다. 모든 Office365, Azure 및 Dynamics CRM 테넌트는 실제로 이미 Azure AD 테넌트입니다. 원할 때마다 해당 테넌트를 시작하여 Azure AD와 통합할 수천 개의 다른 클라우드 응용 프로그램에 대한 액세스를 관리할 수 있습니다.





<center>![Azure AD Connect Stack](./media/active-directory-whatis/Azure_Active_Directory.png) </center>


## Azure AD가 안정적입니까?

Azure AD의 다중 테넌트, 지리적으로 분산된 고가용성 디자인은 가장 중요한 비즈니스 요구에 의존할 수 있음을 의미합니다. 자동 장애 조치로 전세계 28개 데이터 센터를 실행 중이므로 Azure AD가 안전성이 뛰어나고, 데이터 센터의 작동이 중단되더라도 디렉터리 데이터 복사본이 두 개 이상의 지역에 있는 데이터 센터에 분산되어 있으며 즉시 액세스할 수 있다는 점을 미루어 볼 때 안전성을 신뢰할 수 있습니다.

자세한 내용은 [서비스 수준 계약](https://azure.microsoft.com/support/legal/sla/)을 참조하세요.



## Azure AD의 장점은 무엇입니까?

조직에서 Azure AD를 사용하면 여러 가지 방법으로 직원의 생산성을 향상시키고, IT 프로세스를 간소화하고, 보안을 개선하고, 비용을 절감할 수 있습니다.

-	신속하게 클라우드 서비스를 채택하여 Azure AD에서 자동화된 SaaS 앱 액세스 관리 및 프로비저닝 서비스 기능에서 제공하는 간단한 Single Sign-On 환경을 직원과 파트너에게 제공합니다.
-	직원에게 전 세계 클라우드 앱과 서비스에 액세스할 수 있는 권한과 사용 장치에서 작업할 수 있는 셀프 서비스 기능을 제공합니다.
-	회사의 소셜 미디어 계정에 대한 직원 및 공급 업체 액세스를 쉽고 안전하게 관리합니다.
-	Azure AD 다단계 인증 및 조건부 액세스를 사용하여 응용 프로그램 보안을 개선합니다.
-	일관된 셀프서비스 응용 프로그램 액세스 관리를 구현하여 비즈니스 소유자가 IT 비용 및 오버헤드를 줄이면서 신속하게 이동할 수 있도록 합니다.
-	응용 프로그램 사용을 모니터링하고 보안 보고 및 모니터링을 통해 비즈니스를 고급 위협으로부터 보호합니다.
-	온-프레미스 응용 프로그램에 대한 모바일(원격) 액세스를 보호합니다.






## 어떻게 시작하나요?
-	IT 관리자인 경우:
 - [사용해 보기](https://azure.microsoft.com/trial/get-started-active-directory/) - 지금 무료 30일 평가판에 등록하면 이 링크를 사용하여 5분 내에 첫 번째 클라우드 솔루션을 배포할 수 있습니다.
 - Azure AD 테넌트 시작 및 빠른 실행에 대한 팁과 요령에 대해서는 "Azure AD 시작"을 읽어 보세요.
-	개발자인 경우:
 - Azure Active Directory에 대한 [개발자 가이드](active-directory-developers-guide.md)를 확인하세요. 
 - [평가판 시작](https://azure.microsoft.com/trial/get-started-active-directory/) – 지금 무료 30일 평가판을 등록하고 Azure AD와 앱 통합을 시작하세요.



## 자세한 내용을 알아보려면 어떤 정보를 참조해야 하나요?

Azure AD에 대한 자세한 내용을 알아볼 수 있도록 수많은 리소스를 온라인으로 제공합니다. 다음은 시작에 대한 기사 목록입니다.


- [Azure AD Connect를 사용하여 하이브리드 관리에 디렉터리 사용](active-directory-aadconnect.md)

- [연결된 적이 있는 세계에 대한 추가 보안](../multi-factor-authentication/multi-factor-authentication.md)

- [Azure Active Directory를 사용하여 SaaS 응용 프로그램의 사용자를 자동으로 프로비저닝 및 프로비저닝 해제](active-directory-saas-app-provisioning.md)

- [Azure AD Reporting 시작](active-directory-reporting-getting-started.md)

- [어디에서나 암호 관리](active-directory-passwords.md)

- [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On이란 무엇입니까?](active-directory-appssoaccess-whatis.md)

- [Azure Active Directory를 사용하여 SaaS 응용 프로그램의 사용자를 자동으로 프로비저닝 및 프로비저닝 해제](active-directory-saas-app-provisioning.md)

- [온-프레미스 응용 프로그램에 보안된 원격 액세스를 제공하는 방법](active-directory-application-proxy-get-started.md)

- [Azure Active Directory 그룹을 사용하여 리소스에 대한 액세스 관리](active-directory-manage-groups.md)

- [Microsoft Azure Active Directory 라이선스란?](active-directory-licensing-what-is.md)

- [조직 내에서 사용되고 있는 허용되지 않은 클라우드 앱을 검색하는 방법](active-directory-cloudappdiscovery-whatis.md)

<!---HONumber=AcomDC_0413_2016-->