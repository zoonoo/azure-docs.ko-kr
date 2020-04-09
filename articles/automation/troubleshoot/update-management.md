---
title: Azure 업데이트 관리를 통해 오류 문제 해결
description: Azure의 업데이트 관리 솔루션으로 문제를 해결하고 해결하는 방법을 알아봅니다.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/17/2020
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: c9ff05591c98fda8be39e32f26da484f56e0831b
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984626"
---
# <a name="troubleshooting-issues-with-update-management"></a>업데이트 관리 문제 해결

이 문서에서는 업데이트 관리를 사용할 때 발생할 수 있는 문제에 대한 해결 방법을 설명합니다.

하이브리드 작업자 에이전트가 근본적인 문제를 확인하는 에이전트 문제 해결사가 있습니다. 이 문제 해결사에 대한 자세한 내용은 [업데이트 에이전트 문제 해결](update-agent-issues.md)을 참조하세요. 기타 모든 문제의 경우 다음 문제 해결 지침을 사용합니다.

가상 시스템(VM)에서 솔루션을 온보딩할 때 문제가 발견되면 로컬 컴퓨터의 응용 프로그램 및 서비스 로그 아래의 **Operations Manager** **로그를 확인하십시오.** 이벤트 ID 4502 및 포함된 이벤트 `Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent`세부 정보가 포함된 이벤트를 찾습니다.

다음 섹션에서는 특정 오류 메시지와 각 오류에 대한 가능한 해결 을 강조 합니다. 기타 온보딩 문제에 대한 [해결 방법은 온보딩](onboarding.md)을 참조하십시오.

## <a name="scenario-you-receive-the-error-failed-to-enable-the-update-solution"></a>시나리오: "업데이트 솔루션을 사용하도록 설정하지 못했습니다" 오류가 발생합니다.

### <a name="issue"></a>문제

자동화 계정에서 업데이트 관리 솔루션을 사용하도록 설정하려고 하면 다음과 같은 오류가 발생합니다.

```error
Error details: Failed to enable the Update solution
```

### <a name="cause"></a>원인

이 오류는 다음과 같은 이유로 발생할 수 있습니다.

* Log Analytics 에이전트의 네트워크 방화벽 요구 사항이 올바르게 구성되지 않았을 수 있습니다. 이 경우 DNS URL을 해결할 때 에이전트가 실패할 수 있습니다.

* 솔루션 대상 기가 잘못 구성 하 고 컴퓨터 예상 대로 업데이트를 받지 않습니다.

* 컴퓨터에 규정 `Non-compliant` **준수**의 상태가 표시될 수도 있습니다. 동시에 **에이전트 데스크톱 분석은** 에이전트를 로 `Disconnected`보고합니다.

### <a name="resolution"></a>해결 방법

* OS에 따라 [Windows](update-agent-issues.md#troubleshoot-offline) 또는 [Linux용](update-agent-issues-linux.md#troubleshoot-offline)문제 해결사를 실행합니다.

* 네트워크 [계획으로](../automation-hybrid-runbook-worker.md#network-planning) 이동하여 업데이트 관리가 작동하려면 허용되는 주소와 포트에 대해 알아봅니다.  

* 네트워크 [계획으로](../../azure-monitor/platform/log-analytics-agent.md#network-requirements) 이동하여 Log Analytics 에이전트가 작동하도록 허용해야 하는 주소와 포트에 대해 알아봅니다.

* 범위 구성 문제를 확인합니다. [범위 구성은](../automation-onboard-solutions-from-automation-account.md#scope-configuration) 솔루션에 대해 구성되는 컴퓨터를 결정합니다. 컴퓨터가 작업 영역에 표시되지만 **업데이트 관리 포털에 표시되지 않는 경우 컴퓨터를 대상으로 하는 범위 구성을 설정해야 합니다. 범위 구성에 대한 자세한 내용은 [작업 영역의 온보드 컴퓨터를](../automation-onboard-solutions-from-automation-account.md#onboard-machines-in-the-workspace)참조하십시오.

* [하이브리드 Runbook 작업자 삭제](../automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker)단계따라 작업자 구성을 제거합니다. 

## <a name="scenario-superseded-update-indicated-as-missing-in-update-management"></a>시나리오: 업데이트 관리에서 누락된 것으로 표시된 대체된 업데이트

### <a name="issue"></a>문제

이전 업데이트가 대체되었음에도 불구하고 자동화 계정에 대해 누락된 것으로 나타나고 있습니다. 대체된 업데이트는 동일한 취약점을 수정하는 이후 업데이트를 사용할 수 있기 때문에 설치할 필요가 없는 업데이트입니다. 업데이트 관리는 대체된 업데이트를 무시하고 대체된 업데이트를 위해 적용할 수 없습니다. 관련 문제에 대한 자세한 내용은 [업데이트가 대체됩니다.](https://docs.microsoft.com/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer)

### <a name="cause"></a>원인

대체된 업데이트는 해당되지 않는 것으로 간주될 수 있도록 거부된 것으로 올바르게 표시되지 않습니다.

### <a name="resolution"></a>해결 방법

대체된 업데이트가 100% 적용되지 않는 경우 해당 업데이트의 승인 상태를 `Declined`로 변경해야 합니다. 모든 업데이트에 대한 승인 상태를 변경하려면 다음을 수행하십시오.

1. 자동화 계정에서 업데이트 **관리를** 선택하여 컴퓨터 상태를 확인합니다. [업데이트 평가 보기를](../manage-update-multi.md#view-an-update-assessment)참조하십시오.

2. 대체된 업데이트를 확인하여 100% 적용되지 않는지 확인합니다. 

3. 업데이트에 대한 질문이 없는 한 업데이트를 거부된 것으로 표시합니다. 

4. **컴퓨터를** 선택하고 규정 준수 열에서 규정 **준수를** 위해 다시 검색하도록 합니다. [여러 컴퓨터의 업데이트 관리를](../manage-update-multi.md)참조하십시오.

5. 다른 대체 된 업데이트에 대 한 위의 단계를 반복 합니다.

6. 정리 마법사를 실행하여 거부된 업데이트에서 파일을 삭제합니다. 

7. WSUS(Windows 서버 업데이트 서비스)의 경우 대체된 모든 업데이트를 수동으로 정리하여 인프라를 새로 고칩니다.

8. 이 절차를 정기적으로 반복하여 디스플레이 문제를 해결하고 업데이트 관리에 사용되는 디스크 공간의 양을 최소화합니다.

## <a name="scenario-machines-dont-show-up-in-the-portal-under-update-management"></a><a name="nologs"></a>시나리오: 업데이트 관리 아래 포털에 컴퓨터가 표시되지 않습니다.

### <a name="issue"></a>문제

컴퓨터에 다음과 같은 증상이 있습니다.

* VM의 `Not configured` 업데이트 관리 보기에서 컴퓨터가 표시됩니다.

* Azure 자동화 계정의 업데이트 관리 보기에서 컴퓨터가 누락되었습니다.

* 규정 `Not assessed` **준수**로 표시 되는 컴퓨터가 있습니다. 그러나 하이브리드 Runbook 작업자에 대 한 Azure 모니터 로그에 하트 비트 데이터가 표시 되지만 업데이트 관리에 대 한.

### <a name="cause"></a>원인

이 문제는 로컬 구성 문제 또는 잘못 구성된 범위 구성으로 인해 발생할 수 있습니다. 가능한 특정 원인은 다음과 같습니다.

* 하이브리드 Runbook 작업자를 다시 등록하고 다시 설치해야 할 수 있습니다.

* 작업 영역에 할당량을 정의하여 도달한 경우 추가 데이터 저장소를 방지할 수 있습니다.

### <a name="resolution"></a>해결 방법

1. OS에 따라 [Windows](update-agent-issues.md#troubleshoot-offline) 또는 [Linux용](update-agent-issues-linux.md#troubleshoot-offline)문제 해결사를 실행합니다.

2. 컴퓨터가 올바른 작업 영역에 보고하고 있는지 확인합니다. 이 측면을 확인하는 방법에 대한 지침은 [로그 분석에 대한 에이전트 연결 확인을](../../azure-monitor/platform/agent-windows.md#verify-agent-connectivity-to-log-analytics)참조하십시오. 또한 이 작업 영역이 Azure 자동화 계정에 연결되어 있는지 확인합니다. 확인하려면 자동화 계정으로 이동하여 **관련 리소스**에서 연결된 **작업 영역을** 선택합니다.

3. 자동화 계정에 연결된 Log Analytics 작업 영역에 컴퓨터가 표시되는지 확인합니다. 로그 분석 작업 영역에서 다음 쿼리를 실행합니다.

   ```kusto
   Heartbeat
   | summarize by Computer, Solutions
   ```

4. 쿼리 결과에 컴퓨터가 표시되지 않으면 최근에 체크 인하지 않은 것입니다. 로컬 구성 문제가 있을 수 있으므로 [에이전트를 다시 설치해야](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows)합니다. 

5. 컴퓨터가 쿼리 결과에 표시되는 경우 범위 구성 문제를 확인합니다. [범위 구성은](../automation-onboard-solutions-from-automation-account.md#scope-configuration) 솔루션에 대해 구성된 컴퓨터를 결정합니다. 

6. 컴퓨터가 작업 영역에 표시되지만 업데이트 관리에 표시되지 않는 경우 컴퓨터를 대상으로 하는 범위 구성을 구성해야 합니다. 이 작업을 수행하는 방법에 대해 알아보려면 [작업 영역의 온보드 컴퓨터를](../automation-onboard-solutions-from-automation-account.md#onboard-machines-in-the-workspace)참조하십시오.

7. 작업 영역에서 이 쿼리를 실행합니다.

   ```kusto
   Operation
   | where OperationCategory == 'Data Collection Status'
   | sort by TimeGenerated desc
   ```

8. `Data collection stopped due to daily limit of free data reached. Ingestion status = OverQuota` 결과를 얻으면 작업 영역에 정의된 할당량에 도달하여 데이터가 저장되지 않도록 합니다. 작업 영역에서 **사용량 및 예상 비용**에서 데이터 볼륨 **관리로** 이동하여 할당량을 변경하거나 제거합니다.

9. 그래도 문제가 해결되지 않으면 [Windows 하이브리드 Runbook 작업자 배포의](../automation-windows-hrw-install.md) 단계를 수행하여 Windows용 하이브리드 워커를 다시 설치합니다. Linux의 경우 Linux [하이브리드 Runbook 작업자 배포의](../automation-linux-hrw-install.md)단계를 따릅니다.

## <a name="scenario-unable-to-register-automation-resource-provider-for-subscriptions"></a><a name="rp-register"></a>시나리오: 구독에 자동화 리소스 공급자를 등록할 수 없음

### <a name="issue"></a>문제

자동화 계정에서 솔루션으로 작업할 때 다음과 같은 오류가 발생합니다.

```error
Error details: Unable to register Automation Resource Provider for subscriptions
```

### <a name="cause"></a>원인

자동화 리소스 공급자가 구독에 등록되지 않았습니다.

### <a name="resolution"></a>해결 방법

자동화 리소스 공급자를 등록하려면 Azure 포털에서 다음 단계를 따릅니다.

1. 포털 맨 아래에 있는 Azure 서비스 목록에서 **모든 서비스를**선택한 다음 일반 서비스 그룹에서 **구독을 선택합니다.**

2. 구독을 선택합니다.

3. **설정에서** **리소스 공급자를**선택합니다.

4. 리소스 공급자 목록에서 Microsoft.Automation 리소스 공급자가 등록되어 있는지 확인합니다.

5. 목록에 없는 경우 [리소스 공급자 등록에 대한 해결 오류의](/azure/azure-resource-manager/resource-manager-register-provider-errors)단계에 따라 Microsoft.Automation 공급자를 등록합니다.

## <a name="scenario-scheduled-update-with-a-dynamic-schedule-missed-some-machines"></a><a name="scheduled-update-missed-machines"></a>시나리오: 동적 일정이 있는 예약된 업데이트가 일부 컴퓨터를 놓쳤습니다.

### <a name="issue"></a>문제

업데이트 미리 보기에 포함된 컴퓨터가 예약된 실행 중에 패치된 컴퓨터 목록에 모두 나타나지는 않습니다.

### <a name="cause"></a>원인

이 문제는 다음 원인 중 하나가 있을 수 있습니다.

* 동적 쿼리의 범위에 정의된 구독은 등록된 자동화 리소스 공급자에 대해 구성되지 않습니다.

* 일정이 실행될 때 컴퓨터를 사용할 수 없거나 적절한 태그가 없습니다.

### <a name="resolution"></a>해결 방법

#### <a name="subscriptions-not-configured-for-registered-automation-resource-provider"></a>등록된 자동화 리소스 공급자를 위해 구성되지 않은 구독

자동화 리소스 공급자에 대해 구독이 구성되지 않은 경우 해당 구독의 컴퓨터에 대한 정보를 쿼리하거나 가져올 수 없습니다. 다음 단계를 사용하여 구독 등록을 확인합니다.

1. Azure [포털에서](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types#azure-portal)Azure 서비스 목록에 액세스합니다.

2. **모든 서비스를**선택한 다음 일반 서비스 그룹에서 **구독을 선택합니다.** 

3. 배포 범위에 정의된 구독을 찾습니다.

4. **설정에서** **리소스 공급자를**선택합니다.

5. Microsoft.Automation 리소스 공급자가 등록되어 있는지 확인합니다.

6. 목록에 없는 경우 [리소스 공급자 등록에 대한 해결 오류의](/azure/azure-resource-manager/resource-manager-register-provider-errors)단계에 따라 Microsoft.Automation 공급자를 등록합니다.

#### <a name="machines-not-available-or-not-tagged-correctly-when-schedule-executed"></a>예약이 실행될 때 컴퓨터를 사용할 수 없거나 올바르게 태그가 지정되지 않음

자동화 리소스 공급자에 대해 구독이 구성되었지만 지정된 [동적 그룹으로](../automation-update-management-groups.md) 업데이트 일정을 실행하면 일부 컴퓨터가 누락된 경우 다음 절차를 사용합니다.

1. Azure 포털에서 자동화 계정을 열고 **업데이트 관리를 선택합니다.**

2. [업데이트 관리 기록을](https://docs.microsoft.com/azure/automation/manage-update-multi#view-results-of-an-update-deployment) 확인하여 업데이트 배포가 실행된 정확한 시간을 확인합니다. 

3. 업데이트 관리에서 누락된 것으로 의심되는 컴퓨터의 경우 ARG(Azure 리소스 그래프)를 사용하여 [컴퓨터 변경 내용을 찾습니다.](https://docs.microsoft.com/azure/governance/resource-graph/how-to/get-resource-changes#find-detected-change-events-and-view-change-details) 

4. 업데이트 배포가 실행되기 전의 하루와 같이 상당한 기간 동안 변경 내용을 검색합니다.

5. 이 기간의 컴퓨터에 대한 변경 내용 삭제 또는 업데이트와 같은 시스템 변경 사항이 있는지 검색 결과를 확인합니다. 이러한 변경 사항은 업데이트가 배포될 때 컴퓨터 목록에서 컴퓨터가 선택되지 않도록 컴퓨터 상태 또는 태그를 변경할 수 있습니다.

6. 컴퓨터 상태 또는 태그 문제를 수정하려면 필요에 따라 컴퓨터 및 리소스 설정을 조정합니다.

7. 업데이트 일정을 다시 실행하여 지정된 동적 그룹이 있는 배포에 모든 컴퓨터가 포함되도록 합니다.

## <a name="scenario-expected-machines-dont-appear-in-preview-for-dynamic-group"></a><a name="machines-not-in-preview"></a>시나리오: 동적 그룹에 대한 예상 컴퓨터가 미리 보기에 나타나지 않습니다.

### <a name="issue"></a>문제

동적 그룹의 선택한 범위에 대한 가상 컴퓨터가 Azure 포털 미리 보기 목록에 표시되지 않습니다. 이 목록은 선택한 범위에 대한 ARG 쿼리에서 검색한 모든 컴퓨터로 구성됩니다. 범위는 하이브리드 Runbook 작업자가 설치되어 있고 액세스 권한이 있는 컴퓨터에 대해 필터링됩니다. 

### <a name="cause"></a>원인
 
이 문제의 가능한 원인은 다음과 같습니다.

* 선택한 범위에 대한 올바른 액세스 권한이 없습니다.
* ARG 쿼리는 예상된 컴퓨터를 검색하지 않습니다.
* 하이브리드 Runbook 작업자가 컴퓨터에 설치되지 않습니다.

### <a name="resolution"></a>해결 방법 

#### <a name="incorrect-access-on-selected-scopes"></a>선택한 범위에 대한 잘못된 액세스

Azure 포털은 지정된 범위에서 쓰기 액세스 권한이 있는 컴퓨터만 표시합니다. 범위에 대한 올바른 액세스 권한이 없는 경우 [자습서: RBAC 및 Azure 포털을 사용하여 Azure 리소스에 대한 사용자 액세스 권한 부여를](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal)참조하십시오.

#### <a name="arg-query-doesnt-return-expected-machines"></a>ARG 쿼리가 예상된 컴퓨터를 반환하지 않습니다.

아래 단계에 따라 쿼리가 제대로 작동하는지 확인합니다.

1. Azure 포털의 리소스 그래프 탐색기 블레이드에서 아래와 같이 서식이 지정된 ARG 쿼리를 실행합니다. 이 쿼리는 업데이트 관리에서 동적 그룹을 만들 때 선택한 필터를 모방합니다. [업데이트 관리를 사용하여 동적 그룹 사용을](https://docs.microsoft.com/azure/automation/automation-update-management-groups)참조하십시오. 

    ```kusto
    where (subscriptionId in~ ("<subscriptionId1>", "<subscriptionId2>") and type =~ "microsoft.compute/virtualmachines" and properties.storageProfile.osDisk.osType == "<Windows/Linux>" and resourceGroup in~ ("<resourceGroupName1>","<resourceGroupName2>") and location in~ ("<location1>","<location2>") )
    | project id, location, name, tags = todynamic(tolower(tostring(tags)))
    | where  (tags[tolower("<tagKey1>")] =~ "<tagValue1>" and tags[tolower("<tagKey2>")] =~ "<tagValue2>") // use this if "All" option selected for tags
    | where  (tags[tolower("<tagKey1>")] =~ "<tagValue1>" or tags[tolower("<tagKey2>")] =~ "<tagValue2>") // use this if "Any" option selected for tags
    | project id, location, name, tags
    ```

   다음은 예제입니다.

    ```kusto
    where (subscriptionId in~ ("20780d0a-b422-4213-979b-6c919c91ace1", "af52d412-a347-4bc6-8cb7-4780fbb00490") and type =~ "microsoft.compute/virtualmachines" and properties.storageProfile.osDisk.osType == "Windows" and resourceGroup in~ ("testRG","withinvnet-2020-01-06-10-global-resources-southindia") and location in~ ("australiacentral","australiacentral2","brazilsouth") )
    | project id, location, name, tags = todynamic(tolower(tostring(tags)))
    | where  (tags[tolower("ms-resource-usage")] =~ "azure-cloud-shell" and tags[tolower("temp")] =~ "temp")
    | project id, location, name, tags
    ```
 
2. 찾고 있는 컴퓨터가 쿼리 결과에 나열되어 있는지 확인합니다. 

3. 컴퓨터가 나열되지 않은 경우 동적 그룹에서 선택한 필터에 문제가 있을 수 있습니다. 필요에 따라 그룹 구성을 조정합니다.

#### <a name="hybrid-runbook-worker-not-installed-on-machines"></a>컴퓨터에 설치되지 않은 하이브리드 Runbook 작업자

컴퓨터는 ARG 쿼리 결과에 나타나지만 동적 그룹 미리 보기에는 표시되지 않습니다. 이 경우 컴퓨터가 하이브리드 작업자로 지정되지 않아 Azure 자동화 및 업데이트 관리 작업을 실행할 수 없습니다. 보려는 컴퓨터가 하이브리드 Runbook 작업자로 설정되어 있는지 확인하려면 다음을 수행합니다.

1. Azure 포털에서 올바르게 나타나지 않는 컴퓨터의 자동화 계정으로 이동합니다.

2. **프로세스 자동화**에서 하이브리드 **작업자 그룹을** 선택합니다.

3. 시스템 **하이브리드 작업자 그룹 탭을** 선택합니다.

4. 하이브리드 작업자가 해당 컴퓨터에 있는지 확인합니다.

5. 컴퓨터가 하이브리드 작업자로 설정되지 않은 경우 [하이브리드 Runbook Worker를 사용하여 데이터 센터 또는 클라우드의 리소스 자동화에서 지침을 사용하여 조정합니다.](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker)

6. 컴퓨터를 하이브리드 Runbook 작업자 그룹에 조인합니다.

7. 미리 보기에 표시되지 않은 모든 컴퓨터에 대해 위의 단계를 반복합니다.

## <a name="scenario-components-for-update-management-solution-enabled-while-vm-continues-to-show-as-being-configured"></a><a name="components-enabled-not-working"></a>시나리오: VM이 구성된 것으로 계속 표시되는 동안 업데이트 관리 솔루션용 구성 요소가 활성화되었습니다.

### <a name="issue"></a>문제

온보딩 후 15분이 지났는데도 가상 머신에 다음 메시지가 계속 표시됩니다.

```error
The components for the 'Update Management' solution have been enabled, and now this virtual machine is being configured. Please be patient, as this can sometimes take up to 15 minutes.
```

### <a name="cause"></a>원인

이 오류는 다음과 같은 이유로 발생할 수 있습니다.

* 자동화 계정과의 통신이 차단되고 있습니다.

* 다른 원본 컴퓨터 아이디와 중복된 컴퓨터 이름이 있습니다. 이 시나리오는 특정 컴퓨터 이름이 있는 VM이 다른 리소스 그룹에서 만들어지고 구독의 동일한 물류 에이전트 작업 영역에 보고하는 경우에 발생합니다.

* 온보던 VM 이미지는 MMA(Microsoft 모니터링 에이전트)가 설치된 시스템 준비(sysprep)로 준비되지 않은 복제된 컴퓨터에서 올 수 있습니다.

### <a name="resolution"></a>해결 방법

VM의 정확한 문제를 파악하려면 자동화 계정에 연결된 Log Analytics 작업 영역에서 다음 쿼리를 실행합니다.

```
Update
| where Computer contains "fillInMachineName"
| project TimeGenerated, Computer, SourceComputerId, Title, UpdateState 
```

#### <a name="communication-with-automation-account-blocked"></a>자동화 계정과의 통신이 차단되었습니다.

네트워크 [계획으로](../automation-update-management.md#ports) 이동하여 업데이트 관리가 작동하려면 허용되는 주소와 포트에 대해 알아봅니다.

#### <a name="duplicate-computer-name"></a>컴퓨터 이름 중복

VM의 이름을 변경하여 해당 환경에서 고유한 이름을 확인합니다.

#### <a name="onboarded-image-from-cloned-machine"></a>복제된 컴퓨터에서 온보기 이미지

복제된 이미지를 사용하는 경우 다른 컴퓨터 이름에 동일한 원본 컴퓨터 ID가 있습니다. 이 경우 다음과 같습니다.

1. 로그 분석 작업 영역에서 VM이 표시되는 경우 `MicrosoftDefaultScopeConfig-Updates` 범위 구성에 대한 저장된 검색에서 VM을 제거합니다. 저장된 검색은 작업 영역의 **일반**에서 찾을 수 있습니다.

2. 다음 cmdlet을 실행합니다.

    ```azurepowershell-interactive
    Remove-Item -Path "HKLM:\software\microsoft\hybridrunbookworker" -Recurse -Force
    ```

3. 실행하여 `Restart-Service HealthService` 상태 서비스를 다시 시작합니다. 이 작업은 키를 다시 만들고 새 UUID를 생성합니다.

4. 이 접근 방식이 작동하지 않으면 먼저 이미지에서 Sysprep을 실행한 다음 MMA를 설치합니다.

## <a name="scenario-you-receive-a-linked-subscription-error-when-you-create-an-update-deployment-for-machines-in-another-azure-tenant"></a><a name="multi-tenant"></a>시나리오: 다른 Azure 테넌트의 컴퓨터에 대한 업데이트 배포를 만들 때 연결된 구독 오류가 발생합니다.

### <a name="issue"></a>문제

다른 Azure 테넌트에서 컴퓨터에 대한 업데이트 배포를 만들려고 할 때 다음과 같은 오류가 발생합니다.

```error
The client has permission to perform action 'Microsoft.Compute/virtualMachines/write' on scope '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Automation/automationAccounts/automationAccountName/softwareUpdateConfigurations/updateDeploymentName', however the current tenant '00000000-0000-0000-0000-000000000000' is not authorized to access linked subscription '00000000-0000-0000-0000-000000000000'.
```

### <a name="cause"></a>원인

이 오류는 업데이트 배포에 포함된 다른 테넌트에 Azure VM이 있는 업데이트 배포를 만들 때 발생합니다.

### <a name="resolution"></a>해결 방법

다음 해결 방법을 사용하여 이러한 항목을 예약합니다. [새-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) cmdlet 매개 변수를 `ForUpdate` 사용 하 여 일정을 만들 수 있습니다. 그런 다음 [New-AzureRmAutomationSoftwareConfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) cmdlet을 사용하고 다른 테넌트의 `NonAzureComputer` 컴퓨터를 매개 변수에 전달합니다. 다음 예제에 이 작업을 수행하는 방법이 나와 있습니다.

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$s = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName $aa -Schedule $s -Windows -AzureVMResourceId $azureVMIdsW -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="scenario-unexplained-reboots"></a><a name="node-reboots"></a>시나리오: 설명할 수 없는 재부팅

### <a name="issue"></a>문제

**재부팅 제어** 옵션을 **다시 부팅 안**됨으로 설정했지만 업데이트가 설치된 후에도 컴퓨터가 여전히 재부팅됩니다.

### <a name="cause"></a>원인

Windows 업데이트는 여러 레지스트리 키에 의해 수정할 수 있으며, 이 중 어느 것이든 재부팅 동작을 수정할 수 있습니다.

### <a name="resolution"></a>해결 방법

다시 시작을 관리하는 데 사용되는 레지스트리 및 레지스트리 [키를 편집하여 자동 업데이트 구성](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry) 아래에 나열된 레지스트리 [키를](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) 검토하여 컴퓨터가 제대로 구성되었는지 확인합니다.

## <a name="scenario-machine-shows-failed-to-start-in-an-update-deployment"></a><a name="failed-to-start"></a>시나리오: 업데이트 배포에서 컴퓨터가 "시작 실패"를 표시합니다.

### <a name="issue"></a>문제

컴퓨터가 상태를 `Failed to start` 표시합니다. 컴퓨터에 대한 특정 세부 정보를 보면 다음과 같은 오류가 표시됩니다.

```error
Failed to start the runbook. Check the parameters passed. RunbookName Patch-MicrosoftOMSComputer. Exception You have requested to create a runbook job on a hybrid worker group that does not exist.
```

### <a name="cause"></a>원인

이 오류는 다음 이유 중 하나로 인해 발생할 수 있습니다.

* 컴퓨터가 더 이상 존재하지 않습니다.
* 기기가 꺼져 있고 연결할 수 없습니다.
* 컴퓨터에 네트워크 연결 문제가 있으므로 컴퓨터의 하이브리드 작업자에 연결할 수 없습니다.
* 원본 컴퓨터 ID를 변경한 MMA에 대한 업데이트가 있었습니다.
* 자동화 계정에서 2000개의 동시 작업 제한을 초과하면 업데이트 실행이 제한됩니다. 각 배포는 작업으로 간주되며 업데이트 배포의 각 컴퓨터는 작업으로 계산됩니다. 현재 자동화 계정에서 실행 중인 다른 모든 자동화 작업 또는 업데이트 배포는 동시 작업 제한에 해당합니다.

### <a name="resolution"></a>해결 방법

해당하는 경우 업데이트 배포에 [동적 그룹을](../automation-update-management-groups.md) 사용합니다. 또한 다음 단계를 수행할 수 있습니다.

1. 기기가 여전히 존재하고 연결할 수 있는지 확인합니다. 
2. 컴퓨터가 없는 경우 배포를 편집하고 컴퓨터를 제거합니다.
3. 업데이트 관리에 필요한 포트 및 주소 목록은 [네트워크 계획](../automation-update-management.md#ports) 섹션을 참조한 다음 컴퓨터가 이러한 요구 사항을 충족하는지 확인합니다.
4. 하이브리드 Runbook 작업자 에이전트 문제 해결사를 사용하여 하이브리드 Runbook 작업자에 대한 연결을 확인합니다. 이 문제 해결사에 대한 자세한 내용은 [업데이트 에이전트 문제 해결](update-agent-issues.md)을 참조하세요.
5. 로그 애널리틱스에서 다음 쿼리를 실행하여 원본 컴퓨터 ID가 변경된 환경에서 컴퓨터를 찾습니다. 값은 같지만 `Computer` 값은 다른 `SourceComputerId` 컴퓨터를 찾습니다.

   ```kusto
   Heartbeat | where TimeGenerated > ago(30d) | distinct SourceComputerId, Computer, ComputerIP
   ```

6. 영향을 받는 컴퓨터를 찾은 후 해당 컴퓨터를 대상으로 하는 업데이트 배포를 편집한 `SourceComputerId` 다음 올바른 값을 반영되도록 제거하고 읽습니다.

## <a name="scenario-updates-are-installed-without-a-deployment"></a><a name="updates-nodeployment"></a>시나리오: 업데이트가 배포 없이 설치됩니다.

### <a name="issue"></a>문제

업데이트 관리에 Windows 컴퓨터를 등록하면 배포 없이 업데이트가 설치됩니다.

### <a name="cause"></a>원인

Windows에서 업데이트는 사용 가능한 즉시 자동으로 설치됩니다. 이 동작은 컴퓨터에 배포할 업데이트를 예약하지 않은 경우 혼동을 일으킬 수 있습니다.

### <a name="resolution"></a>해결 방법

`HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU` 레지스트리 키기본값은 4: `auto download and install`입니다.

업데이트 관리 클라이언트의 경우 이 키를 `auto download but do not auto install`3으로 설정하는 것이 좋습니다.

자세한 내용은 [자동 업데이트 구성을](https://docs.microsoft.com/windows/deployment/update/waas-wu-settings#configure-automatic-updates)참조하십시오.

## <a name="scenario-machine-is-already-registered-to-a-different-account"></a><a name="machine-already-registered"></a>시나리오: 컴퓨터가 이미 다른 계정에 등록되어 있음

### <a name="issue"></a>문제

다음과 같은 오류 메시지가 표시됩니다.

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

### <a name="cause"></a>원인

컴퓨터가 업데이트 관리를 위해 다른 작업 영역에 이미 온보로 추가되었습니다.

### <a name="resolution"></a>해결 방법

1. 컴퓨터가 올바른 작업 영역에 보고하고 있는지 확인하기 위해 [업데이트 관리 아래의 포털에 표시되지 않는 컴퓨터의](#nologs) 단계를 따릅니다.
2. [하이브리드 Runbook 그룹을 삭제하여](../automation-hybrid-runbook-worker.md#remove-a-hybrid-worker-group)컴퓨터에서 아티팩트를 정리한 다음 다시 시도합니다.

## <a name="scenario-machine-cant-communicate-with-the-service"></a><a name="machine-unable-to-communicate"></a>시나리오: 컴퓨터가 서비스와 통신할 수 없습니다.

### <a name="issue"></a>문제

다음과 같은 오류 메시지 중 하나가 표시됩니다.

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.Net.Http.HttpRequestException: An error occurred while sending the request. ---> System.Net.WebException: The underlying connection was closed: An unexpected error occurred on a receive. ---> System.ComponentModel.Win32Exception: The client and server can't communicate, because they do not possess a common algorithm
```

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception Newtonsoft.Json.JsonReaderException: Error parsing positive infinity value.
```

```error
The certificate presented by the service <wsid>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.
```

```error
Access is denied. (Exception form HRESULT: 0x80070005(E_ACCESSDENIED))
```

### <a name="cause"></a>원인

프록시, 게이트웨이 또는 방화벽이 네트워크 통신을 차단하고 있을 수 있습니다. 

### <a name="resolution"></a>해결 방법

네트워킹을 검토하고 적절한 포트와 주소가 허용되는지 확인합니다. 업데이트 관리 및 하이브리드 Runbook 작업자가 요구하는 포트 및 주소 목록에 대한 [네트워크 요구 사항을](../automation-hybrid-runbook-worker.md#network-planning) 참조하십시오.

## <a name="scenario-unable-to-create-self-signed-certificate"></a><a name="unable-to-create-selfsigned-cert"></a>시나리오: 자체 서명된 인증서를 만들 수 없음

### <a name="issue"></a>문제

다음과 같은 오류 메시지 중 하나가 표시됩니다.

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

### <a name="cause"></a>원인

하이브리드 Runbook 작업자가 자체 서명된 인증서를 생성할 수 없습니다.

### <a name="resolution"></a>해결 방법

시스템 계정이 **C:\ProgramData\Microsoft\Crypto\RSA** 폴더에 대한 읽기 액세스 권한을 가지고 있는지 확인하고 다시 시도하십시오.

## <a name="scenario-the-scheduled-update-failed-with-a-maintenancewindowexceeded-error"></a><a name="mw-exceeded"></a>시나리오: 예약된 업데이트가 유지 관리창초과 오류로 실패했습니다.

### <a name="issue"></a>문제

업데이트의 기본 유지 관리 기간은 120분입니다. 유지 관리 기간을 최대 6시간 또는 360분으로 늘릴 수 있습니다.

### <a name="resolution"></a>해결 방법

실패한 예약된 업데이트 배포를 편집하고 유지 관리 기간을 늘립니다.

유지 관리 창에 대한 자세한 내용은 [업데이트 설치를](../automation-tutorial-update-management.md#schedule-an-update-deployment)참조하십시오.

## <a name="scenario-machine-shows-as-not-assessed-and-shows-an-hresult-exception"></a><a name="hresult"></a>시나리오: 컴퓨터가 "평가되지 않음"으로 표시되고 HRESULT 예외가 표시됩니다.

### <a name="issue"></a>문제

* 규정 `Not assessed` **준수로**표시되는 컴퓨터가 있고 그 아래에 예외 메시지가 표시됩니다.
* 포털에 HRESULT 오류 코드가 표시됩니다.

### <a name="cause"></a>원인

업데이트 에이전트(Windows의 Windows 업데이트 에이전트, Linux 배포판의 패키지 관리자)가 올바르게 구성되지 않았습니다. 업데이트 관리는 컴퓨터의 업데이트 에이전트를 사용하여 필요한 업데이트, 패치 상태 및 배포된 패치 결과를 제공합니다. 이 정보가 없으면 업데이트 관리에서 필요하거나 설치된 패치를 제대로 보고할 수 없습니다.

### <a name="resolution"></a>해결 방법

컴퓨터에서 로컬로 업데이트를 수행해 보십시오. 이 작업이 실패하면 일반적으로 업데이트 에이전트 구성 오류가 있음을 의미합니다.

이 문제는 네트워크 구성 및 방화벽 문제로 인해 자주 발생합니다. 다음 검사를 사용하여 문제를 해결합니다.

* Linux의 경우 적절한 설명서를 확인하여 패키지 리포지토리의 네트워크 끝점에 도달할 수 있는지 확인합니다.

* Windows의 경우 업데이트에 나열된 에이전트 [구성이 인트라넷 끝점(WSUS/SCCM)에서 다운로드되지 않는지](/windows/deployment/update/windows-update-troubleshooting#updates-arent-downloading-from-the-intranet-endpoint-wsussccm)확인합니다.

  * 컴퓨터가 Windows 업데이트에 대해 구성된 경우 [HTTP/프록시와 관련된 문제에](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy)설명된 끝점에 도달할 수 있는지 확인합니다.
  * 컴퓨터가 WSUS(Windows 서버 업데이트 서비스)에 대해 구성된 경우 [WUServer 레지스트리 키로](/windows/deployment/update/waas-wu-settings)구성된 WSUS 서버에 연결할 수 있는지 확인합니다.

HRESULT가 표시되면 빨간색으로 표시된 예외를 두 번 클릭하여 전체 예외 메시지를 확인합니다. 잠재적인 솔루션 이나 권장 된 작업에 대 한 다음 표를 검토 합니다.

|예외  |해결 또는 작업  |
|---------|---------|
|`Exception from HRESULT: 0x……C`     | Windows 업데이트 오류 [코드 목록에서](https://support.microsoft.com/help/938205/windows-update-error-code-list) 관련 오류 코드를 검색하여 예외원인에 대한 추가 세부 정보를 찾습니다.        |
|`0x8024402C`</br>`0x8024401C`</br>`0x8024402F`      | 이는 네트워크 연결 문제를 나타냅니다. 컴퓨터가 업데이트 관리에 대한 네트워크 연결이 있는지 확인합니다. 필요한 포트 및 주소 목록은 [네트워크 계획](../automation-update-management.md#ports) 섹션을 참조하십시오.        |
|`0x8024001E`| 서비스 또는 시스템이 종료되었기 때문에 업데이트 작업이 완료되지 않았습니다.|
|`0x8024002E`| Windows 업데이트 서비스가 비활성화되어 있습니다.|
|`0x8024402C`     | WSUS 서버를 사용하는 경우 레지스트리 키의 레지스트리 `WUServer` 값이 `WUStatusServer` `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate` 올바른 WSUS 서버를 지정하는지 확인합니다.        |
|`0x80072EE2`|네트워크 연결 문제 또는 구성된 WSUS 서버와 대화할 때 문제가 있습니다. WSUS 설정을 확인하고 클라이언트에서 서비스에 액세스할 수 있는지 확인합니다.|
|`The service cannot be started, either because it is disabled or because it has no enabled devices associated with it. (Exception from HRESULT: 0x80070422)`     | Windows 업데이트 서비스(wuauserv)가 실행 중이고 비활성화되지 않았는지 확인합니다.        |
|`0x80070005`| 액세스 거부 오류는 다음 중 하나로 인해 발생할 수 있습니다.<br> 감염된 컴퓨터<br> Windows 업데이트 설정이 올바르게 구성되지 않음<br> %WinDir%\소프트웨어 배포 폴더가 있는 파일 권한 오류<br> 시스템 드라이브의 디스크 공간이 부족합니다(C:).
|다른 제네릭 예외     | 인터넷에서 가능한 솔루션을 검색하고 로컬 IT 지원팀에 문의하십시오.         |

**%Windir%\Windowsupdate.log** 파일을 검토하면 가능한 원인을 파악하는 데도 도움이 될 수 있습니다. 로그를 읽는 방법에 대한 자세한 내용은 [Windowsupdate.log 파일을 읽는 방법을](https://support.microsoft.com/help/902093/how-to-read-the-windowsupdate-log-file)참조하십시오.

[또한 Windows 업데이트 문제 해결사를](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) 다운로드하여 실행하여 컴퓨터에서 Windows 업데이트와 관련된 문제를 확인할 수도 있습니다.

> [!NOTE]
> [Windows 업데이트 문제 해결사](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) 설명서는 Windows 클라이언트에서 사용하기 위한 것이지만 Windows 서버에서도 작동합니다.

## <a name="scenario-update-run-returns-failed-status-linux"></a>시나리오: 업데이트 실행 반환 실패 상태 (리눅스)

### <a name="issue"></a>문제

업데이트 실행이 시작되지만 실행 중에 오류가 발생합니다.

### <a name="cause"></a>원인

가능한 원인:

* 패키지 관리자가 비정상입니다.
* 업데이트 에이전트 (윈도 즈에 대 한 WUA, 리눅스에 대 한 배포판 관련 패키지 관리자) 잘못 구성.
* 특정 패키지가 클라우드 기반 패치를 방해하고 있습니다.
* 기기에 연결할 수 없습니다.
* 업데이트에는 해결되지 않은 종속성이 있었습니다.

### <a name="resolution"></a>해결 방법

업데이트가 성공적으로 시작된 후 업데이트 실행 중에 오류가 발생하는 경우 실행 중 영향을 받는 컴퓨터의 [작업 출력을 확인합니다.](../manage-update-multi.md#view-results-of-an-update-deployment) 컴퓨터에서 조사하고 조치를 취할 수 있는 특정 오류 메시지를 찾을 수 있습니다. 업데이트 관리에서 업데이트 배포에 성공하려면 패키지 관리자가 정상 상태여야 합니다.

작업이 실패하기 직전에 특정 패치, 패키지 또는 업데이트가 표시되는 경우 다음 업데이트 배포에서 이러한 항목을 [제외해](../automation-tutorial-update-management.md#schedule-an-update-deployment) 볼 수 있습니다. Windows 업데이트에서 로그 정보를 수집하려면 [Windows 업데이트 로그 파일을](/windows/deployment/update/windows-update-logs)참조하십시오.

패치 문제를 해결할 수 없는 경우 **/var/opt/microsoft/omsagent/run/automationworker/omsupdatemgmt.log** 파일의 복사본을 만들고 다음 업데이트 배포가 시작되기 전에 문제 해결을 위해 보존합니다.

## <a name="patches-arent-installed"></a>패치가 설치되지 않았습니다.

### <a name="machines-dont-install-updates"></a>머신이 업데이트를 설치하지 않음

머신에서 직접 업데이트를 실행해 봅니다. 컴퓨터에서 업데이트를 적용할 수 없는 경우 [문제 해결 가이드의 잠재적오류 목록을](https://docs.microsoft.com/azure/automation/troubleshoot/update-management#hresult)참조하십시오.

업데이트가 로컬에서 실행되는 경우 [업데이트 관리에서 VM 제거의](https://docs.microsoft.com/azure/automation/automation-onboard-solutions-from-browse#clean-up-resources)지침에 따라 컴퓨터에서 에이전트를 제거하고 다시 설치해 보십시오.

### <a name="i-know-updates-are-available-but-they-dont-show-as-available-on-my-machines"></a>업데이트를 사용할 수 있지만 컴퓨터에서 사용할 수 있는 것으로 표시되지 않습니다.

이는 컴퓨터가 WSUS 또는 Microsoft 엔드포인트 구성 관리자로부터 업데이트를 얻도록 구성되었지만 WSUS 및 구성 관리자가 업데이트를 승인하지 않은 경우에 종종 발생합니다.

이 문서의 레지스트리 섹션을 `UseWUServer` [편집하여 자동 업데이트 구성에서](https://support.microsoft.com/help/328010/how-to-configure-automatic-updates-by-using-group-policy-or-registry-s)레지스트리 키를 레지스트리 키에 상호 참조하여 컴퓨터가 WSUS 및 SCCM에 대해 구성되어 있는지 확인할 수 있습니다.

WSUS에서 업데이트가 승인되지 않으면 설치되지 않은 것입니다. 다음 쿼리를 실행하여 Log Analytics에서 승인되지 않은 업데이트를 확인할 수 있습니다.

  ```kusto
  Update | where UpdateState == "Needed" and ApprovalSource == "WSUS" and Approved == "False" | summarize max(TimeGenerated) by Computer, KBID, Title
  ```

### <a name="updates-show-as-installed-but-i-cant-find-them-on-my-machine"></a>업데이트가 설치된 것으로 표시되지만, 머신에서 업데이트를 찾을 수 없음

업데이트가 종종 다른 업데이트로 대체됩니다. 자세한 내용은 Windows 업데이트 문제 해결 가이드에서 [업데이트가 대체되는](https://docs.microsoft.com/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer) 것을 참조하세요.

### <a name="installing-updates-by-classification-on-linux"></a>Linux에서 분류를 기준으로 업데이트 설치

분류("중요 업데이트 및 보안 업데이트")를 기준으로 Linux에 업데이트를 배포할 때, 특히 CentOS와 관련하여 중요한 주의 사항이 있습니다. 이러한 제한 사항은 [업데이트 관리 개요 페이지에](https://docs.microsoft.com/azure/automation/automation-update-management#linux-2)설명되어 있습니다.

### <a name="kb2267602-is-consistently-missing"></a>KB2267602가 지속적으로 누락되었습니다.

KB2267602는 [Windows Defender 정의 업데이트](https://www.microsoft.com/wdsi/definitions)이며 매일 업데이트됩니다.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 시도하여 추가 지원을 요청하세요.

* Azure 포럼을 통해 Azure 전문가의 답변을 얻을 [수 있습니다.](https://azure.microsoft.com/support/forums/)
* [@AzureSupport](https://twitter.com/azuresupport)연결합니다.
* Azure 지원 인시던트 제출 [Azure 지원 사이트로](https://azure.microsoft.com/support/options/) 이동하여 **지원 받기를**선택합니다.
