---
title: Azure Active Directory 액세스 패널 배포 계획
description: Azure Active Directory 액세스 패널 배포에 대한 지침
services: active-directory
author: barbaraselden
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2019
ms.author: baselden
ms.reviewer: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 04d8b6c6d40aa81bf56baed59f90417f2147fa56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76897065"
---
# <a name="plan-an-azure-active-directory-access-panel-deployment"></a>Azure Active Directory 액세스 패널 배포 계획

Azure Active Directory(Azure AD) 액세스 패널은 지원 비용을 절감하고 생산성과 보안을 높이며 사용자 불만을 줄이는 데 도움이 되는 웹 기반 포털입니다. 시스템에는 시스템에 액세스할 때 추적하는 자세한 보고가 포함되어 있으며 관리자에게 오용 또는 남용을 알려줍니다.

Azure AD 액세스 패널을 사용하여 다음을 수행할 수 있습니다.

* 응용 프로그램과 같은 회사의 모든 Azure AD 연결 리소스 검색 및 액세스
* 새 앱 및 그룹에 대한 액세스 요청
* 다른 사람을 위해 이러한 리소스에 대한 액세스 관리
* 셀프 서비스 암호 재설정 및 Azure 다단계 인증 설정 관리
* 기기 관리

또한 관리자가 다음을 관리할 수 있습니다.

* 서비스 약관
* 조직
* 액세스 검토


## <a name="benefits-of-azure-ad-access-panel-integration"></a>Azure AD 액세스 패널 통합의 이점

Azure AD 액세스 패널은 다음과 같은 방법으로 비즈니스에 이점을 제공합니다.

**직관적인 사용자 환경 제공:** 액세스 패널은 SSO(Azure 단일 사인온) 연결된 모든 응용 프로그램에 대한 단일 플랫폼을 제공합니다. 통합 포털을 통해 그룹 관리 및 셀프 서비스 암호 재설정과 같은 기존 설정 및 새로운 기능을 추가할 수 있습니다. 직관적인 경험을 통해 사용자는 더 빠르게 작업하고 생산성을 높일 수 있으며 좌절감을 줄일 수 있습니다.

**생산성 향상**: 액세스 패널의 모든 사용자 응용 프로그램에SSO가 활성화되어 있습니다. 엔터프라이즈 응용 프로그램 및 Office 365에서 SSO를 사용하도록 설정하면 추가 로그인 프롬프트를 줄이거나 제거하여 뛰어난 로그인 환경을 제공합니다. 액세스 패널은 셀프 서비스 및 동적 멤버십을 사용하고 ID 시스템의 전반적인 보안을 향상시킵니다. 이를 위해 적합한 사람이 응용 프로그램에 대한 액세스를 관리하도록 합니다. 액세스 패널은 리소스를 빠르게 찾고 작업을 계속할 수 있는 일관된 방문 페이지 역할을 합니다.

**비용 관리**: Azure AD를 통해 액세스 패널을 사용하도록 설정하면 온-프레미스 인프라의 분리에 도움이 될 수 있습니다. 모든 앱을 찾고, 리소스에 대한 액세스를 요청하고, 계정을 관리할 수 있는 일관된 포털을 제공함으로써 지원 비용을 절감할 수 있습니다.

**유연성 및 보안 향상**: 액세스 패널을 사용하면 클라우드 플랫폼이 제공하는 보안 및 유연성에 액세스할 수 있습니다. 관리자는 응용 프로그램 및 리소스로 설정을 쉽게 변경할 수 있으며 사용자에게 영향을 주지 않고 새로운 보안 요구 사항을 수용할 수 있습니다.

**강력한 감사 및 사용 추적 활성화**: 모든 사용자 기능에 대한 감사 및 사용 추적을 통해 사용자가 리소스를 사용하는 시기를 알리고 보안을 평가할 수 있습니다.

### <a name="licensing-considerations"></a>라이선스 고려 사항

액세스 패널은 무료이며 기본 수준에서 사용할 라이센스가 필요하지 않습니다. 그러나 디렉터리에 있는 개체 수와 배포하려는 추가 기능에는 추가 라이선스가 필요할 수 있습니다. 라이선스 요구 사항이 있는 몇 가지 일반적인 Azure AD 시나리오에는 다음과 같은 보안 기능이 포함됩니다.

* [Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-how-it-works)

* [그룹 기반 멤버십](https://docs.microsoft.com/azure/active-directory/active-directory-manage-groups)

* [셀프 서비스 암호 재설정](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr)

* [Azure Active Directory ID 보호](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)

Azure [AD에 대한 전체 라이선스 가이드를](https://azure.microsoft.com/pricing/details/active-directory/)참조하십시오.

### <a name="prerequisites-for-deploying-the-azure-ad-access-panel"></a>Azure AD 액세스 패널 배포를 위한 필수 구성 조건

이 프로젝트를 시작하기 전에 다음 필수 구성 조건을 완료하십시오.

* [애플리케이션 SSO 통합](https://docs.microsoft.com/azure/active-directory/manage-apps/plan-sso-deployment)

* [Azure AD 사용자 및 그룹 인프라 관리](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-manage-groups)

## <a name="plan-the-azure-ad-access-panel-deployment"></a>Azure AD 액세스 패널 배포 계획

다음 표에서는 액세스 패널 배포의 주요 사용 사례를 간략하게 설명합니다.

| 영역| 설명 |
| - | - |
| 액세스 권한| 액세스 패널 포털은 회사 네트워크 내의 회사 및 개인 장치에서 액세스할 수 있습니다. |
|액세스 권한 | 액세스 패널 포털은 회사 네트워크 외부의 회사 장치에서 액세스할 수 있습니다. |
| 감사| 사용 데이터는 적어도 29일마다 회사 시스템에 다운로드됩니다. |
| 거버넌스| Azure AD 연결 응용 프로그램 및 그룹에 대한 사용자 할당의 수명 주기가 정의되고 모니터링됩니다. |
| 보안| 리소스에 대한 액세스는 사용자 및 그룹 할당을 통해 제어됩니다. 권한이 부여된 사용자만 리소스 액세스를 관리할 수 있습니다. |
| 성능| 액세스 할당 전파 타임라인이 문서화되고 모니터링됩니다. |
| 설치 환경| 사용자는 액세스 패널 기능과 사용 방법을 알고 있습니다.|
| 설치 환경| 사용자는 응용 프로그램 및 그룹에 대한 액세스를 관리할 수 있습니다.|
| 설치 환경| 사용자는 자신의 계정을 관리할 수 있습니다. |
| 설치 환경| 사용자는 브라우저 호환성을 알고 있습니다. |
| 고객 지원팀| 사용자는 액세스 패널 문제에 대한 지원을 찾을 수 있습니다. |

### <a name="best-practices-for-deploying-the-azure-ad-access-panel"></a>Azure AD 액세스 패널 배포모범 사례

액세스 패널의 기능을 점진적으로 활성화할 수 있습니다. 다음과 같은 배포 순서를 따르는 것이 좋습니다.

1. 내 앱
   * 앱 런처
   * 셀프 서비스 앱 관리
   * 마이크로소프트 오피스 365 통합

1. 셀프 서비스 앱 검색
   * 셀프 서비스 암호 재설정
   * 다단계 인증 설정
   * 디바이스 관리
   * 사용 약관
   * 조직 관리

1. 내 그룹
   * 셀프 서비스 그룹 관리
1. 액세스 검토
   * 액세스 검토 관리

내 앱부터 시작하여 사용자가 리소스에 액세스하는 일반적인 장소로 포털에 사용자를 소개합니다. 셀프 서비스 응용 프로그램 검색의 추가는 내 앱 환경을 기반으로 합니다. 내 그룹 및 액세스 검토는 셀프 서비스 기능을 기반으로 합니다.

### <a name="plan-configurations-for-the-azure-ad-access-panel"></a>Azure AD 액세스 패널에 대한 구성 계획

다음 표에는 몇 가지 중요한 액세스 패널 구성과 사용할 수 있는 일반적인 값이 나열되어 있습니다.

| Configuration| 일반적인 값 |
| - | - |
| 파일럿 그룹 결정| 사용할 Azure AD 보안 그룹을 식별하고 모든 파일럿 구성원이 그룹의 일부인지 확인합니다. |
| 프로덕션에 사용할 그룹 또는 그룹을 결정합니다.| 사용할 Azure AD 보안 그룹 또는 Azure AD에 동기화된 Active Directory 그룹을 식별합니다. 모든 파일럿 멤버가 그룹의 일부인지 확인합니다. |
| 사용자가 특정 유형의 응용 프로그램에 SSO를 사용할 수 있도록 허용| 페더레이션 된 SSO, OAuth, 암호 SSO, 앱 프록시 |
| 사용자가 셀프 서비스 암호 재설정을 사용할 수 있도록 허용 | yes |
| 사용자가 다단계 인증을 사용할 수 있도록 허용| yes |
| 사용자가 특정 유형의 그룹에 셀프 서비스 그룹 관리를 사용할 수 있도록 허용| 보안 그룹, Office 365 그룹 |
| 사용자가 셀프 서비스 앱 관리를 사용할 수 있도록 허용| yes |
| 사용자가 액세스 검토를 사용할 수 있도록 허용| yes |

### <a name="plan-consent-strategy"></a>계획 동의 전략

사용자 또는 관리자는 응용 프로그램의 사용 약관 및 개인 정보 보호 정책에 동의해야 합니다. 가능하면 비즈니스 규칙에 따라 관리자 동의를 사용하여 사용자에게 더 나은 환경을 제공합니다.

관리자 동의를 사용하려면 조직의 글로벌 관리자여야 하며 응용 프로그램은 다음 중 하나여야 합니다.

* 조직에 등록

* 다른 Azure AD 조직에 등록되어 있고 이전에 한 명 이상의 사용자가 동의한 경우

자세한 내용은 [최종 사용자가 Azure Active Directory의 응용 프로그램에 동의하는 방법 구성을](configure-user-consent.md)참조하십시오.

### <a name="engage-the-right-stakeholders"></a>올바른 이해 관계자 참여

기술 프로젝트가 실패하면 일반적으로 영향, 결과 및 책임에 대한 불일치 기대로 인해 그렇게 합니다. 이러한 위험을 방지하려면 [올바른 이해 관계자와](../fundamentals/active-directory-deployment-plans.md) 프로젝트의 이해 관계자 역할을 잘 이해해야 합니다.

### <a name="plan-communications"></a>통신 계획

통신은 새로운 서비스의 성공에 매우 중요합니다. 사용자에게 환경이 변경되고 필요한 경우 지원을 받는 방법과 시기를 사전에 알려줍니다.

액세스 패널은 일반적으로 사용자 문제를 만들지 않지만 준비하는 것이 중요합니다. 출시 전에 지원 담당자를 위한 모든 리소스 목록과 가이드를 만듭니다.

#### <a name="communications-templates"></a>통신 템플릿

Microsoft는 [액세스 패널에 대한 전자 메일 및 기타 통신에](https://aka.ms/APTemplates) 대한 사용자 지정 가능한 템플릿을 제공합니다. 이러한 자산을 다른 통신 채널에서 사용하도록 기업 문화에 맞게 적절하게 조정할 수 있습니다.

## <a name="plan-your-sso-configuration"></a>SSO 구성 계획

사용자가 응용 프로그램에 등록하면 인증 프로세스를 거치며 사용자가 누구인지 증명해야 합니다. SSO가 없으면 암호가 응용 프로그램에 저장되며 사용자는 이 암호를 알아야 합니다. SSO를 사용하면 사용자의 자격 증명이 응용 프로그램에 전달되므로 각 응용 프로그램에 대한 암호를 다시 입력할 필요가 없습니다.

내 앱에서 응용 프로그램을 시작하려면 SSO를 사용하도록 설정해야 합니다.

Azure AD는 [응용 프로그램에서 단일 사인온을](what-is-single-sign-on.md)활성화하는 세 가지 방법을 지원합니다.

* **페더레이션된 단일 사인온** 
    * 암호를 묻는 메시지가 표시되지 않고 응용 프로그램이 사용자 인증을 위해 Azure AD로 리디렉션할 수 있습니다. 
    * SAML 2.0, WS-페더레이션 또는 OpenID Connect와 같은 프로토콜을 사용하는 응용 프로그램에 지원되며 단일 사인온의 가장 풍부한 모드입니다.

* **암호 기반 단일 사인온** 
    * 웹 브라우저 확장 또는 모바일 앱을 사용하여 안전한 응용 프로그램 암호 저장 및 재생을 지원합니다. 
    * 응용 프로그램에서 제공하는 기존 로그인 프로세스를 활용하지만 관리자가 암호를 관리할 수 있습니다. 사용자가 암호를 알 필요는 없습니다.

* **기존 단일 사인온** 
    * Azure AD를 사용하면 응용 프로그램에 대해 구성된 기존 단일 사인온을 활용할 수 있습니다.
    * 이러한 응용 프로그램을 Office 365 또는 Azure AD 액세스 패널 포털에 연결할 수 있습니다. 
    * 응용 프로그램이 시작될 때 Azure AD에서 추가 보고를 활성화합니다. 
    * Azure 응용 프로그램 프록시 및 연결된 단일 사인온 모드 사용을 포함합니다.

응용 프로그램의 SSO 모드를 구성하는 방법은 [여기: Azure Active Directory의 응용 프로그램에 대한 단일 사인온.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

내 앱 페이지에서 최상의 환경을 원하려면 페더레이션된 SSO에 사용할 수 있는 클라우드 응용 프로그램의 통합으로 시작합니다. 페더레이션된 SSO를 사용하면 사용자가 앱 시작 표면에서 일관된 원클릭 경험을 할 수 있으며 구성 제어에서 보다 강력한 경향이 있습니다.

암호 기반 SSO 및 ADFS 대신 응용 프로그램이 지원하는 경우 Azure AD(OpenID Connect/SAML)와 함께 페더레이션된 SSO를 사용합니다.

SaaS 응용 프로그램을 배포하고 구성하는 방법에 대한 자세한 내용은 [SaaS SSO 배포 계획을](https://aka.ms/deploymentplans/sso)참조하십시오.

#### <a name="plan-to-deploy-the-my-apps-browser-extension"></a>내 앱 브라우저 확장 배포 계획

사용자가 암호 기반 SSO 응용 프로그램에 로그인할 때 내 앱 보안 로그인 확장을 설치하고 사용해야 합니다. 확장은 암호를 응용 프로그램의 로그인 형식으로 전송하는 스크립트를 실행합니다. 암호 기반 SSO 응용 프로그램을 처음 시작할 때 확장을 설치하라는 메시지가 표시됩니다. 확장에 대한 자세한 내용은 액세스 [패널 브라우저 확장 을 설치하는](https://docs.microsoft.com/azure/active-directory/application-access-panel-extension-problem-installing?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav)방법에 대한 이 설명서에서 찾을 수 있습니다.

암호 기반 SSO 응용 프로그램을 통합해야 하는 경우 [지원되는 브라우저를](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)사용하여 확장 프로그램을 대규모로 배포하는 메커니즘을 정의해야 합니다. 옵션은 다음과 같습니다.

* [인터넷 익스플로러에 대한 그룹 정책](https://azure.microsoft.com/documentation/articles/active-directory-saas-ie-group-policy/)

* [인터넷 익스플로러용 구성 관리자](https://docs.microsoft.com/configmgr/core/clients/deploy/deploy-clients-to-windows-computers)

* [크롬에 대 한 사용자 기반 다운로드 및 구성, 파이어 폭스, 마이크로소프트 가장자리, 또는 IE](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

자세히 알아보기: [암호 단일 사인온을 구성하는 방법.](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-password-sso-non-gallery)

암호 기반 SSO 응용 프로그램을 사용하지 않는 사용자도 확장의 이점을 누릴 수 있습니다. 이러한 이점에는 검색 표시줄에서 모든 앱을 실행하고, 최근에 사용한 응용 프로그램에 대한 액세스를 찾고, 내 앱 페이지에 대한 링크를 갖는 기능이 포함됩니다.

암호 기반 SSO 응용 프로그램을 처음 시작할 때 사용자에게 표시되는 내용은 다음과 같습니다.

![내 앱 브라우저 확장 설치 화면의 스크린샷 ](media/access-panel-deployment-plan/ap-dp-sign-in.png)

#### <a name="plan-for-mobile-access"></a>모바일 액세스 계획

암호 기반 SSO 응용 프로그램을 시작하는 모바일 사용자를 위해 Intune 정책(Microsoft Edge 또는 Intune 관리 브라우저)으로 보호되는 브라우저가 필요합니다. 정책 으로 보호된 브라우저를 사용하면 응용 프로그램에 대해 저장된 암호를 전송할 수 있습니다. Microsoft Edge 또는 관리되는 브라우저는 일련의 웹 데이터 보호 기능을 제공합니다. 또한 iOS 및 Android 장치에서 엔터프라이즈 시나리오에 Microsoft Edge를 사용할 수도 있습니다. Microsoft Edge는 Intune 관리 브라우저와 동일한 관리 시나리오를 지원하고 사용자 환경을 개선합니다. 자세히 알아보기: [Microsoft Intune 정책 보호 브라우저를 사용하여 웹 액세스를 관리합니다.](https://docs.microsoft.com/intune/app-configuration-managed-browser)

## <a name="plan-your-my-apps-deployment"></a>내 앱 배포 계획

액세스 패널의 기초는 사용자가 에서 액세스하는 응용 프로그램 [https://myapps.microsoft.com](https://myapps.microsoft.com/)실행 프로그램 내 앱입니다. 내 앱 페이지는 사용자에게 작업을 시작하고 필요한 응용 프로그램에 도착할 수 있는 단일 장소를 제공합니다. 여기서 사용자는 단일 사인온 액세스 권한이 있는 모든 응용 프로그램의 목록을 찾을 수 있습니다. 

![앱 패널의 스크린샷](media/access-panel-deployment-plan/ap-dp-app-launcher.png)

사용자가 Office 365 포털을 사용할 때 Office 365 앱 실행 프로그램에 동일한 응용 프로그램이 표시됩니다.

내 앱 실행 프로그램에 응용 프로그램을 추가할 순서를 계획하고 점진적으로 또는 한 번에 둘 다 롤아웃할지 여부를 결정합니다. 이렇게 하려면 인증 유형과 각 응용 프로그램에 대한 기존 SSO 통합을 나열하는 응용 프로그램 인벤토리를 만듭니다.

#### <a name="add-applications-to-the-my-apps-panel"></a>내 앱 패널에 응용 프로그램 추가

모든 Azure AD SSO 지원 응용 프로그램을 내 앱 실행 프로그램에 추가할 수 있습니다. 연결된 SSO 옵션을 사용하여 다른 응용 프로그램이 추가됩니다. 기존 웹 응용 프로그램의 URL에 연결되는 응용 프로그램 타일을 구성할 수 있습니다. 연결된 SSO를 사용하면 모든 응용 프로그램을 Azure AD SSO로 마이그레이션하지 않고 사용자를 내 앱 포털로 안내할 수 있습니다. 사용자 환경을 방해하지 않고 Azure AD SSO 구성 응용 프로그램으로 점진적으로 이동할 수 있습니다.

#### <a name="use-my-apps-collections"></a>내 앱 컬렉션 사용

기본적으로 모든 응용 프로그램은 단일 페이지에 함께 나열됩니다. 그러나 컬렉션을 사용하여 관련 응용 프로그램을 함께 그룹화하고 별도의 탭에 표시하여 쉽게 찾을 수 있습니다. 예를 들어 컬렉션을 사용하여 특정 작업 역할, 작업, 프로젝트 등을 위한 응용 프로그램의 논리적 그룹을 만들 수 있습니다. 자세한 내용은 [내 앱 컬렉션을 사용하여 사용자 액세스 패널을 사용자 지정하는 방법을 참조하세요.](access-panel-collections.md) 

#### <a name="plan-whether-to-use-my-apps-or-an-existing-portal"></a>내 앱 또는 기존 포털 사용 여부 계획

사용자가 이미 내 앱 이외의 응용 프로그램 이나 포털을 가지고 있을 수 있습니다. 그렇다면 두 포털을 모두 지원할지 아니면 포털만 사용할지 결정합니다.

기존 포털이 이미 사용자의 시작점으로 사용되고 있는 경우 사용자 액세스 URL을 사용하여 내 앱 기능을 통합할 수 있습니다. 사용자 액세스 URL은 내 앱 포털에서 사용할 수 있는 응용 프로그램에 대한 직접 링크로 작동합니다. 이러한 URL은 기존 웹 사이트에 포함할 수 있습니다. 사용자가 링크를 선택하면 내 앱 포털에서 응용 프로그램이 열립니다.

Azure 포털에서 응용 프로그램의 **속성** 영역에서 사용자 액세스 URL 속성을 찾을 수 있습니다.

![앱 패널의 스크린샷](media/access-panel-deployment-plan/ap-dp-user-access-url.png)


## <a name="plan-self-service-application-discovery-and-access"></a>셀프 서비스 응용 프로그램 검색 및 액세스 계획

핵심 응용 프로그램 집합이 사용자의 내 앱 페이지에 배포되면 셀프 서비스 앱 관리 기능을 사용하도록 설정해야 합니다. 셀프 서비스 앱 검색을 통해 사용자는 다음을 수행할 수 있습니다.

* 내 앱에 추가할 새 앱을 찾습니다. 
* 설정 하는 동안 필요 하지 않은 것 일 수 있습니다 선택적 애플 리 케이 션을 추가 합니다.

응용 프로그램에 대한 명시적 승인을 위해 승인 워크플로를 사용할 수 있습니다. 승인자인 사용자는 응용 프로그램에 대한 액세스 요청이 보류 중인 경우 내 앱 포털 내에서 알림을 받게 됩니다.

## <a name="plan-self-service-group-membership"></a>셀프 서비스 그룹 멤버십 계획 

사용자가 Azure AD에서 자신의 보안 그룹 또는 Office 365 그룹을 만들고 관리할 수 있도록 설정할 수 있습니다. 그룹의 소유자는 구성원 자격 요청을 승인 또는 거부하고 그룹 구성원의 제어를 위임할 수 있습니다. 메일 지원 보안 그룹 이나 메일 그룹에 대 한 셀프 서비스 그룹 관리 기능을 사용할 수 없습니다.

셀프 서비스 그룹 구성원 자격을 계획하려면 조직의 모든 사용자가 그룹 또는 하위 집합만 만들거나 관리할 수 있도록 허용할지 확인합니다. 사용자의 하위 집합을 허용하는 경우 해당 사용자가 추가되는 그룹을 설정해야 합니다. 이러한 시나리오를 사용하도록 설정하는 방법에 대한 자세한 내용은 [Azure Active Directory에서 셀프 서비스 그룹 관리 설정을](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-self-service-management) 참조하십시오.

## <a name="plan-reporting-and-auditing"></a>보고 및 감사 계획

Azure AD는 [기술 및 비즈니스 통찰력을 제공하는 보고서를](https://azure.microsoft.com/documentation/articles/active-directory-view-access-usage-reports/)제공합니다. 비즈니스 및 기술 응용 프로그램 소유자와 협력하여 이러한 보고서의 소유권을 가정하고 정기적으로 사용하십시오. 다음 표에서는 일반적인 보고 시나리오의 몇 가지 예를 제공합니다.

|   | 위험 관리| 생산성 향상| 거버넌스 및 규정 준수 |
|  - |- | - | - |
| 보고서 유형|  응용 프로그램 권한 및 사용 권한| 계정 프로비전 활동| 응용 프로그램에 액세스하는 사용자 검토 |
| 잠재적인 작업| 감사 액세스; 권한 취소| 프로비저닝 오류 수정| 액세스 취소 |

Azure AD는 대부분의 감사 데이터를 30일 동안 유지합니다. 데이터는 Azure 관리 포털 또는 API를 통해 분석 시스템에 다운로드할 수 있습니다.

#### <a name="auditing"></a>감사

응용 프로그램 액세스에 대한 감사 로그를 30일 동안 사용할 수 있습니다. 기업 내 보안 감사에 더 긴 보존이 필요한 경우 로그를 Splunk 또는 ArcSight와 같은 SIEM(보안 정보 이벤트 및 관리) 도구로 내보내야 합니다.

감사, 보고 및 재해 복구 백업의 경우 필요한 다운로드 빈도, 대상 시스템 및 각 백업 관리를 담당하는 사람을 문서화합니다. 별도의 감사 및 보고 백업이 필요하지 않을 수 있습니다. 재해 복구 백업은 별도의 엔터티여야 합니다.

## <a name="deploy-applications-to-users-my-apps-panel"></a>사용자의 내 앱 패널에 응용 프로그램 배포

응용 프로그램이 SSO에 대해 구성되면 그룹에 액세스 권한이 할당됩니다. 할당된 그룹의 사용자는 액세스 권한을 가지며 내 앱 및 Office 365 앱 실행 프로그램에서 응용 프로그램을 볼 수 있습니다.

[Active Directory의 응용 프로그램에 사용자 및 그룹 할당을 참조하세요.](methods-for-assigning-users-and-groups.md)

테스트 또는 배포 중에 그룹을 추가하지만 아직 내 앱에 응용 프로그램이 표시되지 않도록 하려는 경우 [Azure Active Directory에서 사용자의 환경에서 응용 프로그램 숨기기를](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app)참조하세요.

### <a name="deploy-microsoft-office-365-applications-to-my-apps"></a>내 앱에 Microsoft Office 365 응용 프로그램 배포

Office 365 응용 프로그램의 경우 사용자는 할당된 라이선스에 따라 Office 복사본을 받게 됩니다. Office 응용 프로그램에 액세스하기 위한 전제 조건은 사용자가 Office 응용 프로그램에 연결된 올바른 라이선스를 할당하는 것입니다. 사용자에게 라이선스를 할당하면 내 앱 페이지와 Office 365 앱 실행 프로그램에서 라이선스와 연결된 응용 프로그램이 자동으로 표시됩니다.

사용자로부터 Office 응용 프로그램 집합을 숨기려면 Office 365 포털에서 액세스를 허용하면서 내 앱 포털에서 앱을 숨길 수 있는 옵션이 있습니다. 응용 프로그램의 사용자 설정 부분에서 이러한 설정을 찾습니다. 자세히 알아보기: [Azure Active Directory.](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app)

![응용 프로그램을 숨기는 방법을 구성하는 스크린샷](media/access-panel-deployment-plan/ap-dp-o365-portal.png)

### <a name="deploy-application-self-service-capabilities"></a>애플리케이션 셀프 서비스 기능 배포

셀프 서비스 응용 프로그램 액세스를 통해 사용자는 자체 검색하고 응용 프로그램에 대한 액세스를 요청할 수 있습니다. 사용자는 액세스를 요청할 때마다 IT 그룹을 거치지 않고도 필요한 앱에 액세스할 수 있습니다. 사용자가 액세스를 요청하고 앱 소유자가 자동으로 또는 수동으로 액세스하도록 승인되면 백 엔드의 그룹에 추가됩니다. 액세스 권한을 요청, 승인 또는 제거한 사람에 대해 보고가 활성화되며 할당된 역할 관리를 제어할 수 있습니다.

응용 프로그램 액세스 요청의 승인을 비즈니스 승인자에 위임할 수 있습니다. 비즈니스 승인자가 비즈니스 승인자의 내 앱 페이지에서 앱 액세스 암호를 설정할 수 있습니다.

자세히 알아보기: [셀프 서비스 응용 프로그램 액세스를 사용하는 방법.](https://docs.microsoft.com/azure/active-directory/application-access-panel-self-service-applications-how-to)

![셀프 서비스 응용 프로그램 관리 구성 스크린샷](media/access-panel-deployment-plan/ap-dp-salesforce.png)

## <a name="validate-your-deployment"></a>배포 유효성 검사

액세스 패널 배포를 철저히 테스트하고 롤백 계획을 마련해야 합니다.

다음 테스트는 회사 소유 장치와 개인 장치 모두에서 수행해야 합니다. 이러한 테스트 사례는 비즈니스 사용 사례도 반영해야 합니다. 다음은 이 문서의 샘플 비즈니스 요구 사항과 일반적인 기술 시나리오에 기반한 몇 가지 사례입니다. 필요에 따라 다른 사람을 추가합니다.

#### <a name="application-sso-access-test-case-examples"></a>응용 프로그램 SSO 액세스 테스트 사례 예제:


| 비즈니스 사례| 예상된 결과 |
| - | -|
| 내 앱 포털에 사용자가 로그인| 사용자는 로그인하여 응용 프로그램을 볼 수 있습니다. |
| 사용자가 페더레이션된 SSO 응용 프로그램을 시작합니다.| 사용자가 응용 프로그램에 자동으로 로그인됨 |
| 사용자가 처음으로 암호 SSO 응용 프로그램을 실행합니다.| 사용자가 내 앱 확장을 설치해야 합니다. |
| 사용자가 후속 시간에 암호 SSO 응용 프로그램을 실행합니다.| 사용자가 응용 프로그램에 자동으로 로그인됨 |
| 사용자가 Office 365 포털에서 앱을 실행합니다.| 사용자가 응용 프로그램에 자동으로 로그인됨 |
| 사용자가 관리되는 브라우저에서 앱을 실행합니다.| 사용자가 응용 프로그램에 자동으로 로그인됨 |


#### <a name="application-self-service-capabilities-test-case-examples"></a>응용 프로그램 셀프 서비스 기능 테스트 사례 예제


| 비즈니스 사례| 예상된 결과 |
| - | - |
| 사용자는 응용 프로그램에 대한 멤버십을 관리할 수 있습니다.| 사용자는 앱에 액세스할 수 있는 구성원을 추가/제거할 수 있습니다. |
| 사용자는 응용 프로그램을 편집할 수 있습니다.| 사용자는 암호 SSO 응용 프로그램에 대한 응용 프로그램의 설명 및 자격 증명을 편집할 수 있습니다. |

### <a name="rollback-steps"></a>롤백 단계

배포가 계획대로 진행되지 않는 경우 수행할 작업을 계획하는 것이 중요합니다. 배포 중에 SSO 구성에 장애가 발생하면 [SSO 문제를 해결하는](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sso) 방법을 이해하고 사용자에게 미치는 영향을 줄여야 합니다. 극단적인 상황에서는 SSO 를 [롤백해야](../manage-apps/plan-sso-deployment.md)할 수 있습니다.


## <a name="manage-your-implementation"></a>구현 관리

Azure Active Directory 내에서 필요한 작업을 수행 하려면 최소 권한 역할을 사용 해야 합니다. [사용 가능한 다양한 역할을 검토하고](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) 이 응용 프로그램에 대한 각 페르소나에 대한 요구를 해결할 수 있는 올바른 역할을 선택합니다. 배포가 완료된 후 일부 역할을 일시적으로 적용하고 제거해야 할 수 있습니다.

| 가상 사용자| 역할| Azure AD 역할  |
| - | -| -|
| 헬프 데스크 관리자| 계층 1 지원| None |
| ID 관리자| 문제가 Azure AD에 영향을 미치는 경우 구성 및 디버그| 글로벌 관리자 |
| 응용 프로그램 관리자| 응용 프로그램의 사용자 증명, 권한이있는 사용자에 대한 구성| None |
| 인프라 관리자| 인증서 롤오버 소유자| 글로벌 관리자 |
| 비즈니스 소유자/이해 관계자| 응용 프로그램의 사용자 증명, 권한이있는 사용자에 대한 구성| None |

[권한 있는 ID 관리를](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) 사용하여 역할을 관리하여 디렉터리 권한이 있는 사용자에 대한 추가 감사, 제어 및 액세스 검토를 제공할 수 있습니다.

### <a name="troubleshoot-access-panel-issues"></a>액세스 패널 문제 해결

일반적인 시나리오를 사용하여 지원 조직에 대한 문제 해결 가이드를 만듭니다. 조직에서 사용하는 계층으로 지원을 중단하는 가이드를 만들 수 있습니다.

참조를 참조할 수 있는 다음 문제 해결 가이드를 참조하십시오.

[응용 프로그램이 표시되지 않음](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-application-not-appearing)

[예기치 않은 응용 프로그램 표시](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-unexpected-application)

[사용자가 액세스 패널에 로그인할 수 없음](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-web-sign-in-problem)

[셀프 서비스 응용 프로그램 액세스를 사용하는 문제](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-self-service-access)

[브라우저 확장 문제](https://docs.microsoft.com/azure/active-directory/manage-apps/manage-access-panel-browser-extension)

## <a name="next-steps"></a>다음 단계

[Azure 다단계 인증 배포 계획](https://aka.ms/deploymentplans/mfa)
