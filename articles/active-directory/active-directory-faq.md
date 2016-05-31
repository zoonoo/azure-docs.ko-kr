<properties
	pageTitle="Azure Active Directory FAQ | Microsoft Azure"
	description="Azure 및 Azure Active Directory에 액세스, 암호 관리 및 응용 프로그램 액세스와 함께 질문에 대한 답변을 제공하는 Azure Active Directory FAQ입니다."
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
	ms.date="05/20/2016"
	ms.author="markusvi"/>

# Azure Active Directory FAQ

Azure Active Directory는 ID, 액세스 관리 및 보안의 모든 측면에 걸쳐 포괄적인 IDaaS(Identity as a Service) 솔루션입니다.


자세한 내용은 [Azure Active Directory란](active-directory-whatis.md)을 참조하세요.



## Azure 및 Azure Active Directory에 액세스


****Q: Azure 클래식 포털에서 Azure AD에 액세스하려고 할 때 "구독을 찾을 수 없음"이 표시되는 이유는 무엇인가요(https://manage.windowsazure.com)?**

**A:** Azure 클래식 포털에 엑세스하려면 각 사용자에게 Azure 구독에 대한 권한이 있어야 합니다. 유료 Office 365 또는 Azure AD를 사용하여 일회성 활성화 단계를 위해 [http://aka.ms/accessAAD](http://aka.ms/accessAAD)로 이동하는 경우 구독에 대한 사용 권한이 있어야 하며 그렇지 않으면 [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/) 또는 유료 구독을 활성화해야 합니다.

자세한 내용은 다음을 참조하세요.

- [Azure 구독과 Azure Active Directory의 연관 관계](active-directory-how-subscriptions-associated-directory.md)

- [Azure에서 Office 365 구독의 디렉터리 관리](active-directory-manage-o365-subscription.md)

---

**Q: Azure AD, Office 365와 Azure 간에는 어떤 관계가 있나요?**

**A:** Azure Active Directory는 모든 Microsoft Online Services에 일반적인 ID 및 액세스 기능을 제공합니다. Office 365, Microsoft Azure, Intune 또는 기타 제품을 사용하든지 이러한 모든 서비스에 대해 로그온 및 액세스 관리를 설정하는 데 Azure AD를 이미 사용 중입니다.

사실 Microsoft Online Services에 대해 설정한 모든 사용자는 하나 이상의 Azure AD 인스턴스에서 사용자 계정으로 정의됩니다. 클라우드 응용 프로그램 액세스와 같은 무료 Azure AD 기능에 이러한 계정을 사용할 수 있습니다.
 
또한 Azure AD 유료 서비스(예: Azure AD Basic, Premium, EMS 등)는 포괄적인 엔터프라이즈 규모 관리 및 보안 솔루션으로 Office 365 및 Microsoft Azure와 같은 다른 온라인 서비스를 보완합니다.


---



## 하이브리드 Azure AD 시작


**Q: Azure AD에 온-프레미스 디렉터리를 연결하려면 어떻게 해야 하나요?**

**A:** **Azure AD Connect**를 사용하여 온-프레미스 디렉터리를 Azure AD에 연결할 수 있습니다.

자세한 내용은 [Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)을 참조하세요.


---

**Q: 온-프레미스 디렉터리와 클라우드 응용 프로그램 간에 SSO를 설정하려면 어떻게 해야 하나요?**

**A:** 온-프레미스 디렉터리와 Azure AD 간에 SSO를 설정해야 합니다. Azure AD를 통해 클라우드 응용 프로그램에 액세스할 수만 있다면 서비스는 온-프레미스 자격 증명으로 올바르게 인증하도록 사용자를 자동으로 유도합니다.

온-프레미스에서 SSO 구현은 ADFS와 같은 페더레이션 솔루션 또는 암호 해시 동기화 구성으로 쉽게 달성할 수 있습니다. 두 옵션 모두 Azure AD Connect 구성 마법사를 사용하여 쉽게 배포할 수 있습니다.
  

자세한 내용은 [Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)을 참조하세요.
  

---

**Q: Azure Active Directory에서 내 조직의 사용자에 대한 셀프 서비스 포털을 제공하나요?**

**A:** 예, Azure Active Directory는 사용자 셀프 서비스 및 응용 프로그램 액세스를 위해 [Azure AD 액세스 패널](http://myapps.microsoft.com)을 제공합니다. Office 365 고객인 경우 Office 365 포털에서 동일한 많은 기능을 찾을 수 있습니다.

자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하세요.



---

**Q: Azure AD를 사용하면 내 온-프레미스 인프라를 관리하는 데 도움이 되나요?**

**A:** 예, 그렇습니다. Azure AD Premium Edition은 **Connect Health**를 제공합니다. Azure AD Connect Health를 사용하면 온-프레미스 ID 인프라 및 동기화 서비스를 모니터링하고 파악할 수 있습니다.

자세한 내용은 [온-프레미스 ID 인프라 및 클라우드 동기화 서비스 모니터링](active-directory-aadconnect-health.md)을 참조하세요.

---

## 암호 관리

**Q: Azure AD 비밀번호 쓰기 저장을 암호 동기화 없이 사용할 수 있나요? (즉, 비밀번호 쓰기 저장으로 Azure AD SSPR을 사용하고자 하지만 클라우드에 암호를 저장하지 않으려면 어떻게 하나요?)**

**A:** 쓰기 저장을 사용하기 위해 Azure AD에 AD 암호를 동기화할 필요가 없습니다. 페더레이션된 환경에서 Azure AD SSO는 온-프레미스 디렉터리에 의존하여 사용자를 인증합니다. 이 시나리오는 Azure AD에서 추적되는 온-프레미스 암호를 필요로 하지 않습니다.

---

**Q: AD 온-프레미스에 암호를 쓰기 저장하는 데 시간이 얼마나 걸리나요?**

**A:** 비밀번호 쓰기 저장은 실시간으로 작동됩니다.

자세한 내용은 [암호 관리 시작](active-directory-passwords-getting-started.md)을 참조하세요.


---

**Q: 관리자가 관리하는 암호로 비밀번호 쓰기 저장을 사용할 수 있나요?**

**A:** 예, 비밀번호 쓰기 저장을 사용하도록 설정하는 경우 관리자가 수행하는 암호 작업이 온-프레미스 환경에 다시 기록됩니다.

암호와 관련된 질문에 대한 자세한 답변은 [암호 관리 질문과 대답](active-directory-passwords-faq.md)을 참조하세요.

---

## 응용 프로그램 액세스


**Q: Azure AD 및 해당 기능과 미리 통합된 응용 프로그램의 목록을 어디에서 찾을 수 있나요?**

**A:** Azure AD에는 Microsoft, 응용 프로그램 서비스 공급자 또는 파트너에서 2600개가 넘는 미리 통합된 응용 프로그램이 있습니다. 모든 사전 통합된 응용 프로그램에서 SSO를 지원합니다. SSO를 사용하면 앱에 액세스하는 데 조직의 자격 증명을 사용할 수 있습니다. 일부 응용 프로그램에서도 자동화된 프로비전 및 프로비전 해제를 지원합니다.

미리 통합된 응용 프로그램의 전체 목록은 [Active Directory 마켓플레이스](https://azure.microsoft.com/marketplace/active-directory/)를 참조하세요.


---

**Q: 필요한 응용 프로그램이 Azure AD 마켓플레이스에 없는 경우 어떻게 하나요?**

**A:** Azure AD Premium에서는 원하는 응용 프로그램을 추가하고 구성할 수 있습니다. 응용 프로그램의 기능 및 기본 설정에 따라 SSO 및 자동화된 프로비전을 구성할 수 있습니다.

자세한 내용은 다음을 참조하세요.

- [Azure Active Directory 응용 프로그램 갤러리에 있지 않은 응용 프로그램에 Single Sign-On 구성](active-directory-saas-custom-apps.md)
- [SCIM를 사용하여 Azure Active Directory으로부터 응용 프로그램에 사용자 및 그룹의 자동 프로비전 사용](active-directory-scim-provisioning.md) 


---

**Q: 사용자가 Azure Active Directory를 사용하여 응용 프로그램에 서명하려면 어떻게 하나요?**
 
**A:** Azure Active Directory는 다음과 같이 사용자가 자신의 응용 프로그램을 보고 액세스할 수 있는 여러 가지 방법을 제공합니다.

- Azure AD 액세스 패널

- Office 365 응용 프로그램 실행 프로그램

- 페더레이션된 앱에 직접 로그온

- 페더레이션된 앱, 암호로 보호된 앱 또는 기존 앱에 대한 딥 링크

자세한 내용은 [사용자에게 Azure AD 통합 응용 프로그램 배포](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users)를 참조하세요.


---

**Q: Azure Active Directory에서 응용 프로그램에 대한 인증 및 Single Sign-On을 설정하는 다른 방법은 무엇인가요?**
 
**A:** Azure Active Directory는 SAML 2.0, OpenID Connect, OAuth 2.0, WS-Federation 등 인증 및 권한 부여를 위해 여러 표준화된 프로토콜을 지원합니다. 또한 Azure AD는 양식 기반 인증만 지원하는 앱에 대해 암호 보관 및 자동화된 로그인 기능도 지원합니다.

자세한 내용은 다음을 참조하세요.

- [Azure AD의 인증 시나리오](active-directory-authentication-scenarios.md)

- [Active Directory 인증 프로토콜](https://msdn.microsoft.com/library/azure/dn151124.aspx)

- [Azure Active Directory에서 Single Sign-On이 작동하는 방식](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work)


---

**Q: 온-프레미스를 실행하는 응용 프로그램을 추가할 수 있나요?**

**A:** Azure AD 응용 프로그램 프록시는 선택한 온-프레미스 웹 응용 프로그램에 대해 손쉽고 안전한 액세스를 제공합니다. Azure Active Directory에서 SaaS 앱에 액세스하는 것과 동일한 방식으로 이러한 응용 프로그램에 액세스할 수 있습니다. 네트워크 인프라 변경이나 VPN이 필요하지 않습니다.

자세한 내용은 [온-프레미스 응용 프로그램에 보안된 원격 액세스를 제공하는 방법](active-directory-application-proxy-get-started.md)을 참조하세요.


--- 

**Q: 특정 응용 프로그램에 액세스하는 사용자의 경우 MFA를 어떻게 요구하나요?**

**A:** Azure AD 조건부 액세스에서는 각 응용 프로그램에 대한 고유한 액세스 정책을 할당할 수 있습니다. 정책에서 언제든지 또는 사용자가 로컬 네트워크에 연결되지 않은 경우 MFA를 요구할 수 있습니다.

자세한 내용은 [Azure Active Directory에 연결된 Office 365 및 기타 앱에 대한 액세스 보호](active-directory-conditional-access.md)를 참조하세요.


---

**Q: SaaS 앱을 위한 자동 사용자 프로비전이란?**

**A:** Azure Active Directory를 사용하면 여러 인기 있는 클라우드(SaaS) 응용 프로그램에서 사용자 ID의 생성, 유지 관리 및 제거를 자동화할 수 있습니다.

자세한 내용은 [Azure Active Directory를 사용하여 SaaS 응용 프로그램의 사용자를 자동으로 프로비전 및 프로비전 해제](active-directory-saas-app-provisioning.md)를 참조하세요.

---

<!---HONumber=AcomDC_0525_2016-->