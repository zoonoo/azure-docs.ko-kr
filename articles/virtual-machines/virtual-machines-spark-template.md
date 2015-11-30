<properties
	pageTitle="Ubuntu 리소스 관리자 템플릿의 Spark | Microsoft Azure"
	description="Azure PowerShell 또는 Azure CLI 및 리소스 관리자 템플릿을 사용하여 Ubuntu VM에 새 Spark 클러스터를 배포할 수 있는 방법입니다."
	services="virtual-machines"
	documentationCenter=""
	authors="paolosalvatori"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="multiple"
	ms.date="05/16/2015"
	ms.author="paolosalvatori"/>

# 리소스 관리자 템플릿을 사용하는 Ubuntu의 Spark

Apache Spark는 대규모 데이터 처리를 위한 고속 엔진입니다. Spark는 순환 데이터 흐름 및 메모리 내 컴퓨팅을 지원하는 고급 DAG 실행 엔진을 포함하며, HDFS, Spark, HBase, S3 등 다양한 데이터 원본에 액세스할 수 있습니다.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]클래식 배포 모델. 클래식 배포 모델을 사용하여 이 리소스를 배포할 수 없습니다.


Spark는 Mesos 또는 YARN 클러스터 관리자를 실행할 뿐만 아니라 간단한 독립 실행형 배포 모드를 제공합니다. 이 자습서는 샘플 Azure 리소스 관리자 템플릿을 사용하여 [Azure PowerShell](../powershell-install-configure.md) 또는 [Azure CLI](../xplat-cli-install.md)를 통해 Ubuntu VM에 Spark 클러스터를 배포하는 방법을 안내합니다.

이 템플릿은 Ubuntu 가상 컴퓨터에 Spark 클러스터를 배포합니다. 또한 이 템플릿은 설치에 필요한 저장소 계정, 가상 네트워크, 가용성 집합, 공용 IP 주소 및 네트워크 인터페이스를 프로비전합니다. Spark 클러스터는 서브넷에서 생성되므로 공용 IP를 사용하여 Spark 클러스터에 액세스할 수 없습니다. 배포 과정에서, "점프 상자"를 선택적으로 배포할 수 있습니다. 이 "점프 상자"는 서브넷에도 배포된 Ubuntu VM이지만 연결할 수 있는 열린 SSH 포트로 공용 IP 주소를 노출*합니다*. 그런 다음 "점프 상자"에서, 서브넷에 있는 모든 Spark VM에 대해 SSH를 실행할 수 있습니다.

이 템플릿은 “T셔츠 크기" 개념을 사용하여 “Small", “Medium" 또는 “Large"로 Spark 클러스터 설정을 지정합니다. 템플릿 언어가 보다 동적인 템플릿 크기 지정 방법을 지원하는 경우 해당 설정을 변경하여 Spark 클러스터 마스터 노드와 슬레이브 노드의 수, VM 크기 등을 지정할 수 있습니다. 지금은, azuredeploy.json 파일의 **tshirtSizeS**, **tshirtSizeM** 및 **tshirtSizeL** 변수에 정의된 마스터와 슬레이브의 수 및 VM 크기를 확인할 수 있습니다.

- S: 마스터 1대, 슬레이브 1대
- M: 마스터 1대, 슬레이브 4대
- L: 마스터 1대, 슬레이브 6대

이 템플릿을 기반으로 새로 배포되는 클러스터에는 다음 다이어그램에 설명된 토폴로지가 구축됩니다. 물론 제공된 템플릿을 사용자 지정하여 다른 토폴리지를 쉽게 실현할 수도 있습니다.

![cluster-architecture](media/virtual-machines-spark-template/cluster-architecture.png)

위의 그림과 같이 배포 토폴로지는 다음과 같은 요소로 구성됩니다.

-	새로 만든 가상 컴퓨터의 OS 디스크를 호스트하는 새 저장소 계정
-	서브넷을 사용하는 가상 네트워크. 템플릿으로 생성된 모든 가상 컴퓨터가 이 가상 네트워크 내에 프로비전됩니다.
-	마스터 및 슬레이브 노드에 대한 가용성 집합
-	새로 생성된 가용성 집합의 마스터 노드
-	동일한 가상 서브넷에서 실행하는 슬레이브 노드 4개 및 마스터 노드인 가용성 집합
-	클러스터에 액세스하는 데 사용할 수 있는 동일한 가상 네트워크 및 서브넷에 있는 점프 상자 VM

Spark 버전 3.0.0이 기본 버전이며, Spark 리포지토리에서 사용할 수 있는 미리 작성된 이진 파일로 변경할 수 있습니다. 스크립트에는 원본에서 빌드의 주석 처리를 제거하는 프로비전도 있습니다. 각 Spark 마스터 노드에는 고정 IP 주소 10.0.0.10이 할당됩니다. 고정 IP 주소는 템플릿 내에서 동적으로 IP 주소 목록을 작성할 수 없는 현재 제한을 해결하기 위해 각 Spark 슬레이브 노드에 할당됩니다. 기본적으로 첫 번째 노드에는 개인 IP 주소 10.0.0.30이 할당되고, 두 번째 노드에는 10.0.0.31이 할당됩니다. 배포 오류를 확인하려면 새 Azure Preview 포털로 이동하고 **리소스 그룹** > **마지막 배포** > **작업 세부 정보 확인**을 살펴보세요.

Azure 리소스 관리자 및 이 배포에 사용할 템플릿과 관련된 자세한 내용을 살펴보기 전에 Azure PowerShell 또는 Azure CLI가 올바르게 구성되어 있는지 확인해야 합니다.

[AZURE.INCLUDE [arm-getting-setup-powershell](../../includes/arm-getting-setup-powershell.md)]

[AZURE.INCLUDE [xplat-getting-set-up-arm](../../includes/xplat-getting-set-up-arm.md)]

## 리소스 관리자 템플릿을 사용하여 Spark 클러스터 만들기

다음 단계를 따라 Azure PowerShell 또는 Azure CLI 중 하나를 통해 GitHub 템플릿 리포지토리에서 리소스 관리자 템플릿을 사용하여 Spark 클러스터를 만듭니다.

### 1-a단계: Azure PowerShell을 사용하여 템플릿 파일 다운로드

JSON 템플릿과 기타 연결된 파일을 저장할 로컬 폴더(예: C:\\Azure\\Templates\\Spark)를 만듭니다.

로컬 폴더의 폴더 이름을 대신 입력하여 다음 명령을 실행합니다.

```powershell
# Define variables
$folderName="C:\Azure\Templates\Spark"
$baseAddress = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/spark-on-ubuntu/"
$webclient = New-Object System.Net.WebClient
$files = $("azuredeploy.json", `
         "azuredeploy-parameters.json", `
         "jumpbox-resources-disabled.json", `
         "jumpbox-resources-enabled.json",
         "metadata.json", `
         "shared-resources.json", `
         "spark-cluster-install.sh", `
         "jumpbox-resources-enabled.json")

# Download files
foreach ($file in $files)
{
    $url = $baseAddress + $file
    $filePath = $folderName + $file
    $webclient.DownloadFile($baseAddress + $file, $folderName + $file)
    Write-Host $url "downloaded to" $filePath
}
```

### 1-b단계: Azure CLI를 사용하여 템플릿 파일 다운로드

다음과 같이 원하는 Git 클라이언트를 사용하여 전체 템플릿 리포지토리를 복제합니다.

	git clone https://github.com/Azure/azure-quickstart-templates C:\Azure\Templates

복제가 완료되면 C:\\Azure\\Templates 디렉터리에서 **spark-on-ubuntu** 폴더를 찾습니다.

### 2단계: (선택 사항) 템플릿 매개 변수 파악

템플릿을 사용하여 Spark 클러스터를 만들 때 필요한 여러 설정을 처리하는 구성 매개 변수 집합을 지정해야 합니다. 템플릿 정의에서 이러한 매개 변수를 선언하면 외부 파일을 통해 또는 명령줄에서 배포를 실행하는 동안 값을 지정할 수 있습니다.

azuredeploy.json 파일 위쪽의 "parameters" 섹션에서 Spark 클러스터를 구성하기 위해 템플릿에 필요한 매개 변수 집합을 찾을 수 있습니다. 이 템플릿의 azuredeploy.json 파일에 있는 “parameters” 섹션에 대한 예제는 다음과 같습니다.

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
	"imagePublisher": {
		"type": "string",
		"defaultValue": "Canonical",
		"metadata": {
			"Description": "Image publisher"
		}
	},
	"imageOffer": {
		"type": "string",
		"defaultValue": "UbuntuServer",
		"metadata": {
			"Description": "Image offer"
		}
	},
	"imageSKU": {
		"type": "string",
		"defaultValue": "14.04.2-LTS",
		"metadata": {
			"Description": "Image SKU"
		}
	},
	"storageAccountName": {
		"type": "string",
		"defaultValue": "uniquesparkstoragev12",
		"metadata": {
			"Description": "Unique namespace for the Storage account where the virtual machine's disks will be placed"
		}
	},
	"region": {
		"type": "string",
		"defaultValue": "West US",
		"metadata": {
			"Description": "Location where resources will be provisioned"
		}
	},
	"virtualNetworkName": {
		"type": "string",
		"defaultValue": "myVNETspark",
		"metadata": {
			"Description": "The arbitrary name of the virtual network provisioned for the cluster"
		}
	},
	"dataDiskSize": {
		"type": "int",
		"defaultValue": 100,
		"metadata": {
			"Description": "Size of the data disk attached to Spark nodes (in GB)"
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
		"defaultValue": "Subnet-1",
		"metadata": {
			"Description": "Subnet name for the virtual network that resources will be provisioned into"
		}
	},
	"subnetPrefix": {
		"type": "string",
		"defaultValue": "10.0.0.0/24",
		"metadata": {
			"Description": "Address space for the virtual network subnet"
		}
	},
	"sparkVersion": {
		"type": "string",
		"defaultValue": "stable",
		"metadata": {
			"Description": "The version of the Spark package to be deployed on the cluster (or use 'stable' to pull in the latest and greatest)"
		}
	},
	"sparkClusterName": {
		"type": "string",
		"metadata": {
			"Description": "The arbitrary name of the Spark cluster (maps to cluster's configuration file name)"
		}
	},
	"sparkNodeIPAddressPrefix": {
		"type": "string",
		"defaultValue": "10.0.0.1",
		"metadata": {
			"Description": "The IP address prefix that will be used for constructing a static private IP address for each node in the cluster"
		}
	},
	"sparkSlaveNodeIPAddressPrefix": {
		"type": "string",
		"defaultValue": "10.0.0.3",
		"metadata": {
			"Description": "The IP address prefix that will be used for constructing a static private IP address for each node in the cluster"
		}
	},
	"jumpbox": {
		"type": "string",
		"defaultValue": "enabled",
		"allowedValues": [
		"enabled",
		"disabled"
		],
		"metadata": {
			"Description": "The flag allowing to enable or disable provisioning of the jump-box VM that can be used to access the Spark nodes"
		}
	},
	"tshirtSize": {
		"type": "string",
		"defaultValue": "S",
		"allowedValues": [
		"S",
		"M",
		"L"
		],
		"metadata": {
			"Description": "T-shirt size of the Spark cluster"
		}
	}
}
```

각 매개 변수에는 데이터 형식, 허용되는 값 등의 세부 정보가 있습니다. 따라서 Azure PowerShell 또는 Azure CLI와 같은 대화형 모드에서 템플릿을 실행하는 동안 전달되는 매개 변수의 유효성을 검사할 수 있으며, 필수 매개 변수 및 해당 설명 목록을 구문 분석하여 동적으로 작성할 수 있는 자체 검색 UI도 사용할 수 있습니다.

### 3a 단계: Azure PowerShell을 통해 탬플릿을 사용하여 Spark 클러스터 배포

모든 매개 변수에 대한 런타임 값이 포함된 JSON 파일을 만들어 배포용 매개 변수 파일을 준비합니다. 이 파일이 배포 명령에 단일 엔터티로 전달됩니다. 매개 변수 파일을 포함하지 않으면 Azure PowerShell는 템플릿에 지정된 기본값을 사용하며 나머지 값을 입력하라는 메시지를 표시합니다.

다음은 azuredeploy-parameters.json 파일에서 볼 수 있는 매개 변수 집합의 예입니다. **storageAccountName**, **adminUsername** 및 **adminPassword** 매개 변수와 다른 매개 변수의 사용자 지정 항목에 대한 유효한 값을 제공해야 합니다.

```json
{
  "storageAccountName": {
    "value": "paolosspark"
  },
  "adminUsername": {
    "value": "paolos"
  },
  "adminPassword": {
    "value": "Passw0rd!"
  },
  "region": {
    "value": "West US"
  },
  "virtualNetworkName": {
    "value": "sparkClustVnet"
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
  "sparkVersion": {
    "value": "3.0.0"
  },
  "sparkClusterName": {
    "value": "spark-arm-cluster"
  },
  "sparkNodeIPAddressPrefix": {
		"value": "10.0.0.1"
  },
  "sparkSlaveNodeIPAddressPrefix": {
    "value": "10.0.0.3"
  },
  "jumpbox": {
    "value": "enabled"
  },
  "tshirtSize": {
    "value": "M"
  }
}
```
Azure 배포 이름, 리소스 그룹 이름, Azure 위치 및 저장된 JSON 배포 파일의 폴더를 입력합니다. 그런 후에 다음 명령을 실행합니다.

```powershell
$deployName="<deployment name>"
$RGName="<resource group name>"
$locName="<Azure location, such as West US>"
$folderName="<folder name, such as C:\Azure\Templates\Spark>"
$templateFile= $folderName + "\azuredeploy.json"
$templateParameterFile= $folderName + "\azuredeploy-parameters.json"
New-AzureResourceGroup -Name $RGName -Location $locName
New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateParamterFile $templateParameterFile -TemplateFile $templateFile
```
> [AZURE.NOTE]**$RGName**은 구독 내에서 고유해야 합니다.

**New-AzureResourceGroupDeployment** 명령을 실행하면 JSON 매개 변수 파일에서 매개 변수 값이 추출되고 그에 따라 템플릿 실행이 시작됩니다. 여러 매개 변수 파일을 정의하여 다양한 환경(예: 테스트, 프로덕션 등)에서 사용하면 템플릿 재사용이 향상되고 복잡한 다중 환경 솔루션이 간소화됩니다.

저장소 계정 매개 변수로 제공하는 이름은 고유해야 하며 Azure 저장소 계정의 모든 요구 사항(소문자와 숫자만 사용 가능함)을 충족해야 하므로 배포할 때 새 Azure 저장소 계정을 만들어야 합니다.

배포 중에는 다음과 같은 내용이 표시됩니다.

```powershell
PS C:\> New-AzureResourceGroup -Name $RGName -Location $locName

ResourceGroupName : SparkResourceGroup
Location          : westus
ProvisioningState : Succeeded
Tags              :
Permissions       :
                    Actions  NotActions
                    =======  ==========
                    *

ResourceId        : /subscriptions/2018abc3-dbd9-4437-81a8-bb3cf56138ed/resourceGroups/sparkresourcegroup

PS C:\> New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateParameterFile $templateParameterFile -TemplateFile $templateFile
VERBOSE: 10:08:28 AM - Template is valid.
VERBOSE: 10:08:28 AM - Create template deployment 'SparkTestDeployment'.
VERBOSE: 10:08:37 AM - Resource Microsoft.Resources/deployments 'shared-resources' provisioning status is running
VERBOSE: 10:09:11 AM - Resource Microsoft.Resources/deployments 'shared-resources' provisioning status is succeeded
VERBOSE: 10:09:13 AM - Resource Microsoft.Network/networkInterfaces 'nicsl0' provisioning status is succeeded
VERBOSE: 10:09:16 AM - Resource Microsoft.Network/networkInterfaces 'nic0' provisioning status is succeeded
VERBOSE: 10:09:16 AM - Resource Microsoft.Resources/deployments 'jumpbox-resources' provisioning status is running
VERBOSE: 10:09:24 AM - Resource Microsoft.Compute/virtualMachines 'mastervm0' provisioning status is running
VERBOSE: 10:11:04 AM - Resource Microsoft.Compute/virtualMachines/extensions 'mastervm0/installsparkmaster'
provisioning status is running
VERBOSE: 10:11:04 AM - Resource Microsoft.Compute/virtualMachines 'mastervm0' provisioning status is succeeded
VERBOSE: 10:11:11 AM - Resource Microsoft.Compute/virtualMachines 'slavevm0' provisioning status is running
VERBOSE: 10:11:42 AM - Resource Microsoft.Resources/deployments 'jumpbox-resources' provisioning status is succeeded
VERBOSE: 10:13:10 AM - Resource Microsoft.Compute/virtualMachines 'slavevm0' provisioning status is succeeded
VERBOSE: 10:13:15 AM - Resource Microsoft.Compute/virtualMachines/extensions 'slavevm0/installsparkslave' provisioning
status is running
VERBOSE: 10:16:58 AM - Resource Microsoft.Compute/virtualMachines/extensions 'mastervm0/installsparkmaster'
provisioning status is succeeded
VERBOSE: 10:19:05 AM - Resource Microsoft.Compute/virtualMachines/extensions 'slavevm0/installsparkslave' provisioning
status is succeeded


DeploymentName    : SparkTestDeployment
ResourceGroupName : SparkResourceGroup
ProvisioningState : Succeeded
Timestamp         : 5/5/2015 5:19:05 PM
Mode              : Incremental
TemplateLink      :
Parameters        :
                    Name             Type                       Value
                    ===============  =========================  ==========
                    adminUsername    String                     Paolos
                    adminPassword    SecureString
                    imagePublisher   String                     Canonical
                    imageOffer       String                     UbuntuServer
                    imageSKU         String                     14.04.2-LTS
                    storageAccountName  String                  paolosspark
                    region           String                     West US
                    virtualNetworkName  String                  sparkClustVnet
                    addressPrefix    String                     10.0.0.0/16
										subnetName       String                     Subnet1
                    subnetPrefix     String                     10.0.0.0/24
                    sparkVersion     String                     3.0.0
                    sparkClusterName  String                    spark-arm-cluster
                    sparkNodeIPAddressPrefix  String            10.0.0.1
                    sparkSlaveNodeIPAddressPrefix  String       10.0.0.3
                    jumpbox          String                     enabled
                    tshirtSize       String                     M
```

배포하는 동안과 배포한 후 발생한 모든 오류를 포함하여 프로비전 중에 실행된 모든 요청을 확인할 수 있습니다.

이렇게 하려면 [Azure Preview 포털](https://portal.azure.com)로 이동하여 다음을 수행합니다.

- 왼쪽 탐색 모음에서 **찾아보기**를 클릭한 다음 아래로 스크롤하여 **리소스 그룹**을 클릭합니다.
- 방금 만든 리소스 그룹을 클릭하면 “리소스 그룹" 블레이드가 나타납니다.
- "리소스 그룹" 블레이드의 **모니터링** 파트에서 **이벤트** 막대 그래프를 클릭하면 배포에 대한 이벤트를 확인할 수 있습니다.
- 개별 이벤트를 클릭하면 템플릿을 대신해 수행된 개별 작업에 대한 세부 정보를 볼 수 있습니다.

![portal-events](media/virtual-machines-spark-template/portal-events.png)

테스트 후 이 리소스 그룹과 모든 해당 리소스(저장소 계정, 가상 컴퓨터 및 가상 네트워크)를 제거해야 하는 경우 다음 단일 명령을 사용합니다.

```powershell
Remove-AzureResourceGroup -Name "<resource group name>" -Force
```

### 3b 단계: Azure CLI를 통해 탬플릿을 사용하여 Spark 클러스터 배포

Azure CLI를 통해 Spark 클러스터를 배포하려면 먼저 이름과 위치를 지정하여 리소스 그룹을 만듭니다.

	azure group create SparkResourceGroup "West US"

이 리소스 그룹 이름, JSON 템플릿 파일의 위치, 그리고 매개 변수 파일의 위치(위의Azure PowerShell 섹션에서 자세한 내용 확인)를 다음 명령으로 전달합니다.

	azure group deployment create SparkResourceGroup -f .\azuredeploy.json -e .\azuredeploy-parameters.json

다음 명령을 사용하여 개별 리소스 배포의 상태를 확인할 수 있습니다.

	azure group deployment list SparkResourceGroup

## Spark 템플릿 구조 및 파일 구성 살펴보기

강력하고 재사용 가능한 리소스 관리자 템플릿을 디자인하려면 Spark와 같은 복잡한 솔루션을 배포하는 동안 필요한 상호 관련된 일련의 복잡한 작업을 구성해야 합니다. 관련된 확장을 통해 스크립트를 실행하고 리소스 관리자 템플릿 연결 및 리소스 루핑을 활용하면 거의 모든 복잡한 템플릿 기반 배포에서 재사용할 수 있는 모듈식 방법을 구현할 수 있습니다.

다음 다이어그램에서는 이 배포를 위해 GitHub에서 다운로드한 모든 파일 간의 관계를 설명합니다.

![spark-files](media/virtual-machines-spark-template/spark-files.png)

이 섹션에서는 Spark 클러스터용 azuredeploy.json 파일의 구조를 단계별로 설명합니다.

템플릿 파일의 복사본을 아직 다운로드하지 않은 경우 파일 위치로 로컬 폴더(예: C:\\Azure\\Templates\\Spark)를 지정하여 해당 폴더를 만듭니다. 폴더 이름을 입력하고 다음 명령을 실행합니다.

```powershell
$folderName="<folder name, such as C:\Azure\Templates\Spark>"
$webclient = New-Object System.Net.WebClient
$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/spark-on-ubuntu/azuredeploy.json"
$filePath = $folderName + "\azuredeploy.json"
$webclient.DownloadFile($url,$filePath)
```

텍스트 편집기 또는 선택한 도구에서 azuredeploy.json 템플릿을 엽니다. 다음 정보에 템플릿 파일의 구조와 각 섹션의 용도에 대한 설명을 담고 있습니다. [여기](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/spark-on-ubuntu/azuredeploy.json)서 브라우저를 통해 이 템플릿의 내용을 확인할 수도 있습니다.

### "parameters" 섹션

azuredeploy.json의 "parameters" 섹션은 이 템플릿에 데이터를 입력하는 데 사용되는 매개 변수를 지정합니다. 앞서 언급한 azuredeploy-parameters.json 파일은 템플릿을 실행하는 동안 azuredeploy.json의 "parameters" 섹션으로 값을 전달하는 데 사용됩니다.

“T셔츠 크기"에 대한 매개 변수의 예는 다음과 같습니다.

```json
"tshirtSize": {
    "type": "string",
    "defaultValue": "S",
    "allowedValues": [
        "S",
        "M",
        "L"
    ],
    "metadata": {
        "Description": "T-shirt size of the Spark deployment"
    }
},
```

> [AZURE.NOTE]**defaultValue**와 **allowedValues**도 지정할 수 있습니다.

### "variables" 섹션

"variables" 섹션은 이 템플릿 전체에서 사용할 수 있는 변수를 지정합니다. 이 섹션에는 실행 시 상수 또는 계산된 값으로 설정되는 여러 필드(JSON 데이터 형식 또는 조각)가 포함됩니다. 아래에는 간단한 예제와 복잡한 예제가 모두 나와 있습니다.

```json
"variables": {
	"vmStorageAccountContainerName": "vhd",
	"vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
	"subnetRef": "[concat(variables('vnetID'),'/subnets/',parameters('subnetName'))]",
	"computerNamePrefix": "sparknode",
	"scriptUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/spark-on-ubuntu/",
	"templateUrl": "[variables('scriptUrl')]",
	"sharedTemplateName": "shared-resources",
	"jumpboxTemplateName": "jumpbox-resources-",
	"tshirtSizeS": {
		"numberOfMasterInstances": 1,
		"numberOfSlavesInstances" : 1,
		"vmSize": "Standard_A2"
	},
	"tshirtSizeM": {
		"numberOfMasterInstances": 1,
		"numberOfSlavesInstances" : 4,
		"vmSize": "Standard_A4"
	},
	"tshirtSizeL": {
		"numberOfMasterInstances": 1,
		"numberOfSlavesInstances" : 6,
		"vmSize": "Standard_A7"
	},
	"numberOfMasterInstances": "[variables(concat('tshirtSize', parameters('tshirtSize'))).numberOfMasterInstances]",
	"numberOfSlavesInstances": "[variables(concat('tshirtSize', parameters('tshirtSize'))).numberOfSlavesInstances]",
	"vmSize": "[variables(concat('tshirtSize', parameters('tshirtSize'))).vmSize]"
},
```

**vmStorageAccountContainerName**은 단순한 이름/값 변수의 예입니다. **vnetID**는 **resourceId** 및 **parameters** 함수를 사용하여 런타임에 계산되는 변수의 예입니다. **numberOfMasterInstances** 및 **vmSize** 변수의 값은 **concat**, **variables** 및 **parameters** 함수를 사용하여 런타임에 계산됩니다.

Spark 클러스터 배포의 크기를 사용자 지정하려는 경우 azuredeploy.json 템플릿에서 **tshirtSizeS**, **tshirtSizeM** 및 **tshirtSizeL** 변수의 속성을 변경할 수 있습니다.

템플릿 언어에 대한 자세한 내용은 MSDN의 [Azure 리소스 관리자 템플릿 언어](../resource-group-authoring-templates.md)에서 확인할 수 있습니다.


### "resources" 섹션

"resources" 섹션에서 대부분의 작업이 수행됩니다. 이 섹션 내부를 신중하게 찾으면, 두 가지 다른 경우를 즉시 식별할 수 있습니다. 첫 번째는 기본 일정 내에서 상주하는 배포를 호출하는 형식 `Microsoft.Resources/deployments`의 정의된 요소입니다. 그리고 두 번째는 매개 변수 집합을 입력으로 전달하여 호출하려는 연결된 템플릿 파일을 지정할 수 있도록 하는 **templateLink** 속성 및 관련 **contentVersion** 속성입니다. 이러한 사항은 다음 템플릿 코드 조각에서 확인할 수 있습니다.

```json
"resources": [
{
	"name": "shared-resources",
	"type": "Microsoft.Resources/deployments",
	"apiVersion": "2015-01-01",
	"properties": {
		"mode": "Incremental",
		"templateLink": {
			"uri": "[concat(variables('templateUrl'), variables('sharedTemplateName'), '.json')]",
			"contentVersion": "1.0.0.0"
		},
		"parameters": {
			"region": {
				"value": "[parameters('region')]"
			},
			"storageAccountName": {
				"value": "[parameters('storageAccountName')]"
			},
			"virtualNetworkName": {
				"value": "[parameters('virtualNetworkName')]"
			},
			"addressPrefix": {
				"value": "[parameters('addressPrefix')]"
			},
			"subnetName": {
				"value": "[parameters('subnetName')]"
			},
			"subnetPrefix": {
				"value": "[parameters('subnetPrefix')]"
			}
		}
	}
},
```

이 첫 번째 예제에서는 이 시나리오의 azuredeploy.json이 일종의 오케스트레이션 메커니즘으로 구성되어 여러 다른 템플릿 파일을 호출합니다. 각 파일은 필요한 배포 작업의 일부를 담당합니다.

특히 다음과 같은 연결된 템플릿이 이 배포에 사용됩니다.

-	**shared-resource.json**: 배포에서 공유할 모든 리소스의 정의를 포함합니다. 예제는 VM의 OS 디스크 및 가상 네트워크를 저장하는 데 사용되는 저장소 계정입니다.
-	**jumpbox-resources-enabled.json**: "점프 상자" VM 및 모든 관련 리소스(예: 환경에 대한 SSH를 실행하는 데 사용되는 입력 끝점, 공용 IP 주소, 네트워크 인터페이스)를 배포합니다.

이러한 두 템플릿을 호출한 후 azuredeploy.json은 모든 Spark 클러스터 노드 VM 및 연결된 리소스(예: 네트워크 어댑터, 개인 IP 등)를 프로비전합니다. 또한 이 템플릿은 각 노드에 Spark를 물리적으로 설치 및 설정하기 위해 VM 확장(Linux용 사용자 지정 스크립트)을 배포하고 bash 스크립트(spark-cluster-install.sh)를 호출합니다.

이 마지막 템플릿, 즉 azuredeploy.json은 템플릿 개발 관점에서 가장 흥미로운 항목 중 하나이므로 사용 *방법*을 좀 더 자세히 살펴보겠습니다. 한 가지 중요한 개념은 단일 템플릿 파일에서 단일 리소스 유형의 여러 복사본을 배포하고 각 인스턴스에 대해 필요한 설정의 고유 값을 설정하는 방법입니다. 이 개념을 **리소스 루핑**이라고 합니다.

**copy** 요소를 사용하는 리소스는 **copy** 요소의 **count** 매개 변수에 지정된 횟수만큼 자체적으로 "복사"됩니다. 배포된 리소스의 여러 인스턴스 간에 고유한 값을 지정하는 데 필요한 모든 설정의 경우 **copyindex()** 함수를 사용하여 해당 특정 리소스 루프 만들기에서 현재 인덱스를 나타내는 숫자 값을 가져올 수 있습니다. azuredeploy.json의 다음 조각에서, Spark 클러스터에 대해 생성 중인 여러 네트워크 어댑터, VM 및 VM 확장에 이 개념이 적용된 것을 볼 수 있습니다.

```json
{
	"apiVersion": "2015-05-01-preview",
	"type": "Microsoft.Network/networkInterfaces",
	"name": "[concat('nic', copyindex())]",
	"location": "[parameters('region')]",
	"copy": {
		"name": "nicLoop",
		"count": "[variables('numberOfMasterInstances')]"
	},
	"dependsOn": [
	"[concat('Microsoft.Resources/deployments/', 'shared-resources')]"
	],
	"properties": {
		"ipConfigurations": [
		{
			"name": "ipconfig1",
			"properties": {
				"privateIPAllocationMethod": "Static",
				"privateIPAddress": "[concat(parameters('sparkNodeIPAddressPrefix'), copyindex())]",
				"subnet": {
					"id": "[variables('subnetRef')]"
				}
			}
		}
		]
	}
},
{
	"apiVersion": "2015-05-01-preview",
	"type": "Microsoft.Network/networkInterfaces",
	"name": "[concat('nicsl', copyindex())]",
	"location": "[parameters('region')]",
	"copy": {
		"name": "nicslLoop",
		"count": "[variables('numberOfSlavesInstances')]"
	},
	"dependsOn": [
	"[concat('Microsoft.Resources/deployments/', 'shared-resources')]"
	],
	"properties": {
		"ipConfigurations": [
		{
			"name": "ipconfig1",
			"properties": {
				"privateIPAllocationMethod": "Static",
				"privateIPAddress": "[concat(parameters('sparkSlaveNodeIPAddressPrefix'), copyindex())]",
				"subnet": {
					"id": "[variables('subnetRef')]"
				}
			}
		}
		]
	}
},
{
	"apiVersion": "2015-05-01-preview",
	"type": "Microsoft.Compute/virtualMachines",
	"name": "[concat('mastervm', copyindex())]",
	"location": "[parameters('region')]",
	"copy": {
		"name": "virtualMachineLoopMaster",
		"count": "[variables('numberOfMasterInstances')]"
	},
	"dependsOn": [
	"[concat('Microsoft.Resources/deployments/', 'shared-resources')]",
	"[concat('Microsoft.Network/networkInterfaces/', 'nic', copyindex())]"
	],
	"properties": {
		"availabilitySet": {
			"id": "[resourceId('Microsoft.Compute/availabilitySets', 'sparkCluserAS')]"
		},
		"hardwareProfile": {
			"vmSize": "[variables('vmSize')]"
		},
		"osProfile": {
			"computername": "[concat(variables('computerNamePrefix'), copyIndex())]",
			"adminUsername": "[parameters('adminUsername')]",
			"adminPassword": "[parameters('adminPassword')]",
			"linuxConfiguration": {
				"disablePasswordAuthentication": "false"
			}
		},
		"storageProfile": {
			"imageReference": {
				"publisher": "[parameters('imagePublisher')]",
				"offer": "[parameters('imageOffer')]",
				"sku" : "[parameters('imageSKU')]",
				"version":"latest"
			},
			"osDisk" : {
				"name": "osdisk",
				"vhd": {
					"uri": "[concat('https://',parameters('storageAccountName'),'.blob.core.windows.net/',variables('vmStorageAccountContainerName'),'/','osdisk', copyindex() ,'.vhd')]"
					},
					"caching": "ReadWrite",
					"createOption": "FromImage"
				}
			},
			"networkProfile": {
				"networkInterfaces": [
				{
					"id": "[resourceId('Microsoft.Network/networkInterfaces',concat('nic', copyindex()))]"
				}
				]
			}
		}
	},
	{
		"apiVersion": "2015-05-01-preview",
		"type": "Microsoft.Compute/virtualMachines",
		"name": "[concat('slavevm', copyindex())]",
		"location": "[parameters('region')]",
		"copy": {
			"name": "virtualMachineLoop",
			"count": "[variables('numberOfSlavesInstances')]"
		},
		"dependsOn": [
		"[concat('Microsoft.Resources/deployments/', 'shared-resources')]",
		"[concat('Microsoft.Network/networkInterfaces/', 'nicsl', copyindex())]",
		"virtualMachineLoopMaster"
		],
		"properties": {
			"availabilitySet": {
				"id": "[resourceId('Microsoft.Compute/availabilitySets', 'sparkCluserAS')]"
			},
			"hardwareProfile": {
				"vmSize": "[variables('vmSize')]"
			},
			"osProfile": {
				"computername": "[concat(variables('computerNamePrefix'),'sl', copyIndex())]",
				"adminUsername": "[parameters('adminUsername')]",
				"adminPassword": "[parameters('adminPassword')]",
				"linuxConfiguration": {
					"disablePasswordAuthentication": "false"
				}
			},
			"storageProfile": {
				"imageReference": {
					"publisher": "[parameters('imagePublisher')]",
					"offer": "[parameters('imageOffer')]",
					"sku" : "[parameters('imageSKU')]",
					"version":"latest"
				},
				"osDisk" : {
					"name": "osdisksl",
					"vhd": {
						"uri": "[concat('https://',parameters('storageAccountName'),'.blob.core.windows.net/',variables('vmStorageAccountContainerName'),'/','osdisksl', copyindex() ,'.vhd')]"
					},
					"caching": "ReadWrite",
					"createOption": "FromImage"
				}
			},
			"networkProfile": {
				"networkInterfaces": [
				{
					"id": "[resourceId('Microsoft.Network/networkInterfaces',concat('nicsl', copyindex()))]"
				}
				]
			}
		}
	},
	{
		"type": "Microsoft.Compute/virtualMachines/extensions",
		"name": "[concat('mastervm', copyindex(), '/installsparkmaster')]",
		"apiVersion": "2015-05-01-preview",
		"location": "[parameters('region')]",
		"copy": {
			"name": "virtualMachineExtensionsLoopMaster",
			"count": "[variables('numberOfMasterInstances')]"
		},
		"dependsOn": [
		"[concat('Microsoft.Compute/virtualMachines/', 'mastervm', copyindex())]",
		"[concat('Microsoft.Network/networkInterfaces/', 'nic', copyindex())]"
		],
		"properties": {
			"publisher": "Microsoft.OSTCExtensions",
			"type": "CustomScriptForLinux",
			"typeHandlerVersion": "1.2",
			"settings": {
				"fileUris": [
				"[concat(variables('scriptUrl'), 'spark-cluster-install.sh')]"
				],
				"commandToExecute": "[concat('bash spark-cluster-install.sh -k ',parameters('sparkVersion'),' -d ', reference('nic0').ipConfigurations[0].properties.privateIPAddress,' -s ',variables('numberOfSlavesInstances'),' -m ', ' 1 ')]"
			}
		}
	},
	{
		"type": "Microsoft.Compute/virtualMachines/extensions",
		"name": "[concat('slavevm', copyindex(), '/installsparkslave')]",
		"apiVersion": "2015-05-01-preview",
		"location": "[parameters('region')]",
		"copy": {
			"name": "virtualMachineExtensionsLoopSlave",
			"count": "[variables('numberOfSlavesInstances')]"
		},
		"dependsOn": [
		"[concat('Microsoft.Compute/virtualMachines/', 'slavevm', copyindex())]",
		"[concat('Microsoft.Network/networkInterfaces/', 'nicsl', copyindex())]"
		],
		"properties": {
			"publisher": "Microsoft.OSTCExtensions",
			"type": "CustomScriptForLinux",
			"typeHandlerVersion": "1.2",
			"settings": {
				"fileUris": [
				"[concat(variables('scriptUrl'), 'spark-cluster-install.sh')]"
				],
				"commandToExecute": "[concat('bash spark-cluster-install.sh -k ',parameters('sparkVersion'),' -m ', ' 0 ')]"
			}
		}
	}
```

리소스 만들기의 또 다른 중요한 개념은 **dependsOn** JSON 배열에서 볼 수 있는 것처럼 리소스 간의 종속성 및 우선 순위를 지정하는 기능입니다. 이 특정 템플릿에서는 Spark 클러스터 노드가 먼저 생성되는 공유 리소스 및 **networkInterfaces** 리소스에 종속된다는 것을 확인할 수 있습니다.

또 다른 흥미로운 조각은 **CustomScriptForLinux** VM 확장과 관련된 조각입니다. 이러한 확장은 별도의 리소스 유형으로 설치되며 각 클러스터 노드에 대한 종속성이 적용됩니다. 여기서는 이 확장을 사용하여 각 VM 노드에서 Spark를 설치 및 설정합니다. 이러한 확장을 사용하는 azuredeploy.json 템플릿의 코드 조각을 살펴보겠습니다.

```json
{
	"type": "Microsoft.Compute/virtualMachines/extensions",
	"name": "[concat('mastervm', copyindex(), '/installsparkmaster')]",
	"apiVersion": "2015-05-01-preview",
	"location": "[parameters('region')]",
	"copy": {
		"name": "virtualMachineExtensionsLoopMaster",
		"count": "[variables('numberOfMasterInstances')]"
	},
	"dependsOn": [
		"[concat('Microsoft.Compute/virtualMachines/', 'mastervm', copyindex())]",
		"[concat('Microsoft.Network/networkInterfaces/', 'nic', copyindex())]"
	],
	"properties": {
		"publisher": "Microsoft.OSTCExtensions",
		"type": "CustomScriptForLinux",
		"typeHandlerVersion": "1.2",
		"settings": {
			"fileUris": [
				"[concat(variables('scriptUrl'), 'spark-cluster-install.sh')]"
			],
			"commandToExecute": "[concat('bash spark-cluster-install.sh -k ',parameters('sparkVersion'),' -d ', reference('nic0').ipConfigurations[0].properties.privateIPAddress,' -s ',variables('numberOfSlavesInstances'),' -m ', ' 1 ')]"
		}
	}
},
{
	"type": "Microsoft.Compute/virtualMachines/extensions",
	"name": "[concat('slavevm', copyindex(), '/installsparkslave')]",
	"apiVersion": "2015-05-01-preview",
	"location": "[parameters('region')]",
	"copy": {
		"name": "virtualMachineExtensionsLoopSlave",
		"count": "[variables('numberOfSlavesInstances')]"
	},
	"dependsOn": [
		"[concat('Microsoft.Compute/virtualMachines/', 'slavevm', copyindex())]",
		"[concat('Microsoft.Network/networkInterfaces/', 'nicsl', copyindex())]"
	],
	"properties": {
		"publisher": "Microsoft.OSTCExtensions",
		"type": "CustomScriptForLinux",
		"typeHandlerVersion": "1.2",
		"settings": {
			"fileUris": [
				"[concat(variables('scriptUrl'), 'spark-cluster-install.sh')]"
			],
			"commandToExecute": "[concat('bash spark-cluster-install.sh -k ',parameters('sparkVersion'),' -m ', ' 0 ')]"
		}
	}
}
```

마스터 및 슬레이브 노드 리소스에 대한 확장은 **commandToExecute 속성**에 정의된 다양한 명령을 프로비전 과정에서 실행합니다.

최신 가상 컴퓨터 확장의 JSON 조각을 보면 이 리소스가 가상 컴퓨터 리소스 및 해당 네트워크 인터페이스에 종속된 것을 알 수 있습니다. 이는 이 VM 확장을 실행하기 전에 이 두 리소스를 미리 프로비전해야 함을 나타냅니다. **copyindex()** 함수를 사용하여 각 슬레이브 가상 컴퓨터에 대해 이 단계를 반복합니다.

이 배포에 포함된 다른 파일을 알면 Azure 리소스 관리자 템플릿을 활용하여 모든 기술을 기반으로 하는 다중 노드 솔루션에 대한 복잡한 배포 전략을 구성 및 오케스트레이션하는 데 필요한 모든 세부 정보 및 모범 사례를 이해할 수 있습니다. 필수 사항은 아니지만 다음 다이어그램에 강조 표시된 대로 템플릿 파일을 구성하는 것이 좋습니다.

![spark-template-structure](media/virtual-machines-spark-template/spark-template-structure.png)

기본적으로 이 방법은 다음 사항을 제안합니다.

-	템플릿 연결을 활용하여 하위 템플릿 실행을 호출하는 모든 특정 배포 작업을 위해 핵심 템플릿 파일을 중앙 오케스트레이션 지점으로 정의합니다.
-	다른 모든 특정 배포 작업(저장소 계정, 가상 네트워크 구성 등)에서 공유되는 모든 리소스를 배포할 특정 템플릿 파일을 만듭니다. 이는 주로 공용 인프라 면에서 요구 사항이 유사한 배포 간에 재사용될 수 있습니다.
-	지정된 리소스에 특정한 요구 사항을 충족하는 선택적 리소스 템플릿을 포함합니다.
-	리소스 그룹의 동일한 구성원(클러스터의 노드 등)에 대해 고유한 속성의 여러 인스턴스를 배포하기 위해 리소스 루핑을 활용하는 특정 템플릿을 만듭니다.
-	모든 배포 후 작업(제품 설치, 구성 등)의 경우 스크립트 배포 확장을 활용하고 각 기술에 특정한 스크립트를 만듭니다.

자세한 내용은 [Azure 리소스 관리자 템플릿 언어](../resource-group-authoring-templates.md)를 참조하세요.

## 다음 단계

[템플릿 배포](../resource-group-template-deploy.md)에 대한 자세한 내용을 알아봅니다.

[응용 프로그램 프레임워크](virtual-machines-app-frameworks.md)에 대해 자세히 알아보세요.

[템플릿 배포 문제를 해결](resource-group-deploy-debug.md)합니다.

<!---HONumber=Nov15_HO4-->