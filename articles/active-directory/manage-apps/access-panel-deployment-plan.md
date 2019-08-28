---
title: Azure Active Directory 액세스 패널 배포 계획
description: Azure AD 액세스 패널 기능 배포에 대 한 지침
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
ms.date: 08/16/2019
ms.author: baselden
ms.reviewer: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 448af7075297c2b18df1eceaeaac34d5da762489
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2019
ms.locfileid: "69576525"
---
# <a name="plan-an-azure-active-directory-access-panel-deployment"></a>Azure Active Directory 액세스 패널 배포 계획

Azure Active Directory 액세스 패널은 지원 비용을 낮추고 생산성과 보안을 높이고 사용자 불만을 줄일 수 있는 웹 기반 포털입니다. 시스템에는 사용자가 시스템에 액세스 하는 시기를 추적 하 고 관리자에 게 오용 또는 남용 하도록 알리는 상세 보고가 포함 되어 있습니다.

Azure Active Directory 액세스 패널을 통해 사용자는 다음을 수행할 수 있습니다.

* 모든 회사의 연결 된 리소스 (예: 응용 프로그램)를 검색 하 고 액세스할 Azure Active Directory.
* 새 앱 및 그룹에 대 한 액세스를 요청 하거나 다른 사용자를 위해 이러한 리소스에 대 한 액세스를 관리 합니다.
* 셀프 서비스 암호 재설정 및 multi-factor authentication 설정을 관리 합니다.
* 장치를 관리 합니다.

또한 관리자는 다음을 관리할 수 있습니다.

* 서비스 약관
* 조직
* 액세스 검토


## <a name="benefits-of-azure-active-directory-access-panel-integration"></a>Azure Active Directory 액세스 패널 통합의 이점

Azure AD (Microsoft Azure Active Directory) 액세스 패널은 다음과 같은 방법으로 비즈니스를 활용 합니다.

**직관적인 사용자 환경을 제공 합니다**. 액세스 패널을 사용 하도록 설정 하면 사용자는 모든 Azure single sign-on 연결 된 응용 프로그램에 대해 단일 실행 패드를 사용할 수 있습니다. 그룹 관리 및 셀프 서비스 암호 재설정과 같은 기능을 추가 하면 사용자가 계속 해 서 이러한 설정을 찾을 수 있습니다. 직관적인 환경을 사용 하면 사용자가 신속 하 게 작업 하 고 생산성을 높일 수 있을 뿐 아니라, 사용자의 불만을 줄일 수 있습니다.

**생산성 향상**: 액세스 패널의 모든 사용자 응용 프로그램에 SSO (single sign-on)를 사용할 수 있습니다. 엔터프라이즈 응용 프로그램 및 Office 365에서 single sign-on을 사용 하도록 설정 하면 기존 사용자에 게 더 뛰어난 로그인 환경을 제공 하 고 추가 로그인 프롬프트를 줄이거나 제거 합니다. 액세스 패널을 통해 셀프 서비스와 동적 멤버 자격을 사용할 수 있으며, 올바른 사람이 응용 프로그램에 대 한 액세스를 관리할 수 있도록 하 여 id 시스템의 전반적인 보안을 향상 시킬 수 있습니다. 액세스 패널은 사용자가 신속 하 게 리소스를 찾고 작업을 계속할 수 있는 일관 된 방문 페이지로 사용 됩니다.

**비용 관리**: Azure Active Directory를 사용 하 여 액세스 패널을 사용 하도록 설정 하면 온-프레미스 인프라를 divestment 수 있습니다. 사용자가 모든 앱을 찾고, 리소스에 대 한 액세스를 요청 하 고, 자신의 계정을 관리 하는 일관 된 포털을 사용 하도록 설정 하 여 지원 비용을 절감 합니다.

**유연 하 고 보안이 향상**됩니다. 액세스 패널을 사용 하 여 클라우드 플랫폼에서 제공 하는 보안 및 유연성에 액세스할 수 있습니다. 관리자는 설정을 응용 프로그램 및 리소스로 쉽게 변경 하 고 사용자에 게 영향을 주지 않고 새로운 보안 요구 사항을 수용할 수 있습니다.

**강력한 감사 및 사용 추적을 사용 하도록 설정**합니다. 모든 최종 사용자 기능에 대 한 감사 및 사용 추적을 사용 하면 사용자가 리소스를 사용 하 고 있는지 확인 하 고 보안을 평가할 수 있습니다.

### <a name="licensing-considerations"></a>라이선스 고려 사항

액세스 패널은 무료 이며 기본 모든 사용자가 사용할 수 있는 라이선스가 필요 하지 않습니다. 그러나 디렉터리의 개체 수와 배포 하려는 기능에는 추가 라이선스가 필요할 수 있습니다. 일반적인 Azure AD 시나리오에는 라이선스 요구 사항을 충족 하는 다음과 같은 보안 기능이 포함 되어 있습니다.

* [Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-how-it-works)

* [그룹 기반 구성원 자격](https://docs.microsoft.com/azure/active-directory/active-directory-manage-groups)

* [셀프 서비스 암호 요구 사항](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr)

* [ID 보호](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)

[Azure Active Directory에 대 한 전체 라이선스 가이드](https://azure.microsoft.com/pricing/details/active-directory/)를 참조 하세요.

### <a name="prerequisites-for-deploying-the-azure-ad-access-panel"></a>Azure AD 액세스 패널 배포를 위한 필수 구성 요소

이 프로젝트를 시작 하기 전에 다음 필수 구성 요소를 완료 해야 합니다.

* [응용 프로그램 SSO 통합](https://docs.microsoft.com/azure/active-directory/manage-apps/plan-sso-deployment)

* [Azure AD 사용자 및 그룹 인프라](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-manage-groups)

## <a name="plan-the-azure-ad-access-panel-deployment"></a>Azure AD 액세스 패널 배포 계획

다음 표에서는 액세스 패널 배포에 대 한 주요 사용 사례를 간략하게 설명 합니다.

| 영역| Description |
| - | - |
| 액세스| 액세스 패널 포털은 회사 네트워크 내의 회사 및 개인 장치에서 액세스할 수 있습니다. |
|액세스 | 액세스 패널 포털은 회사 네트워크 외부의 회사 장치에 액세스할 수 있습니다. |
| 감사| 사용 현황 데이터는 최소 29 일 마다 회사 시스템에 다운로드 됩니다. |
| 거버넌스| Azure AD 연결 응용 프로그램 및 그룹에 대 한 사용자 할당의 수명 주기는 정의 되 고 모니터링 됩니다. |
| 보안| 리소스에 대 한 액세스는 사용자 및 그룹 할당을 통해 제어 됩니다. 권한 있는 사용자만 리소스 액세스를 관리할 수 있습니다. |
| 성능| 액세스 할당 전파 타임 라인은 문서화 및 모니터링 됩니다. |
| 사용자 환경| 사용자는 액세스 패널 기능과이 기능을 사용 하는 방법을 알고 있습니다.|
| 사용자 환경| 사용자는 응용 프로그램 및 그룹에 대 한 액세스를 직접 관리할 수 있습니다.|
| 사용자 환경| 사용자는 자신의 계정을 관리할 수 있습니다. |
| 사용자 환경| 사용자는 브라우저 호환성을 인식 합니다. |
| 지원| 사용자는 액세스 패널 문제에 대 한 지원을 찾을 수 있습니다. |

### <a name="best-practices-for-deploying-the-azure-ad-access-panel"></a>Azure AD 액세스 패널 배포에 대 한 모범 사례

액세스 패널의 기능을 점진적으로 사용 하도록 설정할 수 있습니다. 다음 배포 순서를 권장 합니다.

1. 내 앱
   * 앱 시작 관리자
   * 셀프 서비스 앱 관리
   * Microsoft Office 365 통합

1. 셀프 서비스 앱 검색
   * 셀프 서비스 암호 재설정
   * Multi-factor authentication 설정
   * 디바이스 관리
   * 서비스 계약
   * 조직 관리

1. 내 그룹
   * 셀프 서비스 그룹 관리
1. 액세스 검토
   * 액세스 검토 관리

내 앱에서 시작 하 여 리소스에 액세스 하는 일반적인 장소로 포털에 사용자를 소개 합니다. 셀프 서비스 응용 프로그램 검색을 추가 하는 것은 내 앱 환경을 기반으로 합니다. 내 그룹 및 액세스 검토는 셀프 서비스 기능을 기반으로 합니다.

### <a name="plan-configurations-for-the-azure-ad-access-panel"></a>Azure AD 액세스 패널에 대 한 계획 구성

다음 표에서는 사용할 수 있는 몇 가지 중요 한 액세스 패널 구성과 일반적인 값을 보여 줍니다.

| Configuration| 일반적인 값 |
| - | - |
| 파일럿 그룹 결정| 사용할 Azure AD 보안 그룹을 식별 하 고 모든 파일럿 구성원이 그룹에 속해 있는지 확인 합니다. |
| 프로덕션에 사용할 그룹을 결정 합니다.| 사용할 azure ad 보안 그룹 또는 Azure AD에 동기화 된 AD 그룹을 식별 합니다. 모든 파일럿 구성원이 그룹의 일부 인지 확인 합니다. |
| 사용자가 single sign-on을 사용 하 여 응용 프로그램 형식에 대해 허용| 페더레이션된 SSO, OAuth, 암호 SSO, 앱 프록시 |
| 사용자가 셀프 서비스 암호 재설정을 사용 하도록 허용| 예 |
| 사용자가 multi-factor authentication을 사용할 수 있도록 허용| 예 |
| 사용자가 그룹 유형에 대해 셀프 서비스 그룹 관리를 사용할 수 있도록 허용| 보안 그룹, O365 그룹 |
| 사용자가 셀프 서비스 앱 관리를 사용할 수 있도록 허용| 예 |
| 사용자가 액세스 검토를 사용할 수 있도록 허용| 예 |

### <a name="plan-consent-strategy"></a>계획 승인 전략

사용자 또는 관리자는 모든 응용 프로그램의 사용 약관 및 개인 정보 취급 방침에 동의 해야 합니다. 가능 하면 비즈니스 규칙이 제공 되는 경우 사용자에 게 더 나은 환경을 제공 하는 관리자 동의를 사용 하는 것이 좋습니다.

관리자 동의를 사용 하려면 테 넌 트의 전역 관리자 여야 하며 응용 프로그램은 다음과 같아야 합니다.

* 테넌트에 등록되어 있습니다. 또는

* 다른 Azure AD 테 넌 트에 등록 되었으며 이전에는 하나 이상의 최종 사용자에 의해 동의한.

자세한 내용은 [Azure Active Directory에서 응용 프로그램에 대 한 최종 사용자 동의를 구성 하는 방법 구성](configure-user-consent.md)을 참조 하세요.

### <a name="engage-the-right-stakeholders"></a>올바른 관련자 참여

기술 프로젝트에 오류가 발생 하는 경우 일반적으로 영향, 결과 및 책임에 대 한 예상치가 일치 하지 않기 때문에이 작업을 수행 합니다. 이러한 문제를 방지 하려면 [올바른 관련자에](../fundamentals/active-directory-deployment-plans.md) 게 참여 하 고 프로젝트의 관련자 역할을 잘 이해 하 고 있어야 합니다.

### <a name="plan-communications"></a>통신 계획

통신은 모든 새 서비스의 성공에 중요 합니다. 사용자에 게 경험을 어떻게 변경 하는지 사전에 알리고 필요한 경우 지원을 얻는 방법에 대해 설명 합니다.

액세스 패널은 일반적으로 사용자 문제를 만들지 않지만 준비 하는 것이 중요 합니다. 시작 하기 전에 지원 담당자의 모든 리소스 목록 및 가이드를 만듭니다.

#### <a name="communications-templates"></a>통신 템플릿

Microsoft는 액세스 패널에 대 한 [전자 메일 및 기타 통신을 위한 사용자 지정 가능한 템플릿을](https://aka.ms/APTemplates) 제공 합니다. 회사 문화에 맞게 다른 통신 채널에서 사용 하기 위해 이러한 자산을 조정할 수 있습니다.

## <a name="plan-your-sso-configuration"></a>SSO 구성 계획

사용자가 응용 프로그램에 로그인 하면 인증 프로세스를 거치 며 사용자를 증명 하는 데 필요 합니다. Single sign-on을 사용 하지 않는 경우 응용 프로그램에 저장 된 암호 및 사용자가이 암호를 알고 있어야 합니다. SSO (single sign-on) 사용자의 자격 증명은 응용 프로그램에 전달 되므로 각 응용 프로그램에 대 한 암호를 다시 입력할 필요가 없습니다.

내 앱에서 응용 프로그램을 시작 하려면 응용 프로그램에 SSO (single sign-on)를 사용 하도록 설정 해야 합니다.

Azure AD는 [응용 프로그램에 single sign-on](what-is-single-sign-on.md)을 사용 하도록 설정 하는 세 가지 다른 방법을 지원 합니다.

* **페더레이션된 single sign-on** 
    * 응용 프로그램에서 암호를 묻는 메시지를 표시 하지 않고 사용자 인증을 위해 Azure AD로 리디렉션할 수 있습니다. 
    * 는 SAML 2.0, WS-FEDERATION 또는 Openid connect Connect와 같은 프로토콜을 사용 하는 응용 프로그램에서 지원 되며 single sign-on의 가장 다양 한 모드입니다.

* **암호 기반 single sign-on** 
    * 웹 브라우저 확장 또는 모바일 앱을 사용 하 여 보안 응용 프로그램 암호 저장 및 재생을 사용 하도록 설정 합니다. 
    * 응용 프로그램에서 제공 하는 기존 로그인 프로세스를 활용 하지만 관리자가 암호를 관리할 수 있도록 합니다. 사용자는 암호를 몰라도 됩니다.

* **기존 single sign-on** 
    * Azure AD에서 응용 프로그램에 대해 구성 된 모든 기존 single sign-on을 활용할 수 있도록 합니다.
    * 이러한 응용 프로그램을 Office 365 또는 Azure AD 액세스 패널 포털에 연결할 수 있습니다. 
    * 응용 프로그램을 시작할 때 Azure AD에서 추가 보고 기능을 사용 하도록 설정 합니다. 
    * Azure 애플리케이션 프록시와 연결 된 single sign-on 모드 사용을 포함 합니다.

응용 프로그램의 SSO 모드를 구성 하는 방법에 대 한 자세한 내용은 다음을 참조 하세요. [Azure Active Directory에서 응용 프로그램에 대 한 Single sign-on](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

내 앱 페이지에서 최상의 경험을 위해 페더레이션된 SSO에 사용할 수 있는 클라우드 응용 프로그램을 통합 하는 것으로 시작 하는 것이 좋습니다. 페더레이션된 SSO를 사용 하면 사용자가 앱을 시작 하는 일관 된 한 번 클릭 환경을 제공 하 고 구성 제어에서 더욱 강력해 집니다.

암호 기반 SSO 및 ADFS 대신 응용 프로그램에서 지원할 때 Azure AD (Openid connect Connect/SAML)에서 페더레이션된 SSO를 사용 합니다.

SaaS 응용 프로그램을 배포 하 고 구성 하는 방법에 대 한 자세한 내용은 [SAAS SSO 배포 계획](https://aka.ms/deploymentplans/sso)을 참조 하세요.

#### <a name="plan-to-deploy-the-my-apps-browser-extension"></a>My Apps 브라우저 확장 배포 계획

최종 사용자가 암호 기반 SSO 응용 프로그램을 사용할 수 있게 되 면 로그인 하려면 My Apps 보안 로그인 확장을 설치 해야 합니다. 확장은 암호를 응용 프로그램의 로그인 형식으로 전송 하는 스크립트를 실행 합니다. 사용자가 암호 기반 SSO 응용 프로그램을 처음 시작할 때 확장을 설치 하 라는 메시지가 표시 됩니다. 확장에 대 한 자세한 내용은 [액세스 패널 브라우저 확장 설치](https://docs.microsoft.com/azure/active-directory/application-access-panel-extension-problem-installing?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav)에 대 한 설명서에 나와 있습니다.

암호 기반 SSO 응용 프로그램을 통합 해야 하는 경우 [지원 되는 브라우저](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 사용 하 여 확장을 대규모로 배포 하는 메커니즘을 정의 해야 합니다. 다음 옵션을 사용할 수 있습니다.

* [Internet Explorer에 대 한 그룹 정책](https://azure.microsoft.com/documentation/articles/active-directory-saas-ie-group-policy/)

* [Internet Explorer 용 System Center Configuration Manager (SCCM)](https://docs.microsoft.com/sccm/core/clients/deploy/deploy-clients-to-windows-computers)

* [Chrome, Firefox, Microsoft Edge 또는 IE에 대 한 사용자 구동 다운로드 및 구성](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

자세한 정보: [암호 single sign-on을 구성 하는 방법](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-password-sso-non-gallery)

암호 기반 SSO 응용 프로그램을 사용 하지 않는 사용자는 확장의 이점을 누릴 수 있습니다. 이러한 혜택에는 검색 표시줄에서 앱을 시작 하 고 최근에 사용한 응용 프로그램에 대 한 액세스를 찾고 내 앱 페이지에 대 한 링크를 포함 하는 기능이 포함 됩니다.

처음으로 암호 기반 SSO 응용 프로그램을 시작할 때 사용자에 게 표시 되는 항목은 다음과 같습니다.

![내 앱 브라우저 확장 설치 화면의 스크린샷 ](media/access-panel-deployment-plan/ap-dp-sign-in.png)

#### <a name="plan-for-mobile-access"></a>모바일 액세스 계획

Intune 정책 (Microsoft Edge 또는 Intune Managed Browser)으로 보호 된 브라우저는 암호 기반 SSO 응용 프로그램을 시작 하는 모바일 사용자에 게 필요 합니다. 정책으로 보호 된 브라우저를 사용 하면 응용 프로그램에 대해 저장 된 암호를 전송할 수 있습니다. Microsoft Edge 또는 managed browser는 웹 데이터 보호 기능 집합을 제공 합니다. IOS 및 Android 장치에서 엔터프라이즈 시나리오용 Microsoft Edge를 사용할 수도 있습니다. Microsoft Edge는 Intune Managed Browser와 동일한 관리 시나리오를 지원 하 고 최종 사용자 환경을 개선 합니다. 자세한 정보: [Microsoft Intune 정책으로 보호 된 브라우저를 사용 하 여 웹 액세스 관리](https://docs.microsoft.com/intune/app-configuration-managed-browser)

## <a name="plan-your-my-apps-deployment"></a>내 앱 배포 계획

액세스 패널의 기초는 응용 프로그램 시작 관리자 내 앱 이며, 사용자는에 [https://myapps.microsoft.com](https://myapps.microsoft.com/)액세스 합니다. 내 앱 페이지에서는 사용자에 게 작업을 시작 하 고 필요한 응용 프로그램에 액세스할 수 있는 단일 장소를 제공 합니다. 여기서 사용자는 single sign-on 액세스 권한이 있는 모든 응용 프로그램의 목록을 찾을 수 있습니다. 

![앱 패널의 스크린샷](media/access-panel-deployment-plan/ap-dp-app-launcher.png)

사용자가 Office 365 포털을 사용 하는 경우 동일한 응용 프로그램이 Office 365 앱 시작 관리자에 표시 됩니다.

응용 프로그램을 내 앱 시작 관리자에 추가 하는 순서 및 점진적으로 또는 한꺼번에 롤아웃할 지를 계획 합니다. 이렇게 하려면 각 응용 프로그램에 대 한 인증 유형 및 기존 SSO (single sign-on) 통합을 나열 하는 응용 프로그램 인벤토리를 만듭니다.

#### <a name="add-applications-to-the-my-apps-panel"></a>내 앱 패널에 응용 프로그램 추가

모든 Azure AD SSO 사용 응용 프로그램을 My Apps 시작 관리자에 추가할 수 있습니다. 다른 응용 프로그램은 연결 된 SSO 옵션을 사용 하 여 추가 됩니다. 기존 웹 응용 프로그램의 URL에 연결 되는 응용 프로그램 타일을 구성할 수 있습니다. 연결 된 SSO를 사용 하면 모든 응용 프로그램을 Azure Active Directory SSO로 마이그레이션하지 않고도 My Apps 포털로 사용자의 연결을 시작할 수 있습니다. 사용자 환경을 방해 하지 않고 Azure AD SSO 구성 응용 프로그램으로 점진적으로 이동할 수 있습니다.

#### <a name="plan-whether-to-use-my-apps-or-an-existing-portal"></a>내 앱 또는 기존 포털의 사용 여부 계획

사용자에 게 내 앱이 아닌 응용 프로그램이 나 포털이 이미 있을 수 있습니다. 이 경우 두 포털을 모두 지원 해야 하는지 또는 하나만 사용 하는지 결정 합니다.

기존 포털을 사용자의 시작 지점으로 이미 사용 하 고 있는 경우 "사용자 액세스 Url"을 사용 하 여 내 앱 기능을 통합할 수 있습니다. 사용자 액세스 Url은 내 앱 포털에서 사용할 수 있는 응용 프로그램에 대 한 직접 링크 처럼 작동 합니다. 이러한 Url은 기존 웹 사이트에 포함 될 수 있습니다. 사용자가 링크를 클릭 하면 내 앱 포털에서 응용 프로그램이 시작 됩니다.

사용자 액세스 URL 속성은 Azure Portal 응용 프로그램의 속성 영역에서 찾을 수 있습니다.

![앱 패널의 스크린샷](media/access-panel-deployment-plan/ap-dp-user-access-url.png)


## <a name="plan-self-service-application-discovery-and-access"></a>셀프 서비스 응용 프로그램 검색 및 액세스 계획

응용 프로그램의 핵심 집합이 사용자의 내 앱 페이지에 배포 되 면 셀프 서비스 앱 관리 기능을 사용 하는 것이 좋습니다. 셀프 서비스 앱 검색을 사용 하면 다음을 수행할 수 있습니다.
* 사용자가 내 앱에 추가할 수 있는 새 앱을 찾을 수 있습니다. 
* 사용자는 설치 중에 필요 하지 않은 선택적 앱을 추가할 수 있습니다.

승인 워크플로를 사용 하 여 응용 프로그램에 대 한 액세스를 명시적으로 승인할 수 있습니다. 승인자 인 사용자는 응용 프로그램에 대 한 액세스 요청을 보류 중인 경우 내 앱 포털 내에서 알림을 받게 됩니다.

## <a name="plan-self-service-group-membership"></a>셀프 서비스 그룹 구성원 자격 계획 

사용자가 Azure Active Directory (Azure AD)에서 고유한 보안 그룹 또는 Office 365 그룹을 만들고 관리할 수 있도록 설정할 수 있습니다. 그룹 소유자는 멤버 자격 요청을 승인 또는 거부 하 고 그룹 멤버 자격에 대 한 제어를 위임할 수 있습니다. 메일 사용이 가능한 보안 그룹이 나 메일 그룹에는 셀프 서비스 그룹 관리 기능을 사용할 수 없습니다.

셀프 서비스 그룹 멤버 자격에 대 한 계획을 수립 하려면 조직의 모든 사용자가 그룹을 만들고 관리할 수 있도록 허용할지, 아니면 사용자의 하위 집합만 허용할지를 결정 해야 합니다. 사용자의 하위 집합인 경우 해당 사용자가 추가 되는 그룹을 설정 해야 합니다. 이러한 시나리오를 사용 하는 방법에 대 한 자세한 내용은 [Azure Active Directory에서 셀프 서비스 그룹 관리 설정](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-self-service-management) 을 참조 하세요.

## <a name="plan-reporting-and-auditing"></a>보고 및 감사 계획

Azure AD [는 기술 및 비즈니스 정보를 제공 하는 보고서를](https://azure.microsoft.com/documentation/articles/active-directory-view-access-usage-reports/)제공 합니다. 비즈니스 및 기술 응용 프로그램 소유자에 게 문의 하 여 정기적으로 이러한 보고서의 소유권을 가정 하 고 사용 합니다. 다음 표에서는 일반적인 보고 시나리오에 대 한 몇 가지 예를 제공 합니다.

|   | 위험 관리| 생산성 향상| 거버넌스 및 규정 준수 |
|  - |- | - | - |
| 보고서 유형|  응용 프로그램 사용 권한 및 사용.| 계정 프로비전 활동| 응용 프로그램에 액세스 하는 사용자 검토 |
| 잠재적 작업| 감사 액세스 권한 해지| 프로 비전 오류 수정| 액세스 해지 |

Azure AD는 30 일 동안 대부분의 감사 데이터를 유지 합니다. 분석 시스템으로 다운로드 하기 위해 Azure 관리 포털 또는 API를 통해 데이터를 사용할 수 있습니다.

#### <a name="auditing"></a>감사

응용 프로그램 액세스에 대 한 감사 로그는 30 일 동안 사용할 수 있습니다. 엔터프라이즈 내의 보안 감사에 더 긴 보존이 필요한 경우 로그를 Splunk 또는 ArcSight와 같은 SIEM (보안 정보 이벤트 및 관리) 도구로 내보내야 합니다.

감사, 보고 및 재해 복구 백업의 경우 필요한 다운로드 빈도, 대상 시스템 및 각 백업 관리를 담당 하는 사용자를 문서화 합니다. 별도의 감사 및 보고 백업이 필요 하지 않을 수 있습니다. 재해 복구 백업은 별도의 엔터티입니다.

## <a name="deploy-applications-to-users-my-apps-panel"></a>사용자의 내 앱 패널에 응용 프로그램 배포

응용 프로그램이 SSO를 사용 하도록 구성 된 후에는 그룹에 액세스 권한이 할당 됩니다. 할당 된 그룹의 사용자는 액세스 권한을 보유 하 고 내 앱 및 Office 365 앱 시작 관리자에서 응용 프로그램을 볼 수 있습니다.

[Active Directory에서 응용 프로그램에 사용자 및 그룹 할당을](methods-for-assigning-users-and-groups.md)참조 하세요.

테스트 또는 배포 중에 그룹을 추가 하려고 하지만 응용 프로그램이 내 앱에 표시 되는 것을 허용 하지 않는 경우 [Azure Active Directory의 사용자 환경에서 응용 프로그램 숨기기](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app)를 참조 하세요.

### <a name="deploy-microsoft-office-365-applications-to-my-apps"></a>내 앱에 Microsoft Office 365 응용 프로그램 배포

Office 365 응용 프로그램의 경우 사용자는 할당 된 라이선스에 따라 Office의 복사본을 받습니다. Office 응용 프로그램에 액세스 하기 위한 필수 구성 요소는 Office 응용 프로그램에 연결 된 올바른 라이선스에 사용자를 할당 하는 것입니다. 사용자에 게 라이선스를 할당 하면 해당 라이선스와 관련 된 응용 프로그램이 내 앱 페이지 및 O365 앱 시작 관리자에 자동으로 표시 됩니다.

사용자의 Office 응용 프로그램 집합을 숨기려면 앱 포털에서 앱을 숨기는 동시에 O365 포털에서 계속 액세스할 수 있는 옵션이 있습니다. 응용 프로그램의 사용자 설정 부분에서 이러한 설정을 찾습니다. 자세한 정보: [Azure Active Directory의 사용자 환경에서 응용 프로그램을 숨깁니다](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app).

![응용 프로그램을 숨기는 방법 구성의 스크린샷](media/access-panel-deployment-plan/ap-dp-o365-portal.png)

### <a name="deploy-application-self-service-capabilities"></a>응용 프로그램 셀프 서비스 기능 배포

셀프 서비스 응용 프로그램 액세스를 통해 사용자는 모든 응용 프로그램에 대 한 액세스를 직접 검색 하 고 요청할 수 있습니다. 사용자는 액세스를 요청할 때마다 IT 그룹을 거치지 않고 필요한 앱에 자유롭게 액세스할 수 있습니다. 사용자가 액세스를 요청 하 고 앱 소유자가 자동으로 또는 수동으로 승인 하는 경우 백 엔드의 그룹에 추가 됩니다. 보고는 액세스를 요청, 승인 또는 제거 하는 사용자에 게 사용 하도록 설정 되며, 할당 된 역할 관리에 대 한 제어를 제공 합니다.

비즈니스 승인자에 게 응용 프로그램 액세스 요청에 대 한 승인을 위임할 수 있습니다. 비즈니스 승인자는 비즈니스 승인자의 내 앱 페이지에서 바로 앱 액세스 암호를 설정할 수 있습니다.

자세한 정보: [셀프 서비스 응용 프로그램 액세스를 사용 하는 방법](https://docs.microsoft.com/azure/active-directory/application-access-panel-self-service-applications-how-to)

![셀프 서비스 응용 프로그램 관리를 구성 하는 스크린샷](media/access-panel-deployment-plan/ap-dp-salesforce.png)

## <a name="validate-your-deployment"></a>배포 유효성 검사

액세스 패널 배포가 철저 하 게 테스트 되 고 롤백 계획이 준비 되어 있는지 확인 합니다.

회사 소유 장치와 개인 장치 모두에서 다음 테스트를 수행 해야 합니다. 이러한 테스트 사례에도 비즈니스 사용 사례가 반영 되어야 합니다. 다음은이 문서의 샘플 비즈니스 요구 사항과 일반적인 기술 시나리오를 기반으로 하는 몇 가지 사례입니다. 필요에 따라 다른 사용자를 추가 합니다.

#### <a name="application-sso-access-test-case-examples"></a>응용 프로그램 SSO 액세스 테스트 사례 예:


| 비즈니스 사례| 예상된 결과 |
| - | -|
| 사용자가 내 앱 포털에 로그인| 사용자가 로그인 하 여 응용 프로그램을 볼 수 있음 |
| 사용자가 페더레이션된 SSO 응용 프로그램을 시작 합니다.| 사용자가 응용 프로그램에 자동으로 로그인 됩니다. |
| 사용자가 처음으로 암호 SSO 응용 프로그램을 시작 합니다.| 사용자가 내 앱 확장을 설치 해야 함 |
| 사용자가 다음 번에 암호 SSO 응용 프로그램을 시작 합니다.| 사용자가 응용 프로그램에 자동으로 로그인 됩니다. |
| 사용자가 O365 포털에서 앱을 시작 합니다.| 사용자가 응용 프로그램에 자동으로 로그인 됩니다. |
| 사용자가 Managed Browser에서 앱을 시작 합니다.| 사용자가 응용 프로그램에 자동으로 로그인 됩니다. |


#### <a name="application-self-service-capabilities-test-case-examples"></a>응용 프로그램 셀프 서비스 기능 테스트 사례 예제


| 비즈니스 사례| 예상된 결과 |
| - | - |
| 사용자가 응용 프로그램에 대 한 멤버 자격을 관리할 수 있음| 사용자는 앱에 대 한 액세스 권한이 있는 구성원을 추가/제거할 수 있습니다. |
| 사용자가 응용 프로그램을 편집할 수 있습니다.| 사용자는 암호 SSO 응용 프로그램에 대 한 응용 프로그램의 설명 및 자격 증명을 편집할 수 있습니다. |

### <a name="rollback-steps"></a>롤백 단계

배포가 계획 대로 진행 되지 않는 경우 수행할 작업을 계획 하는 것이 중요 합니다. 배포 하는 동안 SSO 구성이 실패할 경우 [sso 문제를 해결](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sso) 하 고 사용자에 대 한 영향을 줄이는 방법을 이해 해야 합니다. 극단적인 경우 [SSO를 롤백해야](../manage-apps/plan-sso-deployment.md)할 수 있습니다.


## <a name="manage-your-implementation"></a>구현 관리

Azure Active Directory 내에서 필요한 작업을 수행 하려면 최소 권한 있는 역할을 사용 하는 것이 좋습니다. [사용 가능한 다른 역할을 검토](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) 하 고 올바른 역할을 선택 하 여이 응용 프로그램의 각 가상 사용자에 대 한 요구 사항을 해결 하는 것이 좋습니다. 일부 역할은 배포를 완료 한 후 임시로 적용 해야 할 수도 있습니다.

| 가상 사용자| 역할| Azure AD 역할  |
| - | -| -|
| 지원 센터 관리자| 계층 1 지원| 없음 |
| Id 관리| 문제가 Azure AD에 영향을 주는 경우 구성 및 디버그| 전역 관리자 |
| 응용 프로그램 관리자| 응용 프로그램의 사용자 증명, 권한이 있는 사용자의 구성| 없음 |
| 인프라 관리자| 인증서 롤오버 소유자| 전역 관리자 |
| 비즈니스 소유자/관련자| 응용 프로그램의 사용자 증명, 권한이 있는 사용자의 구성| 없음 |

[Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) 사용 하 여 디렉터리 권한이 있는 사용자에 게 추가 감사, 제어 및 액세스 검토를 제공 하는 역할을 관리 하는 것이 좋습니다.

### <a name="troubleshoot-access-panel-issues"></a>액세스 패널 문제 해결

일반적인 시나리오를 사용 하 여 지원 조직에 대 한 문제 해결 가이드를 만들고 Microsoft 문서에서 해결 방법을 가리킵니다. 조직에서 사용 하는 계층에 대 한 지원을 중단 하는 가이드를 만들 수 있습니다.

참조는 아래 문제 해결 가이드를 참조 하세요.

[응용 프로그램이 나타나지 않음](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-application-not-appearing)

[예기치 않은 응용 프로그램이 나타납니다.](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-unexpected-application)

[사용자가 액세스 패널에 로그인 할 수 없음](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-web-sign-in-problem)

[셀프 서비스 응용 프로그램 액세스를 사용 하는 문제](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-self-service-access)

[브라우저 확장 문제](https://docs.microsoft.com/azure/active-directory/manage-apps/manage-access-panel-browser-extension)

## <a name="next-steps"></a>다음 단계

[Multi-factor authentication Azure Active Directory 배포 계획](https://aka.ms/deploymentplans/mfa)
