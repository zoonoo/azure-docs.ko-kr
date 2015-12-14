<properties
	pageTitle="리소스 관리자 템플릿을 사용하는 Ubuntu의 DataStax | Microsoft Azure"
	description="Azure PowerShell 또는 Azure CLI 및 리소스 관리자 템플릿을 사용하여 Ubuntu VM에 새 DataStax 클러스터를 쉽게 배포할 수 있는 방법을 알아봅니다."
	services="virtual-machines"
	documentationCenter=""
	authors="scoriani"
	manager="timlt"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="multiple"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/29/2015"
	ms.author="scoriani"/>

# 리소스 관리자 템플릿을 사용하는 Ubuntu의 DataStax

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]클래식 배포 모델.


DataStax는 항상 작동하고 민첩하며 원하는 규모로 예측 가능하게 확장할 수 있는 상업적으로 지원되며 엔터프라이즈 지원이 되는 NoSQL 분산 데이터베이스 기술인 Apache Cassandra를 기반으로 하는 솔루션을 개발 및 제공하는 업계 선도 업체입니다. DataStax는 Enterprise(DSE)와 Community(DSC) 유형을 모두 제공합니다. 메모리 내 컴퓨팅, 엔터프라이즈 수준의 보안, 빠르고 강력한 통합 분석 및 엔터프라이즈 검색 등의 기능도 제공합니다.

Azure 마켓플레이스에서 이미 제공되는 것 외에도, 이제 [Azure PowerShell](../powershell-install-configure.md) 또는 [Azure CLI](../xplat-cli-install.md)를 통해 배포된 리소스 관리자 템플릿을 사용하여 Ubuntu VM에서 새 Datastax 클러스터를 쉽게 배포할 수도 있습니다.

이 템플릿을 기반으로 새로 배포되는 클러스터에는 다음 다이어그램에 설명된 토폴로지가 구축됩니다. 물론 제공된 템플릿을 사용자 지정하여 다른 토폴리지를 쉽게 실현할 수도 있습니다.

![cluster-architecture](media/virtual-machines-datastax-template/cluster-architecture.png)

매개 변수를 사용하여 새 Apache Cassandra 카산드라에서 배포할 노드 수를 정의할 수 있습니다. DataStax Operation Center 서비스 인스턴스도 동일한 가상 네트워크 내의 독립 실행형 VM에 배포되므로 클러스터 및 모든 개별 노드의 상태를 모니터링하고, 노드를 추가/제거하고, 해당 클러스터와 관련된 모든 관리 작업을 수행할 수 있습니다.

배포가 완료되면 구성된 DNS 주소를 사용하여 Datastax Operations Center VM 인스턴스에 액세스할 수 있습니다. OpsCenter VM에서는 SSH 포트 22와 HTTPS용 포트 8443을 사용합니다. 운영 센터의 DNS 주소는 `{dnsName}.{region}.cloudapp.azure.com` 형식의 매개 변수로 입력된 *dnsName* 및 *지역*을 포함합니다. "West US" 지역에서 "datastax"로 설정된 *dnsName* 매개 변수를 사용하여 배포를 만든 경우 `https://datastax.westus.cloudapp.azure.com:8443`에서 해당 배포를 위한 DataStax Operations Center VM에 액세스할 수 있습니다.

> [AZURE.NOTE]배포에 사용되는 인증서는 브라우저 경고를 생성하는 자체 서명된 인증서입니다. [DataStax](http://www.datastax.com/) 웹 사이트의 프로세스에 따라 인증서를 사용자 고유의 SSL 인증서로 대체할 수 있습니다.

Azure 리소스 관리자 및 이 배포에 사용할 템플릿과 관련된 자세한 내용을 살펴보기 전에 Azure PowerShell 또는 Azure CLI가 올바르게 구성되어 있는지 확인해야 합니다.

[AZURE.INCLUDE [arm-getting-setup-powershell](../../includes/arm-getting-setup-powershell.md)]

[AZURE.INCLUDE [xplat-getting-set-up-arm](../../includes/xplat-getting-set-up-arm.md)]

## 리소스 관리자 템플릿을 사용하여 Datastax 기반 Cassandra 클러스터 만들기

다음 단계에 따라 GitHub 템플릿 리포지토리의 리소스 관리자 템플릿을 사용하여 DataStax를 기반으로 Apache Cassandra 클러스터를 만들 수 있습니다. 각 단계에는 Azure PowerShell 및 Azure CLI에 대한 지침이 모두 포함되어 있습니다.

### 1-a단계: Azure PowerShell을 사용하여 템플릿 파일 다운로드

JSON 템플릿과 기타 관련 파일을 저장할 로컬 폴더를 만듭니다(예: C:\\Azure\\Templates\\DataStax).

로컬 폴더의 폴더 이름을 대신 입력하여 다음 명령을 실행합니다.

	$folderName="C:\Azure\Templates\DataStax"
	$webclient = New-Object System.Net.WebClient
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/azuredeploy.json"
	$filePath = $folderName + "\azuredeploy.json"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/azuredeploy-parameters.json"
	$filePath = $folderName + "\azuredeploy-parameters.json"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/dsenode.sh"
	$filePath = $folderName + "\dsenode.sh"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/ephemeral-nodes-resources.json"
	$filePath = $folderName + "\ephemeral-nodes-resources.json"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/metadata.json"
	$filePath = $folderName + "\metadata.json"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/opscenter-install-resources.json"
	$filePath = $folderName + "\opscenter-install-resources.json"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/opscenter-resources.json"
	$filePath = $folderName + "\opscenter-resources.json"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/opscenter.sh"
	$filePath = $folderName + "\opscenter.sh"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/shared-resources.json"
	$filePath = $folderName + "shared-resources.json"
	$webclient.DownloadFile($url,$filePath)

### 1-b단계: Azure CLI를 사용하여 템플릿 파일 다운로드

다음과 같이 원하는 Git 클라이언트를 사용하여 전체 템플릿 리포지토리를 복제합니다.

	git clone https://github.com/Azure/azure-quickstart-templates C:\Azure\Templates

복제가 완료되면 C:\\Azure\\Templates 디렉터리에서 **datastax-on-ubuntu** 폴더를 찾습니다.

### 2단계: (선택 사항) 템플릿 매개 변수 파악

DataStax를 기반으로 Apache Cassandra 클러스터와 같은 중요한 솔루션을 배포하는 경우 여러 필수 설정을 처리할 구성 매개 변수 집합을 지정해야 합니다. 템플릿 정의에서 이러한 매개 변수를 선언하면 외부 파일을 통해 또는 명령줄에서 배포하는 동안 값을 지정할 수 있습니다.

azuredeploy.json 파일 위쪽의 "parameters" 섹션에서 DataStax 클러스터를 구성하기 위해 템플릿에 필요한 매개 변수 집합을 찾을 수 있습니다. 이 템플릿의 azuredeploy.json 파일에 있는 “parameters” 섹션에 대한 예제는 다음과 같습니다.

	"parameters": {
		"region": {
			"type": "string",
			"defaultValue": "West US",
			"metadata": {
				"Description": "Location where resources will be provisioned"
			}
		},
		"storageAccountPrefix": {
			"type": "string",
			"defaultValue": "uniqueStorageAccountName",
			"metadata": {
				"Description": "Unique namespace for the storage account where the virtual machine's disks will be placed"
			}
		},
		"dnsName": {
			"type": "string",
			"metadata" : {
				"Description": "DNS subname for the operations center public IP"
			}
		},
		"virtualNetworkName": {
			"type": "string",
			"defaultValue": "myvnet",
			"metadata": {
				"Description": "Name of the virtual network provisioned for the cluster"
			}
		},
		"adminUsername": {
			"type": "string",
			"metadata": {
				"Description": "Administrator user name used when provisioning virtual machines"
			}
		},
		"adminPassword": {
			"type": "securestring",
			"metadata": {
				"Description": "Administrator password used when provisioning virtual machines"
			}
		},
		"opsCenterAdminPassword": {
			"type": "securestring",
			"metadata": {
				"Description": "Sets the operations center admin user password"
			}
		},
		"clusterVmSize": {
			"type": "string",
			"defaultValue": "Standard_D3",
			"allowedValues": [
				"Standard_D1",
				"Standard_D2",
				"Standard_D3",
				"Standard_D4",
				"Standard_D11",
				"Standard_D12",
				"Standard_D13",
				"Standard_D14"
			],
			"metadata": {
				"Description": "The size of the virtual machines used when provisioning cluster nodes"
			}
		},
		"clusterNodeCount": {
			"type": "int",
			"metadata": {
				"Description": "The number of nodes provisioned in the cluster"
			}
		},
		"clusterName": {
			"type": "string",
			"metadata": {
				"Description": "The name of the cluster provisioned"
			}
		}
	}

각 매개 변수에는 데이터 형식, 허용되는 값 등의 세부 정보가 있습니다. 따라서 Azure PowerShell 또는 Azure CLI와 같은 대화형 모드에서 템플릿을 실행하는 동안 전달되는 매개 변수의 유효성을 검사할 수 있으며, 필수 매개 변수 및 해당 설명 목록을 구문 분석하여 동적으로 작성할 수 있는 자체 검색 UI도 사용할 수 있습니다.

### 3a 단계: Azure PowerShell을 통해 탬플릿을 사용하여 DataStax 클러스터 배포

모든 매개 변수에 대한 런타임 값이 포함된 JSON 파일을 만들어 배포용 매개 변수 파일을 준비합니다. 이 파일이 배포 명령에 단일 엔터티로 전달됩니다. 매개 변수 파일을 포함하지 않으면 Azure PowerShell는 템플릿에 지정된 기본값을 사용하며 나머지 값을 입력하라는 메시지를 표시합니다.

다음은 azuredeploy-parameters.json 파일에서 볼 수 있는 매개 변수 집합의 예입니다.

	{
		"storageAccountPrefix": {
			"value": "scorianisa"
		},
		"dnsName": {
			"value": "scorianids"
		},
		"virtualNetworkName": {
			"value": "datastax"
		},
		"adminUsername": {
			"value": "scoriani"
		},
		"adminPassword": {
			"value": ""
		},
		"region": {
			"value": "West US"
		},
		"opsCenterAdminPassword": {
			"value": ""
		},
		"clusterVmSize": {
			"value": "Standard_D3"
		},
		"clusterNodeCount": {
			"value": 3
		},
		"clusterName": {
			"value": "cl1"
		}
	}

Azure 배포 이름, 리소스 그룹 이름, Azure 위치 및 저장된 JSON 배포 파일의 폴더를 입력합니다. 그리고 다음 명령을 실행합니다.

	$deployName="<deployment name>"
	$RGName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$folderName="<folder name, such as C:\Azure\Templates\DataStax>"
	$templateFile= $folderName + "\azuredeploy.json"
	$templateParameterFile= $folderName + "\azuredeploy-parameters.json"

	New-AzureResourceGroup –Name $RGName –Location $locName

	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateParameterFile $templateParameterFile -TemplateFile $templateFile

**New-AzureResourceGroupDeployment** 명령을 실행하면 JSON 매개 변수 파일에서 매개 변수 값이 추출되고 그에 따라 템플릿 실행이 시작됩니다. 여러 매개 변수 파일을 정의하여 다양한 환경(예: 테스트, 프로덕션 등)에서 사용하면 템플릿 재사용이 향상되고 복잡한 다중 환경 솔루션이 간소화됩니다.

저장소 계정 매개 변수로 제공하는 이름은 고유해야 하며 Azure 저장소 계정의 모든 요구 사항(소문자와 숫자만 사용 가능함)을 충족해야 하므로 배포할 때 새 Azure 저장소 계정을 만들어야 합니다.

배포하는 동안과 배포한 후 발생한 모든 오류를 포함하여 프로비전 중에 실행된 모든 요청을 확인할 수 있습니다.

이렇게 하려면 [Azure 포털](https://portal.azure.com)로 이동하여 다음을 수행합니다.

- 왼쪽 탐색 모음에서 **찾아보기**를 클릭한 다음 아래로 스크롤하여 **리소스 그룹**을 클릭합니다.  
- 방금 만든 리소스 그룹을 클릭하면 “리소스 그룹" 블레이드가 나타납니다.  
- "리소스 그룹" 블레이드의 **모니터링** 파트에서 "이벤트" 막대 그래프를 클릭하면 배포에 대한 이벤트를 확인할 수 있습니다.
- 개별 이벤트를 클릭하면 템플릿을 대신해 수행된 각 개별 작업에 대한 세부 정보를 볼 수 있습니다.

테스트 후 이 리소스 그룹과 모든 해당 리소스(저장소 계정, 가상 컴퓨터 및 가상 네트워크)를 제거해야 하는 경우 다음 단일 명령을 사용합니다.

	Remove-AzureResourceGroup –Name "<resource group name>" -Force

### 3b 단계: Azure CLI를 통해 탬플릿을 사용하여 DataStax 클러스터 배포

Azure CLI를 통해 DataStax 클러스터를 배포하려면 먼저 이름과 위치를 지정하여 리소스 그룹을 만듭니다.

	azure group create dsc "West US"

이 리소스 그룹 이름, JSON 템플릿 파일의 위치, 그리고 매개 변수 파일의 위치(위의Azure PowerShell 섹션에서 자세한 내용 확인)를 다음 명령으로 전달합니다.

	azure group deployment create dsc -f .\azuredeploy.json -e .\azuredeploy-parameters.json

다음 명령을 사용하여 개별 리소스 배포의 상태를 확인할 수 있습니다.

	azure group deployment list dsc

## DataStax 템플릿 구조 및 파일 구성 살펴보기

강력하고 재사용 가능한 리소스 관리자 템플릿을 디자인하려면 DataStax와 같은 복잡한 솔루션을 배포하는 동안 필요한 상호 관련된 일련의 복잡한 작업을 구성해야 합니다. 관련된 확장을 통해 스크립트를 실행하고 리소스 관리자 템플릿 연결 및 리소스 루핑을 활용하면 거의 모든 복잡한 템플릿 기반 배포에서 재사용할 수 있는 모듈식 방법을 구현할 수 있습니다.

이 섹션에서는 DataStax 클러스터용 azuredeploy.json 파일의 구조를 단계별로 설명합니다.

### "parameters" 섹션

azuredeploy.json의 "parameters" 섹션은 이 템플릿에 데이터를 입력하는 데 사용되는 매개 변수를 지정합니다. 앞서 언급한 azuredeploy-parameters.json 파일은 템플릿을 실행하는 동안 azuredeploy.json의 "parameters" 섹션으로 값을 전달하는 데 사용됩니다.

### "variables" 섹션

"variables" 섹션은 이 템플릿 전체에서 사용할 수 있는 변수를 지정합니다. 이 섹션에는 실행 시 상수 또는 계산된 값으로 설정되는 여러 필드(JSON 데이터 형식 또는 조각)가 포함됩니다. 다음은 이 DataStax 템플릿의 "variables" 섹션입니다.

	"variables": {
	"templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/datastax-on-ubuntu/",
	"sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
	"clusterNodesTemplateUrl": "[concat(variables('templateBaseUrl'), 'ephemeral-nodes-resources.json')]",
	"opsCenterTemplateUrl": "[concat(variables('templateBaseUrl'), 'opscenter-resources.json')]",
	"opsCenterInstallTemplateUrl": "[concat(variables('templateBaseUrl'), 'opscenter-install-resources.json')]",
	"opsCenterVmSize": "Standard_A1",
	"networkSettings": {
		"virtualNetworkName": "[parameters('virtualNetworkName')]",
		"addressPrefix": "10.0.0.0/16",
		"subnet": {
			"dse": {
				"name": "dse",
				"prefix": "10.0.0.0/24",
				"vnet": "[parameters('virtualNetworkName')]"
			}
		},
		"statics": {
			"clusterRange": {
				"base": "10.0.0.",
				"start": 5
			},
			"opsCenter": "10.0.0.240"
		}
	},
	"osSettings": {
		"imageReference": {
			"publisher": "Canonical",
			"offer": "UbuntuServer",
			"sku": "14.04.2-LTS",
			"version": "latest"
		},
		"scripts": [
			"[concat(variables('templateBaseUrl'), 'dsenode.sh')]",
			"[concat(variables('templateBaseUrl'), 'opscenter.sh')]",
			"https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/shared_scripts/ubuntu/vm-disk-utils-0.1.sh"
		]
	},
	"sharedStorageAccountName": "[concat(parameters('storageAccountPrefix'),'cmn')]",
	"nodeList": "[concat(variables('networkSettings').statics.clusterRange.base, variables('networkSettings').statics.clusterRange.start, '-', parameters('clusterNodeCount'))]"
	},

이 예제를 자세히 살펴보면 두 가지 방법을 볼 수 있습니다. 이 첫 번째 조각에서 **osSettings** 변수는 4개의 키 값 쌍이 있는 중첩된 JSON 요소입니다.

	"osSettings": {
	      "imageReference": {
	        "publisher": "Canonical",
	        "offer": "UbuntuServer",
	        "sku": "14.04.2-LTS",
	        "version": "latest"
	      },

	 
이 두 번째 코드 조각에서 **scripts** 변수는 템플릿 언어 함수(concat) 및 다른 변수 값과 문자열 상수를 사용하여 런타임에서 각각의 요소가 계산되는 JSON 배열입니다.

	      "scripts": [
	        "[concat(variables('templateBaseUrl'), 'dsenode.sh')]",
	        "[concat(variables('templateBaseUrl'), 'opscenter.sh')]",
	        "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/shared_scripts/ubuntu/vm-disk-utils-0.1.sh"
	      ]

### "resources" 섹션

"resources" 섹션은 대부분의 작업이 발생하는 곳입니다. 이 섹션 내부를 신중하게 찾으면, 두 가지 다른 경우를 즉시 식별할 수 있습니다. 첫 번째는 기본 일정 내에서 상주하는 배포를 호출을 의미하는 형식 `Microsoft.Resources/deployments`의 정의된 요소입니다. 아래 조각에서 볼 수 있듯이, **templateLink** 요소(및 관련 버전 속성)를 통해서는 호출할 연결된 템플릿 파일을 지정할 수 있습니다. 이 파일은 매개 변수 집합을 입력으로 전달합니다.

	{
	      "name": "shared",
	      "type": "Microsoft.Resources/deployments",
	      "apiVersion": "2015-01-01",
	      "properties": {
	        "mode": "Incremental",
	        "templateLink": {
	          "uri": "[variables('sharedTemplateUrl')]",
	          "contentVersion": "1.0.0.0"
	        },
	        "parameters": {
	          "region": {
	            "value": "[parameters('region')]"
	          },
	          "networkSettings": {
	            "value": "[variables('networkSettings')]"
	          },
	          "storageAccountName": {
	            "value": "[variables('sharedStorageAccountName')]"
	          }
	        }
	      }
	    },

이 첫 번째 예제에서는 이 시나리오의 azuredeploy.json이 일종의 오케스트레이션 메커니즘으로 구성되어 여러 다른 템플릿 파일을 호출합니다. 각 파일은 필요한 배포 작업의 일부를 담당합니다.

특히 다음과 같은 연결된 템플릿이 이 배포에 사용됩니다.

-	**shared-resource.json**: 배포에서 공유할 모든 리소스의 정의를 포함합니다. 예제는 VM의 OS 디스크 및 가상 네트워크를 저장하는 데 사용되는 저장소 계정입니다.
-	**opscenter-resources.json**: OpsCenter VM 및 네트워크 인터페이스, 공용 IP 주소를 비롯한 모든 관련 리소스를 배포합니다.
-	**opscenter-install-resources.json**: 해당 VM 내에 OpsCenter 서비스를 설치하는 데 필요한 특정 bash 스크립트 파일(opscenter.sh)을 호출할 OpsCenter VM 확장(Linux용 사용자 지정 스크립트)을 배포합니다.
-	**ephemeral-nodes-resources.json**: 모든 클러스터 노드 VM 및 연결된 리소스(네트워크 카드, 개인 IP 등)를 배포합니다. 또한 이 템플릿은 각 노드에 Apache Cassandra 비트를 물리적으로 설치하기 위해 VM 확장(Linux용 사용자 지정 스크립트)을 배포하고 bash 스크립트(dsenode.sh)를 호출합니다.

이 마지막 템플릿은 템플릿 사용 방법은 템플릿 개발 관점에서 가장 흥미로운 것 중 하나이므로 좀 더 자세히 살펴보겠습니다. 한 가지 중요한 개념은 단일 템플릿 파일에서 단일 리소스 유형의 여러 복사본을 배포하고 각 인스턴스에 대해 필요한 설정의 고유 값을 설정하는 방법입니다. 이 개념은 **리소스 루핑**이라고 합니다.

기본 azuredeploy.json 파일 내에서 ephemeral-nodes-resources.json을 호출하면 *nodeCount*라는 매개 변수가 매개 변수 목록의 일부로 제공됩니다. 자식 템플릿 내에서 *nodeCount*(클러스터에 배포할 노드 수)는 아래 조각에 강조 표시된 것처럼 여러 복사본으로 배포해야 하는 각 리소스의 **copy** 요소 내에 사용됩니다. 배포된 리소스의 여러 인스턴스 간에 고유한 값을 지정하는 데 필요한 모든 설정의 경우 **copyindex()** 함수를 사용하여 해당 특정 리소스 루프 만들기에서 현재 인덱스를 나타내는 숫자 값을 가져올 수 있습니다. 다음 조각에서는 DataStax 클러스터 노드에 대해 작성되는 여러 VM에 이 개념이 적용됨을 확인할 수 있습니다.

			   {
			      "apiVersion": "2015-05-01-preview",
			      "type": "Microsoft.Compute/virtualMachines",
			      "name": "[concat(parameters('namespace'), 'vm', copyindex())]",
			      "location": "[parameters('region')]",
			      "copy": {
			        "name": "[concat(parameters('namespace'), 'vmLoop')]",
			        "count": "[parameters('nodeCount')]"
			      },
			      "dependsOn": [
			        "[concat('Microsoft.Network/networkInterfaces/', parameters('namespace'), 'nic', copyindex())]",
			        "[concat('Microsoft.Compute/availabilitySets/', parameters('namespace'), 'set')]",
			        "[concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]"
			      ],
			      "properties": {
			        "availabilitySet": {
			          "id": "[resourceId('Microsoft.Compute/availabilitySets', concat(parameters('namespace'), 'set'))]"
			        },
			        "hardwareProfile": {
			          "vmSize": "[parameters('vmSize')]"
			        },
			        "osProfile": {
			          "computername": "[concat(parameters('namespace'), 'vm', copyIndex())]",
			          "adminUsername": "[parameters('adminUsername')]",
			          "adminPassword": "[parameters('adminPassword')]"
			        },
			        "storageProfile": {
			          "imageReference": "[parameters('osSettings').imageReference]",
			          "osDisk": {
			            "name": "osdisk",
			            "vhd": {
			              "uri": "[concat('http://',variables('storageAccountName'),'.blob.core.windows.net/vhds/', variables('vmName'), copyindex(), '-osdisk.vhd')]"
			            },
			            "caching": "ReadWrite",
			            "createOption": "FromImage"
			          },
			          "dataDisks": [
			            {
			              "name": "datadisk1",
			              "diskSizeGB": 1023,
			              "lun": 0,
			              "vhd": {
			                "Uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/','vhds/', variables('vmName'), copyindex(), 'DataDisk1.vhd')]"
			              },
			              "caching": "None",
			              "createOption": "Empty"
			            }
			          ]
			        },
			        "networkProfile": {
			          "networkInterfaces": [
			            {
			              "id": "[resourceId('Microsoft.Network/networkInterfaces',concat(parameters('namespace'), 'nic', copyindex()))]"
			            }
			          ]
			        }
			      }
			    },

리소스 만들기의 또 다른 중요한 개념은 **dependsOn** JSON 배열에서 볼 수 있는 것처럼 리소스 간의 종속성 및 우선 순위를 지정하는 기능입니다. 이 특정 템플릿의 각 노드에는 Apache Cassandra 인스턴스의 백업 및 스냅숏을 호스트하는 데 사용될 수 있는 연결된 1TB 디스크("dataDisks" 참조)가 있습니다.

연결된 디스크는 dsenode.sh 스크립트 파일의 실행을 통해 트리거되는 노드 준비 작업의 일부로 포맷됩니다. 해당 스크립트의 첫째 행은 다른 스크립트를 호출합니다.

	bash vm-disk-utils-0.1.sh

vm-disk-utils-0.1.sh 파일은 azure-quickstart-tempates GitHub 리포지토리 내 **shared\_scripts\\ubuntu** 폴더의 일부이며, 디스크 탑재, 포맷 및 스트라이프에 매우 유용한 함수를 포함하고 있습니다. 이러한 함수는 리포지토리의 모든 템플릿에서 사용할 수 있습니다.

또 다른 흥미로운 조각은 CustomScriptForLinux VM 확장과 관련된 조각입니다. 이러한 확장은 별도의 리소스 유형으로 설치되며 각 클러스터 노드(및 OpsCenter 인스턴스)에 대한 종속성이 적용됩니다. 가상 컴퓨터에 대해 설명하는 동일한 리소스 루핑 메커니즘을 활용합니다.

	{
	"type": "Microsoft.Compute/virtualMachines/extensions",
	"name": "[concat(parameters('namespace'), 'vm', copyindex(), '/installdsenode')]",
	"apiVersion": "2015-05-01-preview",
	"location": "[parameters('region')]",
	"copy": {
		"name": "[concat(parameters('namespace'), 'vmLoop')]",
		"count": "[parameters('nodeCount')]"
	},
	"dependsOn": [
		"[concat('Microsoft.Compute/virtualMachines/', parameters('namespace'), 'vm', copyindex())]",
		"[concat('Microsoft.Network/networkInterfaces/', parameters('namespace'), 'nic', copyindex())]"
	],
	"properties": {
		"publisher": "Microsoft.OSTCExtensions",
		"type": "CustomScriptForLinux",
		"typeHandlerVersion": "1.2",
		"settings": {
			"fileUris": "[parameters('osSettings').scripts]",
			"commandToExecute": "bash dsenode.sh"
		}
	}
	}

이 배포에 포함된 다른 파일을 알면 Azure 리소스 관리자 템플릿을 활용하여 모든 기술을 기반으로 하는 다중 노드 솔루션에 대한 복잡한 배포 전략을 구성 및 오케스트레이션하는 데 필요한 모든 세부 정보 및 모범 사례를 이해할 수 있습니다. 필수 사항은 아니지만 다음 다이어그램에 강조 표시된 대로 템플릿 파일을 구성하는 것이 좋습니다.

![datastax-template-structure](media/virtual-machines-datastax-template/datastax-template-structure.png)

기본적으로 이 방법은 다음 사항을 제안합니다.

-	템플릿 연결을 활용하여 하위 템플릿 실행을 호출하는 모든 특정 배포 작업을 위해 핵심 템플릿 파일을 중앙 오케스트레이션 지점으로 정의합니다.
-	다른 모든 특정 배포 작업(저장소 계정, 가상 네트워크 구성 등)에서 공유되는 모든 리소스를 배포할 특정 템플릿 파일을 만듭니다. 이는 주로 공용 인프라 면에서 요구 사항이 유사한 배포 간에 재사용될 수 있습니다.
-	지정된 리소스에 특정한 요구 사항을 충족하는 선택적 리소스 템플릿을 포함합니다.
-	리소스 그룹의 동일한 구성원(클러스터의 노드 등)에 대해 고유한 속성의 여러 인스턴스를 배포하기 위해 리소스 루핑을 활용하는 특정 템플릿을 만듭니다.
-	모든 배포 후 작업(제품 설치, 구성 등)의 경우 스크립트 배포 확장을 활용하고 각 기술에 특정한 스크립트를 만듭니다.

자세한 내용은 [Azure 리소스 관리자 템플릿 언어](../resource-group-authoring-templates.md)를 참조하세요.

<!---HONumber=AcomDC_1203_2015-->