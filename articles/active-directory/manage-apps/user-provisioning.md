---
title: Azure AD에서 SaaS 앱 사용자를 자동으로 프로비저닝 | Microsoft Docs
description: Azure AD를 사용하여 여러 타사 SaaS 응용 프로그램에서 사용자 계정을 자동으로 프로비저닝, 프로비저닝 해제, 지속적으로 업데이트하는 방법을 소개합니다.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/30/2018
ms.author: barbkess
ms.reviewer: asmalser
ms.openlocfilehash: 680cea983fb7435bf4492fc295e29f3a234a4323
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/11/2018
ms.locfileid: "44356066"
---
# <a name="automate-user-provisioning-and-deprovisioning-to-saas-applications-with-azure-active-directory"></a>Azure Active Directory를 사용하여 SaaS 응용 프로그램의 사용자를 자동으로 프로비저닝 및 프로비저닝 해제
## <a name="what-is-automated-user-provisioning-for-saas-apps"></a>SaaS 앱을 위한 자동 사용자 프로비저닝이란?
Azure AD(Azure Active Directory)를 사용하면 Dropbox, Salesforce, ServiceNow 등과 같은 클라우드([SaaS](https://azure.microsoft.com/overview/what-is-saas/)) 응용 프로그램의 사용자 ID 생성, 관리 및 제거를 자동화할 수 있습니다.

> [!VIDEO https://www.youtube.com/embed/_ZjARPpI6NI]

**다음은 이 기능을 어떻게 활용할 수 있는지에 대한 몇 가지 예입니다.**

* 새로운 사람이 팀 또는 조직에 가입할 때 적절한 시스템에서 새 계정을 자동으로 만듭니다.
* 사용자가 팀 또는 조직을 떠날 때 적절한 시스템에서 계정을 자동으로 비활성화합니다.
* 디렉터리 또는 인사 관리 시스템의 변경 내용에 따라 앱 및 시스템의 ID가 최신 상태로 유지되도록 합니다.
* 그룹과 같이 사용자가 아닌 개체를 지원하는 응용 프로그램에 해당 개체를 프로비전합니다.

**자동 사용자 프로비저닝에는 다음과 같은 기능도 포함됩니다.**

* 원본 및 대상 시스템 간에 기존 ID를 일치시킬 수 있는 기능
* 소스 시스템에서 대상 시스템으로 이동되어야 하는 사용자 데이터를 정의하는 사용자 지정 가능한 특성 매핑입니다.
* 프로비저닝 오류를 메일로 경고 받을 수 있는 선택적 기능
* 모니터링 및 문제 해결에 도움이 되는 보고 및 활동 로그.

## <a name="why-use-automated-provisioning"></a>자동 프로비전을 사용하는 이유는 무엇입니까?
이 기능을 사용하게 되는 일반적인 동기는 다음과 같습니다.

* 수동 프로비저닝 절차에서 빚어지는 비용, 비효율성, 사람의 실수를 방지합니다.
* 사용자 지정 개발된 프로비저닝 솔루션과 스크립트를 호스트하고 유지하는 데 관련된 비용을 방지합니다.
* 사용자가 조직을 떠날 때 즉시 주요 SaaS 앱에서 사용자 ID를 제거하여 조직의 보안을 유지합니다.
* 특정 SaaS 응용 프로그램 또는 시스템에 다수의 사용자를 손쉽게 가져옵니다.
* 단일 정책 집합을 사용하여 프로비전된 사용자와 앱에 로그인할 수 있는 사용자를 편리하게 확인합니다.

## <a name="how-does-automatic-provisioning-work"></a>자동 프로비전은 어떻게 작동합니까?
    
**Azure AD 프로비전 서비스**는 각 응용 프로그램 공급업체에서 제공하는 사용자 관리 API 엔드포인트에 연결하여 SaaS 앱 및 다른 시스템에 사용자를 프로비전합니다. 이러한 사용자 관리 API 엔드포인트를 사용하면 Azure AD에서 프로그래밍 방식으로 사용자를 만들고, 업데이트하고, 제거할 수 있습니다. 선택한 응용 프로그램의 경우 프로비전 서비스는 그룹 및 역할과 같은 추가 ID 관련 개체를 만들고, 업데이트하고, 제거할 수 있습니다. 

![프로비전](./media/user-provisioning/provisioning0.PNG)
*그림 1: Azure AD 프로비전 서비스*

![아웃바운드 프로비전](./media/user-provisioning/provisioning1.PNG)
*그림 2: Azure AD에서 인기 있는 SaaS 응용 프로그램으로의 "아웃바운드" 사용자 프로비전 워크플로*

![인바운드 프로비전](./media/user-provisioning/provisioning2.PNG)
*그림 3: 인기 있는 HCM(Human Capital Management) 응용 프로그램에서 Azure Active Directory 및 Windows Server Active Directory로의 "인바운드" 사용자 프로비전 워크플로*


## <a name="what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning"></a>Azure AD 자동 사용자 프로비전에서 사용할 수 있는 응용 프로그램과 시스템은 무엇입니까?

Azure AD는 SCIM 2.0 표준의 특정 부분을 구현하는 앱에 대한 일반적 지원뿐만 아니라 다양한 인기 있는 SaaS 앱 및 인사 관리 시스템에 대한 사전 통합된 지원도 제공합니다.

### <a name="pre-integrated-applications"></a>사전 통합된 응용 프로그램
Azure AD가 미리 통합된 프로비저닝 커넥터를 지원하는 모든 응용 프로그램 목록을 보려면 [사용자 프로비저닝에 대한 응용 프로그램 자습서 목록](../saas-apps/tutorial-list.md)을 참조하세요.

Azure AD 엔지니어링 팀에 문의하여 추가 응용 프로그램에 대한 프로비전 지원을 요청하려면 [Azure Active Directory 피드백 포럼](https://feedback.azure.com/forums/374982-azure-active-directory-application-requests/filters/new?category_id=172035)을 통해 메시지를 제출하세요.

> [!NOTE]
> 응용 프로그램에서 자동화된 사용자 프로비저닝을 지원하려면, 먼저 외부 프로그램에서 사용자 만들기, 유지 관리 및 제거를 자동화하는 데 필요한 사용자 관리 API를 제공해야 합니다. 따라서 모든 SaaS 앱이 이 기능과 호환되지는 않습니다. 사용자 관리 API를 지원하는 앱의 경우 Azure AD 엔지니어링 팀에서 해당 앱에 대한 프로비전 커넥터를 빌드할 수 있게 되며, 현재 및 잠재 고객의 요구 사항에 따라 이 작업의 우선 순위가 지정됩니다. 

### <a name="connecting-applications-that-support-scim-20"></a>SCIM 2.0을 지원하는 응용 프로그램 연결
SCIM 2.0 기반 사용자 관리 API를 구현하는 응용 프로그램에 일반적으로 연결하는 방법에 대한 자세한 내용은 [SCIM을 사용하여 사용자 및 그룹을 Azure Active Directory에서 응용 프로그램으로 자동으로 프로비전](use-scim-to-provision-users-and-groups.md)을 참조하세요.

    
## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>응용 프로그램에 자동 프로비전을 설정하려면 어떻게 합니까?

> [!VIDEO https://www.youtube.com/embed/pKzyts6kfrw]

선택한 응용 프로그램에 대한 Azure AD 프로비전 서비스의 구성은 **[Azure Portal](https://portal.azure.com)** 에서 시작됩니다. **Azure Active Directory > 엔터프라이즈 응용 프로그램** 섹션에서 **추가**, **모두**를 차례로 선택한 다음, 시나리오에 따라 다음 중 하나를 추가합니다.

* **주요 응용 프로그램** 섹션에 있는 응용 프로그램은 모두 자동 프로비저닝을 지원합니다. 추가 응용 프로그램을 보려면 [사용자 프로비전에 대한 응용 프로그램 자습서 목록](../saas-apps/tutorial-list.md)을 참조하세요.

* 사용자 지정 개발된 SCIM 통합에 "비갤러리 응용 프로그램" 옵션을 사용합니다.

![갤러리](./media/user-provisioning/gallery.png)

응용 프로그램 관리 화면의 **프로비전** 탭에서 프로비전을 구성합니다.

![설정](./media/user-provisioning/provisioning_settings0.PNG)


* **관리자 자격 증명**을 Azure AD 프로비전 서비스에 제공해야 응용 프로그램에서 제공하는 사용자 관리 API에 연결할 수 있습니다. 이 섹션에서는 자격 증명이 실패하거나 프로비저닝 작업이 [격리](#quarantine) 상태로 전환될 경우 메일 알림을 받도록 설정할 수도 있습니다.

* **특성 매핑**은 원본 시스템(예: Azure AD)의 필드와 대상 시스템의 필드(예: ServiceNow) 간에 내용을 동기화하도록 지정하기 위해 구성할 수 있습니다. 대상 응용 프로그램에서 이 설정을 지원하는 경우 이 섹션에서는 필요에 따라 사용자 계정 외에도 그룹을 프로비전하도록 구성할 수 있습니다. "일치하는 속성"을 사용하면 시스템 간에 계정을 일치시키는 데 사용되는 필드를 선택할 수 있습니다. "[식](functions-for-customizing-application-data.md)"을 사용하면 대상 시스템에 쓰기 전에 원본 시스템에서 검색된 값을 수정하고 변환할 수 있습니다. 자세한 내용은 [특성 매핑 사용자 지정](customize-application-attributes.md)을 참조하세요.

![설정](./media/user-provisioning/provisioning_settings1.PNG)

* **범위 지정 필터**는 대상 시스템에 프로비전 및/또는 프로비전 해제되어야 하는 원본 시스템의 사용자 및 그룹을 프로비전 서비스에 알립니다. 프로비전 범위에 속하는 사람을 결정하기 위해 함께 평가되는 범위 지정 필터에는 다음 두 가지 측면이 있습니다.

    * **Filter on attribute values(특성 값 기준 필터링)** - 특성 매핑의 "원본 개체 범위" 메뉴를 사용하면 특정 특성 값에 따라 필터링할 수 있습니다. 예를 들어 "Department" 특성이 "Sales"인 사용자만 프로비전 범위에 속해야 한다고 지정할 수 있습니다. 자세한 내용은 [범위 지정 필터 사용](define-conditional-rules-for-provisioning-user-accounts.md)을 참조하세요.

    * **Filter on assignments(할당 기준 필터링)** - 포털의 프로비전 > 설정 섹션에 있는 "범위" 메뉴를 사용하면, "할당된" 사용자 및 그룹만 프로비전 범위에 속해야 하는지 또는 Azure AD 디렉터리의 모든 사용자가 프로비전되어야 하는지를 지정할 수 있습니다. 사용자 및 그룹 "할당"에 대한 자세한 내용은 [Azure Active Directory에서 엔터프라이즈 앱에 사용자 또는 그룹 할당](assign-user-or-group-access-portal.md)을 참조하세요.
    
* **설정**은 현재 실행 중인지 여부를 포함하여 응용 프로그램에 대한 프로비전 서비스 작업을 제어합니다.

* **감사 로그**는 Azure AD 프로비전 서비스가 수행한 모든 작업에 대한 기록을 제공합니다. 자세한 내용은 [프로비전 보고 가이드](check-status-user-account-provisioning.md)를 참조하세요.

![설정](./media/user-provisioning/audit_logs.PNG)

> [!NOTE]
> [Microsoft Graph API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)를 사용하여 Azure AD 사용자 프로비저닝 서비스를 구성 및 관리할 수도 있습니다.


## <a name="what-happens-during-provisioning"></a>프로비전하는 동안 어떻게 됩니까?

Azure AD가 소스 시스템인 경우 프로비저닝 서비스는 [Azure AD Graph API의 차등 쿼리 기능](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query)을 사용하여 사용자와 그룹을 모니터링합니다. 프로비저닝 서비스는 소스 시스템 및 대상 시스템에 대해 초기 동기화를 실행한 다음, 주기적 증분 동기화를 실행합니다. 

### <a name="initial-sync"></a>초기 동기화
프로비저닝 서비스를 시작하면 첫 번째 동기화에서 다음을 수행합니다.

1. [특성 매핑](customize-application-attributes.md)에 정의된 모든 특성을 검색하여 소스 시스템에서 모든 사용자와 그룹을 쿼리합니다.
2. 구성된 [할당](assign-user-or-group-access-portal.md) 또는 [특성 기반 범위 지정 필터](define-conditional-rules-for-provisioning-user-accounts.md)를 사용하여 반환된 사용자 및 그룹을 필터링합니다.
3. 사용자가 할당되었거나 프로비저닝 범위에 있는 경우 서비스는 지정된 [일치 특성](customize-application-attributes.md#understanding-attribute-mapping-properties)을 사용하여 대상 시스템에서 일치하는 사용자를 쿼리합니다. 예: 소스 시스템의 userPrincipal 이름이 일치하는 특성이고 대상 시스템의 userName에 매핑되는 경우 프로비저닝 서비스는 대상 시스템에서 소스 시스템의 userPrincipal 이름 값과 일치하는 userNames를 쿼리합니다.
4. 대상 시스템에 일치하는 사용자가 없으면 소스 시스템에서 반환된 특성을 사용하여 생성됩니다.
5. 일치하는 사용자가 있으면 소스 시스템에서 제공하는 특성을 사용하여 업데이트됩니다.
6. 특성 매핑에 “참조” 특성이 포함되어 있을 경우 서비스는 대상 시스템에서 추가 업데이트를 수행하여 참조된 개체를 만들고 연결합니다. 예를 들어 대상 시스템에 있는 한 사용자의 “Manager” 특성이 대상 시스템에서 생성된 다른 사용자에 연결되어 있을 수 있습니다.
7. 초기 동기화가 끝나면 워터마크를 저장합니다. 이 워터마크는 후속 증분 동기화의 시작점을 제공합니다.

ServiceNow, Google Apps, Box 등의 일부 응용 프로그램은 사용자 프로비저닝뿐 아니라 그룹 및 해당 멤버 프로비저닝도 지원합니다. 이 경우 [매핑](customize-application-attributes.md)에서 그룹 프로비저닝을 사용하도록 설정하면 프로비저닝 서비스에서 사용자와 그룹을 동기화한 다음, 그룹 멤버 자격을 동기화합니다. 

### <a name="incremental-syncs"></a>증분 동기화
초기 동기화 후 모든 후속 동기화는 다음을 수행합니다.

1. 소스 시스템에서 마지막 워터마크가 저장된 이후 업데이트된 사용자 및 그룹을 쿼리합니다.
2. 구성된 [할당](assign-user-or-group-access-portal.md) 또는 [특성 기반 범위 지정 필터](define-conditional-rules-for-provisioning-user-accounts.md)를 사용하여 반환된 사용자 및 그룹을 필터링합니다.
3. 사용자가 할당되었거나 프로비저닝 범위에 있는 경우 서비스는 지정된 [일치 특성](customize-application-attributes.md#understanding-attribute-mapping-properties)을 사용하여 대상 시스템에서 일치하는 사용자를 쿼리합니다.
4. 대상 시스템에 일치하는 사용자가 없으면 소스 시스템에서 반환된 특성을 사용하여 생성됩니다.
5. 일치하는 사용자가 있으면 소스 시스템에서 제공하는 특성을 사용하여 업데이트됩니다.
6. 특성 매핑에 “참조” 특성이 포함되어 있을 경우 서비스는 대상 시스템에서 추가 업데이트를 수행하여 참조된 개체를 만들고 연결합니다. 예를 들어 대상 시스템에 있는 한 사용자의 “Manager” 특성이 대상 시스템에서 생성된 다른 사용자에 연결되어 있을 수 있습니다.
7. 이전에 프로비저닝 범위에 있던 사용자가 범위에서 제거되면(할당 취소되는 경우 포함), 서비스는 업데이트를 통해 대상 시스템에서 사용자를 해제합니다.
8. 이전에 프로비저닝 범위에 있던 사용자가 소스 시스템에서 해제 또는 일시 삭제되면 서비스는 업데이트를 통해 대상 시스템에서 사용자를 해제합니다.
9. 이전에 프로비저닝 범위에 있던 사용자가 소스 시스템에서 영구 삭제되면 서비스는 대상 시스템에서 사용자를 삭제합니다. Azure AD에서 사용자는 일시 삭제된 날부터 30일 후에 영구 삭제됩니다.
10. 증분 동기화가 끝나면 새 워터마크를 저장합니다. 이 워터마크는 후속 증분 동기화의 시작점을 제공합니다.

>[!NOTE]
> 필요에 따라 [특성 매핑](customize-application-attributes.md) 섹션의 **대상 개체 작업** 확인란을 사용하여 만들기, 업데이트 또는 삭제 작업을 사용하지 않도록 설정할 수 있습니다. 업데이트 중에 사용자를 해제하는 논리도 “accountEnabled”와 같은 필드의 특성 매핑을 통해 제어됩니다.

프로비저닝 서비스는 다음 이벤트 중 하나가 발생할 때까지 [각 응용 프로그램과 관련된 자습서](../saas-apps/tutorial-list.md)에 정의된 간격마다 연속 증분 동기화를 무기한 실행합니다.

* 서비스가 Azure Portal을 사용하여 수동으로 중지되거나 적절한 Graph API 명령을 사용하여 중지됩니다. 
* Azure Portal의 **상태 지우기 및 다시 시작** 옵션을 사용하거나 적절한 Graph API 명령을 사용하여 새 초기 동기화가 트리거됩니다. 이 경우 저장된 워터마크가 모두 지워지며 모든 소스 개체가 다시 평가됩니다.
* 특성 매핑 또는 범위 지정 필터가 변경되어 새 초기 동기화가 트리거됩니다. 이 경우에도 저장된 워터마크가 모두 지워지며 모든 소스 개체가 다시 평가됩니다.
* 높은 오류 비율로 인해 프로비저닝 프로세스가 격리(아래 참조) 상태로 전환되고 4주 넘게 격리 상태로 유지됩니다. 이 이벤트가 발생하면 자동으로 서비스를 사용할 수 없게 됩니다.

### <a name="errors-and-retries"></a>오류 및 다시 시도 
대상 시스템의 오류로 인해 대상 시스템에서 개별 사용자를 추가, 업데이트 또는 삭제할 수 없는 경우 다음 동기화 주기에서 작업이 다시 시도됩니다. 사용자가 계속 실패하면 감소된 빈도로 다시 시도되기 시작하여, 점차 하루에 한 번만 시도되는 빈도까지 축소됩니다. 오류를 해결하려면 관리자가 “프로세스 에스크로” 이벤트에 대한 [감사 로그](check-status-user-account-provisioning.md)를 확인하여 근본 원인을 파악하고 적절한 조치를 수행해야 합니다. 일반적인 오류는 다음과 같습니다.

* 대상 시스템에서 필요한 특성이 사용자의 소스 시스템에 채워져 있지 않음
* 대상 시스템에서 UNIQUE 제약 조건을 가진 특성 값이 사용자의 소스 시스템에 있고 다른 사용자 레코드에 동일한 값이 있음

이러한 오류는 소스 시스템에서 영향을 받는 사용자의 특성 값을 조정하거나 충돌이 발생하지 않도록 특성 매핑을 조정하여 해결할 수 있습니다.   

### <a name="quarantine"></a>격리
대상 시스템에 대한 호출이 오류(예: 잘못된 관리자 자격 증명)로 인해 대부분 또는 모두 일관되게 실패하는 경우 프로비저닝 작업이 “격리” 상태로 전환됩니다. 이 사실은 [프로비저닝 요약 보고서](check-status-user-account-provisioning.md) 및 Azure Portal에서 메일 알림이 구성된 경우 메일을 통해 알 수 있습니다. 

격리 중에 증분 동기화 빈도는 하루에 한 번까지 점차 감소됩니다. 

위반 오류를 모두 수정하고 다음 동기화 주기가 시작되면 프로비저닝 작업이 격리에서 제거됩니다. 프로비저닝 작업이 4주 넘게 격리 상태로 유지되면 프로비저닝 작업을 사용할 수 없게 됩니다.


## <a name="how-long-will-it-take-to-provision-users"></a>사용자를 프로비전하는 데 걸리는 시간은 어느 정도인가요?

성능은 이전 섹션에서 설명한 대로 프로비저닝 작업이 초기 동기화 또는 증분 동기화를 수행하는지에 따라 다릅니다.

**초기 동기화**의 경우, 작업 시간은 프로비저닝 범위에 속하는 사용자 및 그룹 수와 원본 시스템에 있는 사용자 및 그룹의 총 수를 포함하여 다양한 요인에 따라 달라집니다. 초기 동기화 성능에 영향을 주는 포괄적인 요인 목록은 이 섹션의 뒷부분에 요약되어 있습니다.

**증분 동기화**의 경우, 작업 시간은 해당 동기화 주기에서 검색된 변경 수에 따라 달라집니다. 사용자 또는 그룹 멤버 자격 변경이 5,000개 미만인 경우, 단일 증분 동기화 주기 내에 작업이 완료될 수 있습니다. 

다음 표에는 일반적인 프로비저닝 시나리오의 동기화 시간이 요약되어 있습니다. 이러한 시나리오에서 원본 시스템은 Azure AD이고, 대상 시스템은 SaaS 응용 프로그램입니다. 동기화 시간은 SaaS 응용 프로그램인 ServiceNow, Workplace, Salesforce 및 Google Apps에 대한 동기화 작업의 통계 분석에서 파생된 것입니다.


| 범위 구성 | 범위 내 사용자, 그룹 및 멤버 | 초기 동기화 시간 | 증분 동기화 시간 |
| -------- | -------- | -------- | -------- |
| 할당된 사용자 및 그룹만 동기화 |  1,000 미만 |  30분 미만 | 30분 미만 |
| 할당된 사용자 및 그룹만 동기화 |  1,000 - 10,000 | 142 - 708분 | 30분 미만 |
| 할당된 사용자 및 그룹만 동기화 |   10,000 - 100,000 | 1,170 - 2,340분 | 30분 미만 |
| Azure AD의 모든 사용자 및 그룹 동기화 |  1,000 미만 | 30분 미만  | 30분 미만 |
| Azure AD의 모든 사용자 및 그룹 동기화 |  1,000 - 10,000 | 30 - 120분 | 30분 미만 |
| Azure AD의 모든 사용자 및 그룹 동기화 |  10,000 - 100,000  | 713 - 1,425분 | 30분 미만 |
| Azure AD의 모든 사용자 동기화|  1,000 미만  | 30분 미만 | 30분 미만 |
| Azure AD의 모든 사용자 동기화 | 1,000 - 10,000  | 43 - 86분 | 30분 미만 |


**할당된 사용자 및 그룹만 동기화** 구성의 경우, 다음 공식을 사용하여 대략적인 최소 및 최대 예상 **초기 동기화** 시간을 확인할 수 있습니다.

    Minimum minutes =  0.01 x [Number of assigned users, groups, and group members]
    Maximum minutes = 0.08 x [Number of assigned users, groups, and group members] 
    
**초기 동기화**를 완료하는 데 걸리는 시간에 영향을 주는 요인 요약:

* 프로비저닝 범위에 속하는 사용자 및 그룹의 총 수

* 원본 시스템(Azure AD)에 있는 사용자, 그룹 및 그룹 멤버의 총 수

* 프로비저닝 범위 내 사용자가 대상 응용 프로그램의 기존 사용자와 일치하는지 여부 또는 처음 생성해야 하는지 여부. 모든 사용자가 처음 생성되는 동기화 작업이 모든 사용자가 기존 사용자와 일치하는 동기화 작업보다 대략 *두 배* 정도 오래 걸립니다.

* [감사 로그](check-status-user-account-provisioning.md)의 오류 수. 많은 오류가 있고 프로비저닝 서비스가 격리 상태로 전환된 경우 성능이 저하됩니다. 

* 대상 시스템에서 구현된 요청 비율 한도 및 제한. 일부 대상 시스템은 큰 동기화 작업 중 성능에 영향을 줄 수 있는 요청 비율 한도 및 제한을 구현합니다. 이러한 조건에서 너무 많은 요청을 너무 빠르게 받는 앱은 응답 속도가 느려지거나 연결을 닫을 수도 있습니다. 성능을 개선하려면 커넥터가 앱이 처리할 수 있는 속도보다 빠르게 앱 요청이 전송되지 않도록 조정해야 합니다. Microsoft에서 빌드한 프로비저닝 커넥터는 이러한 조정 작업을 수행합니다. 

* 할당된 그룹 수 및 크기. 할당된 그룹 동기화가 사용자 동기화보다 시간이 오래 걸립니다. 할당된 그룹 수와 크기는 둘 다 성능에 영향을 줍니다. 응용 프로그램에 [그룹 개체 동기화가 사용되는 매핑](customize-application-attributes.md#editing-group-attribute-mappings)이 있는 경우 그룹 이름 및 멤버 자격과 같은 그룹 속성이 사용자와 함께 동기화됩니다. 이러한 추가 동기화 때문에 사용자 객체만 동기화하는 것보다 시간이 오래 걸립니다.


##<a name="how-can-i-tell-if-users-are-being-provisioned-properly"></a>사용자가 적절히 프로비전되는지 어떻게 확인할 수 있나요?

사용자 프로비저닝 서비스에서 수행되는 모든 작업은 Azure AD 감사 로그에 기록됩니다. 원본 및 대상 시스템에 만들어진 모든 읽기 및 쓰기 작업 뿐만 아니라 각 작업 동안 읽거나 작성된 사용자 데이터를 포함합니다.

Azure Portal에서 감사 로그를 읽는 방법에 대한 자세한 내용은 [프로비전 보고 가이드](check-status-user-account-provisioning.md)를 참조하세요.


##<a name="how-do-i-troubleshoot-issues-with-user-provisioning"></a>사용자 프로비저닝 문제를 어떻게 해결하나요?

자동 사용자 프로비전 문제를 해결하는 방법에 대한 시나리오 기반 지침은 [응용 프로그램에 사용자를 구성 및 프로비전하는 문제](application-provisioning-config-problem.md)를 참조하세요.


##<a name="what-are-the-best-practices-for-rolling-out-automatic-user-provisioning"></a>자동 사용자 프로비저닝을 롤아웃하기 위한 모범 사례는 무엇인가요?

> [!VIDEO https://www.youtube.com/embed/MAy8s5WSe3A]

응용 프로그램에 대한 아웃바운드 사용자 프로비저닝에 대한 단계별 배포 계획 예제는 [사용자 프로비저닝에 대한 ID 배포 가이드](https://aka.ms/userprovisioningdeploymentplan)를 참조하세요.


## <a name="related-articles"></a>관련 문서
* [SaaS App을 통합하는 방법에 대한 자습서 목록](../saas-apps/tutorial-list.md)
* [사용자 프로비저닝에 대한 특성 매핑 사용자 지정](customize-application-attributes.md)
* [특성 매핑에 대한 식 작성](functions-for-customizing-application-data.md)
* [사용자 프로 비전에 대 한 필터 범위 지정](define-conditional-rules-for-provisioning-user-accounts.md)
* [SCIM를 사용하여 Azure Active Directory으로부터 응용 프로그램에 사용자 및 그룹의 자동 프로비전 사용](use-scim-to-provision-users-and-groups.md)
* [Azure AD 동기화 API 개요](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
