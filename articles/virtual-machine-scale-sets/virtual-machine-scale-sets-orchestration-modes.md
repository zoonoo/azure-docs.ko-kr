---
title: Azure의 가상 머신 확장 집합에 대 한 오케스트레이션 모드
description: Azure의 가상 머신 확장 집합에 대해 유연 하 고 일관적인 오케스트레이션 모드를 사용 하는 방법을 알아봅니다.
author: fitzgeraldsteele
ms.author: fisteele
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: extensions
ms.date: 02/12/2021
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: 71ddb1217be7fe3e1254e0d49e1f40c43a55a3f0
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102502419"
---
# <a name="preview-orchestration-modes-for-virtual-machine-scale-sets-in-azure"></a>미리 보기: Azure의 가상 머신 확장 집합에 대 한 오케스트레이션 모드 

Virtual Machines 확장 집합은 플랫폼 관리 가상 컴퓨터의 논리적 그룹화를 제공 합니다. 크기 집합을 사용 하 여 가상 머신 구성 모델을 만들고, CPU 또는 메모리 부하에 따라 추가 인스턴스를 자동으로 추가 또는 제거 하 고, 최신 OS 버전으로 자동으로 업그레이드 합니다. 일반적으로 확장 집합을 사용 하면 확장 집합을 만들 때 제공 되는 VM 구성 모델을 사용 하 여 가상 컴퓨터를 만들 수 있으며, 확장 집합은 구성 모델에 따라 암시적으로 만들어진 가상 컴퓨터만 관리할 수 있습니다. 

크기 집합 오케스트레이션 모드를 사용 하면 확장 집합에서 가상 머신 인스턴스를 관리 하는 방법을 보다 강력 하 게 제어할 수 있습니다. 

> [!IMPORTANT]
> 오케스트레이션 모드는 확장 집합을 만들 때 정의 되며 나중에 변경 하거나 업데이트할 수 없습니다.


## <a name="scale-sets-with-uniform-orchestration"></a>균일 한 오케스트레이션이 포함 된 확장 집합
동일한 인스턴스를 사용 하는 대규모 상태 비저장 워크 로드에 최적화 되었습니다.

Uniform 오케스트레이션이 있는 가상 머신 확장 집합은 가상 머신 프로필 또는 템플릿을 사용 하 여 원하는 용량까지 확장 합니다. 개별 가상 머신 인스턴스를 관리 하거나 사용자 지정 하는 기능이 있지만 Uniform은 동일한 VM 인스턴스를 사용 합니다. 개별 Uniform VM 인스턴스는 가상 머신 확장 집합 VM API 명령을 통해 노출 됩니다. 개별 인스턴스는 표준 Azure IaaS VM API 명령, Azure Resource Manager 리소스 태깅 RBAC 권한, Azure Backup 또는 Azure Site Recovery와 같은 Azure 관리 기능과 호환 되지 않습니다. 균일 오케스트레이션은 100 개 미만의 인스턴스로 구성 된 경우 장애 도메인 고가용성을 보장 합니다. 균일 오케스트레이션은 일반적으로 사용할 수 있으며 메트릭 기반 자동 크기 조정, 인스턴스 보호 및 자동 OS 업그레이드를 포함 하 여 모든 범위의 확장 집합 관리 및 오케스트레이션을 지원 합니다. 


## <a name="scale-sets-with-flexible-orchestration"></a>유연한 오케스트레이션이 포함 된 확장 집합 
동일 하거나 여러 가상 머신 유형을 사용 하 여 대규모로 고가용성을 구현 합니다.

유연한 오케스트레이션을 사용 하면 azure는 Azure VM 에코 시스템에서 통합 된 환경을 제공 합니다. 유연한 오케스트레이션은 지역 또는 가용성 영역 내의 장애 도메인에 Vm을 분산 하 여 고가용성 보증 (최대 1000 Vm)을 제공 합니다. 이렇게 하면 다음을 비롯 한 쿼럼 기반 또는 상태 저장 워크 로드를 실행 하는 데 필수적인 장애 도메인 격리를 유지 하면서 응용 프로그램을 확장할 수 있습니다.
- 쿼럼 기반 작업
- Open-Source 데이터베이스
- 상태 저장 응용 프로그램
- 고가용성 및 대규모 규모를 필요로 하는 서비스
- 가상 컴퓨터 유형을 혼합 하거나 단일 요청 Vm을 함께 활용 하려는 서비스
- 기존 가용성 집합 응용 프로그램  

> [!IMPORTANT]
> 유연한 오케스트레이션 모드의 가상 머신 확장 집합은 현재 공개 미리 보기로 제공 됩니다. 아래에 설명 된 공개 미리 보기 기능을 사용 하려면 옵트인 프로시저가 필요 합니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공 되며 프로덕션 워크 로드에는 권장 되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.


## <a name="what-has-changed-with-flexible-orchestration-mode"></a>유연한 오케스트레이션 모드로 변경 된 사항은 무엇입니까?
유연한 오케스트레이션의 주요 장점 중 하나는 확장 집합 자식 가상 머신 대신 표준 Azure IaaS Vm을 통해 오케스트레이션 기능을 제공 한다는 것입니다. 즉, 단일 오케스트레이션에 서 사용 하는 가상 머신 확장 집합 VM Api 대신 유연한 오케스트레이션 인스턴스를 관리할 때 모든 표준 VM Api를 사용할 수 있습니다. 미리 보기 기간 중에는 유연한 오케스트레이션과 균일 한 오케스트레이션에 서 인스턴스를 관리 하는 것 사이에 몇 가지 차이점이 있습니다. 일반적으로 가능한 경우 표준 Azure IaaS VM Api를 사용 하는 것이 좋습니다. 이 섹션에서는 유연한 오케스트레이션을 사용 하 여 VM 인스턴스를 관리 하기 위한 모범 사례 예를 강조 합니다.  

### <a name="assign-fault-domain-during-vm-creation"></a>VM을 만드는 동안 장애 도메인 할당
유연한 오케스트레이션 확장 집합에 대 한 장애 도메인 수를 선택할 수 있습니다. 기본적으로 유연한 확장 집합에 VM을 추가 하는 경우 Azure는 장애 도메인 간에 인스턴스를 균등 하 게 분산 합니다. Azure에서 장애 도메인을 할당 하는 것이 좋습니다. 고급 또는 문제 해결 시나리오의 경우이 기본 동작을 재정의 하 고 인스턴스가 배치 될 장애 도메인을 지정할 수 있습니다.

```azurecli-interactive 
az vm create –vmss "myVMSS"  –-platform_fault_domain 1
```

### <a name="instance-naming"></a>인스턴스 이름 지정 
VM을 만들어 유연한 확장 집합에 추가 하는 경우 Azure 명명 규칙 규칙 내에서 인스턴스 이름을 완전히 제어할 수 있습니다. 자동 크기 조정을 통해 Vm이 확장 집합에 자동으로 추가 되는 경우 접두사를 제공 하 고, Azure는 이름 끝에 고유 번호를 추가 합니다.

### <a name="query-instances-for-power-state"></a>전원 상태에 대 한 쿼리 인스턴스
선호 되는 방법은 Azure 리소스 그래프를 사용 하 여 가상 머신 확장 집합의 모든 Vm에 대해 쿼리 하는 것입니다. Azure 리소스 그래프는 구독에서 대규모로 Azure 리소스에 대 한 효율적인 쿼리 기능을 제공 합니다. 

``` 
| where type =~ 'Microsoft.Compute/virtualMachines' 
| where properties.virtualMachineScaleSet contains "demo" 
| extend powerState = properties.extended.instanceView.powerState.code 
| project name, resourceGroup, location, powerState 
| order by resourceGroup desc, name desc 
```

[Azure 리소스 그래프](../governance/resource-graph/overview.md) 를 사용 하 여 리소스를 쿼리하면 azure 리소스를 쿼리하고 리소스 공급자에 대 한 API 호출을 최소화 하는 쉽고 효율적인 방법입니다. Azure 리소스 그래프는 새 리소스 또는 업데이트 된 리소스가 최대 60 초 동안 반영 되지 않을 수 있는 궁극적으로 일관 된 캐시입니다. 다음과 같습니다.
- 리소스 그룹 또는 구독에서 Vm을 나열 합니다.
- 확장 옵션을 사용 하 여 구독에 있는 모든 Vm에 대 한 인스턴스 보기 (장애 도메인 할당, 전원 및 프로 비전 상태)를 검색 합니다.
- Get VM API 및 명령을 사용 하 여 단일 인스턴스에 대 한 모델 및 인스턴스 뷰를 가져옵니다.

### <a name="scale-sets-vm-batch-operations"></a>크기 집합 VM 일괄 작업
표준 VM 명령을 사용 하 여 가상 머신 확장 집합 VM Api 대신 인스턴스를 시작, 중지, 다시 시작, 삭제 합니다. 가상 머신 확장 집합 VM 일괄 처리 작업 (전체 시작, 모두 중지, 이미지 다시 설치 등)은 유연한 오케스트레이션 모드에서 사용 되지 않습니다. 

### <a name="monitor-application-health"></a>애플리케이션 상태 모니터링 
응용 프로그램 상태 모니터링을 통해 응용 프로그램은 Azure에 하트 비트를 제공 하 여 응용 프로그램이 정상 상태 인지 비정상 상태 인지 확인할 수 있습니다. Azure는 비정상 상태인 VM 인스턴스를 자동으로 바꿀 수 있습니다. 유연한 확장 집합 인스턴스의 경우 가상 머신에서 응용 프로그램 상태 확장을 설치 하 고 구성 해야 합니다. 균일 한 확장 집합 인스턴스의 경우 응용 프로그램 상태 확장을 사용 하거나 Azure Load Balancer 사용자 지정 상태 프로브를 사용 하 여 상태를 측정할 수 있습니다. 

### <a name="list-scale-sets-vm-api-changes"></a>목록 크기 집합 VM API 변경 
Virtual Machine Scale Sets를 사용 하 여 확장 집합에 속한 인스턴스를 나열할 수 있습니다. 유연한 오케스트레이션을 사용 하 여 list Virtual Machine Scale Sets VM 명령은 확장 집합 VM Id의 목록을 제공 합니다. 그런 다음 GET Virtual Machine Scale Sets VM 명령을 호출 하 여 확장 집합에서 VM 인스턴스로 작업 하는 방법에 대 한 자세한 정보를 얻을 수 있습니다. VM에 대 한 전체 세부 정보를 얻으려면 standard GET VM 명령 또는 [Azure 리소스 그래프](https://docs.microsoft.com/azure/governance/resource-graph/overview)를 사용 합니다. 

### <a name="retrieve-boot-diagnostics-data"></a>부팅 진단 데이터 검색 
표준 VM Api 및 명령을 사용 하 여 인스턴스 부트 진단 데이터 및 스크린샷을 검색 합니다. Virtual Machine Scale Sets VM 부팅 진단 Api 및 명령은 유연한 오케스트레이션 모드 인스턴스와 함께 사용 되지 않습니다.

### <a name="vm-extensions"></a>VM 확장 
균일 한 오케스트레이션 모드 인스턴스를 대상으로 하는 확장 대신 표준 가상 머신을 대상으로 하는 확장을 사용 합니다.


## <a name="a-comparison-of-flexible-uniform-and-availability-sets"></a>유연 하 고 일관 되며 가용성 집합의 비교 
다음 표에서는 유연성 있는 오케스트레이션 모드, 균일 오케스트레이션 모드 및 해당 기능에 대 한 가용성 집합을 비교 합니다.

| 기능 | 유연한 오케스트레이션에 서 지원 됨 (미리 보기) | 단일 오케스트레이션에 서 지원 됩니다 (일반 공급). | AvSets에서 지원 됩니다 (일반 공급). |
|-|-|-|-|
|         가상 머신 유형  | 표준 Azure IaaS VM (/virtualmachines)  | 확장 집합 특정 Vm (/virtualmachinescalesets/virtualmachines)  | 표준 Azure IaaS VM (/virtualmachines)  |
|         지원되는 SKU  |            D 시리즈, E 시리즈, F 시리즈, A 시리즈, B 시리즈, Intel, AMD  |            모든 Sku  |            모든 Sku  |
|         가용성 영역  |            필요에 따라 단일 가용성 영역에 모든 인스턴스를 지정 합니다. |            1, 2 또는 3 개의 가용성 영역에서 인스턴스를 지정 합니다.  |            지원되지 않음  |
|         VM, Nic, 디스크에 대 한 모든 권한  |            예  |            가상 머신 확장 집합 VM API를 사용 하 여 제한 된 제어  |            예  |
|         자동 크기 조정  |            아니요  |            예  |            아니요  |
|         특정 장애 도메인에 VM 할당  |            예  |             아니요   |            아니요  |
|         VM 인스턴스를 삭제할 때 Nic 및 디스크 제거  |            아니요  |            예  |            아니요  |
|         업그레이드 정책 (VM 크기 집합) |            아니요  |            자동, 롤링, 수동  |            해당 없음  |
|         자동 OS 업데이트 (VM 크기 집합) |            아니요  |            예  |            해당 없음  |
|         게스트 보안 패치  |            예  |            아니요  |            예  |
|         알림 종료 (VM 크기 집합) |            아니요  |            예  |            해당 없음  |
|         인스턴스 복구 (VM 크기 집합) |            아니요  |            예   |            해당 없음  |
|         가속된 네트워킹  |            예  |            예  |            예  |
|         스폿 인스턴스 및 가격   |            예, 스폿 및 일반 우선 순위 인스턴스를 모두 사용할 수 있습니다.  |            예, 인스턴스는 모두 스폿 이거나 모두 정상 이어야 합니다.  |            아니요, 일반 우선 순위 인스턴스만  |
|         운영 체제 혼합  |            예, Linux 및 Windows는 동일한 유연한 확장 집합에 상주할 수 있습니다. |            아니요, 인스턴스는 동일한 운영 체제입니다.  |               예, Linux 및 Windows는 동일한 유연한 확장 집합에 상주할 수 있습니다. |
|         응용 프로그램 상태 모니터링  |            응용 프로그램 상태 확장  |            응용 프로그램 상태 확장 또는 Azure 부하 분산 장치 프로브  |            응용 프로그램 상태 확장  |
|         디스크 UltraSSD   |            예  |            예, 영역 배포에만 해당  |            아니요  |
|         Infiniband   |            아니요  |            예, 단일 배치 그룹만  |            예  |
|         쓰기 가속기   |            아니요  |            예  |            예  |
|         근접 배치 그룹   |            예  |            예  |            예  |
|         Azure 전용 호스트   |            아니요  |            예  |            예  |
|         기본 SLB   |            아니요  |            예  |            예  |
|         Azure Load Balancer Standard SKU |            예  |            예  |            예  |
|         Application Gateway  |            아니요  |            예  |            예  |
|         유지 관리 제어   |            아니요  |            예  |            예  |
|         집합의 Vm 나열  |            예  |            예  |            예, AvSet의 Vm을 나열 합니다.  |
|         Azure Alerts  |            아니요  |            예  |            예  |
|         VM 인사이트  |            아니요  |            예  |            예  |
|         Azure Backup  |            예  |            예  |            예  |
|         Azure Site Recovery  |            예, PowerShell만  |            예  |            예  |
|         그룹에 기존 VM 추가/제거  |            아니요  |            아니요  |            아니요  | 


## <a name="register-for-flexible-orchestration-mode"></a>유연한 오케스트레이션 모드 등록
유연한 오케스트레이션 모드로 가상 머신 확장 집합을 배포 하려면 먼저 미리 보기 기능에 대 한 구독을 등록 해야 합니다. 등록을 완료 하는 데 몇 분 정도 걸릴 수 있습니다. 다음 Azure PowerShell 또는 Azure CLI 명령을 사용 하 여 등록할 수 있습니다.

### <a name="azure-powershell"></a>Azure PowerShell 
[AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) cmdlet을 사용 하 여 구독에 대 한 미리 보기를 사용 하도록 설정 합니다. 

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName VMOrchestratorMultiFD -ProviderNamespace Microsoft.Compute `
Register-AzProviderFeature -FeatureName VMOrchestratorSingleFD -ProviderNamespace Microsoft.Compute  
```

기능 등록에는 최대 15 분이 걸릴 수 있습니다. 등록 상태를 확인하는 방법은 다음과 같습니다. 

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName VMOrchestratorMultiFD -ProviderNamespace Microsoft.Compute 
```

구독에 대 한 기능을 등록 한 후에는 계산 리소스 공급자에 변경 내용을 전파 하 여 옵트인 프로세스를 완료 합니다. 

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute 
```

### <a name="azure-cli-20"></a>Azure CLI 2.0 
[Az feature register](/cli/azure/feature#az-feature-register) 를 사용 하 여 구독에 대 한 미리 보기를 사용 하도록 설정 합니다. 

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name VMOrchestratorMultiFD
az feature register --namespace microsoft.compute --name VMOrchestratorSingleFD 
```

기능 등록에는 최대 15 분이 걸릴 수 있습니다. 등록 상태를 확인하는 방법은 다음과 같습니다. 

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name VMOrchestratorMultiFD 
```

구독에 대 한 기능을 등록 한 후에는 계산 리소스 공급자에 변경 내용을 전파 하 여 옵트인 프로세스를 완료 합니다. 

```azurecli-interactive
az provider register --namespace Microsoft.Compute 
```


## <a name="get-started-with-flexible-orchestration-mode"></a>유연한 오케스트레이션 모드 시작

Azure Portal, Azure CLI, Terraform 또는 REST API를 통해 확장 집합에 대 한 유연한 오케스트레이션 모드로 시작 하세요.

### <a name="azure-portal"></a>Azure portal

Azure Portal를 통해 유연한 오케스트레이션 모드로 가상 머신 확장 집합을 만듭니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 검색 창에서 **가상 머신 확장 집합** 을 검색 하 고 선택 합니다. 
1. **Virtual machine scale sets** 페이지에서 **만들기** 를 선택 합니다.
1. **가상 머신 확장 집합 만들기** 페이지에서 **오케스트레이션** 섹션을 봅니다.
1. **오케스트레이션 모드** 의 경우 **유연한** 옵션을 선택 합니다.
1. **장애 도메인 개수** 를 설정 합니다.
1. 크기 집합 만들기를 완료 합니다. 크기 집합을 만드는 방법에 대 한 자세한 내용은 [Azure Portal에서 확장 집합 만들기](quick-create-portal.md#create-virtual-machine-scale-set) 를 참조 하세요.

:::image type="content" source="./media/virtual-machine-scale-sets-orchestration-modes/portal-create-orchestration-mode-flexible.png" alt-text="확장 집합을 만들 때 포털의 오케스트레이션 모드":::

다음으로, 유연한 오케스트레이션 모드에서 확장 집합에 가상 머신을 추가 합니다.

1. 검색 창에서 **가상 컴퓨터** 를 검색 하 고 선택 합니다.
1. **가상 컴퓨터** 페이지에서 **추가** 를 선택 합니다.
1. **기본 사항** 탭에서 **인스턴스 세부 정보** 섹션을 확인 합니다.
1. **가용성 옵션** 에서 확장 집합을 선택 하 여 유연한 오케스트레이션 모드에서 확장 집합에 VM을 추가 합니다. 동일한 지역, 영역 및 리소스 그룹의 확장 집합에 가상 머신을 추가할 수 있습니다.
1. 가상 컴퓨터 만들기를 완료 합니다. 

:::image type="content" source="./media/virtual-machine-scale-sets-orchestration-modes/vm-portal-orchestration-mode-flexible.png" alt-text="유연한 오케스트레이션 모드 확장 집합에 VM 추가":::


### <a name="azure-cli-20"></a>Azure CLI 2.0
Azure CLI를 사용 하 여 유연한 가상 머신 확장 집합을 만듭니다. 다음 예제에서는 장애 도메인 수가 3으로 설정 된 유연한 확장 집합을 만드는 방법을 보여 줍니다. 그러면 가상 머신이 만들어지고 유연한 확장 집합에 추가 됩니다. 

```azurecli-interactive
vmssflexname="my-vmss-vmssflex"  
vmname="myVM"  
rg="my-resource-group"  

az group create -n "$rg" -l $location  
az vmss create -n "$vmssflexname" -g "$rg" -l $location --orchestration-mode flexible --platform-fault-domain-count 3  
az vm create -n "$vmname" -g "$rg" -l $location --vmss $vmssflexname --image UbuntuLTS 
```

### <a name="terraform"></a>Terraform
Terraform을 사용 하 여 유연한 가상 머신 확장 집합을 만듭니다. 이 프로세스에는 **Terraform Azurerm provider v 2.15.0** 이상이 필요 합니다. 다음 매개 변수를 확인 합니다.
- 영역을 지정 하지 않으면 `platform_fault_domain_count` 지역에 따라은 1, 2 또는 3이 될 수 있습니다.
- 영역을 지정 하면가 `the fault domain count` 1이 될 수 있습니다.
- `single_placement_group``false`유연한 가상 머신 확장 집합에 대 한 매개 변수는 이어야 합니다.
- 지역별 배포를 수행 하는 경우에는를 지정할 필요가 없습니다 `zones` .

```terraform
resource "azurerm orchestrated_virtual_machine_scale_set" "tf_vmssflex" {
name = "tf_vmssflex"
location = azurerm_resource_group.myterraformgroup.location
resource_group_name = azurerm_resource_group.myterraformgroup.name
platform_fault_domain_count = 1
single_placement_group = false
zones = ["1"]
}
```


### <a name="rest-api"></a>REST API

1. 빈 확장 집합을 만듭니다. 필수 매개 변수는 다음과 같습니다.
    - API 버전 2019-12-01 이상 
    - `false`유연한 확장 집합을 만들 때 단일 배치 그룹은 여야 합니다.

    ```json
    {
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "name": "[parameters('virtualMachineScaleSetName')]",
    "apiVersion": "2019-12-01",
    "location": "[parameters('location')]",
    "properties": {
        "singlePlacementGroup": false,
        "platformFaultDomainCount": "[parameters('virtualMachineScaleSetPlatformFaultDomainCount')]"
        },
    "zones": "[variables('selectedZone')]"
    }
    ```

2. 확장 집합에 가상 컴퓨터를 추가 합니다.
    1. `virtualMachineScaleSet`이전에 만든 확장 집합에 속성을 할당 합니다. VM을 만들 때 속성을 지정 해야 `virtualMachineScaleSet` 합니다. 
    1. **Copy ()** Azure Resource Manager template 함수를 사용 하 여 여러 vm을 동시에 만들 수 있습니다. Azure Resource Manager 템플릿에서 [리소스 반복](https://docs.microsoft.com/azure/azure-resource-manager/templates/copy-resources#iteration-for-a-child-resource) 을 참조 하세요. 

    ```json
    {
    "type": "Microsoft.Compute/virtualMachines",
    "name": "[concat(parameters('virtualMachineNamePrefix'), copyIndex(1))]",
    "apiVersion": "2019-12-01",
    "location": "[parameters('location')]",
    "copy": {
        "name": "VMcopy",
        "count": "[parameters('virtualMachineCount')]"
        },
    "dependsOn": [
        "
        [resourceID('Microsoft.Compute/virtualMachineScaleSets', parameters('virtualMachineScaleSetName'))]",
        "
        [resourceID('Microsoft.Storage/storageAccounts', variables('diagnosticsStorageAccountName'))]",
        "
        [resourceID('Microsoft.Network/networkInterfaces', concat(parameters('virtualMachineNamePrefix'), copyIndex(1), '-NIC1'))]"
        ],
    "properties": {
        "virtualMachineScaleSet": {
            "id": "[resourceID('Microsoft.Compute/virtualMachineScaleSets', parameters('virtualMachineScaleSetName'))]"
        }
    }
    ```

전체 예는 [Azure 빠른](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-vmss-flexible-orchestration-mode) 시작을 참조 하세요.


## <a name="frequently-asked-questions"></a>질문과 대답

**유연한 오케스트레이션이 지 원하는 크기는 얼마나 되나요?**

유연한 오케스트레이션 모드에서 확장 집합에 최대 1000 개의 Vm을 추가할 수 있습니다.

**유연한 오케스트레이션이 있는 가용성은 가용성 집합 또는 균일 오케스트레이션과 어떻게 비교 되나요?**

|   | 유연한 오케스트레이션  | 단일 오케스트레이션  | 가용성 집합  |
|-|-|-|-|
| 가용성 영역 간 배포  | 아니요  | 예  | 아니요  |
| 지역 내에서 장애 도메인 가용성 보장  | 예, 최대 1000 개 인스턴스는 지역에서 최대 3 개의 장애 도메인에 걸쳐 분산 될 수 있습니다. 최대 장애 도메인 수는 지역에 따라 다릅니다.  | 예, 최대 100 인스턴스  | 예, 최대 200 인스턴스  |
| 배치 그룹  | 유연 모드는 항상 여러 배치 그룹을 사용 합니다 (Singleementgroup = false).  | 단일 배치 그룹 또는 여러 배치 그룹을 선택할 수 있습니다. | 해당 없음  |
| 업데이트 도메인  | 없음, 유지 관리 또는 호스트 업데이트가 장애 도메인에 의해 수행 되지 않음  | 최대 5 개의 업데이트 도메인  | 최대 20 개의 업데이트 도메인  |


## <a name="troubleshoot-scale-sets-with-flexible-orchestration"></a>유연한 오케스트레이션을 사용 하 여 크기 집합 문제 해결
문제 해결 시나리오에 적합 한 솔루션을 찾습니다. 

```
InvalidParameter. The value 'False' of parameter 'singlePlacementGroup' is not allowed. Allowed values are: True
```

**원인:** 구독이 유연한 오케스트레이션 모드 공개 미리 보기에 등록 되어 있지 않습니다. 

**해결 방법:** 위의 지침에 따라 유연한 오케스트레이션 모드 공개 미리 보기에 등록 합니다. 

```
InvalidParameter. The specified fault domain count 2 must fall in the range 1 to 1.
```

**원인:** `platformFaultDomainCount` 선택한 영역 또는 영역에 대 한 매개 변수가 잘못 되었습니다. 

**해결 방법:** 올바른 값을 선택 해야 `platformFaultDomainCount` 합니다. 영역 배포의 경우 최대값은 `platformFaultDomainCount` 1입니다. 영역을 지정 하지 않는 지역 배포의 경우 최대값은 `platformFaultDomainCount` 지역에 따라 다릅니다. 지역별 최대 장애 도메인 수를 확인 하는 [스크립트에 대 한 vm의 가용성 관리](../virtual-machines/availability.md) 를 참조 하세요. 

```
OperationNotAllowed. Deletion of Virtual Machine Scale Set is not allowed as it contains one or more VMs. Please delete or detach the VM(s) before deleting the Virtual Machine Scale Set.
```

**원인:** 하나 이상의 가상 컴퓨터와 연결 된 유연한 오케스트레이션 모드에서 확장 집합을 삭제 하려고 합니다. 

**해결 방법:** 유연한 오케스트레이션 모드에서 확장 집합에 연결 된 모든 가상 머신을 삭제 한 다음 확장 집합을 삭제할 수 있습니다.

```
InvalidParameter. The value 'True' of parameter 'singlePlacementGroup' is not allowed. Allowed values are: False.
```
**원인:** 구독이 유연한 오케스트레이션 모드 미리 보기에 등록 되어 있습니다. 그러나 `singlePlacementGroup` 매개 변수는 *True* 로 설정 됩니다. 

**해결 방법:** 는 `singlePlacementGroup` *False* 로 설정 해야 합니다. 


## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [확장 집합에 응용 프로그램을 배포 하는 방법을 알아봅니다.](virtual-machine-scale-sets-deploy-app.md)
