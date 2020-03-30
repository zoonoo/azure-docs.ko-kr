---
title: 응용 프로그램에 대한 동의 관리 및 동의 요청 평가 - Azure AD
description: 사용자 동의가 비활성화되거나 제한될 때 동의 요청을 관리하는 방법과 응용 프로그램에 대한 테넌트 전체 관리자 동의 요청을 평가하는 방법에 대해 알아봅니다.
services: active-directory
author: psignoret
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/27/2019
ms.author: mimart
ms.reviewer: phsignor
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0451fe18629a572c9b49f14924bfa50293f42a2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77367841"
---
# <a name="managing-consent-to-applications-and-evaluating-consent-requests"></a>응용 프로그램에 대한 동의 관리 및 동의 요청 평가

응용 프로그램에 대한 최종 사용자 동의를 사용하지 않도록 설정하는 [것이 좋습니다.](https://docs.microsoft.com/azure/security/fundamentals/steps-secure-identity#restrict-user-consent-operations) 이렇게 하면 조직의 보안 및 ID 관리자 팀과 함께 의사 결정 프로세스를 중앙 집중화할 수 있습니다.

최종 사용자 동의를 사용하지 않도록 설정하거나 제한한 후에도 비즈니스에 중요한 응용 프로그램을 계속 사용할 수 있도록 조직의 보안을 유지하는 몇 가지 중요한 고려 사항이 있습니다. 이러한 단계는 타사 응용 프로그램에서 관리되지 않는 계정을 사용하지 않도록 조직의 지원 팀과 IT 관리자에게 미치는 영향을 최소화하는 데 중요합니다.

## <a name="process-changes-and-education"></a>프로세스 변경 및 교육

 1. 사용자가 동의 화면에서 직접 관리자 승인을 요청할 수 있도록 [관리자 동의 워크플로(미리 보기)를](configure-admin-consent-workflow.md) 사용하도록 설정하는 것이 좋습니다.

 2. 모든 관리자가 [권한 및 동의 프레임워크,](../develop/consent-framework.md) [동의 프롬프트작동](../develop/application-consent-experience.md) 방식 및 [테넌트 전체 관리자 동의 요청을 평가하는](#evaluating-a-request-for-tenant-wide-admin-consent)방법을 이해해야 합니다.
 3. 사용자가 응용 프로그램에 대한 관리자 승인을 요청하고 필요한 경우 업데이트할 수 있도록 조직의 기존 프로세스를 검토합니다. 프로세스가 변경된 경우:
    * 관련 문서, 모니터링, 자동화 등을 업데이트합니다.
    * 영향을 받는 모든 사용자, 개발자, 지원 팀 및 IT 관리자에게 프로세스 변경 내용을 전달합니다.

## <a name="auditing-and-monitoring"></a>감사 및 모니터링

1. 부당하거나 의심스러운 응용 프로그램이 이전에 데이터에 대한 액세스 권한을 부여하지 않도록 조직의 [앱 및 권한 부여를 감사합니다.](https://docs.microsoft.com/azure/security/fundamentals/steps-secure-identity#audit-apps-and-consented-permissions)

2. OAuth 동의를 요청하는 의심스러운 응용 프로그램에 대한 추가 모범 사례 및 보호 를 위해 [Office 365에서 불법 동의 교부금을 검색하고 수정합니다.](https://docs.microsoft.com/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants)

3. 조직에 적절한 라이선스가 있는 경우:

    * Microsoft [클라우드 앱 보안에서 추가 OAuth 응용 프로그램 감사 기능을](https://docs.microsoft.com/cloud-app-security/investigate-risky-oauth)사용합니다.
    * Azure 모니터 통합 문서를 사용하여 사용 권한 및 동의 관련 활동을 [모니터링합니다.](../reports-monitoring/howto-use-azure-monitor-workbooks.md) *동의 인사이트* 통합 문서에서는 실패한 동의 요청 수로 앱보기를 제공합니다. 이는 관리자가 관리자 동의를 부여할지 여부를 검토하고 결정할 수 있도록 응용 프로그램의 우선 순위를 지정하는 데 도움이 될 수 있습니다.

### <a name="additional-considerations-for-reducing-friction"></a>마찰 감소를 위한 추가 고려 사항

이미 사용 중인 신뢰할 수 있는 업무상 중요한 응용 프로그램에 대한 영향을 최소화하려면 사용자 동의 부여가 많은 응용 프로그램에 관리자 동의를 사전에 부여하는 것이 좋습니다.

1. 로그인 로그인 또는 동의 부여 활동을 기반으로 사용량이 많은 조직에 이미 추가된 앱의 인벤토리를 작성합니다. PowerShell [스크립트를](https://gist.github.com/psignoret/41793f8c6211d2df5051d77ca3728c09) 사용하여 많은 수의 사용자 동의 권한 부여가 있는 응용 프로그램을 빠르고 쉽게 검색할 수 있습니다.

2. 관리자 동의가 아직 부여되지 않은 상위 응용 프로그램을 평가합니다.

   > [!IMPORTANT]
   > 조직의 많은 사용자가 이미 동의한 경우에도 테넌트 전체 관리자 동의를 부여하기 전에 응용 프로그램을 신중하게 평가합니다.

3. 승인된 각 응용 프로그램에 대해 아래에 설명된 방법 중 하나를 사용하여 테넌트 전체 관리자 동의를 부여합니다.

4. 승인된 각 응용 프로그램에 대해 [사용자 액세스를 제한하는 것이](configure-user-consent.md)좋습니다.

## <a name="evaluating-a-request-for-tenant-wide-admin-consent"></a>테넌트 전체 관리자 동의 요청 평가

테넌트 전체 관리자 동의를 부여하는 것은 중요한 작업입니다.  권한은 전체 조직을 대신하여 부여되며 권한높은 권한을 부여받은 작업을 시도할 수 있는 권한을 포함할 수 있습니다. 예를 들어 역할 관리, 모든 사서함 또는 모든 사이트에 대한 전체 액세스 및 전체 사용자 가장.

테넌트 전체 관리자 동의를 부여하기 전에 응용 프로그램 및 응용 프로그램 게시자를 신뢰하여 부여하려는 액세스 수준을 확인해야 합니다. 응용 프로그램을 제어하는 사람과 응용 프로그램이 권한을 요청하는 이유를 이해하지 못하는 경우 *동의를 부여하지 마십시오.*

다음 목록은 관리자 동의를 부여하는 요청을 평가할 때 고려해야 할 몇 가지 권장 사항을 제공합니다.

* **Microsoft ID 플랫폼의 [사용 권한 및 동의 프레임워크를](../develop/consent-framework.md) 이해합니다.**

* **[위임된 사용 권한과 응용 프로그램 사용 권한의 차이점을 이해합니다.](../develop/v2-permissions-and-consent.md#permission-types)**

   응용 프로그램 권한을 사용하면 응용 프로그램이 사용자 상호 작용 없이 전체 조직의 데이터에 액세스할 수 있습니다. 위임된 사용 권한을 사용하면 응용 프로그램이 응용 프로그램에 서명된 사용자를 대신하여 작동할 수 있습니다.

* **요청되는 권한을 이해합니다.**

   응용 프로그램에서 요청한 권한은 동의 [프롬프트에](../develop/application-consent-experience.md)나열됩니다. 권한 제목을 확장하면 사용 권한설명이 표시됩니다. 응용 프로그램 사용 권한에 대한 설명은 일반적으로 "로그인한 사용자 없이"로 끝납니다. 위임된 사용 권한에 대한 설명은 일반적으로 "로그인한 사용자를 대신하여"로 끝납니다. Microsoft 그래프 API에 대한 사용 권한은 [Microsoft 그래프 사용 권한 참조]에 설명되어 있습니다.

   요청되는 권한을 이해하지 못하는 경우 *동의를 부여하지 마십시오.*

* **권한을 요청하는 응용 프로그램과 응용 프로그램을 게시한 사람을 이해합니다.**

   다른 응용 프로그램처럼 보이려는 악의적인 응용 프로그램에 주의하십시오.

   응용 프로그램 또는 해당 게시자의 정당성을 의심하는 경우 *동의를 부여하지 마십시오.* 대신 추가 확인(예: 응용 프로그램 게시자에서 직접)을 요청합니다.

* **요청된 사용 권한이 응용 프로그램에서 예상한 기능과 일치하는지 확인합니다.**

   예를 들어 SharePoint 사이트 관리를 제공하는 응용 프로그램은 모든 사이트 모음을 읽기 위해 위임된 액세스 권한이 필요할 수 있지만 모든 사서함에 대한 전체 액세스 또는 디렉터리의 전체 가장 권한이 반드시 필요한 것은 아닙니다.

   응용 프로그램이 필요한 것보다 더 많은 권한을 요청하는 것으로 의심되는 경우 *동의를 부여하지 마십시오.* 자세한 내용은 응용 프로그램 게시자에게 문의하십시오.

## <a name="granting-consent-as-an-administrator"></a>관리자동의 부여

### <a name="granting-tenant-wide-admin-consent"></a>테넌트 전체 관리자 동의 부여

Azure 포털에서 테넌트 전체 [관리자 동의를](grant-admin-consent.md) 부여하거나 Azure AD PowerShell을 사용하거나 동의 프롬프트 자체에서 테넌트 전체 관리자 동의를 부여하기 위한 단계별 지침은 응용 프로그램에 대한 권한 부여 를 참조하십시오.

### <a name="granting-consent-on-behalf-of-a-specific-user"></a>특정 사용자를 대신하여 동의 승인

관리자는 전체 조직에 대한 동의를 부여하는 대신 [Microsft Graph API를](https://docs.microsoft.com/graph/use-the-api) 사용하여 단일 사용자를 대신하여 위임된 권한에 대한 동의를 부여할 수도 있습니다. 자세한 내용은 [사용자를 대신하여 액세스 권한을](https://docs.microsoft.com/graph/auth-v2-user)참조하세요.

## <a name="limiting-user-access-to-applications"></a>응용 프로그램에 대한 사용자 액세스 제한

테넌트 전체 관리자 동의가 부여된 경우에도 응용 프로그램에 대한 사용자의 액세스가 제한될 수 있습니다. 응용 프로그램에 사용자 할당을 요구하는 방법에 대한 자세한 내용은 [사용자 및 그룹을 할당하는 메서드를](methods-for-assigning-users-and-groups.md)참조하십시오.

추가 복잡한 시나리오를 처리하는 방법을 포함하여 보다 광범위한 개요는 [응용 프로그램 액세스 관리에 Azure AD를 사용 참조하세요.](what-is-access-management.md)

## <a name="next-steps"></a>다음 단계

[ID 인프라를 보호하기 위한 5단계](https://docs.microsoft.com/azure/security/fundamentals/steps-secure-identity#before-you-begin-protect-privileged-accounts-with-mfa)

[관리자 동의 워크플로 구성](configure-admin-consent-workflow.md)

[최종 사용자가 응용 프로그램에 동의하는 방식 구성](configure-user-consent.md)

[Microsoft ID 플랫폼의 권한 및 동의](../develop/active-directory-v2-scopes.md)

[스택 오버플로에서 Azure AD](https://stackoverflow.com/questions/tagged/azure-active-directory)