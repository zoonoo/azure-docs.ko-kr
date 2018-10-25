---
title: Azure Virtual Machine Scale Sets를 사용하여 자동으로 OS 업그레이드 | Microsoft Docs
description: 확장 집합의 VM 인스턴스에서 OS를 자동으로 업그레이드하는 방법을 알아봅니다
services: virtual-machine-scale-sets
documentationcenter: ''
author: rajsqr
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2018
ms.author: rajraj
ms.openlocfilehash: cf25d08fc9a0e1ae458d350be93af31447928ecb
ms.sourcegitcommit: 7b0778a1488e8fd70ee57e55bde783a69521c912
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/10/2018
ms.locfileid: "49069457"
---
# <a name="azure-virtual-machine-scale-set-automatic-os-image-upgrades"></a>Azure Virtual Machine Scale Sets 자동 OS 업그레이드

자동 OS 이미지 업그레이드는 모든 VM을 최신 OS 이미지로 자동 업그레이드하는 Azure Virtual Machine Scale Sets의 기능입니다.

자동 OS 업그레이드의 특징은 다음과 같습니다.

- 구성이 완료되면 이미지 게시자가 게시한 최신 OS 이미지가 사용자 개입 없이 확장 집합에 자동으로 적용됩니다.
- 판매자가 새 플랫폼 이미지를 게시할 때마다 인스턴스 배치를 롤링 방식으로 업그레이드합니다.
- 응용 프로그램 상태 프로브와 통합됩니다.
- 모든 VM 크기와 Windows 및 Linux 플랫폼 이미지에 모두 사용할 수 있습니다.
- 언제든지 자동 업그레이드를 옵트아웃할 수 있습니다(OS 업그레이드를 수동으로 시작 가능).
- VM의 OS 디스크는 최신 이미지 버전을 사용하여 만든 새 OS 디스크로 교체됩니다. 구성된 확장 및 사용자 지정 데이터 스크립트가 실행되고 지속형 데이터 디스크는 유지됩니다.
- Azure Disk Encryption(미리 보기)은 현재 지원되지 않습니다.  

## <a name="how-does-automatic-os-image-upgrade-work"></a>자동 OS 이미지 업그레이드의 작동 방식

업그레이드는 VM OS 디스크를 최신 이미지 버전을 사용하여 생성된 새 디스크로 교체하는 방식으로 작동합니다. 구성된 확장 및 사용자 지정 데이터 스크립트가 실행되고 지속형 데이터 디스크는 유지됩니다. 응용 프로그램 가동 중지 시간을 최소화하기 위해 업그레이드는 여러 컴퓨터가 포함된 배치 단위로 진행되며, 확장 집합에서 업그레이드되는 컴퓨터의 비율은 항상 20% 이하로 유지됩니다. Azure Load Balancer 응용 프로그램 상태 프로브를 통합하는 옵션도 제공됩니다. 응용 프로그램 하트비트를 통합하여 업그레이드 프로세스의 각 배치에서 업그레이드 성공의 유효성을 검사하는 것이 좋습니다. 실행 단계는 다음과 같습니다. 

1. 업그레이드 프로세스를 시작하기 전에 오케스트레이터가 비정상 상태인 인스턴스 비율이 20% 이하인지를 확인합니다. 
2. 업그레이드할 VM 인스턴스 배치를 확인합니다. 최대 배치 크기는 총 인스턴스 개수의 20%입니다.
3. 이 VM 인스턴스 배치의 OS 이미지를 업그레이드합니다.
4. 고객이 응용 프로그램 상태 프로브를 구성한 경우 업그레이드 작업은 프로브가 정상이 될 때까지 최대 5분 동안 기다렸다가 다음 배치 업그레이드를 진행합니다. 
5. 업그레이드할 인스턴스가 남아 있으면 1단계로 이동하여 다음 일괄 처리를 진행하고, 그렇지 않으면 업그레이드가 완료됩니다.

확장 집합 OS 업그레이드 오케스트레이터는 모든 배치를 업그레이드하기 전에 전체 VM 인스턴스 상태를 확인합니다. 일괄 처리를 업그레이드하는 동안 Azure 데이터 센터에서 VM의 가용성에 영향을 줄 수 있는 다른 동시 계획된 유지 관리 또는 계획되지 않은 유지 관리가 발생할 수 있습니다. 따라서 20%가 넘는 인스턴스가 일시적으로 중지될 수 있습니다. 이 경우 현재 일괄 처리가 끝나면 확장 집합 업그레이드가 중지됩니다.

## <a name="supported-os-images"></a>지원되는 OS 이미지
현재는 특정 OS 플랫폼 이미지만 지원됩니다. 지금은 사용자가 직접 만든 사용자 지정 이미지를 사용할 수 없습니다. 

현재 지원되는 SKU는 다음과 같습니다(향후 더 추가 예정).
    
| 게시자               | OS 제품      |  SKU               |
|-------------------------|---------------|--------------------|
| Canonical               | UbuntuServer  | 16.04-LTS          |
| Canonical               | UbuntuServer  | 18.04-LTS *        | 
| Rogue Wave(OpenLogic)  | CentOS        | 7.5 *              | 
| CoreOS                  | CoreOS        | Stable             | 
| Microsoft Corporation   | WindowsServer | 2012-R2-Datacenter | 
| Microsoft Corporation   | WindowsServer | 2016-Datacenter    | 
| Microsoft Corporation   | WindowsServer | 2016-Datacenter-Smalldisk |
| Microsoft Corporation   | WindowsServer | 2016-Datacenter-with-Containers |

* 이러한 이미지에 대한 지원은 현재 시작되는 중이며, 조만간 모든 Azure 지역에서 제공될 예정입니다. 

## <a name="requirements-for-configuring-automatic-os-image-upgrade"></a>자동 OS 이미지 업그레이드 구성을 위한 요구 사항

- 플랫폼 이미지의 *version* 속성을 *latest*로 설정해야 합니다.
- Service Fabric 이외의 확장 집합에 대해 응용 프로그램 상태 프로브를 사용해야 합니다.
- 확장 집합 모델이 참조하는 리소스가 사용 가능한 상태이며 최신 상태로 유지되는지 확인해야 합니다. 
  VM 확장 속성의 페이로드, 저장소 계정의 페이로드, 모델의 비밀에 대한 참조 등을 부트스트랩하기 위한 SAS URI를 예로 들 수 있습니다. 

## <a name="configure-automatic-os-image-upgrade"></a>자동 OS 이미지 업그레이드 구성
자동 OS 이미지 업그레이드를 구성하려면 확장 집합 모델 정의에서 *automaticOSUpgradePolicy.enableAutomaticOSUpgrade* 속성을 *true*로 설정해야 합니다. 

```
PUT or PATCH on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version=2018-10-01`
```

```json
{ 
  "properties": { 
    "upgradePolicy": { 
      "automaticOSUpgradePolicy": { 
        "enableAutomaticOSUpgrade":  true 
      } 
    } 
  } 
} 
```

다음 예제에서는 Azure CLI(2.0.47 이상)를 사용하여 *myResourceGroup* 리소스 그룹에 있는 *myVMSS* 확장 집합에 대해 자동 업그레이드를 구성합니다.

```azurecli
az vmss update --name myVMSS --resource-group myResourceGroup --set UpgradePolicy.AutomaticOSUpgradePolicy.EnableAutomaticOSUpgrade=true
```
Azure PowerShell을 통해 이 속성을 구성하는 작업도 곧 지원될 예정입니다.

## <a name="using-application-health-probes"></a>응용 프로그램 상태 프로브 사용 

OS가 업그레이드되는 동안 확장 집합의 VM 인스턴스는 한 번에 하나의 일괄 처리 단위로 업그레이드됩니다. 업그레이드된 VM 인스턴스에서 고객 응용 프로그램의 상태가 정상인 경우에만 업그레이드를 계속 진행해야 합니다. 응용 프로그램이 확장 집합 OS 업그레이드 엔진에 상태 신호를 제공하는 것이 좋습니다. 기본적으로 OS를 업그레이드하는 동안 플랫폼은 VM 전원 상태 및 확장 프로비전 상태를 고려하여 업그레이드 후 VM 인스턴스가 정상 상태인지 확인합니다. VM 인스턴스의 OS를 업그레이드하는 동안 VM 인스턴스의 OS 디스크는 최신 이미지 버전에 따라 새 디스크로 교체됩니다. OS 업그레이드가 완료되면 구성된 확장이 이러한 VM에서 실행됩니다. VM의 모든 확장이 성공적으로 프로비전된 경우에만 응용 프로그램의 상태가 정상인 것으로 간주됩니다. 

원한다면 플랫폼에 응용 프로그램의 현재 상태에 대한 정확한 정보를 제공하도록 확장 집합을 구성할 수 있습니다. 응용 프로그램 상태 프로브는 상태 신호로 사용되는 사용자 지정 부하 분산 장치 프로브입니다. 확장 집합 VM 인스턴스에서 실행되는 응용 프로그램은 외부 HTTP 또는 TCP 요청에 응답하여 상태를 알릴 수 있습니다. 사용자 지정 부하 분산 장치 프로브에 대한 자세한 내용은 [부하 분산 장치 프로브 이해](../load-balancer/load-balancer-custom-probe-overview.md)를 참조하세요. 자동 OS 업그레이드에 응용 프로그램 상태 프로브가 꼭 필요한 것은 아니지만 사용하는 것이 좋습니다.

여러 배치 그룹을 사용하도록 확장 집합을 구성한 경우 [표준 부하 분산 장치](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)를 사용하는 프로브를 사용해야 합니다.

### <a name="configuring-a-custom-load-balancer-probe-as-application-health-probe-on-a-scale-set"></a>확장 집합의 응용 프로그램 상태 프로브로 사용자 지정 부하 분산 장치 구성
모범 사례에 따라 확장 집합 상태에 대한 부하 분산 장치 프로브를 명시적으로 만듭니다. 기존 HTTP 검색 또는 TCP 프로브에 대한 동일한 엔드포인트를 사용할 수 있지만, 상태 프로브에 기존의 부하 분산 장치 프로브와 다른 동작이 필요할 수 있습니다. 예를 들어 기존의 부하 분산 장치 프로브는 인스턴스의 부하가 너무 높으면 비정상 상태를 반환하기도 하는데, 이 동작은 OS를 자동으로 업그레이드하는 동안 인스턴스 상태를 확인하는 데에는 적합하지 않습니다. 검색 속도가 2분을 넘지 않도록 프로브를 구성하세요.

부하 분산 장치 프로브는 다음과 같이 확장 집합의 *networkProfile*에서 참조할 수 있으며 내부 또는 공용 부하 분산 장치에 연결할 수 있습니다.

```json
"networkProfile": {
  "healthProbe" : {
    "id": "[concat(variables('lbId'), '/probes/', variables('sshProbeName'))]"
  },
  "networkInterfaceConfigurations":
  ...
```
> [!NOTE]
> Service Fabric과 함께 자동 OS 업그레이드를 사용할 때 새 OS 이미지는 Service Fabric에서 실행되는 서비스의 고가용성을 유지하기 위해 업데이트 도메인에 의해 업데이트 도메인에 롤아웃됩니다. Service Fabric 클러스터의 내구성 특성에 대한 자세한 내용은 [이 설명서](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster)를 참조하세요.

### <a name="keep-credentials-up-to-date"></a>자격 증명을 최신 상태로 유지
저장소 계정에 대해 SAS 토큰을 사용하는 VM 확장이 구성되어 있는 등 확장 집합이 자격 증명을 사용하여 외부 리소스에 액세스하는 경우에는 자격 증명이 최신 상태로 유지되는지 확인해야 합니다. 인증서 및 토큰을 포함한 자격 증명이 만료되는 경우 업그레이드에 실패하고 VM의 첫 번째 일괄 처리가 실패한 상태가 됩니다.

리소스 인증 오류 시 VM을 복구하고 자동 OS 업그레이드를 다시 설정하는 권장된 단계는 다음과 같습니다.

* 확장에 전달된 토큰(또는 다른 자격 증명)를 다시 생성합니다.
* 외부 엔터티에 연결하기 위해 VM 내에서 사용되는 자격 증명이 최신 상태인지 확인합니다.
* 새 토큰으로 확장 집합의 확장을 업데이트합니다.
* 업데이트된 확장 집합을 배포합니다. 그러면 실패한 인스턴스를 포함하여 모든 VM 인스턴스를 업데이트합니다. 

## <a name="get-the-history-of-automatic-os-image-upgrades"></a>자동 OS 이미지 업그레이드의 기록 가져오기 
Azure PowerShell, Azure CLI 2.0 또는 REST API를 사용하여 확장 집합에서 수행된 가장 최근의 OS 업그레이드 기록을 확인할 수 있습니다. 지난 2개월 동안 마지막으로 시도한 OS 업그레이드 5개의 기록을 가져올 수 있습니다.

### <a name="azure-powershell"></a>Azure PowerShell
다음 예제에서는 Azure PowerShell을 사용하여 *myResourceGroup* 리소스 그룹에 있는 *myVMSS* 확장 집합의 상태를 확인합니다.

```powershell
Get-AzureRmVmss -ResourceGroupName myResourceGroup -VMScaleSetName myVMSS -OSUpgradeHistory
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
다음 예제에서는 Azure CLI(2.0.47 이상)를 사용하여 *myResourceGroup* 리소스 그룹에 있는 *myVMSS* 확장 집합의 상태를 확인합니다.

```azurecli
az vmss get-os-upgrade-history --resource-group myResourceGroup --name myVMSS
```

### <a name="rest-api"></a>REST API
다음 예제에서는 REST API를 사용하여 *myResourceGroup*이라는 리소스 그룹에 있는 *myVMSS*라는 확장 집합의 상태를 확인합니다.

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/osUpgradeHistory?api-version=2018-10-01`
```
이 API의 설명서는 다음 페이지를 참조하세요. https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/getosupgradehistory

GET 호출은 다음 예제 출력과 비슷한 속성을 반환합니다.

```json
{
  "value": [
    {
      "properties": {
        "runningStatus": {
          "code": "RollingForward",
          "startTime": "2018-07-24T17:46:06.1248429+00:00",
          "completedTime": "2018-04-21T12:29:25.0511245+00:00"
        },
        "progress": {
          "successfulInstanceCount": 16,
          "failedInstanceCount": 0,
          "inProgressInstanceCount": 4,
          "pendingInstanceCount": 0
        },
        "startedBy": "Platform",
        "targetImageReference": {
          "publisher": "MicrosoftWindowsServer",
          "offer": "WindowsServer",
          "sku": "2016-Datacenter",
          "version": "2016.127.20180613"
        },
        "rollbackInfo": {
          "successfullyRolledbackInstanceCount": 0,
          "failedRolledbackInstanceCount": 0
        }
      },
      "type": "Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades",
      "location": "westeurope"
    }
  ]
} 
```

## <a name="how-to-get-the-latest-version-of-a-platform-os-image"></a>플랫폼 OS 이미지의 최신 버전을 가져오는 방법 

아래 예제를 사용하면 자동 OS 업그레이드가 지원되는 SKU의 이미지 버전을 가져올 수 있습니다. 

```
GET on `/subscriptions/subscription_id/providers/Microsoft.Compute/locations/{location}/publishers/{publisherName}/artifacttypes/vmimage/offers/{offer}/skus/{skus}/versions?api-version=2018-10-01`
```

```powershell
Get-AzureRmVmImage -Location "westus" -PublisherName "Canonical" -Offer "UbuntuServer" -Skus "16.04-LTS"
```

```azurecli
az vm image list --location "westus" --publisher "Canonical" --offer "UbuntuServer" --sku "16.04-LTS" --all
```

## <a name="deploy-with-a-template"></a>템플릿을 사용하여 배포

다음 템플릿을 사용하여 <a href='https://github.com/Azure/vm-scale-sets/blob/master/preview/upgrade/autoupdate.json'>자동 롤링 업그레이드 - Ubuntu 16.04-LTS</a> 자동 업그레이드를 사용하는 확장 집합을 배포할 수 있습니다.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fvm-scale-sets%2Fmaster%2Fpreview%2Fupgrade%2Fautoupdate.json" target="_blank">
    <img src="http://azuredeploy.net/deploybutton.png"/>
</a>

## <a name="next-steps"></a>다음 단계
확장 집합에 자동 OS 업그레이드를 사용하는 방법에 대한 더 많은 예제를 보려면 [미리 보기 기능에 대한 GitHub 리포지토리](https://github.com/Azure/vm-scale-sets/tree/master/preview/upgrade)를 참조하세요.
