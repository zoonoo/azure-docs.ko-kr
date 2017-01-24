---
title: "Azure 진단을 사용하여 로그 수집 | Microsoft Docs"
description: "이 문서는 Azure에서 실행 중인 서비스 패브릭에서 로그를 수집하도록 Azure 진단을 설정하는 방법을 설명합니다."
services: service-fabric
documentationcenter: .net
author: ms-toddabel
manager: timlt
editor: 
ms.assetid: 9f7e1fa5-6543-4efd-b53f-39510f18df56
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/28/2016
ms.author: toddabel
translationtype: Human Translation
ms.sourcegitcommit: a957a70be915459baa8c687c92e251c6011b6172
ms.openlocfilehash: bc8eaf68b89bdefe203fc7ceea7b5241ac3e9dfa


---
# <a name="collect-logs-by-using-azure-diagnostics"></a>Azure 진단을 사용하여 로그 수집
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-how-to-setup-wad.md)
> * [Linux](service-fabric-diagnostics-how-to-setup-lad.md)
> 
> 

Azure 서비스 패브릭 클러스터를 실행할 때 모든 노드의 로그를 중앙 위치에 수집하는 것이 좋습니다. 중앙 위치에 로그를 두면 클러스터나 해당 클러스터에서 실행 중인 응용 프로그램 및 서비스의 문제를 분석하고 해결하는 데 도움이 됩니다.

로그를 업로드 및 수집하는 방법 중 하나는 로그를 Azure 저장소에 업로드하는 Azure 진단 확장을 사용하는 것입니다. 로그는 저장소에 그렇게 직접적으로 도움이 되지는 않습니다. 하지만 외부 프로세스를 사용하여 저장소의 이벤트를 읽고 [Log Analytics](../log-analytics/log-analytics-service-fabric.md), [Elastic Search](service-fabric-diagnostic-how-to-use-elasticsearch.md), 기타 로그 구문 분석 솔루션 등의 제품에 배치할 수 있습니다.

## <a name="prerequisites"></a>필수 조건
다음 도구는 이 문서의 일부 작업을 수행하는 데 사용됩니다.

* [Azure 진단](../cloud-services/cloud-services-dotnet-diagnostics.md)(Azure Cloud Services와 관련이 있지만 여러 좋은 정보와 예 제공)
* [Azure 리소스 관리자](../azure-resource-manager/resource-group-overview.md)
* [Azure PowerShell](/powershell/azureps-cmdlets-docs)
* [Azure Resource Manager 클라이언트](https://github.com/projectkudu/ARMClient)
* [Azure Resource Manager 템플릿](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="log-sources-that-you-might-want-to-collect"></a>수집하려는 로그 원본
* **Service Fabric 로그:** 플랫폼에서 표준 ETW(Windows용 이벤트 추적)와 EventSource 채널로 내보냅니다. 로그는 여러 유형 중 하나일 수 있습니다.
  * 작업 이벤트: Service Fabric 플랫폼이 수행하는 작업에 대한 로그입니다. 응용 프로그램 및 서비스 만들기, 노드 상태 변경, 업그레이드 정보 등을 예로 들 수 있습니다.
  * [Reliable Actors 프로그래밍 모델 이벤트](service-fabric-reliable-actors-diagnostics.md)
  * [Reliable Services 프로그래밍 모델 이벤트](service-fabric-reliable-services-diagnostics.md)
* **응용 프로그램 이벤트:** 서비스 코드에서 발생하며 Visual Studio에서 제공하는 EventSource 도우미 클래스를 사용하여 작성된 이벤트입니다. 응용 프로그램에서 로그를 작성하는 방법에 대한 자세한 내용은 [로컬 컴퓨터 개발 설정에서의 모니터링 및 진단 서비스](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)를 참조하세요.

## <a name="deploy-the-diagnostics-extension"></a>진단 확장 배포
로그를 수집하는 첫 단계는 서비스 패브릭 클러스터의 각 VM에 진단 확장을 배포하는 것입니다. 진단 확장은 각 VM에서 로그를 수집하여 사용자가 지정하는 저장소 계정에 업로드합니다. 단계는 Azure Portal 또는 Azure Resource Manager 사용 여부에 따라 약간 달라집니다. 또한 배포가 클러스터 생성의 일부인지 아니면 이미 있는 클러스터에 대한 것인지에 따라서도 단계가 달라집니다. 각 시나리오에 대한 단계를 살펴보겠습니다.

### <a name="deploy-the-diagnostics-extension-as-part-of-cluster-creation-through-the-portal"></a>포털을 통해 클러스터 만들기의 일환으로 진단 확장 배포
클러스터 만들기의 일부로 클러스터의 VM에 진단 확장을 배포하려면 다음 이미지에 표시된 진단 설정 패널을 사용합니다. Reliable Actors 또는 Reliable Services 이벤트 컬렉션을 사용하려면 진단이 기본 설정인 **켜기**로 설정되어 있는지 확인합니다. 클러스터를 만든 후에는 포털을 사용하여 이러한 설정을 변경할 수 없습니다.

![클러스터를 만들기 위해 포털에서 Azure 진단 설정](./media/service-fabric-diagnostics-how-to-setup-wad/portal-cluster-creation-diagnostics-setting.png)

Azure 지원 팀이 사용자의 지원 요청을 처리하려면 지원 로그가 *필요*합니다. 이러한 로그는 실시간으로 수집되어 리소스 그룹에 만들어진 저장소 계정 중 하나에 저장됩니다. 진단 설정은 응용 프로그램 수준 이벤트를 구성합니다. 이러한 이벤트로는 Azure Storage에 저장될 [Reliable Actors](service-fabric-reliable-actors-diagnostics.md) 이벤트, [Reliable Services](service-fabric-reliable-services-diagnostics.md) 이벤트 및 일부 시스템 수준 Service Fabric 이벤트가 포함됩니다.

[Elastic Search](service-fabric-diagnostic-how-to-use-elasticsearch.md) 같은 제품 또는 사용자 고유의 프로세스는 저장소 계정에서 이벤트를 가져올 수 있습니다. 현재 테이블로 전송되는 이벤트를 필터링하거나 영구 제거할 방법은 없습니다. 테이블에서 이벤트를 제거하는 프로세스를 구현하지 않으면 테이블이 계속 커집니다.

포털을 사용하여 클러스터를 만드는 경우 클러스터를 만들기 위해 **확인*을 클릭하기 전에* 템플릿을 다운로드하는 것이 좋습니다. 자세한 내용은 [Azure Resource Manager 템플릿을 사용하여 Service Fabric 클러스터 설정](service-fabric-cluster-creation-via-arm.md)을 참조하세요. 일부는 포털을 사용하여 변경할 수 없으므로 나중에 변경하려면 템플릿이 필요합니다.

다음 단계에 따라 포털에서 템플릿을 내보낼 수 있습니다. 그러나 이러한 템플릿은 필요한 정보를 누락된 null 값이 포함될 수 있으므로 사용하기 더 어려울 수 있습니다.

1. 리소스 그룹을 엽니다.
2. **설정**을 선택하여 설정 패널을 표시합니다.
3. **배포**를 선택하여 배포 기록 패널을 표시합니다.
4. 배포 세부 정보를 표시할 배포를 선택합니다.
5. **템플릿 내보내기**를 선택하여 템플릿은 패널을 표시합니다.
6. **파일에 저장**을 선택하여 템플릿, 매개 변수 및 PowerShell 파일을 포함하는 .zip 파일을 내보냅니다.

파일을 내보낸 후 수정해야 합니다. parameters.json 파일을 편집하고 **adminPassword** 요소를 제거합니다. 배포 스크립트가 실행될 때 암호를 묻는 메시지가 표시됩니다. 배포 스크립트를 실행할 때 null 매개 변수 값을 수정해야 할 수도 있습니다.

다운로드한 템플릿을 사용하여 구성을 업데이트하려면:

1. 로컬 컴퓨터의 폴더에 압축을 풉니다.
2. 새 구성을 반영하도록 콘텐츠를 수정합니다.
3. PowerShell을 시작하고 압축을 푼 폴더로 변경합니다.
4. **deploy.ps1** 을 실행하고 구독 ID, 리소스 그룹 이름(같은 이름을 사용하여 구성 업데이트) 및 고유한 배포 이름을 입력합니다.

### <a name="deploy-the-diagnostics-extension-as-part-of-cluster-creation-by-using-azure-resource-manager"></a>Azure 리소스 관리자를 사용하여 클러스터 만들기의 일환으로 진단 확장 배포
리소스 관리자를 사용하여 클러스터를 만들려면 클러스터를 만들기 전에 진단 구성 JSON을 전체 클러스터 Resource Manager 템플릿에 추가해야 합니다. 리소스 관리자 템플릿 샘플의 일부로 진단 구성이 추가된 샘플 5VM 클러스터 리소스 관리자 템플릿이 제공됩니다. Azure 샘플 갤러리의 [진단 Resource Manager 템플릿 샘플이 포함된 5노드 클러스터](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype-wad)에서 샘플을 볼 수 있습니다.

Resource Manager 템플릿에서 진단 설정을 표시하려면 azuredeploy.json 파일을 열고 **IaaSDiagnostics**를 검색합니다. 이 템플릿을 사용하여 클러스터를 만들려면 이전 링크에 제공된 **Azure에 배포** 버튼을 선택합니다.

또는 리소스 관리자 샘플을 다운로드하여 변경한 후 Azure PowerShell 창에서 `New-AzureRmResourceGroupDeployment` 명령을 사용하여 수정된 템플릿으로 클러스터를 만들 수 있습니다. 명령에 전달할 매개 변수는 다음 코드를 참조하세요. PowerShell을 사용하여 리소스 그룹을 배포하는 방법에 대한 자세한 내용은 [Azure Resource Manager 템플릿을 사용하여 리소스 그룹 배포](../resource-group-template-deploy.md) 문서를 참조하세요.

```powershell

New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -Name $deploymentName -TemplateFile $pathToARMConfigJsonFile -TemplateParameterFile $pathToParameterFile –Verbose
```

### <a name="deploy-the-diagnostics-extension-to-an-existing-cluster"></a>기존 클러스터에 진단 확장 배포
진단이 배포되지 않은 기존 클러스터가 있거나 기존 구성을 수정하려는 경우 기존 클러스터를 추가하거나 업데이트할 수 있습니다. 기존 클러스터를 만드는 데 사용되는 Resource Manager 템플릿을 수정하거나 앞의 설명대로 포털에서 템플릿을 다운로드합니다. 다음 작업을 수행하여 template.json 파일을 수정합니다.

리소스 섹션에 추가하여 새 저장소 리소스를 템플릿에 추가합니다.

```json
{
  "apiVersion": "2015-05-01-preview",
  "type": "Microsoft.Storage/storageAccounts",
  "name": "[parameters('applicationDiagnosticsStorageAccountName')]",
  "location": "[parameters('computeLocation')]",
  "properties": {
    "accountType": "[parameters('applicationDiagnosticsStorageAccountType')]"
  },
  "tags": {
    "resourceType": "Service Fabric",
    "clusterName": "[parameters('clusterName')]"
  }
},
```

 저장소 계정 정의 바로 뒤 `supportLogStorageAccountName`과 `vmNodeType0Name` 사이의 매개 변수 섹션에 추가합니다. 자리 표시자 텍스트 *storage account name goes here*를 저장소 계정의 이름으로 바꿉니다.

```json
    "applicationDiagnosticsStorageAccountType": {
      "type": "string",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS"
      ],
      "defaultValue": "Standard_LRS",
      "metadata": {
        "description": "Replication option for the application diagnostics storage account"
      }
    },
    "applicationDiagnosticsStorageAccountName": {
      "type": "string",
      "defaultValue": "storage account name goes here",
      "metadata": {
        "description": "Name for the storage account that contains application diagnostics data from the cluster"
      }
    },
```
그런 다음 확장 배열 내에 다음 코드를 추가하여 template.json의 `VirtualMachineProfile` 섹션을 업데이트합니다. 삽입되는 위치에 따라 시작 부분 또는 끝 부분에 쉼표를 추가해야 합니다.

```json
{
    "name": "[concat(parameters('vmNodeType0Name'),'_Microsoft.Insights.VMDiagnosticsSettings')]",
    "properties": {
        "type": "IaaSDiagnostics",
        "autoUpgradeMinorVersion": true,
        "protectedSettings": {
        "storageAccountName": "[parameters('applicationDiagnosticsStorageAccountName')]",
        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
        "storageAccountEndPoint": "https://core.windows.net/"
        },
        "publisher": "Microsoft.Azure.Diagnostics",
        "settings": {
        "WadCfg": {
            "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": "50000",
            "EtwProviders": {
                "EtwEventSourceProviderConfiguration": [
                {
                    "provider": "Microsoft-ServiceFabric-Actors",
                    "scheduledTransferKeywordFilter": "1",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableActorEventTable"
                    }
                },
                {
                    "provider": "Microsoft-ServiceFabric-Services",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableServiceEventTable"
                    }
                }
                ],
                "EtwManifestProviderConfiguration": [
                {
                    "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                    "scheduledTransferLogLevelFilter": "Information",
                    "scheduledTransferKeywordFilter": "4611686018427387904",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricSystemEventTable"
                    }
                }
                ]
            }
            }
        },
        "StorageAccount": "[parameters('applicationDiagnosticsStorageAccountName')]"
        },
        "typeHandlerVersion": "1.5"
    }
}
```

template.json 파일을 설명대로 수정한 후에는 Resource Manager 템플릿을 다시 게시합니다. 템플릿을 내보낸 후 deploy.ps1 파일을 실행하면 템플릿이 다시 게시됩니다. 배포 후에는 **ProvisioningState**가 **성공**했는지 확인합니다.

## <a name="update-diagnostics-to-collect-and-upload-logs-from-new-eventsource-channels"></a>새 EventSource 채널에서 로그를 수집 및 업로드하도록 진단 업데이트
배포하려는 새 응용 프로그램을 나타내는 새 EventSource 채널에서 로그를 수집하도록 진단을 업데이트하려면 기존 클러스터에 대한 진단 설정을 설명하는 [이전 섹션](#deploywadarm)과 동일한 단계를 수행해야 합니다.

새 EventSource 채널의 항목을 추가하도록 template.json 파일의 `EtwEventSourceProviderConfiguration` 섹션을 업데이트한 후 `New-AzureRmResourceGroupDeployment` PowerShell 명령을 사용하여 구성 업데이트를 적용합니다. 이벤트 원본의 이름은 ServiceEventSource.cs 파일을 생성한 Visual Studio에서 코드의 일부로 정의됩니다.

예를 들어 이벤트 원본의 이름을 Eventsource라고 지정한 경우 My-Eventsource의 이벤트를 MyDestinationTableName이라는 테이블에 배치하는 다음 코드를 추가합니다.

```json
        {
            "provider": "My-Eventsource",
            "scheduledTransferPeriod": "PT5M",
            "DefaultEvents": {
            "eventDestination": "MyDestinationTableName"
            }
        }
```

성능 카운터 또는 이벤트 로그를 수집하려면 [Azure Resource Manager 템플릿을 사용하여 모니터링 및 진단이 포함된 Windows 가상 컴퓨터 만들기](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)에 제공된 예제를 사용하여 Resource Manager 템플릿을 수정합니다. 그런 다음 Resource Manager 템플릿을 다시 게시합니다.

## <a name="next-steps"></a>다음 단계
문제를 해결하는 동안 조사해야 하는 이벤트에 대한 자세한 내용을 확인하려면 [Reliable Actors](service-fabric-reliable-actors-diagnostics.md) 및 [Reliable Services](service-fabric-reliable-services-diagnostics.md)가 내보낸 진단 이벤트를 확인합니다.

## <a name="related-articles"></a>관련 문서
* [진단 확장을 사용하여 성능 카운터 또는 로그를 수집하는 방법 알아보기](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Log Analytics의 Service Fabric 솔루션](../log-analytics/log-analytics-service-fabric.md)




<!--HONumber=Dec16_HO1-->


