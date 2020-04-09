---
title: 응용 프로그램 인증을 AD FS에서 Azure Active 디렉터리로 이동
description: 이 문서는 조직에서 페더레이션된 SaaS 애플리케이션에 중점을 두고 애플리케이션을 Azure AD로 이동하는 방법을 이해할 수 있도록 돕기 위한 것입니다.
services: active-directory
author: barbaraselden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 04/01/2020
ms.author: baselden
ms.collection: M365-identity-device-management
ms.openlocfilehash: bcb39606cdbb6488ccdee2828029d3617d689508
ms.sourcegitcommit: df8b2c04ae4fc466b9875c7a2520da14beace222
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80891941"
---
# <a name="moving-application-authentication-from-active-directory-federation-services-to-azure-active-directory"></a>응용 프로그램 인증을 Active Directory 페더레이션 서비스에서 Azure Active Directory로 이동

[Azure Active Directory(Azure AD)는](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) 사용자, 파트너 및 고객에게 응용 프로그램에 액세스하고 모든 플랫폼 및 장치에서 공동 작업을 수행할 수 있는 단일 ID를 제공하는 범용 ID 플랫폼을 제공합니다. Azure AD에는 [ID 관리 기능의 전체 제품군이](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)있습니다. 응용 프로그램(앱) 인증 및 권한 부여를 Azure AD로 표준화하면 이러한 기능이 제공하는 이점을 얻을 수 있습니다. 

> [!NOTE]
> 이 문서에서는 온-프레미스 Active Directory 및 Active Directory 페더레이션 서비스에서 Azure AD로 응용 프로그램 인증을 이동하는 방법에 중점을 둡니다. 이 이동 계획에 대한 개요는 [응용 프로그램 인증을 Azure AD로 마이그레이션하는](https://aka.ms/migrateapps/whitepaper) 백서를 참조하십시오. 이 백서에서는 마이그레이션, 테스트 및 인사이트를 계획하는 방법에 대해 설명합니다.

## <a name="introduction"></a>소개

사용자 계정이 포함된 온-프레미스 디렉터리가 있는 경우 사용자가 인증하는 응용 프로그램이 많을 수 있습니다. 이러한 각 앱은 사용자가 자신의 ID를 사용하여 액세스할 수 있도록 구성됩니다. 


사용자는 온-프레미스 Active Directory를 통해 직접 인증할 수도 있습니다. AD FS(활성 디렉터리 페더레이션 서비스)는 온-프레미스 ID 서비스를 기반으로 하는 표준입니다. AD FS는 사용자가 각 응용 프로그램에 별도로 로그인할 필요 없이 신뢰할 수 있는 비즈니스 파트너 간에 단일 사인온(SSO) 기능을 사용할 수 있는 기능을 확장합니다. 이를 페더레이션이라고 합니다.

대부분의 조직에는 Office 365 및 Azure AD 기반 앱과 함께 AD FS에 직접 페더레이션된 SaaS(서비스형 소프트웨어) 또는 LOB(사용자 지정 LOB) 앱이 있습니다. 

![온-프레미스에서 직접 연결된 애플리케이션](media/migrate-adfs-apps-to-azure/app-integration-before-migration1.png)

**응용 프로그램 보안을 강화하려면 온-프레미스 및 클라우드 환경에서 단일 액세스 제어 및 정책 집합을 설정하는 것이 목표입니다.** 

![Azure AD를 통해 연결된 응용 프로그램](media/migrate-adfs-apps-to-azure/app-integration-after-migration1.png)



## <a name="types-of-apps-to-migrate"></a>마이그레이션할 앱 유형

모든 응용 프로그램 인증을 Azure AD로 마이그레이션하는 것은 ID 및 액세스 관리를 위한 단일 제어 평면을 제공하므로 최적입니다.

응용 프로그램은 인증을 위해 최신 또는 레거시 프로토콜을 사용할 수 있습니다. 최신 인증 프로토콜(예: SAML 및 Open ID Connect)을 사용하는 응용 프로그램을 먼저 마이그레이션하는 것이 좋습니다. 이러한 앱은 앱 갤러리에 기본 제공 커넥터를 사용하거나 Azure AD에 응용 프로그램을 등록하여 Azure AD로 인증하도록 재구성할 수 있습니다. 이전 프로토콜을 사용하는 앱은 [응용 프로그램 프록시를](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-application-proxy)사용하여 통합할 수 있습니다. 

자세한 내용은 [Azure AD와 통합할 수 있는 응용 프로그램 유형을](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-application-management)참조하세요.

AD FS 응용 프로그램 활동 보고서를 사용하여 [Azure AD Connect 상태를 사용하도록 설정한](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-adfs)경우 응용 프로그램을 Azure [AD로 마이그레이션할](https://docs.microsoft.com/azure/active-directory/manage-apps/migrate-adfs-application-activity) 수 있습니다. 

### <a name="the-migration-process"></a>마이그레이션 프로세스

앱 인증을 Azure AD로 이동하는 동안 앱 및 구성을 적절하게 테스트합니다. 프로덕션 환경으로 이동하는 마이그레이션 테스트에 기존 테스트 환경을 계속 사용하는 것이 좋습니다. 현재 테스트 환경을 사용할 수 없는 경우 응용 프로그램의 아키텍처에 따라 [Azure 앱 서비스](https://azure.microsoft.com/services/app-service/) 또는 Azure 가상 [컴퓨터를](https://azure.microsoft.com/free/virtual-machines/search/?OCID=AID2000128_SEM_lHAVAxZC&MarinID=lHAVAxZC_79233574796345_azure%20virtual%20machines_be_c__1267736956991399_kwd-79233582895903%3Aloc-190&lnkd=Bing_Azure_Brand&msclkid=df6ac75ba7b612854c4299397f6ab5b0&ef_id=XmAptQAAAJXRb3S4%3A20200306231230%3As&dclid=CjkKEQiAhojzBRDg5ZfomsvdiaABEiQABCU7XjfdCUtsl-Abe1RAtAT35kOyI5YKzpxRD6eJS2NM97zw_wcB)사용하여 테스트 환경을 설정할 수 있습니다.

앱 구성을 개발할 때 사용할 별도의 테스트 Azure AD 테넌트를 설정할 수 있습니다. 

마이그레이션 프로세스는 다음과 같습니다.

**1 단계 – 현재 상태: AD FS로 인증하는 프로덕션 앱**

![마이그레이션 단계 1 ](media/migrate-adfs-apps-to-azure/stage1.jpg)

 
**2 단계 – 선택 사항: Azure 테넌트를 테스트하는 앱 의 테스트 인스턴스**

응용 프로그램의 테스트 인스턴스를 테스트 Azure AD 테넌트에 가리키도록 구성을 업데이트하고 필요한 사항을 변경합니다. 앱은 Azure AD 테넌트 테스트의 사용자와 함께 테스트할 수 있습니다. 개발 과정에서 [Fiddler와](https://www.telerik.com/fiddler) 같은 도구를 사용하여 요청 및 응답을 비교하고 확인할 수 있습니다.

별도의 테스트 테넌트를 설정하는 것이 불가능한 경우 이 단계를 건너뛰고 앱의 테스트 인스턴스를 설정하고 아래 3단계에 설명된 대로 프로덕션 Azure AD 테넌트를 가리킵니다.

![마이그레이션 단계 2 ](media/migrate-adfs-apps-to-azure/stage2.jpg)

**3 단계 - 프로덕션 Azure 테넌트를 가리키는 테스트 앱**

응용 프로그램의 테스트 인스턴스를 Azure의 프로덕션 인스턴스로 가리키도록 구성을 업데이트합니다. 이제 프로덕션 인스턴스에서 사용자와 함께 테스트할 수 있습니다. 필요한 경우 전환 사용자에 대한 이 문서의 섹션을 검토합니다.

![마이그레이션 단계 3 ](media/migrate-adfs-apps-to-azure/stage3.jpg)

**4 단계 – 프로덕션 AD 테넌트를 가리키는 프로덕션 앱**

프로덕션 Azure 테넌트를 가리키도록 프로덕션 응용 프로그램의 구성을 업데이트합니다.

![마이그레이션 단계 1 ](media/migrate-adfs-apps-to-azure/stage4.jpg)

 AD FS로 인증하는 앱은 사용 권한에 Active Directory 그룹을 사용할 수 있습니다. [Azure AD Connect 동기화를](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis) 사용하여 온-프레미스 환경과 Azure AD 간에 ID 데이터를 동기화한 후 마이그레이션을 시작합니다. 응용 프로그램을 마이그레이션할 때 동일한 사용자에게 액세스 권한을 부여할 수 있도록 마이그레이션 하기 전에 해당 그룹 및 구성원을 확인 합니다.

### <a name="line-of-business-lob-apps"></a>LOB(비즈니스 라인) 앱

LOB 앱은 조직에서 내부적으로 개발하거나 데이터 센터에 설치된 표준 패키지 제품으로 제공됩니다. 예를 들어 Windows ID 기반 및 SharePoint 앱(SharePoint Online이 아님)에 빌드된 앱이 있습니다. 

OAuth 2.0, OpenID Connect 또는 WS-페더레이션을 사용하는 LOB 앱은 Azure AD와 [앱 등록으로](https://docs.microsoft.com/azure/active-directory/develop/app-registrations-training-guide-for-app-registrations-legacy-users)통합할 수 있습니다. [Azure 포털의](https://portal.azure.com/)엔터프라이즈 응용 프로그램 페이지에서 SAML 2.0 또는 WS-페더레이션을 [비갤러리 응용 프로그램으로](https://docs.microsoft.com/azure/active-directory/manage-apps/add-non-gallery-app) 사용하는 사용자 지정 앱을 통합합니다.

## <a name="saml-based-single-sign-on"></a>SAML 기반 단일 사인온

인증을 위해 SAML 2.0을 사용하는 앱은 [SAML 기반 단일 사인온(SAML](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) 기반 SSO)에 대해 구성할 수 있습니다. [SAML 기반 SSO를](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)사용하면 SAML 클레임에서 정의한 규칙에 따라 사용자를 특정 응용 프로그램 역할에 매핑할 수 있습니다. 

SAML 기반 단일 사인온에 대한 SaaS 응용 프로그램을 구성하려면 [SAML 기반 단일 사인온 구성을](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-single-sign-on-non-gallery-applications)참조하십시오. 

![SSO SAML 사용자 스크린샷 ](media/migrate-adfs-apps-to-azure/sso-saml-user-attributes-claims.png)

 
대부분의 SaaS 응용 프로그램에는 SAML 기반 단일 사인온에 대한 구성을 단계별로 하는 [응용 프로그램별 자습서가](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list) 있습니다.

![앱 자습서](media/migrate-adfs-apps-to-azure/app-tutorial.png)

일부 앱은 쉽게 마이그레이션할 수 있습니다. 사용자 지정 클레임과 같이 더 복잡한 요구 사항이 있는 앱의 경우 Azure AD 및/또는 Azure AD Connect에서 추가로 구성해야 할 수도 있습니다. 지원되는 클레임 매핑에 대한 자세한 내용은 [Azure Active Directory의 클레임 매핑을](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping)참조하십시오.

특성을 매핑할 때 다음과 같은 제한 사항에 유의하십시오.

* AD FS에서 발급할 수 있는 모든 특성이 해당 특성이 동기화된 경우에도 SamL 토큰에 내보전하는 특성으로 Azure AD에 표시되지는 않습니다. 특성을 편집하면 값 드롭다운 목록에 Azure AD에서 사용할 수 있는 다양한 특성이 표시됩니다. [Azure AD Connect 동기화](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis) 구성을 확인하여 필요한 특성(예: samAccountName)이 Azure AD에 동기화되고 있는지 확인합니다. 확장 특성을 사용하여 Azure AD의 표준 사용자 스키마에 속하지 않는 클레임을 내보를 내도록 할 수 있습니다.

* 가장 일반적인 시나리오에서는 NameID 클레임 및 다른 일반 사용자 식별자 클레임만 앱에 필요합니다. 추가 클레임이 필요한지 확인하려면 AD FS에서 발급하는 클레임을 검토합니다.

* 일부 클레임은 Azure AD에서 보호되기 때문에 모든 클레임이 문제가 될 수 있는 것은 아닙니다. 

* 암호화된 SAML 토큰을 사용할 수 있는 기능이 이제 미리 보기상태입니다. [방법: 엔터프라이즈 응용 프로그램에 대한 SAML 토큰에서 발급된 클레임 사용자 지정](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)을 참조하십시오.

 

### <a name="software-as-a-service-saas-apps"></a>서비스로서의 소프트웨어(SaaS) 앱

사용자가 Salesforce, ServiceNow 또는 Workday와 같은 SaaS 앱에 로그인하고 AD FS와 통합된 경우 SaaS 앱에 페더레이션 사인온을 사용하는 것입니다. 

대부분의 SaaS 응용 프로그램은 Azure AD에서 이미 구성할 수 있습니다. Microsoft는 [Azure AD](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps)앱 갤러리에서 SaaS 앱에 대한 많은 사전 구성된 연결을 통해 전환을 더 쉽게 만듭니다. SAML 2.0 응용 프로그램은 Azure AD 앱 갤러리를 통해 또는 [비갤러리 응용 프로그램으로](https://docs.microsoft.com/azure/active-directory/manage-apps/add-non-gallery-app)Azure AD와 통합될 수 있습니다. 

OAuth 2.0 또는 OpenID Connect를 사용하는 앱은 [앱 등록과](https://docs.microsoft.com/azure/active-directory/develop/app-registrations-training-guide-for-app-registrations-legacy-users)마찬가지로 Azure AD와 통합될 수 있습니다. 레거시 프로토콜을 사용하는 앱은 [Azure AD 응용 프로그램 프록시를](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) 사용하여 Azure AD로 인증할 수 있습니다.

SaaS 앱 온보딩과 관련된 문제가 있는 경우 [SaaS 응용 프로그램 통합 지원 별칭에](mailto:SaaSApplicationIntegrations@service.microsoft.com)문의할 수 있습니다.

**SSO에 대한 SAML 서명 인증서**: 인증서 서명은 모든 SSO 배포에서 중요한 부분입니다. Azure AD는 SaaS 응용 프로그램에 SAML 기반 페더레이션 된 SSO를 설정 하기 위해 서명 인증서를 만듭니다. 갤러리 또는 비갤러리 응용 프로그램을 추가하면 페더레이션된 SSO 옵션을 사용하여 추가된 응용 프로그램을 구성합니다. [Azure Active Directory에서 페더레이션된 단일 사인온에 대한 인증서 관리를](https://docs.microsoft.com/azure/active-directory/manage-apps/manage-certificates-for-federated-single-sign-on)참조하십시오.

### <a name="apps-and-configurations-that-can-be-moved-today"></a>현재 이동할 수 있는 앱 및 구성

오늘날 쉽게 이동할 수 있는 앱에는 구성 요소 및 클레임의 표준 집합을 사용하는 SAML 2.0 앱이 포함됩니다.

* 사용자 계정 이름

* 메일 주소

* 이름

* Surname

* SAML **NameID**로 대체되는 특성(Azure AD 메일 특성, 메일 접두사, 직원 ID, 확장 특성(1-15) 또는 온-프레미스 **SamAccountName** 특성 포함). 자세한 내용은 [NameIdentifier 클레임 편집](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)을 참조하세요.

* 사용자 지정 클레임.

다음은 Azure AD로 마이그레이션하기 위한 추가 구성 단계가 필요합니다.

* AD FS의 사용자 지정 권한 부여 또는 MFA(다단계 인증) 규칙입니다. [Azure AD 조건부 액세스](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal) 기능을 사용하여 구성합니다.

* 회신 URL 끝점이 여러 개인 앱입니다. PowerShell또는 Azure 포털 인터페이스를 사용하여 Azure AD에서 구성합니다.

* SAML 버전 1.1 토큰이 필요한 WS-Federation 앱(예: SharePoint 앱). PowerShell을 사용하여 수동으로 구성할 수 있습니다. 갤러리에서 SharePoint 및 SAML 1.1 응용 프로그램에 대한 사전 통합된 일반 템플릿을 추가할 수도 있습니다. SAML 2.0 프로토콜을 지원합니다.

* 복잡한 클레임 발급은 규칙을 변환합니다. 지원되는 클레임 매핑에 대한 자세한 내용은 다음을 참조하십시오.
   *  [Azure Active Directory의 클레임 매핑](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping) 
   * [Azure Active Directory의 엔터프라이즈 애플리케이션에 SAML 토큰에서 발급된 클레임 사용자 지정](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)

 

### <a name="apps-and-configurations-not-supported-in-azure-ad-today"></a>현재 Azure AD에서 지원되지 않는 앱 및 구성

다음 기능이 필요한 앱은 현재 마이그레이션할 수 없습니다.

**프로토콜 기능**

* WS-Trust ActAs 패턴 지원

* SAML 아티팩트 확인

* 서명된 SAML 요청의 서명 확인  
서명된 요청은 수락되지만 서명은 확인되지 않습니다.  
Azure AD가 응용 프로그램에서 미리 구성된 끝점으로만 토큰을 반환한다는 점을 감안할 때 대부분의 경우 서명 확인이 필요하지 않을 수 있습니다.

**토큰 기능의 클레임**

* 데이터가 Azure AD에 동기화되지 않는 한 Azure AD 디렉터리 이외의 특성 저장소의 클레임입니다. 자세한 내용은 Azure [AD 동기화 API 개요를](https://docs.microsoft.com/graph/api/resources/synchronization-overview?view=graph-rest-beta)참조하십시오.

* 디렉터리 다중 값 특성의 발급입니다. 예를 들어 현재 프록시 주소에 대한 다중 값 클레임을 발행할 수 없습니다.

## <a name="map-app-settings-from-ad-fs-to-azure-ad"></a>AD FS에서 Azure AD로 앱 설정 매핑

마이그레이션은 온-프레미스에서 애플리케이션을 구성하는 방법을 평가하고 해당 구성을 Azure AD에 매핑하는 것으로 시작됩니다. AD FS 및 Azure AD도 마찬가지로 작동하므로 트러스트, 로그인 및 로그아웃 URL 및 식별자를 구성하는 개념이 두 경우 모두에 적용됩니다. Azure AD에서 쉽게 구성할 수 있도록 응용 프로그램의 AD FS 구성 설정을 문서화합니다.

### <a name="map-app-configuration-settings"></a>앱 구성 설정 매핑

다음 표에서는 AD FS 신뢰에서 Azure AD 엔터프라이즈 응용 프로그램에 대한 신뢰 자 간의 가장 일반적인 설정 매핑에 대해 설명합니다.

* AD FS - 응용 프로그램에 대한 AD FS 의존 파티 신뢰에서 설정을 찾을 수 있습니다. 의존 당사자를 마우스 오른쪽 단추로 클릭하고 속성을 선택합니다. 

* Azure AD: 설정은 각 응용 프로그램의 Single 사인온 속성에서 [Azure 포털](https://portal.azure.com/) 내에서 구성됩니다.

| 구성 설정| AD FS| Azure AD에서 구성하는 방법| SAML 토큰 |
| - | - | - | - |
| **앱 로그온 URL** <p>SP(서비스 공급자)가 시작한 SAML 흐름에서 사용자가 앱에 로그인할 수 있는 URL입니다.| 해당 없음| SAML 기반 사인온에서 기본 SAML 구성 열기| 해당 없음 |
| **앱 회신 URL** <p>ID 공급자(IdP) 관점에서 앱의 URL입니다. IdP는 사용자가 IdP에 로그인한 후 여기에 사용자와 토큰을 보냅니다.  **이를 SAML 어설션 소비자 끝점이라고도**합니다.| **끝점** 탭 선택| SAML 기반 사인온에서 기본 SAML 구성 열기| SAML 토큰의 대상 요소입니다. 예제 값:[https://contoso.my.salesforce.com](https://contoso.my.salesforce.com/) |
| **앱 로그아웃 URL** <p>사용자가 앱에서 로그아웃할 때 "로그아웃 정리" 요청이 전송되는 URL입니다. IdP는 다른 모든 앱에서도 사용자를 로그아웃하도록 요청을 보냅니다.| **끝점** 탭 선택| SAML 기반 사인온에서 기본 SAML 구성 열기| 해당 없음 |
| **앱 식별자** <p>IdP의 관점에서 앱 식별자입니다. 로그인 URL 값은 식별자(항상 은 아님)에 자주 사용됩니다.  경우에 따라 앱에서 이를 "엔터티 ID"라고 부릅니다.| **식별자** 탭 선택|SAML 기반 사인온에서 기본 SAML 구성 열기| SAML 토큰의 **Audience** 요소에 매핑합니다. |
| **앱 페더레이션 메타데이터** <p>앱의 페더레이션 메타데이터의 위치입니다. IdP에서 엔드포인트 또는 암호화 인증서와 같은 특정 구성 설정을 자동으로 업데이트하는 데 사용합니다.| **모니터링** 탭 선택| 해당 없음. Azure AD는 응용 프로그램 페더레이션 메타데이터를 직접 사용하는 것을 지원하지 않습니다. 페더레이션 메타데이터를 수동으로 가져올 수 있습니다.| 해당 없음 |
| **사용자 식별자/ 이름 ID** <p>Azure AD 또는 AD FS의 사용자 ID를 앱에 고유하게 표시하는 데 사용되는 특성입니다.  이 특성은 일반적으로 UPN 또는 사용자의 전자 메일 주소입니다.| 클레임 규칙. 대부분의 경우 클레임 규칙은 NameIdentifier로 끝나는 형식의 클레임을 발행합니다.| 헤더 사용자 속성 및 클레임 에서 식별자를 찾을 수 **있습니다.** 기본적으로 UPN은| SAML 토큰의 **NameID** 요소에 매핑합니다. |
| **기타 클레임** <p>IdP에서 앱으로 일반적으로 전송되는 기타 클레임 정보의 예로는 이름, 성, 이메일 주소 및 그룹 구성원이 있습니다.| AD FS에서는 신뢰 당사자에 대한 다른 클레임 규칙으로 찾을 수 있습니다.| 헤더 사용자 특성 & 클레임 에서 식별자를 찾을 수 **있습니다.** **보기**를 선택하고 다른 모든 사용자 특성을 편집합니다.| 해당 없음 |


### <a name="map-identity-provider-idp-settings"></a>IdP(IDP) 설정 매핑

SSO에 대한 Azure AD와 AD FS를 가리키도록 응용 프로그램을 구성합니다. 여기서는 SAML 프로토콜을 사용하는 SaaS 앱에 중점을 두고 있습니다. 그러나 이 개념은 사용자 지정 LOB 앱에도 확장됩니다.

> [!NOTE]
> Azure AD의 구성 값은 Azure 테넌트 ID가 {테넌트 ID}를 대체하고 응용 프로그램 ID가 {응용 프로그램 id}를 대체하는 패턴을 따릅니다. Azure Active 디렉터리 > 속성 [아래의 Azure 포털에서](https://portal.azure.com/) 이 정보를 찾을 수 있습니다. 

* 디렉토리 ID를 선택하여 테넌트 ID를 확인합니다. 

* 응용 프로그램 ID를 선택하여 응용 프로그램 ID를 확인합니다.

 상위 수준에서 다음 주요 SaaS 앱 구성 요소를 Azure AD에 매핑합니다. 

 

| 요소| 구성 값 |
| - | - |
| ID 공급자 발급자| [https://sts.windows.net/{tenant-id}/](https://sts.windows.net/{tenant-id}/) |
| ID 공급자 로그인 URL| [https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) |
| ID 공급자 로그아웃 URL| [https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) |
| 페더레이션 메타데이터 위치| [https://login.windows.net/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={application-id}](https://login.windows.net/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={application-id}) |


### <a name="map-sso-settings-for-saas-apps"></a>SaaS 앱에 대한 지도 SSO 설정

SaaS 앱은 인증 요청을 보낼 위치와 수신된 토큰의 유효성을 검사하는 방법을 알아야 합니다. 다음 표에서는 앱에서 SSO 설정을 구성하는 요소와 AD FS 및 Azure AD 내의 해당 값 또는 위치에 대해 설명합니다.

| 구성 설정| AD FS| Azure AD에서 구성하는 방법 |
| - | - | - |
| **IDP 사인온 URL** <p>앱의 관점에서 IdP의 로그온 URL(사용자가 로그인으로 리디렉션되는 위치).| AD FS 사인온 URL은 AD FS 페더레이션 서비스 이름 다음에 "/adfs/ls/"입니다. <p>예를 들어:[https://fs.contoso.com/adfs/ls/](https://fs.contoso.com/adfs/ls/)| {테넌트 id}를 테넌트 ID로 바꿉습니다. <p> SAML-P 프로토콜을 사용하는 앱의 경우:[https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) <p>WS-페더레이션 프로토콜을 사용하는 앱의 경우:[https://login.microsoftonline.com/{tenant-id}/wsfed](https://login.microsoftonline.com/{tenant-id}/wsfed) |
| **IdP 로그아웃 URL**<p>앱의 관점에서 IdP의 로그아웃 URL(사용자가 앱에서 로그아웃하도록 선택할 때 리디렉션됨).| 로그아웃 URL은 로그온 URL과 동일하거나 "wa=wsignout1.0"이 추가된 동일한 URL입니다. 예를 들어:[https://fs.contoso.com/adfs/ls/?wa=wsignout1.0](https://fs.contoso.com/adfs/ls/?wa=wsignout1.0)| {테넌트 id}를 테넌트 ID로 바꿉습니다.<p>SAML-P 프로토콜을 사용하는 앱의 경우:<p>[https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) <p> WS-페더레이션 프로토콜을 사용하는 앱의 경우:[https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0](https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0) |
| **토큰 서명 인증서**<p>IdP는 인증서의 개인 키를 사용하여 발급된 토큰에 서명합니다. 앱이 신뢰하도록 구성된 것과 동일한 IdP에서 토큰이 제공되었는지 확인합니다.| AD FS 토큰 서명 인증서는 AD FS 관리의 **인증서** 아래에 있습니다.| **헤더 SAML 서명 인증서**아래 응용 프로그램의 단일 **사인온 속성에서** Azure 포털에서 찾을 수 있습니다. 여기서는 앱에 업로드할 인증서를 다운로드할 수 있습니다.  <p>응용 프로그램에 두 개 이상의 인증서가 있는 경우 페더레이션 메타데이터 XML 파일에서 모든 인증서를 찾을 수 있습니다. |
| **식별자/ "발급자"**<p>앱의 관점에서 IdP식별자("발급자 ID"라고도 함).<p>SAML 토큰에서 값은 발급자 요소로 나타납니다.| AD FS의 식별자는 일반적으로 서비스 > 편집 페더레이션 **서비스 속성에서**AD FS 관리의 페더레이션 서비스 식별자입니다. 예를 들어:[http://fs.contoso.com/adfs/services/trust](http://fs.contoso.com/adfs/services/trust)| {테넌트 id}를 테넌트 ID로 바꿉습니다.<p>[https://sts.windows.net/{tenant-id}/](https://sts.windows.net/{tenant-id}/) |
| **IdP 페더레이션 메타데이터**<p>IdP에서 공개적으로 사용할 수 있는 페더레이션 메타데이터의 위치입니다. (일부 앱은 URL, 식별자 및 토큰 서명 인증서를 개별적으로 구성하는 관리자 대신 연합 메타데이터를 사용합니다.)| **서비스 > 엔드포인트 > 메타데이터 > 유형: 페더레이션 메타데이터**에서 AD FS 페더레이션 메타데이터 URL을 찾습니다. 예를 들어:[https://fs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml](https://fs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml)| Azure AD에 대한 해당 [https://login.microsoftonline.com/{TenantDomainName}/FederationMetadata/2007-06/FederationMetadata.xml](https://login.microsoftonline.com/{TenantDomainName}/FederationMetadata/2007-06/FederationMetadata.xml)값은 패턴을 따릅니다. {TenantDomainName} 를 "contoso.onmicrosoft.com" 형식의 테넌트 이름으로 바꿉니다.   <p>자세한 내용은 [페더레이션 메타데이터](https://docs.microsoft.com/azure/active-directory/azuread-dev/azure-ad-federation-metadata)를 참조하세요. |


## <a name="represent-ad-fs-security-policies-in-azure-ad"></a>Azure AD에서 AD FS 보안 정책을 나타냅니다.

앱 인증을 Azure AD로 이동할 때 기존 보안 정책에서 Azure AD에서 사용할 수 있는 동등한 또는 대체 변형으로 매핑을 만듭니다. 앱 소유자가 요구하는 보안 표준을 충족하는 동안 이러한 매핑을 수행할 수 있도록 하면 나머지 앱 마이그레이션이 훨씬 쉬워집니다.

각 규칙 유형 및 해당 예제에 대해 AD FS, AD FS 규칙 언어 등가 코드 및 Azure AD에서 이 맵의 모양이 다음과 같습니다.

### <a name="map-authorization-rules"></a>지도 권한 부여 규칙

다음은 AD FS의 권한 부여 규칙 유형및 이를 Azure AD에 매핑하는 방법의 예입니다.

#### <a name="example-1-permit-access-to-all-users"></a>예 1: 모든 사용자에 대한 액세스 허용

모든 사용자에 대한 액세스 허용 AD FS에서처럼 보입니다. 

![마이그레이션 단계 1 ](media/migrate-adfs-apps-to-azure/sso-saml-user-attributes-claims.png)


이 맵은 다음 방법 중 하나로 Azure AD에 매핑됩니다.

Azure [포털에서:](https://portal.azure.com/)
* 옵션 1: 사용자 할당을 아니요로 설정 ![SaaS 앱에 대한 액세스 제어 정책 편집 ](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-2.png)

    사용자 할당을 예로 전환해야 하므로 사용자가 액세스 권한을 얻으려면 응용 프로그램에 할당해야 합니다. 아니요로 설정하면 모든 사용자가 액세스할 수 있습니다. 이 스위치는 내 앱 환경에서 사용자에게 표시되는 내용을 제어하지 않습니다. 

 
* 옵션 2: 사용자 및 그룹 탭에서 응용 프로그램을 "모든 사용자" 자동 그룹에 할당합니다. <p>
Azure AD 테넌트에서 기본 '모든 사용자' 그룹을 사용할 수 있도록 [설정해야](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule) 합니다.

   ![Azure AD의 내 SaaS 앱 ](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-3.png)


#### <a name="example-2-allow-a-group-explicitly"></a>예제 2: 그룹을 명시적으로 허용

AD FS의 명시적 그룹 권한 부여:


![발급 권한 부여 규칙 ](media/migrate-adfs-apps-to-azure/allow-a-group-explicitly-1.png)


규칙이 Azure AD에 매핑하는 방법은 다음과 입니다.

Azure [포털에서](https://portal.azure.com/)먼저 AD FS의 사용자 그룹에 해당하는 사용자 [그룹을 만든](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal) 다음 해당 그룹에 앱 권한을 할당합니다.

![과제 추가 ](media/migrate-adfs-apps-to-azure/allow-a-group-explicitly-2.png)


#### <a name="example-3-authorize-a-specific-user"></a>예 3: 특정 사용자에게 권한 부여

AD FS의 명시적 사용자 권한 부여:

![발급 권한 부여 규칙 ](media/migrate-adfs-apps-to-azure/authorize-a-specific-user-1.png)

규칙이 Azure AD에 매핑하는 방법은 다음과 입니다.

Azure [포털에서](https://portal.azure.com/)다음과 같이 앱의 할당 추가 탭을 통해 사용자를 앱에 추가합니다.

![Azure의 내 SaaS 앱 ](media/migrate-adfs-apps-to-azure/authorize-a-specific-user-2.png)

 
### <a name="map-multi-factor-authentication-rules"></a>다단계 인증 규칙 매핑 

[MFA(다단계 인증)](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication) 및 AD FS의 온프레미스 배포는 AD FS와 페더레이션되므로 마이그레이션 후에도 계속 작동합니다. 그러나 Azure AD의 조건부 액세스 워크플로에 연결된 Azure의 기본 제공 MFA 기능으로 마이그레이션하는 것이 좋습니다. 

다음은 AD FS의 MFA 규칙 유형과 다양한 조건에 따라 Azure AD에 매핑하는 방법의 예입니다.

AD FS의 MFA 규칙 설정:

![Azure AD MFA 설정](media/migrate-adfs-apps-to-azure/mfa-location-1.png)


#### <a name="example-1-enforce-mfa-based-on-usersgroups"></a>예 1: 사용자/그룹에 따라 MFA 적용

사용자/그룹 선택기는 그룹별(그룹 SID) 또는 사용자당(기본 SID) 기준으로 MFA를 적용할 수 있는 규칙입니다. 사용자/그룹 할당 외에도 AD FS MFA 구성 UI의 모든 추가 확인란은 사용자/그룹 규칙이 적용된 후 평가되는 추가 규칙으로 작동합니다. 


Azure AD에서 사용자 또는 그룹에 대한 MFA 규칙을 지정합니다.

1. 새 [조건부 액세스 정책을](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-azure-mfa?toc=/azure/active-directory/conditional-access/toc.json&bc=/azure/active-directory/conditional-access/breadcrumb/toc.json)만듭니다.

2. **할당을 선택합니다.** MFA를 적용할 사용자 또는 그룹을 추가합니다.

3. 아래와 같이 **액세스 제어** 옵션을 구성합니다.  
‎

![AAD MFA 설정](media/migrate-adfs-apps-to-azure/mfa-usersorgroups.png)

 
 #### <a name="example-2-enforce-mfa-for-unregistered-devices"></a>예 2: 등록되지 않은 장치에 대해 MFA 적용

Azure AD에서 등록되지 않은 장치에 대한 MFA 규칙을 지정합니다.

1. 새 [조건부 액세스 정책을](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-azure-mfa?toc=/azure/active-directory/conditional-access/toc.json&bc=/azure/active-directory/conditional-access/breadcrumb/toc.json)만듭니다.

2. 할당을 모든 **사용자에게** **설정합니다.**

3. 아래와 같이 **액세스 제어** 옵션을 구성합니다.  
‎

![AAD MFA 설정](media/migrate-adfs-apps-to-azure/mfa-unregistered-devices.png)

 
여러 컨트롤을 선택된 컨트롤 중 하나를 요구하도록 설정하면 확인란에 지정된 조건 중 하나가 사용자에 의해 충족되면 앱에 대한 액세스 권한이 부여됩니다.

#### <a name="example-3-enforce-mfa-based-on-location"></a>예 3: 위치에 따라 MFA 적용

Azure AD에서 사용자의 위치에 따라 MFA 규칙을 지정합니다.

1. 새 [조건부 액세스 정책을](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-azure-mfa?toc=/azure/active-directory/conditional-access/toc.json&bc=/azure/active-directory/conditional-access/breadcrumb/toc.json)만듭니다.

1. 할당을 모든 **사용자에게** **설정합니다.**

1. [Azure AD에서 명명된 위치를 구성하는](https://docs.microsoft.com/azure/active-directory/active-directory-named-locations) 경우 회사 네트워크 내에서 페더레이션을 신뢰할 수 있습니다. 

1. 조건 **규칙을** 구성하여 MFA를 적용할 위치를 지정합니다.

![Azure AD MFA 설정](media/migrate-adfs-apps-to-azure/mfa-location-1.png)

5. 아래와 같이 **액세스 제어** 옵션을 구성합니다.


![맵 액세스 제어 정책](media/migrate-adfs-apps-to-azure/mfa-location-2.png)

 
### <a name="map-emit-attributes-as-claims-rule"></a>클레임 규칙으로 내시 속성 매핑

다음은 AD FS에서 특성이 매핑되는 방법의 예입니다.

![Azure AD MFA 설정](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claimsrule-1.png)


규칙이 Azure AD에 매핑하는 방법은 다음과 입니다.

Azure [포털에서](https://portal.azure.com/) **엔터프라이즈 응용 프로그램**, 단일 **사인온**을 선택하고 아래와 같이 **SAML 토큰 특성을 추가합니다.**

![Azure AD MFA 설정](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claimsrule-2.png)



### <a name="map-built-in-access-control-policies"></a>기본 제공 액세스 제어 정책 매핑

AD FS 2016에는 다음과 같은 몇 가지 기본 제공 액세스 제어 정책을 선택할 수 있습니다.

![액세스 제어에 내장된 Azure AD](media/migrate-adfs-apps-to-azure/map-builtin-access-control-policies-1.png)

 
Azure AD에서 기본 제공 정책을 구현하려면 [새 조건부 액세스 정책을](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-azure-mfa?toc=/azure/active-directory/conditional-access/toc.json&bc=/azure/active-directory/conditional-access/breadcrumb/toc.json) 사용하고 액세스 제어를 구성하거나 AD FS 2016의 사용자 지정 정책 디자이너를 사용하여 액세스 제어 정책을 구성할 수 있습니다. 규칙 편집기에는 모든 종류의 순열을 만드는 데 도움이 되는 허가 및 제외 옵션의 전체 목록이 있습니다.

![Azure AD 액세스 제어 정책](media/migrate-adfs-apps-to-azure/map-builtin-access-control-policies-2.png)



이 표에서는 몇 가지 유용한 허가 및 제외 옵션과 Azure AD에 매핑하는 방법을 나열했습니다. 


| | Azure AD에서 허가 옵션을 구성하는 방법은 무엇입니까?| Azure AD에서 제외 옵션을 구성하는 방법은 무엇입니까? |
| - | - | - |
| 특정 네트워크| Azure AD의 [명명된 위치에](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations) 대한 지도| [신뢰할 수 있는 위치에](https://docs.microsoft.com/azure/active-directory/conditional-access/location-condition) 대해 **제외** 옵션을 사용합니다. |
| 특정 그룹| [사용자/그룹 할당 설정](https://docs.microsoft.com/azure/active-directory/manage-apps/assign-user-or-group-access-portal)| 사용자 및 그룹에서 **제외** 옵션 사용 |
| 특정 신뢰 수준이 있는 장치에서| 할당 -> 조건에서 '장치 상태' 컨트롤에서 설정합니다.| 장치 상태 조건에서 **제외** 옵션을 사용하고 **모든 장치** 포함 |
| 요청의 특정 클레임| 이 설정을 마이그레이션할 수 없습니다.| 이 설정을 마이그레이션할 수 없습니다. |


Azure Portal에서 신뢰할 수 있는 위치에 대해 제외 옵션을 구성하는 방법의 예는 다음과 같은 것입니다.

![매핑 액세스 제어 정책 스크린샷](media/migrate-adfs-apps-to-azure/map-builtin-access-control-policies-3.png)



## <a name="transition-users-from-ad-fs-to-azure-ad"></a>AD FS에서 Azure AD로 사용자 전환

### <a name="sync-ad-fs-groups-in-azure-ad"></a>Azure AD에서 AD FS 그룹 동기화

권한 부여 규칙을 매핑할 때 AD FS로 인증하는 앱은 권한에 Active Directory 그룹을 사용할 수 있습니다. 이러한 경우 응용 프로그램을 마이그레이션하기 전에 [Azure AD Connect를](https://go.microsoft.com/fwlink/?LinkId=615771) 사용하여 이러한 그룹을 Azure AD와 동기화합니다. 응용 프로그램을 마이그레이션할 때 동일한 사용자에게 액세스 권한을 부여할 수 있도록 마이그레이션 하기 전에 해당 그룹 및 구성원 을 확인 해야 합니다.

자세한 내용은 [Active Directory에서 동기화된 그룹 특성을 사용하기 위한 필수 구성 조건을](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-fed-group-claims)참조하십시오.

### <a name="setup-user-self-provisioning"></a>사용자 자체 프로비저닝 설정 

일부 SaaS 응용 프로그램은 응용 프로그램에 처음 로그인할 때 사용자를 자체 프로비전하는 기능을 지원합니다. Azure Active Directory(Azure AD)에서 앱 프로비저닝이라는 용어는 사용자가 액세스해야 하는[클라우드(SaaS)](https://azure.microsoft.com/overview/what-is-saas/)응용 프로그램에서 사용자 ID 및 역할을 자동으로 만드는 것을 의미합니다. 마이그레이션된 사용자는 이미 SaaS 응용 프로그램에 계정을 갖게 됩니다. 마이그레이션 후 추가된 모든 새 사용자는 프로비전해야 합니다. 응용 프로그램이 마이그레이션되면 [SaaS 앱 프로비전을](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning) 테스트합니다.

### <a name="sync-external-users-in-azure-ad"></a>Azure AD에서 외부 사용자 동기화

기존 외부 사용자는 AD FS 내에서 두 가지 주요 방법으로 설정할 수 있습니다.

#### <a name="external-users-with-a-local-account-within-your-organization"></a>조직 내에 로컬 계정이 있는 외부 사용자

내부 사용자 계정이 작동하는 것과 동일한 방식으로 이러한 계정을 계속 사용할 수 있습니다. 이러한 외부 사용자 계정에는 조직 내에서 원칙 이름이 있지만 계정의 전자 메일은 외부를 가리킬 수 있습니다. 마이그레이션을 진행하면서 [Azure AD B2B가](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b) 이러한 ID를 사용할 수 있을 때 해당 사용자를 자신의 회사 ID를 사용하도록 마이그레이션하여 제공하는 이점을 활용할 수 있습니다. 이렇게 하면 해당 사용자가 자체 회사 로그온으로 로그인하는 경우가 많기 때문에 로그인 프로세스가 간소화됩니다. 외부 사용자의 계정을 더 이상 관리할 필요가 없어 조직의 관리도 완화됩니다.

#### <a name="federated-external-identities"></a>페더레이션된 외부 ID

현재 외부 조직과 페더레이션을 하는 경우 다음과 같은 몇 가지 방법을 사용할 수 있습니다.

* [Azure 포털에서 Azure Active Directory B2B 공동 작업 사용자를 추가합니다.](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator) Azure AD 관리 포털에서 개별 구성원이 사용 하 던 앱 및 자산을 계속 사용 하 여 파트너 조직에 B2B 공동 작업 초대를 사전에 보낼 수 있습니다. 

* B2B 초대 API를 사용하여 파트너 조직의 개별 사용자에 대한 요청을 생성하는 [셀프 서비스 B2B 등록 워크플로를 만듭니다.](https://docs.microsoft.com/azure/active-directory/b2b/self-service-portal)

기존 외부 사용자가 구성된 방식에 관계없이 그룹 구성원 또는 특정 사용 권한에서 계정과 연결된 사용 권한이 있을 수 있습니다. 이러한 사용 권한을 마이그레이션하거나 정리해야 하는지 여부를 평가합니다. 외부 사용자를 나타내는 조직 내의 계정은 사용자가 외부 ID로 마이그레이션된 후 비활성화해야 합니다. 리소스에 연결하는 기능이 중단될 수 있으므로 마이그레이션 프로세스를 비즈니스 파트너와 논의해야 합니다.

## <a name="migrate-and-test-your-apps"></a>앱 마이그레이션 및 테스트

이 문서에서 자세히 설명한 마이그레이션 프로세스를 따릅니다.

그런 다음 [Azure 포털로](https://aad.portal.azure.com/) 이동하여 마이그레이션이 성공했는지 테스트합니다. 아래의 지침을 따르세요.
1. **엔터프라이즈 응용 프로그램을** > 선택하고 모든 응용 프로그램을 선택하고 목록에서 앱을**찾습니다.**

1. **사용자 및 그룹** **관리를** > 선택하여 앱에 하나 이상의 사용자 또는 그룹을 할당합니다.

1. **조건부 액세스** **관리를** > 선택합니다. 정책 목록을 검토하고 [조건부 액세스 정책을](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)사용하여 응용 프로그램에 대한 액세스를 차단하지 않는지 확인합니다.

앱을 구성하는 방법에 따라 SSO가 제대로 작동하는지 확인합니다. 

| 인증 유형| 테스트 |
| - | - |
| OAuth / 오픈 ID 연결| **권한 > 엔터프라이즈 응용 프로그램을** 선택하고 앱의 사용자 설정에서 조직에서 사용할 응용 프로그램에 동의한지 확인합니다.  
‎ |
| SAML 기반 SSO| **단일 사인온**에서 찾을 수 있는 [SAML 설정 테스트](https://docs.microsoft.com/azure/active-directory/develop/howto-v1-debug-saml-sso-issues) 단추를 사용합니다.  
‎ |
| 암호 기반 SSO| 다운로드 및[확장에서](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction) [MyApps 보안 로그인을](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction)[-](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction)설치합니다. 이 확장을 사용하면 SSO 프로세스를 사용해야 하는 조직의 클라우드 앱을 시작할 수 있습니다.  
‎ |
| 애플리케이션 프록시| 커넥터가 실행되고 응용 프로그램에 할당되어 있는지 확인합니다. 추가 지원을 받으시면 [응용 프로그램 프록시 문제 해결 가이드를](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-troubleshoot) [ ](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-troubleshoot)방문하십시오.  
‎ |

> [!NOTE]
> 이전 AD FS 환경의 쿠키는 사용자의 컴퓨터에 계속 유지됩니다. 이러한 쿠키는 사용자가 이전 AD FS 로그인 환경과 새 Azure AD 로그인으로 이동될 수 있으므로 마이그레이션에 문제가 발생할 수 있습니다. 사용자 브라우저 쿠키를 수동으로 지우거나 스크립트를 사용해야 할 수 있습니다. 시스템 센터 구성 관리자 또는 유사한 플랫폼을 사용할 수도 있습니다.

### <a name="troubleshoot"></a>문제 해결

마이그레이션된 응용 프로그램의 테스트에서 오류가 있는 경우 기존 AD FS 사용 당사자로 돌아가기 전에 문제 해결이 첫 번째 단계일 수 있습니다. [Azure Active Directory의 응용 프로그램에 SAML 기반 단일 사인온을 디버깅하는 방법을](https://docs.microsoft.com/azure/active-directory/azuread-dev/howto-v1-debug-saml-sso-issues)참조하십시오.

### <a name="rollback-migration"></a>롤백 마이그레이션

마이그레이션에 실패하면 AD FS 서버에 기존 사용 당사자를 두고 사용 당사자에 대한 액세스를 제거하는 것이 좋습니다. 이렇게 하면 배포 중에 필요한 경우 빠른 대체가 허용됩니다.

### <a name="end-user-communication"></a>최종 사용자 통신

계획된 중단 기간 자체는 최소화할 수 있지만 AD FS에서 Azure AD로 잘라내면서 이러한 기간을 직원에게 사전에 전달할 계획입니다. 앱 경험에 피드백 버튼이 있는지 또는 문제에 대한 헬프 데스크에 대한 포인터가 있는지 확인합니다.

배포가 완료되면 사용자에게 성공적인 배포를 알리는 통신을 보내고 수행해야 하는 새로운 단계를 알려줄 수 있습니다.

* 사용자에게 [액세스 패널을](https://myapps.microsoft.com.com/) 사용하여 마이그레이션된 모든 응용 프로그램에 액세스하도록 지시합니다. 

* MFA 설정을 업데이트해야 할 수 있음을 사용자에게 알려줍니다. 

* 셀프 서비스 암호 재설정이 배포된 경우 사용자는 인증 방법을 업데이트하거나 확인해야 할 수 있습니다. [MFA](https://aka.ms/mfatemplates) 및 [SSPR](https://aka.ms/ssprtemplates) 최종 사용자 통신 템플릿을 참조하십시오.

외부 사용자와의 통신: 이 사용자 그룹은 일반적으로 문제가 발생했을 때 가장 큰 영향을 받습니다. 보안 자세에서 외부 파트너에 대한 다른 조건부 액세스 규칙 또는 위험 프로필 집합을 지시하는 경우 특히 그렇습니다. 외부 파트너가 클라우드 마이그레이션 일정을 인식하고 외부 공동 작업고유의 모든 흐름을 테스트하는 파일럿 배포에 참여하는 것이 권장되는 기간을 확보해야 합니다. 마지막으로 문제가 발생했을 경우 헬프 데스크에 액세스할 수 있는 방법이 있는지 확인합니다.

## <a name="next-steps"></a>다음 단계
[Azure AD로 응용 프로그램 인증 마이그레이션 읽기](https://aka.ms/migrateapps/whitepaper)<p>
[조건부 액세스](https://docs.microsoft.com/azure/active-directory/conditional-access/overview) 및 [MFA](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks) 설정
