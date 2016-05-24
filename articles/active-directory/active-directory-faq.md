<properties
	pageTitle="Azure Active Directory FAQ | Microsoft Azure"
	description="Azure Active Directory FAQ"
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
	ms.topic="get-started-article"
	ms.date="04/20/2016"
	ms.author="markusvi"/>

# Azure Active Directory FAQ

Azure AD(Azure Active Directory)는 IDaaS(Identity as a Service)를 제공하는 일련의 포괄적인 클라우드 기반 ID 및 액세스 관리 기능입니다. IDaaS 공급자로서 Azure AD를 사용하면 컴퓨터 네트워크에서 "*수행하도록 허용된 작업 및 사용자*"를 제어할 수 있습니다. 또한 Azure AD는 응용 프로그램에 SSO(Single Sign-on)를 제공합니다. SSO를 사용하여 사용자의 로그온 환경을 크게 개선할 수 있습니다. 자세한 내용은 [Azure Active Directory란?](active-directory-whatis.md)을 참조하세요.



## Azure 및 Azure Active Directory에 액세스

****Q: Azure 포털에서 Azure AD를 찾을 수 없는 이유는 무엇인가요(http://portal.azure.com)?**

**A:** Azure AD 관리 환경은 Azure 클래식 포털에서 계속 제공됩니다(`http://manage.windowsazure.com`). Azure 포털에 통합된 새로운 관리 경험의 공개 미리 보기는 9월에 출시될 예정입니다. 릴리스에 대한 최신 정보는 [Active Directory 팀 블로그](https://blogs.technet.microsoft.com/ad/)를 참조하세요.


---

****Q: Azure 클래식 포털에서 Azure AD에 액세스하려고 할 때 "구독을 찾을 수 없음"이 표시되는 이유는 무엇인가요(http://manage.windowsazure.com)?**

**A:** 유료 Office 365 또는 Azure AD를 사용하여 일회성 활성화 단계를 위해 [http://aka.ms/accessAAD](http://aka.ms/accessAAD)로 이동하는 경우 Azure 구독에 대한 사용 권한이 필요합니다. 그렇지 않으면 [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/) 또는 유료 구독을 활성화해야 합니다.

---

**Q: Azure AD, Office 365와 Azure 간에는 어떤 관계가 있나요?**

**A:** Microsoft Azure는 Microsoft에서 관리되는 데이터 센터의 글로벌 네트워크를 통해 응용 프로그램 및 서비스를 빌드, 배포 및 관리하기 위한 유연하고 열린 엔터프라이즈급 클라우드 컴퓨팅 플랫폼 및 인프라입니다. 예를 들어 Azure는 Office 365와 같은 SaaS(Software as a Service) 솔루션을 제공합니다. Office 365는 생산성 소프트웨어와 관련된 온라인 서비스를 제공하는 소프트웨어 및 서비스 구독의 그룹입니다. Azure AD는 IDaaS(Identity as a Service)를 제공하는 일련의 포괄적인 클라우드 기반 ID 및 액세스 관리 기능으로 Office 365를 보완합니다. Office 365 구독이 있는 경우 Azure에서 실행되는 Azure AD를 보유하게 됩니다.


---

**Q: Azure AD 기능을 무료로 사용할 수 있나요?**

**A:** Azure 구독의 모든 공통 기능을 무료로 사용할 수 있습니다. 이러한 기능의 전체 목록은 [일반적인 기능](active-directory-editions.md/#common-features)을 참조하세요.



---

Q: **Azure AD에 온-프레미스 디렉터리를 연결하려면 어떻게 해야 하나요?**

A: **Azure AD Connect**를 사용하여 온-프레미스 디렉터리를 Azure AD에 연결할 수 있습니다. 자세한 내용은 [Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)을 참조하세요.


---

**Q: 온-프레미스 디렉터리와 클라우드 응용 프로그램 간에 SSO를 설정하려면 어떻게 해야 하나요?**

**A:** 온-프레미스 디렉터리와 Azure AD 간에 SSO를 설정해야 합니다. Azure AD에서 클라우드 응용 프로그램을 관리하는 한 ADFS 또는 암호 동기화와로 페더레이션을 구현하여 온-프레미스 환경에 SSO 도달 범위를 확장할 수 있습니다. 두 옵션은 모두 **Azure AD Connect**에 포함됩니다. 자세한 내용은 [Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)을 참조하세요.
  


---

**Q: Azure AD를 사용하면 내 온-프레미스 인프라를 관리하는 데 도움이 되나요?**

**A:** 예, 그렇습니다. Azure AD Premium Edition은 **Connect Health**를 제공합니다. Azure AD Connect Health를 사용하면 온-프레미스 ID 인프라 및 동기화 서비스를 모니터링하고 파악할 수 있습니다. 자세한 내용은 [온-프레미스 ID 인프라 및 클라우드 동기화 서비스 모니터링](active-directory-aadconnect-health.md)을 참조하세요.



## 암호 관리

**Q: Azure AD 비밀번호 쓰기 저장을 암호 동기화 없이 사용할 수 있나요? (즉, 비밀번호 쓰기 저장으로 Azure AD SSPR을 사용하고자 하지만 클라우드에 암호를 저장하지 않으려면 어떻게 하나요?)**

**A:** 쓰기 저장을 사용하기 위해 Azure AD에 AD 암호를 동기화할 필요가 없습니다. 페더레이션된 환경에서 Azure AD SSO는 온-프레미스 디렉터리에 의존하여 사용자를 인증합니다. 이 시나리오는 Azure AD에서 추적되는 온-프레미스 암호를 필요로 하지 않습니다.

---

**Q: AD 온-프레미스에 암호를 쓰기 저장하는 데 시간이 얼마나 걸리나요?**

**A:** 비밀번호 쓰기 저장은 실시간으로 작동됩니다. 자세한 내용은 [암호 관리 시작](active-directory-passwords-getting-started.md)을 참조하세요.


---

**Q: 관리자가 관리하는 암호로 비밀번호 쓰기 저장을 사용할 수 있나요?**

**A:** 예, 비밀번호 쓰기 저장을 사용하도록 설정하는 경우 관리자가 수행하는 암호 작업이 온-프레미스 환경에 다시 기록됩니다. 암호와 관련된 질문에 대한 자세한 답변은 [암호 관리 질문과 대답](active-directory-passwords-faq.md)을 참조하세요.



## 응용 프로그램 액세스


**Q: Azure AD 및 해당 기능과 미리 통합된 응용 프로그램의 목록을 어디에서 찾을 수 있나요?**

**A:** Azure AD에는 Microsoft, 응용 프로그램 서비스 공급자 또는 파트너에서 2600개가 넘는 미리 통합된 응용 프로그램이 있습니다. 미리 통합된 응용 프로그램의 전체 목록은 [Active Directory 마켓플레이스](https://azure.microsoft.com/marketplace/active-directory/)를 참조하세요.


---

**Q: 필요한 응용 프로그램이 Azure AD 마켓플레이스에 없는 경우 어떻게 하나요?**

**A:** Azure AD Premium에서는 원하는 응용 프로그램을 추가하고 구성할 수 있습니다. 또한 응용 프로그램의 기능 및 기본 설정에 따라 구성 SSO 및 자동화된 프로비전을 구성할 수 있습니다. 자세한 내용은 다음을 참조하세요.

- [Azure Active Directory 응용 프로그램 갤러리에 있지 않은 응용 프로그램에 Single Sign-On 구성](active-directory-saas-custom-apps.md)
- [SCIM를 사용하여 Azure Active Directory으로부터 응용 프로그램에 사용자 및 그룹의 자동 프로비전 사용](active-directory-scim-provisioning.md) 



---

**Q: 온-프레미스를 실행하는 응용 프로그램을 추가할 수 있나요?**

**A:** Azure AD 응용 프로그램 프록시를 사용하여 VPN을 사용하거나 네트워크 인프라를 변경할 필요 없이 Azure Active Directory의 SaaS 앱과 동일한 방식으로 온-프레미스 웹 응용 프로그램에 쉽게 액세스할 수 있습니다. 자세한 내용은 [온-프레미스 응용 프로그램에 보안된 원격 액세스를 제공하는 방법](active-directory-application-proxy-get-started.md)을 참조하세요.


--- 

**Q: 특정 응용 프로그램에 액세스하는 사용자의 경우 MFA를 어떻게 요구하나요?**

**A:** Azure AD 조건부 액세스에서는 각 응용 프로그램에 대한 고유한 액세스 정책을 할당할 수 있습니다. 정책에서 언제든지 또는 사용자가 로컬 네트워크에 연결되지 않은 경우 MFA를 요구할 수 있습니다. 자세한 내용은 [Azure Active Directory에 연결된 Office 365 및 기타 앱에 대한 액세스 보호](active-directory-conditional-access.md)를 참조하세요.

<!---HONumber=AcomDC_0518_2016-->