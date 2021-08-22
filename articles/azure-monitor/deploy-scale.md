---
title: Azure Policy를 사용하여 대규모로 Azure Monitor 배포
description: Azure Policy를 사용하여 대규모로 Azure Monitor 기능을 배포합니다.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/08/2020
ms.openlocfilehash: 63da1c8f36f9e2db9593256a071d71ac70ea18bd
ms.sourcegitcommit: 351279883100285f935d3ca9562e9a99d3744cbd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/19/2021
ms.locfileid: "112380035"
---
# <a name="deploy-azure-monitor-at-scale-using-azure-policy"></a>Azure Policy를 사용하여 대규모로 Azure Monitor 배포
한 번 또는 제한된 횟수만 구성하는 Azure Monitor 기능도 있고, 모니터링하려는 각 리소스에 대해 구성을 반복해야 하는 기능도 있습니다. 이 문서에서는 모니터링이 모든 Azure 리소스에 대해 일관되고 정확하게 구성되도록 Azure Policy를 사용하여 대규모로 Azure Monitor를 구현하는 방법을 설명합니다.

예를 들어, 모든 기존 Azure 리소스 및 새로 만든 각 리소스에 대한 진단 설정을 만들어야 합니다. 또한 가상 머신을 만들 때마다 에이전트를 설치하고 구성해야 합니다. PowerShell 또는 CLI와 같은 방법은 Azure Monitor 모든 기능에 사용할 수 있기 때문에 이러한 작업을 수행하는 데도 사용할 수 있습니다. Azure Policy를 사용하면 리소스를 만들거나 수정할 때마다 적절한 구성을 자동으로 수행하는 논리를 적용할 수 있습니다.


## <a name="azure-policy"></a>Azure Policy
이 섹션에서는 최소한의 노력으로 전체 Azure 구독 또는 관리 그룹에서 조직 표준을 평가하고 적용할 수 있는 [Azure Policy](../governance/policy/overview.md)를 간략히 소개합니다. 자세한 내용은 [Azure Policy 설명서](../governance/policy/overview.md)를 참조하세요.

Azure Policy를 사용하면 생성된 모든 리소스에 대한 구성 요구 사항을 지정하고, 규정을 준수하지 않는 리소스를 식별하거나, 리소스 생성을 차단하거나, 필요한 구성을 추가할 수 있습니다. 이러한 작업은 새 리소스를 만들거나 기존 리소스를 수정하는 호출을 가로채서 진행됩니다. 정책 정의에서 예상되는 속성과 일치하지 않는 경우 요청을 거부하거나, 비준수에 대해 플래그를 지정하거나, 관련 리소스를 배포하는 등의 방법으로 이러한 결과에 대응할 수 있습니다. **deployIfNotExists** 또는 **modify** 정책 정의를 통해 기존 리소스를 수정할 수 있습니다.

Azure Policy는 다음 표의 개체로 구성됩니다. 각각에 대한 자세한 설명은 [Azure Policy 개체](../governance/policy/overview.md#azure-policy-objects)를 참조하세요.

| 항목 | Description |
|:---|:---|
| 정책 정의 | 리소스 규정 준수 조건과 이러한 조건이 충족될 때 나타나는 영향을 설명합니다. 특정 형식의 모든 리소스이거나 특정 속성과 일치하는 리소스일 수 있습니다. 영향은 단순히 규정 준수를 위해 리소스에 플래그를 지정하거나 관련 리소스를 배포하는 것일 수 있습니다. 정책 정의는 [Azure Policy 정의 구조](../governance/policy/concepts/definition-structure.md)에 설명된 대로 JSON을 사용하여 작성됩니다. 영향은 [Azure Policy 영향 이해](../governance/policy/concepts/effects.md)에 설명되어 있습니다.
| 정책 이니셔티브 | 함께 적용해야 하는 정책 정의 그룹입니다. 예를 들어, Log Analytics 작업 영역에 리소스 로그를 보내는 정책 정의와 이벤트 허브에 리소스 로그를 보내는 정책 정의가 있을 수 있습니다. 이러한 두 가지 정책 정의를 모두 포함하는 이니셔티브를 만들고, 해당 이니셔티브를 개별 정책 정의 대신 리소스에 적용합니다. 이니셔티브는 [Azure Policy 이니셔티브 구조](../governance/policy/concepts/initiative-definition-structure.md)에 설명된 대로 JSON을 사용하여 작성됩니다. |
| 할당 | 정책 정의 또는 이니셔티브는 먼저 범위에 할당해야만 적용됩니다. 예를 들어, 리소스 그룹에 정책을 할당하여 해당 리소스에서 만든 모든 리소스에 적용하거나 구독에 적용하여 해당 구독의 모든 리소스에 적용합니다.  자세한 내용은 [Azure Policy 할당 구조](../governance/policy/concepts/assignment-structure.md)를 참조하세요. |

## <a name="built-in-policy-definitions-for-azure-monitor"></a>Azure Monitor에 대한 기본 제공 정책 정의
Azure Policy에는 Azure Monitor와 관련된 몇 가지 미리 작성된 정의가 포함되어 있습니다. 이러한 정책 정의를 기존 구독에 할당하거나 고유한 사용자 지정 정의를 만드는 기준으로 사용할 수 있습니다. **모니터링** 범주의 기본 제공 정책 정책의 전체 목록은 [Azure Monitor에 대한 Azure Policy 기본 제공 정책 정의](.//policy-reference.md)를 참조하세요.

모니터링과 관련된 기본 제공 정책 정의를 보려면 다음을 수행합니다.

1. Azure Portal에서 **Azure Policy** 로 이동합니다.
2. **정의** 를 선택합니다.
3. **형식** 에서 ‘기본 제공’을 선택하고 **범주** 에 대해 ‘모니터링’을 선택합니다.

  ![모니터링 범주 및 기본 제공 형식에 대한 정책 정의 목록을 보여 주는 Azure Portal의 Azure Policy 정의 페이지 스크린샷](media/deploy-scale/builtin-policies.png)

## <a name="azure-monitor-agent"></a>Azure Monitor 에이전트
[Azure Monitor 에이전트](agents/azure-monitor-agent-overview.md)는 Azure 가상 머신의 게스트 운영 체제에서 모니터링 데이터를 수집하여 이를 Azure Monitor에 전달합니다. [데이터 수집 규칙](agents/data-collection-rule-overview.md)을 사용하여 각 에이전트에서 수집할 데이터를 구성합니다. 이를 통해 수집 설정을 대규모로 관리하면서도 계속해서 머신의 하위 세트에 대해 고유하고 범위가 지정된 구성을 얻을 수 있습니다.  
가상 머신을 만들 때마다 아래 정책 및 정책 이니셔티브를 사용하여 에이전트를 자동으로 설치하고 데이터 수집 규칙에 연결합니다.

### <a name="built-in-policy-initiatives"></a>기본 제공 정책 이니셔티브
[여기](agents/azure-monitor-agent-install.md#prerequisites)에서 에이전트 설치에 대한 필수 구성을 확인합니다. 

다음을 수행하는 개별 정책으로 구성된 Windows 및 Linux 가상 머신에 대한 정책 이니셔티브가 있습니다.
- 가상 머신에 Azure Monitor 에이전트 확장 설치
- 연결을 만들고 배포하여 가상 머신을 데이터 수집 규칙에 연결

  ![Azure Monitor 에이전트를 구성하기 위한 두 가지 기본 제공 정책 이니셔티브를 보여 주는 Azure Policy 정의 페이지의 부분 스크린샷](media/deploy-scale/built-in-ama-dcr-initiatives.png)  

### <a name="built-in-policy"></a>기본 제공 정책  
각 정책 이니셔티브에서 필요에 따라 개별 정책을 사용하도록 선택할 수 있습니다. 예를 들어, 에이전트를 자동으로 설치하려는 경우 아래와 같이 이니셔티브의 첫 번째 정책을 사용하기만 하면 됩니다.  

  ![Azure Monitor 에이전트를 구성하기 위한 이니셔티브 내에 포함된 정책을 보여 주는 Azure Policy 정의 페이지의 부분 스크린샷](media/deploy-scale/built-in-ama-dcr-policy.png)  

### <a name="remediation"></a>수정
이니셔티브 또는 정책은 생성될 때 각 가상 머신에 적용됩니다. [수정 작업](../governance/policy/how-to/remediate-resources.md)은 이니셔티브의 정책 정의를 **기존 리소스** 에 배포하므로 이미 생성된 모든 리소스에 대해 Azure Monitor 에이전트를 구성할 수 있습니다. Azure Portal을 사용하여 할당을 만들면서 동시에 수정 작업을 만들 수 있습니다. 수정에 대한 자세한 내용은 [Azure Policy을 사용하여 비준수 리소스 수정](../governance/policy/how-to/remediate-resources.md)을 참조하세요.

![AMA에 대한 이니셔티브 수정](media/deploy-scale/built-in-ama-dcr-remediation.png)


## <a name="diagnostic-settings"></a>진단 설정
[진단 설정](essentials/diagnostic-settings.md)은 Azure 리소스에서 여러 위치로 리소스 로그 및 메트릭을 수집하며, 일반적으로 [로그 쿼리](logs/log-query-overview.md) 및 [로그 경고](alerts/alerts-log.md)를 사용하여 데이터를 분석할 수 있는 Log Analytics 작업 영역으로 수집합니다. Policy를 사용하여 리소스를 만들 때마다 진단 설정을 자동으로 만듭니다.

각 Azure 리소스 종류에는 진단 설정에 나열해야 하는 고유한 범주 세트가 있습니다. 이 때문에 각 리소스 종류에는 별도의 정책 정의가 필요합니다. 일부 리소스 종류에는 수정 없이 할당할 수 있는 기본 제공 정책 정의가 있습니다. 다른 리소스 종류의 경우 사용자 지정 정의를 만들어야 합니다.

### <a name="built-in-policy-definitions-for-azure-monitor"></a>Azure Monitor에 대한 기본 제공 정책 정의
각 리소스 종류에 대한 두 가지 기본 제공 정책 정의가 있습니다. 하나는 Log Analytics 작업 영역으로, 다른 하나는 이벤트 허브로 전송됩니다. 위치가 하나만 필요한 경우 리소스 종류에 대해 해당 정책을 할당합니다. 둘 다 필요한 경우 리소스에 대한 두 정책 정의를 모두 할당합니다.

예를 들어, 다음 이미지는 Data Lake Analytics에 대한 기본 제공 진단 설정 정책 정의를 보여 줍니다.

  ![Data Lake Analytics에 대한 두 가지 기본 제공 진단 설정 정책 정의를 보여 주는 Azure Policy 정의 페이지의 부분 스크린샷](media/deploy-scale/builtin-diagnostic-settings.png)

### <a name="custom-policy-definitions"></a>사용자 지정 정책 정의
기본 제공 정책이 없는 리소스 종류의 경우 사용자 지정 정책 정의를 만들어야 합니다. 기존 기본 제공 정책을 복사한 다음, 리소스 종류에 맞게 수정하여 Azure Portal에서 수동으로 이 작업을 수행할 수 있습니다. 하지만 PowerShell 갤러리의 스크립트를 사용하여 프로그래밍 방식으로 정책을 만드는 것이 더 효율적입니다.

[Create-AzDiagPolicy](https://www.powershellgallery.com/packages/Create-AzDiagPolicy) 스크립트는 PowerShell 또는 CLI를 사용하여 설치할 수 있는 특정 리소스 종류에 대한 정책 파일을 만듭니다. 다음 절차에 따라 진단 설정에 대한 사용자 지정 정책 정의를 만듭니다.


1. [Azure PowerShell](/powershell/azure/install-az-ps)이 설치되어 있는지 확인합니다.
2. 다음 명령을 사용하여 스크립트를 설치합니다.
  
    ```azurepowershell
    Install-Script -Name Create-AzDiagPolicy
    ```

3. 매개 변수를 사용하여 스크립트를 실행하여 로그를 보낼 위치를 지정합니다. 구독 및 리소스 종류를 지정하라는 메시지가 표시됩니다. 예를 들어, Log Analytics 작업 영역 및 이벤트 허브로 보내는 정책 정의를 만들려면 다음 명령을 사용합니다.

   ```azurepowershell
   Create-AzDiagPolicy.ps1 -ExportLA -ExportEH -ExportDir ".\PolicyFiles"  
   ```

4. 또는 명령에서 구독 및 리소스 종류를 지정할 수 있습니다. 예를 들어, Azure SQL Server 데이터베이스에 대한 Log Analytics 작업 영역 및 이벤트 허브로 보내는 정책 정의를 만들려면 다음 명령을 사용합니다.

   ```azurepowershell
   Create-AzDiagPolicy.ps1 -SubscriptionID xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -ResourceType Microsoft.Sql/servers/databases  -ExportLA -ExportEH -ExportDir ".\PolicyFiles"  
   ```

5. 이 스크립트는 각 정책 정의에 대해 별도의 폴더를 만듭니다. 각 폴더에는 azurepolicy.json, azurepolicy.rules.json, azurepolicy.parameters.json의 세 파일이 포함되어 있습니다. Azure Portal에서 정책을 수동으로 만들려는 경우 전체 정책 정의를 포함하는 azurepolicy.json의 내용을 복사하여 붙여넣을 수 있습니다. PowerShell 또는 CLI에서 다른 두 개의 파일을 사용하여 명령줄에서 정책 정의를 만듭니다.

    다음 예제에서는 PowerShell 및 CLI 둘 다에서 정책 정의를 설치하는 방법을 보여 줍니다. 각 항목에는 **모니터링** 범주를 지정하여 새 정책 정의를 기본 제공 정책 정의와 그룹화하기 위한 메타데이터가 포함되어 있습니다.

      ```azurepowershell
      New-AzPolicyDefinition -name "Deploy Diagnostic Settings for SQL Server database to Log Analytics workspace" -policy .\Apply-Diag-Settings-LA-Microsoft.Sql-servers-databases\azurepolicy.rules.json -parameter .\Apply-Diag-Settings-LA-Microsoft.Sql-servers-databases\azurepolicy.parameters.json -mode All -Metadata '{"category":"Monitoring"}'
      ```

      ```azurecli
      az policy definition create --name 'deploy-diag-setting-sql-database--workspace' --display-name 'Deploy Diagnostic Settings for SQL Server database to Log Analytics workspace'  --rules 'Apply-Diag-Settings-LA-Microsoft.Sql-servers-databases\azurepolicy.rules.json' --params 'Apply-Diag-Settings-LA-Microsoft.Sql-servers-databases\azurepolicy.parameters.json' --subscription 'AzureMonitor_Docs' --mode All
      ```

### <a name="initiative"></a>이니셔티브
각 정책 정의에 대한 할당을 만드는 대신, 일반적인 전략은 각 Azure 서비스에 대한 진단 설정을 만드는 정책 정의를 포함하는 이니셔티브를 만드는 것입니다. 환경을 관리하는 방법에 따라 이니셔티브와 관리 그룹, 구독 또는 리소스 그룹 간에 할당을 만듭니다. 이 전략은 다음과 같은 이점을 제공합니다.

- 리소스 종류마다 여러 할당을 만드는 대신 이니셔티브에 대해 단일 할당을 만듭니다. 여러 모니터링 그룹, 구독 또는 리소스 그룹에 동일한 이니셔티브를 사용합니다.
- 새 리소스 유형 또는 대상을 추가해야 하는 경우 이니셔티브를 수정합니다. 예를 들어, 초기에는 Log Analytics 작업 영역에만 데이터를 전송해야 하지만 나중에는 이벤트 허브를 추가할 수 있습니다. 새 할당을 만드는 대신 이니셔티브를 수정합니다.

이니셔티브 만들기에 대한 자세한 내용은 [이니셔티브 정의 만들기 및 할당](../governance/policy/tutorials/create-and-manage.md#create-and-assign-an-initiative-definition)을 참조하세요. 다음 권장 사항을 고려할 수 있습니다.

- **범주** 를 **모니터링** 으로 설정하여 관련 기본 제공 및 사용자 지정 정책 정의를 통해 그룹화합니다.
- 이니셔티브에 포함된 정책 정의에 대한 Log Analytics 작업 영역 및 이벤트 허브의 세부 정보를 지정하는 대신 일반적인 이니셔티브 매개 변수를 사용합니다. 이렇게 하면 모든 정책 정의에 대해 공통 값을 쉽게 지정하고 필요한 경우 해당 값을 변경할 수 있습니다.

![이니셔티브 정의](media/deploy-scale/initiative-definition.png)

### <a name="assignment"></a>할당 
모니터링할 리소스의 범위에 따라 Azure 관리 그룹, 구독 또는 리소스 그룹에 이니셔티브를 할당합니다. [관리 그룹](../governance/management-groups/overview.md)은 특히 조직에 여러 구독이 있는 경우 범위 지정 정책에 유용합니다.

![Azure Portal의 Log Analytics 작업 영역에 대한 진단 설정의 이니셔티브 할당 섹션에 있는 기본 사항 탭의 설정 스크린샷](media/deploy-scale/initiative-assignment.png)

이니셔티브 매개 변수를 사용하여 이니셔티브의 모든 정책 정의에 대해 작업 영역 또는 다른 세부 정보를 한 번 지정할 수 있습니다. 

![이니셔티브 매개 변수](media/deploy-scale/initiative-parameters.png)

### <a name="remediation"></a>수정
이니셔티브는 생성되는 각 가상 머신에 적용됩니다. [수정 작업](../governance/policy/how-to/remediate-resources.md)은 이니셔티브의 정책 정의를 기존 리소스에 배포하므로 이미 생성된 모든 리소스에 대해 진단 설정을 만들 수 있습니다. Azure Portal을 사용하여 할당을 만들면서 동시에 수정 작업을 만들 수 있습니다. 수정에 대한 자세한 내용은 [Azure Policy을 사용하여 비준수 리소스 수정](../governance/policy/how-to/remediate-resources.md)을 참조하세요.

![이니셔티브 수정](media/deploy-scale/initiative-remediation.png)


## <a name="vm-insights"></a>VM 인사이트
[VM 인사이트](vm/vminsights-overview.md)는 가상 머신을 모니터링하는 Azure Monitor의 기본 도구입니다. VM 인사이트를 사용하도록 설정하면 Log Analytics 에이전트와 종속성 에이전트가 모두 설치됩니다. 이러한 작업을 수동으로 수행하는 대신 Azure Policy를 사용하여 각 가상 머신을 만들 때 구성했는지 확인합니다.

> [!NOTE]
> VM 인사이트에는 사용자 환경에서 비준수 VM을 검색 및 수정하는 데 사용할 수 있는 **VM 인사이트 정책 적용 범위** 기능이 포함되어 있습니다. Azure VM에 대한 Azure Policy 및 Azure Arc와 연결된 하이브리드 가상 머신으로 직접 작업하는 대신 이 기능을 사용할 수 있습니다. Azure 가상 머신 확장 집합의 경우 Azure Policy를 사용하여 할당을 만들어야 합니다.
 

VM 인사이트에는 전체 모니터링을 사용하기 위해 두 에이전트를 모두 설치하는 다음과 같은 기본 제공 이니셔티브가 포함되어 있습니다. 

|속성 |Description |
|:---|:---|
|VM 인사이트 사용 | Azure VM과 Azure Arc에 연결된 하이브리드 VM에 Log Analytics 에이전트 및 종속성 에이전트를 설치합니다. |
|가상 머신 확장 집합에 Azure Monitor 사용 | Azure 가상 머신 확장 집합에 Log Analytics 에이전트 및 종속성 에이전트를 설치합니다. |


### <a name="virtual-machines"></a>가상 머신
Azure Policy 인터페이스를 사용하여 이러한 이니셔티브에 대한 할당을 만드는 대신, VM 인사이트에는 각 범위의 가상 머신 수를 검사하여 이니셔티브의 적용 여부를 확인할 수 있는 기능이 포함되어 있습니다. 그런 다음, 작업 영역을 구성하고 해당 인터페이스를 사용하여 필요한 할당을 만들 수 있습니다.

이 프로세스에 대한 자세한 내용은 [Azure Policy를 사용하여 VM 인사이트 사용](./vm/vminsights-enable-policy.md)을 참조하세요.

![VM 인사이트 정책](media/deploy-scale/vminsights-policy.png)

### <a name="virtual-machine-scale-sets"></a>가상 머신 크기 집합
Azure Policy를 사용하여 가상 머신 확장 집합에 대한 모니터링을 사용하도록 설정하려면 모니터링할 리소스의 범위에 따라 **가상 머신 확장 집합에 Azure Monitor 사용** 이니셔티브를 Azure 관리 그룹, 구독 또는 리소스 그룹에 할당합니다. [관리 그룹](../governance/management-groups/overview.md)은 특히 조직에 여러 구독이 있는 경우 범위 지정 정책에 유용합니다.

![Azure Portal의 이니셔티브 할당 페이지 스크린샷 이니셔티브 정의가 가상 머신 확장 집합에 Azure Monitor를 사용하도록 설정되어 있습니다.](media/deploy-scale/virtual-machine-scale-set-assign-initiative.png)

데이터가 전송될 작업 영역을 선택합니다. 이 작업 영역에는 [VM 인사이트에 대한 Log Analytics 작업 영역 구성](vm/vminsights-configure-workspace.md)에 설명된 대로 *VMInsights* 솔루션이 설치되어 있어야 합니다.

![작업 영역 선택](media/deploy-scale/virtual-machine-scale-set-workspace.png)

이 정책을 할당해야 하는 기존 가상 머신 확장 집합이 있는 경우 수정 작업을 만듭니다.

![수정 작업](media/deploy-scale/virtual-machine-scale-set-remediation.png)

### <a name="log-analytics-agent"></a>Log Analytics 에이전트
Log Analytics 에이전트를 설치하지만 종속성 에이전트는 설치하지 않으려는 시나리오가 있을 수 있습니다. 에이전트에 대한 기본 제공 이니셔티브는 없지만 VM 인사이트에서 제공하는 기본 제공 정책 정의에 따라 직접 만들 수 있습니다.

> [!NOTE]
> Log Analytics 에이전트가 Azure Monitor에 해당 데이터를 전달해야 하므로 종속성 에이전트를 배포할 이유는 없습니다.


|속성 |Description |
|-----|------------|
|Log Analytics 에이전트 배포 감사 - 목록에 없는 VM 이미지(OS) |VM 이미지(OS)가 목록에 정의되어 있지 않고 에이전트가 설치되어 있지 않은 경우 VM을 비준수로 보고합니다. |
|Linux VM용 Log Analytics 에이전트 배포 |VM 이미지(OS)가 목록에 정의되어 있고 에이전트가 설치되어 있지 않은 경우 Linux VM용 Log Analytics 에이전트를 배포합니다. |
|Windows VM용 Log Analytics 에이전트 배포 |VM 이미지(OS)가 목록에 정의되어 있고 에이전트가 설치되어 있지 않은 경우 Windows VM용 Log Analytics 에이전트를 배포합니다. |
| [미리 보기]: Linux Azure Arc 머신에 Log Analytics 에이전트를 설치해야 함 |VM 이미지(OS)가 목록에 정의되어 있고 에이전트가 설치되어 있지 않은 경우 하이브리드 Azure Arc 머신을 Linux VM에 대해 비규격으로 보고합니다. |
| [미리 보기]: Windows Azure Arc 머신에 Log Analytics 에이전트를 설치해야 함 |VM 이미지(OS)가 목록에 정의되어 있고 에이전트가 설치되어 있지 않은 경우 하이브리드 Azure Arc 머신을 Windows VM에 대해 비규격으로 보고합니다. |
| [미리 보기]: Linux Azure Arc 머신에 Log Analytics 에이전트 배포 |VM 이미지(OS)가 목록에 정의되어 있고 에이전트가 설치되어 있지 않은 경우 Linux 하이브리드 Azure Arc 머신용 Log Analytics 에이전트를 배포합니다. |
| [미리 보기]: Windows Azure Arc 머신에 Log Analytics 에이전트 배포 |VM 이미지(OS)가 목록에 정의되어 있고 에이전트가 설치되어 있지 않은 경우 Windows 하이브리드 Azure Arc 머신용 Log Analytics 에이전트를 배포합니다. |
|가상 머신 확장 집합에서 종속성 에이전트 배포 감사 - VM 이미지(OS)가 나열 취소됨 |VM 이미지(OS)가 목록에 정의되어 있지 않고 에이전트가 설치되어 있지 않은 경우 가상 머신 확장 집합을 비준수로 보고합니다. |
|가상 머신 확장 집합에서 Log Analytics 에이전트 배포 감사 - VM 이미지(OS)가 나열 취소됨 |VM 이미지(OS)가 목록에 정의되어 있지 않고 에이전트가 설치되어 있지 않은 경우 가상 머신 확장 집합을 비준수로 보고합니다. |
|Linux 가상 머신 확장 집합용 Log Analytics 에이전트 배포 |VM 이미지(OS)가 정의된 목록에 있고 에이전트가 설치되지 않은 경우 Linux 가상 머신 확장 집합용 Log Analytics 에이전트를 배포합니다. |
|Windows 가상 머신 확장 집합용 Log Analytics 에이전트 배포 |VM 이미지(OS)가 정의된 목록에 있고 에이전트가 설치되지 않은 경우 Windows 가상 머신 확장 집합용 Log Analytics 에이전트를 배포합니다. |


## <a name="next-steps"></a>다음 단계

- [Azure Policy](../governance/policy/overview.md)에 대해 자세히 알아봅니다.
- [진단 설정](essentials/diagnostic-settings.md)에 대해 자세히 알아봅니다.
