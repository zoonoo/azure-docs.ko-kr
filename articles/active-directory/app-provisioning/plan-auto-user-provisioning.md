---
title: Azure Active Directory에 대한 자동 사용자 프로비저닝 배포 계획
description: 자동 사용자 프로비저닝 계획 및 실행 지침
services: active-directory
author: martincoetzer
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 10/17/2019
ms.author: martinco
ms.reviewer: arvindha
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d2f284fddfc49632e467adbf5877856b40a81dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522413"
---
# <a name="plan-an-automatic-user-provisioning-deployment"></a>자동 사용자 프로비저닝 배포 계획

대부분의 조직에서는 최종 사용자 생산성을 위해 ServiceNow, Zscaler 및 Slack과 같은 SaaS(서비스) 응용 프로그램으로 소프트웨어를 사용합니다. 지금까지 IT 직원은 CSV 파일을 업로드하거나 사용자 지정 스크립트를 사용하여 각 SaaS 응용 프로그램에서 사용자 ID를 안전하게 관리하는 것과 같은 수동 프로비저닝 방법에 의존해 왔습니다. 이러한 프로세스는 오류가 발생하기 쉽고 안전하지 않으며 관리하기 가 어렵습니다.

Azure Active Directory(Azure AD) 자동 사용자 프로비저닝은 비즈니스 규칙에 따라 SaaS 응용 프로그램에서 사용자 ID를 생성, 유지 관리 및 제거하는 작업을 안전하게 자동화하여 이 프로세스를 단순화합니다. 이러한 자동화를 통해 클라우드 기반 솔루션에 대한 의존도를 확장하면서 클라우드 전용 및 하이브리드 환경 모두에서 ID 관리 시스템을 효과적으로 확장할 수 있습니다.

기능을 더 잘 [이해하려면 Azure Active Directory를 사용하여 SaaS 응용 프로그램에 대한 사용자 프로비전 및 프로비저닝 해제를](../app-provisioning/user-provisioning.md) 자동화합니다.

## <a name="learn"></a>배우기

사용자 프로비저닝은 지속적인 ID 거버넌스의 토대를 마련하고 신뢰할 수 있는 ID 데이터에 의존하는 비즈니스 프로세스의 품질을 향상시킵니다.

### <a name="key-benefits"></a>주요 이점

자동 사용자 프로비저닝을 사용하도록 설정하는 주요 이점은 다음과 같습니다.

* **생산성 향상.** 단일 사용자 프로비저닝 관리 인터페이스를 사용하여 SaaS 응용 프로그램에서 사용자 ID를 관리할 수 있습니다. 이 인터페이스에는 단일 프로비저닝 정책 집합이 있습니다.

* **위험 관리**. 역할 및/또는 액세스를 정의하는 직원 상태 또는 그룹 구성원 자격에 따라 변경 내용을 자동화하여 보안을 강화할 수 있습니다.

* **규정 준수 및 거버넌스를 해결합니다.** Azure AD는 모든 사용자 프로비저닝 요청에 대해 기본 감사 로그를 지원합니다. 요청은 원본 및 대상 시스템 모두에서 실행됩니다. 이렇게 하면 단일 화면에서 응용 프로그램에 액세스할 수 있는 사람을 추적할 수 있습니다.

* **비용을 절감합니다.** 자동 사용자 프로비저닝은 수동 프로비저닝과 관련된 비효율성과 인적 오류를 방지하여 비용을 절감합니다. 사용자 지정 프로비저닝 솔루션, 스크립트 및 감사 로그가 필요하지 않습니다.

### <a name="licensing"></a>라이선스

Azure AD는 응용 프로그램 갤러리 메뉴에 제공된 템플릿을 사용하여 모든 응용 프로그램의 셀프 서비스 통합을 제공합니다. 전체 라이센스 요구 사항 목록은 [Azure AD 라이선스 페이지를](https://azure.microsoft.com/pricing/details/active-directory/)참조하십시오.

#### <a name="application-licensing"></a>응용 프로그램 라이선스

자동으로 프로비전하려는 응용 프로그램에 적합한 라이선스가 필요합니다. 응용 프로그램에 할당된 사용자가 응용 프로그램 역할에 적합한 라이선스를 가지고 있는지 응용 프로그램 소유자와 상의합니다. Azure AD가 역할에 따라 자동 프로비저닝을 관리하는 경우 Azure AD에 할당된 역할은 응용 프로그램 라이선스에 맞춰야 합니다. 응용 프로그램에 소유된 잘못된 라이선스는 사용자의 프로비저닝/업데이트 중에 오류가 발생할 수 있습니다.

### <a name="terms"></a>용어

이 문서에서는 다음 용어를 사용합니다.

* CRUD 작업 - 사용자 계정에서 수행한 작업: 만들기, 읽기, 업데이트, 삭제.

* 단일 사인온(SSO) - 사용자가 한 번 로그인하고 모든 SSO 지원 응용 프로그램에 액세스할 수 있는 기능입니다. 사용자 프로비저닝의 맥락에서 SSO는 사용자가 자동 사용자 프로비전을 사용하는 모든 시스템에 액세스할 수 있는 단일 계정을 보유한 결과입니다.

* 원본 시스템 - Azure AD에서 프로비전하는 사용자의 리포지토리입니다. Azure AD는 대부분의 사전 통합된 프로비저닝 커넥터의 원본 시스템입니다. 그러나 SAP, Workday 및 AWS와 같은 클라우드 애플리케이션에는 몇 가지 예외가 있습니다. 예를 들어 [Workday에서 AD로 사용자 프로비저닝을](../saas-apps/workday-inbound-tutorial.md)참조하십시오.

* 대상 시스템 - Azure AD가 프로비전하는 사용자의 리포지토리입니다. 대상 시스템은 일반적으로 ServiceNow, Zscaler 및 Slack과 같은 SaaS 응용 프로그램입니다. 대상 시스템은 AD와 같은 온-프레미스 시스템일 수도 있습니다.

* [SCIM(도메인 간 ID 관리 시스템)](https://aka.ms/scimoverview) - 사용자 프로비저닝의 자동화를 허용하는 개방형 표준입니다. SCIM은 Microsoft와 같은 ID 공급자와 Salesforce 또는 사용자 ID 정보가 필요한 기타 SaaS 앱과 같은 서비스 공급자 간에 사용자 ID 데이터를 통신합니다.

### <a name="training-resources"></a>교육 리소스

| 리소스| 링크 및 설명 |
| - | - |
| 주문형 웹 세미나| [Azure AD로 엔터프라이즈 응용 프로그램 관리](https://info.microsoft.com/CO-AZUREPLAT-WBNR-FY18-03Mar-06-ManageYourEnterpriseApplicationsOption1-MCW0004438_02OnDemandRegistration-ForminBody.html)<br>Azure AD가 엔터프라이즈 SaaS 응용 프로그램에 대한 SSO를 달성하는 데 어떻게 도움이 되는지 알아보고 액세스를 제어하기 위한 모범 사례를 알아봅니다. |
| 비디오| [활성 Azure 디렉터리에서 사용자 프로비전이란 무엇입니까?](https://youtu.be/_ZjARPpI6NI) <br> [활성 Azure 디렉터리에서 사용자 프로비저닝을 배포하는 방법은 무엇입니까?](https://youtu.be/pKzyts6kfrw) <br> [Salesforce와 Azure 광고 통합: 사용자 프로비저닝자동화 방법](https://azure.microsoft.com/resources/videos/integrating-salesforce-with-azure-ad-how-to-automate-user-provisioning/) |
| 온라인 과정| 온라인 스킬업: [ID 관리](https://skillup.online/courses/course-v1:Microsoft+AZ-100.5+2018_T3/about) <br> Azure AD를 여러 SaaS 응용 프로그램과 통합하고 해당 응용 프로그램에 대한 사용자 액세스를 보호하는 방법을 알아봅니다. |
| 책| [웹 응용 프로그램에 대한 Azure Active Directory를 사용 하 인 최신 인증 (개발자 참조) 1 판](https://www.amazon.com/Authentication-Directory-Applications-Developer-Reference/dp/0735696942/ref=sr_1_fkmr0_1?keywords=Azure+multifactor+authentication&qid=1550168894&s=gateway&sr=8-1-fkmr0).  <br> 이 가이드는 이러한 새로운 환경에 대한 Active Directory 인증 솔루션을 빌드하기 위한 신뢰할 수 있는 심층 적인 가이드입니다. |
| 자습서| Azure [AD와 SaaS 앱을 통합하는 방법에 대한 자습서 목록을](../saas-apps/tutorial-list.md)참조하십시오. |
| FAQ| 자동화된 사용자 프로비저닝에 대한 [자주 묻는 질문](../app-provisioning/user-provisioning.md) |

### <a name="solution-architectures"></a>솔루션 아키텍처

Azure AD 프로비전 서비스는 각 응용 프로그램 공급업체에서 제공하는 사용자 관리 API 끝점에 연결하여 사용자를 SaaS 앱 및 기타 시스템에 프로비전합니다. 이러한 사용자 관리 API 엔드포인트를 사용하면 Azure AD에서 프로그래밍 방식으로 사용자를 만들고, 업데이트하고, 제거할 수 있습니다.

#### <a name="automatic-user-provisioning-for-hybrid-enterprises"></a>하이브리드 기업을 위한 자동 사용자 프로비저닝

이 예제에서는 온-프레미스 디렉터리에 연결된 HR 데이터베이스에서 사용자 및 그룹이 만들어집니다. Azure AD 프로비저닝 서비스는 대상 SaaS 응용 프로그램에 대한 자동 사용자 프로비전을 관리합니다.

 ![사용자 프로비저닝](./media/plan-auto-user-provisioning/hybridprovisioning.png)

**워크플로에 대한 설명:**

1. 사용자/그룹은 SAP와 같은 온-프레미스 HR 응용 프로그램/시스템에서 만들어집니다. 

1. **Azure AD Connect 에이전트는** 로컬 AD에서 Azure AD에 이르는 사용자 및 그룹의 예약된 동기화를 실행합니다.

1. **Azure AD 프로비저닝 서비스는** 원본 시스템 및 대상 시스템에 대한 [초기 주기를](../app-provisioning/user-provisioning.md) 시작합니다. 

1. **Azure AD 프로비저닝 서비스는** 초기 주기 이후 변경된 모든 사용자 및 그룹에 대해 원본 시스템을 쿼리하고 [증분 주기에서](../app-provisioning/user-provisioning.md)변경 내용을 푸시합니다.

#### <a name="automatic-user-provisioning-for-cloud-only-enterprises"></a>클라우드 전용 기업을 위한 자동 사용자 프로비저닝

이 예제에서는 Azure AD에서 사용자 만들기가 발생하며 Azure AD 프로비저닝 서비스는 대상(SaaS) 응용 프로그램에 대한 자동 사용자 프로비저닝을 관리합니다.

![그림 2](./media/plan-auto-user-provisioning/cloudprovisioning.png)

**워크플로에 대한 설명:**

1. 사용자/그룹은 Azure AD에서 만들어집니다.

1. **Azure AD 프로비저닝 서비스는** 원본 시스템 및 대상 시스템에 대한 [초기 주기를](../app-provisioning/user-provisioning.md) 시작합니다. 

1. **Azure AD 프로비저닝 서비스는** 초기 주기 이후 업데이트된 모든 사용자 및 그룹에 대해 원본 시스템을 쿼리하고 [증분 주기를 수행합니다.](../app-provisioning/user-provisioning.md)

#### <a name="automatic-user-provisioning-for-cloud-hr-applications"></a>클라우드 HR 애플리케이션을 위한 자동 사용자 프로비저닝 

이 예제에서는 사용자 및 그룹작업일 및 SuccessFactors와 같은 클라우드 HR 응용 프로그램에서 만들어집니다. Azure AD 프로비전 서비스 및 Azure AD Connect 프로비전 에이전트는 클라우드 HR 앱 테넌트의 사용자 데이터를 AD로 프로비전합니다. AD에서 계정이 업데이트되면 Azure AD Connect를 통해 Azure AD와 동기화되고 전자 메일 주소 및 사용자 이름 특성을 클라우드 HR 앱 테넌트에 다시 쓸 수 있습니다.

![그림 2](./media/plan-auto-user-provisioning/workdayprovisioning.png)

1.  **HR 팀은** 클라우드 HR 앱 테넌트에서 트랜잭션을 수행합니다.
2.  **Azure AD 프로비저닝 서비스는** 클라우드 HR 앱 테넌트에서 예약된 주기를 실행하고 AD와 동기화하기 위해 처리해야 하는 변경 내용을 식별합니다.
3.  **Azure AD 프로비저닝 서비스는** AD 계정 생성/업데이트/사용 안 함 작업을 포함하는 요청 페이로드를 사용하여 Azure AD Connect 프로비전 에이전트를 호출합니다.
4.  **Azure AD Connect 프로비저닝 에이전트는** 서비스 계정을 사용하여 AD 계정 데이터를 관리합니다.
5.  **Azure AD Connect는** 델타 동기화를 실행하여 AD에서 업데이트를 가져옵니다.
6.  **AD** 업데이트는 Azure AD와 동기화됩니다. 
7.  **Azure AD 프로비저닝 서비스는 Azure AD에서** 클라우드 HR 앱 테넌트에 대한 전자 메일 특성 및 사용자 이름을 기록합니다.

## <a name="plan-the-deployment-project"></a>구축 프로젝트 계획

사용자 환경에서 사용자 프로비저닝을 배포하기 위한 전략을 결정해야 하는 조직의 요구 사항을 고려하십시오.

### <a name="engage-the-right-stakeholders"></a>올바른 이해 관계자 참여

기술 프로젝트가 실패하면 일반적으로 영향, 결과 및 책임에 대한 불일치 기대가 있기 때문입니다. 이러한 위험을 피하기 위해 [올바른 이해 관계자와](https://aka.ms/deploymentplans) 프로젝트의 이해 관계자 역할을 잘 이해하여 이해 관계자와 프로젝트 입력 및 책임 사항을 문서화해야 합니다.

### <a name="plan-communications"></a>통신 계획

통신은 새로운 서비스의 성공에 매우 중요합니다. 사용자 환경이 어떻게 변할지, 언제 변경될지, 문제가 발생할 경우 지원을 받는 방법을 사전에 사용자와 전달합니다.

### <a name="plan-a-pilot"></a>파일럿 계획

자동 사용자 프로비저닝의 초기 구성은 프로덕션 환경에서 모든 사용자에게 크기를 조정하기 전에 작은 사용자 하위 집합이 있는 테스트 환경에 있는 것이 좋습니다. 시험 운용에 대한 [모범 사례를](../fundamentals/active-directory-deployment-plans.md#best-practices-for-a-pilot) 참조하십시오.

#### <a name="best-practices-for-a-pilot"></a>시험 운용을 위한 모범 사례  

파일럿을 사용하면 모든 사람을 위한 기능을 배포하기 전에 소규모 그룹으로 테스트할 수 있습니다. 테스트의 일환으로 조직 내의 각 사용 사례를 철저히 테스트해야 합니다.

첫 번째 물결에서 IT, 유용성 및 피드백을 테스트하고 제공할 수 있는 기타 적절한 사용자를 대상으로 합니다. 이 피드백을 사용하여 사용자에게 보내는 통신 및 지침을 더욱 개발하고 지원 담당자가 볼 수 있는 문제 유형에 대한 통찰력을 제공합니다.

대상 그룹의 범위를 늘려 더 큰 사용자 그룹으로 롤아웃을 확대합니다. 이 작업은 [동적 그룹 구성원 자격](../users-groups-roles/groups-dynamic-membership.md)또는 대상 그룹에 사용자를 수동으로 추가하여 수행할 수 있습니다.

## <a name="plan-application-connections-and-administration"></a>응용 프로그램 연결 및 관리 계획

Azure AD 포털을 사용하여 프로비저닝을 지원하는 모든 응용 프로그램을 보고 관리합니다. [포털에서 앱 찾기를](../app-provisioning/configure-automatic-user-provisioning-portal.md)참조하십시오.

### <a name="determine-the-type-of-connector-to-use"></a>사용할 커넥터 유형 결정

자동 프로비전을 사용하도록 설정하고 구성하는 데 필요한 실제 단계는 애플리케이션에 따라 달라집니다. 자동으로 프로비전하려는 응용 프로그램이 [Azure AD SaaS 앱 갤러리에](../saas-apps/tutorial-list.md)나열된 경우 [앱별 통합 자습서를](../saas-apps/tutorial-list.md) 선택하여 사전 통합된 사용자 프로비전 커넥터를 구성해야 합니다.

그렇지 않은 경우 아래 단계를 따르십시오.

1. 사전 통합된 사용자 프로비전 커넥터에 대한 [요청을 만듭니다.](../develop/howto-app-gallery-listing.md) 우리 팀은 SCIM을 지원하는 경우 사용자 및 응용 프로그램 개발자와 협력하여 응용 프로그램을 플랫폼에 온보온합니다.

1. 앱에 대한 [BYOA SCIM](../app-provisioning/use-scim-to-provision-users-and-groups.md) 일반 사용자 프로비전 지원을 사용합니다. 이는 Azure AD가 사전 통합된 프로비저닝 커넥터 없이 사용자를 앱에 프로비전하기 위한 요구 사항입니다.

1. 응용 프로그램이 BYOA SCIM 커넥터를 사용할 수 있는 경우 [BYOA SCIM 통합 자습서를](../app-provisioning/use-scim-to-provision-users-and-groups.md) 참조하여 응용 프로그램에 대한 BYOA SCIM 커넥터를 구성합니다.

자세한 내용은 [Azure AD 자동 사용자 프로비저닝에서 사용할 수 있는 응용 프로그램 및 시스템을 참조하세요.](../app-provisioning/user-provisioning.md)

### <a name="collect-information-to-authorize-application-access"></a>응용 프로그램 액세스 권한을 부여하기 위한 정보 수집

자동 사용자 프로비저닝 설정은 응용 프로그램별 프로세스입니다. 각 응용 프로그램에 대해 대상 시스템의 사용자 관리 끝점에 연결할 [관리자 자격 증명을](../app-provisioning/configure-automatic-user-provisioning-portal.md) 제공해야 합니다.

아래 이미지는 필요한 관리자 자격 증명의 한 버전을 보여 주며,

![사용자 계정 프로비저닝 설정을 관리하기 위한 프로비저닝 화면](./media/plan-auto-user-provisioning/userprovisioning-admincredentials.png)

일부 응용 프로그램에는 관리자 사용자 이름과 암호가 필요하지만 다른 응용 프로그램에는 bearer 토큰이 필요할 수 있습니다.

## <a name="plan-user-and-group-provisioning"></a>사용자 및 그룹 프로비저닝 계획

엔터프라이즈 앱에 대한 사용자 프로비전을 사용하도록 설정하면 [Azure 포털은](https://portal.azure.com/) 특성 매핑을 통해 특성 값을 제어합니다.

### <a name="determine-operations-for-each-saas-app"></a>각 SaaS 앱에 대한 작업 결정

각 응용 프로그램에는 Azure AD의 특성에 매핑해야 하는 고유한 사용자 또는 그룹 특성이 있을 수 있습니다. 응용 프로그램에는 CRUD 작업의 하위 집합만 사용할 수 있습니다.

각 응용 프로그램에 대해 다음 정보를 문서화합니다.

* CRUD 프로비저닝 작업은 대상 시스템에 대한 사용자 및 그룹 개체에 대해 수행되어야 합니다. 예를 들어 각 SaaS 앱 비즈니스 소유자는 가능한 모든 작업을 원하지 않을 수 있습니다.

* 소스 시스템에서 사용할 수 있는 특성

* 대상 시스템에서 사용할 수 있는 특성

* 시스템 간 특성 매핑

### <a name="choose-which-users-and-groups-to-provision"></a>프로비저닝할 사용자 및 그룹 선택

자동 사용자 프로비저닝을 구현하기 전에 응용 프로그램에 프로비전할 사용자 및 그룹을 결정해야 합니다.

* [범위 지정 필터를](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) 사용하여 응용 프로그램에 프로비전되는 사용자를 결정하는 특성 기반 규칙을 정의합니다.

* 다음으로 추가 필터링에 필요한 [사용자 및 그룹 할당을](../manage-apps/assign-user-or-group-access-portal.md) 사용합니다.

### <a name="define-user-and-group-attribute-mapping"></a>사용자 및 그룹 특성 매핑 정의

자동 사용자 프로비저닝을 구현하려면 응용 프로그램에 필요한 사용자 및 그룹 특성을 정의해야 합니다. Azure AD 사용자 개체와 각 SaaS 응용 프로그램의 사용자 개체 간에 미리 구성된 특성 및 [특성 매핑](../app-provisioning/configure-automatic-user-provisioning-portal.md) 집합이 있습니다. 모든 SaaS 앱에서 그룹 속성을 사용하도록 설정하는 것은 아닙니다.

Azure AD는 직접 특성-속성 매핑을 지원하여 상수 값을 제공하거나 [특성 매핑에 대한 식을 작성합니다.](../app-provisioning/functions-for-customizing-application-data.md) 이러한 유연성을 통해 대상 시스템의 특성에 채워질 내용을 세밀하게 제어할 수 있습니다. [Microsoft 그래프 API](../app-provisioning/export-import-provisioning-configuration.md) 및 그래프 탐색기를 사용하여 사용자 프로비저닝 특성 매핑 및 스키마를 JSON 파일로 내보내고 Azure AD로 다시 가져올 수 있습니다.

자세한 내용은 [Azure Active Directory의 SaaS 응용 프로그램에 대한 사용자 프로비저닝 특성 매핑 사용자 지정을](../app-provisioning/customize-application-attributes.md)참조하십시오.

### <a name="special-considerations-for-user-provisioning"></a>사용자 프로비저닝을 위한 특별 고려 사항

배포 후 문제를 줄이려면 다음을 고려하십시오.

* 원본 응용 프로그램과 대상 응용 프로그램 간에 사용자/그룹 개체를 매핑하는 데 사용되는 특성이 복원력이 있는지 확인합니다. 특성이 변경되는 경우(예: 사용자가 회사의 다른 부분으로 이동)하는 경우 사용자/그룹이 잘못 프로비전되어서는 안 됩니다.

* 응용 프로그램에는 사용자 프로비저닝이 올바르게 작동하려면 충족해야 하는 특정 제한 및/또는 요구 사항이 있을 수 있습니다. 예를 들어 Slack은 특정 특성에 대한 값을 트렁킨다. 각 응용 프로그램에 특정한 [자동 사용자 프로비저닝 자습서를](../saas-apps/tutorial-list.md) 참조하십시오.

* 원본 시스템과 대상 시스템 간의 스키마 일관성을 확인합니다. 일반적인 문제로는 UPN 또는 메일과 같은 특성이 일치하지 않습니다. 예를 들어 Azure AD의 UPN은 앱에서 *john_smith@contoso.com* *jsmith@contoso.com*설정됩니다. 자세한 내용은 사용자 [및 그룹 스키마 참조를](../app-provisioning/use-scim-to-provision-users-and-groups.md)참조하십시오.

## <a name="plan-testing-and-security"></a>테스트 및 보안 계획

배포의 각 단계에서 결과가 예상대로 테스트되고 프로비저닝 주기를 감사해야 합니다.

### <a name="plan-testing"></a>계획 테스트

응용 프로그램에 대한 자동 사용자 프로비저닝을 구성한 후에는 테스트 사례를 실행하여 이 솔루션이 조직의 요구 사항을 충족하는지 확인합니다.

| 시나리오| 예상 결과 |
| - | - |
| 대상 시스템에 할당된 그룹에 사용자가 추가됩니다. | 사용자 개체는 대상 시스템에 프로비전됩니다. <br>사용자는 대상 시스템에 로그인하고 원하는 작업을 수행할 수 있습니다. |
| 대상 시스템에 할당된 그룹에서 사용자가 제거됩니다. | 사용자 개체가 대상 시스템에서 프로비저닝 해제됩니다.<br>사용자가 대상 시스템에 로그인할 수 없습니다. |
| 사용자 정보는 모든 방법으로 Azure AD에서 업데이트됩니다. | 업데이트된 사용자 속성은 증분 주기 후에 대상 시스템에 반영됩니다. |
| 사용자가 범위를 벗어났습니다. | 사용자 개체가 비활성화또는 삭제되었습니다. <br>참고: 이 동작은 [Workday 프로비저닝에](skip-out-of-scope-deletions.md)대해 재정의됩니다. |

### <a name="plan-security"></a>보안 계획

배포의 일부로 보안 검토가 필요한 것이 일반적입니다. 보안 검토가 필요한 경우 서비스로서의 ID에 대한 개요를 제공하는 많은 Azure AD [백서를](https://www.microsoft.com/download/details.aspx?id=36391) 참조하십시오.

### <a name="plan-rollback"></a>롤백 계획

프로덕션 환경에서 자동 사용자 프로비저닝 구현이 원하는 대로 작동하지 않는 경우 아래의 롤백 단계를 통해 이전에 알려진 양호상태로 되돌릴 수 있습니다.

1. [프로비저닝 요약 보고서](../app-provisioning/check-status-user-account-provisioning.md) 및 [프로비저닝 로그를](../app-provisioning/check-status-user-account-provisioning.md#provisioning-logs-preview) 검토하여 영향을 받는 사용자 및/또는 그룹에서 발생한 잘못된 작업을 확인합니다.

1. 프로비저닝 감사 로그를 사용하여 영향을 받는 사용자 및/또는 그룹의 마지막으로 알려진 상태를 확인합니다. 또한 원본 시스템(Azure AD 또는 AD)을 검토합니다.

1. 응용 프로그램 소유자와 협력하여 마지막으로 알려진 정상 상태 값을 사용하여 응용 프로그램에서 직접 영향을 받는 사용자 및/또는 그룹을 업데이트합니다.

## <a name="deploy-automatic-user-provisioning-service"></a>자동 사용자 프로비저닝 서비스 배포

솔루션 요구 사항에 맞는 단계를 선택합니다.

### <a name="prepare-for-the-initial-cycle"></a>초기 주기 준비

Azure AD 프로비저닝 서비스가 처음으로 실행되면 원본 시스템 및 대상 시스템에 대한 초기 주기가 각 대상 시스템에 대한 모든 사용자 개체의 스냅숏을 만듭니다.

응용 프로그램에 대한 자동 프로비저닝을 사용하도록 설정하는 경우 초기 주기는 20분에서 몇 시간까지 걸릴 수 있습니다. 기간은 Azure AD 디렉터리 크기와 프로비저닝 범위의 사용자 수에 따라 다릅니다. [프로비저닝 성능을 개선하는 방법을](../app-provisioning/application-provisioning-when-will-provisioning-finish.md)참조하십시오.

프로비저닝 서비스는 초기 주기 이후 두 시스템의 상태를 저장하여 후속 증분 주기의 성능을 향상시입니다.

### <a name="configure-automatic-user-provisioning"></a>자동 사용자 프로비저닝 구성

Azure [포털을](https://portal.azure.com/) 사용하여 자동 사용자 계정 프로비전 및 이를 지원하는 응용 프로그램에 대한 프로비저닝 해제를 관리합니다. 응용 프로그램에 [대한 자동 프로비저닝을 설정하는 방법의 단계를 따르십시오.](../app-provisioning/user-provisioning.md)

[Microsoft Graph API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)를 사용하여 Azure AD 사용자 프로비저닝 서비스를 구성 및 관리할 수도 있습니다.

## <a name="manage-automatic-user-provisioning"></a>자동 사용자 프로비저닝 관리

이제 배포되었으므로 솔루션을 관리해야 합니다.

### <a name="monitor-user-provisioning-operation-health"></a>사용자 프로비저닝 작업 상태 모니터링

[성공적인 초기 주기](../app-provisioning/user-provisioning.md)후 Azure AD 프로비저닝 서비스는 다음 이벤트 중 하나가 발생할 때까지 각 응용 프로그램에 특정한 간격으로 증분 업데이트를 무기한 실행합니다.

* 서비스가 수동으로 중지되고 [Azure 포털을](https://portal.azure.com/)사용하거나 적절한 [Microsoft Graph API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview) 명령을 사용하여 새 초기 주기가 트리거됩니다.

* 특성 매핑 또는 범위 지정 필터의 변경으로 새 초기 주기가 트리거됩니다.

* 프로비저닝 프로세스는 높은 오류율로 인해 격리되며 자동으로 비활성화될 때 4주 이상 격리 상태로 유지됩니다.

이러한 이벤트 및 프로비저닝 서비스에서 수행하는 기타 모든 활동을 검토하려면 Azure AD [프로비저닝 로그를](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)참조하십시오.

프로비저닝 주기소요 시간을 이해하고 프로비저닝 작업의 진행 률을 모니터링하려면 [사용자 프로비저닝 상태를 확인할](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)수 있습니다.

### <a name="gain-insights-from-reports"></a>보고서에서 인사이트 확보

Azure AD는 감사 로그 및 보고서를 통해 조직의 사용자 프로비저닝 사용 및 운영 상태와에 대한 [추가 통찰력을](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) 제공할 수 있습니다.

관리자는 프로비저닝 요약 보고서를 확인하여 프로비저닝 작업의 운영 상태를 모니터링해야 합니다. 프로비저닝 서비스에서 수행하는 모든 활동은 Azure AD 감사 로그에 기록됩니다. [자습서: 자동 사용자 계정 프로비저닝에 대한 보고](../app-provisioning/check-status-user-account-provisioning.md)를 참조하십시오.

조직의 요구 사항을 충족하는 종지에서 이러한 보고서의 소유권을 가정하고 사용하는 것이 좋습니다. Azure AD는 30일 동안 대부분의 감사 데이터를 유지합니다.

### <a name="troubleshoot"></a>문제 해결

프로비저닝 중에 발생할 수 있는 문제를 해결하려면 다음 링크를 참조하십시오.

* [Azure AD 갤러리 애플리케이션에 대해 사용자 프로비전 구성 문제](../app-provisioning/application-provisioning-config-problem.md)

* [온-프레미스 Active Directory에서 응용 프로그램에 프로비전하기 위해 Azure AD에 특성 동기화](../app-provisioning/user-provisioning-sync-attributes-for-mapping.md)

* [Azure AD 갤러리 애플리케이션에 대한 사용자 프로비저닝이 오래 걸림](../app-provisioning/application-provisioning-when-will-provisioning-finish.md)

* [사용자가 Azure Active Directory 갤러리 애플리케이션에 프로비전을 구성하는 동안 관리자 자격 증명을 저장하는 문제](../app-provisioning/application-provisioning-config-problem-storage-limit.md)

* [사용자가 Azure AD 갤러리 애플리케이션에 프로비전되지 않음](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md)

* [Azure AD 갤러리 애플리케이션에 잘못된 사용자 집합이 프로비전됨](../app-provisioning/application-provisioning-config-problem-wrong-users-provisioned.md)

### <a name="helpful-documentation"></a>유용한 문서

* [특성 매핑에 대한 표현식 작성](../app-provisioning/functions-for-customizing-application-data.md)

* [Azure AD 동기화 API 개요](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)

* [범위를 벗어난 사용자 계정의 삭제 건너뛰기](skip-out-of-scope-deletions.md)

* [Azure AD 연결 프로비전 에이전트: 버전 릴리스 기록](provisioning-agent-release-version-history.md)

#### <a name="resources"></a>리소스

* [제품 피드백 제공](https://feedback.azure.com/forums/169401-azure-active-directory)

* [Azure AD의 최신 정보를 최신 상태로 유지](https://azure.microsoft.com/updates/?product=active-directory)

* [스택 오버플로 Azure AD 포럼](https://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="next-steps"></a>다음 단계
* [자동 사용자 프로비저닝 구성](../app-provisioning/configure-automatic-user-provisioning-portal.md)

* [Microsoft 그래프 API를 사용하여 프로비저닝 구성 내보내기 또는 가져오기](../app-provisioning/export-import-provisioning-configuration.md)

* [Azure Active 디렉터리에서 특성 매핑에 대한 표현식 작성](../app-provisioning/functions-for-customizing-application-data.md)
