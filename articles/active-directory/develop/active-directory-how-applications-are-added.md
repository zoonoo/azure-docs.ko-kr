---
title: "Azure Active Directory에 응용 프로그램을 추가하는 방법입니다."
description: "이 문서에서는 Azure Active Directory의 인스턴스에 응용 프로그램을 추가하는 방법을 설명합니다."
services: active-directory
documentationcenter: 
author: shoatman
manager: kbrint
editor: 
ms.assetid: 3321d130-f2a8-4e38-b35e-0959693f3576
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/09/2016
ms.author: shoatman
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 65c8a2c020d5ef7ce47905927a248b9e73d2ed1b
ms.lasthandoff: 03/29/2017


---
# <a name="how-and-why-applications-are-added-to-azure-ad"></a>응용 프로그램을 Azure AD에 추가하는 방법 및 이유
Azure Active Directory 인스턴스에서 응용 프로그램 목록을 확인할 때 처음에 어려운 것 중 하나는 응용 프로그램이 어디서 추가되었는지 및 그 이유를 이해하는 것입니다.  이 문서에서는 응용 프로그램이 디렉터리에 표시되는 방식을 간략하게 설명하고 응용 프로그램이 디렉터리에 추가된 과정을 이해하는 데 도움이 되는 컨텍스트를 제공합니다.

## <a name="what-services-does-azure-ad-provide-to-applications"></a>응용 프로그램에 제공되는 Azure AD 서비스는 무엇인가요?
Azure AD에서 제공하는 하나 이상의 서비스를 활용하기 위해 응용 프로그램이 Azure AD에 추가됩니다.  서비스 포함 사항:

* 인증 및 권한 부여
* 사용자 인증 및 권한 부여
* 페더레이션 또는 암호를 사용한 SSO(Single Sign-On)
* 사용자 프로비전 및 동기화
* 역할 기반 액세스 제어: 응용 프로그램 역할을 정의하는 디렉터리를 사용하여 응용 프로그램에서 역할 기반 권한 부여 확인을 수행합니다.
* oAuth 인증 서비스(API/리소스에 대한 액세스 권한을 부여는 Microsoft 응용 프로그램 및 Office 365에서 사용하는 서비스)
* 응용 프로그램 게시 및 프록시: 개인 네트워크의 응용 프로그램을 인터넷에 게시합니다.

## <a name="how-are-applications-represented-in-the-directory"></a>응용 프로그램이 디렉터리에 어떻게 표시되나요?
응용 프로그램은 Azure AD에서 응용 프로그램 개체와 서비스 주체 개체로 표시됩니다.  "home"/"owner" 또는 "publishing" 디렉터리에 등록된 하나의 응용 프로그램 개체와 응용 프로그램이 동작하는 모든 디렉터리의 응용 프로그램을 나타내는 하나 이상의 서비스 주체 개체가 있습니다.  

응용 프로그램 개체는 Azure AD에 응용 프로그램을 설명하며(다중 테넌트 서비스) 다음을 포함할 수 있습니다(*참고*: 전체 목록은 아님).

* 이름, 로고, 게시자
* 비밀(응용 프로그램을 인증하는 데 사용되는 대칭 및/또는 비대칭 키)
* API 종속성(oAuth)
* 게시된 API/리소스/범위(oAuth)
* 응용 프로그램 역할(RBAC)
* SSO 메타데이터 및 구성(SSO)
* 사용자 프로비전 메타데이터 및 구성
* 프록시 메타데이터 및 구성

서비스 주체는 홈 디렉터리를 포함하여 응용 프로그램이 동작하는 모든 디렉터리의 응용 프로그램 기록입니다.  서비스 주체가 수행하는 작업은 다음과 같습니다.

* 응용 프로그램 ID 속성을 통한 응용 프로그램 개체 다시 참조
* 로컬 사용자 및 그룹 응용 프로그램 역할 할당 기록
* 응용 프로그램에 부여된 로컬 사용자 및 관리자 권한 기록
  * 예: 특정 사용자 메일에 액세스하기 위한 앱 권한
* 조건부 액세스 정책을 포함하여 로컬 정책 기록
* 앱의 대체 로컬 설정 기록
  * 클레임 변환 규칙
  * 특성 매핑(사용자 프로비전)
  * 테넌트 특정 응용 프로그램 역할(응용 프로그램이 사용자 지정 역할을 지원하는 경우)
  * 이름/로고

### <a name="a-diagram-of-application-objects-and-service-principals-across-directories"></a>응용 프로그램 개체 및 서비스 주체 액세스 디렉터리 다이어그램
![응용 프로그램 개체와 서비스 주체가 Azure AD 인스턴스와 같이 존재하는 방법을 보여 주는 다이어그램입니다.][apps_service_principals_directory]

위의 다이어그램에서 볼 수 있듯이  Microsoft는 두 개의 디렉터리를 내부적으로 유지하며(왼쪽 부분) 응용 프로그램을 게시하는 데 사용합니다.

* Microsoft 응용 프로그램용 디렉터리(Microsoft 서비스 디렉터리)
* 사전 통합된 타사 응용 프로그램용 디렉터리(응용 프로그램 갤러리 디렉터리)

Azure AD와 통합하는 응용 프로그램 게시자/공급업체에는 게시 디렉터리가 있어야 합니다.  (일부 SAAS 디렉터리)

직접 추가하는 응용 프로그램은 다음과 같습니다.

* 직접 개발한 응용 프로그램(AAD와 통합됨)
* Single-Sign-On용으로 연결된 응용 프로그램
* Azure AD 응용 프로그램 프록시를 사용하여 게시된 응용 프로그램

### <a name="a-couple-of-notes-and-exceptions"></a>일부 참고 및 예외 사항
* 일부 서비스 주체만 응용 프로그램 개체를 다시 가리킵니다.  그렇죠? Azure AD가 처음 빌드되었을 때 응용 프로그램에 제공된 서비스는 훨씬 제한적이었으며 서비스 주체로 충분히 응용 프로그램 ID를 설정할 수 있었습니다.  원래 서비스 주체는 Windows Server Active Directory 서비스 계정의 형태에 더 가까웠습니다.  이러한 이유로 먼저 응용 프로그램 개체를 만들지 않고도 Azure AD PowerShell을 사용하여 서비스 주체를 만들 수 있는 것입니다.  Graph API는 응용 프로그램 개체가 있어야 서비스 주체를 만들 수 있습니다.
* 위에서 설명한 정보 중 일부만 프로그래밍 방식으로 나타납니다.  다음은 UI에서만 사용할 수 있습니다.
  * 클레임 변환 규칙
  * 특성 매핑(사용자 프로비전)
* 서비스 주체 및 응용 프로그램 개체에 대한 자세한 내용은 Azure AD Graph REST API 참조 문서를 참조하세요.  *힌트*: The Azure AD Graph API 설명서는 현재 사용할 수 있는 Azure AD의 스키마 참조에 가깝습니다.  
  * [응용 프로그램](https://msdn.microsoft.com/library/azure/dn151677.aspx)
  * [서비스 주체](https://msdn.microsoft.com/library/azure/dn194452.aspx)

## <a name="how-are-apps-added-to-my-azure-ad-instance"></a>응용 프로그램이 Azure AD 인스턴스에 어떻게 추가되나요?
다음과 같은 여러 가지 방법으로 응용 프로그램을 Azure AD에 추가할 수 있습니다.

* [Azure Active Directory 응용 프로그램 갤러리](https://azure.microsoft.com/updates/azure-active-directory-over-1000-apps/)
* Azure Active Directory와 통합된 타사 응용 프로그램에 등록/로그인(예: [Smartsheet](https://app.smartsheet.com/b/home) 또는 [DocuSign](https://www.docusign.net/member/MemberLogin.aspx))
  * 등록/로그인하는 동안 해당 프로필 및 다른 사용 권한에 액세스하도록 앱에 권한을 부여하라는 메시지가 사용자에게 표시됩니다.  처음으로 동의하는 사람이 응용 프로그램을 나타내는 서비스 주체를 디렉터리에 추가하게 됩니다.
* [Office 365](http://products.office.com/)
  * Office 365를 구독하거나 평가판을 시작하면 Office 365와 관련된 모든 기능을 전달하는 데 사용되는 다양한 서비스를 나타내는 디렉터리에 하나 이상의 서비스 주체가 만들어집니다.
  * SharePoint와 같은 일부 Office 365 서비스는 워크플로 등 구성 요소 간의 보안 통신을 허용하도록 지속적으로 서비스 주체를 만듭니다.
* Azure 관리 포털에서 개발 중인 응용 프로그램 추가. 참조: https://msdn.microsoft.com/library/azure/dn132599.aspx
* Visual Studio를 사용하여 개발 중인 응용 프로그램 추가. 참조:
  * [ASP.Net 인증 방법](http://www.asp.net/visual-studio/overview/2013/creating-web-projects-in-visual-studio#orgauthoptions)
  * [연결된 서비스](http://blogs.msdn.com/b/visualstudio/archive/2014/11/19/connecting-to-cloud-services.aspx)
* [Azure AD 응용 프로그램 프록시](https://msdn.microsoft.com/library/azure/dn768219.aspx)
* SAML 또는 암호 SSO를 사용하여 Single-Sign-On용 응용 프로그램 연결
* Azure의 다양한 개발자 환경 및 개발자 센터의 API 탐색기 환경을 포함한 기타 여러 가지 방법

## <a name="who-has-permission-to-add-applications-to-my-azure-ad-instance"></a>응용 프로그램을 Azure AD 인스턴스에 추가할 권한이 있는 사용자는?
다음 작업은 전역 관리자만 수행할 수 있습니다.

* Azure AD 응용 프로그램 갤러리(사전 통합된 타사 응용 프로그램)에서 앱 추가
* Azure AD 응용 프로그램 프록시를 사용하여 응용 프로그램 게시

디렉터리의 모든 사용자는 개발 중인 응용 프로그램을 추가할 권한 및 조직 데이터를 공유하거나 액세스 권한을 부여할 응용 프로그램을 결정할 권한을 갖습니다.  *사용자가 앱을 등록 및 로그인하고 권한을 부여하면 서비스 주체가 만들어질 수 있습니다.*

처음에는 염려스러울 수 있으나 다음을 기억하세요.

* 응용 프로그램을 디렉터리에 등록/기록하지 않고도 응용 프로그램은 수년간 사용자 인증에 Windows Server Active Directory를 활용해왔습니다.  이제 조직에서 디렉터리를 사용하는 응용 프로그램 수 및 용도를 정확하게 파악할 수 있습니다.
* 관리자 중심 응용 프로그램 게시/등록 프로세스가 필요하지 않습니다.  Active Directory Federation Services를 사용할 경우 신뢰 당사자로 개발자가 아닌 관리자가 앱을 추가해야 했습니다.  이제 개발자가 직접 할 수 있습니다.
* 비즈니스 목적의 경우 사용자는 조직 계정을 사용하여 응용 프로그램에 로그인/등록하는 것이 좋습니다.  이후에 사용자가 조직을 떠날 경우 사용하던 응용 프로그램에서 자신의 계정에 액세스할 수 없게 됩니다.
* 어떤 데이터를 어떤 응용 프로그램과 공유했는지 기록하는 것이 좋습니다.  데이터는 더욱 전송하기 쉬워졌으며 어떤 데이터를 어떤 응용 프로그램으로 누구와 공유했는지 기록해놓는 것이 유용합니다.
* oAuth용 Azure AD를 사용하는 응용 프로그램은 사용자가 응용 프로그램에 부여할 수 있는 권한 및 관리자가 동의하는 데 필요한 권한을 정확하게 결정합니다.  더 큰 범위 및 보다 중요한 사용 권한에 대해서는 관리자만 동의할 수 있습니다.
* 사용자가 응용 프로그램을 추가하고 응용 프로그램이 데이터에 액세스하도록 허용하는 것은 감사된 이벤트이므로 Azure 관리 포털 내의 감사 보고서를 보고 디렉터리에 응용 프로그램을 추가하는 방법을 결정할 수 있습니다.

**참고:** *Microsoft는 수개월 동안 기본 구성을 사용하여 운영해오고 있습니다.*

요약하자면 Azure 관리 포털에서 디렉터리 구성을 수정하여 디렉터리의 사용자가 응용 프로그램을 추가하거나 응용 프로그램과 공유할 정보를 결정하지 못하도록 할 수 있습니다.  다음 구성은 Azure 관리 포털 내의 디렉터리의 "구성" 탭에서 액세스할 수 있습니다.

![통합된 응용 프로그램 설정을 구성하기 위한 UI의 스크린샷][app_settings]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>다음 단계
응용 프로그램을 Azure AD에 추가하는 방법 및 응용 프로그램에 맞게 서비스를 구성하는 방법에 대해 자세히 알아보세요.

* 개발자: [응용 프로그램을 AAD와 통합하는 방법에 대해 알아보기](https://msdn.microsoft.com/library/azure/dn151122.aspx)
* 개발자: [GitHub에서 Azure Active Directory와 통합된 앱의 샘플 코드 검토하기](https://github.com/AzureADSamples)
* 개발자 및 IT 전문가: [Azure Active Directory Graph API용 REST API 설명서 검토하기](https://msdn.microsoft.com/library/azure/hh974478.aspx)
* IT 전문가: [응용 프로그램 갤러리에서 Azure Active Directory 사전 통합된 응용 프로그램을 사용하는 방법에 대해 알아보기](https://msdn.microsoft.com/library/azure/dn308590.aspx)
* IT 전문가: [사전 통합된 특정 응용 프로그램 구성에 대한 자습서 찾기](https://msdn.microsoft.com/library/azure/dn893637.aspx)
* IT 전문가: [Azure Active Directory 응용 프로그램 프록시를 사용하여 응용 프로그램을 게시하는 방법에 대해 알아보기](https://msdn.microsoft.com/library/azure/dn768219.aspx)

## <a name="see-also"></a>참고 항목
* [Azure Active Directory의 응용 프로그램 관리를 위한 문서 인덱스](../active-directory-apps-index.md)

<!--Image references-->
[apps_service_principals_directory]:../media/active-directory-how-applications-are-added/HowAppsAreAddedToAAD.jpg
[app_settings]:../media/active-directory-how-applications-are-added/IntegratedAppSettings.jpg

