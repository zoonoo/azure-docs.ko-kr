<properties
   pageTitle="응용 프로그램을 관리하기 위한 문서 라이브러리 | Microsoft Azure"
   description="사용 방법, 문제 해결 및 FAQ에 대한 기술 참조 링크가 있는 Azure Active Directory 응용 프로그램 관리 항목"
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="12/03/2015"
   ms.author="kgremban"/>

# 응용 프로그램을 관리하기 위한 문서 라이브러리

이 페이지는 Azure Active Directory를 사용하는 Single Sign-On의 주요 기능에 대한 유용한 링크를 포함합니다. 링크는 다음 범주로 구성됩니다.

- **개요:** 사용 사례 및 항목 소개를 통한 기능의 개요입니다.
- **방법:** 특정 시나리오를 시작하는 자세한 지침입니다.
- **문제 해결:** 일반적인 문제를 진단하기 위한 팁입니다.
- **FAQ:** 일반적인 질문 및 문제에 대한 대답입니다.  

## 클라우드 앱 검색

| | |
| ------ | ------ |
| 개요 | [조직 내에서 사용되고 있는 허용되지 않은 클라우드 앱을 검색하는 방법](active-directory-cloudappdiscovery-whatis.md) |
| 방법 | [클라우드 앱 검색 시작](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx) <br><br> [클라우드 앱 검색 보안 및 개인 정보 취급 방침 고려 사항](active-directory-cloudappdiscovery-security-and-privacy-considerations.md) <br><br> [클라우드 앱 검색 그룹 정책 배포 가이드](http://social.technet.microsoft.com/wiki/contents/articles/30965.cloud-app-discovery-group-policy-deployment-guide.aspx) <br><br> [클라우드 앱 검색 System Center 배포 가이드](http://social.technet.microsoft.com/wiki/contents/articles/30968.cloud-app-discovery-system-center-deployment-guide.aspx) <br><br> [프록시 서비스용 클라우드 앱 검색 레지스트리 설정](active-directory-cloudappdiscovery-registry-settings-for-proxy-services.md) |
| FAQ | [클라우드 앱 검색—질문과 대답](http://social.technet.microsoft.com/wiki/contents/articles/24037.cloud-app-discovery-frequently-asked-questions.aspx) |

## 앱 할당

| | |
| ------ | ------ |
| 개요 | [Azure Active Directory 그룹을 사용하여 리소스에 대한 액세스 관리](active-directory-manage-groups.md) |
| 방법 | [간단한 규칙을 만들어 그룹에 동적 멤버 자격 구성](active-directory-accessmanagement-simplerulegroup.md) <br><br> [그룹을 사용하여 SaaS 응용 프로그램에 대한 액세스 관리](active-directory-accessmanagement-group-saasapps.md) <br><br> [셀프 서비스 응용 프로그램 액세스 관리를 위해 Azure AD 설정](active-directory-accessmanagement-self-service-group-management.md) <br><br> [그룹에 대한 소유자 관리](active-directory-accessmanagement-managing-group-owners.md) <br><br> [Azure Active Directory에서 보안 그룹 관리](active-directory-accessmanagement-manage-groups.md) <br><br> [Azure Active Directory의 전용 그룹](active-directory-accessmanagement-dedicated-groups.md) <br><br> [특성을 사용하여 고급 규칙 만들기](active-directory-accessmanagement-groups-with-advanced-rules.md) <br><br> [Azure Active Directory를 사용하여 SaaS 응용 프로그램에 사용자 프로비전 및 프로비전 해제 자동화](active-directory-saas-app-provisioning.md) <br><br> [Azure Active Directory를 사용하여 SaaS 앱을 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md) |

## 페더레이션된 Single Sign-On

| | |
| ------ | ------ |
| 개요 |[AD FS로 페더레이션 구성](active-directory-aadconnect-get-started-custom.md)
| 방법 | [Single Sign-On을 사용한 DirSync](https://msdn.microsoft.com/library/azure/dn441213.aspx) <br><br> [Microsoft Azure는 이제 Windows Server Active Directory를 통한 페더레이션을 지원합니다](https://azure.microsoft.com/blog/windows-azure-now-supports-federation-with-windows-server-active-directory/) <br><br> [Azure Active Directory를 사용하여 SaaS 앱을 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md) <br><br> [Active Directory 마켓플레이스](https://azure.microsoft.com/marketplace/active-directory/) |
| 문제 해결 | [Azure AD DirSync 도구 구성 마법사 문제 해결](http://social.technet.microsoft.com/wiki/contents/articles/19100.troubleshooting-azure-ad-dirsync-tool-configuration-wizard-failed-to-get-address-for-method-createidentityhandle2.aspx) <br><br> [Azure Active Directory 동기화 도구 설치 및 구성 마법사 오류 문제를 해결하는 방법](https://support.microsoft.com/kb/2684395) <br><br> [디렉터리 동기화 문제 해결](https://msdn.microsoft.com/library/azure/jj151787.aspx) <br><br> [Single Sign-On 문제 해결](https://msdn.microsoft.com/library/azure/jj151834.aspx) |
| FAQ | [Azure Active Directory에서 페더레이션된 Single Sign-On에 대한 인증서 관리](active-directory-sso-certs.md) <br><br> [Azure Active Directory Connect FAQ](active-directory-aadconnect-faq.md) |

## 암호 SSO(Single sign-on)

| | |
| ------ | ------ |
| 방법 | [Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md) <br><br> [암호 동기화를 사용한 DirSync](https://msdn.microsoft.com/library/azure/dn441214.aspx) |
| 문제 해결 | [Azure AD DirSync 도구 구성 마법사 문제 해결](http://social.technet.microsoft.com/wiki/contents/articles/19100.troubleshooting-azure-ad-dirsync-tool-configuration-wizard-failed-to-get-address-for-method-createidentityhandle2.aspx) <br><br> [Azure Active Directory 동기화 도구 설치 및 구성 마법사 오류 문제를 해결하는 방법](https://support.microsoft.com/kb/2684395) <br><br> [디렉터리 동기화 문제 해결](https://msdn.microsoft.com/library/azure/jj151787.aspx) |
| FAQ | [Azure Active Directory Connect FAQ](active-directory-aadconnect-faq.md) |

## 다양한 앱 프로비전

| | |
| ------ | ------ |
| 개요 | [Azure Active Directory를 사용하여 SaaS 응용 프로그램의 사용자를 자동으로 프로비전 및 프로비전 해제](active-directory-saas-app-provisioning.md) |
| 방법 | [자습서: Concur와 Azure Active Directory 통합](active-directory-saas-concur-tutorial.md) <br><br> [자습서: DocuSign과 Azure Active Directory 통합](active-directory-saas-docussign-tutorial.md) <br><br> [자습서: Dropbox for Business와 Azure Active Directory 통합](active-directory-saas-dropboxforbusiness-tutorial.md) <br><br> [자습서: Google Apps와 Azure Active Directory를 통합하는 방법](active-directory-saas-google-apps-tutorial.md) <br><br> [자습서: Salesforce와 Azure Active Directory를 통합하는 방법](active-directory-saas-salesforce-tutorial.md) <br><br> [자습서: ServiceNow와 Azure Active Directory 통합](active-directory-saas-servicenow-tutorial.md) <br><br> [자습서: 인바운드 동기화에 Workday 구성](active-directory-saas-workday-inbound-tutorial.md) |

## Azure AD 앱 갤러리

| | |
| ------ | ------ |
| 방법 | [Azure Active Directory 응용 프로그램 갤러리에 응용 프로그램 나열](active-directory-app-gallery-listing.md) <br><br> [Azure AD 셀프 서비스 SAML 구성을 사용하여 "앱 가져오기"](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx) |

## 사용자 지정 앱

| | |
| ------ | ------ |
| 개요 | [Azure AD 셀프 서비스 SAML 구성을 사용하여 "앱 가져오기"](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx) |
| 방법 | [자습서: Azure Active Directory와 Salesforce를 통합하는 방법](active-directory-saas-salesforce-tutorial.md) |

## 앱 프록시

| | |
| ------ | ------ |
| 개요 | [온-프레미스 응용 프로그램에 보안된 원격 액세스를 제공하는 방법](active-directory-application-proxy-get-started.md) |
| 방법 | [Azure AD 응용 프로그램 프록시 사용](active-directory-application-proxy-enable.md) <br><br> [Azure AD 응용 프로그램 프록시를 사용하여 응용 프로그램 게시](active-directory-application-proxy-publish.md) <br><br> [조건부 액세스로 작업](active-directory-application-proxy-conditional-access.md) <br><br> [Azure AD 응용 프로그램 프록시에 사용자 지정 도메인으로 작업](active-directory-application-proxy-custom-domains.md) <br><br> [응용 프로그램 프록시에서 클레임 인식 앱으로작업](active-directory-application-proxy-claims-aware-apps.md) <br><br> [응용 프로그램 프록시를 사용하는 Single Sign-On](active-directory-application-proxy-sso-using-kcd.md) |
| 문제 해결 | [응용 프로그램 프록시 문제 해결](active-directory-application-proxy-troubleshoot.md) |

## 앱 시작 환경

| | |
| ------ | ------ |
| 개요 | [액세스 패널 소개](active-directory-saas-access-panel-introduction.md) |
| 방법 | [Azure Active Directory 액세스 패널 - EMS](http://blogs.msdn.com/b/haddy_el-haggan_blog/archive/2015/04/02/azure-active-directory-access-panel-ems.aspx) <br><br> [Azure Active Directory와 통합](active-directory-how-to-integrate.md) |

<!---HONumber=AcomDC_1210_2015-->