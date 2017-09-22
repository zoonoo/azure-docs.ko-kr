---
title: "Security Center 플랫폼 마이그레이션 FAQ | Microsoft Docs"
description: "이 FAQ는 Azure Security Center 플랫폼 마이그레이션에 대한 질문에 답변합니다."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 4d1364cd-7847-425a-bb3a-722cb0779f78
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/14/2017
ms.author: terrylan
ms.translationtype: HT
ms.sourcegitcommit: d24c6777cc6922d5d0d9519e720962e1026b1096
ms.openlocfilehash: 4b88b5015fcf44e8979b8b1a3aa1eb26f0fbb704
ms.contentlocale: ko-kr
ms.lasthandoff: 09/15/2017

---
# <a name="security-center-platform-migration-faq"></a>Security Center 마이그레이션 FAQ
2017년 6월 초에 Azure Security Center는 Microsoft Monitoring Agent를 사용하여 데이터를 수집하고 저장합니다. 자세한 내용은 [Azure Security Center 플랫폼 마이그레이션](security-center-platform-migration.md)을 참조하세요. 이 FAQ는 플랫폼 마이그레이션에 대한 질문에 답변합니다.

## <a name="data-collection-agents-and-workspaces"></a>데이터 수집, 에이전트 및 작업 영역

### <a name="how-is-data-collected"></a>데이터를 어떻게 수집해야 하나요?
Security Center는 Microsoft Monitoring Agent를 사용하여 VM에서 보안 데이터를 수집했습니다. 이 보안 데이터는 취약점 및 보안 이벤트를 식별하는 데 사용되는 보안 구성에 대한 정보를 포함하며 위협을 검색하는 데 사용됩니다. 에이전트에 의해 수집된 데이터는 VM에 연결된 기존 Log Analytics 작업 영역 또는 Security Center에서 만든 새 작업 영역 중 하나에 저장됩니다. Security Center가 새 작업 영역을 만들 때 VM의 지리적 위치가 고려됩니다.

> [!NOTE]
> Microsoft Monitoring Agent는 OMS(Operations Management Suite), Log Analytics 서비스 및 SCOM(System Center Operations Manager)에서 사용되는 것과 동일한 에이전트입니다.
>
>

처음으로 데이터 수집을 활성화하거나 구독을 마이그레이션하는 경우 Security Center는 각 VM에 대한 Azure 확장으로 Microsoft Monitoring Agent가 이미 설치되어 있는지 확인합니다. Microsoft Monitoring Agent가 설치되지 않은 경우 Security Center는 다음을 수행합니다.

- VM에 Microsoft Monitoring Agent 설치
   - Security Center에서 만든 작업 영역이 이미 VM과 동일한 지리적 위치에 있는 경우 에이전트는 이 작업 영역에 연결됩니다.
   - 작업 영역이 없는 경우 Security Center는 해당 지리적 위치에서 새 리소스 그룹 및 기본 작업 영역을 만들고 해당 작업 영역에 에이전트를 연결합니다. 작업 영역 및 리소스 그룹에 대한 명명 규칙은 다음과 같습니다.

       작업 영역: DefaultWorkspace-[subscription-ID]-[geo]

       리소스 그룹: DefaultResouceGroup-[geo]
- 작업 영역에서 Security Center 솔루션 설치

작업 영역의 위치는 VM의 위치를 기반으로 합니다. 자세한 내용은 [데이터 보안](security-center-data-security.md)을 참조하세요.

> [!NOTE]
> 플랫폼 마이그레이션 이전에 Security Center는 Azure Monitoring Agent를 사용하여 VM에서 보안 데이터를 수집하고 저장소 계정에 데이터를 저장했습니다. 플랫폼 마이그레이션 후에 Security Center는 Microsoft Monitoring Agent 및 작업 영역을 사용하여 동일한 데이터를 수집하고 저장합니다. 저장소 계정은 마이그레이션 후에 제거할 수 있습니다.
>
>

### <a name="am-i-billed-for-log-analytics-or-oms-on-the-workspaces-created-by-security-center"></a>Security Center에서 만든 작업 영역에서 Log Analytics 또는 OMS에 대한 요금을 청구하나요?
안 됩니다. Security Center에서 만든 작업 영역은 노드 요금 청구당 OMS에 구성되는 동안 OMS 요금이 청구되지 않습니다. Security Center 청구는 항상 작업 영역에 설치된 Security Center 보안 정책 및 솔루션에 기반합니다.

- **무료 계층** – Security Center는 기본 작업 영역에서 'SecurityCenterFree' 솔루션을 설치합니다. 체험 계층에 대한 요금이 청구되지 않습니다.
- **표준 계층** – Security Center는 기본 작업 영역에서 'SecurityCenterFree' 및 'Security' 솔루션을 설치합니다.

자세한 내용은 [Security Center 가격 책정](https://azure.microsoft.com/pricing/details/security-center/)을 참조하세요. 가격 책정 페이지 주소는 2017년 6월부터 보안 데이터 저장소 및 비례 배분 청구로 변경됩니다.

> [!NOTE]
> Security Center에서 만든 작업 영역의 OMS 가격 책정 계층은 Security Center 청구에 영향을 주지 않습니다.
>
>

### <a name="can-i-delete-the-default-workspaces-created-by-security-center"></a>Security Center에서 만든 기본 작업 영역을 삭제할 수 있나요?
**기본 작업 영역을 삭제하지 않는 것이 좋습니다.** Security Center는 기본 작업 영역을 사용하여 VM의 보안 데이터를 저장합니다.  작업 영역을 삭제하는 경우 Security Center는 이 데이터 및 일부 보안 권장 사항을 수집할 수 없고 경고를 사용할 수 없습니다.

복구하려면 삭제된 작업 영역에 연결된 VM에서 Microsoft Monitoring Agent를 제거합니다. Security Center는 에이전트를 다시 설치하고 새 기본 작업 영역을 만듭니다.

### <a name="how-can-i-use-my-existing-log-analytics-workspace"></a>기존 Log Analytics 작업 영역을 사용하려면 어떻게 해야 하나요?

기존 Log Analytics 작업 영역을 선택하여 Security Center에서 수집한 데이터를 저장할 수 있습니다. 기존 Log Analytics 작업 영역을 사용하려면 다음을 수행합니다.

- 작업 영역이 선택한 Azure 구독과 연결되어 있어야 합니다.
- 작업 영역에 액세스하려면 읽기 이상의 권한이 필요합니다.

기존 Log Analytics 작업 영역을 선택하려면 다음을 수행합니다.

1. **보안 정책 - 데이터 수집** 아래에서 **다른 작업 영역 사용**을 선택합니다.

   ![다른 작업 영역 사용][5]

2. 풀다운 메뉴에서 수집된 데이터를 저장할 작업 영역을 선택합니다.

   > [!NOTE]
   > 풀다운 메뉴에는 액세스 권한이 있으며 Azure 구독에 포함된 작업 영역만 표시됩니다.
   >
   >

3. **저장**을 선택합니다.
4. **저장**을 선택하고 나면 모니터링되는 VM을 다시 구성할지를 묻는 메시지가 표시됩니다.

   - 새 작업 영역 설정을 **새 VM에만 적용**하려면 **아니요**를 선택합니다. 그러면 새 작업 영역 설정이 새 Agent 설치(Microsoft Monitoring Agent가 설치되어 있지 않은 새로 검색된 VM)에만 적용됩니다.
   - 새 작업 영역 설정을 **모든 VM에 적용**하려면 **예**를 선택합니다. 이 경우에는 Security Center에서 만든 작업 영역에 연결된 모든 VM이 새 대상 작업 영역에 다시 연결됩니다.

   > [!NOTE]
   > 예를 선택하는 경우 모든 VM이 새 대상 작업 영역에 다시 연결될 때까지는 Security Center에서 작성된 작업 영역을 삭제하면 안 됩니다. 작업 영역을 너무 빨리 삭제하면 이 작업이 실패합니다.
   >
   >

   - 작업을 취소하려면 **취소**를 선택합니다.

      ![모니터링되는 VM 다시 구성][6]

### <a name="what-if-the-microsoft-monitoring-agent-was-already-installed-as-an-extension-on-the-vm"></a>Microsoft Monitoring Agent가 VM에 확장으로 이미 설치되어 있으면 어떻게 되나요?
Security Center는 사용자 작업 영역에 대한 기존 연결을 재정의하지 않습니다. Security Center는 이미 연결된 작업 영역에서 VM의 보안 데이터를 저장합니다.

### <a name="what-if-i-had-a-microsoft-monitoring-agent-installed-on-the-machine-but-not-as-an-extension"></a>컴퓨터에 확장판이 아닌 Microsoft Monitoring Agent를 설치하면 어떻게 되나요?
Microsoft Monitoring Agent를 Azure 확장으로 설치하는 것이 아니라 VM에 직접 설치하는 경우 Security Center는 Microsoft Monitoring Agent를 설치하지 않으며, 보안 모니터링 기능이 제한됩니다.

### <a name="what-is-the-impact-of-removing-these-extensions"></a>이러한 확장을 제거할 경우 어떤 영향이 있나요?
Microsoft Monitoring 확장을 제거하는 경우 Security Center는 VM의 보안 데이터 및 일부 보안 권장 사항을 수집할 수 없고 경고를 사용할 수 없습니다. 24시간 이내에 Security Center는 VM이 확장을 누락하고 확장을 다시 설치했는지 확인합니다.

### <a name="how-do-i-stop-the-automatic-agent-installation-and-workspace-creation"></a>자동 에이전트 설치 및 작업 영역 생성을 중지하려면 어떻게 할까요?
보안 정책에서 구독에 대해 자동 프로비저닝을 해제할 수는 있지만, 해제하지 않는 것이 좋습니다. 자동 프로비저닝을 해제하면 Security Center 권장 사항 및 경고 기능이 제한됩니다. 표준 가격 책정 계층의 구독에는 자동 프로비저닝을 반드시 사용해야 합니다. 자동 프로비저닝을 사용하지 않도록 설정하려면 다음을 수행합니다.

1. 표준 계층에 대한 구독이 구성된 경우 해당 구독에 대한 보안 정책을 열고 **체험** 계층을 선택합니다.

   ![가격 책정 계층 ][1]

2. 다음으로 **보안 정책 - 데이터 수집** 블레이드에서 **끄기**를 선택하여 자동 프로비저닝을 해제합니다.
   ![데이터 수집][2]

### <a name="how-do-i-remove-oms-extensions-installed-by-security-center"></a>Security Center에서 설치한 OMS 확장을 제거하려면 어떻게 할까요?
Microsoft Monitoring Agent를 수동으로 제거할 수 있습니다. Security Center 권장 사항 및 경고를 제한하기 때문에 권장되지는 않습니다.

> [!NOTE]
> 데이터 수집을 사용하는 경우 Security Center는 에이전트를 제거한 후에 다시 설치합니다.  수동으로 에이전트를 제거하기 전에 데이터 수집을 사용하지 않도록 설정해야 합니다. 데이터 수집을 사용하지 않도록 설정하는 지침은 [자동 에이전트 설치 및 작업 영역 생성을 중지하려면 어떻게 할까요?](#how-do-i-stop-the-automatic-agent-installation-and-workspace-creation?)를 참조하세요.
>
>

수동으로 에이전트를 제거하려면:

1.  포털에서 **Log Analytics**을 엽니다.
2.  Log Analytics 블레이드에서 작업 영역을 선택합니다.
3.  모니터링하지 않을 각 VM을 선택하고 **연결 끊기**를 선택합니다.

   ![에이전트 제거][3]

> [!NOTE]
> Linux VM에 이미 비확장 OMS 에이전트가 설치된 경우 확장명을 제거하면 에이전트도 제거되고 고객이 다시 설치해야 합니다.
>
>

## <a name="existing-oms-customers"></a>기존 OMS 고객

### <a name="does-security-center-override-any-existing-connections-between-vms-and-workspaces"></a>Security Center에서 VM과 작업 영역 간의 모든 기존 연결을 재정의하나요?
VM에 Azure 확장으로 Microsoft Monitoring Agent가 이미 설치되어 있으면 Security Center에서 기존 작업 영역 연결을 재정의하지 않습니다. 대신 Security Center에서 기존 작업 영역을 사용합니다.

Security Center 솔루션이 작업 영역에 없는 경우 설치되고 솔루션은 관련 VM에만 적용됩니다. 솔루션을 추가하면 기본적으로 Log Analytics 작업 영역에 연결된 모든 Windows 및 Linux 에이전트에 의해 배포됩니다. OMS 기능인 [솔루션 대상 지정](../operations-management-suite/operations-management-suite-solution-targeting.md)을 사용하면 솔루션에 범위를 적용할 수 있습니다.

Microsoft Monitoring Agent를 Azure 확장으로 설치하는 것이 아니라 VM에 직접 설치하는 경우 Security Center는 Microsoft Monitoring Agent를 설치하지 않으며, 보안 모니터링 기능이 제한됩니다.

### <a name="what-should-i-do-if-i-suspect-that-the-data-platform-migration-broke-the-connection-between-one-of-my-vms-and-my-workspace"></a>데이터 플랫폼 마이그레이션이 내 VM과 내 작업 영역 간에 연결을 끊는 것으로 의심되는 경우 어떻게 해야 할까요?
이런 상황은 일어나지 않습니다. 발생하는 경우 [Azure 지원을 요청](../azure-supportability/how-to-create-azure-support-request.md)하고 다음 세부 정보를 포함합니다.

- 영향을 받는 VM의 Azure 리소스 ID
- 연결이 끊기기 전에 확장에 구성된 작업 영역의 Azure 리소스 ID
- 이전에 설치된 에이전트 및 버전

### <a name="does-security-center-install-solutions-on-my-existing-oms-workspaces-what-are-the-billing-implications"></a>Security Center에서 기존 OMS 작업 영역에 솔루션을 설치하나요? 요금 청구에 영향을 주는 요인은 무엇인가요?
Security Center에서 VM이 만든 작업 영역에 이미 연결되어 있는지를 식별하는 경우 Security Center를 통해 가격 책정 계층에 따라 이 작업 영역에서 솔루션을 사용할 수 있습니다. 솔루션이 [솔루션 대상 지정](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-solution-targeting)을 통해 관련 Azure VM에만 적용되므로 청구는 동일하게 유지됩니다.

- **체험 계층** – Security Center는 작업 영역에서 'SecurityCenterFree' 솔루션을 설치합니다. 체험 계층에 대한 요금이 청구되지 않습니다.
- **표준 계층** – Security Center는 작업 영역에서 'SecurityCenterFree' 및 'Security' 솔루션을 설치합니다.

   ![기본 작업 영역의 솔루션][4]

> [!NOTE]
> Log Analytics에서 'Security' 솔루션은 OMS의 보안 및 감사 솔루션입니다.
>
>

### <a name="i-already-have-workspaces-in-my-environment-can-i-use-them-to-collect-security-data"></a>내 환경에 이미 작업 영역이 있는 경우 보안 데이터를 수집하는 데 사용할 수 있나요?
VM에 Azure 확장으로 Microsoft Monitoring Agent가 이미 설치되어 있으면 Security Center에서 기존 연결된 작업 영역 연결을 사용합니다. Security Center 솔루션이 작업 영역에 없는 경우 설치되고 솔루션은 [솔루션 대상 지정](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-solution-targeting)을 통해 관련 VM에만 적용됩니다.

Security Center에서 VM에 Microsoft Monitoring Agent를 설치하면 Security Center에서 만든 기본 작업 영역을 사용합니다. 곧 고객은 사용되는 작업 영역을 구성할 수 있게 됩니다.

### <a name="i-already-have-security-solution-on-my-workspaces-what-are-the-billing-implications"></a>내 작업 영역에 보안 솔루션이 이미 있습니다. 요금 청구에 영향을 주는 요인은 무엇인가요?
보안 및 감사 솔루션은 Azure VM에 대한 Security Center 표준 계층 기능을 활성화하는 데 사용됩니다. 보안 및 감사 솔루션이 작업 영역에 이미 설치되어 있는 경우 Security Center에서는 기존 솔루션을 사용합니다. 요금 청구는 변하지 않습니다.

## <a name="next-steps"></a>다음 단계
Security Center 플랫폼 마이그레이션을 자세히 알아보려면 다음을 참조하세요.

- [Azure Security Center 플랫폼 마이그레이션](security-center-platform-migration.md)
- [Azure Security Center 문제 해결 가이드](security-center-troubleshooting-guide.md).

<!--Image references-->
[1]: ./media/security-center-platform-migration-faq/pricing-tier.png
[2]: ./media/security-center-platform-migration-faq/data-collection.png
[3]: ./media/security-center-platform-migration-faq/remove-the-agent.png
[4]: ./media/security-center-platform-migration-faq/solutions.png
[5]: ./media/security-center-platform-migration-faq/use-another-workspace.png
[6]: ./media/security-center-platform-migration-faq/reconfigure-monitored-vm.png

