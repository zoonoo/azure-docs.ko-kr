---
title: Azure Active Directory 자격 관리를 사용 하 여 외부 액세스 관리
description: 전체 외부 액세스 보안 계획의 일부로 Azure Active Directory 권한 관리를 사용 하는 방법입니다.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: f06a54f59405d9833194b2e7d4488bc93d2437ae
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98725181"
---
# <a name="manage-external-access-with-entitlement-management"></a>권한 관리를 사용 하 여 외부 액세스 관리 


[자격 관리](../governance/entitlement-management-overview.md) 는 조직에서 액세스 요청 워크플로, 액세스 할당, 검토 및 만료를 자동화 하 여 규모에 따라 id 및 액세스 수명 주기를 관리할 수 있게 해 주는 id 거 버 넌 스 기능입니다. 권한 관리를 통해 관리자가 아닌 위임 된 관리자가 다른 조직의 외부 사용자가에 대 한 액세스를 요청할 수 있는 [액세스 패키지](../governance/entitlement-management-overview.md) 를 만들 수 있습니다. 요청을 평가 하 고 되풀이 검토를 사용 하 여 시간 제한 된 액세스에 대 한 사용자를 [프로 비전](../governance/what-is-provisioning.md) 하도록 하나 및 여러 단계 승인 워크플로를 구성할 수 있습니다. 자격 관리를 통해 외부 계정 프로 비전 및 프로 비전 해제를 수행할 수 있습니다.

## <a name="key-concepts-for-enabling-entitlement-management"></a>자격 관리를 사용 하기 위한 주요 개념

다음 주요 개념은 자격 관리를 이해 하는 데 중요 합니다.

### <a name="access-packages"></a>액세스 패키지

[액세스 패키지](../governance/entitlement-management-overview.md) 는 자격 관리의 기초가 됩니다. 액세스 패키지는 사용자가 프로젝트에서 공동 작업을 하거나 다른 작업을 수행 하는 데 필요한 정책 적용 리소스의 그룹입니다. 예를 들어 액세스 패키지에는 다음이 포함 될 수 있습니다.

* 특정 SharePoint 사이트에 액세스 합니다.

* Salesforce와 같은 사용자 지정 사내 및 SaaS 앱을 포함 하는 엔터프라이즈 응용 프로그램

* Microsoft 팀 채널.

* Microsoft 365 그룹 

### <a name="catalogs"></a>Catalogs

액세스 패키지는 [카탈로그](../governance/entitlement-management-catalog-create.md)에 있습니다. 관련 리소스를 그룹화 하 고 패키지에 액세스 하 고 관리 하는 기능을 위임 하려는 경우 카탈로그를 만듭니다. 먼저 카탈로그에 리소스를 추가 하 고 해당 리소스를 추가 하 여 패키지에 액세스할 수 있습니다. 예를 들어 "재무" 카탈로그를 만들고 [해당 관리](../governance/entitlement-management-delegate.md) 를 재무 팀의 구성원에 게 위임할 수 있습니다. 그런 다음 해당 사용자는 [리소스를 추가](../governance/entitlement-management-catalog-create.md)하 고, 액세스 패키지를 만들고, 해당 패키지에 대 한 액세스 승인을 관리할 수 있습니다.

다음 다이어그램에서는 만료 된 액세스 패키지에 대 한 액세스 권한을 얻는 외부 사용자의 일반적인 거 버 넌 스 수명 주기를 보여 줍니다.

![외부 사용자 관리 주기의 다이어그램입니다.](media/secure-external-access/6-governance-lifecycle.png)

### <a name="self-service-external-access"></a>셀프 서비스 외부 액세스

[AZURE AD 내 액세스 포털](../governance/entitlement-management-request-access.md) 을 통해 액세스 패키지를 노출 하 여 외부 사용자가 액세스를 요청할 수 있도록 할 수 있습니다. 정책은 액세스 패키지를 요청할 수 있는 사용자를 결정 합니다. 액세스 패키지를 요청할 수 있는 사용자를 지정 합니다.

* [연결 된 특정 조직](../governance/entitlement-management-organization.md)

* 구성 된 모든 연결 된 조직

* 모든 조직의 모든 사용자

* 테 넌 트에 이미 있는 구성원 또는 게스트 사용자

### <a name="approvals"></a>승인   
액세스 패키지에는 액세스에 대 한 필수 승인이 포함 될 수 있습니다. **외부 사용자에 대 한 승인 프로세스를 항상 구현** 합니다. 승인은 단일 또는 다단계 승인이 될 수 있습니다. 승인은 정책에 따라 결정 됩니다. 내부 및 외부 사용자 모두 동일한 패키지에 액세스 해야 하는 경우 연결 된 조직의 여러 범주 및 내부 사용자에 대해 서로 다른 액세스 정책을 설정할 가능성이 높습니다.

### <a name="expiration"></a>만료  
액세스 패키지에는 만료 날짜가 포함 될 수 있습니다. 만료는 특정 날로 설정 하거나 사용자에 게 액세스를 위한 특정 일 수를 지정할 수 있습니다. 액세스 패키지가 만료 되 고 사용자에 게 다른 액세스 권한이 없는 경우 사용자를 나타내는 B2B 게스트 사용자 개체를 삭제 하거나 로그인 하지 못하도록 차단할 수 있습니다. 외부 사용자에 대 한 액세스 패키지에 대해 만료를 적용 하는 것이 좋습니다. 일부 액세스 패키지에는 만료 된 내용이 없습니다. 이렇게 하지 않은 경우에는 액세스 검토를 수행 해야 합니다.

### <a name="access-reviews"></a>액세스 검토

액세스 패키지에는 사용자의 액세스에 대 한 지속적인 요구를 증명 하기 위해 패키지 소유자 또는 지정가 필요한 정기적인 [액세스 검토가](../governance/manage-guest-access-with-access-reviews.md)필요할 수 있습니다. 

검토를 설정 하기 전에 다음을 결정 합니다.

* 대상

   * 지속적인 액세스에 대 한 기준은 무엇 인가요?

   * 지정 된 검토자는 누구 인가요?

* 예약 된 검토가 얼마나 자주 발생 하나요?

   * 기본적으로 제공 되는 옵션에는 월별, 분기별, bi, 매년 또는 매년이 포함 됩니다. 

   * 외부 액세스를 지 원하는 패키지에는 분기별로 자주 사용 하는 것이 좋습니다. 

 

> [!IMPORTANT]
> 액세스 패키지의 액세스 검토는 자격 관리를 통해 부여 된 액세스만 검토 합니다. 따라서 자격 관리 외부 사용자에 게 제공 되는 액세스를 검토 하도록 다른 프로세스를 설정 해야 합니다.

액세스 검토에 대 한 자세한 내용은 [AZURE AD 액세스 검토 배포 계획](../governance/deploy-access-reviews.md)을 참조 하세요.

## <a name="using-automation-in-entitlement-management"></a>자격 관리에서 자동화 사용

다음을 포함 하 여 [Microsoft Graph를 사용 하 여 권한 관리 기능](/graph/tutorial-access-package-api)을 수행할 수 있습니다.

* [액세스 패키지 관리](/graph/api/resources/accesspackage?view=graph-rest-beta)

* [액세스 검토 관리](/graph/api/resources/accessreviewsv2-root?view=graph-rest-beta)

* [연결 된 조직 관리](/graph/api/resources/connectedorganization?view=graph-rest-beta)

* [권한 관리 설정 관리](/graph/api/resources/entitlementmanagementsettings?view=graph-rest-beta)

## <a name="recommendations"></a>권장 사항 

권한 관리를 사용 하 여 외부 액세스를 제어 하는 방법을 권장 합니다.

**하나 이상의 비즈니스 파트너를 포함 하는 프로젝트의 경우 [액세스 패키지를 만들고 사용](../governance/entitlement-management-access-package-create.md) 하 여 해당 파트너의 사용자에 게 리소스에** 대 한 액세스를 등록 하 고 프로 비전 합니다. 

* 디렉터리에 B2B 사용자가 이미 있는 경우 해당 사용자를 적절 한 액세스 패키지에 직접 할당할 수도 있습니다.

* [Azure Portal](../governance/entitlement-management-access-package-assignments.md)또는 [Microsoft Graph](/graph/api/resources/accesspackageassignmentrequest?view=graph-rest-beta)를 통해 액세스 권한을 할당할 수 있습니다.

**Id 거 버 넌 스 설정을 사용 하 여 액세스 패키지가 만료 될 때 디렉터리에서 사용자를 제거** 합니다.

![외부 사용자의 수명 주기 관리를 구성 하는 스크린샷](media/secure-external-access/6-manage-external-lifecycle.png)

이러한 설정은 자격 관리를 통해 등록 된 사용자 에게만 적용 됩니다.

**액세스 해야 하는 사용자에 대 한 자세한 정보가 있는 비즈니스 소유자에 게 [카탈로그 및 액세스 패키지 관리를 위임](../governance/entitlement-management-delegate.md)** 합니다.

![카탈로그를 구성 하는 스크린샷](media/secure-external-access/6-catalog-management.png)

**외부 사용자가 액세스할 수 있는 [액세스 패키지의 만료를 적용](../governance/entitlement-management-access-package-lifecycle-policy.md) 합니다.**


![액세스 패키지 만료 구성의 스크린샷](media/secure-external-access/6-access-package-expiration.png)

* 프로젝트 기반 액세스 패키지의 종료 날짜를 알고 있는 경우 날짜를 사용 하 여 특정 날짜를 설정 합니다. 

* 그렇지 않으면 multi-factor engagement로 알려져 있지 않는 한 만료를 365 일이 넘지 않는 것이 좋습니다.

* 사용자가 액세스를 확장할 수 있습니다.

* 확장을 부여 하려면 승인이 필요 합니다.

**게스트에 대 한 부적절 한 액세스를 방지 하기 위해 [패키지의 액세스 검토를 적용](../governance/manage-guest-access-with-access-reviews.md) 합니다.**

![새 액세스 패키지를 만드는 스크린샷](media/secure-external-access/6-new-access-package.png)

* 분기별 리뷰를 적용 합니다.

* 규정 준수를 구분 하는 프로젝트의 경우 외부 사용자에 대 한 자체 검토 대신 검토자를 특정 검토자로 설정 합니다. 패키지 관리자에 액세스 하는 사용자는 검토자를 위해 시작 하는 것이 좋습니다. 

* 중요 하지 않은 프로젝트의 경우에는 사용자가 직접 검토 하 여 조직에서 더 이상 홈 조직을 사용 하지 않는 사용자의 액세스 권한을 제거 하는 부담을 줄일 수 있습니다.

자세한 내용은 [AZURE AD 자격 관리에서 외부 사용자에 대 한 액세스 제어](../governance/entitlement-management-external-users.md) 를 참조 하세요. 

### <a name="next-steps"></a>다음 단계

리소스에 대 한 외부 액세스 보안에 대 한 다음 문서를 참조 하세요. 이러한 작업은 나열 된 순서 대로 수행 하는 것이 좋습니다.

1. [외부 액세스를 위한 보안 상태 결정](1-secure-access-posture.md)

2. [현재 상태 검색](2-secure-access-current-state.md)

3. [거 버 넌 스 계획 만들기](3-secure-access-plan.md)

4. [보안을 위해 그룹 사용](4-secure-access-groups.md)

5. [Azure AD B2B로 전환](5-secure-access-b2b.md)

6. [자격 관리를 통해 안전](6-secure-access-entitlement-managment.md) 하 게 액세스할 수 있습니다.

7. [조건부 액세스 정책을 사용 하 여 안전 하 게 액세스](7-secure-access-conditional-access.md)

8. [민감도 레이블을 사용 하 여 안전 하 게 액세스](8-secure-access-sensitivity-labels.md)

9. [Microsoft 팀, OneDrive 및 SharePoint에 대 한 액세스 보호](9-secure-access-teams-sharepoint.md)

 

