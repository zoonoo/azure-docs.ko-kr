---
title: Azure Policy를 사용 하 여 규모에 Azure Monitor 배포
description: Azure Policy를 사용 하 여 대규모로 Azure Monitor 기능을 배포 합니다.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/08/2020
ms.openlocfilehash: f2f2272363cbc26895b061fe7b6263ed2a29fbab
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91993248"
---
# <a name="deploy-azure-monitor-at-scale-using-azure-policy"></a>Azure Policy를 사용 하 여 규모에 Azure Monitor 배포
일부 Azure Monitor 기능은 한 번 또는 여러 번 구성 되지만 모니터링 하려는 각 리소스에 대해 다른 기능을 반복 해야 합니다. 이 문서에서는 Azure Policy를 사용 하 여 모든 Azure 리소스에 대해 모니터링이 일관적이 고 정확 하 게 구성 되도록 하는 Azure Monitor을 구현 하는 방법을 설명 합니다.

예를 들어 기존 Azure 리소스와 사용자가 만드는 각 새 리소스에 대 한 진단 설정을 만들어야 합니다. 또한 가상 컴퓨터를 만들 때마다 에이전트를 설치 하 고 구성 해야 합니다. 이러한 메서드는 Azure Monitor의 모든 기능에 사용할 수 있으므로 PowerShell 또는 CLI와 같은 메서드를 사용 하 여 이러한 작업을 수행할 수 있습니다. Azure Policy를 사용 하 여 리소스를 만들거나 수정할 때마다 적절 한 구성을 자동으로 수행 하는 논리를 사용할 수 있습니다.


## <a name="azure-policy"></a>Azure Policy
이 섹션에서는 최소한의 노력으로 전체 Azure 구독 또는 관리 그룹에서 조직의 표준을 평가 하 고 적용할 수 있는 [Azure Policy](../governance/policy/overview.md) 에 대 한 간략 한 소개를 제공 합니다. 자세한 내용은 [Azure Policy 설명서](../governance/policy/overview.md) 를 참조 하세요.

Azure Policy를 사용 하 여 생성 된 모든 리소스에 대 한 구성 요구 사항을 지정 하 고 규정을 준수 하지 않는 리소스를 식별 하거나, 리소스가 생성 되지 않도록 차단 하거나, 필요한 구성을 추가할 수 있습니다. 호출을 가로채 새 리소스를 만들거나 기존 리소스를 수정 하는 방식으로 작동 합니다. 이는 정책 정의에 필요한 속성과 일치 하는 항목이 일치 하지 않을 경우 요청을 거부 하는 것과 같은 방법으로 응답 하거나 비준수에 대해 플래그를 적용 하거나 관련 리소스를 배포할 수 있습니다. **Deployifnotexists** 를 사용 하 여 기존 리소스를 재구성 하거나 정책 정의를 **수정할** 수 있습니다.

Azure Policy은 다음 테이블의 개체로 구성 됩니다. 각에 대 한 자세한 설명은 [Azure Policy 개체](../governance/policy/overview.md#azure-policy-objects) 를 참조 하세요.

| 항목 | 설명 |
|:---|:---|
| 정책 정의 | 리소스 준수 조건 및 조건이 충족 될 경우 수행할 영향을 설명 합니다. 특정 형식의 모든 리소스 이거나 특정 속성과 일치 하는 리소스만 될 수 있습니다. 이에 따른 결과는 리소스를 준수 하도록 플래그를 지정 하거나 관련 된 리소스를 배포 하는 것입니다. 정책 정의는 [Azure Policy 정의 구조](../governance/policy/concepts/definition-structure.md)에 설명 된 대로 JSON을 사용 하 여 작성 됩니다. 효과는 [Azure Policy 효과 이해](../governance/policy/concepts/effects.md)에 설명 되어 있습니다.
| 정책 이니셔티브 | 함께 적용 해야 하는 정책 정의 그룹입니다. 예를 들어 리소스 로그를 Log Analytics 작업 영역에 보내고 다른 하나는 리소스 로그를 event hubs로 보내는 정책 정의가 하나 있을 수 있습니다. 두 정책 정의를 모두 포함 하는 이니셔티브를 만들고 개별 정책 정의 대신 리소스에 이니셔티브를 적용 합니다. 이니셔티브는 [Azure Policy 이니셔티브 구조](../governance/policy/concepts/initiative-definition-structure.md)에 설명 된 대로 JSON을 사용 하 여 작성 됩니다. |
| 할당 | 정책 정의 또는 이니셔티브는 범위에 할당 될 때까지 적용 되지 않습니다. 예를 들어 리소스 그룹에 정책을 할당 하 여 해당 리소스에서 만든 모든 리소스에 적용 하거나 구독에 적용 하 여 해당 구독에 있는 모든 리소스에 정책을 적용 합니다.  자세한 내용은 [Azure Policy 할당 구조](../governance/policy/concepts/assignment-structure.md)를 참조 하세요. |

## <a name="built-in-policy-definitions-for-azure-monitor"></a>Azure Monitor에 대한 기본 제공 정책 정의
Azure Policy에는 Azure Monitor와 관련 된 몇 가지 미리 작성 된 정의가 포함 되어 있습니다. 이러한 정책 정의를 기존 구독에 할당 하거나 기본으로 사용 하 여 고유한 사용자 지정 정의를 만들 수 있습니다. **모니터링** 범주에 있는 기본 제공 정치의 전체 목록은 [Azure Policy 기본 제공 정책 정의 Azure Monitor를](./samples/policy-reference.md)참조 하세요.

모니터링과 관련 된 기본 제공 정책 정의를 보려면 다음을 수행 합니다.

1. Azure Portal **Azure Policy** 으로 이동 합니다.
2. **정의**를 선택 합니다.
3. **유형**에 대해 *기본 제공* 을 선택 하 고 **범주**에 대해 *모니터링*을 선택 합니다.

  ![모니터링 범주 및 기본 제공 형식에 대 한 정책 정의 목록을 보여 주는 Azure Portal의 Azure Policy 정의 페이지 스크린샷](media/deploy-scale/builtin-policies.png)


## <a name="diagnostic-settings"></a>진단 설정
[진단 설정은](platform/diagnostic-settings.md) Azure 리소스에서 여러 위치로, 일반적으로 Log Analytics 작업 영역에 대 한 리소스 로그 및 메트릭을 수집 하 여 [로그 쿼리](log-query/log-query-overview.md) 및 [로그 경고](platform/alerts-log.md)로 데이터를 분석할 수 있도록 합니다. 정책을 사용 하 여 리소스를 만들 때마다 진단 설정을 자동으로 만듭니다.

각 Azure 리소스 종류에는 진단 설정에 나열 해야 하는 고유한 범주 집합이 있습니다. 따라서 각 리소스 종류에는 별도의 정책 정의가 필요 합니다. 일부 리소스 종류에는 수정 하지 않고 할당할 수 있는 기본 제공 정책 정의가 있습니다. 다른 리소스 형식의 경우 사용자 지정 정의를 만들어야 합니다.

### <a name="built-in-policy-definitions-for-azure-monitor"></a>Azure Monitor에 대한 기본 제공 정책 정의
각 리소스 유형에 대 한 기본 제공 정책 정의는 Log Analytics 작업 영역으로 전송 하 고 다른 하나는 이벤트 허브에 두 가지가 있습니다. 하나의 위치만 필요한 경우 리소스 종류에 대해 해당 정책을 할당 합니다. 둘 다 필요한 경우 리소스에 대 한 두 정책 정의를 모두 할당 합니다.

예를 들어 다음 이미지는 Data Lake Analytics에 대 한 기본 제공 진단 설정 정책 정의를 보여 줍니다.

  ![Data Lake Analytics에 대 한 두 가지 기본 제공 진단 설정 정책 정의를 보여 주는 Azure Policy 정의 페이지의 부분 스크린샷.](media/deploy-scale/builtin-diagnostic-settings.png)

### <a name="custom-policy-definitions"></a>사용자 지정 정책 정의
기본 제공 정책이 없는 리소스 종류의 경우 사용자 지정 정책 정의를 만들어야 합니다. 기존 기본 제공 정책을 복사한 다음 리소스 유형을 수정 하 여 Azure Portal에서이 작업을 수동으로 수행할 수 있습니다. PowerShell 갤러리 스크립트를 사용 하 여 프로그래밍 방식으로 정책을 만드는 것이 더 효율적입니다.

[AzDiagPolicy](https://www.powershellgallery.com/packages/Create-AzDiagPolicy) 스크립트는 POWERSHELL 또는 CLI를 사용 하 여 설치할 수 있는 특정 리소스 종류에 대 한 정책 파일을 만듭니다. 진단 설정에 대 한 사용자 지정 정책 정의를 만들려면 다음 절차를 따르십시오.


1. [Azure PowerShell](/powershell/azure/install-az-ps) 설치 되어 있는지 확인 합니다.
2. 다음 명령을 사용 하 여 스크립트를 설치 합니다.
  
    ```azurepowershell
    Install-Script -Name Create-AzDiagPolicy
    ```

3. 매개 변수를 사용 하 여 스크립트를 실행 하 여 로그를 보낼 위치를 지정 합니다. 구독 및 리소스 종류를 지정 하 라는 메시지가 표시 됩니다. 예를 들어 Log Analytics 작업 영역 및 이벤트 허브로 보내는 정책 정의를 만들려면 다음 명령을 사용 합니다.

   ```azurepowershell
   Create-AzDiagPolicy.ps1 -ExportLA -ExportEH -ExportDir ".\PolicyFiles"  
   ```

4. 또는 명령에서 구독 및 리소스 종류를 지정할 수 있습니다. 예를 들어 Azure SQL Server 데이터베이스용 Log Analytics 작업 영역 및 이벤트 허브로 보내는 정책 정의를 만들려면 다음 명령을 사용 합니다.

   ```azurepowershell
   Create-AzDiagPolicy.ps1 -SubscriptionID xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -ResourceType Microsoft.Sql/servers/databases  -ExportLA -ExportEH -ExportDir ".\PolicyFiles"  
   ```

5. 이 스크립트는 각 정책 정의에 대 한 별도의 폴더를 만듭니다. 각 정책 정의에는 azurepolicy.json, azurepolicy.rules.json, azurepolicy.parameters.js에 각각 3 개의 파일이 포함 됩니다. Azure Portal에서 정책을 수동으로 만들려면 전체 정책 정의가 포함 되어 있으므로 azurepolicy.js의 내용을 복사 하 여 붙여 넣을 수 있습니다. PowerShell 또는 CLI를 사용 하 여 다른 두 파일을 사용 하 여 명령줄에서 정책 정의를 만듭니다.

    다음 예에서는 PowerShell 및 CLI에서 정책 정의를 설치 하는 방법을 보여 줍니다. 각에는 기본 제공 정책 정의를 사용 하 여 새 정책 정의를 그룹화 하는 **모니터링** 범주를 지정 하는 메타 데이터가 포함 됩니다.

      ```azurepowershell
      New-AzPolicyDefinition -name "Deploy Diagnostic Settings for SQL Server database to Log Analytics workspace" -policy .\Apply-Diag-Settings-LA-Microsoft.Sql-servers-databases\azurepolicy.rules.json -parameter .\Apply-Diag-Settings-LA-Microsoft.Sql-servers-databases\azurepolicy.parameters.json -mode All -Metadata '{"category":"Monitoring"}'
      ```

      ```azurecli
      az policy definition create --name 'deploy-diag-setting-sql-database--workspace' --display-name 'Deploy Diagnostic Settings for SQL Server database to Log Analytics workspace'  --rules 'Apply-Diag-Settings-LA-Microsoft.Sql-servers-databases\azurepolicy.rules.json' --params 'Apply-Diag-Settings-LA-Microsoft.Sql-servers-databases\azurepolicy.parameters.json' --subscription 'AzureMonitor_Docs' --mode All
      ```

### <a name="initiative"></a>이니셔티브
각 정책 정의에 대 한 할당을 만드는 대신 일반적인 전략은 각 Azure 서비스에 대 한 진단 설정을 만드는 정책 정의를 포함 하는 이니셔티브를 만드는 것입니다. 환경을 관리 하는 방법에 따라 이니셔티브와 관리 그룹, 구독 또는 리소스 그룹 간의 할당을 만듭니다. 이 전략은 다음과 같은 이점을 제공 합니다.

- 각 리소스 유형에 대해 여러 할당이 아닌 이니셔티브에 대해 단일 할당을 만듭니다. 여러 모니터링 그룹, 구독 또는 리소스 그룹에 동일한 이니셔티브를 사용 합니다.
- 새 리소스 유형 또는 대상을 추가 해야 하는 경우 이니셔티브를 수정 합니다. 예를 들어 Log Analytics 작업 영역에만 데이터를 전송 하 고 나중에 이벤트 허브를 추가 하려면 초기 요구 사항을 충족 해야 합니다. 새 할당을 만드는 대신 이니셔티브를 수정 합니다.

이니셔티브 만들기에 대 한 자세한 내용은 [이니셔티브 정의 만들기 및 할당](../governance/policy/tutorials/create-and-manage.md#create-and-assign-an-initiative-definition) 을 참조 하세요. 다음 권장 사항을 고려할 수 있습니다.

- **범주** 를 **모니터링** 으로 설정 하 여 관련 기본 제공 및 사용자 지정 정책 정의로 그룹화 합니다.
- Log Analytics 작업 영역에 대 한 세부 정보와 이니셔티브에 포함 된 정책 정의에 대 한 이벤트 허브를 지정 하는 대신 일반적인 이니셔티브 매개 변수를 사용 합니다. 이렇게 하면 모든 정책 정의에 대 한 공통 값을 쉽게 지정 하 고 필요한 경우 해당 값을 변경할 수 있습니다.

![이니셔티브 정의](media/deploy-scale/initiative-definition.png)

### <a name="assignment"></a>할당 
모니터링할 리소스의 범위에 따라 Azure 관리 그룹, 구독 또는 리소스 그룹에 이니셔티브를 할당 합니다. [관리 그룹](../governance/management-groups/overview.md) 은 특히 조직에 여러 구독이 있는 경우 범위 지정 정책에 특히 유용 합니다.

![Azure Portal의 Log Analytics 작업 영역에 대 한 진단 설정의 이니셔티브 할당 섹션에 있는 기본 사항 탭에 대 한 설정의 스크린샷](media/deploy-scale/initiative-assignment.png)

이니셔티브 매개 변수를 사용 하 여 이니셔티브의 모든 정책 정의에 대해 작업 영역 또는 다른 세부 정보를 한 번 지정할 수 있습니다. 

![이니셔티브 매개 변수](media/deploy-scale/initiative-parameters.png)

### <a name="remediation"></a>수정
이니셔티브는 생성 되는 각 가상 컴퓨터에 적용 됩니다. [수정 작업](../governance/policy/how-to/remediate-resources.md) 은 이니셔티브의 정책 정의를 기존 리소스에 배포 하므로 이미 생성 된 모든 리소스에 대 한 진단 설정을 만들 수 있습니다. Azure Portal를 사용 하 여 할당을 만들 때 재구성 작업을 동시에 만들 수 있습니다. 해결 방법에 대 한 자세한 내용은 [Azure Policy를 사용 하 여 비호환 리소스](../governance/policy/how-to/remediate-resources.md) 수정을 참조 하세요.

![이니셔티브 수정](media/deploy-scale/initiative-remediation.png)


## <a name="azure-monitor-for-vms"></a>VM용 Azure Monitor
[VM용 Azure Monitor](insights/vminsights-overview.md) 는 가상 컴퓨터를 모니터링 하는 Azure Monitor 기본 도구입니다. VM용 Azure Monitor 사용 하도록 설정 하면 Log Analytics 에이전트와 종속성 에이전트가 모두 설치 됩니다. 이러한 작업을 수동으로 수행 하는 대신 Azure Policy를 사용 하 여 각 가상 컴퓨터를 만들 때 구성 했는지 확인 합니다.

> [!NOTE]
> VM용 Azure Monitor에는 사용자 환경에서 비규격 Vm을 검색 및 재구성 하는 데 사용할 수 있는 **VM용 Azure Monitor 정책 적용** 기능 이라는 기능이 포함 되어 있습니다. Azure Vm에 대 한 Azure Policy 및 Azure Arc와 연결 된 하이브리드 가상 머신에 직접 작업 하는 대신이 기능을 사용할 수 있습니다. Azure 가상 머신 확장 집합의 경우 Azure Policy를 사용 하 여 할당을 만들어야 합니다.
 

VM용 Azure Monitor는 전체 모니터링을 사용 하기 위해 두 에이전트를 모두 설치 하는 다음과 같은 기본 제공 이니셔티브를 포함 합니다. 

|속성 |설명 |
|:---|:---|
|VM용 Azure Monitor 사용 | Azure Arc에 연결 된 Azure Vm 및 하이브리드 Vm에 Log Analytics 에이전트 및 종속성 에이전트를 설치 합니다. |
|가상 머신 확장 집합에 대 한 Azure Monitor 사용 | Azure 가상 머신 확장 집합에 Log Analytics 에이전트 및 종속성 에이전트를 설치 합니다. |


### <a name="virtual-machines"></a>가상 머신
Azure Policy 인터페이스를 사용 하 여 이러한 이니셔티브에 대 한 할당을 만드는 대신 VM용 Azure Monitor에는 각 범위에 있는 가상 머신의 수를 검사 하 여 이니셔티브의 적용 여부를 확인할 수 있는 기능이 포함 되어 있습니다. 그런 다음 작업 영역을 구성 하 고 해당 인터페이스를 사용 하 여 필요한 할당을 만들 수 있습니다.

이 프로세스에 대 한 자세한 내용은 [Azure Policy를 사용 하 여 VM용 Azure Monitor 사용](./insights/vminsights-enable-policy.md)을 참조 하세요.

![VM용 Azure Monitor 정책](media/deploy-scale/vminsights-policy.png)

### <a name="virtual-machine-scale-sets"></a>가상 머신 크기 집합
Azure Policy를 사용 하 여 가상 머신 확장 집합에 대 한 모니터링을 사용 하도록 설정 하려면 모니터링할 리소스의 범위에 따라 **가상 머신 확장 집합에 대 한 Azure Monitor 사용** 이니셔티브를 Azure 관리 그룹, 구독 또는 리소스 그룹에 할당 합니다. [관리 그룹](../governance/management-groups/overview.md) 은 특히 조직에 여러 구독이 있는 경우 범위 지정 정책에 특히 유용 합니다.

![Azure Portal에서 이니셔티브 할당 페이지의 스크린샷 가상 머신 확장 집합에 대 한 Azure Monitor 사용 하도록 이니셔티브 정의가 설정 되었습니다.](media/deploy-scale/virtual-machine-scale-set-assign-initiative.png)

데이터가 전송 될 작업 영역을 선택 합니다. 이 작업 영역에는의 설명에 따라 *VMInsights* 솔루션이 설치 되어 있어야 합니다 []() .

![작업 영역 선택](media/deploy-scale/virtual-machine-scale-set-workspace.png)

이 정책을 할당 해야 하는 기존 가상 머신 확장 집합이 있는 경우 수정 작업을 만듭니다.

![수정 작업](media/deploy-scale/virtual-machine-scale-set-remediation.png)

### <a name="log-analytics-agent"></a>Log Analytics 에이전트
Log Analytics 에이전트를 설치 하지만 종속성 에이전트는 설치 하지 않으려는 시나리오가 있을 수 있습니다. 에이전트에 대 한 기본 제공 이니셔티브는 없지만 VM용 Azure Monitor에서 제공 하는 기본 제공 정책 정의에 따라 직접 만들 수 있습니다.

> [!NOTE]
> Log Analytics 에이전트가 Azure Monitor에 데이터를 전달 해야 하므로 종속성 에이전트를 자체에 배포할 이유가 없습니다.


|속성 |설명 |
|-----|------------|
|감사 Log Analytics 에이전트 배포 – VM 이미지 (OS) 나열 되지 않음 |VM 이미지 (OS)가 목록에 정의 되어 있지 않고 에이전트가 설치 되어 있지 않은 경우 vm을 비규격으로 보고 합니다. |
|Linux Vm에 대 한 Log Analytics 에이전트 배포 |VM 이미지 (OS)가 목록에 정의 되어 있고 에이전트가 설치 되어 있지 않은 경우 Linux Vm에 대 한 Log Analytics 에이전트를 배포 합니다. |
|Windows Vm에 대 한 Log Analytics 에이전트 배포 |VM 이미지 (OS)가 목록에 정의 되어 있고 에이전트가 설치 되어 있지 않은 경우 Windows Vm에 대 한 Log Analytics 에이전트를 배포 합니다. |
| [Preview]: Linux Azure Arc 컴퓨터에 Log Analytics 에이전트를 설치 해야 합니다. |VM 이미지 (OS)가 목록에 정의 되어 있고 에이전트가 설치 되어 있지 않은 경우 Linux Vm에 대 한 비규격으로 하이브리드 Azure Arc 컴퓨터를 보고 합니다. |
| [Preview]: Log Analytics 에이전트가 Windows Azure Arc 컴퓨터에 설치 되어 있어야 합니다. |VM 이미지 (OS)가 목록에 정의 되어 있고 에이전트가 설치 되어 있지 않은 경우 Windows Vm에 대 한 비규격으로 하이브리드 Azure Arc 컴퓨터를 보고 합니다. |
| [Preview]: Linux Azure Arc 컴퓨터에 Log Analytics 에이전트 배포 |VM 이미지 (OS)가 목록에 정의 되어 있고 에이전트가 설치 되어 있지 않은 경우 Linux 하이브리드 Azure Arc 컴퓨터용 Log Analytics 에이전트를 배포 합니다. |
| [Preview]: Microsoft Azure Arc 컴퓨터에 Log Analytics 에이전트를 배포 합니다. |VM 이미지 (OS)가 목록에 정의 되어 있고 에이전트가 설치 되어 있지 않은 경우 Windows 하이브리드 Azure Arc 컴퓨터용 Log Analytics 에이전트를 배포 합니다. |
|가상 머신 확장 집합에서 종속성 에이전트 배포 감사 – VM 이미지 (OS)가 나열 되지 않음 |VM 이미지 (OS)가 목록에 정의 되어 있지 않고 에이전트가 설치 되어 있지 않으면 가상 머신 확장 집합을 비규격으로 보고 합니다. |
|Virtual machine scale sets의 감사 Log Analytics 에이전트 배포 – VM 이미지 (OS)가 나열 되지 않음 |VM 이미지 (OS)가 목록에 정의 되어 있지 않고 에이전트가 설치 되어 있지 않으면 가상 머신 확장 집합을 비규격으로 보고 합니다. |
|Linux 가상 머신 확장 집합용 Log Analytics 에이전트 배포 |VM 이미지 (OS)가 목록에 정의 되어 있고 에이전트가 설치 되어 있지 않은 경우 Linux 가상 머신 확장 집합에 대 한 Log Analytics 에이전트를 배포 합니다. |
|Windows 가상 머신 확장 집합용 Log Analytics 에이전트 배포 |VM 이미지 (OS)가 목록에 정의 되어 있고 에이전트가 설치 되어 있지 않은 경우 Windows 가상 머신 확장 집합에 대 한 Log Analytics 에이전트를 배포 합니다. |


## <a name="next-steps"></a>다음 단계

- 자세한 내용은 [Azure Policy](../governance/policy/overview.md)를 참조 하세요.
- 자세한 내용은 [진단 설정](platform/diagnostic-settings.md)을 참조 하세요.