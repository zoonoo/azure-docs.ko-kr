<properties
	pageTitle="Redis 클러스터 리소스 관리자 템플릿"
	description="Azure PowerShell 또는 Azure CLI 및 리소스 관리자 템플릿을 사용하여 Ubuntu VM에 새 Redis 클러스터를 쉽게 배포할 수 있는 방법을 알아봅니다."
	services="virtual-machines"
	documentationCenter=""
	authors="timwieman"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="virtual-machines"
	ms.workload="multiple"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/04/2015"
	ms.author="twieman"/>

# 리소스 관리자 템플릿을 사용하는 Redis 클러스터

Redis는 오픈 소스 키-값 캐시 및 저장소이며, 키에는 문자열, 해시, 목록, 집합, 정렬된 집합 등의 데이터 구조가 포함될 수 있습니다.  Redis는 이러한 데이터 유형에 대한 소규모 작업 집합을 지원합니다. Redis 버전 3.0이 릴리스되면서 이제는 안정적인 최신 Redis 버전에서 Redis 클러스터를 사용할 수 있게 되었습니다. Redis 클러스터는 데이터가 여러 Redis 노드로 자동 분할되는 분산형 Redis 구현으로, 노드 하위 집합에서 오류가 발생해도 작업을 계속 진행할 수 있는 기능이 제공됩니다.

Microsoft Azure Redis Cache는 Microsoft에서 관리하는 전용 Redis 캐시 서비스지만 모든 Microsoft Azure 고객이 Azure Redis Cache를 사용해야 하는 것은 아닙니다. 즉, 자체 Azure 배포 환경 내의 서브넷에 Redis 캐시를 보관하는 고객도 있고 모든 Redis 기능을 완전하게 활용하기 위해 Linux 가상 컴퓨터에서 자체 Redis 서버를 호스팅하려는 고객도 있습니다.

이 자습서에서는 샘플 ARM(Azure 리소스 관리자) 템플릿을 사용하여 Microsoft Azure에서 리소스 그룹의 서브넷 내에 있는 Ubuntu VM에 Redis 클러스터를 배포하는 방법을 안내합니다. 이 템플릿은 Redis 3.0 클러스터 외에 Redis Sentinel이 포함된 Redis 2.8 배포도 지원합니다. 이 자습서에서는 Redis 3.0 클러스터 구현만 중점적으로 설명합니다.

Redis 클러스터는 서브넷에서 생성되므로 공용 IP를 사용하여 Redis 클러스터에 액세스할 수는 없습니다. 배포 프로세스의 일부로 "점프 상자"를 선택적으로 배포할 수 있습니다. 이 "점프 상자"는 서브넷에서도 배포되는 Ubuntu VM이지만 SSH를 실행할 수 있는 열린 SSH 포트를 통해 공용 IP 주소를 *표시*합니다. 그러면 이 "점프 상자"에서 서브넷의 모든 Redis VM에 대해 SSH를 실행할 수 있습니다.

이 템플릿은 “T셔츠 크기" 개념을 사용하여 “S", “M" 또는 “L"로 Redis 클러스터 설정을 지정합니다. ARM 템플릿 언어가 보다 동적인 템플릿 크기 지정 방법을 지원하는 경우 해당 설정을 변경하여 Redis 클러스터 마스터 노드와 슬레이브 노드의 수, VM 크기 등을 지정할 수 있습니다. 현재는 **azuredeploy.json** 파일의 `tshirtSizeSmall`, `tshirtSizeMedium`, `tshirtSizeLarge` 변수에 정의된 마스터와 슬레이브의 수 및 VM 크기를 확인할 수 있습니다.

“M” T셔츠 크기의 Redis 클러스터 템플릿을 사용하면 다음과 같은 구성을 만들 수 있습니다.

![cluster-architecture](media/virtual-machines-redis-template/cluster-architecture.png)

Azure 리소스 관리자 및 이 배포에 사용할 템플릿과 관련된 자세한 내용을 살펴보기 전에 Azure PowerShell 또는 Azure CLI가 올바르게 구성되어 있는지 확인하세요.

[AZURE.INCLUDE [arm-getting-setup-powershell](../../includes/arm-getting-setup-powershell.md)]

[AZURE.INCLUDE [xplat-getting-set-up-arm](../../includes/xplat-getting-set-up-arm.md)]

## 리소스 관리자 템플릿을 사용하여 Redis 클러스터 배포

Github 템플릿 리포지토리의 리소스 관리자 템플릿을 사용하여 Redis 클러스터를 만들려면 다음 단계를 수행합니다. 각 단계에는 Azure PowerShell 및 Azure CLI에 대한 지침이 모두 포함되어 있습니다.

### 1a단계: PowerShell을 사용하여 템플릿 파일 다운로드

JSON 템플릿과 기타 관련 파일을 저장할 C:\\Azure\\Templates\\RedisCluster와 같은 로컬 폴더를 만듭니다.

다음 예제의 폴더 이름을 사용 중인 로컬 폴더의 이름으로 대체하여 다음 명령을 실행합니다.

```powershell
$folderName="<folder name, such as C:\Azure\Templates\RedisCluster>"
$webclient = New-Object System.Net.WebClient
$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/azuredeploy.json"
$filePath = $folderName + "\azuredeploy.json"
$webclient.DownloadFile($url,$filePath)

$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/azuredeploy-parameters.json"
$filePath = $folderName + "\azuredeploy-parameters.json"
$webclient.DownloadFile($url,$filePath)

$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/empty-resources.json"
$filePath = $folderName + "\empty-resources.json"
$webclient.DownloadFile($url,$filePath)

$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/jumpbox-resources.json"
$filePath = $folderName + "\jumpbox-resources.json"
$webclient.DownloadFile($url,$filePath)

$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/node-resources.json"
$filePath = $folderName + "\node-resources.json"
$webclient.DownloadFile($url,$filePath)

$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/redis-cluster-install.sh"
$filePath = $folderName + "\redis-cluster-install.sh"
$webclient.DownloadFile($url,$filePath)

$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/redis-cluster-setup.sh"
$filePath = $folderName + "\redis-cluster-setup.sh"
$webclient.DownloadFile($url,$filePath)

$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/redis-sentinel-startup.sh"
$filePath = $folderName + "\redis-sentinel-startup.sh"
$webclient.DownloadFile($url,$filePath)

$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/shared-resources.json"
$filePath = $folderName + "\shared-resources.json"
$webclient.DownloadFile($url,$filePath)
```

### 1b단계: Azure CLI를 사용하여 템플릿 파일 다운로드

다음과 같이 원하는 git 클라이언트를 사용하여 전체 템플릿 리포지토리를 복제합니다.

```
git clone https://github.com/Azure/azure-quickstart-templates C:\Azure\Templates
```

복제가 완료되면 C:\\Azure\\Templates 디렉터리에서 **redis-high-availability** 폴더를 찾습니다.

### 2단계: (선택 사항) 템플릿 매개 변수 파악 

템플릿을 사용하여 Redis 클러스터를 만들 때는 구성 매개 변수 집합을 지정해야 합니다. Redis 클러스터를 만드는 명령을 실행하기 전에 로컬 JSON 파일에서 템플릿을 지정하는 데 필요한 매개 변수를 확인하려면 선택한 도구 또는 텍스트 편집기에서 JSON 파일을 엽니다.

파일의 위쪽에 있는 **"parameters"** 섹션을 찾습니다. 이 섹션은 템플릿에서 Redis 클러스터를 구성하는 데 필요한 매개 변수 집합을 나열합니다. 다음은 azuredeploy.json 템플릿의 **"parameters"** 섹션입니다.

```json
"parameters": {
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
	"storageAccountName": {
		"type": "string",
		"defaultValue": "",
		"metadata": {
			"Description": "Unique namespace for the Storage Account where the Virtual Machine's disks will be placed"
		}
	},
	"location": {
		"type": "string",
		"metadata": {
			"Description": "Location where resources will be provisioned"
		}
	},
	"virtualNetworkName": {
		"type": "string",
		"defaultValue": "redisVirtNet",
		"metadata": {
			"Description": "The arbitrary name of the virtual network provisioned for the Redis cluster"
		}
	},
	"addressPrefix": {
		"type": "string",
		"defaultValue": "10.0.0.0/16",
		"metadata": {
			"Description": "The network address space for the virtual network"
		}
	},
	"subnetName": {
		"type": "string",
		"defaultValue": "redisSubnet1",
		"metadata": {
			"Description": "Subnet name for the virtual network that resources will be provisioned in to"
		}
	},
	"subnetPrefix": {
		"type": "string",
		"defaultValue": "10.0.0.0/24",
		"metadata": {
			"Description": "Address space for the virtual network subnet"
		}
	},
	"nodeAddressPrefix": {
		"type": "string",
		"defaultValue": "10.0.0.1",
		"metadata": {
			"Description": "The IP address prefix that will be used for constructing a static private IP address for each node in the cluster"
		}
	},
	"jumpbox": {
		"type": "string",
		"defaultValue": "Disabled",
		"allowedValues": [
			"Enabled",
			"Disabled"
		],
		"metadata": {
			"Description": "The flag allowing to enable or disable provisioning of the jumpbox VM that can be used to access the Redis nodes"
		}
	},
	"tshirtSize": {
		"type": "string",
		"defaultValue": "Small",
		"allowedValues": [
			"Small",
			"Medium",
			"Large"
		],
		"metadata": {
			"Description": "T-shirt size of the Redis deployment"
		}
	},
	"redisVersion": {
		"type": "string",
		"defaultValue": "stable",
		"metadata": {
			"Description": "The version of the Redis package to be deployed on the cluster (or use 'stable' to pull in the latest and greatest)"
		}
	},
	"redisClusterName": {
		"type": "string",
		"defaultValue": "redis-cluster",
		"metadata": {
			"Description": "The arbitrary name of the Redis cluster"
		}
	}
},
```

각 매개 변수에는 데이터 형식, 허용되는 값 등의 세부 정보가 있습니다. 따라서 PowerShell 또는 Azure CLI와 같은 대화형 모드에서 템플릿을 실행하는 동안 전달되는 매개 변수의 유효성을 검사할 수 있으며, 필수 매개 변수 및 해당 설명 목록을 구문 분석하여 동적으로 작성할 수 있는 자체 검색 UI도 사용 가능합니다.

### 3a단계: PowerShell을 사용하여 템플릿을 통해 Redis 클러스터 배포

모든 매개 변수에 대한 런타임 값이 포함된 JSON 파일을 만들어 배포용 매개 변수 파일을 준비합니다. 이 파일을 배포 명령에 단일 엔터티로 전달합니다. 매개 변수 파일을 포함하지 않으면 PowerShell은 템플릿에 지정된 기본값을 사용하며 나머지 값을 입력하라는 메시지를 표시합니다.

다음은 **azuredeploy-parameters.json** 파일에서 볼 수 있는 예제입니다. `storageAccountName`, `adminUsername`, `adminPassword` 매개 변수의 유효한 값을 입력해야 하며 필요한 경우 다른 매개 변수도 사용자 지정해야 합니다.

```json
{
	"storageAccountName": {
		"value": "redisdeploy1"
	},
	"adminUsername": {
		"value": ""
	},
	"adminPassword": {
		"value": ""
	},
	"location": {
		"value": "West US"
	},
	"virtualNetworkName": {
		"value": "redisClustVnet"
	},
	"subnetName": {
		"value": "Subnet1"
	},
	"addressPrefix": {
		"value": "10.0.0.0/16"
	},
	"subnetPrefix": {
		"value": "10.0.0.0/24"
	},
	"nodeAddressPrefix": {
		"value": "10.0.0.1"
	},
	"redisVersion": {
		"value": "3.0.0"
	},
	"redisClusterName": {
		"value": "redis-arm-cluster"
	},
	"jumpbox": {
		"value": "Enabled"
	},
	"tshirtSize":  {
		"value": "Small"
	}
}
```

>[AZURE.NOTE]`storageAccountName` 매개 변수는 Microsoft Azure 저장소 계정의 이름 지정 요구 사항(소문자와 숫자만 사용 가능함)을 충족하는 고유한 새 저장소 계정 이름이어야 합니다. 이 저장소 계정은 배포 프로세스의 일부로 작성됩니다.

Azure 배포 이름, 리소스 그룹 이름, Azure 위치 및 저장된 JSON 파일의 폴더를 입력합니다. 그런 후에 다음 명령을 실행합니다.

```powershell
$deployName="<deployment name>, such as TestDeployment"
$RGName="<resource group name>, such as TestRG"
$locName="<Azure location, such as West US>"
$folderName="<folder name, such as C:\Azure\Templates\RedisCluster>"
$templateFile= $folderName + "\azuredeploy.json"
$templateParameterFile= $folderName + "\azuredeploy-parameters.json"

New-AzureResourceGroup –Name $RGName –Location $locName

New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateParameterFile $templateParameterFile -TemplateFile $templateFile
```

>[AZURE.NOTE]`$RGName`은 구독 내에서 고유해야 합니다.

**New-AzureResourceGroupDeployment** 명령을 실행하면 매개 변수 JSON 파일(azuredeploy-parameters.json)에서 매개 변수 값이 추출되며 그에 따라 템플릿 실행이 시작됩니다. 여러 매개 변수 파일을 정의하여 다양한 환경(예: 테스트, 프로덕션 등)에서 사용하면 템플릿 재사용률이 향상되고 복잡한 다중 환경 솔루션이 간소화됩니다.

저장소 계정 매개 변수로 제공하는 이름은 고유해야 하며 Azure 저장소 계정의 모든 요구 사항(소문자와 숫자만 사용 가능함)을 충족해야 하므로 배포할 때 새 Azure 저장소 계정을 만들어야 합니다.

배포 중에는 다음과 같은 내용이 표시됩니다.

	PS C:> New-AzureResourceGroup –Name $RGName –Location $locName

	ResourceGroupName : TestRG
	Location          : westus
	ProvisioningState : Succeeded
	Tags              :
	Permissions       :
                    Actions  NotActions
                    =======  ==========
                    *

	ResourceId        : /subscriptions/1234abc1-abc1-1234-12a1-ab1ab12345ab/resourceGroups/TestRG

	PS C:> New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateParameterFile $templateParameterFile -TemplateFile $templateFile
	VERBOSE: 2:39:10 PM - Template is valid.
	VERBOSE: 2:39:14 PM - Create template deployment 'TestDeployment'.
	VERBOSE: 2:39:25 PM - Resource Microsoft.Resources/deployments 'shared-resources' provisioning status is running
	VERBOSE: 2:40:04 PM - Resource Microsoft.Resources/deployments 'node-resources0' provisioning status is running
	VERBOSE: 2:40:05 PM - Resource Microsoft.Resources/deployments 'jumpbox-resources' provisioning status is running
	VERBOSE: 2:40:05 PM - Resource Microsoft.Resources/deployments 'node-resources1' provisioning status is running
	VERBOSE: 2:40:05 PM - Resource Microsoft.Resources/deployments 'shared-resources' provisioning status is succeeded
	VERBOSE: 2:42:23 PM - Resource Microsoft.Resources/deployments 'jumpbox-resources' provisioning status is succeeded
	VERBOSE: 2:47:44 PM - Resource Microsoft.Resources/deployments 'node-resources1' provisioning status is succeeded
	VERBOSE: 2:47:57 PM - Resource Microsoft.Resources/deployments 'node-resources0' provisioning status is succeeded
	VERBOSE: 2:48:01 PM - Resource Microsoft.Resources/deployments 'lastnode-resources' provisioning status is running
	VERBOSE: 2:58:24 PM - Resource Microsoft.Resources/deployments 'lastnode-resources' provisioning status is succeeded

	DeploymentName    : TestDeployment
	ResourceGroupName : TestRG
	ProvisioningState : Succeeded
	Timestamp         : 4/28/2015 7:58:23 PM
	Mode              : Incremental
	TemplateLink      :
	Parameters        :
                    Name             Type                       Value
                    ===============  =========================  ==========
                    adminUsername    String                     myadmin
                    adminPassword    SecureString
                    storageAccountName  String                     myuniqstgacct87
                    location         String                     West US
                    virtualNetworkName  String                     redisClustVnet
                    addressPrefix    String                     10.0.0.0/16
                    subnetName       String                     Subnet1
                    subnetPrefix     String                     10.0.0.0/24
                    nodeAddressPrefix  String                     10.0.0.1
                    jumpbox          String                     Enabled
                    tshirtSize       String                     Small
                    redisVersion     String                     3.0.0
                    redisClusterName  String                     redis-arm-cluster

	Outputs           :
                    Name             Type                       Value
                    ===============  =========================  ==========
                    installCommand   String                     bash redis-cluster-install.sh -n redis-arm-cluster -v
                    3.0.0 -c 3 -m 3 -s 0 -p 10.0.0.1
                    setupCommand     String                     bash redis-cluster-install.sh -n redis-arm-cluster -v
                    3.0.0 -c 3 -m 3 -s 0 -p 10.0.0.1 -l
	...

배포하는 동안과 배포한 후 발생한 모든 오류를 포함하여 프로비전 중에 실행된 모든 요청을 확인할 수 있습니다.

이렇게 하려면 [Azure 포털](https://portal.azure.com)로 이동하여 다음을 수행합니다.

- 왼쪽 탐색 모음에서 "찾아보기"를 클릭하고 아래로 스크롤하여 "리소스 그룹"을 클릭합니다.
- 방금 만든 리소스 그룹을 선택하면 "리소스 그룹" 블레이드가 나타납니다.
- 모니터링 섹션에서 "이벤트" 막대 그래프를 선택합니다. 그러면 배포에 대한 이벤트가 표시됩니다.
- 개별 이벤트를 클릭하면 템플릿을 대신해 수행된 개별 작업에 대한 세부 정보를 볼 수 있습니다.

테스트 후 이 리소스 그룹과 모든 해당 리소스(저장소 계정, 가상 컴퓨터 및 가상 네트워크)를 제거해야 하는 경우 다음 명령을 사용합니다.

```powershell
Remove-AzureResourceGroup –Name "<resource group name>"
```

### 3b단계: Azure CLI를 사용하여 템플릿을 통해 Redis 클러스터 배포

Azure CLI를 통해 Redis 클러스터를 배포하려면 먼저 이름과 위치를 지정하여 리소스 그룹을 만듭니다.

```powershell
azure group create TestRG "West US"
```

이 리소스 그룹 이름, JSON 템플릿 파일의 위치, 그리고 매개 변수 파일의 위치(위의 PowerShell 섹션에서 자세한 내용 확인)를 다음 명령으로 전달합니다.

```powershell
azure group deployment create TestRG -f .\azuredeploy.json -e .\azuredeploy-parameters.json
```

다음 명령을 사용하여 개별 리소스 배포의 상태를 확인할 수 있습니다.

```powershell
azure group deployment list TestRG
```

## Redis 클러스터 템플릿 구조 및 파일 구성 살펴보기 

강력하고 재사용 가능한 리소스 관리자 템플릿 디자인을 만들려면 Redis 클러스터와 같은 복잡한 솔루션을 배포하는 동안 필요한 상호 관련된 일련의 복잡한 작업을 구성해야 합니다. 관련된 확장을 통해 스크립트를 실행하는 것 외에 ARM **템플릿 연결** 기능 및 **리소스 루핑**을 활용하면 대부분의 복잡한 템플릿 기반 배포에서 재사용할 수 있는 모듈식 방법을 구현할 수 있습니다.

다음 다이어그램에서는 이 배포를 위해 GitHub에서 다운로드한 모든 파일 간의 관계를 설명합니다.

![redis-files](media/virtual-machines-redis-template/redis-files.png)

이 섹션에서는 Redis 클러스터용 azuredeploy.json 템플릿의 구조를 단계별로 설명합니다.

템플릿 파일의 복사본을 다운로드하지 않은 경우 파일 위치로 C:\\Azure\\Templates\\RedisCluster 등의 로컬 폴더를 지정하여 해당 폴더를 만듭니다. 폴더 이름을 입력하고 다음 명령을 실행합니다.

```powershell
$folderName="<folder name, such as C:\Azure\Templates\RedisCluster>"
$webclient = New-Object System.Net.WebClient
$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/azuredeploy.json"
$filePath = $folderName + "\azuredeploy.json"
$webclient.DownloadFile($url,$filePath)
```

텍스트 편집기 또는 선택한 도구에서 azuredeploy.json 템플릿을 엽니다. 아래에 템플릿 파일의 구조와 각 섹션의 용도에 대한 설명이 나와 있습니다. [여기](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/azuredeploy.json)서 브라우저를 통해 이 템플릿의 내용을 확인할 수도 있습니다.

### "parameters" 섹션

템플릿 실행 중 지정된 매개 변수 값 집합을 전달하는 데 사용되는 **azuredeploy-parameters.json**의 역할에 대해서는 이미 설명했습니다. azuredeploy.json의 "parameters" 섹션은 이 템플릿에 데이터를 입력하는 데 사용되는 매개 변수를 지정합니다.

“T셔츠 크기"에 대한 매개 변수의 예는 다음과 같습니다.

```json
"tshirtSize": {
	"type": "string",
	"defaultValue": "Small",
	"allowedValues": [
		"Small",
		"Medium",
		"Large"
	],
	"metadata": {
		"Description": "T-shirt size of the Redis deployment"
	}
},
```

>[AZURE.NOTE]`defaultValue`와 `allowedValues`를 모두 지정할 수 있습니다.

### "variables" 섹션

"variables" 섹션은 이 템플릿 전체에서 사용할 수 있는 변수를 지정합니다. 기본적으로 이 섹션에는 실행 시 상수 또는 계산된 값으로 설정되는 여러 필드(JSON 데이터 형식 또는 조각)가 포함됩니다. 아래에는 간단한 예제와 복잡한 예제가 모두 나와 있습니다.

```json
"vmStorageAccountContainerName": "vhd-redis",
"vmStorageAccountDomain": ".blob.core.windows.net",
"vnetID": "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworkName'))]",
...
"machineSettings": {
	"adminUsername": "[parameters('adminUsername')]",
	"machineNamePrefix": "redisnode-",
	"osImageReference": {
		"publisher": "[variables('osFamilyUbuntu').imagePublisher]",
		"offer": "[variables('osFamilyUbuntu').imageOffer]",
		"sku": "[variables('osFamilyUbuntu').imageSKU]",
		"version": "latest"
	}
},
...
"vmScripts": {
	"scriptsToDownload": [
		"[concat(variables('scriptUrl'), 'redis-cluster-install.sh')]",
		"[concat(variables('scriptUrl'), 'redis-cluster-setup.sh')]",
		"[concat(variables('scriptUrl'), 'redis-sentinel-startup.sh')]"
	],
	"installCommand": "[concat('bash ', variables('installCommand'))]",
	"setupCommand": "[concat('bash ', variables('installCommand'), ' -l')]"
}
```

 `vmStorageAccountContainerName` 및 `vmStorageAccountDomain`은 간단한 이름/값 변수의 예입니다. `vnetID`는 `resourceId` 및 `parameters` 함수를 사용하여 런타임에 계산되는 변수의 예입니다. `machineSettings`는 JSON 개체 `osImageReference`를 `machineSettings` 변수에 중첩하여 이러한 개념을 확장합니다. `vmScripts`에는 JSON 배열 `scriptsToDownload`가 포함되며, 이 배열은 `concat` 및 `variables` 함수를 사용하여 런타임에 계산됩니다.

Redis 클러스터 배포의 크기를 사용자 지정하려는 경우 **azuredeploy.json** 템플릿에서 `tshirtSizeSmall`, `tshirtSizeMedium`, 및 `tshirtSizeLarge` 변수의 속성을 변경할 수 있습니다.

```json
"tshirtSizeSmall": {
	"vmSizeMember": "Standard_A1",
	"numberOfMasters": 3,
	"numberOfSlaves": 0,
	"totalMemberCount": 3,
	"totalMemberCountExcludingLast": 2,
	"vmTemplate": "[concat(variables('templateBaseUrl'), 'node-resources.json')]"
},
"tshirtSizeMedium": {
	"vmSizeMember": "Standard_A2",
	"numberOfMasters": 3,
	"numberOfSlaves": 3,
	"totalMemberCount": 6,
	"totalMemberCountExcludingLast": 5,
	"vmTemplate": "[concat(variables('templateBaseUrl'), 'node-resources.json')]"
},
"tshirtSizeLarge": {
	"vmSizeMember": "Standard_A5",
	"numberOfMasters": 3,
	"numberOfSlaves": 6,
	"totalMemberCount": 9,
	"totalMemberCountExcludingLast": 8,
	"arbiter": "Enabled",
	"vmTemplate": "[concat(variables('templateBaseUrl'), 'node-resources.json')]"
},
```

참고: 현재 템플릿 언어에는 수학 식 연산이 포함되어 있지 않으므로 `totalMemberCountExcludingLast` 및 `totalMemberCount` 속성이 필요합니다.

템플릿 언어에 대한 자세한 내용은 MSDN의 [Azure 리소스 관리자 템플릿 언어](https://msdn.microsoft.com/library/azure/dn835138.aspx)에서 확인할 수 있습니다.

### "resources" 섹션

**"resources"** 섹션에서 대부분의 작업이 수행됩니다. 이 섹션을 자세히 살펴보면 두 가지 사항을 즉시 확인할 수 있습니다. 그 중 첫 번째는 기본적으로 기본 배포 내 중첩된 배포를 호출하는 `Microsoft.Resources/deployments` 형식으로 정의된 요소입니다. 그리고 두 번째는 매개 변수 집합을 입력으로 전달하여 호출하려는 연결된 템플릿 파일을 지정할 수 있도록 하는 `templateLink` 속성 및 관련 `contentVersion` 속성입니다. 이러한 사항은 다음 템플릿 코드 조각에서 확인할 수 있습니다.

```json
{
    "name": "shared-resources",
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2015-01-01",
    "properties": {
        "mode": "Incremental",
        "templateLink": {
            "uri": "[variables('sharedTemplateUrl')]",
            "contentVersion": "1.0.0.0"
        },
        "parameters": {
            "commonSettings": {
                "value": "[variables('commonSettings')]"
            },
            "storageSettings": {
                "value": "[variables('storageSettings')]"
            },
            "networkSettings": {
                "value": "[variables('networkSettings')]"
            }
        }
    }
},
```

이 첫 번째 예제에서는 이 시나리오의 **azuredeploy.json**이 일종의 오케스트레이션 메커니즘으로 구성되어 각각 필요한 배포 작업의 일부를 담당하는 여러 템플릿 파일을 호출합니다.

특히 다음과 같은 연결된 템플릿이 이 배포에 사용됩니다.

- **shared-resource.json**: 배포에서 공유할 모든 리소스의 정의를 포함합니다. 예를 들어 VM의 OS 디스크, 가상 네트워크 및 가용성 집합을 저장하는 데 사용되는 저장소 계정이 여기에 속합니다.
- **jumpbox resources.json**: "점프 상자" VM 및 모든 관련 리소스(예: 환경에 대한 SSH를 실행하는 데 사용되는 입력 끝점, 공용 IP 주소, 네트워크 인터페이스)를 배포합니다.
- **node-resources.json**: 모든 Redis 클러스터 노드 VM 및 연결된 리소스(예: 네트워크 카드, 개인 IP 등)를 배포합니다. 또한 이 템플릿은 각 노드에 Redis를 물리적으로 설치 및 설정하기 위해 VM 확장(Linux용 사용자 지정 스크립트)을 배포하고 bash 스크립트를 호출합니다. 호출할 스크립트는 `machineSettings` 매개 변수 `commandToExecute` 속성에서 이 템플릿으로 전달됩니다. Redis 클러스터 노드 중 하나를 제외한 모든 노드를 병렬로 배포하고 스크립팅할 수 있습니다. Redis 클러스터 설정은 노드 하나에서만 실행할 수 있으며 모든 노드에서 Redis 서버를 실행한 후에 설정을 수행해야 하므로 노드 하나는 끝까지 남겨 두어야 합니다. 따라서 실행할 스크립트가 이 템플릿으로 전달됩니다. 마지막 노드는 Redis 서버를 설치할 뿐 아니라 Redis 클러스터도 설정하는 약간 다른 스크립트를 실행해야 하기 때문입니다.

이 마지막 템플릿, 즉 **node-resources.json**은 템플릿 개발 관점에서 가장 흥미로운 항목 중 하나이므로 사용 *방법*{b>을 <b}좀 더 자세히 살펴보겠습니다. 한 가지 중요한 개념은 단일 템플릿 파일에서 단일 리소스 유형의 여러 복사본을 배포하고 각 인스턴스에 대해 필요한 설정의 고유 값을 설정하는 방법입니다. 이 개념은 **리소스 루핑**이라고 합니다.

**node-resources.json**은 기본 **azuredeploy.json** 파일 내에서 호출하면 `copy` 요소를 사용하여 일종의 루프를 만드는 리소스 내에서 호출됩니다. `copy` 요소를 사용하는 리소스는 `copy` 요소의 `count` 매개 변수에 지정된 횟수만큼 자체적으로 “복사"됩니다. 배포된 리소스의 여러 인스턴스 간에 고유한 값을 지정하는 데 필요한 모든 설정의 경우 **copyindex()** 함수를 사용하여 해당 특정 리소스 루프 만들기에서 현재 인덱스를 나타내는 숫자 값을 가져올 수 있습니다. 다음 **azuredeploy.json** 코드 조각에서는 Redis 클러스터 노드에 대해 작성되는 여러 VM에 이 개념이 적용됨을 확인할 수 있습니다.

```json
{
    "type": "Microsoft.Resources/deployments",
    "name": "[concat('node-resources', copyindex())]",
    "apiVersion": "2015-01-01",
    "dependsOn": [
        "[concat('Microsoft.Resources/deployments/', 'shared-resources')]"
    ],
    "copy": {
        "name": "memberNodesLoop",
        "count": "[variables('clusterSpec').totalMemberCountExcludingLast]"
    },
    "properties": {
        "mode": "Incremental",
        "templateLink": {
            "uri": "[variables('clusterSpec').vmTemplate]",
            "contentVersion": "1.0.0.0"
        },
        "parameters": {
            "commonSettings": {
                "value": "[variables('commonSettings')]"
            },
            "storageSettings": {
                "value": "[variables('storageSettings')]"
            },
            "networkSettings": {
                "value": "[variables('networkSettings')]"
            },
            "machineSettings": {
                "value": {
                    "adminUsername": "[variables('machineSettings').adminUsername]",
                    "machineNamePrefix": "[variables('machineSettings').machineNamePrefix]",
                    "osImageReference": "[variables('machineSettings').osImageReference]",
                    "vmSize": "[variables('clusterSpec').vmSizeMember]",
                    "machineIndex": "[copyindex()]",
                    "vmScripts": "[variables('vmScripts').scriptsToDownload]",
                    "commandToExecute": "[concat(variables('vmScripts').installCommand, ' -i ', copyindex())]"
                }
            },
            "adminPassword": {
                "value": "[parameters('adminPassword')]"
            }
        }
    }
},
```

리소스 만들기의 또 다른 중요한 개념은 `dependsOn` JSON 배열에서 볼 수 있는 것처럼 리소스 간의 종속성 및 우선 순위를 지정하는 기능입니다. 이 특정 템플릿에서는 Redis 클러스터 노드가 먼저 작성되는 공유 리소스에 종속된다는 사실을 확인할 수 있습니다.

앞에서 설명한 것처럼 Redis 클러스터의 다른 모든 노드가 프로비전되고 모든 노드에서 Redis 서버가 실행될 때까지 기다린 후에 마지막 노드를 프로비전해야 합니다. 이렇게 하려면 **azuredeploy.json**에서 위 템플릿 코드 조각의 `memberNodesLoop`라는 `copy` 루프를 사용하는 `lastnode-resources` 리소스를 포함합니다. `memberNodesLoop`가 완료되면 `lastnode-resources`를 프로비전할 수 있습니다.

```json
{
	"name": "lastnode-resources",
	"type": "Microsoft.Resources/deployments",
	"apiVersion": "2015-01-01",
	"dependsOn": [
		"memberNodesLoop"
	],
	"properties": {
		"mode": "Incremental",
		"templateLink": {
			"uri": "[variables('clusterSpec').vmTemplate]",
			"contentVersion": "1.0.0.0"
		},
		"parameters": {
			"commonSettings": {
				"value": "[variables('commonSettings')]"
			},
			"storageSettings": {
				"value": "[variables('storageSettings')]"
			},
			"networkSettings": {
				"value": "[variables('networkSettings')]"
			},
			"machineSettings": {
				"value": {
					"adminUsername": "[variables('machineSettings').adminUsername]",
					"machineNamePrefix": "[variables('machineSettings').machineNamePrefix]",
					"osImageReference": "[variables('machineSettings').osImageReference]",
					"vmSize": "[variables('clusterSpec').vmSizeMember]",
					"machineIndex": "[variables('clusterSpec').totalMemberCountExcludingLast]",
					"vmScripts": "[variables('vmScripts').scriptsToDownload]",
					"commandToExecute": "[concat(variables('vmScripts').setupCommand, ' -i ', variables('clusterSpec').totalMemberCountExcludingLast)]"
				}
			},
			"adminPassword": {
				"value": "[parameters('adminPassword')]"
			}
		}
	}
}
```

`lastnode-resources` 리소스는 연결된 템플릿에 약간 다른 `machineSettings.commandToExecute`를 전달합니다. 마지막 노드의 경우 설치된 Redis 서버뿐 아니라 Redis 클러스터를 설정하는 스크립트도 호출해야 하며, 이 호출은 모든 Redis 서버가 작동 및 실행된 후에만 수행해야 하기 때문입니다.

또 다른 흥미로운 조각은 `CustomScriptForLinux` VM 확장과 관련된 조각입니다. 이러한 확장은 별도의 리소스 유형으로 설치되며 각 클러스터 노드에 대한 종속성이 적용됩니다. 여기서는 이 확장을 사용하여 각 VM 노드에서 Redis를 설치 및 설정합니다. 이러한 확장을 사용하는 **node-resources.json** 템플릿의 코드 조각을 살펴보겠습니다.

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "[concat('vmMember', parameters('machineSettings').machineIndex, '/installscript')]",
    "apiVersion": "2015-05-01-preview",
    "location": "[parameters('commonSettings').location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', 'vmMember', parameters('machineSettings').machineIndex)]"
    ],
    "properties": {
        "publisher": "Microsoft.OSTCExtensions",
        "type": "CustomScriptForLinux",
        "typeHandlerVersion": "1.2",
        "settings": {
            "fileUris": "[parameters('machineSettings').vmScripts]",
            "commandToExecute": "[parameters('machineSettings').commandToExecute]"
        }
    }
}
```

이 리소스는 이미 배포된 리소스 VM(Microsoft.Compute/virtualMachines/vmMember<X>)에 종속됨을 확인할 수 있습니다. 여기서 <X>는 “copyindex()” 함수를 사용하여 이 스크립트로 전달된 VM의 인덱스인 "machineSettings.machineIndex" 매개 변수입니다.

이 배포에 포함된 다른 파일을 알면 Azure 리소스 관리자 템플릿을 활용하여 모든 기술을 기반으로 하는 다중 노드 솔루션에 대한 복잡한 배포 전략을 구성 및 오케스트레이션하는 데 필요한 모든 세부 정보 및 모범 사례를 이해할 수 있습니다. 필수 사항은 아니지만 다음 다이어그램에 강조 표시된 대로 템플릿 파일을 구성하는 것이 좋습니다.

![redis-template-structure](media/virtual-machines-redis-template/redis-template-structure.png)

기본적으로 이 방법은 다음 사항을 제안합니다.

- 템플릿 연결을 활용하여 하위 템플릿 실행을 호출하는 모든 특정 배포 작업을 위해 핵심 템플릿 파일을 중앙 오케스트레이션 지점으로 정의합니다.
- 다른 모든 특정 배포 작업(예: 저장소 계정, vnet 구성 등)에서 공유되는 모든 리소스를 배포할 특정 템플릿 파일을 만듭니다. 이는 주로 공용 인프라 면에서 요구 사항이 유사한 배포 간에 재사용될 수 있습니다.
- 지정된 리소스에 특정한 요구 사항을 충족하는 선택적 리소스 템플릿을 포함합니다.
- 리소스 그룹의 동일한 구성원(클러스터의 노드 등)에 대해 고유한 속성의 여러 인스턴스를 배포하기 위해 리소스 루핑을 활용하는 특정 템플릿을 만듭니다.
- 모든 배포 후 작업(예: 제품 설치, 구성 등)의 경우 스크립트 배포 확장을 활용하고 각 기술에 특정한 스크립트를 만듭니다.

자세한 내용은 [Azure 리소스 관리자 템플릿 언어](https://msdn.microsoft.com/library/azure/dn835138.aspx)를 참조하세요.

<!--HONumber=54--> 