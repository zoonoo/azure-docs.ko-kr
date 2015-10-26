<properties 
    pageTitle="Azure AD를 사용하여 모든 내 응용 프로그램에서 Single Sign-On(SSO)을 사용할 수 있습니다. | Microsoft Azure" 
    description="Azure Active Directory를 사용하여 ID의 범위 및 관리를 확장하는 방법에 대해 알아봅니다." 
    services="active-directory" 
    authors="markusvi"  
    documentationCenter="na" 
    manager="stevenpo"/>

<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="10/12/2015" 
    ms.author="markvi" />


# Azure AD를 사용하여 모든 내 응용 프로그램에서 Single Sign-On(SSO)을 사용할 수 있습니다.
이 문서는 조직에서 사용하는 응용 프로그램에 Azure Active Directory가 제공하는 비즈니스 가치에 대해 자세히 알아보려는 IT 의사 결정을 위한 용도입니다.

## 클라우드 기반 환경에서 앱 실행  

오늘날 클라우드 기반 환경에서 작업을 수행하는 데 수 천 가지 앱이 있습니다. IT 부서에서는 종종 조직에서 사용하는 SaaS 앱조차도 일부는 모릅니다. 대부분의 경우 앱 사용은 알려져 있지만 일정한 수준의 관리 및 보안에는 많은 비용과 시간이 소비됩니다. 많은 응용 프로그램이 엔터프라이즈 ID 시스템과 통합을 지원하지 않고 다른 경우 일회성 통합은 비용 및 복잡성으로 인해 회피됩니다.

이에 따라,

- 많은 조직은 관리되지 않는 응용 프로그램에 의한 기업 데이터에 대한 권한 없는 액세스, 데이터 누출 가능성 및 응용 프로그램에 내재된 다른 보안 위험에 대해 염려합니다. 어떤 앱이 몇 개나 사용되고 있는지 모르기 때문에 이러한 위험에 대응하기 위한 계획을 세우는 것조차 어려워 보입니다..
- 관리자는 사용자 환경에서 관리된 앱에서 도입된 모든 IAM 서비스 공급자를 개별적으로 관리해야 합니다. 사용자 및 그룹을 만들고 삭제할 뿐만 아니라 이러한 앱에 대한 액세스 권한을 부여하거나 취소하는 작업이 포함됩니다.
- 사용자가 다양한 자격 증명을 암기하여 작업하는 데 필요한 앱에 액세스해야 합니다. 잊어버린 암호는 조직 대부분의 운영 비용에서 거대한 비중을 차지합니다. 이러한 모든 문제에서 공통되는 것은 사용자의 생산성과 운영 비용에 부정적인 영향을 준다는 점입니다.  
 
## Azure Active Directory는 어떻게 지원합니까?
Azure Active Directory를 사용하면 이를 통해 기존 ID 인프라를 클라우드로 쉽게 확장하여 이러한 문제를 해결할 수 있습니다.

- 앱에 모바일 사용자의 범위 확장 
- 클라우드 앱에 대한 액세스 관리의 범위 확장 
- 사용자가 액세스하는 데 사용한 앱 검색


### 앱에 ID의 범위 확장 

기본적으로 Azure Active Directory는 Office 365, CRM Online 및 Intune와 같은 Azure 및 다른 Microsoft 온라인 서비스에서 호스팅되는 클라우드 앱에 SSO를 제공합니다.

이외에도 Google, Amazon, IBM 등과 같은 다른 공용 클라우드에서 앱에 대한 Single Sign-On용 ID broker일 수 있습니다. <br> 앱이 광범위하게 알려지거나 인기있는 경우 해당 앱을 Azure Active Directory의 앱 갤러리에 미리 통합합니다. 오늘날 앱 갤러리에서 미리 통합된 2500개의 앱을 찾을 수 있고 그 수는 지속적으로 증가하고 있습니다. <br> 앱 갤러리에서 미리 통합된 앱에 대 한 SSO를 구성하려면 몇 번 클릭합니다. 앱 갤러리에서 내 앱이 아직 나열되지 않았다면 어떻게 합니까? 마법사를 사용하여 Azure Active Directory에 공용 앱을 통합하고 이런 방식으로 SSO를 사용하도록 설정합니다. 자세한 정보는 [새로 획득한 SaaS 앱에 Azure Active Directory를 사용하여 Single Sign-On 배포](active-directory-single-sign-on-newly-acquired-saas-apps.md) 및 [기존 앱과 Azure Active Directory SSO 통합](active-directory-sso-integrate-existing-apps.md)을 참조하세요.<br> 조직에서 개발한 앱은 어떻습니까? 프로그램 개발자가 조직에서 개발한 앱을 쉽게 Azure Active Directory에 통합할 수 있도록 하는 관련된 설명서를 제공합니다. 이에 대해 자세히 알려면 [Azure AD 및 응용 프로그램: 개발자 안내](active-directory-applications-guiding-developers-for-lob-applications.md)를 참조하세요.

앱 갤러리에 없는 미리 통합된 앱, 공용 앱 및 조직에서 개발한 앱에 대한 지원을 제공하는 Azure Active Directory를 사용하면 모든 클라우드 앱에 SSO를 제공할 수 있습니다.

Azure Active Directory의 값은 “그저” 클라우드 앱의 수준을 넘어섭니다. 또한 Azure Active Directory를 사용하여 SSO 통해 온-프레미스 앱에 대한 안전한 원격 액세스를 제공하여 Azure ID의 범위를 확장할 수 있습니다. 즉, 원격 액세스는 온-프레미스 웹앱에 액세스하는 데 VPN 또는 다른 기존 원격 액세스 인프라와 같은 기술이 필요하지 않습니다. Azure Active Directory는 앱 프록시 기능을 통해 이 기능을 제공합니다.

기존 SSO 모델은 두 개의 리포지토리에서 두 개의 개별 계정의 매핑을 기반으로 합니다. Azure Active Directory를 사용하여 회사 Twitter 계정과 같은 공유 회사 계정으로 개별 계정을 매핑할 수 있습니다. 회사 공유 계정에 SSO를 구현하여 사용자와 계정 자격 증명을 실제로 공유하지 않아도 되며 이는 이러한 계정 중 보호를 크게 향상시킵니다. 자세히 알고 싶다면 [Azure AD를 사용하여 계정 공유](active-directory-sharing-accounts.md)를 참조하세요.

ID의 범위를 확장하여 하나의 암호가 수 천 개의 앱에 액세스를 제공합니다.



### 클라우드 앱에 대한 액세스 관리의 범위 확장

이를 앱 단위에 따라 수동으로 수행해야 하는 경우 클라우드 앱에 대한 액세스 관리는 많은 비용이 듭니다. Azure Active Directory를 사용하여 중앙 지점-Azure 포털에서 그룹에 기반한 클라우드 앱에 대한 액세스를 관리할 수 있습니다. 개별 사용자 또는 그룹에도 액세스를 할당할 수 있습니다. 자세한 내용은 [앱에 대한 액세스 관리](active-directory-managing-access-to-apps.md)를 참조하세요.

Salesforce, Box, Google Apps 및 Concur 등의 일부 앱은 계정 만들기 및 제거(또는 비활성화)에 자동화 인터페이스를 제공합니다. 공급자가 이러한 인터페이스를 제공하는 경우 Azure Active Directory에서 활용됩니다. 즉, Azure Active Directory는 관련된 자동화 인터페이스를 제공하는 앱을 프로비전하는 자동화된 사용자를 지원합니다.

자동화된 사용자 프로비전을 사용하여

- 사용자가 관련된 앱에 대한 액세스 권한이 있을 때 Azure Active Directory은 자동으로 앱에 필요한 SSO 계정을 프로비전합니다.
- Azure Active Directory에서 사용자의 계정이 삭제되었을 때 앱에서 계정은 비활성화되거나 삭제됩니다. 공급자를 프로비전하는 자동화된 사용자를 구성하고 다음과 같은 이점을 제공합니다.
- 	IT 직원이 수행해야 하는 관리 작업을 자동화하기 때문에 운영 비용이 줄어듭니다.
- 필요하지 않은 앱에 액세스할 수 있는 가능성이 줄어들기 때문에 환경의 보안을 향상시킵니다.

자동된 사용자 프로비전에 대한 자세한 내용은 [Azure Active Directory를 사용하여 SaaS 앱에 사용자 프로비전 및 프로비전 해제 자동화](active-directory-saas-app-provisioning.md)를 참조하세요.


### 사용자가 액세스하는 데 사용한 앱 검색

SSO 및 계정을 프로비전하여 Azure Active Directory는 사용자 및 관리자가 인식하고 있는 환경에서 앱을 이용하여 어떻게 작업할 수 있는지를 개선하는 강력한 메커니즘을 제공합니다. 그러나 오늘날 기업에서 IT 부서가 사용되는 클라우드 앱의 일부를 인지하지 못하는 경우가 있습니다. 클라우드 앱 검색을 사용하여 Azure Active Directory는 이러한 앱을 검색하는 솔루션을 제공합니다. 클라우드 앱 검색으로 다음을 할 수 있습니다.

- 사용되고 있는 앱을 검색하고 사용자 수, 트래픽 볼륨 또는 앱에 대한 웹 요청 수로 사용 현황을 측정합니다.
- 앱을 사용하는 사용자를 식별합니다.
- 추가 오프라인 분석을 위해 데이터를 내보냅니다.
- 앱의 우선 순위를 정하여 IT 제어에 두고 앱을 쉽게 통합하여 Single Sign-on과 사용자 관리가 가능하게 합니다.

클라우드 앱 검색에 대한 자세한 내용은 [조직 내에서 사용되고 있는 허용되지 않은 클라우드 앱을 검색하는 방법](active-directory-cloudappdiscovery-whatis.md)을 참조하세요.


## 어떻게 시작하나요?

우선 Azure AD를 아직 사용하지 않는 IT 관리자인 경우입니다.

- [사용해 보기](https://azure.microsoft.com/trial/get-started-active-directory) - 지금 무료 30일 평가판에 등록하면 이 링크를 사용하여 5분 내에 첫 번째 클라우드 솔루션을 배포할 수 있습니다.

<!---HONumber=Oct15_HO3-->