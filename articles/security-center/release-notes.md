---
title: Azure Security Center에 대한 릴리스 정보
description: Azure Security Center의 새로운 기능과 변경된 기능에 대한 설명
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: reference
ms.date: 03/11/2021
ms.author: memildin
ms.openlocfilehash: 4f2b31a4760a07779eebdd9492731ffe7e324d37
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103149643"
---
# <a name="whats-new-in-azure-security-center"></a>Azure Security Center의 새로운 기능

Security Center는 현재 개발 중이며 지속적으로 향상된 기능을 수용합니다. 이 페이지에서는 최신 개발 정보를 제공하기 위해 새로운 기능, 버그 수정 및 사용되지 않는 기능에 대한 정보를 제공합니다.

이 페이지는 자주 업데이트되므로 자주 다시 방문하세요. 

곧 Security Center에 적용되는 *계획된* 변경 내용에 대한 자세한 내용은 [Azure Security Center의 예정된 중요 변경 내용](upcoming-changes.md)을 참조하세요. 

> [!TIP]
> 6개월 이상된 항목을 찾으려는 경우 [Azure Security Center의 새로운 기능 아카이브](release-notes-archive.md)에서 찾을 수 있습니다.



## <a name="march-2021"></a>2021년 3월

3월의 업데이트는 다음과 같습니다.

- [Security Center에 Azure 방화벽 관리 통합](#azure-firewall-management-integrated-into-security-center)
- [이제 SQL 취약성 평가에 "규칙 사용 안 함" 환경(미리 보기) 포함](#sql-vulnerability-assessment-now-includes-the-disable-rule-experience-preview)
- [Security Center에 Azure Monitor 통합 문서를 통합하고 세 가지 템플릿 제공](#azure-monitor-workbooks-integrated-into-security-center-and-three-templates-provided)
- [이제 규정 준수 대시보드에 Azure 감사 보고서(미리 보기) 포함](#regulatory-compliance-dashboard-now-includes-azure-audit-reports-preview)
- [Azure Resource Graph에서 "ARG에서 살펴보기"를 사용하여 권장 사항 데이터 확인 가능](#recommendation-data-can-be-viewed-in-azure-resource-graph-with-explore-in-arg)
- [워크플로 자동화 배포에 대한 정책 업데이트](#updates-to-the-policies-for-deploying-workflow-automation)
- [두 가지 레거시 권장 사항은 더 이상 Azure 활동 로그에 직접 데이터를 쓰지 않습니다.](#two-legacy-recommendations-no-longer-write-data-directly-to-azure-activity-log)


### <a name="azure-firewall-management-integrated-into-security-center"></a>Security Center에 Azure 방화벽 관리 통합

Azure Security Center를 열 때 처음으로 표시되는 페이지는 개요 페이지입니다. 

이 대화형 대시보드는 하이브리드 클라우드 워크로드의 보안 상태에 대한 통합된 보기를 제공합니다. 또한 보안 경고, 적용 범위 정보 등을 보여 줍니다.

중앙 환경에서 보안 상태를 확인하는 데 도움이 될 수 있도록 Azure Firewall Manager를 이 대시보드에 통합했습니다. 이제 모든 네트워크에서 방화벽 검사 상태를 확인하고 Security Center에서 Azure 방화벽 정책을 중앙 집중식으로 관리할 수 있습니다.

[Azure Security Center의 개요 페이지](overview-page.md)에서 이 대시보드에 대해 자세히 알아보세요.

:::image type="content" source="media/release-notes/overview-dashboard-firewall-manager.png" alt-text="Azure 방화벽에 대한 타일이 포함된 Security Center의 개요 대시보드":::


### <a name="sql-vulnerability-assessment-now-includes-the-disable-rule-experience-preview"></a>이제 SQL 취약성 평가에 "규칙 사용 안 함" 환경(미리 보기) 포함

Security Center에는 잠재적 데이터베이스 취약성을 검색, 추적 및 수정하는 데 도움이 되는 기본 제공 취약점 스캐너가 포함되어 있습니다. 평가 검사의 결과는 SQL 머신의 보안 상태에 대한 개요와 보안 결과에 대한 세부 정보를 제공합니다.

조직에서 결과를 수정하지 않고 무시해야 하는 요구 사항이 있으면 필요에 따라 이 결과를 사용하지 않도록 설정할 수 있습니다. 사용하지 않도록 설정된 결과는 보안 점수에 영향을 주거나 원치 않는 노이즈를 생성하지 않습니다.

[특정 결과 사용 안 함](defender-for-sql-on-machines-vulnerability-assessment.md#disable-specific-findings-preview)에서 자세히 알아보세요.



### <a name="azure-monitor-workbooks-integrated-into-security-center-and-three-templates-provided"></a>Security Center에 Azure Monitor 통합 문서를 통합하고 세 가지 템플릿 제공

Ignite Spring 2021에서 Security Center에 통합된 Azure Monitor 통합 문서 환경이 발표된 바 있습니다.

새로운 통합 환경을 활용하여 Security Center 갤러리에서 기본 제공 템플릿 사용을 시작할 수 있습니다. 통합 문서 템플릿을 사용하여 동적 및 시각적 보고서에 액세스하고 이를 작성하여 조직의 보안 상태를 추적할 수 있습니다. 또한 Security Center 데이터 또는 지원되는 기타 데이터 형식을 기반으로 새 통합 문서를 만들고 Security Center의 GitHub 커뮤니티에서 커뮤니티 통합 문서를 빠르게 배포할 수 있습니다.

다음과 같은 세 가지 템플릿 보고서가 제공됩니다.

- **시간 경과에 따른 보안 점수** - 구독의 점수와 리소스에 대한 권장 사항의 변경 내용 추적
- **시스템 업데이트** - 리소스, OS, 심각도 등에 따라 누락된 시스템 업데이트 보기
- **취약성 평가 결과** - Azure 리소스의 취약성 검사 결과 보기

이러한 보고서를 사용하는 방법 또는 [Security Center 데이터에 대한 풍부한 자체 대화형 보고서](custom-dashboards-azure-workbooks.md)를 작성하는 방법에 대해 알아보세요.

:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-snip.png" alt-text="시간 경과에 따른 보안 점수 보고서":::


### <a name="regulatory-compliance-dashboard-now-includes-azure-audit-reports-preview"></a>이제 규정 준수 대시보드에 Azure 감사 보고서(미리 보기) 포함

이제 규정 준수 대시보드의 도구 모음에서 Azure 및 Dynamics 인증 보고서를 다운로드할 수 있습니다. 

:::image type="content" source="media/release-notes/audit-reports-regulatory-compliance-dashboard.png" alt-text="규정 준수 대시보드의 도구 모음":::

관련 보고서 유형(PCI, SOC, ISO 및 기타)에 대한 탭을 선택하고 필터를 사용하여 필요한 특정 보고서를 찾을 수 있습니다.

[규정 준수 대시보드에서 표준을 관리](update-regulatory-compliance-packages.md)하는 방법에 대해 자세히 알아보세요.

:::image type="content" source="media/release-notes/audit-reports-list-regulatory-compliance-dashboard.png" alt-text="사용 가능한 Azure 감사 보고서 목록 필터링":::



### <a name="recommendation-data-can-be-viewed-in-azure-resource-graph-with-explore-in-arg"></a>Azure Resource Graph에서 "ARG에서 살펴보기"를 사용하여 권장 사항 데이터 확인 가능

이제 권장 사항 세부 정보 페이지에 "ARG에서 살펴보기" 도구 모음 단추가 포함됩니다. 이 단추를 사용하여 Azure Resource Graph 쿼리를 열고 권장 사항의 데이터를 살펴보고, 내보내고, 공유할 수 있습니다.

ARG(Azure Resource Graph)의 강력한 필터링, 그룹화 및 정렬 기능을 통해 클라우드 환경에서 리소스 정보에 즉시 액세스할 수 있습니다. Azure 구독 간에 프로그래밍 방식으로 또는 Azure Portal 내에서 정보를 쿼리하는 빠르고 효율적인 방법입니다.

[ARG(Azure Resource Graph)](../governance/resource-graph/index.yml)에 대해 자세히 알아보세요.

:::image type="content" source="media/release-notes/explore-in-resource-graph.png" alt-text="Azure Resource Graph에서 권장 사항 데이터 살펴보기":::


### <a name="updates-to-the-policies-for-deploying-workflow-automation"></a>워크플로 자동화 배포에 대한 정책 업데이트

조직의 모니터링 및 인시던트 대응 프로세스를 자동화하면 보안 인시던트를 조사하고 완화하는 데 걸리는 시간을 크게 향상시킬 수 있습니다.

자동화를 조직 전체에 배포할 수 있도록 워크플로 자동화 프로시저를 만들고 구성하는 다음과 같은 세 가지 Azure Policy 'DeployIfNotExist' 정책을 제공합니다.

|목표  |정책  |정책 ID  |
|---------|---------|---------|
|보안 경고에 대한 워크플로 자동화|[Azure Security Center 경고에 대한 워크플로 자동화 배포](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2ff1525828-9a90-4fcf-be48-268cdd02361e)|f1525828-9a90-4fcf-be48-268cdd02361e|
|보안 추천 사항에 대한 워크플로 자동화|[Azure Security Center 권장 사항에 대한 워크플로 자동화 배포](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-2475-4850-afd6-43795f3492ef)|73d6ab6c-2475-4850-afd6-43795f3492ef|
|규정 준수 변경에 대한 워크플로 자동화|[Azure Security Center 규정 준수에 대한 워크플로 자동화 배포](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-509122b9-ddd9-47ba-a5f1-d0dac20be63c)|509122b9-ddd9-47ba-a5f1-d0dac20be63c|
||||

이러한 정책의 기능에 대한 두 가지 업데이트가 있습니다.

- 할당 후 적용하면 활성 상태로 유지됩니다.
- 이제 이미 배포된 후에도 이러한 정책을 사용자 지정하고 매개 변수를 업데이트할 수 있습니다. 예를 들어 사용자가 다른 평가 키를 추가하거나 기존 평가 키를 편집하려는 경우 그렇게 할 수 있습니다.

[워크플로 자동화 템플릿](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation)을 시작합니다.

[Security Center 트리거에 대한 응답 자동화](workflow-automation.md) 방법에 대해 자세히 알아보세요.


### <a name="two-legacy-recommendations-no-longer-write-data-directly-to-azure-activity-log"></a>두 가지 레거시 권장 사항은 더 이상 Azure 활동 로그에 직접 데이터를 쓰지 않습니다. 

Security Center는 거의 모든 보안 권장 사항에 대한 데이터를 Azure Advisor에 전달한 후 [Azure 활동 로그](../azure-monitor/essentials/activity-log.md)에 씁니다.

두 가지 권장 사항의 경우 데이터가 Azure 활동 로그에 동시에 직접 씁니다. 이 변경으로 Security Center는 이러한 레거시 보안 권장 사항에 대한 데이터를 활동 로그에 직접 쓰는 것을 중지합니다. 대신 다른 모든 권장 사항과 마찬가지로 Azure Advisor로 데이터를 내보냅니다.

두 가지 레거시 권장 사항은 다음과 같습니다.
- 머신에서 엔드포인트 보호 상태 문제를 해결해야 함
- 머신 보안 구성의 취약성을 수정해야 합니다.

활동 로그의 "TaskDiscovery 유형의 권장 사항" 범주에서 이 두 가지 권장 사항에 대한 정보에 액세스한 경우 이 정보는 더 이상 사용할 수 없습니다.



## <a name="february-2021"></a>2021년 2월

2월의 업데이트는 다음과 같습니다.

- [GA(일반 공급)를 위해 릴리스된 Azure Portal의 새 보안 경고 페이지](#new-security-alerts-page-in-the-azure-portal-released-for-general-availability-ga)
- [Kubernetes 워크로드 보호 권장 사항이 GA(일반 공급)로 릴리스됨](#kubernetes-workload-protection-recommendations-released-for-general-availability-ga)
- [이제 Azure Defender와 Microsoft Defender for Endpoint의 통합을 통해 Windows Server 2019 및 Windows 10 Virtual Desktop(WVD) 지원(미리 보기)](#microsoft-defender-for-endpoint-integration-with-azure-defender-now-supports-windows-server-2019-and-windows-10-virtual-desktop-wvd-in-preview)
- [권장 사항 세부 정보 페이지에서 정책으로 직접 연결](#direct-link-to-policy-from-recommendation-details-page)
- [SQL 데이터 분류 권장 사항이 더 이상 보안 점수에 영향을 주지 않음](#sql-data-classification-recommendation-no-longer-affects-your-secure-score)
- [규정 준수 평가 변경 사항으로 워크플로 자동화가 트리거될 수 있음(미리 보기)](#workflow-automations-can-be-triggered-by-changes-to-regulatory-compliance-assessments-in-preview)
- [자산 인벤토리 페이지 향상](#asset-inventory-page-enhancements)


### <a name="new-security-alerts-page-in-the-azure-portal-released-for-general-availability-ga"></a>GA(일반 공급)를 위해 릴리스된 Azure Portal의 새 보안 경고 페이지

Azure Security Center의 보안 경고 페이지가 다음을 제공하도록 다시 디자인되었습니다.

- **경고에 대한 향상된 심사 환경** - 경고 피로를 줄이고 가장 관련성이 높은 위협에 더 쉽게 집중하는 데 도움이 됩니다. 목록에는 사용자 지정 가능한 필터 및 그룹화 옵션이 포함되어 있습니다.
- **경고 목록의 추가 정보** - 예: MITRE ATT&ACK 전술.
- **샘플 알림을 만드는 단추** - Azure Defender 기능을 평가하고 알림을 테스트 구성(SIEM 통합, 이메일 알림 및 워크플로 자동화)을 테스트하기 위해 모든 Azure Defender 계획의 샘플 경고를 만들 수 있습니다.
- **Azure Sentinel의 인시던트 환경에 맞춤** - 이제 두 제품을 사용하는 고객이 두 제품 간에 더 직관적으로 전환할 수 있는 환경이 제공되며, 두 제품을 손쉽게 알아볼 수 있습니다.
- 대규모 경고 목록의 **성능 개선**.
- 경고 목록 **키보드 탐색**.
- **Azure Resource Graph의 경고** - 모든 리소스에 대한 Kusto 같은 API인 Azure Resource Graph에서 경고를 쿼리할 수 있습니다. 이는 자체 경고 대시보드를 빌드하는 경우에도 유용합니다. [Azure Resource Graph에 대한 자세한 정보](../governance/resource-graph/index.yml).
- **샘플 경고 기능 만들기** - 새 경고 환경에서 샘플 경고를 만들려면 [샘플 Azure Defender 경고 생성](security-center-alert-validation.md#generate-sample-azure-defender-alerts)을 참조하세요.

:::image type="content" source="media/security-center-managing-and-responding-alerts/alerts-page.png" alt-text="Azure Security Center의 보안 경고 목록":::


### <a name="kubernetes-workload-protection-recommendations-released-for-general-availability-ga"></a>Kubernetes 워크로드 보호 권장 사항이 GA(일반 공급)로 릴리스됨

Kubernetes 워크로드 보호를 위한 권장 사항 세트가 GA(일반 공급)된다는 것을 발표하게 되어 기쁘게 생각합니다.

Kubernetes 워크로드를 기본적으로 안전하게 보호하기 위해 Security Center에서 Kubernetes 허용 제어가 있는 적용 옵션을 포함하여 Kubernetes 수준 보안 강화 권장 사항이 추가되었습니다.

Kubernetes에 대한 Azure Policy 추가 항목을 AKS(Azure Kubernetes Service) 클러스터에 설치하면 Kubernetes API 서버에 대한 모든 요청이 클러스터에 유지되기 전에 미리 정의된 모범 사례 세트(예: 13개 보안 권장 사항으로 표시됨)에 대해 모니터링됩니다. 그런 다음, 모범 사례를 적용하고 향후 워크로드에 대해 위임하도록 구성할 수 있습니다.

예를 들어 권한 있는 컨테이너를 만들지 않도록 위임할 수 있습니다. 그러면, 이러한 작업에 대한 이후의 모든 요청이 차단됩니다.

[Kubernetes 허용 제어를 사용하여 워크로드 보호 모범 사례](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control)에서 자세히 알아보세요.

> [!NOTE]
> 권장 사항은 미리 보기에 있었지만 AKS 클러스터 리소스를 비정상으로 렌더링하지 않았으며 보안 점수 계산에 포함되지 않았습니다. 이제 이러한 권장 사항은 이 GA 알림과 함께 점수 계산에 포함됩니다. 아직 수정하지 않은 경우 보안 점수에 약간의 영향을 줄 수 있습니다. 가능한 경우 [Azure Security Center의 권장 사항 수정](security-center-remediate-recommendations.md)에서 설명한 대로 이러한 권장 사항을 수정합니다.


### <a name="microsoft-defender-for-endpoint-integration-with-azure-defender-now-supports-windows-server-2019-and-windows-10-virtual-desktop-wvd-in-preview"></a>이제 Azure Defender와 Microsoft Defender for Endpoint의 통합을 통해 Windows Server 2019 및 Windows 10 Virtual Desktop(WVD) 지원(미리 보기)

Microsoft Defender for Endpoint는 클라우드에서 제공하는 전체적인 엔드포인트 보안 솔루션입니다. 위험 기반 취약성 관리 및 평가뿐 아니라 엔드포인트 검색 및 응답(EDR)도 제공합니다. Azure Security Center와 Defender for Endpoint를 함께 사용하는 경우의 이점에 대한 전체 목록은 [Security Center의 통합 EDR 솔루션 Microsoft Defender for Endpoint로 엔드포인트 보호](security-center-wdatp.md)를 참조하세요.

Windows 서버에서 서버용 Azure Defender를 사용하도록 설정하면 Defender for Endpoint용 라이선스가 플랜에 포함됩니다. 서버에 Azure Defender를 사용하도록 이미 설정했고 구독에 Windows 2019 서버가 있는 경우 Defender for Endpoint가 이 업데이트와 함께 자동으로 수신됩니다. 수동 조치가 필요하지 않습니다. 

이제 Windows Server 2019 및 [WVD(Windows Virtual Desktop)](../virtual-desktop/overview.md)를 포함하도록 지원 범위가 확장되었습니다.

> [!NOTE]
> Windows Server 2019 머신에서 Defender for Endpoint를 사용하도록 설정하는 경우 [Defender for Endpoint 통합 사용](security-center-wdatp.md#enabling-the-microsoft-defender-for-endpoint-integration)에 설명된 필수 조건을 충족하는지 확인합니다.

### <a name="direct-link-to-policy-from-recommendation-details-page"></a>권장 사항 세부 정보 페이지에서 정책으로 직접 연결

권장 사항에 대한 세부 정보를 검토할 때 기본 정책을 볼 수 있으면 도움이 되는 경우가 많습니다. 정책으로 지원되는 모든 권장 사항에는 권장 사항 세부 정보 페이지의 새 링크가 있습니다.

:::image type="content" source="media/release-notes/view-policy-definition.png" alt-text="권장 사항을 지원하는 특정 정책에 대한 Azure Policy 페이지로 연결":::

이 링크를 사용하여 정책 정의를 확인하고 평가 논리를 검토합니다. 

[보안 권장 사항 참조 가이드](recommendations-reference.md)에서 권장 사항 목록을 검토하는 경우 정책 정의 페이지에 대한 링크도 표시됩니다.

:::image type="content" source="media/release-notes/view-policy-definition-from-documentation.png" alt-text="Azure Security Center 권장 사항 참조 페이지에서 직접 특정 정책에 대한 Azure Policy 페이지에 액세스" lightbox="media/release-notes/view-policy-definition-from-documentation.png":::


### <a name="sql-data-classification-recommendation-no-longer-affects-your-secure-score"></a>SQL 데이터 분류 권장 사항이 더 이상 보안 점수에 영향을 주지 않음
**SQL 데이터베이스에서 중요한 데이터를 분류해야 합니다.** 권장 사항은 더 이상 보안 점수에 영향을 주지 않습니다. 이는 **데이터 분류 적용** 보안 제어의 유일한 권장 사항이므로 이제 해당 제어의 보안 점수 값은 0입니다.

Security Center의 모든 보안 컨트롤에 대한 전체 목록과 각 보안 컨트롤의 점수 및 권장 사항 목록은 [보안 컨트롤 및 권장 사항](secure-score-security-controls.md#security-controls-and-their-recommendations)을 참조하세요.

### <a name="workflow-automations-can-be-triggered-by-changes-to-regulatory-compliance-assessments-in-preview"></a>규정 준수 평가 변경 사항으로 워크플로 자동화가 트리거될 수 있음(미리 보기)
워크플로 자동화를 위한 트리거 옵션에 세 번째 데이터 유형인 규정 준수 평가 변경 사항을 추가했습니다.

[Security Center 트리거에 대한 응답 자동화](workflow-automation.md)에서 워크플로 자동화 도구를 사용하는 방법에 대해 알아봅니다.

:::image type="content" source="media/release-notes/regulatory-compliance-triggers-workflow-automation.png" alt-text="규정 준수 평가 변경 사항을 사용하여 워크플로 자동화 트리거" lightbox="media/release-notes/regulatory-compliance-triggers-workflow-automation.png":::


### <a name="asset-inventory-page-enhancements"></a>자산 인벤토리 페이지 향상
Security Center의 자산 인벤토리 페이지는 다음과 같은 방식으로 개선되었습니다.

- 이제 페이지 상단의 요약에 **등록되지 않은 구독** 이 포함되어 Security Center가 활성화되지 않은 구독 수를 보여줍니다.

    :::image type="content" source="media/release-notes/unregistered-subscriptions.png" alt-text="자산 인벤토리 페이지 상단의 요약에 있는 등록되지 않은 구독 수":::

- 필터는 다음을 포함하도록 확장 및 개선되었습니다.
    - **개수** - 각 필터는 각 범주의 조건을 충족하는 리소스 수를 표시합니다.

        :::image type="content" source="media/release-notes/counts-in-inventory-filters.png" alt-text="Azure Security Center의 자산 인벤토리 페이지에 있는 필터의 개수":::

    - **예외 필터 포함**(선택 사항) - 예외가 있거나 없는 리소스로 결과 범위를 좁힙니다. 이 필터는 기본적으로 표시되지 않지만 **필터 추가** 단추에서 액세스할 수 있습니다.

        :::image type="content" source="media/release-notes/adding-contains-exemption-filter.gif" alt-text="Azure Security Center의 자산 인벤토리 페이지에 '예외 포함' 필터 추가":::

[자산 인벤토리로 리소스를 검색 및 관리](asset-inventory.md)하는 방법에 대해 자세히 알아보세요.

## <a name="january-2021"></a>2021년 1월

1월의 업데이트는 다음과 같습니다.

- [Azure Security Benchmark는 이제 Azure Security Center의 기본 정책 이니셔티브입니다.](#azure-security-benchmark-is-now-the-default-policy-initiative-for-azure-security-center)
- [온-프레미스 및 다중 클라우드 머신의 취약성 평가가 GA(일반 공급)를 위해 릴리스되었습니다.](#vulnerability-assessment-for-on-premise-and-multi-cloud-machines-is-released-for-general-availability-ga)
- [이제 관리 그룹에 대한 보안 점수를 미리 보기에서 볼 수 있습니다.](#secure-score-for-management-groups-is-now-available-in-preview)
- [보안 점수 API는 GA(일반 공급)를 위해 릴리스되었습니다.](#secure-score-api-is-released-for-general-availability-ga)
- [Azure Defender for App Service에 추가된 현수 DNS 보호](#dangling-dns-protections-added-to-azure-defender-for-app-service)
- [다중 클라우드 커넥터는 GA(일반 공급)용으로 릴리스되었습니다.](#multi-cloud-connectors-are-released-for-general-availability-ga)
- [구독 및 관리 그룹에 대한 보안 점수에서 전체 권장 사항 제외](#exempt-entire-recommendations-from-your-secure-score-for-subscriptions-and-management-groups)
- [사용자는 이제 전역 관리자에게 테넌트 전체의 가시성을 요청할 수 있습니다.](#users-can-now-request-tenant-wide-visibility-from-their-global-administrator)
- [Azure 보안 벤치마크의 적용 범위를 늘리기 위해 35개의 미리 보기 추천 사항이 추가됨](#35-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark)
- [필터링된 권장 목록의 CSV 내보내기](#csv-export-of-filtered-list-of-recommendations)
- [Azure Policy 평가에서 "해당 없음" 리소스가 이제 "준수"로 보고됨](#not-applicable-resources-now-reported-as-compliant-in-azure-policy-assessments)
- [연속 내보내기를 통해 보안 점수 및 규정 준수 데이터의 주간 스냅샷 내보내기(미리 보기)](#export-weekly-snapshots-of-secure-score-and-regulatory-compliance-data-with-continuous-export-preview)


### <a name="azure-security-benchmark-is-now-the-default-policy-initiative-for-azure-security-center"></a>Azure Security Benchmark는 이제 Azure Security Center의 기본 정책 이니셔티브입니다.

Azure 보안 벤치마크는 일반적인 규정 준수 프레임워크를 기반으로 하는 보안 및 규정 준수 모범 사례에 대해 Microsoft에서 작성한 Azure 관련 지침 세트입니다. 널리 인정받는 이 벤치마크는 클라우드 중심 보안에 초점을 맞춘 [CIS(Center for Internet Security)](https://www.cisecurity.org/benchmark/azure/) 및 [NIST(National Institute of Standards and Technology)](https://www.nist.gov/)의 컨트롤을 기반으로 합니다.

최근 몇 개월 동안 Security Center의 기본 제공 보안 권장 사항 목록은 이 벤치마크의 적용 범위를 확장하기 위해 크게 증가했습니다.

이번 릴리스부터 벤치마크는 Security Center 권장 사항의 기반이 되었으며 기본 정책 이니셔티브로 완전히 통합되었습니다. 

모든 Azure 서비스에는 설명서에 보안 기준 페이지가 있습니다. 예를 들어, [이는 Security Center의 기준입니다](security-baseline.md). 이러한 기준은 Azure 보안 벤치마크를 기반으로 합니다.

Security Center의 규정 준수 대시보드를 사용하는 경우 전환 기간 동안 두 가지 벤치마크 인스턴스를 볼 수 있습니다.

:::image type="content" source="media/release-notes/regulatory-compliance-with-azure-security-benchmark.png" alt-text="Azure 보안 벤치마크를 보여주는 Azure Security Center의 규정 준수 대시보드":::

기존 권장 사항은 영향을 받지 않으며 벤치마크가 증가하면 변경 사항이 Security Center에 자동으로 반영됩니다. 

자세히 알아보려면 다음 페이지를 참조하세요.

- [Azure 보안 벤치마크에 대한 자세한 정보](../security/benchmarks/introduction.md)
- [규정 준수 대시보드의 표준 집합 사용자 지정](update-regulatory-compliance-packages.md)

### <a name="vulnerability-assessment-for-on-premise-and-multi-cloud-machines-is-released-for-general-availability-ga"></a>온-프레미스 및 다중 클라우드 머신의 취약성 평가가 GA(일반 공급)를 위해 릴리스되었습니다.

당사는 10월에 [서버용 Azure Defender](defender-for-servers-introduction.md)의 통합 취약성 평가 스캐너(Qualys 기반)로 Azure Arc 사용 서버를 검색하는 기능의 미리 보기를 발표했습니다.

이제 GA(일반 공급)를 위해 릴리스되었습니다.

Azure가 아닌 머신에서 Azure Arc를 사용하도록 설정한 경우 Security Center는 이러한 머신에 통합 취약성 스캐너를 수동 및 대규모로 배포할 수 있습니다.

이번 업데이트를 통해 모든 Azure 및 비-Azure 자산의 취약성 관리 프로그램을 통합하는 **서버용 Azure Defender** 의 강력한 성능을 발휘할 수 있습니다.

주요 기능:

- Azure Arc 머신의 VA(취약성 평가) 스캐너 프로비저닝 상태 모니터링
- 보호되지 않는 Windows 및 Linux Azure Arc 머신에 통합 VA 에이전트 프로비저닝(수동 및 대규모로)
- 배포된 에이전트에서 탐지된 취약성 수신 및 분석(수동 및 대규모로)
- Azure VM 및 Azure Arc 머신에 대한 통합 환경

[하이브리드 머신에 통합 취약성 스캐너를 배포하는 내용을 자세히 알아보세요](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-scanner-to-your-azure-and-hybrid-machines).

[Azure Arc 사용 서버에 대해 자세히 알아보세요](../azure-arc/servers/index.yml).


### <a name="secure-score-for-management-groups-is-now-available-in-preview"></a>이제 관리 그룹에 대한 보안 점수를 미리 보기에서 볼 수 있습니다.

이제 보안 점수 페이지에는 구독 수준 외에도 관리 그룹에 대해 집계된 보안 점수가 표시됩니다. 이제 조직의 관리 그룹 목록과 각 관리 그룹의 점수를 볼 수 있습니다.

:::image type="content" source="media/secure-score-security-controls/secure-score-management-groups.png" alt-text="관리 그룹의 보안 점수 보기.":::

[Azure Security Center의 보안 점수 및 보안 제어](secure-score-security-controls.md)에 대해 자세히 알아보세요.

### <a name="secure-score-api-is-released-for-general-availability-ga"></a>보안 점수 API는 GA(일반 공급)를 위해 릴리스되었습니다.

이제 [보안 점수 API](/rest/api/securitycenter/securescores/)를 통해 점수에 액세스할 수 있습니다. API 메서드는 데이터를 쿼리할 수 있는 유연성을 제공하고, 시간 경과에 따른 보안 점수에 대한 사용자 고유의 보고 메커니즘을 빌드합니다. 예를 들면 다음과 같습니다.

- **보안 점수** API를 사용하여 특정 구독에 대한 점수를 가져옵니다.
- **보안 점수 제어** API를 사용하여 보안 제어 및 구독의 현재 점수를 나열합니다.

[GitHub 커뮤니티의 보안 점수 영역](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score)에서 보안 점수 API를 사용하여 가능한 외부 도구에 대해 알아보세요.

[Azure Security Center의 보안 점수 및 보안 제어](secure-score-security-controls.md)에 대해 자세히 알아보세요.


### <a name="dangling-dns-protections-added-to-azure-defender-for-app-service"></a>Azure Defender for App Service에 추가된 현수 DNS 보호

하위 도메인 인수는 조직에 일반적이고 심각도가 높은 위협입니다. 프로비저닝이 해제된 웹 사이트를 가리키는 DNS 레코드가 있는 경우 하위 도메인 인수가 발생할 수 있습니다. 이러한 DNS 레코드는 "현수 DNS" 항목으로도 알려져 있습니다. CNAME 레코드는 특히 이 위협에 취약합니다. 

하위 도메인 인수를 통해 위협 행위자는 조직의 도메인에 대한 트래픽을 악의적인 활동을 수행하는 사이트로 리디렉션할 수 있습니다.

이제 앱 서비스 웹 사이트가 해제될 때 Azure Defender for App Service가 현수 DNS 항목을 검색합니다. 이때는 DNS 항목이 존재하지 않는 리소스를 가리키고 웹 사이트가 하위 도메인 인수에 취약한 시점입니다. 이러한 보호는 도메인이 Azure DNS 또는 외부 도메인 등록 기관으로 관리되는지 여부에 관계없이 사용할 수 있고 Windows의 App Service와 Linux의 App Service 모두에 적용합니다.

자세한 정보:

- [App Service 경고 참조 테이블](alerts-reference.md#alerts-azureappserv) - 현수 DNS 항목이 검색될 때 트리거되는 두 개의 새로운 Azure Defender 경고가 포함됩니다.
- [현수 DNS 항목 방지 및 하위 도메인 인수 방지](../security/fundamentals/subdomain-takeover.md) - 하위 도메인 인수 위협 및 현수 DNS 측면에 대해 알아보세요.
- [Azure Defender for App Service 소개](defender-for-app-service-introduction.md)


### <a name="multi-cloud-connectors-are-released-for-general-availability-ga"></a>다중 클라우드 커넥터는 GA(일반 공급)용으로 릴리스되었습니다.

클라우드 워크로드가 일반적으로 여러 클라우드 플랫폼에 걸쳐 있는 경우 클라우드 보안 서비스도 동일한 작업을 수행해야 합니다.

Azure Security Center는 Azure, AWS(Amazon Web Services) 및 GCP(Google Cloud Platform)의 워크로드를 보호합니다.

AWS 또는 GCP 계정을 연결하면 AWS Security Hub 및 GCP 보안 명령 센터와 같은 기본 보안 도구를 Azure Security Center에 통합합니다.

이 기능은 Security Center가 모든 주요 클라우드 환경에서 가시성과 보호를 제공함을 의미합니다. 이러한 통합의 이점 중 일부는 다음과 같습니다.

- 자동 에이전트 프로비저닝 - Security Center가 Azure Arc를 사용하여 AWS 인스턴스에 Log Analytics 에이전트 배포
- 정책 관리
- 취약점 관리
- 포함된 EDR(엔드포인트 검색 및 응답)
- 잘못된 보안 구성 검색
- 모든 클라우드 공급자의 보안 권장 사항을 보여주는 단일 보기
- Security Center의 보안 점수 계산에 모든 리소스 통합
- AWS 및 GCP 리소스의 규정 준수 평가

Security Center의 메뉴에서 **다중 클라우드 커넥터** 를 선택하면 새 커넥터를 만드는 옵션이 표시됩니다.

:::image type="content" source="./media/quickstart-onboard-aws/add-aws-account.png" alt-text="Security Center의 다중 클라우드 커넥터 페이지에 표시되는 [AWS 계정 추가] 단추":::

다음에서 자세히 알아보세요.
- [Azure Security Center에 AWS 계정 연결](quickstart-onboard-aws.md)
- [Azure Security Center에 GCP 계정 연결](quickstart-onboard-gcp.md)


### <a name="exempt-entire-recommendations-from-your-secure-score-for-subscriptions-and-management-groups"></a>구독 및 관리 그룹에 대한 보안 점수에서 전체 권장 사항 제외

전체 권장 사항을 포함하도록 면제 기능을 확장하고 있습니다. Security Center에서 구독, 관리 그룹 또는 리소스에 대해 제공하는 보안 권장 사항을 세부 조정할 수 있는 추가 옵션을 제공합니다.

경우에 따라 Security Center가 검색하지 못한 타사 도구로 문제가 해결되었음을 알고 있는 경우 리소스가 비정상으로 표시됩니다. 또는 해당 영역이 아닌 것으로 생각되는 범위에 권장 사항이 표시됩니다. 권장 사항은 특정 구독에 적합하지 않을 수도 있습니다. 또는 조직에서 특정 리소스 또는 권장 사항과 관련된 위험을 수용하기로 결정했을 수 있습니다.

이 미리 보기 기능으로 이제 다음과 같은 권장 사항에 대한 예외를 만들 수 있습니다.

- **리소스를 제외** 하여 향후 비정상적인 리소스에 나열되지 않고 보안 점수에 영향을 주지 않도록 합니다. 리소스가 해당되지 않는 것으로 나열되고 선택한 특정 근거와 함께 이유가 "예외"로 표시됩니다.

- 권장 사항이 보안 점수에 영향을 주지 않고 향후 구독 또는 관리 그룹에 표시되지 않도록 하려면 **구독 또는 관리 그룹을 제외** 합니다. 이는 기존 리소스와 나중에 만드는 리소스와 관련이 있습니다. 권장 사항은 선택한 범위에 대해 선택한 특정 근거로 표시됩니다.

[보안 점수에서 리소스 및 권장 사항 제외](exempt-resource.md)에서 자세히 알아보세요.



### <a name="users-can-now-request-tenant-wide-visibility-from-their-global-administrator"></a>사용자는 이제 전역 관리자에게 테넌트 전체의 가시성을 요청할 수 있습니다.

사용자에게 Security Center 데이터를 볼 수 있는 권한이 없는 경우 이제 조직의 전역 관리자에게 권한을 요청하는 링크가 표시됩니다. 요청에는 원하는 역할과 필요한 이유에 대한 근거가 포함됩니다.

:::image type="content" source="media/security-center-management-groups/request-tenant-permissions.png" alt-text="사용자에게 테넌트 전체의 사용 권한을 요청할 수 있음을 알리는 배너입니다.":::

[사용자 권한이 부족할 때 테넌트 전체 권한 요청](tenant-wide-permissions-management.md#request-tenant-wide-permissions-when-yours-are-insufficient)에서 자세히 알아보세요.


### <a name="35-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark"></a>Azure 보안 벤치마크의 적용 범위를 늘리기 위해 35개의 미리 보기 추천 사항이 추가됨

Azure Security Benchmark는 Azure Security Center의 기본 정책 이니셔티브입니다. 

이 벤치마크의 적용 범위를 늘리기 위해 다음 35개의 미리 보기 권장 사항이 Security Center에 추가되었습니다.

> [!TIP]
> 미리 보기 추천 사항은 리소스를 비정상으로 렌더링하지 않으며 보안 점수 계산에 포함되지 않습니다. 미리 보기 기간이 끝나면 점수에 기여할 수 있도록 가능한 경우 언제든지 수정합니다. [Azure Security Center의 추천 사항 수정](security-center-remediate-recommendations.md)에서 이러한 추천 사항에 대응하는 방법에 대해 자세히 알아보세요.

| 보안 컨트롤                     | 새로운 권장 사항                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
|--------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 저장 데이터 암호화 사용            | - Azure Cosmos DB 계정은 고객 관리형 키를 사용하여 미사용 데이터를 암호화해야 함<br>- Azure Machine Learning 작업 영역은 CMK(고객 관리형 키)를 사용하여 암호화해야 함<br>- MySQL 서버에 대해 BYOK(Bring Your Own Key) 데이터 보호를 사용하도록 설정해야 함<br>- PostgreSQL 서버에 대해 BYOK(Bring Your Own Key) 데이터 보호를 사용하도록 설정해야 함<br>- Cognitive Services 계정은 CMK(고객 관리형 키)로 데이터 암호화를 사용하도록 설정해야 함<br>- 컨테이너 레지스트리는 CMK(고객 관리형 키)로 암호화해야 함<br>- SQL Managed Instance는 고객 관리형 키를 사용하여 미사용 데이터를 암호화해야 함<br>- SQL Server는 고객 관리형 키를 사용하여 미사용 데이터를 암호화해야 함<br>- 스토리지 계정은 암호화에 CMK(고객 관리형 키)를 사용해야 함                                                                                                                                                              |
| 보안 모범 사례 구현    | - 구독에 보안 문제에 대한 연락처 이메일 주소가 있어야 함<br> - 구독에 Log Analytics 에이전트의 자동 프로비저닝을 사용하도록 설정해야 함<br> - 심각도가 높은 경고에 대해 이메일 알림을 사용하도록 설정해야 함<br> - 심각도가 높은 경고에 대해 구독 소유자에게 이메일 알림을 사용하도록 설정해야 함<br> - 키 자격 증명 모음에 제거 방지를 사용하도록 설정해야 함<br> - 키 자격 증명 모음에 일시 삭제를 사용하도록 설정해야 함 |
| 액세스 및 사용 권한 관리        | - 함수 앱은 '클라이언트 인증서(들어오는 클라이언트 인증서)'를 사용하도록 설정해야 함 |
| DDoS 공격으로부터 애플리케이션 보호 | - Application Gateway에 WAF(웹 애플리케이션 방화벽)를 사용하도록 설정해야 함<br> - Azure Front Door Service 서비스에 대해 WAF(Web Application Firewall)를 사용하도록 설정해야 함 |
| 무단 네트워크 액세스 제한 | - Key Vault에서 방화벽을 사용하도록 설정해야 함<br> - Key Vault의 프라이빗 엔드포인트를 구성해야 함<br> - App Configuration은 프라이빗 링크를 사용해야 함<br> - Azure Cache for Redis는 가상 네트워크 내에 있어야 함<br> - Azure Event Grid 도메인은 프라이빗 링크를 사용해야 함<br> - Azure Event Grid 토픽은 프라이빗 링크를 사용해야 함<br> - Azure Machine Learning 작업 영역은 프라이빗 링크를 사용해야 함<br> - Azure SignalR Service는 프라이빗 링크를 사용해야 함<br> - Azure Spring Cloud는 네트워크 주입을 사용해야 함<br> - 컨테이너 레지스트리는 무제한 네트워크 액세스를 허용하지 않아야 함<br> - 컨테이너 레지스트리는 프라이빗 링크를 사용해야 함<br> - MariaDB 서버에 대해 공용 네트워크 액세스를 사용하지 않도록 설정해야 함<br> - MySQL 서버에 대해 공용 네트워크 액세스를 사용하지 않도록 설정해야 함<br> - PostgreSQL 서버에 대해 공용 네트워크 액세스를 사용하지 않도록 설정해야 함<br> - 스토리지 계정은 프라이빗 링크 연결을 사용해야 함<br> - 스토리지 계정은 가상 네트워크 규칙을 사용하여 네트워크 액세스를 제한해야 함<br> - VM Image Builder 템플릿은 프라이빗 링크를 사용해야 함|
|                                      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

관련 링크:

- [Azure 보안 벤치마크에 대한 자세한 정보](../security/benchmarks/introduction.md)
- [Azure Database for MariaDB에 대한 자세한 정보](../mariadb/overview.md)
- [Azure Database for MySQL에 대한 자세한 정보](../mysql/overview.md)
- [Azure Database for PostgreSQL에 대한 자세한 정보](../postgresql/overview.md)




### <a name="csv-export-of-filtered-list-of-recommendations"></a>필터링된 권장 목록의 CSV 내보내기 

2020년 11월에 권장 사항 페이지에 필터를 추가했습니다([이제 권장 목록에 필터가 포함됨](#recommendations-list-now-includes-filters)). 12월에 이러한 필터를 확장했습니다([권장 사항 페이지에는 환경, 심각도 및 사용 가능한 응답에 대한 새 필터가 있음](#recommendations-page-has-new-filters-for-environment-severity-and-available-responses)). 

이 공지 사항에서는 **CSV로 다운로드** 단추의 동작을 변경하여 CSV 내보내기에 현재 필터링된 목록에 표시된 권장 사항만 포함되도록 합니다. 

예를 들어 아래 이미지에서 목록이 두 가지 권장 사항으로 필터링된 것을 볼 수 있습니다. 생성된 CSV 파일에는 이러한 두 가지 권장 사항이 적용되는 모든 리소스에 대한 상태 정보가 포함됩니다.   

:::image type="content" source="media/security-center-managing-and-responding-alerts/export-to-csv-with-filters.png" alt-text="필터링된 권장 사항을 CSV 파일로 내보내기":::

[Azure Security Center의 보안 권장 사항](security-center-recommendations.md)에서 자세히 알아보세요.


### <a name="not-applicable-resources-now-reported-as-compliant-in-azure-policy-assessments"></a>Azure Policy 평가에서 "해당 없음" 리소스가 이제 "준수"로 보고됨

이전에는 권장 사항에 대해 평가되고 **해당 없음** 으로 확인된 리소스는 Azure Policy에 "비규격"으로 표시되었습니다. 어떤 사용자 작업도 상태를 "규정 준수"로 변경할 수 없었습니다. 이 변경으로 명확성을 높이기 위해 "규정 준수"로 보고됩니다.

규정 준수 리소스의 수가 증가하는 Azure Policy에만 영향을 줍니다. Azure Security Center의 보안 점수에는 영향을 주지 않습니다.


### <a name="export-weekly-snapshots-of-secure-score-and-regulatory-compliance-data-with-continuous-export-preview"></a>연속 내보내기를 통해 보안 점수 및 규정 준수 데이터의 주간 스냅샷 내보내기(미리 보기)

보안 점수 및 규정 준수 데이터의 주간 스냅샷을 내보내기 위한 [연속 내보내기](continuous-export.md) 도구에 새로운 미리 보기 기능을 추가했습니다.

연속 내보내기를 정의할 때 내보내기 빈도를 설정합니다.

:::image type="content" source="media/release-notes/export-frequency.png" alt-text="연속 내보내기의 빈도 선택":::

- **스트리밍** – 리소스의 상태가 업데이트되면 평가를 실시간으로 보냅니다(업데이트되지 않으면 데이터를 보내지 않음).
- **스냅샷** – 모든 규정 준수 평가의 현재 상태에 대한 스냅샷이 매주 전송됩니다(이는 보안 점수 및 규정 준수 데이터의 주간 스냅샷에 대한 미리 보기 기능임).

[Security Center 데이터 연속 내보내기](continuous-export.md)에서 이 기능의 전체 기능에 대해 자세히 알아보세요.

## <a name="december-2020"></a>2020년 12월

12월의 업데이트는 다음과 같습니다.

- [머신의 SQL 서버용 Azure Defender를 일반적으로 사용할 수 있습니다.](#azure-defender-for-sql-servers-on-machines-is-generally-available)
- [Azure Synapse Analytics 전용 SQL 풀에 대한 Azure Defender for SQL 지원이 일반적으로 제공됩니다.](#azure-defender-for-sql-support-for-azure-synapse-analytics-dedicated-sql-pool-is-generally-available)
- [전역 관리자는 이제 자신에게 테넌트 수준 권한을 부여할 수 있습니다.](#global-administrators-can-now-grant-themselves-tenant-level-permissions)
- [새로운 두 가지 Azure Defender 계획: Azure Defender for DNS 및 Azure Defender for Resource Manager(미리 보기)](#two-new-azure-defender-plans-azure-defender-for-dns-and-azure-defender-for-resource-manager-in-preview)
- [Azure Portal의 새 보안 경고 페이지(미리 보기)](#new-security-alerts-page-in-the-azure-portal-preview)
- [Azure SQL Database & SQL Managed Instance의 재활성화된 Security Center 환경](#revitalized-security-center-experience-in-azure-sql-database--sql-managed-instance)
- [자산 인벤토리 도구 및 필터 업데이트됨](#asset-inventory-tools-and-filters-updated)
- [SSL 인증서를 요청하는 웹앱에 대한 권장 사항은 더 이상 보안 점수에 포함되지 않음](#recommendation-about-web-apps-requesting-ssl-certificates-no-longer-part-of-secure-score)
- [권장 사항 페이지에는 환경, 심각도 및 사용 가능한 응답에 대한 새 필터가 있음](#recommendations-page-has-new-filters-for-environment-severity-and-available-responses)
- [연속 내보내기는 새 데이터 형식 및 향상된 deployifnotexist 정책을 가져옴](#continuous-export-gets-new-data-types-and-improved-deployifnotexist-policies)


### <a name="azure-defender-for-sql-servers-on-machines-is-generally-available"></a>머신의 SQL 서버용 Azure Defender를 일반적으로 사용할 수 있습니다.

Azure Security Center는 SQL Server에 대한 두 가지 Azure Defender 계획을 제공합니다.

- **Azure SQL 데이터베이스 서버용 Azure Defender** - Azure 네이티브 SQL Server 방어 
- **머신의 SQL 서버용 Azure Defender** - 하이브리드, 다중 클라우드 및 온-프레미스 환경의 SQL 서버로 동일한 보호를 확장합니다.

이 공지를 통해 **Azure Defender for SQL** 은 이제 데이터베이스와 해당 데이터가 어디에 있든 보호합니다.

Azure Defender for SQL에는 취약성 평가 기능이 포함되어 있습니다. 취약성 평가 도구에는 다음과 같은 고급 기능이 포함되어 있습니다.

- **기본 구성**(신규!)을 통해 취약성 검색 결과를 실제 보안 문제를 나타낼 수 있는 결과로 지능적으로 구체화할 수 있습니다. 기본 보안 상태를 설정한 후 취약성 평가 도구는 해당 기준 상태의 편차만 보고합니다. 기준과 일치하는 결과는 후속 검색을 통과한 것으로 간주됩니다. 이를 통해 사용자와 분석가는 중요한 부분에 집중할 수 있습니다.
- 검색된 결과 및 리소스와 관련된 이유를 *이해* 하는 데 도움이 되는 **자세한 벤치마크 정보**.
- 식별된 위험을 완화하는 데 도움이 되는 **수정 스크립트**.

[Azure Defender for SQL](defender-for-sql-introduction.md)에 대해 자세히 알아봅니다.


### <a name="azure-defender-for-sql-support-for-azure-synapse-analytics-dedicated-sql-pool-is-generally-available"></a>Azure Synapse Analytics 전용 SQL 풀에 대한 Azure Defender for SQL 지원이 일반적으로 제공됩니다.

Azure Synapse Analytics(이전의 SQL DW)는 엔터프라이즈 데이터 웨어하우징과 빅 데이터 분석을 결합한 분석 서비스입니다. 전용 SQL 풀은 Azure Synapse의 엔터프라이즈 데이터 웨어하우징 기능입니다. [Azure Synapse Analytics(이전의 SQL DW)란?](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)에서 자세히 알아봅니다.

Azure Defender for SQL은 다음을 통해 전용 SQL 풀을 보호합니다.

- 위협 및 공격을 감지하는 **지능형 위협 방지** 
- 보안 오류를 식별하고 수정하기 위한 **취약성 평가 기능**

Azure Synapse Analytics SQL 풀에 대한 Azure Defender for SQL의 지원은 Azure Security Center의 Azure SQL 데이터베이스 번들에 자동으로 추가됩니다. Azure Portal의 Synapse 작업 영역 페이지에 새로운 "Azure Defender for SQL" 탭을 찾을 수 있습니다.

[Azure Defender for SQL](defender-for-sql-introduction.md)에 대해 자세히 알아봅니다.


### <a name="global-administrators-can-now-grant-themselves-tenant-level-permissions"></a>전역 관리자는 이제 자신에게 테넌트 수준 권한을 부여할 수 있습니다.

**전역 관리자** 의 Azure Active Directory 역할을 가진 사용자는 테넌트 전체의 책임이 있지만 Azure Security Center에서 해당 조직 전체 정보를 볼 수 있는 Azure 권한은 없습니다. 

자신에게 테넌트 수준 사용 권한을 할당하려면 [자신에게 테넌트 전체 사용 권한 부여](tenant-wide-permissions-management.md#grant-tenant-wide-permissions-to-yourself)의 지침을 따르세요.


### <a name="two-new-azure-defender-plans-azure-defender-for-dns-and-azure-defender-for-resource-manager-in-preview"></a>새로운 두 가지 Azure Defender 계획: Azure Defender for DNS 및 Azure Defender for Resource Manager(미리 보기)

Azure 환경을 위한 새로운 두 가지 클라우드 네이티브 너비 위협 방지 기능이 추가되었습니다.

이러한 새로운 보호 기능은 위협 행위자의 공격에 대한 복원력을 크게 향상시키고 Azure Defender로 보호되는 Azure 리소스의 수를 크게 늘립니다.

- **Azure Defender for Resource Manager** - 조직에서 수행되는 모든 리소스 관리 작업을 자동으로 모니터링합니다. 자세한 내용은 다음을 참조하세요.
    - [Azure Defender for Resource Manager 소개](defender-for-resource-manager-introduction.md)
    - [Azure Defender for Resource Manager 경고에 대한 대응](defender-for-resource-manager-usage.md)
    - [Azure Defender for Resource Manager에서 제공하는 경고 목록](alerts-reference.md#alerts-resourcemanager)

- **Azure Defender for DNS** - Azure 리소스의 모든 DNS 쿼리를 지속적으로 모니터링합니다. 자세한 내용은 다음을 참조하세요.
    - [Azure Defender for DNS 소개](defender-for-dns-introduction.md)
    - [Azure Defender for DNS 경고에 대한 대응](defender-for-dns-usage.md)
    - [Azure Defender for DNS에서 제공하는 경고 목록](alerts-reference.md#alerts-dns)


### <a name="new-security-alerts-page-in-the-azure-portal-preview"></a>Azure Portal의 새 보안 경고 페이지(미리 보기)

Azure Security Center의 보안 경고 페이지가 다음을 제공하도록 다시 디자인되었습니다.

- **경고에 대한 향상된 심사 환경** - 경고 피로를 줄이고 가장 관련성이 높은 위협에 더 쉽게 집중하는 데 도움이 됩니다. 목록에는 사용자 지정 가능한 필터 및 그룹화 옵션이 포함되어 있습니다.
- **경고 목록의 추가 정보** - 예: MITRE ATT&ACK 전술
- **샘플 경고를 만드는 단추** - Azure Defender 기능을 평가하고 경고 구성(SIEM 통합, 이메일 알림 및 워크플로 자동화)을 테스트하기 위해 모든 Azure Defender 계획의 샘플 경고를 만들 수 있습니다.
- **Azure Sentinel의 인시던트 환경에 맞춤** - 이제 두 제품을 사용하는 고객이 두 제품 간에 더 직관적으로 전환할 수 있는 환경이 제공되며, 두 제품을 손쉽게 알아볼 수 있습니다.
- 대규모 경고 목록의 **성능 개선**
- 경고 목록 **키보드 탐색**
- **Azure Resource Graph의 경고** - 모든 리소스에 대한 Kusto 같은 API인 Azure Resource Graph에서 경고를 쿼리할 수 있습니다. 이는 자체 경고 대시보드를 빌드하는 경우에도 유용합니다. [Azure Resource Graph에 대한 자세한 정보](../governance/resource-graph/index.yml).

새 환경에 액세스하려면 보안 경고 페이지 맨 위에 있는 배너에서 '지금 사용해 보기' 링크를 사용합니다.

:::image type="content" source="media/security-center-managing-and-responding-alerts/preview-alerts-experience-banner.png" alt-text="새 미리 보기 경고 환경에 대한 링크가 포함된 배너":::

새 경고 환경에서 샘플 경고를 만들려면 [샘플 Azure Defender 경고 생성](security-center-alert-validation.md#generate-sample-azure-defender-alerts)을 참조하세요.


### <a name="revitalized-security-center-experience-in-azure-sql-database--sql-managed-instance"></a>Azure SQL Database & SQL Managed Instance의 재활성화된 Security Center 환경 

SQL 내의 Security Center 환경은 다음과 같은 Security Center 및 Azure Defender for SQL 기능에 대한 액세스를 제공합니다.

- **보안 권장 사항** – Security Center는 연결된 모든 Azure 리소스의 보안 상태를 정기적으로 분석하여 잠재적인 보안 구성 오류를 식별합니다. 그런 다음, 이러한 취약성을 해결하고 조직의 보안 태세를 개선하는 방법에 대한 권장 사항을 제공합니다.
- **보안 경고** - SQL 삽입, 무차별 암호 대입 공격 및 권한 남용 같은 위협에 대해 Azure SQL 활동을 지속적으로 모니터링하는 검색 서비스입니다. 이 서비스는 Security Center에서 세부적이고 작업 지향적인 보안 경고를 트리거하고 Microsoft의 Azure 네이티브 SIEM 솔루션인 Azure Sentinel을 사용하여 지속적으로 조사하기 위한 옵션을 제공합니다.
- **검색 결과** – Azure SQL 구성을 지속적으로 모니터링하고 취약성을 해결하는 데 도움이 되는 취약성 평가 서비스입니다. 평가 검사는 자세한 보안 결과와 함께 Azure SQL 보안 상태의 개요를 제공합니다.     

:::image type="content" source="media/release-notes/azure-security-center-experience-in-sql.png" alt-text="SQL에 대한 Azure Security Center의 보안 기능은 Azure SQL 내에서 사용할 수 있습니다.":::


### <a name="asset-inventory-tools-and-filters-updated"></a>자산 인벤토리 도구 및 필터 업데이트됨

Azure Security Center의 인벤토리 페이지가 다음과 같이 변경되었습니다.

- 도구 모음에 **가이드 및 피드백** 추가. 관련 정보 및 도구에 대한 링크가 포함된 창이 열립니다. 
- 리소스에 사용할 수 있는 기본 필터에 **구독 필터** 추가.
- 현재 필터 옵션을 Azure Resource Graph 쿼리(이전에는 "리소스 그래프 탐색기에서 보기"라고 함)로 열기 위한 **쿼리 열기** 링크.
- 각 필터에 대한 **운영자 옵션**. 이제 '=' 이외의 추가 논리 연산자에서 선택할 수 있습니다. 예를 들어, 'encrypt(암호화)' 문자열이 있는 제목을 가진 활성 권장 사항이 있는 모든 리소스를 찾을 수 있습니다. 

    :::image type="content" source="media/release-notes/inventory-filter-operators.png" alt-text="자산 인벤토리 필터의 운영자 옵션에 대한 컨트롤":::

[자산 인벤토리를 사용하여 리소스 검색 및 관리](asset-inventory.md)에서 인벤토리에 대해 자세히 알아보세요.


### <a name="recommendation-about-web-apps-requesting-ssl-certificates-no-longer-part-of-secure-score"></a>SSL 인증서를 요청하는 웹앱에 대한 권장 사항은 더 이상 보안 점수에 포함되지 않음

"웹앱에서 들어오는 모든 요청에 대해 SSL 인증서를 요청해야 합니다" 권장 사항이 보안 컨트롤 **액세스 및 권한 관리**(최대 4포인트 상당)에서 **보안 모범 사례 구현**(포인트 가치 없음)으로 이동되었습니다. 

웹앱에서 인증서를 요청하도록 보장하면 더 안전하게 보호할 수 있습니다. 그러나 공용 웹앱의 경우에는 관련이 없습니다. HTTP를 통해 사이트에 액세스하고 HTTPS를 통해서는 액세스하지 않는 경우 클라이언트 인증서가 제공되지 않습니다. 따라서 애플리케이션에 클라이언트 인증서가 필요한 경우 HTTP를 통한 애플리케이션 요청을 허용해서는 안 됩니다. [Azure App Service에 대한 TLS 상호 인증 구성](../app-service/app-service-web-configure-tls-mutual-auth.md)에서 자세히 알아보세요.

이러한 변경으로 권장 사항은 이제 점수에 영향을 주지 않는 권장 모범 사례가 됩니다. 

[보안 컨트롤 및 해당 권장](secure-score-security-controls.md#security-controls-and-their-recommendations)에서 각 보안 제어에 어떤 권장 사항이 있는지 알아보세요.


### <a name="recommendations-page-has-new-filters-for-environment-severity-and-available-responses"></a>권장 사항 페이지에는 환경, 심각도 및 사용 가능한 응답에 대한 새 필터가 있음

Azure Security Center는 연결된 모든 리소스를 모니터링하고 보안 권장 사항을 생성합니다. 이러한 권장 사항을 사용하여 하이브리드 클라우드 상태를 강화하고 조직, 산업 및 국가와 관련된 정책 및 표준 준수 여부를 추적합니다.

Security Center가 범위와 기능을 계속 확장함에 따라 보안 권장 사항 목록이 매월 증가하고 있습니다. 예를 들어, [Azure 보안 벤치마크의 적용 범위를 늘리기 위해 29개의 미리 보기 추천 사항이 추가됨](#29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark)을 참조하세요.

목록이 증가함에 따라 가장 관심 있는 권장 사항을 찾기 위해 해당 권장 사항을 필터링해야 합니다. 11월에는 권장 사항 페이지에 필터를 추가했습니다([이제 추천 목록에 필터가 포함됨](#recommendations-list-now-includes-filters) 참조).

이 달에 추가된 필터는 다음에 따라 추천 목록을 세분화하는 옵션을 제공합니다.

- **환경** - AWS, GCP 또는 Azure 리소스(또는 모든 조합)에 대한 권장 사항 보기
- **심각도** - Security Center에 의해 설정된 심각도 분류에 따른 권장 사항 보기
- **대응 조치** - 다음과 같은 Security Center 대응 옵션에 따른 권장 사항 보기 빠른 수정, 거부 및 적용

    > [!TIP]
    > 대응 조치 필터는 **빠른 수정 사용 가능(예/아니요)** 필터를 대체합니다. 
    > 
    > 각각의 대응 옵션에 대해 자세히 알아보세요.
    > - [빠른 수정](security-center-remediate-recommendations.md#quick-fix-remediation)
    > - [적용/거부 권장 사항을 사용하여 구성 오류 방지](prevent-misconfigurations.md)

:::image type="content" source="./media/release-notes/added-recommendations-filters.png" alt-text="보안 컨트롤별로 그룹화된 권장 사항" lightbox="./media/release-notes/added-recommendations-filters.png":::

### <a name="continuous-export-gets-new-data-types-and-improved-deployifnotexist-policies"></a>연속 내보내기는 새 데이터 형식 및 향상된 deployifnotexist 정책을 가져옴

Azure Security Center의 연속 내보내기 도구를 사용하여 환경의 다른 모니터링 도구와 함께 사용할 Security Center의 권장 사항 및 경고를 내보낼 수 있습니다.

연속 내보내기를 사용하면 내보낼 대상과 위치를 자유롭게 사용자 지정할 수 있습니다. 자세한 내용은 [Security Center 데이터 연속 내보내기](continuous-export.md)를 참조하세요.

이러한 도구는 다음과 같은 방법으로 향상되고 확장되었습니다.

- **연속 내보내기의 deployifnotexist 정책이 향상되었습니다**. 현재 정책은

    - **구성을 사용할 수 있는지 여부를 확인합니다.** 그렇지 않은 경우 정책은 비준수로 표시되고 준수 리소스를 생성합니다. [연속 내보내기 설정](continuous-export.md#set-up-a-continuous-export)의 "Azure Policy를 사용하여 대규모 배포 탭"에서 제공된 Azure Policy 템플릿에 대해 자세히 알아보세요.

    - **보안 결과 내보내기를 지원합니다.** Azure Policy 템플릿을 사용하는 경우 검색 결과를 포함하도록 연속 내보내기를 구성할 수 있습니다. 이는 취약성 평가 스캐너의 검색 결과 또는 '상위' 권장 사항 "시스템 업데이트가 컴퓨터에 설치되어 있어야 합니다."에 대한 특정 시스템 업데이트와 같은 '하위' 권장 사항이 있는 권장 사항을 내보낼 때 관련됩니다.
    
    - **보안 점수 데이터 내보내기를 지원합니다.**

- **규정 준수 평가 데이터(미리 보기 형식)가 추가되었습니다.** 이제 사용자 지정 이니셔티브를 포함하여 규정 준수 평가에 대한 업데이트를 Log Analytics 작업 영역 또는 Event Hub로 지속적으로 내보낼 수 있습니다. 국가/소버린 클라우드에서는 이 기능을 사용할 수 없습니다.

    :::image type="content" source="media/release-notes/continuous-export-regulatory-compliance-option.png" alt-text="연속 내보내기 데이터와 함께 규정 준수 평가 정보를 포함하기 위한 옵션.":::


## <a name="november-2020"></a>2020년 11월

11월의 업데이트는 다음과 같습니다.

- [Azure 보안 벤치마크의 적용 범위를 늘리기 위해 29개의 미리 보기 추천 사항이 추가됨](#29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark)
- [Security Center의 규정 준수 대시보드에 NIST SP 800 171 R2가 추가됨](#nist-sp-800-171-r2-added-to-security-centers-regulatory-compliance-dashboard)
- [이제 추천 목록에 필터가 포함됨](#recommendations-list-now-includes-filters)
- [자동 프로비저닝 환경 향상 및 확장](#auto-provisioning-experience-improved-and-expanded)
- [이제 연속 내보내기(미리 보기)에서 보안 점수를 사용할 수 있습니다.](#secure-score-is-now-available-in-continuous-export-preview)
- ["시스템 업데이트가 컴퓨터에 설치되어 있어야 합니다." 권장 사항이 이제 하위 권장 사항을 포함합니다.](#system-updates-should-be-installed-on-your-machines-recommendation-now-includes-subrecommendations)
- [이제 기본 정책 할당의 상태를 보여주는 Azure Portal의 정책 관리 페이지](#policy-management-page-in-the-azure-portal-now-shows-status-of-default-policy-assignments)

### <a name="29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark"></a>Azure 보안 벤치마크의 적용 범위를 늘리기 위해 29개의 미리 보기 권장 사항 추가됨

Azure 보안 벤치마크는 일반적인 규정 준수 프레임워크를 기반으로 하는 보안 및 규정 준수 모범 사례에 대해 Microsoft에서 작성한 Azure 관련 지침 세트입니다. [Azure 보안 벤치마크에 대해 자세히 알아보세요](../security/benchmarks/introduction.md).

이 벤치마크의 적용 범위를 넓히기 위해 다음 29개의 새로운 추천 사항이 Security Center에 추가되었습니다.

미리 보기 추천 사항은 리소스를 비정상으로 렌더링하지 않으며 보안 점수 계산에 포함되지 않습니다. 미리 보기 기간이 끝나면 점수에 기여할 수 있도록 가능한 경우 언제든지 수정합니다. [Azure Security Center의 추천 사항 수정](security-center-remediate-recommendations.md)에서 이러한 추천 사항에 대응하는 방법에 대해 자세히 알아보세요.

| 보안 컨트롤                     | 새로운 권장 사항                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
|--------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 전송 중인 데이터 암호화              | - PostgreSQL 데이터베이스 서버에 대해 SSL 연결 적용을 사용하도록 설정해야 합니다.<br>- MySQL 데이터베이스 서버에 대해 SSL 연결 적용을 사용하도록 설정해야 합니다.<br>- TLS를 최신 API 앱 버전으로 업데이트해야 합니다.<br>- TLS를 최신 함수 앱 버전으로 업데이트해야 합니다.<br>- TLS를 최신 웹앱 버전으로 업데이트해야 합니다.<br>- API 앱에서 FTPS를 요구해야 합니다.<br>- 함수 앱에서 FTPS를 요구해야 합니다.<br>- 웹앱에서 FTPS를 요구해야 합니다.                                                                                                                                                                                                                                                                                                                                                                                                                           |
| 액세스 및 사용 권한 관리        | - 웹앱에서 들어오는 모든 요청에 대해 SSL 인증서를 요청해야 합니다.<br>- API 앱에서 관리 ID를 사용해야 합니다.<br>- 함수 앱에서 관리 ID를 사용해야 합니다.<br>- 웹앱에서 관리 ID를 사용해야 합니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| 무단 네트워크 액세스 제한 | - 프라이빗 엔드포인트를 PostgreSQL 서버에서 사용할 수 있어야 합니다.<br>- 프라이빗 엔드포인트를 MariaDB 서버에서 사용할 수 있어야 합니다.<br>- 프라이빗 엔드포인트를 MySQL 서버에서 사용할 수 있어야 합니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| 감사 및 로깅 사용          | - App Services에서 진단 로그를 사용하도록 설정해야 합니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| 보안 모범 사례 구현    | - Azure Backup을 가상 머신에 사용하도록 설정해야 합니다.<br>- Azure Database for MariaDB에 대해 지역 중복 백업을 사용하도록 설정해야 합니다.<br>- Azure Database for MySQL에 대해 지역 중복 백업을 사용하도록 설정해야 합니다.<br>- Azure Database for PostgreSQL에 대해 지역 중복 백업을 사용하도록 설정해야 합니다.<br>- PHP를 최신 API 앱 버전으로 업데이트해야 합니다.<br>- PHP를 최신 웹앱 버전으로 업데이트해야 합니다.<br>- Java를 최신 API 앱 버전으로 업데이트해야 합니다.<br>- Java를 최신 함수 앱 버전으로 업데이트해야 합니다.<br>- Java를 최신 웹앱 버전으로 업데이트해야 합니다.<br>- Python을 최신 API 앱 버전으로 업데이트해야 합니다.<br>- Python을 최신 함수 앱 버전으로 업데이트해야 합니다.<br>- Python을 최신 웹앱 버전으로 업데이트해야 합니다.<br>- SQL 서버에 대한 감사 보존 기간은 90일 이상으로 설정해야 합니다. |
|                                      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

관련 링크:

- [Azure 보안 벤치마크에 대한 자세한 정보](../security/benchmarks/introduction.md)
- [Azure API 앱에 대한 자세한 정보](../app-service/app-service-web-tutorial-rest-api.md)
- [Azure 함수 앱에 대한 자세한 정보](../azure-functions/functions-overview.md)
- [Azure 웹앱에 대한 자세한 정보](../app-service/overview.md)
- [Azure Database for MariaDB에 대한 자세한 정보](../mariadb/overview.md)
- [Azure Database for MySQL에 대한 자세한 정보](../mysql/overview.md)
- [Azure Database for PostgreSQL에 대한 자세한 정보](../postgresql/overview.md)


### <a name="nist-sp-800-171-r2-added-to-security-centers-regulatory-compliance-dashboard"></a>Security Center의 규정 준수 대시보드에 NIST SP 800 171 R2가 추가됨

NIST SP 800-171 R2 표준은 이제 Azure Security Center의 규정 준수 대시보드에서 사용할 수 있는 기본 이니셔티브로 제공됩니다. 제어에 대한 매핑은 [NIST SP 800-171 R2 규정 준수 기본 제공 이니셔티브의 세부 정보](../governance/policy/samples/nist-sp-800-171-r2.md)에 설명되어 있습니다. 

표준을 구독에 적용하고 규정 준수 상태를 지속적으로 모니터링하려면 [규정 준수 대시보드의 표준 집합 사용자 지정](update-regulatory-compliance-packages.md)의 지침을 사용합니다.

:::image type="content" source="media/release-notes/nist-sp-800-171-r2-standard.png" alt-text="Security Center 규정 준수 대시보드의 NIST SP 800 171 R2 표준":::

이 규정 준수 표준에 대한 자세한 내용은 [NIST SP 800-171 R2](https://csrc.nist.gov/publications/detail/sp/800-171/rev-2/final)를 참조하세요.


### <a name="recommendations-list-now-includes-filters"></a>이제 추천 목록에 필터가 포함됨

이제 일련의 조건에 따라 보안 추천 목록을 필터링할 수 있습니다. 다음 예에서는 다음과 같은 추천을 보여주기 위해 추천 목록이 필터링되었습니다.

- **일반 공급**(즉, 미리 보기 아님)
- **스토리지 계정** 에 대한 추천
- **빠른 수정** 수정을 지원하는 추천

:::image type="content" source="media/release-notes/recommendations-filters.png" alt-text="추천 목록에 대한 필터":::


### <a name="auto-provisioning-experience-improved-and-expanded"></a>자동 프로비저닝 환경 향상 및 확장

자동 프로비저닝 기능은 신규 및 기존 Azure VM에 필요한 확장을 설치하여 관리 오버헤드를 줄이는 데 도움이 되므로 Security Center의 보호 기능을 활용할 수 있습니다. 

Azure Security Center가 성장함에 따라 더 많은 확장이 개발되었으며 보안 센터에서 더 많은 리소스 유형을 모니터링할 수 있습니다. 자동 프로비저닝 도구는 이제 Azure Policy의 기능을 활용하여 다른 확장 및 리소스 종류를 지원하도록 확장되었습니다.

이제 다음 항목의 자동 프로비저닝을 구성할 수 있습니다.

- Log Analytics 에이전트
- (신규) Kubernetes에 대한 Azure Policy 추가 기능
- (신규) Microsoft Dependency Agent

[Azure Security Center에서 에이전트 및 확장 자동 프로비저닝](security-center-enable-data-collection.md)에서 자세히 알아보세요.


### <a name="secure-score-is-now-available-in-continuous-export-preview"></a>이제 연속 내보내기(미리 보기)에서 보안 점수를 사용할 수 있습니다.

보안 점수를 연속적으로 내보내면 점수 변경 내용을 Azure Event Hubs 또는 Log Analytics 작업 영역에 실시간으로 스트리밍할 수 있습니다. 이 기능을 사용하여 다음을 수행할 수 있습니다.

- 동적 보고서로 시간 경과에 따른 보안 점수 추적
- 보안 점수 데이터를 Azure Sentinel(또는 기타 SIEM)로 내보내기
- 이 데이터를 조직에서 보안 점수를 모니터링하는 데 이미 사용하고 있을 수 있는 프로세스와 통합

[Security Center 데이터를 연속적으로 내보내는](continuous-export.md) 방법에 대해 자세히 알아보세요.


### <a name="system-updates-should-be-installed-on-your-machines-recommendation-now-includes-subrecommendations"></a>"시스템 업데이트가 컴퓨터에 설치되어 있어야 합니다." 권장 사항이 이제 하위 권장 사항을 포함합니다.

**시스템 업데이트가 컴퓨터에 설치되어 있어야 합니다.** 권장 사항이 향상되었습니다. 새 버전에는 누락된 각 업데이트에 대한 하위 권장 사항이 포함되어 있으며 다음과 같은 기능이 향상되었습니다.

- Azure Portal의 Azure Security Center 페이지에서 새롭게 디자인된 환경. **시스템 업데이트는 머신에 설치되어야 합니다** 에 대한 권장 사항 세부 정보 페이지에는 아래와 같은 결과 목록이 포함되어 있습니다. 단일 찾기를 선택하면 재구성 정보 및 영향을 받는 리소스의 목록에 대한 링크가 포함된 세부 정보 창이 열립니다.

    :::image type="content" source="./media/upcoming-changes/system-updates-should-be-installed-subassessment.png" alt-text="업데이트된 권장 사항에 대한 포털 환경에서 하위 권장 사항 중 하나 열기":::

- ARG(Azure Resource Graph)의 권장 사항에 대한 보강 데이터. ARG는 효율적인 리소스 탐색을 제공하도록 디자인된 Azure 서비스입니다. ARG를 사용하여 사용자 환경을 효과적으로 관리할 수 있도록 지정된 구독 세트에서 대규모로 쿼리할 수 있습니다. 

    Azure Security Center의 경우 ARG 및 [KQL(Kusto Query Language)](/azure/data-explorer/kusto/query/)을 사용하여 다양한 보안 태세 데이터를 쿼리할 수 있습니다.

    이전에는 ARG에서 이 권장 사항을 쿼리한 경우 권장 사항을 머신에서 수정해야 한다는 정보만 제공되었습니다. 향상된 버전의 다음 쿼리는 누락된 각 시스템 업데이트를 머신별로 그룹화하여 반환합니다.

    ```kusto
    securityresources
    | where type =~ "microsoft.security/assessments/subassessments"
    | where extract(@"(?i)providers/Microsoft.Security/assessments/([^/]*)", 1, id) == "4ab6e3c5-74dd-8b35-9ab9-f61b30875b27"
    | where properties.status.code == "Unhealthy"
    ```

### <a name="policy-management-page-in-the-azure-portal-now-shows-status-of-default-policy-assignments"></a>이제 기본 정책 할당의 상태를 보여주는 Azure Portal의 정책 관리 페이지

이제 Azure Portal의 Security Center **보안 정책** 페이지에서 구독에 기본 Security Center 정책이 할당되었는지 여부를 확인할 수 있습니다.

:::image type="content" source="media/release-notes/policy-assignment-info-per-subscription.png" alt-text="기본 정책 할당을 보여주는 Azure Security Center의 정책 관리 페이지":::

## <a name="october-2020"></a>2020년 10월

10월의 업데이트는 다음과 같습니다.
- [온-프레미스 및 다중 클라우드 머신의 취약성 평가(미리 보기)](#vulnerability-assessment-for-on-premise-and-multi-cloud-machines-preview)
- [Azure Firewall 권장 사항 추가(미리 보기)](#azure-firewall-recommendation-added-preview)
- [[Kubernetes 서비스에서 권한 있는 IP 범위를 정의해야 함] 권장 사항의 빠른 픽스가 업데이트됨](#authorized-ip-ranges-should-be-defined-on-kubernetes-services-recommendation-updated-with-quick-fix)
- [이제 규정 준수 대시보드에는 표준을 제거하는 옵션이 포함됨](#regulatory-compliance-dashboard-now-includes-option-to-remove-standards)
- [ARG(Azure Resource Graph)에서 Microsoft.Security/securityStatuses 테이블이 제거됨](#microsoftsecuritysecuritystatuses-table-removed-from-azure-resource-graph-arg)

### <a name="vulnerability-assessment-for-on-premise-and-multi-cloud-machines-preview"></a>온-프레미스 및 다중 클라우드 머신의 취약성 평가(미리 보기)

[서버용 Azure Defender](defender-for-servers-introduction.md)의 통합 취약성 평가 스캐너(Qualys 기반)는 이제 Azure Arc 사용 서버를 검색합니다.

Azure가 아닌 머신에서 Azure Arc를 사용하도록 설정한 경우 Security Center는 이러한 머신에 통합 취약성 스캐너를 수동 및 대규모로 배포할 수 있습니다.

이번 업데이트를 통해 모든 Azure 및 비-Azure 자산의 취약성 관리 프로그램을 통합하는 **서버용 Azure Defender** 의 강력한 성능을 발휘할 수 있습니다.

주요 기능:

- Azure Arc 머신의 VA(취약성 평가) 스캐너 프로비저닝 상태 모니터링
- 보호되지 않는 Windows 및 Linux Azure Arc 머신에 통합 VA 에이전트 프로비저닝(수동 및 대규모로)
- 배포된 에이전트에서 탐지된 취약성 수신 및 분석(수동 및 대규모로)
- Azure VM 및 Azure Arc 머신에 대한 통합 환경

[하이브리드 머신에 통합 취약성 스캐너를 배포하는 내용을 자세히 알아보세요](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-scanner-to-your-azure-and-hybrid-machines).

[Azure Arc 사용 서버에 대해 자세히 알아보세요](../azure-arc/servers/index.yml).


### <a name="azure-firewall-recommendation-added-preview"></a>Azure Firewall 권장 사항 추가(미리 보기)

Azure Firewall을 통해 모든 가상 네트워크를 보호하기 위해 새로운 권장 사항이 추가되었습니다.

**가상 네트워크는 Azure Firewall로 보호해야 합니다** 권장 사항은 Azure Firewall을 사용하여 가상 네트워크에 대한 액세스를 제한하고 잠재적 위협을 차단할 것을 조언합니다.

[Azure Firewall](https://azure.microsoft.com/services/azure-firewall/)에 대해 자세히 알아보세요.


### <a name="authorized-ip-ranges-should-be-defined-on-kubernetes-services-recommendation-updated-with-quick-fix"></a>[Kubernetes 서비스에서 권한 있는 IP 범위를 정의해야 함] 권장 사항의 빠른 픽스가 업데이트됨

**Kubernetes 서비스에서 권한 있는 IP 범위를 정의해야 함** 권장 사항은 이제 빠른 픽스 옵션을 제공합니다.

이 권장 사항 및 기타 모든 Security Center 권장 사항에 대한 자세한 내용은 [보안 권장 사항 - 참조 가이드](recommendations-reference.md)를 참조하세요.

:::image type="content" source="./media/release-notes/authorized-ip-ranges-recommendation.png" alt-text="빠른 픽스 옵션을 제공하는 [Kubernetes 서비스에서 권한 있는 IP 범위를 정의해야 함] 권장 사항":::


### <a name="regulatory-compliance-dashboard-now-includes-option-to-remove-standards"></a>이제 규정 준수 대시보드에는 표준을 제거하는 옵션이 포함됨

Security Center의 규정 준수 대시보드는 특정 규정 준수 제어 및 요구 사항을 충족하는 방법에 따라 규정 준수 상태에 대한 인사이트를 제공합니다.

대시보드에는 규정 표준의 기본 세트가 포함되어 있습니다. 제공된 표준이 조직과 관련이 없는 경우 이제 구독에 대한 UI에서 해당 표준을 제거할 수 있는 간단한 프로세스입니다. 표준은 관리 그룹 범위가 아닌 *구독* 수준에서만 제거할 수 있습니다.

[대시보드에서 표준 제거](update-regulatory-compliance-packages.md#remove-a-standard-from-your-dashboard)에서 자세히 알아봅니다.


### <a name="microsoftsecuritysecuritystatuses-table-removed-from-azure-resource-graph-arg"></a>ARG(Azure Resource Graph)에서 Microsoft.Security/securityStatuses 테이블이 제거됨

Azure Resource Graph는 작업 환경을 효과적으로 관리할 수 있도록 특정 구독 세트에서 대규모로 쿼리를 수행할 수 있는 효율적인 리소스 탐색 기능을 제공하도록 디자인된 Azure 서비스입니다. 

Azure Security Center의 경우 ARG 및 [KQL(Kusto Query Language)](/azure/data-explorer/kusto/query/)을 사용하여 다양한 보안 태세 데이터를 쿼리할 수 있습니다. 예를 들어:

- 자산 인벤토리 활용(ARG)
- [MFA(다단계 인증)를 사용하지 않고 계정을 식별](security-center-identity-access.md#identify-accounts-without-multi-factor-authentication-mfa-enabled)하는 방법에 대한 샘플 ARG 쿼리가 문서로 준비되어 있습니다.

ARG 내에는 쿼리에 사용할 수 있는 데이터 테이블이 들어 있습니다.

:::image type="content" source="./media/release-notes/azure-resource-graph-tables.png" alt-text="Azure Resource Graph Explorer 및 사용 가능한 테이블":::

> [!TIP]
> ARG 설명서에는 [Azure Resource Graph 테이블 및 리소스 종류 참조](../governance/resource-graph/reference/supported-tables-resources.md)에서 사용 가능한 모든 테이블이 나열되어 있습니다.

이번 업데이트에서 **Microsoft.Security/securityStatuses** 테이블이 제거되었습니다. securityStatuses API는 계속 사용할 수 있습니다.

데이터 교체는 Microsoft.Security/Assessments에서 사용할 수 있습니다.

Microsoft.Security/securityStatuses는 평가의 집계를 보여주고 Microsoft.Security/Assessments는 각 평가에 대한 단일 레코드를 포함한다는 것이 가장 큰 차이점입니다.

예를 들어 Microsoft.Security/securityStatuses는 다음과 같은 두 개의 policyAssessments 배열이 포함된 결과를 반환합니다.

```
{
id: "/subscriptions/449bcidd-3470-4804-ab56-2752595 felab/resourceGroups/mico-rg/providers/Microsoft.Network/virtualNetworks/mico-rg-vnet/providers/Microsoft.Security/securityStatuses/mico-rg-vnet",
name: "mico-rg-vnet",
type: "Microsoft.Security/securityStatuses",
properties:  {
    policyAssessments: [
        {assessmentKey: "e3deicce-f4dd-3b34-e496-8b5381bazd7e", category: "Networking", policyName: "Azure DDOS Protection Standard should be enabled",...},
        {assessmentKey: "sefac66a-1ec5-b063-a824-eb28671dc527", category: "Compute", policyName: "",...}
    ],
    securitystateByCategory: [{category: "Networking", securityState: "None" }, {category: "Compute",...],
    name: "GenericResourceHealthProperties",
    type: "VirtualNetwork",
    securitystate: "High"
}
```
반면 Microsoft.Security/Assessments는 다음과 같이 각 정책 평가에 대한 레코드를 보유합니다.

```
{
type: "Microsoft.Security/assessments",
id:  "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourceGroups/mico-rg/providers/Microsoft. Network/virtualNetworks/mico-rg-vnet/providers/Microsoft.Security/assessments/e3delcce-f4dd-3b34-e496-8b5381ba2d70",
name: "e3deicce-f4dd-3b34-e496-8b5381ba2d70",
properties:  {
    resourceDetails: {Source: "Azure", Id: "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourceGroups/mico-rg/providers/Microsoft.Network/virtualNetworks/mico-rg-vnet"...},
    displayName: "Azure DDOS Protection Standard should be enabled",
    status: (code: "NotApplicable", cause: "VnetHasNOAppGateways", description: "There are no Application Gateway resources attached to this Virtual Network"...}
}

{
type: "Microsoft.Security/assessments",
id:  "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourcegroups/mico-rg/providers/microsoft.network/virtualnetworks/mico-rg-vnet/providers/Microsoft.Security/assessments/80fac66a-1ec5-be63-a824-eb28671dc527",
name: "8efac66a-1ec5-be63-a824-eb28671dc527",
properties: {
    resourceDetails: (Source: "Azure", Id: "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourcegroups/mico-rg/providers/microsoft.network/virtualnetworks/mico-rg-vnet"...),
    displayName: "Audit diagnostic setting",
    status:  {code: "Unhealthy"}
}
```

**이제 assessments 테이블을 사용하도록 securityStatuses를 사용하여 기존 ARG 쿼리를 변환하는 예제:**

SecurityStatuses를 참조하는 쿼리:

```kusto
SecurityResources 
| where type == 'microsoft.security/securitystatuses' and properties.type == 'virtualMachine'
| where name in ({vmnames}) 
| project name, resourceGroup, policyAssesments = properties.policyAssessments, resourceRegion = location, id, resourceDetails = properties.resourceDetails
```

Assessments 테이블의 대체 쿼리:

```kusto
securityresources
| where type == "microsoft.security/assessments" and id contains "virtualMachine"
| extend resourceName = extract(@"(?i)/([^/]*)/providers/Microsoft.Security/assessments", 1, id)
| extend source = tostring(properties.resourceDetails.Source)
| extend resourceId = trim(" ", tolower(tostring(case(source =~ "azure", properties.resourceDetails.Id,
source =~ "aws", properties.additionalData.AzureResourceId,
source =~ "gcp", properties.additionalData.AzureResourceId,
extract("^(.+)/providers/Microsoft.Security/assessments/.+$",1,id)))))
| extend resourceGroup = tolower(tostring(split(resourceId, "/")[4]))
| where resourceName in ({vmnames}) 
| project resourceName, resourceGroup, resourceRegion = location, id, resourceDetails = properties.additionalData
```

다음 링크에서 자세한 내용을 알아보세요.
- [Azure Resource Graph Explorer를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md)
- [Kusto Query Language(KQL)](/azure/data-explorer/kusto/query/)