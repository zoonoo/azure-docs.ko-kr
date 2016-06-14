<properties
   pageTitle="Azure 진단을 사용하여 로그를 수집하는 방법 | Microsoft Azure"
   description="이 문서는 Azure에서 실행 중인 서비스 패브릭에서 로그를 수집하도록 Azure 진단을 설정하는 방법을 설명합니다."
   services="service-fabric"
   documentationCenter=".net"
   authors="ms-toddabel"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="05/20/2016"
   ms.author="toddabel"/>


# Azure 진단을 사용하여 로그를 수집하는 방법

Azure 서비스 패브릭 클러스터를 실행할 때 모든 노드의 로그를 중앙 위치에 수집하는 것이 좋습니다. 중앙 위치에 로그를 두면 클러스터나 해당 클러스터에서 실행 중인 응용 프로그램 및 서비스의 문제를 간편하게 분석하고 해결할 수 있습니다. 로그를 업로드 및 수집하는 방법 중 하나는 로그를 Azure 저장소에 업로드하는 Azure 진단 확장을 사용하는 것입니다. 로그는 실제로 저장소에서 직접 유용한 것은 아니지만 외부 프로세스를 사용하여 저장소에서 이벤트를 읽어 [Elastic Search](service-fabric-diagnostic-how-to-use-elasticsearch.md) 또는 다른 로그 구문 분석 솔루션과 같은 제품에 배치할 수 있습니다.

## 필수 조건
이러한 도구는 이 문서의 일부 작업을 수행하는 데 사용됩니다.

* [Azure 진단](../cloud-services/cloud-services-dotnet-diagnostics.md)(Azure 클라우드 서비스와 관련이 있지만 여러 좋은 정보와 예 제공)
* [Azure 리소스 관리자](../resource-group-overview.md)
* [Azure PowerShell](../powershell-install-configure.md)
* [Azure Resource Manager 클라이언트](https://github.com/projectkudu/ARMClient)
* [Azure 리소스 관리자를 사용하여 Windows 가상 컴퓨터와 모니터링 및 진단 기능 만들기](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md)


## 수집하려는 다양한 로그 원본
1. **서비스 패브릭 로그:** 플랫폼에서 표준 ETW와 EventSource 채널로 내보냅니다. 로그는 여러 유형 중 하나일 수 있습니다.
  - 작업 이벤트: 서비스 패브릭 플랫폼이 수행한 작업에 대한 로그입니다. 응용 프로그램 및 서비스 만들기, 노드 상태 변경, 업그레이드 정보 등을 예로 들 수 있습니다.
  - [행위자 프로그래밍 모델 이벤트](service-fabric-reliable-actors-diagnostics.md)
  - [Reliable Services 프로그래밍 모델 이벤트](service-fabric-reliable-services-diagnostics.md)
2. **응용 프로그램 이벤트:** 서비스 코드에서 발생하며 Visual Studio에서 제공하는 EventSource 도우미 클래스를 사용하여 작성된 이벤트입니다. 응용 프로그램에서 로그를 작성하는 방법에 대한 자세한 내용은 [로컬 컴퓨터 환경에서 서비스를 모니터링 및 진단하는 방법에 대한 이 문서](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)를 참조하세요.


## 진단 확장 배포
로그를 수집하는 첫 단계는 서비스 패브릭 클러스터의 각 VM에 진단 확장을 배포하는 것입니다. 진단 확장은 각 VM에서 로그를 수집하여 사용자가 지정한 저장소 계정에 업로드합니다. Azure 포털과 Azure 리소스 관리자 중 어떤 것을 사용하는지, 클러스터 만들기의 일환으로 배포하는 것인지 아니면 이미 있는 기존 클러스터에 배포하는 것인지에 따라 단계에 약간 차이가 있습니다. 각 시나리오에 대한 단계를 살펴보겠습니다.

### 포털을 통해 클러스터 만들기의 일환으로 진단 확장 배포
클러스터 만들기의 일환으로 VM에 진단 확장을 배포하려면 아래 이미지에 표시된 진단 설정 패널을 사용합니다. 행위자나 Reliable Service 이벤트 컬렉션을 사용하려면 진단이 기본 설정인 **켜기**로 설정되어 있는지 확인합니다. 클러스터를 만든 후에는 포털을 사용하여 이러한 설정을 변경할 수 없습니다.

![클러스터를 만들기 위해 포털에서 Azure 진단 설정](./media/service-fabric-diagnostics-how-to-setup-wad/portal-cluster-creation-diagnostics-setting.png)

지원 로그는 Azure 지원 팀에서 사용자가 만든 지원 요청을 처리하는 데 **필요**합니다. 이러한 로그는 실시간으로 수집되어 리소스 그룹에 만들어진 저장소 계정 중 하나에 저장됩니다. 진단 설정은 Azure 저장소에 저장되는 [행위자](service-fabric-reliable-actors-diagnostics.md) 이벤트, [Reliable Service](service-fabric-reliable-services-diagnostics.md) 이벤트 및 일부 시스템 수준 서비스 패브릭 이벤트를 포함하여 응용 프로그램 수준 이벤트를 구성합니다. [Elastic Search](service-fabric-diagnostic-how-to-use-elasticsearch.md)와 같은 제품 또는 사용자 고유의 프로세스는 저장소 계정에서 이벤트를 선택할 수 있습니다. 현재 테이블로 전송되는 이벤트를 필터링하거나 영구 제거할 방법은 없습니다. 테이블에서 이벤트를 제거하는 프로세스가 구현되지 않은 경우 테이블이 계속 증가합니다. 포털을 사용하여 클러스터를 만드는 경우에는 배포가 완료된 후 템플릿을 내보내는 것이 좋습니다. 다음과 같은 방법으로 포털에서 템플릿을 내보낼 수 있습니다.

1. 리소스 그룹을 엽니다.
2. 설정을 선택하여 설정 패널을 표시합니다.
3. 배포를 선택하여 배포 기록 패널을 표시합니다.
4. 배포 세부 정보를 표시할 배포를 선택합니다.
5. 템플릿 내보내기를 선택하여 템플릿은 패널을 표시합니다.
6. 파일에 저장을 선택하여 템플릿, 매개 변수 및 PowerShell 파일을 포함하는 .zip 파일을 내보냅니다.

파일을 내보낸 후 수정해야 합니다. **parameters.json** 파일을 편집하고 **adminPassword** 요소를 제거합니다. 배포 스크립트가 실행될 때 암호를 묻는 메시지가 표시됩니다. 다운로드한 템플릿을 사용하여 구성을 업데이트하려면

1. 로컬 컴퓨터의 폴더에 압축을 풉니다.
2. 새 구성을 반영하도록 콘텐츠를 수정합니다.
3. PowerShell을 시작하고 압축을 푼 폴더로 변경합니다.
4. **deploy.ps1**을 실행하고 subscriptionId, 리소스 그룹 이름(같은 이름을 사용하여 구성 업데이트), 고유한 배포 이름을 입력합니다.


### Azure Resource Manager를 사용하여 클러스터 만들기의 일환으로 진단 확장 배포
리소스 관리자를 사용하여 클러스터를 만들려면 클러스터를 만들기 전에 진단 구성 JSON을 전체 클러스터 리소스 관리자 템플릿에 추가해야 합니다. 리소스 관리자 템플릿 샘플의 일부로 진단 구성이 추가된 샘플 5VM 클러스터 리소스 관리자 템플릿이 제공됩니다. Azure 샘플 갤러리의 다음 위치에서 샘플을 볼 수 있습니다. [진단 리소스 관리자 템플릿 샘플이 포함된 5노드 클러스터](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype-wad) Resource Manager 템플릿에서 진단 설정을 표시하려면 **azuredeploy.json** 파일을 열고 **IaaSDiagnostics**를 검색합니다. 이 템플릿을 사용하여 클러스터를 만들려면 위의 링크에서 제공하는 **Azure에 배포** 버튼을 누르면 됩니다.

또는 리소스 관리자 샘플을 다운로드하여 변경한 후 Azure PowerShell 창에서 `New-AzureRmResourceGroupDeployment` 명령을 사용하여 수정된 템플릿으로 클러스터를 만들 수 있습니다. 명령에 전달해야 하는 매개 변수는 아래 정보를 참조하세요. PowerShell을 사용하여 리소스 그룹을 배포하는 방법에 대한 자세한 내용은 [Azure Resource Manager 템플릿 사용하여 리소스 그룹 배포](../resource-group-template-deploy.md) 문서를 참조하세요.

```powershell

New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -Name $deploymentName -TemplateFile $pathToARMConfigJsonFile -TemplateParameterFile $pathToParameterFile –Verbose
```

### 기존 클러스터에 진단 확장 배포
진단이 배포되지 않은 기존 클러스터가 있거나 기존 구성을 수정하려는 경우 다음 단계에 따라 기존 클러스터를 추가하거나 업데이트할 수 있습니다. 기존 클러스터를 만드는 데 사용되는 ARM 템플릿을 수정하거나 위에 설명된 대로 포털에서 템플릿을 다운로드합니다. 다음 작업을 수행하여 **template.json** 파일을 수정합니다.

리소스 섹션에 추가하여 새 저장소 리소스를 템플릿에 추가합니다.

##### 리소스 섹션 업데이트
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

 저장소 계정 정의 바로 뒤 "supportLogStorageAccountName"과 "vmNodeType0Name" 사이의 매개 변수 섹션에 추가합니다. 자리 표시자 텍스트 *storage account name goes here*를 원하는 저장소 계정의 이름으로 바꿉니다.

##### 매개 변수 섹션 업데이트
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
"extensions" 배열 내에 다음을 추가하여 **template.json**의 *VirtualMachineProfile* 섹션을 업데이트합니다. 삽입되는 위치에 따라 시작 부분 또는 끝 부분에 쉼표를 추가해야 합니다.

##### VirtualMachineProfile의 확장 배열에 추가
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

**template.json**을 설명된 대로 수정한 후 ARM 템플릿을 다시 게시합니다. 템플릿을 내보낸 경우 **deploy.ps1** 파일을 실행하면 템플릿이 다시 게시됩니다. 배포 후 *ProvisioningState*가 *Succeeded*인지 확인합니다.


## 새 EventSource 채널에서 로그를 수집 및 업로드하도록 진단 업데이트
배포하려는 새 응용 프로그램을 나타내는 새 EventSource 채널에서 로그를 수집하도록 진단을 업데이트하려면 기존 클러스터에 대한 진단 설정을 설명하는 [위 섹션](#deploywadarm)에서와 동일한 단계를 수행하면 됩니다. *New-AzureRmResourceGroupDeployment* PowerShell 명령을 사용하여 구성 업데이트를 적용하기 전에 **template.json**에서 *EtwEventSourceProviderConfiguration* 섹션을 업데이트하여 새 EventSources에 대한 항목을 추가해야 합니다.


## 다음 단계
문제를 해결하는 동안 조사해야 하는 이벤트에 대한 자세한 내용을 확인하려면 [Reliable Actors](service-fabric-reliable-actors-diagnostics.md) 및 [Reliable Services](service-fabric-reliable-services-diagnostics.md)가 내보낸 진단 이벤트를 확인합니다.


## 관련된 문서
* [진단 확장을 사용하여 성능 카운터 또는 로그를 수집하는 방법 알아보기](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md)

<!---HONumber=AcomDC_0601_2016-->