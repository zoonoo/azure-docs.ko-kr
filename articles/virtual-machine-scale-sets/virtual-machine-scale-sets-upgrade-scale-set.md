---
title: "Azure 가상 머신 확장 집합 업그레이드 | Microsoft Docs"
description: "Azure 가상 머신 확장 집합 업그레이드"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: e229664e-ee4e-4f12-9d2e-a4f456989e5d
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: gunegatybo
ms.openlocfilehash: fbdc9d40173a40f35eee60cadfdd258293509d53
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/20/2017
---
# <a name="upgrade-a-virtual-machine-scale-set"></a>가상 머신 확장 집합 업그레이드
이 문서에서는 가동 중단 시간 없이 OS 업데이트를 Azure 가상 머신 크기 집합에 롤아웃하는 방법을 설명합니다. 이 컨텍스트에서 OS 업데이트는 OS의 버전 또는 SKU를 변경하거나 사용자 지정 이미지의 URI를 변경합니다. 가동 중지 시간 없이 업데이트한다는 것은 가상 머신을 모두 한꺼번에 업데이트하지 않고 한 번에 하나씩 또는 그룹으로(예" 한 번에 하나의 장애 도메인) 업데이트하는 것을 의미합니다. 이렇게 하면 업그레이드되지 않는 모든 가상 머신을 계속 실행할 수 있습니다.

모호성을 피하기 위해 수행하려는 네 가지 유형의 OS 업데이트 작업을 구분해 보겠습니다.

* 플랫폼 이미지의 버전 또는 SKU 변경. 예를 들어 Ubuntu 14.04.2-LTS 버전을 14.04.201506100에서 14.04.201507060으로 변경하거나 Ubuntu 15.10/최신 SKU를 16.04.0-LTS/최신으로 변경할 수 있습니다. 이 시나리오는 이 문서에서 설명합니다.
* 작성한 새 버전의 사용자 지정 이미지를 가리키는 URI 변경(**속성** > **virtualMachineProfile** > **storageProfile** > **osDisk** > **image** > **uri**). 이 시나리오는 이 문서에서 설명합니다.
* Azure Managed Disks를 사용하여 만든 확장 집합의 이미지 참조 변경.
* 가상 컴퓨터 내에서 OS 패치(이 예제에는 보안 패치 설치 및 Windows 업데이트 실행 포함). 이 시나리오는 지원되지만 이 문서에서는 다루지 않습니다.

[Azure 서비스 패브릭](https://azure.microsoft.com/services/service-fabric/) 클러스터의 일부로 배포되는 가상 머신 크기 집합은 여기에서 다루지 않습니다. Service Fabric 패치에 대한 자세한 내용은 [Service Fabric 클러스터에서 Windows OS 패치](https://docs.microsoft.com/azure/service-fabric/service-fabric-patch-orchestration-application)를 참조하세요.

플랫폼 이미지의 OS 버전/SKU나 사용자 지정 이미지의 URI를 변경하기 위한 기본 순서는 다음과 같습니다.

1. 가상 머신 확장 집합 모델을 가져옵니다.
2. 모델에서 버전, SKU, 이미지 참조 또는 URI 값을 변경합니다.
3. 모델을 업데이트합니다.
4. 크기 집합의 가상 머신에 대해 *manualUpgrade* 호출을 수행합니다. 이 단계는 크기 집합의 *upgradePolicy* 속성이 **수동** 으로 설정된 경우만 적합합니다. **자동**으로 설정되면 모든 가상 머신이 한꺼번에 업그레이드되므로 가동 중지 시간이 발생합니다.

이 정보를 염두에 두고 PowerShell에서 또는 REST API를 사용하여 확장 집합의 버전을 업데이트하는 방법을 알아보겠습니다. 이러한 예제는 플랫폼 이미지의 경우를 포함하지만, 이 문서에서는 이 프로세스를 사용자 지정 이미지에 맞게 조정하기 위한 충분한 정보를 제공합니다.

## <a name="powershell"></a>PowerShell
이 예제에서는 Windows 가상 머신 확장 집합을 새 버전인 4.0.20160229로 업데이트합니다. 모델을 업데이트한 후 한 번에 하나의 가상 머신 인스턴스를 업데이트합니다.

```powershell
$rgname = "myrg"
$vmssname = "myvmss"
$newversion = "4.0.20160229"
$instanceid = "1"

# get the VMSS model
$vmss = Get-AzureRmVmss -ResourceGroupName $rgname -VMScaleSetName $vmssname

# set the new version in the model data
$vmss.virtualMachineProfile.storageProfile.imageReference.version = $newversion

# update the virtual machine scale set model
Update-AzureRmVmss -ResourceGroupName $rgname -Name $vmssname -VirtualMachineScaleSet $vmss

# now start updating instances
Update-AzureRmVmssInstance -ResourceGroupName $rgname -VMScaleSetName $vmssname -InstanceId $instanceId
```

플랫폼 이미지 버전을 변경하는 대신 사용자 지정 이미지의 URI를 업데이트하는 경우 “새 버전 설정” 줄을 원본 이미지 URI을 업데이트하는 명령으로 바꿉니다. 예를 들어 Azure Managed Disks를 사용하지 않고 확장 집합을 만든 경우 업데이트는 다음과 같습니다.

```powershell
# set the new version in the model data
$vmss.virtualMachineProfile.storageProfile.osDisk.image.uri= $newURI
```

Azure Managed Disks를 사용하여 사용자 지정 이미지 기반 확장 집합을 만든 경우 이미지 참조가 업데이트됩니다. 예: 

```powershell
# set the new version in the model data
$vmss.virtualMachineProfile.storageProfile.imageReference.id = $newImageReference
```

## <a name="the-rest-api"></a>REST API
다음은 Azure REST API를 사용하여 OS 버전 업데이트를 롤아웃하는 Python의 몇 가지 예제입니다. 두 가지 모두 Azure REST API 래퍼 함수의 경량 [azurerm](https://pypi.python.org/pypi/azurerm) 라이브러리를 사용하여 크기 집합 모델에 대해 GET을 수행한 다음 업데이트된 모델에 대해 PUT을 수행합니다. 또한 업데이트 도메인으로 가상 머신을 식별하기 위해 가상 머신 인스턴스 보기도 확인합니다.

### <a name="vmssupgrade"></a>Vmssupgrade
 [Vmssupgrade](https://github.com/gbowerman/vmsstools)는 한 번에 한 업데이트 도메인씩, 실행 중인 가상 머신 확장 집합으로 OS 업그레이드를 롤아웃하는 데 사용되는 Python 스크립트입니다.

![가상 머신 또는 업데이트 도메인을 선택하기 위한 Vmssupgrade 스크립트](./media/virtual-machine-scale-sets-upgrade-scale-set/vmssupgrade-screenshot.png)

이 스크립트를 통해 특정 가상 머신을 선택하여 업데이트 도메인을 업데이트하거나 지정할 수 있습니다. 이 스크립트는 플랫폼 이미지 버전 변경 또는 사용자 지정 이미지의 URI 변경을 지원합니다.

### <a name="vmsseditor"></a>Vmsseditor
[Vmsseditor](https://github.com/gbowerman/vmssdashboard) 는 한 행이 하나의 업데이트 도메인을 나타내는 heatmap으로 가상 머신 상태를 표시하는 가상 머신 크기 집합에 대한 범용 편집기입니다. 무엇보다도 새 버전의 SKU 또는 사용자 지정 이미지 URI로 크기 집합에 대한 모델을 업데이트한 다음 업그레이드할 장애 도메인을 선택할 수 있습니다. 이렇게 하면 해당 업데이트 도메인에 있는 모든 가상 머신이 새 모델로 업그레이드됩니다. 또는 선택한 배치 크기에 따라 롤링 업그레이드를 수행할 수 있습니다.  

다음 스크린샷에서는 Ubuntu 14.04 2LTS 버전 14.04.201507060에 대한 크기 집합 모델을 보여 줍니다. 이 스크린샷이 작성된 이후에 추가 옵션이 이 도구에 추가되었습니다.

![Ubuntu 14.04-2LTS에 대한 확장 집합의 Vmsseditor 모델](./media/virtual-machine-scale-sets-upgrade-scale-set/vmssEditor1.png)

**업그레이드**를 클릭한 후 **세부 정보 보기**를 클릭하면 UD 0의 가상 머신이 업데이트되기 시작합니다.

![진행 중인 업데이트를 보여 주는 Vmsseditor](./media/virtual-machine-scale-sets-upgrade-scale-set/vmssEditor2.png)

