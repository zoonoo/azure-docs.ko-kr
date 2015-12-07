<properties
  pageTitle="Ubuntu에 MongoDB 클러스터 만들기 | Microsoft Azure"
  description="Azure PowerShell 또는 Azure CLI를 통해 Azure 리소스 관리자 템플릿을 사용하여 Ubuntu에서 MongoDB 클러스터 만들기"
  services="virtual-machines"
  documentationCenter=""
  authors="scoriani"
  manager="timlt"
  editor="tysonn"
  tags="azure-resource-manager"/>

<tags
  ms.service="virtual-machines"
  ms.workload="multiple"
  ms.tgt_pltfrm="vm-windows"
  ms.devlang="na"
  ms.topic="article"
  ms.date="04/29/2015"
  ms.author="scoriani"/>

# Azure 리소스 관리자 템플릿을 사용하여 Ubuntu에서 MongoDB 클러스터 만들기

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]클래식 배포 모델.


MongoDB는 높은 성능, 고가용성 및 자동 크기 조정을 제공하는 공개 소스 문서 데이터베이스입니다. MongoDB는 기본 제공되는 복제 기능을 활용하여 독립 실행형 데이터베이스 또는 클러스터 내에서 설치할 수 있습니다. 경우에 따라 읽기 용량을 늘리려면 복제를 사용할 수 있습니다. 클라이언트에는 서로 다른 서버에 읽기 및 쓰기 작업을 보내는 기능이 있습니다. 분산된 응용 프로그램을 위한 데이터의 위치 및 가용성을 높이기 위해 다른 데이터 센터에 복사본을 유지할 수도 있습니다. MongoDB를 사용하여 복제는 중복성을 제공하고 데이터 가용성을 높일 수도 있습니다. 다른 데이터베이스 서버에서 데이터의 여러 복사본을 사용하여 복제는 단일 서버 손실로부터 데이터베이스를 보호합니다. 복제를 사용하면 하드웨어 오류 및 서비스 중단에서 복구할 수도 있습니다. 데이터의 추가 복사본을 사용하여 한 복사본은 재해 복구, 보고 또는 백업에 전념할 수 있습니다.

Azure 마켓플레이스에서 이미 제공되는 것 외에도 이제 [Azure PowerShell](../powershell-install-configure.md) 또는 [Azure CLI](../xplat-cli-install.md)를 통해 배포되는 Azure 리소스 관리자 템플릿을 사용하여 Ubuntu VM에 새 MongoDB 클러스터를 쉽게 배포할 수 있습니다.

이 템플릿을 기반으로 새로 배포되는 클러스터에는 다음 다이어그램에 설명된 토폴로지가 구축됩니다. 물론 제공된 템플릿을 사용자 지정하여 다른 토폴리지를 쉽게 실현할 수도 있습니다.

![cluster-architecture](media/virtual-machines-mongodb-template/cluster-architecture.png)

새 MongoDB 클러스터에 배포되고 기타 매개 변수에 기반한 노드 수를 정의할 수 있는 매개 변수를 통해 공용 IP 주소를 포함하는 VM 인스턴스(Jumpbox)는 공용 인터넷에서 클러스터로 연결하는 기능 및 해당 클러스터에 관련된 일종의 관리 작업을 수행할 수 있는 기능을 제공하는 동일한 VNET 내에서도 배포될 수 있습니다. 매개 변수로 사용할 수 있는 다른 옵션에는 중재자 노드를 복제 집합에 추가하는 기능으로, 일반적으로 멤버 수가 짝수인 경우 제안됩니다. MongoDB 복제 토폴로지 및 세부 정보에 대한 자세한 내용은 공식 [MongoDB 설명서](http://docs.mongodb.org/manual/core/replication-introduction/)를 참조하세요.

배포가 완료되면 SSH 포트 22에 구성된 DNS 주소를 사용하여 Jumpbox에 액세스할 수 있습니다.

Azure 리소스 관리자 및 이 배포에 사용할 템플릿과 관련된 자세한 내용을 살펴보기 전에 Azure PowerShell 또는 Azure CLI가 올바르게 구성되어 있는지 확인해야 합니다.

[AZURE.INCLUDE [arm-getting-setup-powershell](../../includes/powershell-preview-inline-include.md)]

[AZURE.INCLUDE [xplat-getting-set-up-arm](../../includes/xplat-getting-set-up-arm.md)]

## 리소스 관리자 템플릿을 사용하여 MongoDB 클러스터 배포

Github 템플릿 리포지토리의 리소스 관리자 템플릿을 사용하여 MongoDB 클러스터를 만들려면 다음 단계를 수행합니다. 각 단계에는 Azure PowerShell 및 Azure CLI에 대한 지침이 모두 포함되어 있습니다.

### 1-a단계: PowerShell을 사용하여 템플릿 파일 다운로드

JSON 템플릿과 기타 관련 파일을 저장할 로컬 폴더를 만듭니다(예: C:\\Azure\\Templates\\MongoDB).

다음 예에서는 로컬 폴더의 폴더 이름을 대체하고 명령을 실행합니다.

    $folderName="C:\Azure\Templates\MongoDB"
    $webclient = New-Object System.Net.WebClient
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/azuredeploy.json"
    $filePath = $folderName + "\azuredeploy.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/azuredeploy-parameters.json"
    $filePath = $folderName + "\azuredeploy-parameters.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/arbiter-resources.json"
    $filePath = $folderName + "\arbiter-resources.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/empty-resources.json"
    $filePath = $folderName + "\empty-resources.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/jumpbox-resources.json"
    $filePath = $folderName + "\jumpbox-resources.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/member-resources-D1.json"
    $filePath = $folderName + "\member-resources-D1.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/member-resources-D2.json"
    $filePath = $folderName + "\member-resources-D2.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/member-resources-D3.json"
    $filePath = $folderName + "\member-resources-D3.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/member-resources-D4.json"
    $filePath = $folderName + "\member-resources-D4.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/member-resources-D11.json"
    $filePath = $folderName + "\member-resources-D11.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/member-resources-D12.json"
    $filePath = $folderName + "\member-resources-D12.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/member-resources-D13.json"
    $filePath = $folderName + "\member-resources-D13.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/member-resources-D14.json"
    $filePath = $folderName + "\member-resources-D14.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/mongodb-ubuntu-install.sh"
    $filePath = $folderName + "\mongodb-ubuntu-install.sh"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/metadata.json"
    $filePath = $folderName + "\metadata.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/shared-resources.json"
    $filePath = $folderName + "\shared-resources.json"
    $webclient.DownloadFile($url,$filePath)  

### 1-b단계: Azure CLI를 사용하여 템플릿 파일 다운로드

다음 예에서는 원하는 Git 클라이언트를 사용하여 전체 템플릿 리포지토리를 복제하는 방법을 보여줍니다.

    git clone https://github.com/Azure/azure-quickstart-templates C:\Azure\Templates

복제가 완료되면 C:\\Azure\\Template 디렉터리에서 mongodb-high-availability 폴더를 찾습니다.

### 2단계: (선택 사항) 템플릿 매개 변수 파악

MongoDB 클러스터와 같은 중요한 솔루션을 배포하는 경우 필요한 여러 설정을 처리할 구성 매개 변수 집합을 지정해야 합니다. 템플릿 정의에서 이러한 매개 변수를 선언하면 외부 파일을 통해 또는 명령줄에서 배포하는 동안 값을 지정할 수 있습니다.

azuredeploy.json 파일 위쪽의 parameters 섹션에서 MongoDB 클러스터를 구성하기 위해 템플릿에 필요한 매개 변수 집합을 찾을 수 있습니다. 다음 예에서는 이 템플릿의 azuredeploy.json 파일에 있는 parameters 섹션을 보여줍니다.

    "parameters": {
      "adminUsername": {
          "type": "string",
          "metadata": {
            "Description": "Administrator user name used when provisioning virtual machines (which also becomes a system user administrator in MongoDB)"
          }
        },
        "adminPassword": {
          "type": "securestring",
          "metadata": {
            "Description": "Administrator password used when provisioning virtual machines (which is also a password for the system administrator in MongoDB)"
          }
        },
        "storageAccountName": {
          "type": "string",
          "defaultValue": "",
          "metadata": {
            "Description": "Unique namespace for the storage account where the Virtual Machine's disks will be placed (this name will be used as a prefix to create one or more storage accounts as per t-shirt size)"
          }
        },
        "region": {
          "type": "string",
          "metadata": {
            "Description": "Location where resources will be provisioned"
          }
        },
        "virtualNetworkName": {
          "type": "string",
          "defaultValue": "mongodbVnet",
          "metadata": {
            "Description": "The arbitrary name of the virtual network provisioned for the MongoDB deployment"
          }
        },
        "subnetName": {
          "type": "string",
          "defaultValue": "mongodbSubnet",
          "metadata": {
            "Description": "Subnet name for the virtual network that resources will be provisioned in to"
          }
        },
        "addressPrefix": {
          "type": "string",
          "defaultValue": "10.0.0.0/16",
          "metadata": {
            "Description": "The network address space for the virtual network"
          }
        },
        "subnetPrefix": {
          "type": "string",
          "defaultValue": "10.0.0.0/24",
          "metadata": {
            "Description": "The network address space for the virtual subnet"
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
            "Description": "The flag allowing to enable or disable provisioning of the jumpbox VM that can be used to access the MongoDB environment"
          }
        },
        "tshirtSize": {
          "type": "string",
          "defaultValue": "XSmall",
          "allowedValues": [
          "XSmall",
          "Small",
          "Medium",
          "Large",
          "XLarge",
          "XXLarge"
          ],
          "metadata": {
            "Description": "T-shirt size of the MongoDB deployment"
          }
        },
        "osFamily": {
          "type": "string",
          "defaultValue": "Ubuntu",
          "allowedValues": [
          "Ubuntu"
          ],
          "metadata": {
            "Description": "The target OS for the virtual machines running MongoDB"
          }
        },
        "mongodbVersion": {
          "type": "string",
          "defaultValue": "3.0.2",
          "metadata": {
            "Description": "The version of the MongoDB packages to be deployed"
          }
        },
        "replicaSetName": {
          "type": "string",
          "defaultValue": "rs0",
          "metadata": {
            "Description": "The name of the MongoDB replica set"
          }
        },
        "replicaSetKey": {
          "type": "string",
          "metadata": {
            "Description": "The shared secret key for the MongoDB replica set"
          }
        }
      },

각 매개 변수에는 데이터 형식, 허용되는 값 등의 세부 정보가 있습니다. 따라서 PowerShell 또는 Azure CLI와 같은 대화형 모드에서 템플릿을 실행하는 동안 전달되는 매개 변수의 유효성을 검사할 수 있으며, 필수 매개 변수 및 해당 설명 목록을 구문 분석하여 동적으로 작성할 수 있는 자체 검색 UI도 사용할 수 있습니다.

### 3-a단계: PowerShell을 사용하여 템플릿을 통해 MongoDB 클러스터 배포

모든 매개 변수에 대한 런타임 값이 포함된 JSON 파일을 만들어 배포용 매개 변수 파일을 준비합니다. 이 파일이 배포 명령에 단일 엔터티로 전달됩니다. 매개 변수 파일을 포함하지 않으면 PowerShell는 템플릿에 지정된 기본값을 사용하며 나머지 값을 입력하라는 메시지를 표시합니다.

다음 예에서는 azuredeploy-parameters.json 파일의 매개 변수 집합을 보여줍니다.

    {
      "adminUsername": {
          "value": "MongoAdmin"
      },
      "adminPassword": {
          "value": ""
      },
      "storageAccountName": {
          "value": ""
      },
      "region": {
          "value": "West US"
      },
      "virtualNetworkName": {
          "value": "mongodbVnet"
      },
      "subnetName": {
          "value": "mongodbSubnet"
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
      "jumpbox": {
          "value": "Disabled"
      },
      "tshirtSize": {
          "value": "XSmall"
      },
      "osFamily": {
          "value": "Ubuntu"
      },
      "mongodbVersion": {
          "value": "3.0.2"
      },
      "replicaSetName": {
          "value": "rs0"
      },
      "replicaSetKey":  {
          "value": ""
      }
    }

Azure 배포 이름, 리소스 그룹 이름, Azure 위치 및 저장된 JSON 배포 파일의 폴더를 입력합니다. 다음 명령을 실행합니다.

    $deployName="<deployment name>"
    $RGName="<resource group name>"
    $locName="<Azure location, such as West US>"
    $folderName="<folder name, such as C:\Azure\Templates\MongoDB>"
    $templateFile= $folderName + "\azuredeploy.json"
    $templateParameterFile= $folderName + "\azuredeploy-parameters.json"

    New-AzureResourceGroup –Name $RGName –Location $locName

    New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateParameterFile $templateParameterFile -TemplateFile $templateFile

**New-AzureResourceGroupDeployment** 명령을 실행하면 JSON 매개 변수 파일에서 매개 변수 값이 추출되고 그에 따라 템플릿 실행이 시작됩니다. 여러 매개 변수 파일을 정의하여 다양한 환경(예: 테스트, 프로덕션 등)에서 사용하면 템플릿 재사용이 향상되고 복잡한 다중 환경 솔루션이 간소화됩니다.

저장소 계정 매개 변수로 제공하는 이름은 고유해야 하며 Azure 저장소 계정의 모든 요구 사항(소문자와 숫자만 사용 가능함)을 충족해야 하므로 배포할 때 새 Azure 저장소 계정을 만들어야 합니다.

배포하는 동안과 배포한 후 발생한 모든 오류를 포함하여 프로비전 중에 실행된 모든 요청을 확인할 수 있습니다.

이렇게 하려면 [Azure Preview 포털](https://portal.azure.com)로 이동하여 다음을 수행합니다.

- 왼쪽 탐색 모음에서 **찾아보기**를 클릭하고 아래로 스크롤하여 **리소스 그룹**을 클릭합니다.
- 방금 만든 리소스 그룹을 클릭하면 리소스 그룹 블레이드가 나타납니다.
- 리소스 그룹 블레이드의 "모니터링" 파트에서 "이벤트" 막대 그래프를 클릭하면 배포에 대한 이벤트를 확인할 수 있습니다. 개별 이벤트를 클릭하면 템플릿을 대신해 수행된 각 개별 작업에 대한 세부 정보를 볼 수 있습니다.

테스트 후 이 리소스 그룹과 모든 해당 리소스(저장소 계정, 가상 컴퓨터 및 가상 네트워크)를 제거해야 하는 경우 다음 명령을 사용합니다.

    Remove-AzureResourceGroup –Name "<resource group name>" -Force

### 3-b단계: Azure CLI를 사용하여 템플릿을 통해 MongoDB 클러스터 배포

Azure CLI를 통해 MongoDB 클러스터를 배포하려면 다음 명령으로 이름과 위치를 지정하여 먼저 리소스 그룹을 만듭니다.

    azure group create mdbc "West US"

이 리소스 그룹 이름, JSON 템플릿 파일의 위치, 그리고 매개 변수 파일의 위치(이전 PowerShell 섹션에서 자세한 내용 확인)를 다음 명령으로 전달합니다.

    azure group deployment create mdbc -f .\azuredeploy.json -e .\azuredeploy-parameters.json

다음 명령을 사용하여 개별 리소스 배포의 상태를 확인할 수 있습니다.

    azure group deployment list mdbc

## MongoDB 템플릿 구조 및 파일 구성 살펴보기

강력하고 재사용 가능한 Azure 리소스 관리자 템플릿을 디자인하려면 MongoDB와 같은 복잡한 솔루션을 배포하는 동안 필요한 상호 관련된 일련의 복잡한 작업을 구성해야 합니다. 관련된 확장을 통해 스크립트를 실행하고 Azure 리소스 관리자 *템플릿 연결* 및 *리소스 루핑*을 활용하면 거의 모든 복잡한 템플릿 기반 배포에서 다시 사용할 수 있는 모듈식 방법을 구현할 수 있습니다.

다음 다이어그램에서는 이 배포를 위해 GitHub에서 다운로드한 모든 파일 간의 관계를 설명합니다.

![mongodb-files](media/virtual-machines-mongodb-template/mongodb-files.png)

이 섹션에서는 MongoDB 클러스터용 azuredeploy.json 파일의 구조를 단계별로 설명합니다.

### Parameters 섹션

azuredeploy.json의 parameters 섹션은 이 템플릿에 데이터를 입력하는 데 사용되는 매개 변수를 지정합니다. 이 항목에서 앞서 설명한 azuredeploy-parameters.json 파일은 템플릿을 실행하는 동안 azuredeploy.json의 parameters 섹션으로 값을 전달하는 데 사용됩니다.

### Variables 섹션

variables 섹션은 이 템플릿 전체에서 사용할 수 있는 변수를 지정합니다. 이 섹션에는 실행 시 상수 또는 계산된 값으로 설정되는 여러 필드(JSON 데이터 형식 또는 조각)가 포함됩니다. 다음 예제에서는 이 MongoDB 템플릿의 variables 섹션을 보여 줍니다.

    "variables": {
          "_comment0": "/* T-shirt sizes may vary for different reasons, and some customers may want to modify these - so feel free to go ahead and define your favorite t-shirts */",
          "tshirtSizeXSmall": {
              "vmSizeMember": "Standard_D1",
              "vmSizeArbiter": "Standard_A1",
              "numberOfMembers": 1,
              "totalMemberCount": 2,
              "arbiter": "Enabled",
              "vmTemplate": "[concat(variables('templateBaseUrl'), 'member-resources-D1.json')]",
              "storageAccountCount": 1,
              "dataDiskSize": 100
          },
          "tshirtSizeSmall": {
              "vmSizeMember": "Standard_D1",
              "vmSizeArbiter": "Standard_A1",
              "numberOfMembers": 2,
              "totalMemberCount": 3,
              "arbiter": "Disabled",
              "vmTemplate": "[concat(variables('templateBaseUrl'), 'member-resources-D1.json')]",
              "storageAccountCount": 1,
              "dataDiskSize": 100
          },
          "tshirtSizeMedium": {
              "vmSizeMember": "Standard_D2",
              "vmSizeArbiter": "Standard_A1",
              "numberOfMembers": 3,
              "totalMemberCount": 4,
              "arbiter": "Enabled",
              "vmTemplate": "[concat(variables('templateBaseUrl'), 'member-resources-D2.json')]",
              "storageAccountCount": 2,
              "dataDiskSize": 250
          },
          "tshirtSizeLarge": {
              "vmSizeMember": "Standard_D2",
              "vmSizeArbiter": "Standard_A1",
              "numberOfMembers": 7,
              "totalMemberCount": 8,
              "arbiter": "Enabled",
              "vmTemplate": "[concat(variables('templateBaseUrl'), 'member-resources-D2.json')]",
              "storageAccountCount": 4,
              "dataDiskSize": 250
          },
          "tshirtSizeXLarge": {
              "vmSizeMember": "Standard_D3",
              "vmSizeArbiter": "Standard_A1",
              "numberOfMembers": 7,
              "totalMemberCount": 8,
              "arbiter": "Enabled",
              "vmTemplate": "[concat(variables('templateBaseUrl'), 'member-resources-D3.json')]",
              "storageAccountCount": 4,
              "dataDiskSize": 500
          },
          "tshirtSizeXXLarge": {
              "vmSizeMember": "Standard_D3",
              "vmSizeArbiter": "Standard_A1",
              "numberOfMembers": 15,
              "totalMemberCount": 16,
              "arbiter": "Disabled",
              "vmTemplate": "[concat(variables('templateBaseUrl'), 'member-resources-D3.json')]",
              "storageAccountCount": 8,
              "dataDiskSize": 500
          },
          "osFamilyUbuntu": {
              "osName": "ubuntu",
              "installerBaseUrl": "http://repo.mongodb.org/apt/ubuntu",
              "installerPackages": "mongodb-org",
              "imagePublisher": "Canonical",
              "imageOffer": "UbuntuServer",
              "imageSKU": "14.04.2-LTS"
          },
          "vmStorageAccountContainerName": "vhd-mongodb",
          "vmStorageAccountDomain": ".blob.core.windows.net",
          "vnetID": "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworkName'))]",
          "sharedScriptUrl": "[concat('https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/shared_scripts/', variables('osFamilySpec').osName, '/')]",
          "scriptUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-high-availability/",
          "templateBaseUrl": "[variables('scriptUrl')]",
          "jumpboxTemplateEnabled": "jumpbox-resources.json",
          "jumpboxTemplateDisabled": "empty-resources.json",
          "arbiterTemplateEnabled": "arbiter-resources.json",
          "arbiterTemplateDisabled": "empty-resources.json",
          "sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
          "jumpboxTemplateUrl": "[concat(variables('templateBaseUrl'), variables(concat('jumpboxTemplate', parameters('jumpbox'))))]",
          "arbiterTemplateUrl": "[concat(variables('templateBaseUrl'), variables(concat('arbiterTemplate', variables('clusterSpec').arbiter)))]",
          "commonSettings": {
              "availabilitySetName": "mongodbAvailSet",
              "region": "[parameters('region')]"
          },
          "storageSettings": {
              "vhdStorageAccountName": "[parameters('storageAccountName')]",
              "vhdContainerName": "[variables('vmStorageAccountContainerName')]",
              "destinationVhdsContainer": "[concat('https://', parameters('storageAccountName'), variables('vmStorageAccountDomain'), '/', variables('vmStorageAccountContainerName'), '/')]",
              "storageAccountCount": "[variables('clusterSpec').storageAccountCount]"
          },
          "networkSettings": {
              "virtualNetworkName": "[parameters('virtualNetworkName')]",
              "addressPrefix": "[parameters('addressPrefix')]",
              "subnetName": "[parameters('subnetName')]",
              "subnetPrefix": "[parameters('subnetPrefix')]",
              "subnetRef": "[concat(variables('vnetID'), '/subnets/', parameters('subnetName'))]",
              "machineIpPrefix": "[parameters('nodeAddressPrefix')]"
          },
          "machineSettings": {
              "adminUsername": "[parameters('adminUsername')]",
              "adminPassword": "[parameters('adminPassword')]",
              "machineNamePrefix": "mongodb-",
              "osImageReference": {
                  "publisher": "[variables('osFamilySpec').imagePublisher]",
                  "offer": "[variables('osFamilySpec').imageOffer]",
                  "sku": "[variables('osFamilySpec').imageSKU]",
                  "version": "latest"
              }
          },
          "clusterSpec": "[variables(concat('tshirtSize', parameters('tshirtSize')))]",
          "osFamilySpec": "[variables(concat('osFamily', parameters('osFamily')))]",
          "installCommand": "[concat('bash mongodb-', variables('osFamilySpec').osName, '-install.sh', ' -i ', variables('osFamilySpec').installerBaseUrl, ' -b ', variables('osFamilySpec').installerPackages, ' -r ', parameters('replicaSetName'), ' -k ', parameters('replicaSetKey'), ' -u ', parameters('adminUsername'), ' -p ', parameters('adminPassword'), ' -x ', variables('networkSettings').machineIpPrefix, ' -n ', variables('clusterSpec').totalMemberCount)]",
          "vmScripts": {
              "scriptsToDownload": [
                  "[concat(variables('scriptUrl'), 'mongodb-', variables('osFamilySpec').osName, '-install.sh')]",
                  "[concat(variables('sharedScriptUrl'), 'vm-disk-utils-0.1.sh')]"
              ],
              "regularNodeInstallCommand": "[variables('installCommand')]",
              "lastNodeInstallCommand": "[concat(variables('installCommand'), ' -l')]",
              "arbiterNodeInstallCommand": "[concat(variables('installCommand'), ' -a')]"
          },
          "_comment1": "/* The list of values below helps partition VM disks across multiple storage accounts to achieve a better throughput */",
          "_comment2": "/* Feel free to modify the default allocations if you are comfortable and understand what you are doing */",
          "storageAccountForXSmall_0": "0",
          "storageAccountForXSmall_1": "0",
          "storageAccountForSmall_0": "0",
          "storageAccountForSmall_1": "0",
          "storageAccountForSmall_2": "0",
          "storageAccountForMedium_0": "0",
          "storageAccountForMedium_1": "0",
          "storageAccountForMedium_2": "0",
          "storageAccountForMedium_3": "0",
          "storageAccountForLarge_0": "0",
          "storageAccountForLarge_1": "1",
          "storageAccountForLarge_2": "2",
          "storageAccountForLarge_3": "3",
          "storageAccountForLarge_4": "0",
          "storageAccountForLarge_5": "1",
          "storageAccountForLarge_6": "2",
          "storageAccountForLarge_7": "3",
          "storageAccountForXLarge_0": "0",
          "storageAccountForXLarge_1": "1",
          "storageAccountForXLarge_2": "2",
          "storageAccountForXLarge_3": "3",
          "storageAccountForXLarge_4": "0",
          "storageAccountForXLarge_5": "1",
          "storageAccountForXLarge_6": "2",
          "storageAccountForXLarge_7": "3",
          "storageAccountForXXLarge_0": "0",
          "storageAccountForXXLarge_1": "1",
          "storageAccountForXXLarge_2": "2",
          "storageAccountForXXLarge_3": "3",
          "storageAccountForXXLarge_4": "4",
          "storageAccountForXXLarge_5": "5",
          "storageAccountForXXLarge_6": "6",
          "storageAccountForXXLarge_7": "7",
          "storageAccountForXXLarge_8": "0",
          "storageAccountForXXLarge_9": "1",
          "storageAccountForXXLarge_10": "2",
          "storageAccountForXXLarge_11": "3",
          "storageAccountForXXLarge_12": "4",
          "storageAccountForXXLarge_13": "5",
          "storageAccountForXXLarge_14": "6",
          "storageAccountForXXLarge_15": "7"
      },

위 예제에서 두 가지 방법을 볼 수 있습니다. 이 첫 번째 조각에서 "osFamilyUbuntu" 변수는 6개의 키 값 쌍이 있는 JSON 요소로 설정됩니다.

    "osFamilyUbuntu": {
      "osName": "ubuntu",
      "installerBaseUrl": "http://repo.mongodb.org/apt/ubuntu",
      "installerPackages": "mongodb-org",
      "imagePublisher": "Canonical",
      "imageOffer": "UbuntuServer",
      "imageSKU": "14.04.2-LTS"
    },

이 두 번째 코드 조각에서 "vmScripts" 변수는 템플릿 언어 함수(concat) 및 다른 변수 값과 문자열 상수를 사용하여 런타임에 단일 요소가 계산되는 JSON 배열에 할당됩니다.

    "vmScripts": {
      "scriptsToDownload": [
      "[concat(variables('scriptUrl'), 'mongodb-', variables('osFamilySpec').osName, '-install.sh')]",
      "[concat(variables('sharedScriptUrl'), 'vm-disk-utils-0.1.sh')]"
    ],

이 템플릿에서 중요한 개념 중 하나는 MongoDB 클러스터에 대 해 다른 “티셔츠 크기”가 정의되는 방법입니다. 이러한 “tshirtSizeXXXX” 변수 중 하나를 살펴보면 클러스터 배포 방법의 중요한 특징을 설명한다는 것을 알 수 있습니다. 다음 예제에서는 중간 크기를 예로 살펴보겠습니다.

    "tshirtSizeMedium": {
      "vmSizeMember": "Standard_D2",
      "vmSizeArbiter": "Standard_A1",
      "numberOfMembers": 3,
      "totalMemberCount": 4,
      "arbiter": "Enabled",
      "vmTemplate": "[concat(variables('templateBaseUrl'), 'member-resources-D2.json')]",
      "storageAccountCount": 2,
      "dataDiskSize": 250
    },

"중간" MongoDB 클러스터는 3개의 MongoDB 노드 호스팅 데이터에 대한 VM 크기로 D2를 사용하며 네 번째 A1 VM이 복제를 목적으로 중재자로 사용됩니다. 데이터 노드를 배포하기 위해 호출되는 해당 하위 템플릿은 `member-resources-D2.json`이 되며 데이터 파일(각각 250GB)은 두 개의 저장소 계정에 저장됩니다. 이 변수는 노드 배포 및 기타 작업을 오케스트레이션하기 위해 resources 섹션에서 사용됩니다.

### Resources 섹션

resources 섹션은 대부분의 작업이 발생하는 곳입니다. 이 섹션을 자세히 살펴보면 두 가지 경우를 즉시 확인할 수 있습니다. 첫 번째는 기본적으로 기본 배포 내 중첩된 배포 호출을 의미하는 `Microsoft.Resources/deployments` 형식으로 정의된 요소입니다. 다음 예제에서 볼 수 있듯이, "templateLink" 요소(및 관련 버전 속성)를 통해서는 호출할 연결된 템플릿 파일을 지정할 수 있습니다. 이 파일은 매개 변수 집합을 입력으로 전달합니다.

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

이전 예제에서는 이 시나리오의 azuredeploy.json이 일종의 오케스트레이션 메커니즘으로 구성되어 각각 필요한 배포 활동의 일부를 담당하는 여러 템플릿 파일을 호출합니다.

특히 다음과 같은 연결된 템플릿이 이 배포에 사용됩니다.

-	**shared-resource.json**: 배포에서 공유할 모든 리소스의 정의를 포함합니다. 예제는 VM의 OS 디스크 및 가상 네트워크를 저장하는 데 사용되는 저장소 계정입니다.
-	**Jumpbox-resources.json**: 사용하도록 설정하면 Jumpbox VM에 관련된 모든 리소스 배포를 수행합니다. 공용 IP 주소는 공용 네트워크에서 MongoDB 클러스터에 액세스하는 데 사용할 수 있습니다.
-	**Arbiter-resources.json**: 사용하도록 설정하면 이 템플릿은 MongoDB 클러스터에 중재자 멤버를 배포합니다. 중재자는 데이터를 포함하지 않지만 복제 집합이 주요 선택을 관리하기 위해 노드 수를 짝수로 포함할 때 사용됩니다.
-	**Member-resources-Dx.json**: MongoDB 노드를 효과적으로 배포하는 리소스 템플릿을 지정합니다. 특정 파일은 선택한 티셔츠 크기 정의에 기반하여 사용되며, 각 파일은 각 노드에 연결된 디스크 수에 따라 다릅니다.
-	**Mongodb-ubuntu-install.sh**: 클러스터의 모든 노드에서 CustomScriptForLinux 확장에 의해 호출되는 bash 스크립트 파일입니다. 데이터 디스크 탑재와 서식 및 노드에 MongoDB 비트 설치를 담당합니다.

MongoDB 클러스터를 배포하려면 복제 집합을 올바르게 설정하기 위해 특정 논리가 필요합니다. 다음 예제에서는 배포하는 동안 사용해야 하는 특정 순서를 보여줍니다.

데이터 멤버 배포(병렬로) => 마지막 데이터 멤버 배포 ==> (선택 사항) 중재자 배포

이 순서에서 여러 데이터 노드 배포는 마지막 노드를 제외하고 병렬로 발생합니다. 여기서 클러스터가 형성되고 새 복제 집합이 배포되므로 모든 이전 노드는 해당 순간이 되기 전에 실행되어야 합니다. 마지막 단계는 선택적 중재자 노드를 배포하는 것입니다(이 단계가 필요한 지점은 해당 티셔츠 크기에만 해당).

다음 예제에서는 기본 템플릿(azuredeploy.json)을 다시 살펴보고 모든 데이터 멤버에서 시작하여 이 논리가 어떻게 구현되는지 알아보겠습니다.

    {
      "type": "Microsoft.Resources/deployments",
      "name": "[concat('member-resources', copyindex())]",
      "apiVersion": "2015-01-01",
      "dependsOn": [
        "[concat('Microsoft.Resources/deployments/', 'shared-resources')]"
      ],
      "copy": {
        "name": "memberNodesLoop",
        "count": "[variables('clusterSpec').numberOfMembers]"
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
            "value": {
              "vhdStorageAccountName": "[concat(variables('storageSettings').vhdStorageAccountName, variables(concat('storageAccountFor', parameters('tshirtSize'), '_', copyindex())))]",
                              "vhdContainerName": "[variables('storageSettings').vhdContainerName]",
                              "destinationVhdsContainer": "[concat('https://', variables('storageSettings').vhdStorageAccountName, variables(concat('storageAccountFor', parameters('tshirtSize'), '_', copyindex())), variables('vmStorageAccountDomain'), '/', variables('storageSettings').vhdContainerName, '/')]"
            }
          },
          "networkSettings": {
            "value": "[variables('networkSettings')]"
          },
          "machineSettings": {
            "value": {
              "adminUsername": "[variables('machineSettings').adminUsername]",
                              "adminPassword": "[variables('machineSettings').adminPassword]",
                              "machineNamePrefix": "[variables('machineSettings').machineNamePrefix]",
                              "osImageReference": "[variables('machineSettings').osImageReference]",
                              "vmSize": "[variables('clusterSpec').vmSizeMember]",
                              "dataDiskSize": "[variables('clusterSpec').dataDiskSize]",
                              "machineIndex": "[copyindex()]",
                              "vmScripts": "[variables('vmScripts').scriptsToDownload]",
                              "commandToExecute": "[variables('vmScripts').regularNodeInstallCommand]"
            }
          }
        }
      }
    },

한 가지 중요한 개념은 단일 리소스 유형의 여러 복사본을 배포하고 각 인스턴스에 대해 필요한 설정의 고유 값을 설정하는 방법입니다. 이 개념은 *리소스 루핑*이라고 합니다.

이전 예제에서 매개 변수(클러스터에서 배포할 노드 수)는 변수("numberOfMembers") 설정에 사용되며, 이 변수는 **"copy"** 요소로 전달되어 각각 클러스터의 각 멤버에 대한 템플릿 인스턴스를 생성하는 많은(루프) 자식 배포를 트리거합니다. 인스턴스 간에 고유한 값을 지정하는 데 필요한 모든 설정을 설정하려면 **copyindex()** 함수를 사용하여 해당 특정 리소스 루프 만들기에서 현재 인덱스를 나타내는 숫자 값을 가져올 수 있습니다.

리소스 만들기의 또 다른 중요한 개념은 **dependsOn** JSON 배열에서 알 수 있는 것처럼 리소스 간의 종속성 및 우선 순위를 지정하는 기능입니다. 이 특정 템플릿에서 각각의 노드 배포는 **공유 리소스**의 성공적인 이전 배포에 따라 다릅니다.

연결된 디스크는 mongodb-ubuntu-install.sh 스크립트 파일의 실행을 통해 트리거되는 노드 준비 작업의 일부로 포맷됩니다. 실제로 해당 파일 내에서 다음 호출의 인스턴스를 찾을 수 있습니다.

    bash ./vm-disk-utils-0.1.sh -b $DATA_DISKS -s

vm-disk-utils-0.1.sh는 azure-quickstart-tempates Github 리포지토리 내 shared\_scripts\\ubuntu 폴더의 일부이며, 템플릿 만들기의 일부로 유사한 작업을 실행하는 데 필요할 때마다 재사용할 수 있는 디스크 탑재, 포맷 및 스트라이프에 매우 유용한 함수를 포함하고 있습니다.

또 다른 흥미로운 조각은 CustomScriptForLinux VM 확장과 관련된 조각입니다. 이러한 확장은 별도의 리소스 유형으로 설치되며 각 클러스터 노드 배포 템플릿에 대한 종속성이 적용됩니다. 예를 들어 각 member-resources-Dx.json 파일의 끝에서 다음 조각을 확인하세요.

    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "[concat('vmMember', parameters('machineSettings').machineIndex, '/installmongodb')]",
      "apiVersion": "2015-05-01-preview",
      "location": "[parameters('commonSettings').region]",
      "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', 'vmMember', parameters('machineSettings').machineIndex)]"
      ],
      "properties": {
        "publisher": "Microsoft.OSTCExtensions",
        "type": "CustomScriptForLinux",
        "typeHandlerVersion": "1.2",
        "settings": {
          "fileUris": "[parameters('machineSettings').vmScripts]", "commandToExecute":"[parameters('machineSettings').commandToExecute]"
        }
      }
    }

이 배포에 포함된 다른 파일을 알면 Azure 리소스 관리자 템플릿을 활용하여 모든 기술을 기반으로 하는 다중 노드 솔루션에 대한 복잡한 배포 전략을 구성 및 오케스트레이션하는 데 필요한 모든 세부 정보 및 모범 사례를 이해할 수 있습니다. 필수 사항은 아니지만 다음 다이어그램에 표시된 대로 템플릿 파일을 구성하는 것이 좋습니다.

![mongodb-template-structure](media/virtual-machines-mongodb-template/mongodb-template-structure.png)

기본적으로 이 방법은 다음 사항을 제안합니다.

-	템플릿 연결을 활용하여 하위 템플릿 실행을 호출하는 모든 특정 배포 작업을 위해 핵심 템플릿 파일을 중앙 오케스트레이션 지점으로 정의합니다.
-	다른 모든 특정 배포 작업(예: 저장소 계정, vnet 구성 등)에서 공유되는 모든 리소스를 배포할 특정 템플릿 파일을 만듭니다. 이는 주로 공용 인프라 면에서 요구 사항이 유사한 배포 간에 재사용될 수 있습니다.
-	지정된 리소스에 특정한 요구 사항을 충족하는 선택적 리소스 템플릿을 포함합니다.
-	리소스 그룹의 동일한 구성원(클러스터의 노드 등)에 대해 고유한 속성의 여러 인스턴스를 배포하기 위해 리소스 루핑을 활용하는 특정 템플릿을 만듭니다.
-	모든 배포 후 작업(예: 제품 설치, 구성 등)의 경우 스크립트 배포 확장을 활용하고 각 기술에 특정한 스크립트를 만듭니다.

자세한 내용은 [Azure 리소스 관리자 템플릿 언어](../resource-group-authoring-templates.md)를 참조하세요.

<!---HONumber=AcomDC_1125_2015-->