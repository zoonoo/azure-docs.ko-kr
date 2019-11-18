---
title: Azure AD에서 SaaS 앱 사용자를 자동으로 프로비저닝 | Microsoft Docs
description: Azure AD를 사용하여 여러 타사 SaaS 애플리케이션에서 사용자 계정을 자동으로 프로비저닝, 프로비저닝 해제, 지속적으로 업데이트하는 방법을 소개합니다.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/15/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1f661aa67f04de23c7b4871e78d3628c639e7567
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74144517"
---
# <a name="automate-user-provisioning-and-deprovisioning-to-applications-with-azure-active-directory"></a>Azure Active Directory 사용 하 여 응용 프로그램에 대 한 사용자 프로 비전 및 프로 비전 해제 자동화

Azure AD (Azure Active Directory)에서 용어 **앱 프로 비전은** 사용자가 액세스 해야 하는 클라우드 ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)) 응용 프로그램에서 사용자 id 및 역할을 자동으로 만드는 것을 의미 합니다. 사용자 id를 만들 뿐 아니라 자동 프로 비전에는 상태 또는 역할이 변경 되는 사용자 id의 유지 관리 및 제거도 포함 됩니다. 일반적인 시나리오에는 [Dropbox](https://docs.microsoft.com/azure/active-directory/saas-apps/dropboxforbusiness-provisioning-tutorial), [Salesforce](https://docs.microsoft.com/azure/active-directory/saas-apps/salesforce-provisioning-tutorial), [ServiceNow](https://docs.microsoft.com/azure/active-directory/saas-apps/servicenow-provisioning-tutorial)등과 같은 응용 프로그램에 Azure AD 사용자를 프로 비전 하는 작업이 포함 됩니다.

![프로 비전 개요 다이어그램](media/user-provisioning/provisioning-overview.png)

이 기능을 통해 다음을 수행할 수 있습니다.

- **프로 비전 자동화**: 팀 또는 조직에 참여할 때 새 사용자를 위해 올바른 시스템에 새 계정을 자동으로 만듭니다.
- **프로 비전 해제 자동화:** 사용자가 팀 이나 조직을 떠날 때 적절 한 시스템에서 계정을 자동으로 비활성화 합니다.
- **시스템 간 데이터 동기화:** 디렉터리 또는 인적 자원 시스템의 변경 내용에 따라 앱 및 시스템의 id를 최신 상태로 유지 해야 합니다.
- **프로 비전 그룹:** 이를 지 원하는 응용 프로그램에 그룹을 프로 비전 합니다.
- **액세스 제어:** 응용 프로그램에 프로 비전 된 사용자를 모니터링 하 고 감사 합니다.
- **갈색 필드 시나리오에서 원활 하 게 배포:** 시스템 간에 기존 id를 일치 시키고 사용자가 대상 시스템에 이미 있는 경우에도 쉽게 통합할 수 있습니다.
- **다양 한 사용자 지정 사용:** 원본 시스템에서 대상 시스템으로 이동 해야 하는 사용자 데이터를 정의 하는 사용자 지정 가능한 특성 매핑을 활용 합니다.
- **중요 이벤트에 대 한 경고 받기:** 프로 비전 서비스는 중요 이벤트에 대 한 경고를 제공 하며, 비즈니스 요구에 맞게 사용자 지정 경고를 정의할 수 있는 Log Analytics 통합을 허용 합니다.

## <a name="benefits-of-automatic-provisioning"></a>자동 프로 비전의 이점

최신 조직에서 사용 되는 응용 프로그램 수가 계속 증가 함에 따라 IT 관리자는 대규모로 액세스 관리를 수행 합니다. SAML (Security 어설션이 Markup Language) 또는 OIDC (Open ID Connect)와 같은 표준을 사용 하면 관리자가 SSO (Single Sign-On)를 신속 하 게 설정할 수 있지만 액세스를 위해서는 사용자를 앱에 프로 비전 해야 합니다. 대부분의 관리자는 프로 비전을 통해 모든 사용자 계정을 수동으로 만들거나 매주 CSV 파일을 업로드 하는 것을 의미 하지만 이러한 프로세스는 시간이 많이 걸리고, 비용이 많이 들고, 오류가 발생 하기 쉽습니다. 프로 비전을 자동화 하기 위해 SAML JIT (just-in-time)와 같은 솔루션을 채택 했지만, 조직에서 탈퇴 하거나 역할 변경에 따라 특정 앱에 더 이상 액세스할 필요가 없는 경우에는 사용자를 프로 비전 해제 하는 솔루션이 필요 합니다.

자동 프로비저닝을 사용 하는 일반적인 몇 가지 동기는 다음과 같습니다.

- 프로 비전 프로세스의 효율성과 정확성을 극대화 합니다.
- 사용자 지정 개발 프로 비전 솔루션 및 스크립트의 호스팅 및 유지 관리와 관련 된 비용을 절감 합니다.
- 조직에서 나갈 때 키 SaaS 앱에서 사용자 id를 즉시 제거 하 여 조직의 보안을 유지 합니다.
- 특정 SaaS 응용 프로그램 또는 시스템으로 많은 사용자를 쉽게 가져올 수 있습니다.
- 단일 정책 집합을 포함 하 여 프로 비전 된 사용자 및 앱에 로그인 할 수 있는 사람을 결정 합니다.

Azure AD 사용자 프로 비전은 이러한 문제를 해결 하는 데 도움이 될 수 있습니다. 고객이 Azure AD 사용자 프로 비전을 사용 하는 방법에 대해 자세히 알아보려면 [Asos 사례 연구](https://aka.ms/asoscasestudy)를 읽을 수 있습니다. 아래 비디오는 Azure AD에서 사용자 프로 비전의 개요를 제공 합니다.

> [!VIDEO https://www.youtube.com/embed/_ZjARPpI6NI]

## <a name="what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning"></a>Azure AD 자동 사용자 프로비전에서 사용할 수 있는 애플리케이션과 시스템은 무엇입니까?

Azure AD는 널리 사용 되는 많은 SaaS 앱 및 인적 자원 시스템에 대 한 사전 통합 된 지원과 [Scim 2.0 표준](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010)의 특정 부분을 구현 하는 앱에 대 한 일반 지원을 제공 합니다.

* **사전 통합 된 응용 프로그램 (갤러리 SaaS 앱)** . Azure AD [가 사용자 프로 비전을 위한 응용 프로그램 자습서 목록](../saas-apps/tutorial-list.md)에서 사전 통합 된 프로 비전 커넥터를 지 원하는 모든 응용 프로그램을 찾을 수 있습니다. 갤러리에 나열 된 사전 통합 된 응용 프로그램은 일반적으로 프로 비전을 위해 SCIM 2.0 기반 사용자 관리 Api를 사용 합니다. 

   ![Salesforce 로고](media/user-provisioning/gallery-app-logos.png)

   프로 비전을 위해 새 응용 프로그램을 요청 하려는 경우 [응용 프로그램을 앱 갤러리와 통합 하도록 요청할](https://docs.microsoft.com/azure/active-directory/develop/howto-app-gallery-listing)수 있습니다. 사용자 프로 비전 요청의 경우 응용 프로그램에 SCIM 규격 끝점이 있어야 합니다. 앱을 플랫폼에 빠르게 등록할 수 있도록 응용 프로그램 공급 업체가 SCIM 표준을 따르도록 요청 하세요.

* **SCIM 2.0을 지 원하는 응용 프로그램**입니다. 일반적으로 SCIM 2.0 기반 사용자 관리 Api를 구현 하는 응용 프로그램을 연결 하는 방법에 대 한 자세한 내용은 [SCIM을 사용 하 여 Azure Active Directory에서 응용 프로그램으로 사용자 및 그룹 자동 프로 비전](use-scim-to-provision-users-and-groups.md)을 참조 하세요.

## <a name="what-is-scim"></a>SCIM 이란?

프로 비전 및 프로 비전 해제를 자동화 하기 위해 앱은 독점적인 사용자 및 그룹 Api를 노출 합니다. 그러나 둘 이상의 앱에서 사용자를 관리 하려는 사용자는 모든 앱이 사용자 만들기 또는 업데이트, 그룹에 사용자 추가 또는 프로 비전 해제와 같은 간단한 작업을 수행 하려고 한다는 것을 알 수 있습니다. 그러나 이러한 모든 간단한 작업은 다른 끝점 경로, 사용자 정보를 지정 하는 다양 한 메서드 및 정보의 각 요소를 나타내는 다른 스키마를 사용 하 여 약간 약간 다르게 구현 됩니다.

이러한 문제를 해결 하기 위해 SCIM 사양은 사용자가 앱으로 전환 하 고 앱을 확장 하는 데 도움이 되는 공용 사용자 스키마를 제공 합니다. SCIM은 프로 비전을 위한 사실상 표준으로 사용 되며, SAML 또는 Openid connect Connect와 같은 페더레이션 표준과 함께 사용 될 경우 관리자에 게 액세스 관리를 위한 종단 간 표준 기반 솔루션을 제공 합니다.

SCIM을 사용 하 여 응용 프로그램에 사용자 및 그룹의 프로 비전 및 프로 비전 해제를 자동화 하는 방법에 대 한 자세한 지침은 [Azure Active Directory로 scim 사용자 프로 비전](use-scim-to-provision-users-and-groups.md)을 참조 하세요.

## <a name="how-does-automatic-provisioning-work"></a>자동 프로비전은 어떻게 작동합니까?

**AZURE AD 프로 비전 서비스** 는 각 응용 프로그램 공급 업체에서 제공 하는 사용자 관리 API 끝점에 연결 하 여 SaaS 앱 및 기타 시스템에 사용자를 프로 비전 합니다. 이러한 사용자 관리 API 엔드포인트를 사용하면 Azure AD에서 프로그래밍 방식으로 사용자를 만들고, 업데이트하고, 제거할 수 있습니다. 선택한 응용 프로그램의 경우 프로 비전 서비스는 그룹 및 역할과 같은 추가 id 관련 개체를 만들고 업데이트 하 고 제거할 수도 있습니다.

Azure AD 프로 비전 서비스를 ![](./media/user-provisioning/provisioning0.PNG)
*그림 1: AZURE Ad 프로 비전 서비스*

![아웃 바운드 사용자 프로 비전 워크플로](./media/user-provisioning/provisioning1.PNG)
*그림 2: AZURE AD에서 인기 있는 SaaS 응용 프로그램에 대 한 "아웃 바운드" 사용자 프로 비전 워크플로*

인바운드 사용자 프로 비전 워크플로](./media/user-provisioning/provisioning2.PNG)
*그림 3: 인기 있는 HCM (인간 자본 관리) 응용 프로그램에서 Azure Active Directory 및 Windows Server로의 "인바운드" 사용자 프로 비전 워크플로를 ![Active Directory*

## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>애플리케이션에 자동 프로비전을 설정하려면 어떻게 합니까?

갤러리에 나열 된 사전 통합 된 응용 프로그램의 경우 자동 프로 비전을 설정 하는 데 단계별 지침을 사용할 수 있습니다. [통합 갤러리 앱에 대 한 자습서 목록](https://docs.microsoft.com/azure/active-directory/saas-apps/)을 참조 하세요. 다음 비디오는 SalesForce에 대 한 자동 사용자 프로 비전을 설정 하는 방법을 보여 줍니다.

> [!VIDEO https://www.youtube.com/embed/pKzyts6kfrw]

SCIM 2.0을 지 원하는 다른 응용 프로그램의 경우 [Azure Active Directory로 scim 사용자 프로 비전](use-scim-to-provision-users-and-groups.md)문서의 단계를 따르세요.

## <a name="what-happens-during-provisioning"></a>프로비전하는 동안 어떻게 됩니까?

Azure AD가 소스 시스템인 경우 프로비저닝 서비스는 [Azure AD Graph API의 차등 쿼리 기능](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query)을 사용하여 사용자와 그룹을 모니터링합니다. 프로 비전 서비스는 원본 시스템 및 대상 시스템에 대해 초기 주기를 실행 한 다음 주기적 증분 주기를 실행 합니다.

### <a name="initial-cycle"></a>초기 주기

프로 비전 서비스가 시작 되 면 첫 번째 동기화는 다음과 같이 실행 됩니다.

1. [특성 매핑](customize-application-attributes.md)에 정의된 모든 특성을 검색하여 소스 시스템에서 모든 사용자와 그룹을 쿼리합니다.
1. 구성된 [할당](assign-user-or-group-access-portal.md) 또는 [특성 기반 범위 지정 필터](define-conditional-rules-for-provisioning-user-accounts.md)를 사용하여 반환된 사용자 및 그룹을 필터링합니다.
1. 사용자가 할당 되거나 프로 비전 범위에 있는 경우 서비스는 지정 된 [일치 특성](customize-application-attributes.md#understanding-attribute-mapping-properties)을 사용 하 여 대상 시스템에 일치 하는 사용자를 쿼리 합니다. 예: 소스 시스템의 userPrincipal 이름이 일치하는 특성이고 대상 시스템의 userName에 매핑되는 경우 프로비저닝 서비스는 대상 시스템에서 소스 시스템의 userPrincipal 이름 값과 일치하는 userNames를 쿼리합니다.
1. 대상 시스템에서 일치 하는 사용자를 찾을 수 없는 경우 소스 시스템에서 반환 된 특성을 사용 하 여 생성 됩니다. 사용자 계정을 만든 후 프로 비전 서비스는 새 사용자에 대 한 대상 시스템의 ID를 검색 하 고 캐시 합니다 .이 ID는 해당 사용자에 대 한 모든 이후 작업을 실행 하는 데 사용 됩니다.
1. 일치 하는 사용자가 있는 경우 원본 시스템에서 제공 하는 특성을 사용 하 여 업데이트 됩니다. 사용자 계정이 일치 한 후 프로 비전 서비스는 새 사용자에 대 한 대상 시스템의 ID를 검색 하 고 캐시 합니다 .이 ID는 해당 사용자에 대 한 모든 이후 작업을 실행 하는 데 사용 됩니다.
1. 특성 매핑에 "참조" 특성이 포함 되어 있으면 서비스는 대상 시스템에서 추가 업데이트를 수행 하 여 참조 된 개체를 만들고 연결 합니다. 예를 들어 대상 시스템에 있는 한 사용자의 “Manager” 특성이 대상 시스템에서 생성된 다른 사용자에 연결되어 있을 수 있습니다.
1. 이후 증분 주기에 대 한 시작 지점을 제공 하는 초기 주기 끝에 워터 마크를 유지 합니다.

ServiceNow, G Suite 및 Box와 같은 일부 응용 프로그램은 사용자를 프로 비전 할 뿐만 아니라 그룹과 해당 구성원도 프로 비전 할 수 있습니다. 이러한 경우 [매핑에서](customize-application-attributes.md)그룹 프로 비전이 사용 하도록 설정 된 경우 프로 비전 서비스는 사용자와 그룹을 동기화 한 다음 나중에 그룹 멤버 자격을 동기화 합니다.

### <a name="incremental-cycles"></a>증분 주기

초기 주기 후 다른 모든 주기는 다음을 수행 합니다.

1. 소스 시스템에서 마지막 워터마크가 저장된 이후 업데이트된 사용자 및 그룹을 쿼리합니다.
1. 구성된 [할당](assign-user-or-group-access-portal.md) 또는 [특성 기반 범위 지정 필터](define-conditional-rules-for-provisioning-user-accounts.md)를 사용하여 반환된 사용자 및 그룹을 필터링합니다.
1. 사용자가 할당 되거나 프로 비전 범위에 있는 경우 서비스는 지정 된 [일치 특성](customize-application-attributes.md#understanding-attribute-mapping-properties)을 사용 하 여 대상 시스템에 일치 하는 사용자를 쿼리 합니다.
1. 대상 시스템에서 일치 하는 사용자를 찾을 수 없는 경우 소스 시스템에서 반환 된 특성을 사용 하 여 생성 됩니다. 사용자 계정을 만든 후 프로 비전 서비스는 새 사용자에 대 한 대상 시스템의 ID를 검색 하 고 캐시 합니다 .이 ID는 해당 사용자에 대 한 모든 이후 작업을 실행 하는 데 사용 됩니다.
1. 일치 하는 사용자가 있는 경우 원본 시스템에서 제공 하는 특성을 사용 하 여 업데이트 됩니다. 새로 할당 된 계정인 경우 프로 비전 서비스는 새 사용자에 대 한 대상 시스템의 ID를 검색 하 고 캐시 합니다 .이 ID는 해당 사용자에 대 한 모든 이후 작업을 실행 하는 데 사용 됩니다.
1. 특성 매핑에 "참조" 특성이 포함 되어 있으면 서비스는 대상 시스템에서 추가 업데이트를 수행 하 여 참조 된 개체를 만들고 연결 합니다. 예를 들어 대상 시스템에 있는 한 사용자의 “Manager” 특성이 대상 시스템에서 생성된 다른 사용자에 연결되어 있을 수 있습니다.
1. 이전에 프로비저닝 범위에 있던 사용자가 범위에서 제거되면(할당 취소되는 경우 포함), 서비스는 업데이트를 통해 대상 시스템에서 사용자를 해제합니다.
1. 이전에 프로비저닝 범위에 있던 사용자가 소스 시스템에서 해제 또는 일시 삭제되면 서비스는 업데이트를 통해 대상 시스템에서 사용자를 해제합니다.
1. 이전에 프로비저닝 범위에 있던 사용자가 소스 시스템에서 영구 삭제되면 서비스는 대상 시스템에서 사용자를 삭제합니다. Azure AD에서 사용자는 일시 삭제 된 후 30 일 후에 하드 삭제 됩니다.
1. 이후 증분 주기에 대 한 시작점을 제공 하는 증분 주기의 끝에 새 워터 마크를 유지 합니다.

> [!NOTE]
> 필요에 따라 [매핑](customize-application-attributes.md) 섹션에서 **대상 개체 작업** 확인란을 사용 하 여 **만들기**, **업데이트**또는 **삭제** 작업을 비활성화할 수 있습니다. 업데이트 중에 사용자를 해제하는 논리도 “accountEnabled”와 같은 필드의 특성 매핑을 통해 제어됩니다.

프로 비전 서비스는 다음 이벤트 중 하나가 발생할 때까지 [각 응용 프로그램과 관련 된 자습서](../saas-apps/tutorial-list.md)에 정의 된 간격에 따라 다시 백 엔드 증분 주기를 무기한 실행 합니다.

- 서비스가 Azure Portal을 사용하여 수동으로 중지되거나 적절한 Graph API 명령을 사용하여 중지됩니다. 
- Azure Portal에서 **Clear state 및 restart** 옵션을 사용 하거나 적절 한 Graph API 명령을 사용 하 여 새로운 초기 순환이 트리거됩니다. 이 작업을 수행 하면 저장 된 워터 마크가 모두 지워지며 모든 소스 개체가 다시 평가 됩니다.
- 특성 매핑 또는 범위 지정 필터의 변경으로 인해 새 초기 순환이 트리거됩니다. 이 작업을 수행 하면 저장 된 워터 마크가 모두 지워지므로 모든 원본 개체가 다시 평가 됩니다.
- 높은 오류 비율로 인해 프로 비전 프로세스가 격리 (아래 참조) 되 고 4 주 넘게 격리 상태로 유지 됩니다. 이 이벤트가 발생하면 자동으로 서비스를 사용할 수 없게 됩니다.

### <a name="errors-and-retries"></a>오류 및 다시 시도

대상 시스템에 오류가 발생 하 여 대상 시스템에서 개별 사용자를 추가, 업데이트 또는 삭제할 수 없는 경우 다음 동기화 주기에서 작업을 다시 시도 합니다. 사용자가 계속 실패하면 감소된 빈도로 다시 시도되기 시작하여, 점차 하루에 한 번만 시도되는 빈도까지 축소됩니다. 오류를 해결 하려면 관리자가 [프로 비전 로그](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) 를 확인 하 여 근본 원인을 확인 하 고 적절 한 조치를 취해야 합니다. 일반적인 오류는 다음과 같습니다.

- 대상 시스템에서 필요한 특성이 사용자의 소스 시스템에 채워져 있지 않음
- 대상 시스템에 unique 제약 조건이 있고 다른 사용자 레코드에 동일한 값이 있는 원본 시스템에 특성 값이 있는 사용자

이러한 오류는 소스 시스템에서 영향을 받는 사용자의 특성 값을 조정하거나 충돌이 발생하지 않도록 특성 매핑을 조정하여 해결할 수 있습니다.

### <a name="quarantine"></a>격리

오류 (예: 잘못 된 관리자 자격 증명)로 인해 대상 시스템에 대해 수행 되는 대부분의 호출이 일관 되 게 실패 하는 경우 프로 비전 작업은 "격리" 상태가 됩니다. 이 상태는 Azure Portal에서 전자 메일 알림이 구성 된 경우 [프로 비전 요약 보고서](check-status-user-account-provisioning.md) 와 전자 메일을 통해 표시 됩니다.

격리에서 증분 주기의 빈도는 하루에 한 번만 점차적으로 줄어듭니다.

모든 잘못 된 오류를 수정 하 고 다음 동기화 주기를 시작 하면 프로 비전 작업이 격리에서 제거 됩니다. 프로비저닝 작업이 4주 넘게 격리 상태로 유지되면 프로비저닝 작업을 사용할 수 없게 됩니다. [여기에서 격리 상태에](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)대해 자세히 알아보세요.

## <a name="how-long-will-it-take-to-provision-users"></a>사용자를 프로비전하는 데 걸리는 시간은 어느 정도인가요?

성능은 프로 비전 작업에서 초기 프로 비전 주기 또는 증분 주기를 실행 하 고 있는지 여부에 따라 달라 집니다. 프로 비전에 소요 되는 시간 및 프로 비전 서비스의 상태를 모니터링 하는 방법에 대 한 자세한 내용은 [사용자 프로 비전 상태 확인](application-provisioning-when-will-provisioning-finish-specific-user.md)을 참조 하세요.

## <a name="how-can-i-tell-if-users-are-being-provisioned-properly"></a>사용자가 적절히 프로비전되는지 어떻게 확인할 수 있나요?

사용자 프로 비전 서비스에 의해 실행 되는 모든 작업은 Azure AD [프로 비전 로그 (미리 보기)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)에 기록 됩니다. 여기에는 원본 및 대상 시스템에 대 한 모든 읽기 및 쓰기 작업과 각 작업 중에 읽거나 쓴 사용자 데이터가 포함 됩니다.

Azure Portal에서 프로 비전 로그를 읽는 방법에 대 한 자세한 내용은 [프로 비전 보고 가이드](check-status-user-account-provisioning.md)를 참조 하세요.

## <a name="how-do-i-troubleshoot-issues-with-user-provisioning"></a>사용자 프로비저닝 문제를 어떻게 해결하나요?

자동 사용자 프로비전 문제를 해결하는 방법에 대한 시나리오 기반 지침은 [애플리케이션에 사용자를 구성 및 프로비전하는 문제](application-provisioning-config-problem.md)를 참조하세요.

## <a name="what-are-the-best-practices-for-rolling-out-automatic-user-provisioning"></a>자동 사용자 프로비저닝을 롤아웃하기 위한 모범 사례는 무엇인가요?

> [!VIDEO https://www.youtube.com/embed/MAy8s5WSe3A]

애플리케이션에 대한 아웃바운드 사용자 프로비저닝에 대한 단계별 배포 계획 예제는 [사용자 프로비저닝에 대한 ID 배포 가이드](https://aka.ms/deploymentplans/userprovisioning)를 참조하세요.

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="does-automatic-user-provisioning-to-saas-apps-work-with-b2b-users-in-azure-ad"></a>SaaS 앱에 대한 자동 사용자 프로비저닝이 Azure AD의 B2B 사용자에게 작동하나요?

예, Azure AD 사용자 프로 비전 서비스를 사용 하 여 Azure AD의 B2B (또는 게스트) 사용자를 SaaS 응용 프로그램에 프로 비전 할 수 있습니다.

그러나 B2B 사용자가 Azure AD를 사용 하 여 SaaS 응용 프로그램에 로그인 하는 경우 SaaS 응용 프로그램에는 특정 방식으로 구성 된 SAML 기반 Single Sign-On 기능이 있어야 합니다. B2B 사용자의 로그인을 지원하도록 SaaS 애플리케이션을 구성하는 방법에 대한 자세한 내용은 [B2B 협업을 위한 SaaS 앱 구성]( https://docs.microsoft.com/azure/active-directory/b2b/configure-saas-apps)을 참조하세요.

### <a name="does-automatic-user-provisioning-to-saas-apps-work-with-dynamic-groups-in-azure-ad"></a>SaaS 앱에 대한 자동 사용자 프로비저닝이 Azure AD의 동적 그룹에 작동하나요?

예. "할당 된 사용자 및 그룹만 동기화"로 구성 된 경우 Azure AD 사용자 프로 비전 서비스는 사용자가 [동적 그룹](../users-groups-roles/groups-create-rule.md)의 구성원 인지 여부에 따라 SaaS 응용 프로그램에서 사용자를 프로 비전 하거나 프로 비전 해제할 수 있습니다. 또한 동적 그룹은 “모든 사용자 및 그룹 동기화” 옵션을 사용할 때도 작동합니다.

그러나 동적 그룹의 사용은 Azure AD에서 SaaS 애플리케이션으로의 엔드투엔드 사용자 프로비저닝에 대한 전체 성능에 영향을 줄 수 있습니다. 동적 그룹을 사용 하는 경우 다음과 같은 주의 사항 및 권장 사항을 염두에 두십시오.

- 동적 그룹의 사용자가 SaaS 애플리케이션에서 프로비전 또는 프로비전 해제되는 속도는 동적 그룹이 멤버 자격 변경을 평가하는 속도에 따라 좌우됩니다. 동적 그룹의 처리 상태를 확인하는 방법에 대한 내용은 [멤버 자격 규칙에 대한 처리 상태 확인](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule)을 참조하세요.

- 동적 그룹을 사용 하는 경우 멤버 자격이 손실 되어 프로 비전 해제 이벤트가 발생 하므로 규칙은 사용자 프로 비전 및 프로 비전 해제를 염두에 두면 신중 하 게 고려해 야 합니다.

### <a name="does-automatic-user-provisioning-to-saas-apps-work-with-nested-groups-in-azure-ad"></a>SaaS 앱에 대한 자동 사용자 프로비저닝이 Azure AD의 중첩 그룹에 작동하나요?

아니요. "할당 된 사용자 및 그룹만 동기화"로 구성 된 경우 Azure AD 사용자 프로 비전 서비스는 중첩 된 그룹에 있는 사용자를 읽거나 프로 비전 할 수 없습니다. 명시적으로 할당 된 그룹의 직접 구성원 인 사용자를 읽고 프로 비전 할 수 있습니다.

이것은 Single Sign-On에도 영향을 미치는 “애플리케이션에 대한 그룹 기반 할당”의 제한 사항으로, [그룹을 사용하여 SaaS 애플리케이션에 대한 액세스 관리](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-saasapps )에 설명되어 있습니다.

이 문제를 해결 하려면 프로 비전 해야 하는 사용자가 포함 된 그룹을 명시적으로 할당 하거나 그 밖 [의 범위](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)를 지정 해야 합니다.

### <a name="is-provisioning-between-azure-ad-and-a-target-application-using-an-encrypted-channel"></a>암호화 된 채널을 사용 하 여 Azure AD와 대상 응용 프로그램 간에 프로 비전 합니까?

예. 서버 대상에 대해 HTTPS SSL 암호화를 사용 합니다.

## <a name="related-articles"></a>관련된 문서

- [SaaS App을 통합하는 방법에 대한 자습서 목록](../saas-apps/tutorial-list.md)
- [사용자 프로 비전을 위한 특성 매핑 사용자 지정](customize-application-attributes.md)
- [특성 매핑에 대 한 식 작성](functions-for-customizing-application-data.md)
- [사용자 프로 비전을 위한 범위 지정 필터](define-conditional-rules-for-provisioning-user-accounts.md)
- [SCIM을 사용 하 여 Azure AD에서 응용 프로그램으로 사용자 및 그룹의 자동 프로 비전 사용](use-scim-to-provision-users-and-groups.md)
- [Azure AD 동기화 API 개요](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
