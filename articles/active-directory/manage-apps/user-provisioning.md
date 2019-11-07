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
ms.date: 06/12/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: ef2ce1ce7a754868a1adc2e78b4c0a83fc84f071
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73641444"
---
# <a name="automate-user-provisioning-and-deprovisioning-to-saas-applications-with-azure-active-directory"></a>Azure Active Directory를 사용하여 SaaS 애플리케이션의 사용자를 자동으로 프로비저닝 및 프로비저닝 해제

Azure Active Directory (Azure AD)를 사용 하면 [Dropbox](https://docs.microsoft.com/azure/active-directory/saas-apps/dropboxforbusiness-provisioning-tutorial), [Salesforce](https://docs.microsoft.com/azure/active-directory/saas-apps/salesforce-provisioning-tutorial), [ServiceNow](https://docs.microsoft.com/azure/active-directory/saas-apps/servicenow-provisioning-tutorial)등과 같은 클라우드 SaaS 응용 프로그램에서 사용자 id의 생성, 유지 관리 및 제거를 자동화할 수 있습니다. 이를 SaaS 앱에 대 한 자동화 된 사용자 프로비저닝 이라고 합니다.

> [!VIDEO https://www.youtube.com/embed/_ZjARPpI6NI]

이 기능을 통해 다음을 수행할 수 있습니다.

- 새로운 사람이 팀 또는 조직에 가입할 때 적절한 시스템에서 새 계정을 자동으로 만듭니다.
- 사용자가 팀 또는 조직을 떠날 때 적절한 시스템에서 계정을 자동으로 비활성화합니다.
- 디렉터리 또는 인사 관리 시스템의 변경 내용에 따라 앱 및 시스템의 ID가 최신 상태로 유지되도록 합니다.
- 그룹과 같이 사용자가 아닌 개체를 지원하는 애플리케이션에 해당 개체를 프로비전합니다.

자동화 된 사용자 프로 비전에는 다음 기능도 포함 됩니다.

- 원본 시스템과 대상 시스템 간에 기존 id를 일치 시킬 수 있습니다.
- 소스 시스템에서 대상 시스템으로 이동되어야 하는 사용자 데이터를 정의하는 사용자 지정 가능한 특성 매핑입니다.
- 프로비전닝 오류를 전자 메일로 받을 수 있는 선택적 기능.
- 모니터링 및 문제 해결에 도움이 되는 보고 및 활동 로그.

## <a name="why-use-automated-provisioning"></a>자동 프로비전을 사용하는 이유는 무엇입니까?

이 기능을 사용하게 되는 일반적인 동기는 다음과 같습니다.

- 수동 프로비저닝 절차에서 빚어지는 비용, 비효율성, 사람의 실수를 방지합니다.
- 사용자 지정 개발된 프로비저닝 솔루션과 스크립트를 호스트하고 유지하는 데 관련된 비용을 방지합니다.
- 조직에서 나갈 때 키 SaaS 앱에서 사용자 id를 즉시 제거 하 여 조직의 보안을 유지 합니다.
- 특정 SaaS 응용 프로그램 또는 시스템으로 많은 사용자를 쉽게 가져올 수 있습니다.
- 단일 정책 집합을 포함 하 여 프로 비전 된 사용자 및 앱에 로그인 할 수 있는 사람을 결정 합니다.

## <a name="how-does-automatic-provisioning-work"></a>자동 프로비전은 어떻게 작동합니까?

**AZURE AD 프로 비전 서비스** 는 각 응용 프로그램 공급 업체에서 제공 하는 사용자 관리 API 끝점에 연결 하 여 SaaS 앱 및 기타 시스템에 사용자를 프로 비전 합니다. 이러한 사용자 관리 API 엔드포인트를 사용하면 Azure AD에서 프로그래밍 방식으로 사용자를 만들고, 업데이트하고, 제거할 수 있습니다. 선택한 응용 프로그램의 경우 프로 비전 서비스는 그룹 및 역할과 같은 추가 id 관련 개체를 만들고 업데이트 하 고 제거할 수도 있습니다.

Azure AD 프로 비전 서비스를 ![](./media/user-provisioning/provisioning0.PNG)
*그림 1: AZURE Ad 프로 비전 서비스*

![아웃 바운드 사용자 프로 비전 워크플로](./media/user-provisioning/provisioning1.PNG)
*그림 2: AZURE AD에서 인기 있는 SaaS 응용 프로그램에 대 한 "아웃 바운드" 사용자 프로 비전 워크플로*

인바운드 사용자 프로 비전 워크플로](./media/user-provisioning/provisioning2.PNG)
*그림 3: 인기 있는 HCM (인간 자본 관리) 응용 프로그램에서 Azure Active Directory 및 Windows Server로의 "인바운드" 사용자 프로 비전 워크플로를 ![Active Directory*

## <a name="what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning"></a>Azure AD 자동 사용자 프로비전에서 사용할 수 있는 애플리케이션과 시스템은 무엇입니까?

Azure AD는 널리 사용 되는 많은 SaaS 앱 및 인적 자원 시스템에 대 한 사전 통합 된 지원과 [Scim 2.0 표준](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010)의 특정 부분을 구현 하는 앱에 대 한 일반 지원을 제공 합니다.

### <a name="pre-integrated-applications"></a>사전 통합된 애플리케이션

Azure AD가 미리 통합된 프로비저닝 커넥터를 지원하는 모든 애플리케이션 목록을 보려면 [사용자 프로비저닝에 대한 애플리케이션 자습서 목록](../saas-apps/tutorial-list.md)을 참조하세요.

Azure AD 엔지니어링 팀에 문의하여 추가 애플리케이션에 대한 프로비전 지원을 요청하려면 [Azure Active Directory 피드백 포럼](https://feedback.azure.com/forums/374982-azure-active-directory-application-requests/filters/new?category_id=172035)을 통해 메시지를 제출하세요.

> [!NOTE]
> 애플리케이션에서 자동화된 사용자 프로비저닝을 지원하려면, 먼저 외부 프로그램에서 사용자 만들기, 유지 관리 및 제거를 자동화하는 데 필요한 사용자 관리 API를 제공해야 합니다. 따라서 모든 SaaS 앱이 이 기능과 호환되지는 않습니다. 사용자 관리 Api를 지 원하는 앱의 경우 Azure AD 엔지니어링 팀은 이러한 앱에 대 한 프로 비전 커넥터를 빌드할 수 있으며이 작업은 현재 및 잠재 고객의 요구에 따라 우선 순위가 지정 됩니다.

### <a name="connecting-applications-that-support-scim-20"></a>SCIM 2.0을 지원하는 애플리케이션 연결

SCIM 2.0 기반 사용자 관리 API를 구현하는 애플리케이션에 일반적으로 연결하는 방법에 대한 자세한 내용은 [SCIM을 사용하여 사용자 및 그룹을 Azure Active Directory에서 애플리케이션으로 자동으로 프로비전](use-scim-to-provision-users-and-groups.md)을 참조하세요.

## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>애플리케이션에 자동 프로비전을 설정하려면 어떻게 합니까?

> [!VIDEO https://www.youtube.com/embed/pKzyts6kfrw]

Azure Active Directory 포털을 사용 하 여 선택한 응용 프로그램에 대 한 Azure AD 프로 비전 서비스를 구성 합니다.

1. **[Azure Active Directory 포털](https://aad.portal.azure.com)** 을 엽니다.
1. 왼쪽 창에서 **엔터프라이즈 응용 프로그램** 을 선택 합니다. 구성 된 모든 앱의 목록이 표시 됩니다.
1. **+ 새 응용** 프로그램을 선택 하 여 응용 프로그램을 추가 합니다. 
1. 세부 정보를 입력 하 고 **추가**를 선택 합니다. 새 앱이 엔터프라이즈 응용 프로그램 목록에 추가 되 고 응용 프로그램 관리 화면으로 열립니다.
1. **프로 비전** 을 선택 하 여 앱에 대 한 사용자 계정 프로 비전 설정을 관리 합니다.

   ![프로 비전 설정 화면을 표시 합니다.](./media/user-provisioning/provisioning_settings0.PNG)

1. **프로 비전 모드** 에 대해 자동 옵션을 선택 하 여 관리자 자격 증명, 매핑, 시작 및 중지 및 동기화에 대 한 설정을 지정 합니다.

   - **관리자 자격 증명** 을 확장 하 여 Azure AD에 응용 프로그램의 사용자 관리 API에 연결 하는 데 필요한 자격 증명을 입력 합니다. 또한이 섹션에서는 자격 증명이 실패 하거나 프로 비전 작업이 [격리](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)되는 경우 전자 메일 알림을 사용 하도록 설정할 수 있습니다.
   - **매핑** 을 확장 하 여 사용자 계정이 프로 비전 되거나 업데이트 될 때 Azure AD와 대상 응용 프로그램 간에 흐르는 사용자 특성을 보고 편집 합니다. 대상 응용 프로그램에서 지 원하는 경우이 섹션에서 필요에 따라 그룹 및 사용자 계정 프로 비전을 구성할 수 있습니다. 테이블에서 매핑을 선택 하 여 사용자 특성을 확인 하 고 사용자 지정할 수 있는 오른쪽의 매핑 편집기를 엽니다.

     **범위 지정 필터** 는 프로 비전 서비스에 원본 시스템의 사용자 및 그룹을 프로 비전 하거나 대상 시스템에 프로 비전 해제를 알려 줍니다. **특성 매핑** 창에서 **원본 개체 범위** 를 선택 하 여 특정 특성 값을 필터링 합니다. 예를 들어 "Department" 특성이 "Sales"인 사용자만 프로비전 범위에 속해야 한다고 지정할 수 있습니다. 자세한 내용은 [범위 지정 필터 사용](define-conditional-rules-for-provisioning-user-accounts.md)을 참조하세요.

     자세한 내용은 [특성 매핑 사용자 지정](customize-application-attributes.md)을 참조하세요.

   - **설정은** 현재 실행 중인지 여부를 포함 하 여 응용 프로그램에 대 한 프로 비전 서비스의 작업을 제어 합니다. **범위** 메뉴에서는 할당 된 사용자 및 그룹만 프로 비전 범위에 속해야 하는지 아니면 Azure AD 디렉터리의 모든 사용자를 프로 비전 해야 할지를 지정할 수 있습니다. 사용자 및 그룹 "할당"에 대한 자세한 내용은 [Azure Active Directory에서 엔터프라이즈 앱에 사용자 또는 그룹 할당](assign-user-or-group-access-portal.md)을 참조하세요.

앱 관리 화면에서 **프로 비전 로그 (미리 보기)** 를 선택 하 여 Azure AD 프로 비전 서비스에서 실행 하는 모든 작업의 레코드를 확인 합니다. 자세한 내용은 [프로비전 보고 가이드](check-status-user-account-provisioning.md)를 참조하세요.

![예제-앱에 대 한 로그 프로비저닝 화면](./media/user-provisioning/audit_logs.PNG)

> [!NOTE]
> [Microsoft Graph API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)를 사용하여 Azure AD 사용자 프로비저닝 서비스를 구성 및 관리할 수도 있습니다.

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
