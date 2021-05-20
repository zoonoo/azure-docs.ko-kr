---
title: Service Fabric 클러스터에서 Windows 운영 체제 패치
description: 이 문서에서는 Service Fabric 클러스터에서 패치 오케스트레이션 애플리케이션을 사용하여 운영 체제 패치를 자동화하는 방법을 설명합니다.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: de7dacf5-4038-434a-a265-5d0de80a9b1d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/01/2019
ms.author: atsenthi
ms.openlocfilehash: e94b809513bda8edc7a51baf79ec05a2c9c77489
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448557"
---
# <a name="patch-the-windows-operating-system-in-your-service-fabric-cluster"></a>Service Fabric 클러스터에서 Windows 운영 체제 패치

## <a name="automatic-os-image-upgrades"></a>자동 OS 이미지 업그레이드

Azure에서 운영 체제를 최신 패치로 유지하는 모범 사례는 [Virtual Machine Scale Sets에서 자동 OS 이미지 업그레이드](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md)를 구현하는 것입니다. Virtual Machine Scale Sets 기반 자동 OS 이미지 업그레이드를 사용하려면 확장 집합에서 Silver 이상의 내구성이 필요합니다.

Virtual Machine Scale Sets에서 자동 OS 이미지 업그레이드를 사용하기 위한 요구 사항
-   Service Fabric [내구성 수준](../service-fabric/service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster)은 Bronze가 아니라 Silver 또는 Gold입니다.
-   확장 집합 모델 정의의 Service Fabric 확장은 TypeHandlerVersion 1.1 이상이어야 합니다.
-   내구성 수준은 확장 집합 모델 정의의 Service Fabric 클러스터와 Service Fabric 확장에서 동일해야 합니다.
- Virtual Machine Scale Sets에 대한 추가 상태 프로브 또는 애플리케이션 상태 확장을 사용할 필요는 없습니다.

Service Fabric 클러스터와 Service Fabric 확장의 내구성 설정이 일치해야 합니다. 그렇지 않으면 불일치로 인해 업그레이드 오류가 발생합니다. 내구성 수준은 [이 페이지](../service-fabric/service-fabric-cluster-capacity.md#changing-durability-levels)에 설명된 지침에 따라 수정할 수 있습니다.

내구성 수준이 Bronze이면 자동 OS 이미지 업그레이드를 사용할 수 없습니다. Silver 이상의 내구성 수준에는 [패치 오케스트레이션 애플리케이션](#patch-orchestration-application )(Azure가 아닌 호스트 클러스터에만 사용)이 *권장되지 않지만*, Service Fabric 업그레이드 도메인과 관련하여 Windows 업데이트를 자동화하는 유일한 옵션입니다.

> [!IMPORTANT]
> OS 디스크를 바꾸지 않고 "Windows 업데이트"에서 운영 체제 패치를 적용하는 VM 내 업그레이드는 Azure Service Fabric에서 지원되지 않습니다.

운영 체제에서 Windows 업데이트 기능을 사용하지 않고 이 기능을 올바르게 사용하려면 두 가지 단계를 수행해야 합니다.

1. 자동 OS 이미지 업그레이드를 사용하도록 설정하고 Windows 업데이트 ARM을 사용하지 않도록 설정합니다. 
    ```json
    "virtualMachineProfile": { 
        "properties": {
          "upgradePolicy": {
            "automaticOSUpgradePolicy": {
              "enableAutomaticOSUpgrade":  true
            }
          }
        }
      }
    ```
    
    ```json
    "virtualMachineProfile": { 
        "osProfile": { 
            "windowsConfiguration": { 
                "enableAutomaticUpdates": false 
            }
        }
    }
    ```

    Azure PowerShell
    ```azurepowershell-interactive
    Update-AzVmss -ResourceGroupName $resourceGroupName -VMScaleSetName $scaleSetName -AutomaticOSUpgrade $true -EnableAutomaticUpdate $false
    ``` 
    
1. 확장 집합 모델을 업데이트합니다. 이 구성이 변경되면 모든 머신을 이미지로 다시 설치하여 확장 집합 모델을 업데이트해야 합니다. 그래야만 변경 내용이 적용됩니다.
    
    Azure PowerShell
    ```azurepowershell-interactive
    $scaleSet = Get-AzVmssVM -ResourceGroupName $resourceGroupName -VMScaleSetName $scaleSetName
    $instances = foreach($vm in $scaleSet)
    {
        Set-AzVmssVM -ResourceGroupName $resourceGroupName -VMScaleSetName $scaleSetName -InstanceId $vm.InstanceID -Reimage
    }
    ``` 
    
추가 지침은 [Virtual Machine Scale Sets를 사용하여 자동 OS 이미지 업그레이드](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md)를 참조하세요.

## <a name="patch-orchestration-application"></a>패치 오케스트레이션 애플리케이션

> [!IMPORTANT]
> 2019년 4월 30일부터 패치 오케스트레이션 애플리케이션 1.2.* 버전이 더 이상 지원되지 않습니다. 최신 버전으로 업그레이드해야 합니다.

POA(패치 오케스트레이션 애플리케이션)는 Azure에 호스트되지 않는 클러스터에 대해 구성 기반 OS 패치 일정을 예약할 수 있는 Azure Service Fabric Repair Manager 서비스의 래퍼입니다. Azure에 호스트되지 않는 클러스터에는 POA가 필요하지 않지만, 가동 중지 시간 없이 Service Fabric 클러스터 호스트를 패치하려면 업그레이드 도메인에 의한 패치 설치를 예약해야 합니다.

POA는 Service Fabric 클러스터에서 가동 중지 시간 없이 운영 체제 패치를 자동화하는 Service Fabric 애플리케이션입니다.

POA는 다음과 같은 기능을 제공합니다.

- **자동 운영 체제 업데이트 설치**. 운영 체제 업데이트가 자동으로 다운로드되고 설치됩니다. 필요할 때 클러스터 가동 중지 시간 없이 클러스터 노드가 다시 부팅됩니다.

- **클러스터 인식 패치 및 상태 통합**. POA는 업데이트를 적용하는 동안 클러스터 노드의 상태를 모니터링합니다. 클러스터 노드는 한 번에 하나의 노드씩 또는 한 번에 하나의 업그레이드 도메인씩 업그레이드됩니다. 패치 프로세스로 인해 클러스터가 다운되면 문제가 악화되는 것을 방지하기 위해 프로세스가 중지됩니다.

## <a name="internal-details-of-poa"></a>POA 내부 세부 정보

POA는 다음과 같은 하위 구성 요소로 구성됩니다.

- **코디네이터 서비스**: 이 상태 저장 서비스는 다음과 같은 역할을 합니다.
    - 전체 클러스터에서 Windows Update 작업을 조정합니다.
    - 완료된 Windows 업데이트 작업의 결과를 저장합니다.

- **노드 에이전트 서비스**: 이 상태 비저장 서비스는 모든 Service Fabric 클러스터 노드에서 실행됩니다. 서비스는 다음과 같은 역할을 합니다.
    - 노드 에이전트 NTService의 부트스트랩
    - 노드 에이전트 NTService의 모니터링

- **노드 에이전트 NTService**: 이 Windows NT 서비스는 더 높은 수준의 권한(시스템)에서 실행됩니다. 반면, 노드 에이전트 서비스 및 코디네이터 서비스는 하위 수준의 권한(네트워크 서비스)에서 실행됩니다. 서비스는 모든 클러스터 노드에서 다음과 같은 Windows 업데이트 작업을 수행하는 일을 담당합니다.
    - 노드에서 자동 Windows Update 비활성화
    - 사용자가 제공한 정책에 따라 Windows 업데이트 다운로드 및 설치
    - Windows 업데이트 설치 후 머신 다시 시작
    - 코디네이터 서비스에 Windows 업데이트 결과 업로드
    - 재시도 횟수를 모두 사용한 후 작업이 실패하면 상태 보고서 전달

> [!NOTE]
> POA는 Service Fabric Repair Manager 서비스를 사용하여 노드를 사용하도록 또는 사용하지 않도록 설정하고 상태 검사를 수행합니다. POA에서 만든 복구 작업은 각 노드의 Windows 업데이트 진행률을 추적합니다.

## <a name="prerequisites"></a>필수 구성 요소

> [!NOTE]
> 필요한 최소 .NET Framework 버전은 4.6입니다.

### <a name="enable-the-repair-manager-service-if-its-not-running-already"></a>Repair Manager 서비스 사용(아직 실행되지 않은 경우)

POA를 사용하려면 클러스터에서 Repair Manager 서비스를 사용하도록 설정해야 합니다.

#### <a name="azure-clusters"></a>Azure 클러스터

Silver 내구성 계층의 Azure 클러스터는 기본적으로 Repair Manager 서비스를 사용하도록 설정됩니다. Gold 내구성 계층의 Azure 클러스터는 해당 클러스터가 만들어진 시기에 따라 Repair Manager 서비스를 사용하도록 설정될 수도 있고 그렇지 않을 수도 있습니다. Bronze 내구성 계층의 Azure 클러스터는 기본적으로 Repair Manager 서비스를 사용하도록 설정되지 않습니다. 서비스가 이미 사용하도록 설정되어 있는 경우 Service Fabric Explorer의 시스템 서비스 섹션에서 서비스가 실행되고 있는 것을 볼 수 있습니다.

##### <a name="the-azure-portal"></a>Azure Portal
클러스터를 설정할 때 Azure Portal에서 Repair Manager를 사용하도록 설정할 수 있습니다. 클러스터를 구성할 때 **추가 기능** 에서 **Repair Manager 포함** 옵션을 선택합니다.

![Azure Portal에서 Repair Manager를 사용하도록 설정하는 이미지](media/service-fabric-patch-orchestration-application/EnableRepairManager.png)

##### <a name="the-azure-resource-manager-deployment-model"></a>Azure Resource Manager 배포 모델:
또는 [Azure Resource Manager 배포 모델](./service-fabric-cluster-creation-via-arm.md)을 사용하여 신규 및 기존 Service Fabric 클러스터에서 Repair Manager 서비스를 사용하도록 설정할 수 있습니다. 배포하려는 클러스터에 대한 템플릿을 가져옵니다. 예제 템플릿을 사용하거나 사용자 지정 Azure Resource Manager 배포 모델을 만들 수 있습니다. 

[Azure Resource Manager 배포 모델 템플릿](./service-fabric-cluster-creation-via-arm.md)을 사용하여 Repair Manager 서비스를 사용하도록 설정하려면 다음을 수행합니다.

1. *Microsoft.ServiceFabric/clusters* 리소스의 `apiVersion`이 *2017-07-01-preview* 로 설정되었는지 확인합니다. 설정되지 않았다면 `apiVersion`을 *2017-07-01-preview* 이상으로 업데이트해야 합니다.

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

1. 다음 `addonFeatures` 섹션을 `fabricSettings` 섹션 뒤에 추가하여 Repair Manager 서비스를 사용하도록 설정합니다.

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

3. 클러스터 템플릿을 이러한 변경 내용으로 업데이트한 후에는 변경 내용을 적용하고 업그레이드가 완료되기를 기다립니다. 이제 클러스터에서 Repair Manager 서비스가 실행되고 있는 것을 볼 수 있습니다. Service Fabric Explorer의 시스템 서비스 섹션에 *fabric:/System/RepairManagerService* 라고 표시됩니다. 

### <a name="standalone-on-premises-clusters"></a>독립 실행형 온-프레미스 클러스터

신규 및 기존 Service Fabric 클러스터에서 Repair Manager 서비스를 사용하도록 설정하려면 [독립 실행형 Windows 클러스터에 대한 구성 설정](./service-fabric-cluster-manifest.md)을 사용하면 됩니다.

Repair Manager 서비스를 사용하도록 설정하려면 다음을 수행합니다.

1. 다음과 같이 [일반 클러스터 구성](./service-fabric-cluster-manifest.md#general-cluster-configurations)에서 `apiVersion`이 *04-2017* 이상으로 설정되어 있는지 확인합니다.

    ```json
    {
        "name": "SampleCluster",
        "clusterConfigurationVersion": "1.0.0",
        "apiVersion": "04-2017",
        ...
    }
    ```

1. 아래와 같이 다음 `addonFeatures` 섹션을 `fabricSettings` 섹션 뒤에 추가하여 Repair Manager 서비스를 사용하도록 설정합니다.

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

1. 업데이트된 클러스터 매니페스트의 [새 클러스터 만들기](./service-fabric-cluster-creation-for-windows-server.md) 또는 [클러스터 구성 업그레이드](./service-fabric-cluster-upgrade-windows-server.md)를 사용하여 이러한 변경 내용으로 클러스터 매니페스트를 업데이트합니다. 

   클러스터가 업데이트된 클러스터 매니페스트로 실행되면 클러스터에서 실행 중인 Repair Manager 서비스를 볼 수 있습니다. *fabric:/System/RepairManagerService* 라고 표시되며 Service Fabric Explorer의 시스템 서비스 섹션에 있습니다.

### <a name="configure-windows-updates-for-all-nodes"></a>모든 노드에 Windows 업데이트 구성

자동 Windows 업데이트를 사용하면 여러 클러스터 노드가 동시에 다시 시작되어 가용성 손실이 발생할 수 있습니다. POA는 기본적으로 각 클러스터 노드에서 자동 Windows 업데이트를 사용하지 않으려 합니다. 그러나 설정을 관리자 또는 그룹 정책에서 관리하는 경우 Windows 업데이트 정책을 “다운로드하기 전에 알림”으로 명시적으로 설정하는 것이 좋습니다.

## <a name="download-the-application-package"></a>애플리케이션 패키지 다운로드

애플리케이션 패키지를 다운로드하려면 GitHub의 [패치 오케스트레이션 애플리케이션 릴리스 페이지](https://github.com/microsoft/Service-Fabric-POA/releases/latest/)로 이동합니다.

## <a name="configure-poa-behavior"></a>POA 동작 구성

요구 사항에 맞게 POA 동작을 구성할 수 있습니다. 애플리케이션을 만들거나 업데이트하는 동안 애플리케이션 매개 변수를 전달하여 기본값을 재정의합니다. `ApplicationParameter`를 `Start-ServiceFabricApplicationUpgrade` 또는 `New-ServiceFabricApplication` cmdlet으로 지정하여 애플리케이션 매개 변수를 제공할 수 있습니다.

| 매개 변수        | 형식                          | 세부 정보 |
|:-|-|-|
|MaxResultsToCache    |long                              | 캐시해야 하는 Windows 업데이트 결과의 최대 수입니다. <br><br>기본값은 3000이며 다음을 가정합니다. <br> &nbsp;&nbsp;- 노드 수는 20개입니다. <br> &nbsp;&nbsp;- 월간 노드 업데이트 수는 5입니다. <br> &nbsp;&nbsp;- 작업당 결과 수는 10일 수 있습니다. <br> &nbsp;&nbsp;- 지난 3개월의 결과를 저장해야 합니다. |
|TaskApprovalPolicy   |열거형 <br> { NodeWise, UpgradeDomainWise }                          |TaskApprovalPolicy는 Service Fabric 클러스터 노드에서 Windows 업데이트를 설치하기 위해 코디네이터 서비스에서 사용하는 정책을 나타냅니다.<br><br>허용되는 값은 다음과 같습니다. <br>*NodeWise*: Windows 업데이트가 한 번에 하나의 노드에 설치됩니다. <br> *UpgradeDomainWise*: Windows 업데이트가 한 번에 하나의 업데이트 도메인에 설치됩니다. (기껏해야 하나의 업그레이드 도메인에 속한 모든 노드가 Windows 업데이트에 해당될 수 있습니다.)<br><br> 클러스터에 가장 적합한 정책을 결정하는 데 도움이 필요하면 [FAQ](#frequently-asked-questions) 섹션을 참조하세요.
|LogsDiskQuotaInMB   |long  <br> (기본값: *1024*)               | 패치 오케스트레이션 앱 로그의 최대 크기(MB)로, 노드에서 로컬로 유지될 수 있습니다.
| WUQuery               | 문자열<br>(기본값: *IsInstalled=0*)                | Windows 업데이트를 가져올 쿼리입니다. 자세한 내용은 [WuQuery](/windows/win32/api/wuapi/nf-wuapi-iupdatesearcher-search)를 참조하세요.
| InstallWindowsOSOnlyUpdates | *Boolean* <br> (기본값: false)                 | 이 플래그를 사용하여 다운로드하고 설치해야 하는 업데이트를 제어합니다. 다음 값이 허용됩니다. <br>true - Windows 운영 체제 업데이트만 설치합니다.<br>false - 컴퓨터에서 사용 가능한 모든 업데이트를 설치합니다.          |
| WUOperationTimeOutInMinutes | Int <br>(기본값: *90*)                   | Windows 업데이트 작업에 대한 시간 제한을 지정합니다(검색, 다운로드 또는 설치). 지정된 시간 제한 내에 작업이 완료되지 않으면 중단됩니다.       |
| WURescheduleCount     | Int <br> (기본값: *5*)                  | 작업이 영구적으로 실패하는 경우 서비스에서 Windows 업데이트를 다시 예약하는 최대 횟수입니다.          |
| WURescheduleTimeInMinutes | Int <br>(기본값: *30*) | 오류가 계속 발생하는 경우 서비스에서 Windows 업데이트를 다시 예약하는 간격입니다. |
| WUFrequency           | 쉼표로 구분된 문자열(기본값: *매주, 수요일, 7:00:00*)     | Windows 업데이트를 설치하는 빈도입니다. 형식 및 가능한 값은 다음과 같습니다. <br>- 매월, DD, HH:MM:SS(예: *매월, 5일, 12:22:32*) 필드 _DD_(일)에 허용되는 값은 1~28 사이의 숫자와 _last_ 입니다. <br>- 매주, 요일, HH:MM:SS(예: *매주, 화요일, 12:22:32*)  <br>- 매일, HH:MM:SS(예: *매일, 12:22:32*)  <br>- MonthlyByWeekAndDay, 주, 요일, HH:MM:SS(예: *MonthlyByWeekAndDay, 2, 금요일, 21:00:00* 는 매월 둘째 주 금요일 오후 9시(UTC)를 의미) <br>- *없음* 은 Windows 업데이트를 수행하지 않는다는 뜻입니다.  <br><br> 시간은 UTC 기준입니다.|
| AcceptWindowsUpdateEula | 부울 <br>(기본값: *true*) | 이 플래그를 설정하면 애플리케이션이 컴퓨터의 소유자를 대신하여 Windows 업데이트에 대한 최종 사용자 사용권 계약에 동의합니다.              |

> [!TIP]
> Windows 업데이트가 즉시 처리되도록 하려면 애플리케이션 배포 시간에 관한 `WUFrequency`를 설정하세요. 예를 들어 5노드 테스트 클러스터가 있고 약 5PM UTC에 앱을 배포할 계획이라고 가정할 수 있습니다. 애플리케이션 업그레이드 또는 배포에 최대 30분이 소요된다고 가정하는 경우 WUFrequency를 *매일, 17:30:00* 으로 설정하세요.

## <a name="deploy-poa"></a>POA 배포

1. 모든 필수 구성 요소 단계를 완료하여 클러스터를 준비합니다.
1. 다른 Service Fabric 앱과 비슷한 방법으로 POA를 배포합니다. PowerShell을 사용하여 배포하려면 [PowerShell을 사용하여 애플리케이션 배포 및 제거](./service-fabric-deploy-remove-applications.md)를 참조하세요.
1. 배포 시 애플리케이션을 구성하려면 `ApplicationParameter`를 `New-ServiceFabricApplication` cmdlet에 전달합니다. 편의를 위해 Deploy.ps1 스크립트가 애플리케이션과 함께 제공됩니다. 스크립트를 사용하려면 다음을 수행합니다.

    - `Connect-ServiceFabricCluster`를 사용하여 Service Fabric 클러스터에 연결합니다.
    - 적절한 `ApplicationParameter` 값을 사용하여 PowerShell 스크립트 Deploy.ps1을 실행합니다.

> [!NOTE]
> 스크립트 및 애플리케이션 폴더 *PatchOrchestrationApplication* 을 동일한 디렉터리에 유지합니다.

## <a name="upgrade-poa"></a>POA 업그레이드

PowerShell을 사용하여 POA 버전을 업그레이드하려면 [PowerShell을 사용하여 Service Fabric 애플리케이션 업그레이드](./service-fabric-application-upgrade-tutorial-powershell.md)의 지침을 따릅니다.

## <a name="remove-poa"></a>POA 제거

애플리케이션을 제거하려면 [PowerShell을 사용하여 애플리케이션 배포 및 제거](./service-fabric-deploy-remove-applications.md)의 지침을 따릅니다.

편의를 위해 Undeploy.ps1 스크립트가 애플리케이션과 함께 제공됩니다. 스크립트를 사용하려면 다음을 수행합니다.

  - ```Connect-ServiceFabricCluster```를 사용하여 Service Fabric 클러스터에 연결합니다.
  - PowerShell 스크립트 Undeploy.ps1을 실행합니다.

> [!NOTE]
> 스크립트 및 애플리케이션 폴더 *PatchOrchestrationApplication* 을 동일한 디렉터리에 유지합니다.

## <a name="view-the-windows-update-results"></a>Windows 업데이트 결과 보기

POA는 사용자에게 기록 결과를 표시하는 REST API를 노출합니다. 다음은 결과 JSON의 예입니다.

```json
[
  {
    "NodeName": "_stg1vm_1",
    "WindowsUpdateOperationResults": [
      {
        "OperationResult": 0,
        "NodeName": "_stg1vm_1",
        "OperationTime": "2019-05-13T08:44:56.4836889Z",
        "OperationStartTime": "2019-05-13T08:44:33.5285601Z",
        "UpdateDetails": [
          {
            "UpdateId": "7392acaf-6a85-427c-8a8d-058c25beb0d6",
            "Title": "Cumulative Security Update for Internet Explorer 11 for Windows Server 2012 R2 (KB3185319)",
            "Description": "A security issue has been identified in a Microsoft software product that could affect your system. You can help protect your system by installing this update from Microsoft. For a complete listing of the issues that are included in this update, see the associated Microsoft Knowledge Base article. After you install this update, you may have to restart your system.",
            "ResultCode": 0,
            "HResult": 0
          }
        ],
        "OperationType": 1,
        "WindowsUpdateQuery": "IsInstalled=0",
        "WindowsUpdateFrequency": "Daily,10:00:00",
        "RebootRequired": false
      }
    ]
  },
  ...
]
```

다음 표에는 JSON 필드가 설명되어 있습니다.

필드 | 값 | 세부 정보
-- | -- | --
OperationResult | 0 - 성공<br> 1 - 성공하였으나 오류 발생<br> 2 - 실패<br> 3 - 중단<br> 4 - 시간 제한으로 중단 | 전체 작업의 결과를 나타내며, 일반적으로 하나 이상의 업데이트를 설치하는 작업이 포함됩니다.
ResultCode | OperationResult와 동일 | 이 필드는 개별 업데이트를 위한 설치 작업의 결과를 나타냅니다.
OperationType | 1 - 설치<br> 0 - 검색 및 다운로드| 설치는 기본적으로 결과에 표시되는 유일한 OperationType입니다.
WindowsUpdateQuery | 기본값은 “IsInstalled = 0”입니다. | 업데이트 검색에 사용된 Windows 업데이트 쿼리입니다. 자세한 내용은 [WuQuery](/windows/win32/api/wuapi/nf-wuapi-iupdatesearcher-search)를 참조하세요.
RebootRequired | true - 다시 부팅 필요<br> false - 다시 부팅 필요 없음 | 업데이트 설치를 완료하려면 재부팅이 필요한지 여부를 표시합니다.
OperationStartTime | DateTime | 작업(다운로드/설치)이 시작된 시간을 나타냅니다.
OperationTime | DateTime | 작업(다운로드/설치)이 완료된 시간을 나타냅니다.
HResult | 0 - 성공<br> 기타 - 실패| updateID "7392acaf-6a85-427c-8a8d-058c25beb0d6"의 Windows 업데이트가 실패한 이유를 나타냅니다.

업데이트가 아직 예약되어 있지 않으면 결과 JSON은 비어 있습니다.

Windows 업데이트 결과를 쿼리하려면 클러스터에 로그인합니다. 코디네이터 서비스 기본 주소의 복제본 IP 주소를 찾고, 브라우저에서 URL http://&lt;REPLICA-IP&gt;:&lt;ApplicationPort&gt;/PatchOrchestrationApplication/v1/GetWindowsUpdateResults를 입력합니다.

코디네이터 서비스의 REST 엔드포인트에는 동적 포트가 있습니다. 정확한 URL을 확인하려면 Service Fabric Explorer를 참조하세요. 예를 들어 *http://10.0.0.7:20000/PatchOrchestrationApplication/v1/GetWindowsUpdateResults* 에 결과가 제공됩니다.

![REST 엔드포인트의 이미지](media/service-fabric-patch-orchestration-application/Rest_Endpoint.png)

클러스터에서 역방향 프록시를 사용하는 경우 클러스터 외부에서도 URL에 액세스할 수 있습니다.

적중해야 하는 엔드포인트는 *http://&lt;SERVERURL&gt;:&lt;REVERSEPROXYPORT&gt;/PatchOrchestrationApplication/CoordinatorService/v1/GetWindowsUpdateResults* 입니다.

클러스터에서 역방향 프록시를 사용하려면 [Azure Service Fabric의 역방향 프록시](./service-fabric-reverseproxy.md)의 지침을 따릅니다. 

> 
> [!WARNING]
> 역방향 프록시가 구성된 후에는 HTTP 엔드포인트를 표시하는 클러스터에 있는 모든 마이크로서비스의 주소를 클러스터 외부에서 지정할 수 있습니다.

## <a name="diagnostics-and-health-events"></a>진단 및 상태 이벤트

이 섹션에서는 Service Fabric 클러스터에서 POA를 통해 패치 업데이트 관련 문제를 디버그하거나 진단하는 방법을 설명합니다.

> [!NOTE]
> 다음과 같은 여러 가지 자체 진단 기능을 사용하려면 POA 1.4.0 이상 버전을 설치해야 합니다.

노드 에이전트 NTService는 노드에 업데이트를 설치하기 위한 [복구 작업](/dotnet/api/system.fabric.repair.repairtask)을 만듭니다. 그 후 코디네이터 서비스가 작업 승인 정책에 따라 각 작업을 준비합니다. 마지막으로, 준비된 작업을 Repair Manager가 승인합니다. Repair Manager는 클러스터가 비정상 상태인 경우에는 작업을 승인하지 않습니다. 

노드에서 업데이트가 어떻게 진행되는지 이해를 돕기 위해 한 단계씩 진행해 보겠습니다.

1. 모든 노드에서 실행되는 노드 에이전트 NTService는 예약된 시간에 사용 가능한 Windows 업데이트를 찾습니다. 사용 가능한 업데이트가 있으면 해당 업데이트를 노드에 다운로드합니다.

1. 업데이트를 다운로드한 후, 노드 에이전트 NTService는 *POS___\<unique_id>* 라는 이름으로 노드에 대한 해당 복구 작업을 만듭니다. 이러한 복구 작업은 [Get-ServiceFabricRepairTask](/powershell/module/servicefabric/get-servicefabricrepairtask) cmdlet을 사용하거나 노드 세부 정보 섹션에서 SFX를 사용하여 볼 수 있습니다. 생성된 복구 작업은 신속하게 [*클레임됨* 상태](/dotnet/api/system.fabric.repair.repairtaskstate)로 전환됩니다.

1. 코디네이터 서비스는 *클레임됨* 상태인 복구 작업을 주기적으로 찾은 다음, TaskApprovalPolicy에 따라 상태를 *준비 중* 으로 업데이트합니다. TaskApprovalPolicy가 NodeWise로 구성되면 현재 *준비 중*, *승인됨*, *실행 중* 또는 *복원 중* 상태인 다른 복구 작업이 없는 경우에만 노드에 해당하는 복구 작업이 준비됩니다. 

   마찬가지로 UpgradeWise TaskApprovalPolicy의 경우 동일한 업데이트 도메인에 속하는 노드에 대해서만 위의 상태에 있는 작업이 있습니다. 복구 작업이 *준비 중* 상태로 전환된 후에는 해당하는 Service Fabric 노드가 [사용 안 함](/powershell/module/servicefabric/disable-servicefabricnode)으로 설정되고 의도가 *다시 시작* 으로 설정됩니다.

   POA 버전 1.4.0 이상에서는 패치 중인 노드를 표시하기 위해 CoordinatorService의 ClusterPatchingStatus 속성을 사용하여 이벤트를 게시합니다. 업데이트는 다음 이미지처럼 _poanode_0에 설치됩니다.

    [![클러스터 패치 상태 이미지](media/service-fabric-patch-orchestration-application/clusterpatchingstatus.png)](media/service-fabric-patch-orchestration-application/clusterpatchingstatus.png#lightbox)

1. 노드를 사용하지 않도록 설정한 후에는 복구 작업이 *실행 중* 상태로 전환됩니다. 
   
   > [!NOTE]
   > *사용 안 함* 상태로 멈춰 있는 노드가 새 복구 작업을 차단할 수 있으며, 이 경우 클러스터에서 패치 작업이 중단됩니다.

1. 복구 작업이 *실행 중* 상태이면 해당 노드에 패치 설치가 시작됩니다. 패치에 따라 패치가 설치된 후 노드가 다시 시작될 수도 있고 그렇지 않을 수도 있습니다. 다음으로, 복구 작업이 *복원 중* 상태로 전환되고, 노드가 다시 사용됩니다. 그런 다음, 복구 작업이 완료된 것으로 표시됩니다.

   POA 버전 1.4.0 이상에서는 WUOperationStatus-\<NodeName> 속성을 사용하여 NodeAgentService에서 상태 이벤트를 확인하면 업데이트 상태를 알 수 있습니다. 다음 이미지에서 강조 표시된 섹션은 *poanode_0* 및 *poanode_2* 노드의 Windows 업데이트 상태를 보여줍니다.

   [![poanode_0이 강조 표시된 Windows 업데이트 작업 상태의 콘솔 창을 보여주는 스크린샷](media/service-fabric-patch-orchestration-application/wuoperationstatusa.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusa.png#lightbox)

   [![poanode_1이 강조 표시된 Windows 업데이트 작업 상태의 콘솔 창을 보여주는 스크린샷](media/service-fabric-patch-orchestration-application/wuoperationstatusb.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusb.png#lightbox)

   PowerShell을 사용하여 세부 정보를 볼 수도 있습니다. 이렇게 하려면 클러스터에 연결하고 [Get-ServiceFabricRepairTask](/powershell/module/servicefabric/get-servicefabricrepairtask)를 사용하여 복구 작업의 상태를 가져옵니다. 
   
   다음 예제에서 "POS__poanode_2_125f2969-933c-4774-85d1-ebdf85e79f15" 작업은 *DownloadComplete* 상태입니다. *poanode_2* 노드에 업데이트가 다운로드되었으며, 작업이 *실행 중* 상태로 전환될 때 설치를 시도한다는 뜻입니다.

   ``` powershell
    D:\service-fabric-poa-bin\service-fabric-poa-bin\Release> $k = Get-ServiceFabricRepairTask -TaskId "POS__poanode_2_125f2969-933c-4774-85d1-ebdf85e79f15"

    D:\service-fabric-poa-bin\service-fabric-poa-bin\Release> $k.ExecutorData
    {"ExecutorSubState":2,"ExecutorTimeoutInMinutes":90,"RestartRequestedTime":"0001-01-01T00:00:00"}
    ```

   문제가 계속 발생하면 VM(가상 머신)에 로그인하고 Windows 이벤트 로그를 사용하여 문제에 대해 알아봅니다. 앞에서 언급한 복구 작업은 다음 실행기 하위 상태로만 존재할 수 있습니다.

      ExecutorSubState | Description
    -- | -- 
      None=1 |  노드에서 진행 중인 작업이 없다는 뜻입니다. 상태가 전환 중일 수 있습니다.
      DownloadCompleted=2 | 다운로드 작업이 성공, 부분 실패 또는 실패로 끝났다는 뜻입니다.
      InstallationApproved=3 | 다운로드 작업을 이전에 완료했으며 Repair Manager가 설치를 승인했다는 뜻입니다.
      InstallationInProgress=4 | 복구 작업의 실행 상태에 해당합니다.
      InstallationCompleted=5 | 설치가 성공, 부분 성공 또는 실패로 끝났다는 뜻입니다.
      RestartRequested=6 | 패치 설치가 완료되었으며 노드에서 보류 중인 다시 시작 작업이 있다는 뜻입니다.
      RestartNotNeeded=7 |  패치 설치가 완료된 후 다시 시작할 필요가 없다는 뜻입니다.
      RestartCompleted=8 | 재시작이 성공적으로 완료되었다는 뜻입니다.
      OperationCompleted=9 | Windows 업데이트 작업이 성공적으로 완료되었습니다.
      OperationAborted=10 | Windows 업데이트 작업이 중단되었다는 뜻입니다.

1. POA 버전 1.4.0 이상에서는 노드 업데이트 시도가 완료되면 다음에 Windows 업데이트를 다운로드하여 설치하려는 시도가 시작될 때 사용자에게 알리도록 "WUOperationStatus-[NodeName]" 속성이 포함된 이벤트가 NodeAgentService에 게시됩니다. 이 내용은 다음 이미지에 나와 있습니다.

     [![NodeAgentService를 사용하는 Windows 업데이트 작업 상태의 콘솔 창을 보여주는 스크린샷](media/service-fabric-patch-orchestration-application/wuoperationstatusc.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusc.png#lightbox)

### <a name="diagnostics-logs"></a>진단 로그

Service Fabric 런타임 로그의 일부로 패치 오케스트레이션 애플리케이션 로그가 수집됩니다.

원하는 진단 도구 또는 파이프라인을 사용하여 로그를 캡처할 수 있습니다. POA는 다음과 같은 고정된 공급자 ID를 사용하여 [이벤트 원본](/dotnet/api/system.diagnostics.tracing.eventsource)을 통해 이벤트를 기록합니다.

- e39b723c-590c-4090-abb0-11e3e6616346
- fc0028ff-bfdc-499f-80dc-ed922c52c5e9
- 24afa313-0d3b-4c7c-b485-1047fd964b60
- 05dc046c-60e9-4ef7-965e-91660adffa68

### <a name="health-reports"></a>상태 보고서

POA는 다음과 같은 시나리오에서 노드 에이전트 서비스 또는 코디네이터 서비스에 대한 상태 보고서를 게시합니다.

* 노드 에이전트 NTService 중단

   노드에서 노드 에이전트 NTService가 중단되면 노드 에이전트 서비스에 대한 경고 수준 상태 보고서가 생성됩니다.

* Repair Manager 서비스가 사용하도록 설정되지 않음

   Repair Manager 서비스가 클러스터에 없는 경우 코디네이터 서비스에 대해 경고 수준 상태 보고서가 생성됩니다.

## <a name="frequently-asked-questions"></a>질문과 대답

**Q: POA가 실행 중일 때 클러스터가 오류 상태로 표시되는 이유는 무엇인가요?**

A: 설치 프로세스가 진행되는 동안 POA는 노드를 사용하지 않도록 설정하거나 다시 시작하며, 이로 인해 클러스터가 일시적으로 비정상 상태가 될 수 있습니다.

애플리케이션의 정책에 따라 패치 작업 중에 하나의 노드가 중단되거나 *또는* 전체 업그레이드 도메인이 동시에 중단될 수 있습니다.

Windows 업데이트 설치가 완료되면 재시작 후 노드가 다시 사용하도록 설정됩니다.

다음 예제에서는 두 노드가 중단되고 MaxPercentageUnhealthyNodes 정책이 위반되었으므로 클러스터가 일시적으로 오류 상태가 되었습니다. 이 오류는 패치 작업을 시작할 수 있을 때까지 일시적으로 지속됩니다.

![비정상 클러스터의 이미지](media/service-fabric-patch-orchestration-application/MaxPercentage_causing_unhealthy_cluster.png)

문제가 계속되는 경우 문제 해결 섹션을 참조하세요.

**Q: POA가 경고 상태이면 어떻게 해야 하나요?**

A: 애플리케이션에 대해 게시된 상태 보고서에 근본 원인이 나와 있는지 확인합니다. 일반적으로 경고에는 문제에 대한 세부 정보가 포함되어 있습니다. 일시적인 문제인 경우 애플리케이션이 자동으로 복구됩니다.

**Q: 클러스터가 비정상 상태이나 긴급한 운영 체제 업데이트를 수행해야 하는 경우 어떻게 해야 하나요?**

A: 클러스터 상태가 비정상이면 POA는 업데이트를 설치하지 않습니다. 클러스터를 정상 상태로 되돌려서 POA 워크플로의 차단을 해제해야 합니다.

**Q: 내 클러스터에 대해 TaskApprovalPolicy를 "NodeWise" 또는 "UpgradeDomainWise"로 설정해야 하나요?**

A: "UpgradeDomainWise" 설정은 업데이트 도메인에 속하는 모든 노드를 병렬로 패치하여 전체적인 클러스터 복구 속도를 높입니다. 이 과정에서 전체 업데이트 도메인에 속하는 노드는 사용할 수 없습니다([*사용 안 함* 상태](/dotnet/api/system.fabric.query.nodestatus#System_Fabric_Query_NodeStatus_Disabled)).

반대로 "NodeWise" 설정은 한 번에 하나의 노드만 패치하며, 전체적인 클러스터 패치가 더 오래 걸릴 수 있습니다. 하지만 패치 프로세스 중에 사용할 수 없게 되는(*사용 안 함* 상태) 노드가 많아야 하나입니다.

클러스터가 패치 주기 동안 N-1(N은 클러스터에 있는 모든 업데이트 도메인의 수)개의 업데이트 도메인에서 실행되는 것을 허용할 수 있는 경우 정책을 'UpgradeDomainWise'로 설정할 수 있습니다. 그렇지 않으면 "NodeWise"로 설정합니다.

**Q: 노드를 패치하는 데 시간이 얼마나 걸리나요?**

A: 노드를 패치하는 데 몇 분(예: [Windows Defender 정의 업데이트](https://www.microsoft.com/en-us/wdsi/definitions))에서 몇 시간(예: [Windows 누적 업데이트](https://www.catalog.update.microsoft.com/Search.aspx?q=windows%20server%20cumulative%20update))이 걸릴 수 있습니다. 노드를 패치하는 데 필요한 시간은 대부분 다음 조건에 따라 달라집니다. 
 - 업데이트 크기
 - 패치 창에 적용해야 하는 업데이트 수
 - 업데이트를 설치하고, (필요한 경우) 노드를 다시 부팅하고, 다시 부팅 후 설치 단계를 완료하는 데 걸리는 시간
 - VM 또는 머신의 성능 및 네트워크 상태

**Q: 전체 클러스터를 패치하는 데 시간이 얼마나 걸리나요?**

A: 전체 클러스터를 패치하는 데 필요한 시간은 다음 조건에 따라 달라집니다.

- 노드를 패치하는 데 필요한 시간

- 코디네이터 서비스 정책 기본 정책 "NodeWise"는 한 번에 하나의 노드만 패치하며, "UpgradeDomainWise"를 사용하는 것보다 속도가 느립니다. 

   예를 들어 노드 하나를 패치하는 데 최대 1시간이 걸린다고 가정할 때 각각 4개의 노드가 포함된 5개의 업데이트 도메인이 있는 20노드(동일한 형식의 노드) 클러스터를 패치하는 데 필요한 시간은 다음과 같습니다.
    - "NodeWise": 최대 20시간
    - "UpgradeDomainWise": 최대 5시간

- 클러스터 부하. 각 패치 작업에서는 고객 워크로드를 클러스터에서 사용 가능한 다른 노드로 재배치해야 합니다. 패치가 적용되는 노드는 이 시간 동안 [*사용하지 않도록 설정 중* 상태](/dotnet/api/system.fabric.query.nodestatus#System_Fabric_Query_NodeStatus_Disabling)입니다. 클러스터가 거의 최대 부하를 실행하는 경우 프로세스를 사용하지 않도록 설정하는 데 시간이 오래 걸립니다. 따라서 이러한 스트레스 조건에서는 전체 패치 프로세스가 느려질 수 있습니다.

- 패치 중에 클러스터 상태 오류. [클러스터 상태](./service-fabric-health-introduction.md)가 [저하](/dotnet/api/system.fabric.health.healthstate#System_Fabric_Health_HealthState_Error)되면 패치 프로세스가 중단됩니다. 이 문제는 전체 클러스터를 패치하는 데 필요한 전체 시간에 추가됩니다.

**Q: REST API를 통해 가져온 Windows 업데이트 결과에 표시된 일부 업데이트가 머신의 Windows 업데이트 기록에 표시되지 않는 이유는 무엇인가요?**

A: 일부 제품 업데이트는 자체 업데이트 또는 패치 기록에만 표시됩니다. 예를 들어 Windows Defender 업데이트는 Windows Server 2016의 Windows 업데이트 기록에 표시될 수도 있고 표시되지 않을 수도 있습니다.

**Q: POA를 사용하여 개발자 클러스터(1노드 클러스터)를 패치할 수 있나요?**

A: 아니요, POA는 1노드 클러스터를 패치하는 데 사용할 수 없습니다. 이러한 제한을 설계한 이유는 [Service Fabric 시스템 서비스](./service-fabric-technical-overview.md#system-services) 또는 기타 고객 앱에서 가동 중지 시간이 발생하기 때문입니다. 따라서 복구 작업 패치는 절대 Repair Manager의 승인을 받지 못합니다.

**Q: Linux에서 클러스터 노드를 패치하려면 어떻게 해야 하나요?**

A: Linux의 오케스트레이션 업데이트에 대해 알아보려면 [Azure 가상 머신 확장 집합 자동 OS 이미지 업그레이드](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md)를 참조하세요.

**Q: 업데이트 주기가 오래 걸리는 이유는 무엇인가요?**

A: 결과 JSON을 쿼리하고 모든 노드의 업데이트 주기를 입력한 다음, OperationStartTime 및 OperationTime(OperationCompletionTime)을 사용하여 모든 노드에 업데이트를 설치하는 데 걸린 시간을 확인할 수 있습니다. 

업데이트가 수행되지 않는 시간이 긴 경우 클러스터가 오류 상태일 가능성이 있으며, 이 경우 Repair Manager가 POA 복구 작업을 승인할 수 없습니다. 특정 노드에서 업데이트 설치가 오래 걸리면 해당 노드가 한동안 업데이트되지 않은 것일 수 있습니다. 많은 업데이트가 설치 보류 중이어서 시간이 오래 걸릴 수 있습니다. 

노드가 *사용하지 않도록 설정 중* 상태로 멈춰 있어 노드 패치가 차단될 수도 있습니다. 이 문제는 일반적으로 노드를 사용하지 않도록 설정하면 쿼럼 또는 데이터 손실이 발생할 수 있기 때문에 발생합니다.

**Q: POA가 노드를 패치할 때 노드를 사용하지 않도록 설정해야 하는 이유는 무엇인가요?**

A: POA는 *다시 시작* 의도를 사용하여 노드를 사용하지 않도록 설정하고, 그 결과로 노드에서 실행 중인 모든 Service Fabric 서비스가 중지되거나 다시 할당됩니다. POA는 애플리케이션에서 새 DLL과 이전 DLL을 혼합하여 사용하지 않도록 하기 위해 이 작업을 수행하므로, 노드를 사용하지 않도록 설정한 후 패치하는 것이 좋습니다.

**Q: POA를 사용하여 업데이트할 수 있는 최대 노드 수는 몇 개인가요?**

A: POA는 Service Fabric Repair Manager를 사용하여 업데이트할 노드의 복구 작업을 만듭니다. 그러나 250개를 초과하는 복구 작업은 동시에 존재할 수 없습니다. 현재 POA는 각 노드에 대한 복구 작업을 동시에 생성하므로, POA는 클러스터의 노드를 250개까지 업데이트할 수 있습니다. 

## <a name="disclaimers"></a>고지 사항

- POA는 사용자를 대신하여 Windows 업데이트에 대한 최종 사용자 사용권 계약에 동의합니다. 필요에 따라 애플리케이션 구성에서 설정을 해제할 수 있습니다.

- POA는 사용량 및 성능을 추적하기 위해 원격 분석 데이터를 수집합니다. 애플리케이션의 원격 분석은 Service Fabric 런타임의 원격 분석 설정을 따릅니다(기본적으로 설정됨).

## <a name="troubleshooting"></a>문제 해결

이 섹션에서는 노드 패치와 관련된 문제의 솔루션을 제공합니다.

### <a name="a-node-is-not-coming-back-to-up-state"></a>노드가 활성 상태로 전환되지 않음

* 노드가 다음과 같은 이유로 *사용하지 않도록 설정 중* 상태에 멈춰 있을 수 있습니다.

  - 안전 검사가 보류 중입니다. 이 문제를 해결하려면 정상 상태에서 충분히 노드를 사용할 수 있는지 확인합니다.

* 노드가 다음과 같은 이유로 *사용 안 함* 상태에 멈춰 있을 수 있습니다.

  - 수동으로 비활성화되었습니다.
  - 진행 중인 Azure 인프라 작업 때문에 비활성화되었습니다.
  - 노드를 패치하기 위해 POA가 노드를 일시적으로 비활성화했습니다.

* 노드는 다음과 같은 이유로 중단 상태에 멈춰 있을 수 있습니다.

  - 다운된 상태에서 수동으로 배치되었습니다.
  - 노드가 다시 시작하는 중입니다(POA가 재시작을 트리거한 것일 수 있음).
  - VM 또는 머신에 오류가 있거나 네트워크 연결 문제가 있습니다.

### <a name="updates-were-skipped-on-some-nodes"></a>일부 노드에서 업데이트를 건너뛰었습니다.

POA가 다시 예약 정책에 따라 Windows 업데이트를 설치하려고 합니다. 이 서비스는 노드를 복구하고 애플리케이션 정책에 따라 업데이트를 건너뛰려고 합니다.

이러한 경우에 노드 에이전트 서비스에 대한 경고 수준 상태 보고서가 생성됩니다. Windows 업데이트 결과에도 가능한 실패 원인이 포함됩니다.

### <a name="the-health-of-the-cluster-goes-to-error-while-the-update-is-being-installed"></a>업데이트가 설치되는 동안 클러스터의 상태가 오류로 전환됨

잘못된 Windows 업데이트는 특정 노드 또는 업데이트 도메인에 있는 애플리케이션 또는 클러스터의 상태를 중단시킬 수 있습니다. POA는 클러스터가 다시 정상 상태가 될 때까지 후속 Windows 업데이트 작업을 중단합니다.

관리자가 개입하여 Windows 업데이트로 인해 애플리케이션 또는 클러스터가 비정상 상태가 된 이유를 확인해야 합니다.

## <a name="poa-release-notes"></a>POA 릴리스 정보

>[!NOTE]
> POA 버전 1.4.0 이상에서는 GitHub의 [패치 오케스트레이션 애플리케이션 릴리스 페이지](https://github.com/microsoft/Service-Fabric-POA/releases/)에서 릴리스 정보 및 릴리스를 찾을 수 있습니다.

### <a name="version-110"></a>버전 1.1.0
- 공개 릴리스

### <a name="version-111"></a>버전 1.1.1
- NodeAgentNTService의 설치를 방지하는 NodeAgentService의 SetupEntryPoint에서 버그 수정

### <a name="version-120"></a>버전 1.2.0

- 시스템 다시 시작 워크플로 관련 버그 수정
- 복구 중 상태 확인이 예상 대로 작동하지 않는 문제로 인해 RM 작업 생성의 버그 수정
- Windows 서비스 POANodeSvc 시작 모드를 자동에서 지연 자동으로 변경

### <a name="version-121"></a>버전 1.2.1

- 클러스터 축소 워크플로의 버그 수정입니다. 존재하지 않는 노드에 속하는 POA 복구 태스크에 대해 가비지 수집 논리가 도입되었습니다.

### <a name="version-122"></a>버전 1.2.2

- 기타 버그가 수정되었습니다.
- 이제 이진 파일이 서명됩니다.
- 애플리케이션에 대한 sfpkg 링크가 추가되었습니다.

### <a name="version-130"></a>버전 1.3.0

- 이제 InstallWindowsOSOnlyUpdates를 False로 설정하면 사용 가능한 모든 업데이트가 설치됩니다.
- 자동 업데이트를 사용하지 않도록 설정하는 논리가 변경되었습니다. 이로 인해 Server 2016 이상에서 자동 업데이트가 사용하지 않도록 설정되지 않는 버그가 수정됩니다.
- 고급 사용 사례에서 POA의 두 마이크로서비스에 대한 배치 제약 조건이 매개 변수화되었습니다.

### <a name="version-131"></a>버전 1.3.1
- 자동 업데이트 비활성화의 오류로 인해 Windows Server 2012 R2 이하에서 POA 1.3.0이 작동하지 않는 회귀 수정 
- InstallWindowsOSOnlyUpdates 구성이 항상 True로 선택되는 버그 수정
- InstallWindowsOSOnlyUpdates의 기본값을 False로 변경

### <a name="version-132"></a>버전 1.3.2
- 현재 노드 이름의 하위 집합에 속하는 이름을 가진 노드가 있는 경우 노드의 패치 수명 주기에 영향을 주는 문제 수정. 이러한 노드의 경우 패치가 누락되거나 다시 부팅이 보류 중일 수 있습니다.
