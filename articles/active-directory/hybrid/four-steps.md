---
title: 강력한 ID 기반을 위한 네 단계 - Azure AD
description: 이 항목에서는 강력한 ID 기반을 구축하기 위해 하이브리드 ID 고객이 취할 수 있는 네 가지 단계에 대해 설명합니다.
services: active-directory
author: martincoetzer
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/20/2019
ms.subservice: hybrid
ms.author: martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: d3eb98f543e17981be0d5b9ab08fa4e146659b47
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74206790"
---
# <a name="four-steps-to-a-strong-identity-foundation-with-azure-active-directory"></a>Azure Active Directory를 사용하여 강력한 ID 기반을 구축하기 위한 4단계

앱과 데이터에 대한 액세스를 관리하는 것은 클라우드로 앱이 빠르게 이동하기 때문에 주변 네트워크 및 방화벽과 같은 기존 네트워크 보안 경계 전략에 더 이상 의존할 수 없습니다. 이제 조직은 조직의 앱 과 데이터에 액세스할 수 있는 사람과 권한을 제어하기 위해 ID 솔루션을 신뢰해야 합니다. 더 많은 조직에서 직원들이 인터넷에 연결할 수 있는 모든 위치에서 자신의 장치를 작업하고 사용할 수 있도록 허용하고 있습니다. 이러한 장치가 규정을 준수하고 보안을 보장하는 것은 조직이 구현하기로 선택한 ID 솔루션에서 중요한 고려 사항이 되었습니다. 오늘날의 디지털 업무 환경에서 [ID는 클라우드로](https://www.microsoft.com/security/technology/identity-access-management?rtc=1) 이동하는 모든 조직의 주요 제어 국입니다.

Azure Active Directory(Azure AD) 하이브리드 ID 솔루션을 채택할 때 조직은 자동화, 위임, 셀프 서비스 및 단일 사인온 기능을 통해 생산성을 제공하는 프리미엄 기능에 액세스할 수 있습니다. 이를 통해 작업자는 업무를 수행해야 하는 모든 곳에서 회사 리소스에 액세스할 수 있으며, IT 팀이 올바른 사람이 올바른 리소스에 액세스하여 안전한 생산성을 구축할 수 있도록 하여 해당 액세스를 제어할 수 있습니다.

이 모범 사례 체크리스트는 이러한 학습 을 바탕으로 조직에서 *강력한* ID 기반을 구축하기 위해 권장 작업을 신속하게 배포하는 데 도움이 됩니다.

* 앱에 쉽게 연결
* 모든 사용자에 대해 자동으로 하나의 ID 설정
* 사용자에게 안전하게 역량 강화
* 인사이트 운영

## <a name="step-1---connect-to-apps-easily"></a>1단계 - 앱에 쉽게 연결

앱을 Azure AD와 연결하면 단일 사인온(SSO)을 사용하도록 설정하고 사용자 프로비저닝을 수행하여 최종 사용자 생산성과 보안을 향상시킬 수 있습니다. Azure AD라는 한 곳에서 앱을 관리하면 관리 오버헤드를 최소화하고 보안 및 규정 준수 정책에 대한 단일 제어 지점을 달성할 수 있습니다.

이 섹션에서는 앱에 대한 사용자 액세스를 관리하고, 내부 앱에 대한 안전한 원격 액세스를 활성화하고, 앱을 Azure AD로 마이그레이션할 때의 이점을 다룹니다.

### <a name="make-apps-available-to-your-users-seamlessly"></a>사용자가 원활하게 앱을 사용할 수 있도록 만들기

Azure AD를 사용하면 관리자가 [Azure 포털의](https://portal.azure.com/)엔터프라이즈 응용 프로그램 갤러리에 [응용 프로그램을 추가할](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal) 수 있습니다. 엔터프라이즈 응용 프로그램 갤러리에 응용 프로그램을 추가하면 Azure AD를 ID 공급자로 사용하도록 응용 프로그램을 쉽게 구성할 수 있습니다. 또한 조건부 액세스 정책을 사용하여 응용 프로그램에 대한 사용자 액세스를 관리하고 응용 프로그램에 대한 단일 사인온(SSO)을 구성하여 사용자가 암호를 반복적으로 입력할 필요가 없으며 온-프레미스 및 및 온-프레미스 모두에 자동으로 로그인되도록 할 수 있습니다. 클라우드 기반 애플리케이션을 지원합니다.

응용 프로그램이 Azure AD 갤러리에 추가되면 사용자는 해당 사용자에게 할당된 앱을 보고 필요에 따라 다른 앱을 검색하고 요청할 수 있습니다. Azure AD는 사용자가 앱에 액세스할 수 있는 [몇 가지 방법을](https://docs.microsoft.com/azure/active-directory/manage-apps/end-user-experiences) 제공합니다.

* 액세스 패널/내 앱
* Office 365 앱 시작 관리자
* 페더레이션된 앱에 직접 로그온
* 직접 로그온 링크

앱에 대한 사용자 액세스에 대한 자세한 내용은 이 문서의 **3단계 -- 사용자 권한 부여를 참조하세요.**

### <a name="migrate-apps-from-active-directory-federation-services-to-azure-ad"></a>활성 디렉터리 페더레이션 서비스에서 Azure AD로 앱 마이그레이션

ADFS(Active Directory Federation Services)에서 Azure AD로 단일 사인온 구성을 마이그레이션하면 보안, 보다 일관된 관리 용이성 및 공동 작업에 대한 추가 기능을 사용할 수 있습니다. 최적의 결과를 얻으려면 앱을 AD FS에서 Azure AD로 마이그레이션하는 것이 좋습니다. 응용 프로그램 인증 및 권한 부여를 Azure AD로 가져오면 다음과 같은 이점이 제공됩니다.

* 비용 관리
* 위험 관리
* 생산성 향상
* 규정 준수 및 거버넌스 해결

자세한 내용은 응용 [프로그램을 Azure Active 디렉터리 백서로 마이그레이션하는 것을 참조하세요.](https://aka.ms/migrateapps/whitepaper)

### <a name="enable-secure-remote-access-to-apps"></a>앱에 대한 안전한 원격 액세스 지원

[Azure AD 응용 프로그램 프록시는](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-application-proxy) 조직이 안전한 방식으로 내부 앱에 액세스해야 하는 원격 사용자를 위해 온-프레미스 앱을 클라우드에 게시할 수 있는 간단한 솔루션을 제공합니다. Azure AD에 대한 단일 로그온 후 사용자는 외부 URL 또는 내부 응용 프로그램 포털을 통해 클라우드 및 온-프레미스 응용 프로그램에 모두 액세스할 수 있습니다.

Azure AD 응용 프로그램 프록시는 다음과 같은 이점을 제공합니다.

* Azure AD를 온-프레미스 리소스로 확장
  * 클라우드 규모 보안 및 보호
  * 쉽게 사용할 수 있는 조건부 액세스 및 다단계 인증과 같은 기능
* VPN 및 기존 역방향 프록시 솔루션과 같은 경계 네트워크의 구성 요소가 없습니다.
* 필요한 인바운드 연결 없음
* 클라우드 및 온-프레미스의 장치, 리소스 및 앱 전반에 걸친 단일 사인온(SSO)
* 최종 사용자가 언제 어디서나 생산성을 높일 수 있도록 합니다.

### <a name="discover-shadow-it-with-microsoft-cloud-app-security"></a>마이크로소프트 클라우드 앱 보안으로 섀도우 IT 살펴보기

오늘날의 기업에서 IT 부서는 종종 작업을 수행하는 사용자가 사용하는 일부 클라우드 응용 프로그램을 인식하지 못합니다. IT 관리자에게 직원들이 사용하는 클라우드 앱의 수를 묻는 메시지가 표시됩니다. 실제로 조직의 직원이 사용하는 평균 앱은 1,000개 가 넘습니다. 직원의 80%가 아무도 검토하지 않았으며 보안 및 규정 준수 정책을 준수하지 않을 수 있는 비승인 앱을 사용합니다.

[MCAS(Microsoft 클라우드 앱 보안)는](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) IT가 승인하고 엔터프라이즈 응용 프로그램 갤러리에 추가할 수 있는 사용자에게 널리 알려진 유용한 앱을 식별하여 사용자가 SSO 및 조건부 액세스와 같은 기능을 활용할 수 있도록 도와줍니다.

<em>"클라우드**앱 보안은** Accenture를 보호하는 데 도움이 되는 기본 보안 정책을 지원하는 방식으로 클라우드 및 SaaS 애플리케이션을 제대로 사용하고 있는지 확인하는 데 도움이 됩니다."</em> --- [존 블라시, 정보 보안 전무 이사, 액센츄어](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security)

MCAS는 섀도우 IT를 검색하는 것 외에도 앱의 위험 수준을 결정하고, 회사 데이터에 대한 무단 액세스를 방지하고, 데이터 유출 가능성 및 애플리케이션에 내재된 기타 보안 위험을 방지할 수 있습니다.

## <a name="step-2---establish-one-identity-for-every-user-automatically"></a>2단계 - 모든 사용자에 대해 자동으로 하나의 ID 설정

Azure AD 하이브리드 ID 솔루션에 온-프레미스 및 클라우드 기반 디렉터리를 함께 사용하면 클라우드에서 기존 ID를 프로비전하여 기존 온-프레미스 Active Directory 투자를 재사용할 수 있습니다. 이 솔루션은 온-프레미스 ID를 Azure AD와 동기화하는 반면 IT는 온-프레미스 Active Directory를 기존 거버넌스 솔루션과 함께 실행상태로 유지합니다. Microsoft의 Azure AD 하이브리드 ID 솔루션은 온-프레미스 및 클라우드 기반 기능에 걸쳐 있으며, 위치에 관계없이 모든 리소스에 대한 인증 및 권한 부여에 대한 공통 사용자 ID를 만듭니다.

온-프레미스 디렉터리를 Azure AD와 통합하면 사용자가 생산성을 높이고 클라우드 및 온-프레미스 리소스에 모두 액세스할 수 있는 공통 ID를 제공하여 앱 및 서비스 전반에서 여러 계정을 사용하지 못하게 됩니다. 여러 계정을 사용하는 것은 최종 사용자와 IT 모두에게 큰 어려움을 겪습니다. 최종 사용자 관점에서 여러 계정을 보유한다는 것은 여러 암호를 기억해야 한다는 것을 의미합니다. 이를 방지하기 위해 많은 사용자가 각 계정에 대해 동일한 암호를 다시 사용하므로 보안 측면에서는 좋지 않습니다. IT 관점에서 재사용은 종종 최종 사용자 불만과 함께 더 많은 암호 재설정 및 헬프 데스크 비용으로 이어집니다.

Azure AD Connect는 온-프레미스 ID를 Azure AD에 동기화하는 데 사용되는 도구로, 클라우드 응용 프로그램에 액세스하는 데 사용할 수 있습니다. ID가 Azure AD에 있으면 Salesforce 또는 Concur와 같은 SaaS 응용 프로그램에 프로비전할 수 있습니다.

이 섹션에서는 클라우드에 대한 고가용성, 최신 인증을 제공하고 온-프레미스 공간을 줄이기 위한 권장 사항을 나열합니다.

> [!NOTE]
> Azure AD 연결에 대해 자세히 알아보려면 [Azure AD 연결 동기화란 무엇입니까?](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis)

### <a name="set-up-a-staging-server-for-azure-ad-connect-and-keep-it-up-to-date"></a>Azure AD Connect에 대한 스테이징 서버를 설정하고 최신 상태로 유지

Azure AD Connect는 프로비저닝 프로세스에서 중요한 역할을 합니다. 어떤 이유로든 동기화 서버가 오프라인 상태가 되면 온-프레미스에 대한 변경 내용이 클라우드에서 업데이트되지 않고 사용자에게 액세스 문제가 발생합니다. 동기화 서버가 오프라인 상태가 된 후 관리자가 동기화를 신속하게 다시 시작할 수 있는 장애 조치 전략을 정의하는 것이 중요합니다.

기본 Azure AD Connect 서버가 오프라인 상태가 될 경우 고가용성을 제공하려면 Azure AD Connect에 대해 별도의 [스테이징 서버를](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-staging-server) 배포하는 것이 좋습니다. 서버를 배포하면 관리자는 간단한 구성 스위치를 통해 스테이징 서버를 프로덕션 서버로 "승격"할 수 있습니다. 스테이징 모드에서 대기 서버를 구성하면 새 구성 변경 내용을 테스트 및 배포하고 이전 서버를 해제할 경우 새 서버를 도입할 수 있습니다.

> [!TIP]
> Azure AD 연결은 정기적으로 업데이트됩니다. 따라서 각 새 버전에서 제공하는 성능 향상, 버그 수정 및 새 기능을 활용하려면 스테이징 서버를 최신 상태로 유지하는 것이 좋습니다.

### <a name="enable-cloud-authentication"></a>클라우드 인증 사용

온-프레미스 Active Directory가 있는 조직은 Azure AD Connect를 사용하여 해당 디렉터리를 Azure AD로 확장하고 적절한 인증 방법을 구성해야 합니다. 조직에 [적합한 인증 방법을 선택하는](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn) 것은 앱을 클라우드로 이동하는 여정의 첫 번째 단계입니다. 모든 클라우드 데이터 및 리소스에 대한 액세스를 제어하기 때문에 중요한 구성 요소입니다.

Azure AD에서 온-프레미스 디렉터리 개체에 대한 클라우드 인증을 사용하도록 설정하는 가장 간단하고 권장되는 방법은 [PHS(암호 해시 동기화)를](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-password-hash-synchronization) 사용하도록 설정하는 것입니다. 또는 일부 [조직에서는](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-quick-start) PTA(통과 인증)를 사용하도록 설정하는 것을 고려할 수 있습니다.

PHS 또는 PTA를 선택하든 관계없이 회사 네트워크에서 Windows 7 및 8 장치를 사용할 때 사용자가 앱에서 사용자 이름과 암호를 지속적으로 입력하지 않고도 클라우드 앱에 액세스할 수 있도록 [원활한 Single Sign-On을](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso) 사용하도록 설정하는 것을 잊지 마십시오. 단일 사인온이 없으면 사용자는 응용 프로그램별 암호를 기억하고 각 응용 프로그램에 로그인해야 합니다. 마찬가지로 IT 직원은 Office 365, Box 및 Salesforce와 같은 각 응용 프로그램에 대한 사용자 계정을 만들고 업데이트해야 합니다. 사용자는 암호를 기억하고 각 응용 프로그램에 로그인하는 데 시간을 할애해야 합니다. 전체 엔터프라이즈에 표준화된 단일 사인온 메커니즘을 제공하는 것은 최상의 사용자 경험, 위험 감소, 보고 능력 및 거버넌스에 매우 중요합니다.

이미 AD FS 또는 다른 온-프레미스 인증 공급자를 사용 하는 조직의 경우 ID 공급자로 Azure AD로 이동 하면 복잡성을 줄이고 가용성을 향상 시킬 수 있습니다. 페더레이션 사용에 대한 특정 사용 사례가 없는 경우, 퍼미언드 인증에서 PHS 및 원활한 SSO 또는 PTA 및 원활한 SSO로 마이그레이션하여 온-프레미스 설치 공간 감소의 이점과 클라우드에서 제공하는 유연성을 누리는 것이 좋습니다. 향상된 사용자 경험. 자세한 내용은 [Azure Active Directory에 대한 페더레이션에서 암호 해시 동기화로 마이그레이션을](https://docs.microsoft.com/azure/active-directory/hybrid/plan-migrate-adfs-password-hash-sync)참조하십시오.

### <a name="enable-automatic-deprovisioning-of-accounts"></a>계정 자동 디프로비저닝 사용

응용 프로그램에 대한 자동 프로비저닝 및 프로비저닝 해제를 사용하도록 설정하는 것은 여러 시스템에서 ID의 수명 주기를 관리하는 가장 좋은 전략입니다. Azure AD는 ServiceNow 및 Salesforce 및 [SCIM 2.0 프로토콜을](https://docs.microsoft.com/azure/active-directory/manage-apps/use-scim-to-provision-users-and-groups)구현하는 다른 응용 프로그램과 같은 다양한 인기 있는 SaaS 응용 프로그램에 대한 사용자 계정의 [자동화된 정책 기반 프로비저닝 및 프로비저닝을](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-automatic-user-provisioning-portal) 지원합니다. 사용자 지정 코드 또는 CSV 파일의 수동 업로드가 필요한 기존 프로비저닝 솔루션과 달리 프로비저닝 서비스는 클라우드에서 호스팅되며 Azure 포털을 사용하여 설정하고 관리할 수 있는 사전 통합 커넥터를 제공합니다. 자동 프로비저닝 해제의 주요 이점은 조직을 떠날 때 주요 SaaS 앱에서 사용자의 ID를 즉시 제거하여 조직을 보호하는 데 도움이 된다는 것입니다.

자동 사용자 계정 프로비전 및 작동 방식에 대한 자세한 내용은 [Azure Active Directory를 사용하여 SaaS 애플리케이션에 사용자 프로비전 및 프로비전 해제 자동화](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)를 참조하세요.

## <a name="step-3---empower-your-users-securely"></a>3단계 - 사용자에게 안전하게 역량 강화

오늘날의 디지털 업무 환경에서는 보안과 생산성의 균형을 맞추는 것이 중요합니다. 그러나 최종 사용자는 생산성과 클라우드 앱에 대한 액세스를 느리게 하는 보안 조치를 철회하는 경우가 많습니다. 이 문제를 해결하기 위해 Azure AD는 사용자가 관리 오버헤드를 최소화하면서 생산성을 유지할 수 있도록 셀프 서비스 기능을 제공합니다.

이 섹션에서는 경계를 유지하면서 사용자에게 권한을 부여하여 조직에서 마찰을 제거하는 권장 사항을 나열합니다.

### <a name="enable-self-service-password-reset-for-all-users"></a>모든 사용자에 대해 셀프 서비스 암호 재설정 사용

Azure의 [셀프 서비스 암호](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr) 재설정(SSPR)은 IT 관리자가 사용자가 관리자의 개입 없이 암호 또는 계정을 재설정하고 잠금을 해제할 수 있는 간단한 방법을 제공합니다. 이 시스템에는 오용 또는 남용에 대해 경고하는 알림과 함께 사용자가 언제 시스템에 액세스하는지 추적하는 구체적인 보고서가 포함되어 있습니다.

기본적으로 Azure AD는 암호 재설정을 수행할 때 계정을 잠금 해제합니다. 그러나 Azure AD Connect [통합 온-프레미스를](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks#on-premises-integration)사용하도록 설정하면 사용자가 암호를 재설정하지 않고도 계정 잠금을 해제할 수 있도록 이러한 두 작업을 분리할 수도 있습니다.

### <a name="ensure-all-users-are-registered-for-mfa-and-sspr"></a>모든 사용자가 MFA 및 SSPR에 등록되어 있는지 확인

Azure는 사용자가 MFA 및 SSPR에 등록되도록 하기 위해 사용자와 조직에서 사용할 수 있는 보고서를 제공합니다. 등록하지 않은 사용자는 프로세스에 대해 교육을 받아야 할 수 있습니다.

MFA [로그인 보고서에는](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-reporting) MFA 사용에 대한 정보가 포함되어 있으며 MFA가 조직에서 작동하는 방식에 대한 통찰력을 제공합니다. Azure AD에 대한 로그인 활동(및 감사 및 위험 검색)에 액세스하는 것은 문제 해결, 사용 분석 및 포렌식 조사에 매우 중요합니다.

마찬가지로 셀프 [서비스 암호 관리 보고서를](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-reporting) 사용하여 SSPR에 등록된 사용자(또는 등록하지 않은 사람)를 확인할 수 있습니다.

### <a name="self-service-app-management"></a>셀프 서비스 앱 관리

사용자가 액세스 패널에서 응용 프로그램을 자체 검색하려면 사용자가 자체 검색하고 액세스를 요청할 수 있도록 하려는 모든 응용 프로그램에 대한 [셀프 서비스 응용 프로그램](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-manage-self-service-access) 액세스를 사용하도록 설정해야 합니다. 셀프 서비스 응용 프로그램 액세스는 사용자가 응용 프로그램을 자체 검색하고 비즈니스 그룹이 해당 응용 프로그램에 대한 액세스를 승인할 수 있도록 하는 좋은 방법입니다. 비즈니스 그룹이 액세스 패널에서 바로 [암호 Single-Sign On 응용 프로그램에](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-password-sso-gallery#configure-password-sso-for-an-azure-ad-gallery-app) 대해 해당 사용자에게 할당된 자격 증명을 관리하도록 허용할 수 있습니다.

### <a name="self-service-group-management"></a>셀프 서비스 그룹 관리

사용자를 응용 프로그램에 할당하는 것은 대규모로 관리할 수 있는 뛰어난 유연성과 기능을 허용하기 때문에 그룹을 사용할 때 가장 잘 매핑됩니다.

* 동적 그룹 구성원자격을 사용하는 특성 기반
* 앱 소유자에 대한 위임

Azure AD는 보안 그룹 및 Office 365 그룹을 사용하여 리소스에 대한 액세스를 관리하는 기능을 제공합니다. 이러한 그룹은 구성원 자격 요청을 승인 또는 거부하고 그룹 구성원의 제어를 위임할 수 있는 그룹 소유자가 관리할 수 있습니다. 셀프 [서비스 그룹 관리라고](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-self-service-management)하는 이 기능은 관리 역할이 할당되지 않은 그룹 소유자가 관리자가 요청을 처리하는 데 의존하지 않고도 그룹을 만들고 관리할 수 있도록 하여 시간을 절약합니다.

## <a name="step-4---operationalize-your-insights"></a>4단계 - 인사이트 운영

보안 관련 이벤트 및 관련 경고를 감사하고 로깅하는 것은 사용자의 생산성을 유지하고 조직의 보안을 보장하기 위한 효율적인 전략의 필수 구성 요소입니다. 보안 로그 및 보고서는 다음과 같은 질문에 답하는 데 도움이 될 수 있습니다.

* 당신은 당신이 지불하는 것을 사용하고 있습니까?
* 테넌트에서 의심스럽거나 악의적인 일이 발생합니까?
* 보안 사고 중에 영향을 받은 사람은 누구입니까?

보안 로그 및 보고서는 의심스러운 활동에 대한 전자 기록을 제공하며, 네트워크의 외부 침투와 내부 공격의 시도 또는 성공을 나타낼 수 있는 패턴을 검색할 수 있도록 도움을 줍니다. 감사를 사용하여 사용자 활동을 모니터링하고, 규정 준수를 문서화하고, 포렌식 분석을 수행하는 등의 작업을 수행할 수 있습니다. 경고는 보안 이벤트의 알림을 제공합니다.

### <a name="assign-least-privileged-admin-roles-for-operations"></a>작업에 최소 권한 관리자 역할 할당

운영에 대한 접근 방식에 대해 생각할 때 고려해야 할 몇 가지 관리 수준이 있습니다. 첫 번째 수준은 글로벌 관리자에게 관리 부담을 줍니다. 항상 글로벌 관리자 역할을 사용하는 것이 소규모 회사에 적합할 수 있습니다. 그러나 특정 작업을 담당하는 헬프 데스크 직원과 관리자를 담당하는 대규모 조직의 경우, 글로벌 관리자의 역할을 할당하는 것은 해당 개인에게 위와 그 이상의 작업을 관리할 수 있는 기능을 제공하기 때문에 보안 위험이 될 수 있습니다. 그들이 할 수 있어야 하는 일.

이 경우 다음 관리 수준을 고려해야 합니다. Azure AD를 사용하여 최종 사용자를 권한이 낮은 역할에서 작업을 관리할 수 있는 "제한된 관리자"로 지정할 수 있습니다. 예를 들어 헬프 데스크 직원에게 [보안 판독기](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#security-reader) 역할을 할당하여 읽기 전용 액세스로 보안 관련 기능을 관리할 수 있는 기능을 제공할 수 있습니다. 또는 [인증 관리자](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#authentication-administrator) 역할을 개인에게 할당하여 암호가 아닌 자격 증명을 재설정하거나 Azure 서비스 상태를 읽고 구성하는 기능을 제공하는 것이 합리적일 수 있습니다.

자세한 내용은 [Azure Active Directory의 관리자 역할 권한을](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)참조하십시오.

### <a name="monitor-hybrid-components-azure-ad-connect-sync-ad-fs-using-azure-ad-connect-health"></a>Azure AD 연결 상태를 사용하여 하이브리드 구성 요소(Azure AD 연결 동기화, AD FS) 모니터링

Azure AD 연결 및 AD FS는 수명 주기 관리 및 인증을 중단하고 궁극적으로 가동 중단으로 이어질 수 있는 중요한 구성 요소입니다. 따라서 이러한 구성 요소를 모니터링 하고 보고하기 위해 Azure AD Connect 상태를 배포해야 합니다.

자세한 내용은 Azure [AD Connect 상태를 사용하여 모니터 AD FS를](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-adfs)참조하십시오.

### <a name="use-azure-monitor-to-collect-data-logs-for-analytics"></a>Azure 모니터를 사용하여 분석을 위한 데이터 로그 수집

[Azure Monitor는](https://docs.microsoft.com/azure/azure-monitor/overview) 모든 Azure AD 로그에 대한 통합 모니터링 포털로, 심층적인 통찰력, 고급 분석 및 스마트 머신 러닝을 제공합니다. Azure Monitor를 사용하면 포털 및 API를 통해 메트릭 및 로그를 사용하여 리소스의 상태 및 성능에 대한 가시성을 높일 수 있습니다. 기존 타사 SIEM 시스템을 지원하는 API 및 데이터 내보내기 옵션을 통해 광범위한 제품 통합을 지원하면서 포털 내에서 단일 창 을 사용할 수 있습니다. 또한 Azure Monitor는 경고 규칙을 구성하여 알림을 받을 수 있도록 구성하거나 리소스에 영향을 주는 문제에 대해 자동화된 작업을 수행할 수 있는 기능을 제공합니다.

![Azure Monitor](./media/four-steps/image1.png)

### <a name="create-custom-dashboards-for-your-leadership-and-your-day-to-day"></a>리더십과 일상을 위한 맞춤형 대시보드 만들기

SIEM 솔루션이 없는 조직은 Azure AD에 대한 [Power BI 콘텐츠 팩을](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-power-bi-content-pack) 다운로드할 수 있습니다. Power BI 콘텐츠 팩에는 사용자가 Azure AD 기능을 채택하고 사용하는 방법을 이해하는 데 도움이 되는 미리 빌드된 보고서가 포함되어 있어 디렉터리 내의 모든 활동에 대한 통찰력을 얻을 수 있습니다. 사용자 [지정 대시보드를](https://docs.microsoft.com/power-bi/service-dashboards) 만들고 리더십 팀과 공유하여 일상적인 활동을 보고할 수도 있습니다. 대시보드는 비즈니스를 모니터링하고 가장 중요한 모든 메트릭을 한눈에 확인할 수 있는 좋은 방법입니다. 대시보드의 시각화는 하나 또는 여러 개의 기본 데이터 세트, 하나 또는 여러 개의 기본 보고서에서 제공될 수 있습니다. 대시보드는 온-프레미스 및 클라우드 데이터를 결합하여 데이터의 현재 위치에 관계없이 통합된 보기를 제공합니다.

![Power BI 사용자 지정 대시보드](./media/four-steps/image2.png)

### <a name="understand-your-support-call-drivers"></a>지원 통화 드라이버 이해

이 문서에서 설명한 대로 하이브리드 ID 솔루션을 구현할 때 궁극적으로 지원 호출이 줄어듭니다. 잊어버린 암호 및 계정 잠금과 같은 일반적인 문제는 Azure의 셀프 서비스 암호 재설정을 구현하여 완화되는 반면 셀프 서비스 응용 프로그램 액세스를 사용하면 사용자가 의존하지 않고 응용 프로그램에 대한 액세스를 자체 검색하고 요청할 수 있습니다. IT 직원에게 문의할 수 있습니다.

지원 호출이 줄어드는 것을 관찰하지 않는 경우 SSPR 또는 셀프 서비스 응용 프로그램 액세스가 올바르게 구성되었는지 또는 체계적으로 구성할 수 있는 다른 새로운 문제가 있는지 확인하기 위해 지원 호출 드라이버를 분석하는 것이 좋습니다. 해결.

*"디지털 혁신 여정에서 우리는 효과적인 생태계를 위해 당사, 파트너 및 클라우드 서비스 제공업체 간의 원활하면서도 안전한 통합을 촉진하기 위해 신뢰할 수 있는 ID 및 액세스 관리 공급자가 필요했습니다. Azure AD는 위험을 감지하고 대응할 수 있는 필요한 기능과 가시성을 제공하는 최상의 옵션이었습니다."* --- [야잔 알마스리, 아라멕스 글로벌 정보 보안 디렉터](https://customers.microsoft.com/story/aramex-azure-active-directory-travel-transportation-united-arab-emirates-en)

### <a name="monitor-your-usage-of-apps-to-drive-insights"></a>앱 사용 모니터링을 통해 인사이트 조사

Shadow IT를 발견하는 것 외에도 [Microsoft Cloud App Security를](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) 사용하여 조직 전체에서 앱 사용을 모니터링하면 클라우드 응용 프로그램의 약속을 최대한 활용하기 위해 이동하면서 조직에 도움이 될 수 있습니다. 이를 통해 활동에 대한 가시성을 개선하고 클라우드 애플리케이션 전반에 걸쳐 중요한 데이터의 보호를 강화하여 자산을 제어할 수 있습니다. MCAS를 사용하여 조직에서 앱 사용을 모니터링하면 다음 질문에 답하는 데 도움이 될 수 있습니다.

* 직원들이 데이터를 저장하는 데 사용하는 승인되지 않은 앱은 무엇입니까?
* 중요한 데이터가 클라우드에 언제 어디서 저장되나요?
* 클라우드에서 중요한 데이터에 액세스하는 사람은 누구입니까?

*"클라우드 앱 보안을 통해 이상 징후를 신속하게 발견하고 조치를 취할 수 있습니다."* --- [에릭 르펜스케, 정보 보안 담당 수석 매니저, 액센츄어](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security)

## <a name="summary"></a>요약

하이브리드 ID 솔루션을 구현하는 데는 여러 가지 측면이 있지만 이 4단계 검사 목록은 사용자가 생산성과 보안을 강화할 수 있는 ID 인프라를 신속하게 수행하는 데 도움이 됩니다.

* 앱에 쉽게 연결
* 모든 사용자에 대해 자동으로 하나의 ID 설정
* 사용자에게 안전하게 역량 강화
* 인사이트 운영

이 문서가 조직에 대한 강력한 ID 기반을 구축하는 데 유용한 로드맵이 되기를 바랍니다.

## <a name="identity-checklist"></a>ID 확인 목록

조직에서 보다 견고한 ID 기반을 향한 여정을 시작할 때 참조할 수 있도록 다음 검사 목록을 인쇄하는 것이 좋습니다.

### <a name="today"></a>오늘

|등록했나요?|항목|
|:-|:-|
||그룹에 대한 파일럿 셀프 서비스 암호 재설정(SSPR)|
||Azure AD 연결 상태를 사용하여 하이브리드 구성 요소 모니터링|
||작업에 최소 권한 관리자 역할 할당|
||마이크로소프트 클라우드 앱 보안으로 섀도우 IT 살펴보기|
||Azure 모니터를 사용하여 분석을 위한 데이터 로그 수집|

### <a name="next-two-weeks"></a>다음 2주

|등록했나요?|항목|
|:-|:-|
||사용자가 앱을 사용할 수 있도록 만들기|
||선택한 SaaS 앱에 대한 파일럿 Azure AD 프로비저닝|
||Azure AD Connect에 대한 스테이징 서버를 설정하고 최신 상태로 유지|
||ADFS에서 Azure AD로 앱 마이그레이션 시작|
||리더십과 일상을 위한 맞춤형 대시보드 만들기|

### <a name="next-month"></a>다음 달

|등록했나요?|항목|
|:-|:-|
||앱 사용 모니터링을 통해 인사이트 조사|
||앱에 대한 파일럿 보안 원격 액세스|
||모든 사용자가 MFA 및 SSPR에 등록되어 있는지 확인|
||클라우드 인증 사용|

### <a name="next-three-months"></a>다음 3개월

|등록했나요?|항목|
|:-|:-|
||셀프 서비스 앱 관리 활성화|
||셀프 서비스 그룹 관리 지원|
||앱 사용 모니터링을 통해 인사이트 조사|
||지원 통화 드라이버 이해|

## <a name="next-steps"></a>다음 단계

Azure Active Directory및 이 5단계 체크리스트의 기능을 사용하여 보안 상태를 높이는 방법 - [ID 인프라를 보호하기 위한 5단계](https://aka.ms/securitysteps)에 대해 알아봅니다.

Azure AD의 ID 기능을 통해 조직이 기존 온-프레미스 시스템에서 Azure AD로 더 많은 ID 관리를 신속하게 채택하고 이동할 수 있는 솔루션과 기능을 제공하여 클라우드 관리 관리로의 전환을 가속화하는 [방법을](https://aka.ms/cloudgoverned)알아봅니다.
