<properties
	pageTitle="가상 컴퓨터 규모 집합의 크기를 자동으로 조정 | Microsoft Azure"
	description="첫 번째 Azure 가상 컴퓨터 규모 집합 생성 및 관리 시작하기"
	services="virtual-machines"
	documentationCenter=""
	authors="gbowerman"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/11/2015"
	ms.author="guybo"/>

# 가상 컴퓨터 규모 집합에서 자동으로 컴퓨터 규모 조정

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-machines-create-windows-powershell-service-manager.md).


이 문서는 공개 미리 보기 기간에 Azure 가상 컴퓨터 규모 집합을 생성하고 관리하는 작업을 시작하도록 안내하는 빠른 가이드입니다.

이 가이드에서는 사용자가 Azure 리소스 관리자(ARM) 템플릿을 사용하여 가상 컴퓨터 및 VNET 같은 Azure 리소스를 배포하는 것에 익숙하다고 가정합니다. 그렇지 않은 경우를 위해, 리소스 섹션의 처음 3개 링크에 리소스 관리자 개요가 제공되어 있습니다.

## VM 규모 집합은 무엇이며 왜 사용해야 하는가?

VM 규모 집합은 동일한 VM 집합을 배포하고 관리하는데 사용할 수 있는 Azure 계산 리소스입니다.

모든 VM이 동일하게 설정되는 VM 규모 집합은 자동 크기 조정을 충실하게 지원하도록 설계되었습니다. VM 사전 프로비전이 필요하지 않으며, 큰 계산, 빅 데이터, 컨테이너식 워크로드, 계산 리소스의 크기를 확장하거나 축소할 필요가 있는 모든 응용 프로그램을 대상으로 하는 대규모 서비스를 쉽게 구축할 수 있도록 합니다. 크기 조정 작업은 장애 도메인 및 업데이트 도메인 간에 암시적으로 균형이 조정됩니다. VM 규모 집합에 대한 소개는 최신 [Azure 블로그 공지](https://azure.microsoft.com/blog/azure-vm-scale-sets-public-preview)를 참조하세요.

## VM 규모 집합 생성 및 작업

VM 규모 집합은 개별적인 Azure 리소스 관리자 VM과 마찬가지로 JSON 템플릿 및 REST 호출을 사용하여 정의하고 배포할 수 있습니다. 따라서 표준 Azure 리소스 관리자 배포 방법이라면 어떤 방법이든 사용할 수 있고 이 문서에 몇 가지 예가 소개되어 있습니다.

스크립트 언어 및 프로그래밍 언어 명령문 지원과 같은 구체적인 VMSS 통합 분야 및 완전한 포털 통합은 개발 중이며 미리 보기 기간 중에 단계적으로 릴리스될 예정입니다.

## Azure 포털에서 VM 규모 집합 배포 및 관리

초기에는 Azure 포털에서 VM 규모 집합을 처음부터 만들 수 없습니다. 포털 지원은 준비 단계에 있으며 이 문서의 첫 번째 단계에서는 이미 만들어 놓은 규모 집합은 포털에서 볼 수만 있습니다. 규모 집합은 템플릿/스크립트 방식을 사용하여 만들어야 합니다. 모든 경우 “포털"은 새 Azure 포털 즉, [https://portal.azure.com/](https://portal.azure.com/)을 의미하는 것으로 가정합니다.

하지만 규모 집합을 비롯한 리소스를 배포하는 포털의 템플릿 배포 기능은 사용할 수 있습니다. 간단한 규모 집합을 쉽게 배포하는 방법은 다음과 같이 링크를 사용하는 것입니다.

_https://portal.azure.com/#create/Microsoft.Template/uri/<link to VM Scale Set JSON template>_

VM 규모 집합에 대한 예제 템플릿을 Azure 빠른 시작 템플릿 GitHub 리포지토리 [https://github.com/Azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates)에서 찾을 수 있습니다. 제목에 _vmss_가 있는 템플릿을 찾아보세요.

이 템플릿의 추가 정보에는 포털 배포 기능으로 연결되는 단추가 있습니다.

규모 집합을 배포하려면 단추를 클릭하고 포털에 필요한 매개 변수를 채웁니다. 참고: 리소스에서 대문자 또는 대/소문자 혼용이 지원되는지 확실하지 않으면 항상 소문자 매개 변수 값을 사용하는 것이 안전합니다.

**포털/리소스 탐색기에서 VM 규모 집합 리소스 모니터링**

규모 집합을 배포한 후에는 포털에 기본 보기가 표시되지만 초기 미리 보기 기간 동안은 자세한 속성이 표시되지 않거나 VM 규모 집합에서 실행되는 VM을 드릴다운하도록 허용되지 않습니다.

완전한 포털 통합이 준비될 때까지 [https://resources.azure.com](https://resources.azure.com)에서 **Azure 리소스 탐색기**를 사용하여 VM 규모 집합을 보는 것이 좋습니다. VM 규모 집합은 Microsoft.Compute의 리소스이므로 이 사이트에서 다음 링크를 확장하여 VM 규모 집합을 볼 수 있습니다.

구독 -> 사용자의 구독 -> resourceGroups -> 공급자 -> Microsoft.Compute -> virtualMachineScaleSets -> 사용자의 VM 규모 집합 -> 등등

## PowerShell을 사용하여 VM 규모 집합 배포 및 관리

Azure PowerShell 최신 버전을 사용하여 VMSS 템플릿과 쿼리 리소스를 배포할 수 있습니다.

**중요:** 아래 예제는 [Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0-pre/)용이며 다수의 명령에 _AzureRm_ 접두사가 붙습니다. PowerShell 0.9.8과 같이 이전 버전을 사용하는 경우 예제 명령에서 "**Rm**"을 제거하세요. 이 예제는 사용자의 PowerShell 환경(_Login-AzureRmAccount_)에 Azure에 대한 로그인 연결이 이미 설정되어 있다고 가정합니다.

예제:

&#35; **리소스 그룹 만들기**

New-AzureRmResourcegroup -name myrg -location 'Southeast Asia'

&#35; **VM이 2개인 VM 규모 집합 만들기**

New-AzureRmResourceGroupDeployment -name dep1 -vmSSName myvmss -instanceCount 3 -ResourceGroupName myrg -TemplateUri [https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-linux-nat/azuredeploy.json](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-linux-nat/azuredeploy.json)

누락된 매개 변수(이 예제의 경우 위치, 사용자 이름, 암호)를 묻는 프롬프트 메시지가 표시됩니다.

&#35; **VM 규모 집합 세부 정보 표시**

Get-AzureRmResource -name myvmss -ResourceGroupName myrg -ResourceType Microsoft.Compute/virtualMachineScaleSets -ApiVersion 2015-06-15

&#35; 보다 자세한 내용을 보려면 세로줄 문자(|)를 사용하고, | ConvertTo-Json -Depth 10

&#35; 더 자세한 내용을 표시하려면 명령에 –debug를 추가합니다.

&#35; 규모 확장 또는 축소

New-AzureRmResourceGroupDeployment -name dep2 -vmSSName myvmss -instanceCount 2 -ResourceGroupName myrg –TemplateUri [https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-scale-in-or-out/azuredeploy.json](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-scale-in-or-out/azuredeploy.json)

&#35; **규모 집합 제거**

&#35; 간편: 전체 리소스 그룹 및 그에 포함된 것을 모두 제거합니다.

Remove-AzureRmResourceGroup -Name myrg

&#35; 정확: 특정 리소스를 제거합니다.

Remove-AzureRmResource -Name myvmss -ResourceGroupName myrg -ApiVersion 2015-06-15 -ResourceType Microsoft.Compute/virtualMachineScaleSets

### 규모 집합에 대한 PowerShell 명령

앞으로 제공될 Azure PowerShell 빌드에는 템플릿을 사용하지 않고 VM 규모 집합을 만들고 관리하는 명령 집합이 포함될 예정입니다. 이 빌드는 [https://github.com/AzureRT/azure-powershell/releases](https://github.com/AzureRT/azure-powershell/releases)에 미리 보기로 제공됩니다. 명령형 API를 사용하는 방법에 대한 예제는 아래에서 찾을 수 있습니다.

[https://github.com/huangpf/azure-powershell/blob/vmss/src/ResourceManager/Compute/Commands.Compute.Test/ScenarioTests/VirtualMachineScaleSetTests.ps1](https://github.com/huangpf/azure-powershell/blob/vmss/src/ResourceManager/Compute/Commands.Compute.Test/ScenarioTests/VirtualMachineScaleSetTests.ps1)

## Azure CLI를 사용하여 VM 규모 집합 배포 및 관리

Azure CLI 최신 버전을 사용하여 VMSS 템플릿과 쿼리 리소스를 배포할 수 있습니다.

CLI를 설치하는 가장 쉬운 방법은 Docker 컨테이너를 통해 진행하는 것입니다. 설치에 대한 내용은 [https://azure.microsoft.com/blog/run-azure-cli-as-a-docker-container-avoid-installation-and-setup/](https://azure.microsoft.com/blog/run-azure-cli-as-a-docker-container-avoid-installation-and-setup/)을 참조하세요.

CLI 사용에 대한 내용은 [https://azure.microsoft.com/documentation/articles/xplat-cli/](https://azure.microsoft.com/documentation/articles/xplat-cli/)를 참조하세요.

### VM 규모 집합 CLI 예제

&#35; **리소스 그룹 만들기**

azure group create myrg "Southeast Asia"

&#35; **VM 규모 집합 만들기**

azure group deployment create -g myrg -n dep2 --template-uri [https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-linux-nat/azuredeploy.json](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-linux-nat/azuredeploy.json)

&#35; 이를 통해 동적으로 매개 변수를 요청 받거나 매개 변수를 인수로 지정할 수 있습니다.

&#35; **규모 집합 세부 정보 표시**

azure resource show -n vmssname -r Microsoft.Compute/virtualMachineScaleSets -o 2015-06-15 -g myrg

&#35; 또는 자세한 내용 표시:

azure resource show –n vmssname –r Microsoft.Compute/virtualMachineScaleSets –o 2015-06-15 –g myrg –json –vv

앞으로 제공될 Azure CLI 빌드에는 템플릿을 사용하지 않고 VM 규모 집합을 직접적으로 배포하고 관리하는 명령과 VM 규모 집합에서 VM 작업을 수행하는 명령이 포함될 예정입니다. 이 빌드는 [https://github.com/AzureRT/azure-xplat-cli/releases/](https://github.com/AzureRT/azure-xplat-cli/releases/)에서 추적할 수 있습니다.

## VM 규모 집합 템플릿

이 섹션은 VM 규모 집합을 만드는 간단한 Azure 템플릿을 소개하고, 단일 인스턴스 가상 컴퓨터를 만드는데 사용되는 템플릿과 대조합니다. VM 규모 집합 템플릿에 대한 유용한 분석 동영상이 [https://channel9.msdn.com/Blogs/Windows-Azure/VM-Scale-Set-Template-Dissection/player](https://channel9.msdn.com/Blogs/Windows-Azure/VM-Scale-Set-Template-Dissection/player)에 준비되어 있습니다.

### 템플릿 분석

Ubuntu 가상 컴퓨터의 VM 규모 집합을 만드는 간단한 템플릿에서 시작해서 구성 요소까지 자세히 살펴보겠습니다. 이 예제는 VM 규모 집합이 종속되는 VNET, 저장소 계정을 비롯한 리소스를 만듭니다. 예제는 [https://raw.githubusercontent.com/gbowerman/azure-myriad/master/vmss-ubuntu-vnet-storage.json](https://raw.githubusercontent.com/gbowerman/azure-myriad/master/vmss-ubuntu-vnet-storage.json)에서 찾을 수 있습니다. 이 "hello world" 예제의 경우 VM에 내부 IP 주소가 할당되기 때문에 VNET 외부에서 VM 규모 집합의 VM에 바로 연결할 방법이 없습니다. 부하 분산 장치 또는 점프 상자를 사용하여 외부에서 연결하는 방법을 보려면 [Azure 퀵 스타트 템플릿](https://github.com/Azure/azure-quickstart-templates)에서 시나리오 섹션과 템플릿을 참조하세요.

### 템플릿 헤더

스키마 및 콘텐츠 버전을 지정합니다.

{

   "$schema": "http://schema.management.azure.com/schemas/2015-01-01-preview/deploymentTemplate.json",

   "contentVersion": "1.0.0.0",

### 매개 변수

Azure 리소스 관리자(ARM) 템플릿에서와 마찬가지로, 이 섹션은 예제에 포함된 VM 규모 집합의 이름, 시작할 VM 인스턴스의 수, 저장소 계정을 만들 때 사용되는 고유 문자열(대소문자가 어떻게 처리되는지 모르는 경우에는 저장소 계정 같은 개체의 값을 입력할 때 항상 소문자를 사용합니다.)을 비롯하여 배포 시 지정되는 매개 변수를 정의합니다.

````
 "parameters": {

    "vmSSName": {

      "type": "string",

      "metadata": {

        "description": "Scale Set name, also used in this template as a base for naming resources (hence limited less than 10 characters)."

      },

      "maxLength": 10

    },

    "instanceCount": {

      "type": "int",

      "metadata": {

        "description": "Number of VM instances"

      },

      "maxValue": 100

    },

    "adminUsername": {

      "type": "string",

      "metadata": {

        "description": "Admin username on all VMs."

      }

    },

    "adminPassword": {

      "type": "securestring",

      "metadata": {

        "description": "Admin password on all VMs."

      }

    }

  },
````

### 변수

템플릿에서 나중에 참조할 변수를 정의하는 표준 ARM 템플릿 구성 요소입니다. 이 예제에서는 변수를 사용하여 원하는 OS, 일부 네트워크 구성 세부 정보, 저장소 설정 및 위치를 정의합니다. 위치의 경우 _location()_ 함수를 사용하여 배포되는 위치의 리소스 그룹에서 위치를 선택합니다.

고유 문자열 배열이 저장소 계정 접두사에 대해 정의됩니다. 설명은 저장소 섹션을 참조하세요.

````
  "variables": {

    "apiVersion": "2015-06-15",

    "newStorageAccountSuffix": "[concat(parameters('vmssName'), 'sa')]",

    "uniqueStringArray": [

      "[concat(uniqueString(concat(resourceGroup().id, deployment().name, variables('newStorageAccountSuffix'), '0')))]",

      "[concat(uniqueString(concat(resourceGroup().id, deployment().name, variables('newStorageAccountSuffix'), '1')))]",

      "[concat(uniqueString(concat(resourceGroup().id, deployment().name, variables('newStorageAccountSuffix'), '2')))]",

      "[concat(uniqueString(concat(resourceGroup().id, deployment().name, variables('newStorageAccountSuffix'), '3')))]",

      "[concat(uniqueString(concat(resourceGroup().id, deployment().name, variables('newStorageAccountSuffix'), '4')))]"

    ],

    "vmSize": "Standard\_A1",

    "vhdContainerName": "[concat(parameters('vmssName'), 'vhd')]",

    "osDiskName": "[concat(parameters('vmssName'), 'osdisk')]",

    "virtualNetworkName": "[concat(parameters('vmssName'), 'vnet')]",

    "subnetName": "[concat(parameters('vmssName'), 'subnet')]",

    "nicName": "[concat(parameters('vmssName'), 'nic')]",

    "ipConfigName": "[concat(parameters('vmssName'), 'ipconfig')]",

    "addressPrefix": "10.0.0.0/16",

    "subnetPrefix": "10.0.0.0/24",

    "storageAccountType": "Standard\_LRS",

    "location": "[resourceGroup().location]",

    "osType": {

      "publisher": "Canonical",

      "offer": "UbuntuServer",

      "sku": "15.10",

      "version": "latest"

    },

    "imageReference": "[variables('osType')]"

  },
````

### 리소스

이 섹션에서는 각 리소스 유형이 정의됩니다.

````
   "resources": [
````


**저장소를 클릭합니다.** VM 규모 집합을 만들 때 저장소 계정 배열을 지정합니다. 그러면 VM 인스턴스에 대한 OS 디스크가 각 계정 간에 고르게 배포됩니다. 나중에 VM 규모 집합은 저장소 계정을 관리하지 않아도 되는 대신 규모 집합 정의의 일부로 저장소 속성을 설명하는 관리되는 디스크 방식을 사용하도록 전환될 예정입니다. 현재는 저장소 계정을 필요한 만큼 미리 만들어야 합니다. 규모 집합에서 VM을 100개까지 수월하게 지원하도록 저장소 계정을 5개 만드는 것이 좋습니다.

저장소 계정 집합을 만들면 파티션에 저장소 스탬프로 배포되며 배포 스키마는 저장소 계정 이름의 첫 번째 문자에 따라 달라집니다. 이런 이유 때문에 최적의 성능을 위해 저장소 계정을 만들 때 각각 다른 문자로 시작되도록 하여 알파벳이 골고루 분산되도록 하는 것이 좋습니다. 수동으로 이름을 지정할 수 있습니다. 아니면 이 예제에서와 같이 uniqueString() 함수를 사용하여 난수 배포를 제공합니다.

````
    {

      "type": "Microsoft.Storage/storageAccounts",

      "name": "[concat(variables('uniqueStringArray')[copyIndex()], variables('newStorageAccountSuffix'))]",

      "location": "[variables('location')]",

      "apiVersion": "[variables('apiVersion')]",

      "copy": {

        "name": "storageLoop",

        "count": 5

      },

      "properties": {

        "accountType": "[variables('storageAccountType')]"

      }

    },
````

**가상 네트워크**. VNET 만들기. 동일한 VNET 안에 규모 집합을 여러 개 둘 수도 있고 단일 VM을 둘 수도 있습니다.

````
    {

      "type": "Microsoft.Network/virtualNetworks",

      "name": "[variables('virtualNetworkName')]",

      "location": "[variables('location')]",

      "apiVersion": "[variables('apiVersion')]",

      "properties": {

        "addressSpace": {

          "addressPrefixes": [

            "[variables('addressPrefix')]"

          ]

        },

        "subnets": [

          {

            "name": "[variables('subnetName')]",

            "properties": {

              "addressPrefix": "[variables('subnetPrefix')]"

            }

          }

        ]

      }

    },
````

### virtualMachineScaleSets 리소스

여러 측면에서 _virtualMachineScaleSets_ 리소스는 _virtualMachines_ 리소스와 유사합니다. 둘 다 Microsoft.Compute 리소스 공급자에 의해 제공되며 수준이 같습니다. 주요 차이점은 생성하는 VM의 수를 말하는 _용량_ 값을 제공한다는 것과, 여기에 정의하는 값이 VM 규모 집합의 모든 VM에 적용된다는 것입니다.

_dependsOn_ 섹션이 위에서 생성한 저장소 계정과 VNET을 어떻게 참조하는 지와 용량이 _instanceCount_ 매개 변수를 참조하는 것에 주목하세요.

````
    {

      "type": "Microsoft.Compute/virtualMachineScaleSets",

      "name": "[parameters('vmssName')]",

      "location": "[variables('location')]",

      "apiVersion": "[variables('apiVersion')]",

      "dependsOn": [

        "[concat('Microsoft.Storage/storageAccounts/', variables('uniqueStringArray')[0], variables('newStorageAccountSuffix'))]",

        "[concat('Microsoft.Storage/storageAccounts/', variables('uniqueStringArray')[1], variables('newStorageAccountSuffix'))]",

        "[concat('Microsoft.Storage/storageAccounts/', variables('uniqueStringArray')[2], variables('newStorageAccountSuffix'))]",

        "[concat('Microsoft.Storage/storageAccounts/', variables('uniqueStringArray')[3], variables('newStorageAccountSuffix'))]",

        "[concat('Microsoft.Storage/storageAccounts/', variables('uniqueStringArray')[4], variables('newStorageAccountSuffix'))]",

        "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"

      ],

      "sku": {

        "name": "[variables('vmSize')]",

        "tier": "Standard",

        "capacity": "[parameters('instanceCount')]"

      },
````

**속성**

VM 규모 집합에는 upgradePolicy 설정이 있습니다. 나중에 출시될 버전은 조각화된 업데이트(예: 한번에 20%의 VM에 대한 구성을 변경)를 지원할 예정이지만, 현재는 upgradePolicy를 수동 또는 자동으로 설정할 수 있습니다. 수동이란 템플릿을 변경하고 다시 배포하더라도 새 VM이 만들어지거나 확장이 업데이트되어야만(예를 들어 _용량_을 증가시키면) 변경 사항의 효력이 발생한다는 의미입니다. 자동이란 모든 VM을 “한번에” 업데이트하고 모두를 재부팅한다는 의미입니다. 일반적으로 수동이 더 안전한 방식입니다. 점차적으로 업데이트하기 위해서 개별 VM을 삭제하고 필요에 따라 다시 배포할 수 있습니다.

````
      "properties": {

         "upgradePolicy": {

         "mode": "Manual"

        },
````

**속성->virtualMachineProfile**

여기에서는 위에서 VM에 대한 컨테이너로 만든 저장소 계정을 참조합니다. 실제 컨테이너를 미리 만들 필요는 없고 계정만 만들면 됩니다.

````
        "virtualMachineProfile": {

          "storageProfile": {

            "osDisk": {

              "vhdContainers": [

                "[concat('https://', variables('uniqueStringArray')[0], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",

                "[concat('https://', variables('uniqueStringArray')[1], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",

                "[concat('https://', variables('uniqueStringArray')[2], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",

                "[concat('https://', variables('uniqueStringArray')[3], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",

                "[concat('https://', variables('uniqueStringArray')[4], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]"

              ],

              "name": "[variables('osDiskName')]",

              "caching": "ReadOnly",

              "createOption": "FromImage"

            },

            "imageReference": "[variables('imageReference')]"

          },
````

**속성->osProfile**

VM 규모 집합이 개별 VM에 비해 한가지 다른 점은 컴퓨터 이름이 접두사라는 점입니다. VM 규모 집합의 VM 인스턴스는 myvm\_0, myvm\_1 etc 같은 이름으로 만들어집니다.

````
          "osProfile": {

            "computerNamePrefix": "[parameters('vmSSName')]",

            "adminUsername": "[parameters('adminUsername')]",

            "adminPassword": "[parameters('adminPassword')]"

          },
````

**속성->networkProfile**

VMSS에 대한 네트워크 프로필을 정의하는 경우에는 NIC 구성과 IP 구성 양쪽 모두에 이름이 있다는 점을 기억합니다. NIC 구성에는 "_primary_" 설정이 있고 서브넷 id는 위에서 VNET의 일부로 만든 서브넷 리소스를 다시 참조합니다.

````
          "networkProfile": {

            "networkInterfaceConfigurations": [

              {

                "name": "[variables('nicName')]",

                "properties": {

                  "primary": "true",

                  "ipConfigurations": [

                    {

                      "name": "[variables('ipConfigName')]",

                      "properties": {

                        "subnet": {

                          "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'), '/subnets/', variables('subnetName'))]"

                        }

                      }

                    }

                  ]

                }

              }

            ]

          }
````

**속성->extensionProfile**

위의 간단한 예제는 확장 프로필이 필요하지 않습니다. CustomScript Extension([https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-lapstack-autoscale](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-lapstack-autoscale))을 사용하여 Apache와 PHP를 배포하는 이 템플릿의 동작에서 확장 프로필을 볼 수 있습니다. 이 예제에서 네트워크 IP 속성은 부하 분산 장치를 참조합니다. 부하 분산 장치는 VM 규모 집합 시나리오 섹션에서 자세히 설명합니다.

## VM 규모 집합 시나리오

이 섹션은 전형적인 VM 규모 집합 시나리오와 예제 템플릿을 소개합니다. 지금은 템플릿을 필요로 하지만 이들 중 일부는 나중에 포털로 통합될 예정입니다. 또한 높은 수준의 Azure 서비스(예: 배치, 서비스 패브릭, Azure 컨테이너 서비스)에서 이 시나리오를 사용할 예정입니다.

## RDP/SSH에서 VM 규모 집합 인스턴스

VM 규모 집합은 VNET 내부에 생성되며, 개별 VM에 공용 IP 주소가 할당되지 않습니다. 일반적으로 계산 그리드에 있는 상태 비저장 리소스에 IP 주소를 개별적으로 모두 할당하는 비용 및 관리 오버헤드를 원하지 않고, 부하 분산 장치 또는 독립 실행형 가상 컴퓨터와 같이 공용 IP 주소가 있는 리소스를 포함하여 VNET의 다른 리소스에서 VM에 쉽게 연결할 수 있기 때문에 이렇게 하는 것이 좋습니다.

VMSS VM에 연결하는 두 가지의 간단한 방법이 아래 설명되어 있습니다.

### NAT 규칙을 사용하여 VM에 연결

공용 IP 주소를 만들고, 부하 분산 장치에 할당하고, IP 주소의 포트를 VM 규모 집합에 포함된 VM의 포트에 매핑하는 인바운드 NAT 규칙을 정의할 수 있습니다. 예:

Public IP->Port 50000 -> vmss\_0->Port 22 Public IP->Port 50001 -> vmss\_1->Port 22 Public IP->Port 50002-> vmss\_2->Port 22

하나의 공용 IP를 사용하여 규모 집합에 포함된 모든 VM에 SSH 연결을 사용하도록 설정하기 위해서 NAT 규칙을 사용하는 VM 규모 집합을 만드는 예제가 [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat)에 있습니다.

RDP 및 Windows에서 동일한 작업을 수행하는 예제가 [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat)에 있습니다.

### "점프 상자"를 사용하여 VM에 연결

동일한 VNET에 VM 규모 집합과 독립 실행형 VM을 만들면, 독립 실행형 VM과 VMSS VM은 VNET/Subnet에 정의된 대로 내부 IP 주소를 사용하여 서로 연결할 수 있습니다.

공용 IP 주소를 만들어서 독립 실행형 VM에 할당하면 독립 실행형 VM에 RDP 또는 SSH를 사용할 수 있고 해당 컴퓨터에서 사용자의 VMSS 인스턴스로 연결할 수 있습니다. 본질적으로 간단한 VM 규모 집합이 기본 구성에 공용 IP 주소를 포함하는 간단한 독립 실행형 VM보다 더 안전하다는 것을 알 수 있습니다.

이러한 방식의 예제가 [https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json](https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json)에 준비되어 있으며, 이 템플릿은 VM 규모 집합 기반 VM 클러스터를 관리하는 독립 실행형 마스터 VM으로 구성된 간단한 Mesos 클러스터를 만듭니다.

## VM 규모 집합 인스턴스에 대한 라운드 로빈 부하 분산

"라운드 로빈" 방식을 사용하여 VM 계산 클러스터에 작업을 전달하려면, 그에 맞게 Azure 부하 분산 장치에 부하 분산 규칙을 구성합니다. 지정된 프로토콜, 간격, 요청 경로로 포트를 Ping하여 응용 프로그램이 실행되는지 확인하는 프로브를 정의할 수도 있습니다.

IIS 웹 서버를 실행하는 VM으로 구성되는 VM 규모 집합을 만들고 부하 분산 장치를 사용하여 각 VM이 수신하는 부하를 분산하는 예제가 있습니다. 또한 HTTP 프로토콜을 사용하여 각 VM의 특정 URL을 ping합니다. [https://github.com/gbowerman/azure-myriad/blob/master/vmss-win-iis-vnet-storage-lb.json](https://github.com/gbowerman/azure-myriad/blob/master/vmss-win-iis-vnet-storage-lb.json) Microsoft.Network/loadBalancers 리소스 유형을 살펴보고 virtualMachineScaleSet의 networkProfile 및 extensionProfile을 살펴보세요.

## Azure 자동 크기 조정과 VM 규모 집합 인스턴스

CPU/메모리/디스크 사용량 또는 기타 이벤트에 따라서 VM 규모 집합의 인스턴스 수(_용량_)를 다르게 하려는 경우에는, Microsoft.Insights 리소스 공급자의 다양한 자동 크기 조정 설정을 사용할 수 있습니다. 사용할 수 있는 설정에 대한 내용은 [https://msdn.microsoft.com/library/azure/dn931953.aspx](https://msdn.microsoft.com/library/azure/dn931953.aspx) 페이지의 Insights REST 문서에서 읽을 수 있습니다.

Insights 자동 크기 조정은 VM 규모 집합과 직접 통합됩니다. 이것을 설정하려면 규모 집합에 다시 참조되는 _targetResourceUri_ 및 _metricResourceUri_를 포함하는 Microsoft.Insights/autoscaleSettings 리소스 유형을 정의합니다. 규모 집합을 정의할 때 Windows 또는 Linux의 Linux Diagnostic Extension(LDE)에 Azure 진단 에이전트(WAD)를 설치하는 _extensionProfile_을 포함시킵니다. 샘플링 기간 5분 동안 시간 단위(세분성)가 1분이고 평균 CPU 사용량이 50%를 초과할 때 사전에 정의된 한도까지 VM을 추가하는 Linux 예제는 다음과 같습니다.

[https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-lapstack-autoscale](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-lapstack-autoscale).

나중에 자동 크기 조정을 포함하는 VM 규모 집합이 Azure 포털에 직접적으로 통합될 예정입니다.

## VM 규모 집합을 PaaS 클러스터 관리자에서 계산 클러스터로 배포

VM 규모 집합을 차세대 작업자 역할이라고 설명하기도 합니다. 이 설명이 타당하기는 하지만 규모 집합 기능을 PaaS v1 작업자 역할 기능과 혼동할 위험이 있습니다.

VM 규모 집합은 플랫폼/런타임 독립적이고, 사용자 지정이 가능하며, Azure 리소스 관리자 IaaS로 통합되는 일반화된 계산 리소스를 제공한다는 점에서 진정한 “작업자 역할” 또는 작업자 리소스를 제공합니다.

PaaS v1 작업자 역할은 플랫폼/런타임 지원(Windows 플랫폼 이미지에 대해서만)에 관해서는 제한되어 있지만 VIP 교환, 구성 가능한 업그레이드 설정, VM 규모 집합에 _아직_ 제공되지 않는 또는 서비스 패브릭 같은 다른 높은 수준의 PaaS 서비스에 의해 제공될 예정인 런타임/앱 배포별 설정 같은 서비스를 포함합니다. 이것을 고려하여 VM 규모 집합을 PaaS를 지원하는 인프라의 하나로 주목할 수 있습니다. 즉, 서비스 패브릭 같은 PaaS 솔루션 또는 Mesos 같은 클러스터 관리자는 VM 규모 집합을 기반으로 확장성 있는 계산 계층으로 구축될 수 있습니다.

독립 실행형 VM으로 동일한 VNET에 VM 규모 집합을 배포하는 Mesos 클러스터의 예제가 있습니다. 독립 실행형 VM은 Mesos 마스터이며, VM 규모 집합은 슬레이브 노드 집합을 나타냅니다. [https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json](https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json). 앞으로 출시할 [Azure 컨테이너 서비스](https://azure.microsoft.com/blog/azure-container-service-now-and-the-future/) 버전은 VM 규모 집합을 기반으로 더 복잡하고/확정적인 버전의 시나리오를 배포할 예정입니다.

## VM 규모 집합 확장 및 축소

VM 규모 집합에서 가상 컴퓨터의 수를 늘리거나 줄이려면, _용량_ 속성만 변경하고 템플릿을 다시 배포합니다. Azure 자동 크기 조정에서 지원되지 않는 사용자 지정 크기 조정(custom scale) 이벤트를 정의하려는 경우 이러한 단순성을 통해 사용자 지정 크기 조정 계층을 쉽게 작성할 수 있습니다.

용량을 변경하기 위해서 템플릿을 다시 배포하는 경우 SKU와 업데이트된 용량만 포함하는 훨씬 더 작은 템플릿을 정의할 수 있습니다. 이와 관련된 예제를 [https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-scale-in-or-out/azuredeploy.json](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-linux-nat/azuredeploy.json)에서 볼 수 있습니다.

## VM 규모 집합 성능 및 크기 조정 지침

- 공개 미리 보기 기간 동안은 여러 개의 VM 규모 집합에 VM을 한번에 500개 이상 만들지 마십시오. 이 한도는 나중에 상향 조정될 예정입니다.
- 저장소 계정당 VM을 40개 이하로 계획하세요.
- 저장소 계정 이름의 첫 문자를 최대한 골고루 사용합니다. [Azure 빠른 시작 템플릿](https://github.com/Azure/azure-quickstart-templates/)의 예제 VMSS 템플릿에 방법에 대한 예제가 제공되어 있습니다.
- 사용자 지정 VM을 사용하는 경우, 하나의 저장소 계정에 VM 규모 집합당 VM을 40개 이하로 계획합니다. VM 규모 집합 배포를 시작하려면 그 전에 저장소 계정에 이미지를 미리 복사해 두어야 합니다. 자세한 내용은 FAQ를 참조하세요.
- VNET당 VM을 2048개 이하로 계획합니다. 이 한도는 나중에 상향 조정될 예정입니다.
- 사용자가 만들 수 있는 VM의 수는 모든 지역에서 계산 코어 할당량에 의해 제한됩니다. 클라우드 서비스 또는 IaaS v1에 사용할 코어 한도가 높더라도, 계산 할당량 한도를 높이려면 고객 지원팀에 문의해야 합니다. 할당량을 쿼리하려면 Azure CLI 명령 _azure vm list-usage_를 사용하고, PoweShell 명령 _Get-AzureRmVMUsage_를 사용합니다. PowerShell 1.0 이전 버전을 사용하는 경우에는 _Get-AzureVMUsage_ 사용합니다.


## VM 규모 집합 FAQ

**Q. VM 규모 집합에 포함할 수 있는 VM의 수는 몇 개입니까?**

A. 여러 저장소 계정으로 배포될 수 있는 플랫폼 이미지를 사용하는 경우 100개입니다. 사용자 지정 이미지는 미리 보기 기간 동안 하나의 저장소 계정으로 제한되기 때문에 사용자 지정 이미지를 사용하는 경우 40개까지 가능합니다.

**VM 규모 집합에 존재하는 다른 리소스 제한 사항은 무엇입니까?**

A. 기존 Azure 서비스 한도가 적용됩니다. [https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/)

미리 보기 기간 동안 지역 당 다수의 규모 집합에 VM을 500개 이하로 생성하도록 제한됩니다.

**Q. VM 규모 집합 내에서 데이터 디스크가 지원됩니까?**

A. 초기 릴리스에서는 지원되지 않습니다. 데이터 저장 옵션은 다음과 같습니다.

- Azure 파일(SMB 공유 드라이브)

- OS 드라이브

- 임시 드라이브(Azure 저장소에서 지원되지 않는 로컬 드라이브)

- 외부 데이터 서비스(예: 원격 DB, Azure 테이블, Azure BLOB)

**Q. VMSS를 지원하는 Azure 지역은 어디입니까?**

A. Azure 리소스 관리자를 지원하는 지역은 VM 규모 집합을 지원합니다.

**Q. 사용자 지정 이미지를 사용하여 VMSS를 어떻게 만드나요?**

A. vhdContainers 속성을 비워둡니다. 아래 예제를 참조하세요.

````
"storageProfile": {
   "osDisk": {
      "name": "vmssosdisk",
      "caching": "ReadOnly",
      "createOption": "FromImage",
      "image": {
         "uri": [https://mycustomimage.blob.core.windows.net/system/Microsoft.Compute/Images/mytemplates/template-osDisk.vhd](https://mycustomimage.blob.core.windows.net/system/Microsoft.Compute/Images/mytemplates/template-osDisk.vhd)
     },
     "osType": "Windows"
  }
},
````

**Q. VMSS 용량을 20에서 15로 줄이면, 어떤 VM이 제거되나요?**

A. 마지막 VM 5개(인덱스가 가장 큰)가 제거됩니다.

**Q. 그런 다음 용량을 15에서 18로 늘리면 어떻게 되나요?**

18로 늘리면 인덱스가 15, 16, 17인 VM이 생성됩니다. 두 경우 모두, VM은 FD와 UD에서 균형이 조정됩니다.

**Q. VM 규모 집합에서 여러 확장을 사용하는 경우, 실행 순서를 강제로 적용할 수 있습니까?**

A. 직접적으로는 불가능합니다. 하지만 customScript 확장의 경우, 다른 확장이 완료될 때까지 사용자의 스크립트를 대기시킬 수 있습니다.(예를 들면, 확장 로그를 모니터링하여 - [https://github.com/Azure/azure-quickstart-templates/blob/master/201-vmss-lapstack-autoscale/install\_lap.sh](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vmss-lapstack-autoscale/install_lap.sh) 페이지를 참조하세요.)

**Q. VM 규모 집합은 Azure 가용성 집합과 작업이 가능합니까?**

A. 예. 규모 집합은 FD 3개와 UD 5개를 포함하는 암시적인 가용성 집합입니다. virtualMachineProfile에 대해 아무것도 구성할 필요가 없습니다. 앞으로 출시되는 릴리스에서 규모 집합이 여러 테넌트로 확장될 가능성이 있지만 현재 규모 집합은 단일 가용성 집합입니다.

## 다음 단계

VM 규모 집합에 대한 미리 보기 기간 동안, 설명서가 추가되고 포털 및 자동 크기 조정 같은 통합 기능이 공개될 예정입니다.

여러분에게 필요한 기능을 제공하도록 서비스를 구축하는데 있어 여러분의 의견은 매우 중요합니다. 잘 작동하는 기능과 그렇지 않은 기능 그리고 개선할 사항을 알려 주시기 바랍니다. 피드백은 [vmssfeedback@microsoft.com](mailto:vmssfeedback@microsoft.com)으로 보내 주시기 바랍니다.

## 리소스

| **항목** | **링크** |
| --- | --- |
| Azure 리소스 관리자 개요 | [https://azure.microsoft.com/documentation/articles/resource-group-overview/](https://azure.microsoft.com/documentation/articles/resource-group-overview/) |
| Azure 리소스 관리자 템플릿 | [https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/](https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/) |
| Azure 리소스 관리자 템플릿 함수 | [https://azure.microsoft.com/documentation/articles/resource-group-template-functions/](https://azure.microsoft.com/documentation/articles/resource-group-template-functions/) |
| 예제 템플릿(github) | [https://github.com/Azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates) |
| VM 규모 집합 REST API 가이드 | [https://msdn.microsoft.com/library/mt589023.aspx](https://msdn.microsoft.com/library/mt589023.aspx) |
| VM 규모 집합 동영상 | [https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/) [https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman) [https://channel9.msdn.com/Blogs/Windows-Azure/VM-Scale-Set-Template-Dissection/player](https://channel9.msdn.com/Blogs/Windows-Azure/VM-Scale-Set-Template-Dissection/player) |
| 자동 크기 조정 설정(MSDN) | [https://msdn.microsoft.com/library/azure/dn931953.aspx](https://msdn.microsoft.com/library/azure/dn931953.aspx) |
| Azure에서 리소스 그룹 배포 문제 해결 | [https://azure.microsoft.com/documentation/articles/resource-group-deploy-debug/](https://azure.microsoft.com/documentation/articles/resource-group-deploy-debug/) |

<!---HONumber=Nov15_HO3-->