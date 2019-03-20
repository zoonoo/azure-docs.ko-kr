---
title: Azure AD PIM(Privileged Identity Management) 배포 | Microsoft Docs
description: Azure AD PIM(Privileged Identity Management) 배포를 계획하는 방법에 대해 설명합니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 02/08/2019
ms.author: rolyon
ms.custom: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 05bf125d629ffef01a645dc407c341a984805520
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2019
ms.locfileid: "58227034"
---
# <a name="deploy-azure-ad-privileged-identity-management-pim"></a>Azure AD PIM(Privileged Identity Management) 배포

이 단계별 가이드는 조직에서 Azure AD PIM(Privileged Identity Management) 배포를 계획하는 방법에 대해 설명합니다.

> [!TIP]
> 이 문서 전체에서 다음 상태로 표시된 항목을 볼 수 있습니다.
> 
> :heavy_check_mark: **Microsoft 권장 사항**
> 
> 이러한 항목은 일반 권장 사항이며, 특정 기업 요구에 적용되는 경우에만 구현하는 것이 좋습니다.

## <a name="step-1-learn-about-pim"></a>1단계. PIM의 자세한 정보

Azure AD PIM(Privileged Identity Management)을 사용하면 Azure AD, Azure 리소스 및 기타 Microsoft 온라인 서비스 간에 권한 있는 관리자 역할을 관리할 수 있습니다. 권한 있는 ID를 할당했는데 잊어 버린 경우, PIM은 권한 있는 역할의 악성 활동을 실시간으로 식별, 발견 및 방지할 수 있도록 Just-In-Time 액세스, 요청 승인 워크플로 및 완전히 통합된 액세스 검토와 같은 솔루션을 제공합니다. PIM을 배포하여 조직 전체에서 권한 있는 역할을 관리하면 위험이 크게 감소하면서도 권한 있는 역할의 활동에 관한 중요한 인사이트를 얻을 수 있습니다.

### <a name="business-value-of-pim"></a>PIM의 비즈니스 가치

**위험 관리** - [최소 권한 액세스](/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models) 및 Just-In-Time 액세스 원칙을 적용하여 조직을 보호합니다. 권한 있는 역할에 영구적으로 할당하는 사용자 수를 최소화하고 권한 상승에 대한 승인 및 MFA를 적용하여 조직의 권한 있는 액세스와 관련된 보안 위험을 크게 줄일 수 있습니다. 또한 최소 권한 및 Just-In-Time 액세스를 적용하면 권한 있는 역할에 대한 액세스 기록을 보고 보안 문제가 발생할 때 해당 문제를 추적할 수 있습니다.

**규정 준수 및 거버넌스 처리** - PIM을 배포하면 상시적인 ID 거버넌스를 위한 환경이 조성됩니다. 권한 있는 ID의 Just-In-Time 권한 상승은 PIM이 조직의 권한 있는 액세스 활동을 추적하는 방법을 제공합니다. 또한 조직 내에서 영구적이고 자격이 있는 역할의 모든 할당에 대해 알림을 보고 수신할 수 있습니다. 액세스 검토를 통해 정기적으로 감사를 수행하여 불필요한 권한 있는 ID를 제거하고 조직이 가장 엄격한 ID, 액세스 및 보안 표준을 준수하도록 할 수 있습니다.

**비용 절감** - PIM을 올바르게 배포해 비효율성, 인적 오류 및 보안 문제를 제거하여 비용을 절감합니다. 순수한 결과는 복구하려면 비용이 많이 들고 어려운 권한 있는 ID와 연결된 사이버 범죄가 감소한다는 것입니다. 또한 PIM은 조직이 규정 및 표준을 준수하고자 할 때 액세스 정보 감사와 관련된 비용을 줄이는 데에도 도움이 됩니다.

자세한 내용은 [Azure AD Privileged Identity Management란?](pim-configure.md)을 참조하세요.

### <a name="licensing-requirements"></a>라이선싱 요구 사항

PIM을 사용하려면 디렉터리에 다음과 같은 유료 또는 평가판 라이선스 중 하나가 있어야 합니다.

- Azure AD Premium P2
- EMS(Enterprise Mobility + Security) E5
- Microsoft 365 M5

자세한 내용은 [PIM을 사용하기 위한 라이선스 요구 사항](subscription-requirements.md)을 참조하세요.

### <a name="key-pim-terminology"></a>주요 PIM 용어

| 용어 또는 개념 | 설명 |
| --- | --- |
| 적격 | 사용자가 역할을 사용하기 위해 하나 이상의 작업을 수행해야 하는 역할 할당입니다. 사용자가 역할에 대한 자격을 얻은 경우 권한 있는 작업을 수행해야 할 때 해당 역할을 활성화할 수 있음을 의미합니다. 영구 및 적격 역할 할당을 비교했을 때 이 둘을 통해 다른 사람에게 주어진 액세스에는 차이가 없습니다. 유일한 차이는 사람들이 그 액세스를 항상 필요로 하지 않는다는 점입니다. |
| 활성화 | 사용자가 자격이 있는 역할을 사용하기 위해 하나 이상의 작업을 수행하는 프로세스입니다. 작업은 MFA(Multi-Factor Authentication) 검사를 수행하고, 비즈니스 근거를 제공하거나 지정된 승인자의 승인을 요청하는 과정을 포함할 수 있습니다. |
| JIT(Just-In-Time) 액세스 | 사용자가 권한 있는 작업을 수행하기 위해 임시 사용 권한을 받는 모델이며, 악의적 또는 권한이 없는 사용자가 권한이 만료된 후 액세스 권한을 획득하는 것을 방지합니다. 액세스 권한은 사용자에게 필요한 경우에만 부여됩니다. |
| 최소 권한 액세스 원칙 | 모든 사용자에게 수행할 권한이 있는 작업을 수행하는 데 필요한 최소 권한만 제공하는 권장 보안 방법입니다. 이 방법은 글로벌 관리자 수를 최소화하고, 그 대신 특정 시나리오에 대해 구체적 관리자 역할을 사용합니다. |

자세한 내용은 [용어](pim-configure.md#terminology)를 참조하세요.

### <a name="high-level-overview-of-how-pim-works"></a>PIM 작동 원리에 대한 고급 개요

1. PIM은 사용자가 권한 있는 역할에 대한 자격을 갖도록 설정됩니다.
1. 자격이 있는 사용자가 자신의 권한 있는 역할을 사용해야 하는 경우 PIM에서 해당 역할을 활성화합니다.
1. 역할에 대해 구성된 PIM 설정에 따라 사용자는 특정 단계(예: 다단계 인증 수행, 승인 받기 또는 이유 명시)를 완료해야 합니다.
1. 사용자가 해당 역할을 성공적으로 활성화하면 미리 구성된 기간 동안 해당 역할을 획득합니다.
1. 관리자는 감사 로그에서 모든 PIM 활동의 기록을 볼 수 있습니다. 또한 액세스 검토 및 경고와 같은 PIM 기능을 사용하여 자신의 테넌트를 추가로 보호하고 규정을 준수할 수 있습니다.

자세한 내용은 [Azure AD Privileged Identity Management란?](pim-configure.md)을 참조하세요.

### <a name="roles-that-can-be-managed-by-pim"></a>PIM에 의해 관리할 수 있는 역할

**Azure AD 역할** – Azure Active Directory 내의 모든 디렉터리 역할입니다(예: 글로벌 관리자, Exchange 관리자 및 보안 관리자). [Azure Active Directory의 관리자 역할 사용 권한](../users-groups-roles/directory-assign-admin-roles.md)에서 역할 및 해당 역할의 기능에 대해 더 자세히 읽을 수 있습니다. 관리자에게 할당하는 역할을 결정하는 데 도움이 필요한 경우 [작업별 최소 권한 있는 역할](../users-groups-roles/roles-delegate-by-task.md)을 참조하세요.

**Azure 리소스 역할** – 이 역할은 Azure 리소스, 리소스 그룹, 구독 또는 관리 그룹에 연결됩니다. PIM은 소유자, 사용자 액세스 관리자 및 Contributor는 물론 [사용자 지정 역할](../../role-based-access-control/custom-roles.md)에 대해서도 Just-In-Time 액세스를 제공합니다. Azure 리소스 역할에 대한 자세한 내용은 [RBAC(역할 기반 액세스 제어)](../../role-based-access-control/overview.md)를 참조하세요.

자세한 내용은 [PIM에서 관리할 수 없는 역할](pim-roles.md)을 참조하세요.

## <a name="step-2-plan-your-deployment"></a>2단계. 배포 계획

이 섹션에서는 조직에서 PIM을 배포하기 전에 수행해야 하는 작업에 중점을 둡니다. 지침 및 이 섹션의 개념은 조직의 권한 있는 ID에 맞게 조정된 최선의 계획을 만들도록 안내하므로, 해당 지침을 따르고 이 섹션의 개념을 이해해야 합니다.

### <a name="identify-your-stakeholders"></a>관련자 식별

다음 섹션은 프로젝트에 참여하여 로그오프하거나 검토하거나 계속 알림을 받아야 하는 모든 관련자를 식별하는 데 도움이 됩니다. 또한 Azure AD 역할에 대한 PIM 및 Azure 리소스 역할에 대한 PIM을 배포하기 위한 별도의 테이블을 포함합니다. 조직을 위해 적절한 경우 관련자를 다음 테이블에 추가합니다.

- SO = 이 프로젝트에서 로그오프
- R =이 프로젝트를 검토 및 입력 제공
- I = 이 프로젝트에 대한 알림을 받음

#### <a name="stakeholders-pim-for-azure-ad-roles"></a>관련자: Azure AD 역할에 대한 PIM

| 이름 | 역할 | 조치 |
| --- | --- | --- |
| 이름 및 이메일 | **설계자 또는 Azure 글로벌 관리자 식별**<br/>이 변경을 조직의 핵심 ID 관리 인프라와 일치시키는 방법을 정의할 책임이 있는 ID 관리 팀 출신 담당자입니다. | SO/R/I |
| 이름 및 이메일 | **서비스 소유자 / 라인 관리자**<br/>서비스 또는 서비스 그룹의 IT 소유자 출신 담당자입니다. 자신의 팀에 대해 의사 결정을 하고 PIM을 실행하도록 도와 주는 핵심 역할을 합니다. | SO/R/I |
| 이름 및 이메일 | **보안 소유자**<br/>계획이 조직의 보안 요구 사항을 만족하도록 로그오프할 수 있는 보안 팀 출신 담당자입니다. | SO/R |
| 이름 및 이메일 | **IT 지원 관리자 / 고객 지원팀**<br/>고객 지원팀 관점에서 이 변경의 지원 가능성에 대한 입력을 제공할 수 있는 IT 지원 조직 출신 담당자입니다. | R/I |
| 파일럿 사용자에 대한 이름 및 이메일 | **권한 있는 역할 사용자**<br/>권한 있는 ID 관리를 구현하는 사용자 그룹입니다. 이 그룹은 PIM이 구현된 후 자신의 역할을 활성화하는 방법을 알고 있어야 합니다. | I |

#### <a name="stakeholders-pim-for-azure-resource-roles"></a>관련자: Azure 리소스 역할에 대한 PIM

| 이름 | 역할 | 조치 |
| --- | --- | --- |
| 이름 및 이메일 | **구독 / 리소스 소유자**<br/>다음에 대한 PIM을 배포하려는 각 구독 또는 리소스의 IT 소유자 출신 담당자 | SO/R/I |
| 이름 및 이메일 | **보안 소유자**<br/>계획이 조직의 보안 요구 사항을 만족하도록 로그오프할 수 있는 보안 팀 출신 담당자입니다. | SO/R |
| 이름 및 이메일 | **IT 지원 관리자 / 고객 지원팀**<br/>고객 지원팀 관점에서 이 변경의 지원 가능성에 대한 입력을 제공할 수 있는 IT 지원 조직 출신 담당자입니다. | R/I |
| 파일럿 사용자에 대한 이름 및 이메일 | **RBAC 역할 사용자**<br/>권한 있는 ID 관리를 구현하는 사용자 그룹입니다. 이 그룹은 PIM이 구현된 후 자신의 역할을 활성화하는 방법을 알고 있어야 합니다. | I |

### <a name="enable-pim"></a>PIM 사용

계획 프로세스의 일부로 먼저 당사의 [PIM 문서 사용 시작](pim-getting-started.md)에 따라 PIM에 동의하고 PIM을 사용하도록 설정해야 합니다. PIM을 사용하도록 설정하면 배포를 구체적으로 도와 주도록 설계된 몇 가지 기능에 액세스할 수 있습니다.

Azure 리소스에 대한 PIM을 배포하려는 경우 당사의 [PIM 문서에서 관리할 Azure 리소스 검색](pim-resource-roles-discover-resources.md)을 따르는 것이 좋습니다. 각 리소스, 리소스 그룹 및 구독 소유자만이 PIM 내에서 해당 리소스를 검색할 수 있습니다. 전역 관리자 인 경우 Azure 리소스에 대 한 PIM을 배포 하는 동안, 할 수 있습니다 [모든 Azure 구독을 관리 하는 액세스 권한 상승](../../role-based-access-control/elevate-access-global-admin.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json) 직접 액세스 권한을 제공 하는 디렉터리의 모든 Azure 리소스 검색에 대 한 합니다. 그러나 PIM을 사용하여 리소스를 관리하기 전에 각 구독 소유자의 승인을 받는 것이 좋습니다.

### <a name="enforce-principle-of-least-privilege"></a>최소 권한 원칙 적용

조직에서 Azure AD 역할 및 Azure 리소스 역할에 대해 모두 최소 권한 원칙을 적용했는지 확인해야 합니다.

#### <a name="azure-ad-roles"></a>Azure AD 역할

Azure AD 역할의 경우 대부분의 관리자가 한두 가지 특정 관리자 역할만 필요할 때 조직이 많은 수의 관리자에게 글로벌 관리자 역할을 할당하는 것이 일반적입니다. 또한 권한 있는 역할 할당이 관리되지 않은 채로 방치되는 경향이 있습니다.

> [!NOTE]
> 역할 위임의 일반적인 문제:
>
> - Exchange 담당 관리자가 일상적인 작업을 수행하기 위해 Exchange 관리자 역할만 필요한데도 해당 관리자에게 글로벌 관리자 역할을 부여합니다.
> - Office 관리자에게 보안 및 SharePoint 관리자 역할이 모두 필요하기 때문에 해당 관리자에게 글로벌 관리자 역할을 할당하고 한 가지 역할만 위임하는 것이 더 쉽습니다.
> - 오래 전에 작업을 수행하기 위해 관리자에게 보안 관리자 역할을 할당했지만 해당 역할을 제거하지 않았습니다.

Azure AD 역할에 대해 최소 권한 원칙을 적용하려면 아래 단계를 수행합니다.

1. [사용할 수 있는 Azure AD 관리자 역할](../users-groups-roles/directory-assign-admin-roles.md#available-roles)을 읽고 이해하여 역할의 세분성을 알아 두세요. 또한 자신과 자신의 팀이 특정 작업에 대한 최소 권한 있는 역할을 설명하는 [Azure AD의 ID 작업별 관리자 역할](../users-groups-roles/roles-delegate-by-task.md)도 참조하는 것이 좋습니다.

1. 조직에서 권한 있는 역할을 가진 담당자를 나열합니다. [PIM 마법사](pim-security-wizard.md#run-the-wizard)를 사용하여 다음과 같은 페이지에 도달할 수 있습니다.

    ![권한 있는 역할 검색](./media/pim-deployment-plan/discover-privileged-roles-users.png)

1. 조직의 모든 글로벌 관리자에 대해 역할이 필요한 이유를 확인합니다. 이전 문서 읽기를 바탕으로 하나 이상의 세분된 관리자 역할에 의해 개인의 작업을 수행할 수 있는 경우 글로벌 관리자 역할에서 해당 역할을 제거하고 Azure Active Directory 내에서 그에 따라 할당하는 것이 좋습니다(참고: Microsoft에는 현재 글로벌 관리자 역할을 가진 관리자가 약 10명만 있습니다. [Microsoft가 PIM을 사용하는 방법](https://www.microsoft.com/itshowcase/Article/Content/887/Using-Azure-AD-Privileged-Identity-Management-for-elevated-access)에서 더 자세히 알아보세요).

1. 모든 다른 Azure AD 역할에 대해 할당 목록을 검토하고 더 이상 역할이 필요 없는 관리자를 식별하여 해당 관리자의 역할을 제거합니다.

3단계 및 4단계를 자동화하려면 PIM 내의 액세스 검토 기능을 이용할 수 있습니다. [PIM에서 Azure AD 역할에 대한 액세스 검토 시작](pim-how-to-start-security-review.md)의 단계에 따라 하나 이상의 멤버를 포함하는 모든 Azure AD 역할에 대한 액세스 검토를 설정할 수 있습니다.

![액세스 검토 만들기](./media/pim-deployment-plan/create-access-review.png)

검토자를 **멤버(자신)** 로 설정하는 것이 좋습니다. 이렇게 하면 역할의 모든 멤버에게 이메일을 보내서 해당 멤버가 액세스 권한이 필요한지 여부를 확인하도록 합니다. 또한 사용자가 역할이 필요한 이유를 설명할 수 있도록 고급 설정에서 **승인 시 이유 요구**를 설정하는 것이 좋습니다. 이 정보에 따라 불필요한 역할에서 사용자를 제거하고 글로벌 관리자의 경우 더 세분된 관리자 역할을 위임할 수 있습니다.

액세스 검토는 이메일을 이용하여 해당 사용자에게 역할에 대한 자신의 액세스 권한을 검토하라고 알립니다. 이메일이 연결되지 않은 권한 있는 계정이 있는 경우 해당 계정의 보조 이메일 필드에 내용을 입력해야 합니다. 자세한 내용은 [Azure AD의 proxyAddresses 특성](https://support.microsoft.com/help/3190357/how-the-proxyaddresses-attribute-is-populated-in-azure-ad)을 참조하세요.

#### <a name="azure-resource-roles"></a>Azure 리소스 역할

Azure 구독 및 리소스에 대해 각 구독 또는 리소스의 역할을 검토하도록 유사한 액세스 리뷰 프로세스를 설정할 수 있습니다. 이 프로세스의 목적은 각 구독 또는 리소스에 연결된 소유자 및 사용자 액세스 관리자 할당을 최소화하고 불필요한 할당을 제거하는 것입니다. 그러나 조직은 흔히 각 구독 또는 리소스의 소유자가 특정 역할(특히 사용자 지정 역할)을 더 잘 알고 있다는 이유로 소유자에게 해당 작업을 위임합니다.

전역 관리자 역할을 사용 하 여 IT 관리자 인 경우 조직에서 Azure 리소스에 대 한 PIM을 배포 하는 동안, 할 수 있습니다 [모든 Azure 구독을 관리 하는 액세스 권한 상승](../../role-based-access-control/elevate-access-global-admin.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json) 각 구독에 대 한 액세스를 가져오려고 합니다. 그런 다음, 각 구독 소유자를 찾고 해당 소유자와 협력하여 불필요한 할당을 제거하고 소유자 역할 할당을 최소화할 수 있습니다.

또한 Azure 구독에 대한 소유자 역할을 가진 사용자는 Azure AD 역할에 대해 앞에서 설명한 프로세스와 유사하게 [Azure 리소스에 대한 액세스 검토](pim-resource-roles-start-access-review.md)를 이용하여 감사를 수행하고 불필요한 역할 할당을 제거할 수 있습니다.

### <a name="decide-which-role-assignments-should-be-protected-by-pim"></a>PIM이 보호해야 하는 역할 할당 결정

조직에서 권한 있는 역할 할당을 정리한 후 PIM을 사용하여 보호할 역할을 결정해야 합니다.

역할이 PIM에 의해 보호되는 경우 역할에 할당할 자격이 있는 사용자는 해당 역할에서 부여하는 권한을 사용하도록 권한을 상승해야 합니다. 또한 권한 상승 프로세스는 승인 획득, 다단계 인증 수행 및/또는 활성화해야 하는 이유 제공을 포함할 수도 있습니다. 또한 PIM은 알림, PIM 및 Azure AD 감사 이벤트 로그를 통해 권한 상승을 추적할 수 있습니다.

PIM을 사용하여 보호할 역할을 선택하는 것은 어려울 수 있으며 각 조직마다 다릅니다. 이 섹션에서는 Azure AD 및 Azure 리소스 역할에 대한 당사의 모범 사례 조언을 제공합니다.

#### <a name="azure-ad-roles"></a>Azure AD 역할

가장 많은 수의 사용 권한을 가진 Azure AD 역할을 보호하는 우선 순위를 지정해야 합니다. 모든 PIM 고객의 사용 패턴을 기반으로, PIM에 의해 관리되는 상위 10개의 Azure AD 역할은 다음과 같습니다.

1. 전역 관리자
1. 보안 관리자
1. 사용자 관리자
1. Exchange 관리자
1. SharePoint 관리자
1. Intune 관리자
1. 보안 판독기
1. 서비스 관리자
1. 대금 청구 관리자
1. 비즈니스용 Skype 관리자

> [!TIP]
> :heavy_check_mark: **Microsoft 권장 사항**은 글로벌 관리자와 보안 관리자가 손상되면 가장 해로울 수 있는 경우 첫 단계로 PIM을 사용하여 모든 해당 관리자를 관리하는 것입니다.

조직에 가장 중요한 데이터 및 사용 권한을 고려해야 합니다. 예를 들어 일부 조직은 조직의 Power BI 관리자 역할 또는 조직의 팀 관리자 역할이 데이터에 액세스하거나 핵심 워크플로를 변경할 수 있는 경우 PIM을 사용하여 해당 역할을 보호하는 것이 좋습니다.

게스트 사용자가 할당된 역할이 있는 경우 특히 공격에 취약합니다.

> [!TIP]
> :heavy_check_mark: **Microsoft 권장 사항**은 PIM을 사용해 게스트 사용자를 포함하는 모든 역할을 관리하여 손상된 게스트 사용자 계정과 연결된 위험을 줄이는 것입니다.

디렉터리 독자, 메시지 센터 독자 및 보안 독자와 같은 독자 역할은 쓰기 사용 권한이 없기 때문에 간혹 다른 역할에 비해 덜 중요한 것으로 여겨지는 경우가 있습니다. 그러나 이러한 계정에 대한 액세스 권한을 획득한 공격자가 PII(개인 식별 정보)와 같은 중요 데이터를 읽을 수 있기 때문에 이러한 역할을 보호하는 고객도 있습니다. PIM을 사용하여 조직의 독자 역할을 관리해야 할지 여부를 결정할 때 이 점을 고려해야 합니다.

#### <a name="azure-resource-roles"></a>Azure 리소스 역할

Azure 리소스에 대해 PIM을 사용하여 관리해야 하는 역할 할당을 결정하는 경우 먼저 조직에 가장 중요한 구독/리소스를 식별해야 합니다. 이러한 구독/역할의 예:

- 가장 중요한 데이터를 호스팅하는 리소스
- 핵심적이고 고객과 마주하는 애플리케이션이 의존하는 리소스

가장 중요한 구독/리소스를 결정하는 데 어려움이 있는 글로벌 관리자의 경우 조직의 구독 소유자에게 문의하여 각 구독에 의해 관리되는 리소스의 목록을 수집하는 것이 좋습니다. 그런 다음, 구독 소유자와 협력하여 손상될 경우 심각도 수준(낮음, 보통, 높음)에 따라 리소스를 그룹화하는 것이 좋습니다. 이 심각도 수준에 따라 PIM을 사용하여 리소스 관리의 우선 순위를 지정하는 것이 좋습니다.

> [!TIP]
> :heavy_check_mark: **Microsoft 권장 사항**은 중요 서비스의 구독/리소스 소유자와 협력하여 중요한 구독/리소스 내의 모든 역할에 대해 PIM 워크플로를 설정하는 것입니다.

Azure 리소스에 대한 PIM은 시간 제한 서비스 계정을 지원합니다. 서비스 계정을 일반적인 사용자 계정을 처리하는 방법과 동일한 방법으로 처리하는 것이 좋습니다.

중요하지 않은 구독/리소스의 경우 모든 역할에 대해 PIM을 설정할 필요가 없습니다. 그러나 여전히 PIM을 사용하여 소유자 및 사용자 액세스 관리자 역할을 보호하는 것이 좋습니다.

> [!TIP]
> :heavy_check_mark: **Microsoft 권장 사항**은 PIM을 사용하여 모든 구독/리소스의 소유자 역할 및 사용자 액세스 관리자 역할을 관리하는 것입니다.

### <a name="decide-which-role-assignments-should-be-permanent-or-eligible"></a>영구적이거나 자격이 있어야 하는 역할 할당 결정

PIM에 의해 관리할 역할의 목록을 결정한 후 자격이 있는 역할을 획득해야 하는 사용자 및 영구적으로 활성화된 역할을 획득해야 하는 사용자를 결정해야 합니다. 영구적으로 활성화된 역할은 Azure Active Directory 및 Azure 리소스를 통해 할당된 일반적인 역할인 반면에 자격이 있는 역할은 PIM에서만 할당할 수 있습니다.

> [!TIP]
> :heavy_check_mark: **Microsoft 권장 사항**은 권장되는 [두 개의 비상 긴급 액세스 계정](../users-groups-roles/directory-emergency-access.md)(영구적 글로벌 관리자 역할을 가져야 함) 이외의 Azure AD 역할 및 Azure 리소스 역할에 대해 모두 영구적으로 활성화된 할당을 하지 않는 것입니다.

현재 관리자가 없도록 하는 것이 좋지만 조직이 지금 바로 이렇게 하는 것은 어려운 경우가 있습니다. 다음은 이 결정을 내릴 때 고려해야 하는 사항입니다.

- 권한 상승 빈도 – 사용자에게 권한 있는 할당 역할이 한 번만 필요한 경우 해당 사용자에게 영구적 할당을 하지 않는 것이 좋습니다. 반면에 사용자가 일상적인 작업을 위해 역할이 필요하고 PIM 사용으로 해당 사용자의 생산성이 크게 감소할 수 있는 경우 해당 사용자에 대해 영구적 역할을 고려할 수 있습니다.
- 조직에 고유한 사례 - 자격이 있는 역할을 부여한 개인이 아주 먼 거리의 팀 출신이거나 통신 및 권한 상승 프로세스 적용이 어려운 지점에 대한 고위 경영진인 경우 영구적 역할의 할당을 고려할 수 있습니다.

> [!TIP]
> :heavy_check_mark: **Microsoft 권장 사항**은 영구적 역할 할당(해당 역할이 있다면)을 가진 사용자에 대해 정기 액세스 검토를 설정하는 것입니다. 이 배포 계획의 마지막 섹션에서 정기 액세스 검토에 대해 자세히 알아보기

### <a name="draft-your-pim-settings"></a>PIM 설정 초안 작성

PIM 솔루션을 구현하기 전에 조직에서 사용하는 모든 권한 있는 역할에 대한 PIM 설정 초안을 작성하는 것이 모범 사례입니다. 이 섹션에서는 특정 역할에 대한 PIM 설정의 몇 가지 예를 보여 줍니다(해당 설정은 참고용일 뿐이며 조직마다 다를 수 있음). 이러한 각 설정을 테이블 뒤의 Microsoft 권장 사항을 통해 자세히 설명합니다.

#### <a name="pim-settings-for-azure-ad-roles"></a>Azure AD 역할에 대한 PIM 설정

| 역할 | MFA 요구 | 알림 | 인시던트 티켓 | 승인 필요 | 승인자 | 활성화 기간 | 영구 관리자 |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| 전역 관리자 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | 기타 글로벌 관리자 | 1시간 | 응급 액세스 계정 |
| Exchange 관리자 | :heavy_check_mark: | :heavy_check_mark: | :x: | :x: | 없음 | 2시간 | 없음 |
| 기술 지원팀 관리자 | :x: | :x: | :heavy_check_mark: | :x: | 없음 | 8시간 | 없음 |

#### <a name="pim-settings-for-azure-resource-roles"></a>Azure 리소스 역할에 대한 PIM 설정

| 역할 | MFA 요구 | 알림 | 승인 필요 | 승인자 | 활성화 기간 | 활성 관리자 | 활성화 만료 | 자격 만료 |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| 중요한 구독 소유자 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | 구독의 기타 소유자 | 1시간 | 없음 | 해당 없음 | 3개월 |
| 덜 중요한 구독의 사용자 액세스 관리자 | :heavy_check_mark: | :heavy_check_mark: | :x: | 없음 | 1시간 | 없음 | 해당 없음 | 3개월 |
| 가상 머신 참가자 | :x: | :heavy_check_mark: | :x: | 없음 | 3시간 | 없음 | 해당 없음 | 6개월 |

다음 테이블에서 각 설정을 설명합니다.

| 설정 | 설명 |
| --- | --- |
| 역할 | 설정을 정의하는 역할의 이름입니다. |
| MFA 요구 | 자격이 있는 사용자가 역할을 활성화하기 전에 MFA를 수행해야 하는지 여부입니다.<br/><br/> :heavy_check_mark: **Microsoft 권장 사항**은 특히 역할이 게스트 사용자를 포함하는 경우 모든 관리자 역할에 대해 MFA를 적용하는 것입니다. |
| 알림 | True로 설정하는 경우 자격이 있는 사용자가 해당 역할을 활성화하면 글로벌 관리자, 권한 있는 역할 관리자 및 조직의 보안 관리자가 이메일 알림을 받습니다.<br/><br/>**참고:** 일부 조직에서는 조직의 관리자 계정에 연결된 이메일 주소를 갖고 있지 않으므로, 이메일 알림을 받으려면 관리자가 해당 이메일을 받도록 대체 이메일 주소를 설정하는 것이 좋습니다. |
| 인시던트 티켓 | 자격이 있는 사용자가 자신의 역할을 활성화할 때 인시던트 티켓 번호를 기록해야 하는지 여부입니다. 이 설정은 조직이 원치 않는 활성화를 완화하기 위해 내부 문제 번호를 통해 각 활성화를 식별하는 데 도움이 됩니다.<br/><br/> :heavy_check_mark: **Microsoft 권장 사항**은 인시던트 티켓 번호를 이용하여 PIM을 내부 시스템과 연결하는 것입니다. 이 방법은 활성화에 대한 컨텍스트가 필요한 승인자에게 특히 유용합니다. |
| 승인 필요 | 자격이 있는 사용자가 역할을 활성화하기 위해 승인을 받아야 하는지 여부입니다.<br/><br/> :heavy_check_mark: **Microsoft 권장 사항**은 가장 많은 사용 권한을 가진 역할에 대해 승인을 설정하는 것입니다. 모든 PIM 고객의 사용 패턴에 따라 글로벌 관리자, 사용자 관리자, Exchange 관리자, 보안 관리자 및 암호 관리자가 승인 설정을 포함하는 가장 일반적인 역할입니다. |
| 승인자 | 자격이 있는 역할을 활성화하기 위해 승인이 필요한 경우 요청을 승인해야 하는 개인을 나열합니다. 기본적으로 PIM은 권한 있는 역할 관리자인 모든 사용자(영구적인지 또는 자격이 있는지 여부와 관계 없이)에 대해 승인자를 설정합니다.<br/><br/>**참고:** 사용자가 Azure AD 역할과 역할 승인자에 대해 모두 자격이 있는 경우 자기 자신을 승인할 수 없습니다.<br/><br/> :heavy_check_mark: **Microsoft 권장 사항**은 글로벌 관리자보다는 특정 역할에 관하여 가장 잘 알고 해당 역할이 자주 필요한 개인을 승인자로 선택하는 것입니다. |
| 활성화 기간 | 역할이 만료되기 전에 사용자가 해당 역할에서 활성화되는 기간입니다. |
| 영구 관리자 | 역할에 대한 영구 관리자인(활성화할 필요가 없는) 사용자의 목록입니다.<br/><br/> :heavy_check_mark: **Microsoft 권장 사항**은 글로벌 관리자를 제외한 모든 역할에 대해 현재 관리자를 할당하지 않는 것입니다. 자격이 있도록 만들어야 하는 개인과 이 계획의 영구 활성화 섹션이어야 하는 개인의 경우 해당 역할에 대해 더 자세히 읽으세요. |
| 활성 관리자 | Azure 리소스의 경우 활성 관리자는 역할을 사용하기 위해 활성화할 필요가 없는 사용자의 목록입니다. 이 관리자는 이 역할을 상실하는 만료 시간을 설정할 수 있으므로 Azure AD 역할과 같이 영구 관리자라고 하지 않습니다. |
| 활성화 만료 | Azure 리소스 역할에 대한 활성 역할 할당은 이 구성된 기간이 경과한 후 만료됩니다. 15일, 1개월, 3개월, 6개월, 1년 또는 영구 활성 중에서 선택할 수 있습니다. |
| 자격 만료 | Azure 리소스 역할에 대해 자격이 있는 역할 할당은 이 구성된 기간이 경과한 후 만료됩니다. 15일, 1개월, 3개월, 6개월, 1년 또는 영구적으로 자격이 있음 중에서 선택할 수 있습니다. |

## <a name="step-3-implement-your-solution"></a>3단계. 솔루션 구현

적절한 계획의 기초는 Azure Active Directory를 사용하여 애플리케이션을 성공적으로 배포할 수 있는 기준입니다.  해당 기준은 온보딩을 단순화하면서도 성공적인 배포에 필요한 시간을 줄이는 지능적인 보안 및 통합을 제공합니다.  이 조합을 통해 애플리케이션을 쉽게 통합하면서도 최종 사용자에 대한 중단 시간을 완화할 수 있습니다.

### <a name="identify-test-users"></a>테스트 사용자 식별

이 섹션을 사용하여 구현 유효성을 검사할 사용자 세트 및/또는 사용자 그룹을 식별합니다. 계획 섹션에서 선택한 설정에 따라 각 역할에 대해 테스트할 사용자를 식별합니다.

> [!TIP]
> :heavy_check_mark: **Microsoft 권장 사항**은 각 Azure AD 역할의 서비스 소유자가 프로세스를 숙지하고 롤아웃을 위한 내부 옹호자가 될 수 있도록 해당 소유자를 테스트 사용자로 만드는 것입니다.

이 테이블에서 각 역할에 대한 설정이 적절한지 확인할 테스트 사용자를 식별하세요.

| 역할 이름 | 테스트 사용자 |
| --- | --- |
| &lt;역할 이름&gt; | &lt;역할을 테스트할 사용자&gt; |
| &lt;역할 이름&gt; | &lt;역할을 테스트할 사용자&gt; |

### <a name="test-implementation"></a>테스트 구현

테스트 사용자를 식별했으므로 이제 이 단계를 사용하여 테스트 사용자에 대한 PIM을 구성합니다. 조직이 Azure Portal 내부의 PIM 사용자 인터페이스를 사용하는 대신에 PIM 워크플로를 자신의 고유한 내부 애플리케이션에 통합하려는 경우 PIM의 모든 작업은 당사의 [그래프 API](https://docs.microsoft.com/graph/api/resources/privilegedidentitymanagement-root)를 통해서도 지원됩니다.

#### <a name="configure-pim-for-azure-ad-roles"></a>Azure AD 역할에 대한 PIM 구성

1. 계획한 내용에 따라 [Azure AD 디렉터리 역할 설정을 구성](pim-how-to-change-default-settings.md)합니다.

1. **Azure AD 역할**로 이동하고 **역할**을 클릭한 다음, 방금 구성한 역할을 선택합니다.

1. 테스트 사용자 그룹의 경우 해당 사용자가 이미 영구 관리자이면 사용자를 검색하고 해당 행의 점 3개를 클릭하여 사용자를 영구에서 자격이 있음으로 변환하여 자격이 있도록 만들 수 있습니다. 아직 역할을 할당하지 않은 경우 [새 자격이 있는 할당을 만들](pim-how-to-add-role-to-user.md#make-a-user-eligible-for-a-role) 수 있습니다.

1. 테스트하려는 모든 역할에 대해 1-3단계를 반복합니다.

1. 테스트 사용자를 설정한 후 [자신의 Azure AD 역할을 활성화](pim-how-to-activate-role.md)하는 방법에 대한 링크를 해당 사용자에게 보내는 것이 좋습니다.

#### <a name="configure-pim-for-azure-resource-roles"></a>Azure 리소스 역할에 대한 PIM 구성

1. 테스트하려는 구독 또는 리소스 내의 역할에 대해 [Azure 리소스 역할 설정을 구성](pim-resource-roles-configure-role-settings.md)합니다.

1. 해당 구독에 대한 **Azure 리소스**로 이동하여 **역할**을 클릭하고 방금 구성한 역할을 선택합니다.

1. 테스트 사용자 그룹의 경우 해당 사용자가 이미 활성 관리자이면 사용자를 검색하여 자격이 있음으로 만들고 [해당 사용자의 역할 할당을 업데이트](pim-resource-roles-assign-roles.md#update-or-remove-an-existing-role-assignment)할 수 있습니다. 역할이 아직 없는 경우 [새 역할을 할당](pim-resource-roles-assign-roles.md#assign-a-role)할 수 있습니다.

1. 테스트하려는 모든 역할에 대해 1-3단계를 반복합니다.

1. 테스트 사용자를 설정한 후 [자신의 Azure 리소스 역할을 활성화](pim-resource-roles-activate-your-roles.md)하는 방법에 대한 링크를 해당 사용자에게 보내는 것이 좋습니다.

이 단계를 사용하여 역할에 대해 설정한 모든 구성이 올바르게 작동하는지 여부를 확인하는 것이 좋습니다. 다음 테이블을 사용하여 테스트를 문서화하세요. 또한 이 단계를 사용하여 영향을 받는 사용자와의 통신을 최적화하는 것이 좋습니다.

| 역할 | 활성화하는 동안 예상되는 동작 | 실제 결과 |
| --- | --- | --- |
| 전역 관리자 | (1) MFA 요구<br/>(2) 승인 요구<br/>(3) 승인자가 알림을 수신하고 승인할 수 있음<br/>(4) 미리 설정된 시간이 경과한 후 역할 만료 |  |
| 구독 *X*의 소유자 | (1) MFA 요구<br/>(2) 구성된 기간이 경과한 후 자격이 있는 할당 만료 |  |

### <a name="communicate-pim-to-affected-stakeholders"></a>영향을 받는 관련자에게 PIM 전달

PIM 배포는 권한 있는 역할의 사용자에 대한 추가 단계를 도입합니다. PIM은 권한 있는 ID와 연결된 보안 문제를 크게 줄이지만 테넌트 전체에 배포하기 전에 변경 내용을 실질적으로 전달해야 합니다. 영향을 받는 관리자 수에 따라 조직은 흔히 변경 내용에 관한 내부 문서, 비디오 또는 이메일을 만듭니다. 이러한 통신에 자주 포함되는 사항:

- PIM 정의
- 조직에 대한 혜택
- 영향을 받는 개인
- PIM을 롤아웃하는 시기
- 사용자가 자신의 역할을 활성화하기 위해 필요한 추가 단계
    - 당사의 설명서에 대한 링크를 보내는 것이 좋습니다.
    - [Azure AD 역할 활성화](pim-how-to-activate-role.md)
    - [Azure 리소스 역할 활성화](pim-resource-roles-activate-your-roles.md)
- PIM과 연결된 문제에 대한 연락처 정보 또는 고객 지원팀 링크

> [!TIP]
> :heavy_check_mark: **Microsoft 권장 사항**은 고객 지원팀/지원팀과 함께 PIM 워크플로를 자세히 살펴보는 시간을 설정하는 것입니다(조직에 내부 IT 지원팀이 있는 경우). 해당 팀에게 적절한 설명서 및 본인의 연락처 정보를 제공합니다.

### <a name="move-to-production"></a>프로덕션 환경으로 이동

테스트를 성공적으로 완료한 후 PIM 구성에서 정의한 각 역할의 모든 사용자에 대해 테스트 단계의 모든 단계를 반복하여 PIM을 프로덕션으로 이동합니다. Azure AD 역할에 대한 PIM의 경우 조직은 흔히 다른 역할에 대해 PIM을 테스트 및 롤아웃하기 전에 글로벌 관리자에 대한 PIM을 테스트 및 롤아웃합니다. 한편 Azure 리소스의 경우 조직은 일반적으로 한 번에 하나의 Azure 구독에 대해 PIM을 테스트 및 롤아웃합니다.

### <a name="in-the-case-a-rollback-is-needed"></a>롤백이 필요한 경우

PIM이 프로덕션 환경에서 원하는 대로 작동하지 않은 경우 다음 롤백 단계가 PIM을 설정하기 전에 알려진 양호한 상태로 되돌아가는 데 도움이 될 수 있습니다.

#### <a name="azure-ad-roles"></a>Azure AD 역할

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. **Azure AD Privileged Identity Management**를 엽니다.
1. **Azure AD 역할**을 클릭한 다음, **역할**을 클릭합니다.
1. 구성한 각 역할의 자격이 있는 할당을 가진 모든 사용자에 대해 줄임표(**...** )를 클릭합니다.
1. **영구 상태로 만들기** 옵션을 클릭하여 역할 할당을 영구 상태로 만듭니다.

#### <a name="azure-resource-roles"></a>Azure 리소스 역할

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. **Azure AD Privileged Identity Management**를 엽니다.
1. **Azure 리소스**를 클릭한 다음, 롤백하려는 구독 또는 리소스를 클릭합니다.
1. **역할**을 클릭합니다.
1. 구성한 각 역할의 자격이 있는 할당을 가진 모든 사용자에 대해 줄임표(**...** )를 클릭합니다.
1. **영구 상태로 만들기** 옵션을 클릭하여 역할 할당을 영구 상태로 만듭니다.

## <a name="step-4-next-steps-after-deploying-pim"></a>4단계. PIM을 배포한 후 다음 단계

프로덕션에서 PIM의 성공적인 배포는 조직의 권한 있는 ID 보호라는 점에서 중요한 다음 단계입니다. PIM 배포를 통해 보안 및 규정 준수를 위한 추가 PIM 기능을 사용하는 것이 좋습니다.

### <a name="use-pim-alerts-to-safeguard-your-privileged-access"></a>PIM 경고를 사용하여 권한 있는 액세스 보호

PIM에 기본 제공되는 경고 기능을 이용하여 테넌트 보호를 개선하는 것이 좋습니다. 자세한 내용은 [보안 경고](pim-how-to-configure-security-alerts.md#security-alerts)를 참조하세요. 이러한 경고는 관리자가 권한 있는 역할을 사용하지 않음, 역할이 PIM 외부에 할당됨, 역할이 너무 자주 활성화됨 등을 포함합니다. 조직을 완전하게 보호하기 위해 정기적으로 경고 목록을 검토하고 문제를 해결하는 것이 좋습니다. 다음 방법으로 경고를 확인하고 해결할 수 있습니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. **Azure AD Privileged Identity Management**를 엽니다.
1. **Azure AD 역할**을 클릭한 다음, **경고**를 클릭합니다.

> [!TIP]
> :heavy_check_mark: **Microsoft 권장 사항**은 심각도가 높음으로 표시된 모든 경고를 즉시 처리하는 것입니다. 심각도가 보통 및 낮음인 경고의 경우 계속 알리고 보안 위협이 있다고 여겨지는 경우 변경하는 것이 좋습니다.

특정 경고가 유용하지 않거나 조직에 적용되지 않는 경우 언제나 경고 페이지에서 경고를 해제할 수 있습니다. 이 해지를 나중에 언제라도 Azure AD 설정에서 되돌릴 수 있습니다.

### <a name="set-up-recurring-access-reviews-to-regularly-audit-your-organizations-privileged-identities"></a>정기적으로 조직의 권한 있는 ID를 감사하는 정기 액세스 검토 설정

액세스 검토는 권한 있는 역할이 할당된 사용자 또는 특정 검토자에 대해 각 사용자에게 권한 있는 ID가 필요한지 여부를 묻는 최선의 방법입니다. 액세스 검토는 공격 노출을 줄이고 규정 준수를 유지하려는 경우 유용합니다. 액세스 검토 설정에 대한 자세한 내용은 [Azure AD 역할 액세스 검토](pim-how-to-start-security-review.md) 및 [Azure 리소스 역할 액세스 검토](pim-resource-roles-start-access-review.md)를 참조하세요. 일부 조직의 경우 법과 규정 준수를 유지하기 위해 정기 액세스 검토를 수행해야 하는 반면에, 액세스 검토가 조직 전체에 걸쳐 최소 권한 원칙을 준수하는 최선의 방법인 경우도 있습니다.

> [!TIP]
> :heavy_check_mark: **Microsoft 권장 사항**은 모든 Azure AD 및 Azure 리소스 역할에 대해 분기별로 액세스 검토를 설정하는 것입니다.

대부분의 경우 Azure AD 역할에 대한 검토자는 자신이 사용자인 반면에, Azure 리소스 역할에 대한 검토자는 역할이 포함된 구독의 소유자입니다. 그러나 회사가 특정 개인의 이메일 주소와 연결되지 않은 권한 있는 계정을 가진 경우가 많습니다. 이러한 경우 아무도 액세스를 확인하거나 검토하지 않습니다.

> [!TIP]
> :heavy_check_mark: **Microsoft 권장 사항** - 정기적으로 확인하는 이메일 주소에 연결되지 않은 권한 있는 역할 할당을 가진 모든 계정에 대해 보조 이메일 주소 추가

### <a name="get-the-most-out-of-your-audit-log-to-improve-security-and-compliance"></a>보안 및 규정 준수를 개선하기 위해 감사 로그를 최대한 이용

감사 로그를 통해 규정에 대한 최신 정보를 유지하고 규정을 준수할 수 있습니다. PIM은 현재 모든 조직에 대한 30일 기록을 저장합니다. 감사 로그에 포함되는 내용:

- 자격이 있는 역할 활성화/비활성화
- PIM 내부 및 외부의 역할 할당 활동
- 역할 설정 변경 내용
- 승인 설정이 있는 역할 활성화 요청/승인/거부 활동
- 경고 업데이트

글로벌 관리자 또는 권한 있는 역할 권리자가 이 감사 로그에 액세스할 수 있습니다. 자세한 내용은 [Azure AD 역할에 대한 감사 기록](pim-how-to-use-audit-log.md) 및 [Azure 리소스 역할에 대한 감사 기록](azure-pim-resource-rbac.md)을 참조하세요.

> [!TIP]
> :heavy_check_mark: **Microsoft 권장 사항**은 적어도 한 명의 관리자가 매주 1회 이상 모든 감사 이벤트를 읽고 매월 1회 감사 이벤트를 내보내도록 하는 것입니다.

감사 이벤트를 더 오랜 기간 동안 자동으로 저장하려면 PIM의 감사 로그를 [Azure AD 감사 로그](../reports-monitoring/concept-audit-logs.md)에 자동으로 동기화합니다.

> [!TIP]
> :heavy_check_mark: **Microsoft 권장 사항**은 보안 및 규정 준수가 필요한 Azure 스토리지 계정에 감사 이벤트를 보관하는 [Azure 로그 모니터링](../reports-monitoring/concept-activity-logs-azure-monitor.md)을 설정하는 것입니다.
