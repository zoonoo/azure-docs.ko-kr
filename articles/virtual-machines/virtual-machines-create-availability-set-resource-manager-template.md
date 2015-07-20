<properties
	pageTitle="Azure 리소스 관리자 템플릿을 사용하여 가용성 집합 만들기"
	description="가용성 집합 템플릿을 사용하는 방법을 설명하고 템플릿 구문을 포함합니다."
	services="virtual-machines"
	documentationCenter=""
	authors="KBDAzure"
	manager="timlt"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/04/2015"
	ms.author="kathydav"/>

# Azure 리소스 관리자 템플릿을 사용하여 가용성 집합 만들기

Azure PowerShell 또는 Azure 명령줄(CLI) 및 리소스 관리자 템플릿을 사용하여 가상 컴퓨터에 대한 가용성 집합을 쉽게 만들 수 있습니다. 이 템플릿은 가용성 집합을 만듭니다.

시작하기 전에 Azure PowerShell과 Azure CLI이 구성 및 준비되어 있는지 확인합니다.

[AZURE.INCLUDE [arm-getting-setup-powershell](../../includes/arm-getting-setup-powershell.md)]

[AZURE.INCLUDE [xplat-getting-set-up](../../includes/xplat-getting-set-up.md)]


## 리소스 관리자 템플릿을 사용하여 가용성 집합 만들기

다음 단계에 따라 Azure PowerShell과 함께 Github 템플릿 리포지토리의 리소스 관리자 템플릿을 사용하여 가상 컴퓨터에 대한 가용성 집합을 만듭니다.

### 1단계: JSON 파일 다운로드

로컬 폴더를 JSON 템플릿 파일의 위치로 지정하고 해당 폴더(예: C:\\Azure\\Templates\\availability)를 만듭니다.

폴더 이름을 바꾸고 다음 명령을 복사한 후 실행합니다.

	$folderName="<folder name, such as C:\Azure\Templates\availability>"
	$webclient = New-Object System.Net.WebClient
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-2-vms-2-FDs-no-resource-loops/azuredeploy.json"
	$filePath = $folderName + "\azuredeploy.json"
	$webclient.DownloadFile($url,$filePath)

### 2단계: 필수 매개 변수에 대한 세부 정보 수집

템플릿을 사용하면 위치 및 집합 이름과 같은 세부 정보를 제공해야 합니다. 템플릿에 필요한 매개변수를 찾으려면 다음 중 하나를 수행합니다.

- 매개 변수 목록을 [여기](http://azure.microsoft.com/documentation/templates/201-2-vms-2-FDs-no-resource-loops/)에서 검토합니다.
- 사용자가 원하는 도구 또는 텍스트 편집기에서 JSON 파일을 엽니다. 파일의 위쪽에 있는 "parameters" 섹션을 찾습니다. 이 섹션은 템플릿에서 가상 컴퓨터를 구성하는 데 필요한 매개 변수 집합을 나열합니다.

입력할 준비가 되도록 필요한 정보를 수집합니다. 템플릿을 배포하는 명령을 실행하면 정보에 대해 묻는 메시지가 나타납니다.

### 3단계: 가용성 집합 만들기

다음 섹션에서는 Azure PowerShell 또는 Azure CLI를 사용하여 이 작업을 수행하는 방법을 보여줍니다.

### Azure PowerShell 사용

Azure 배포 이름, 리소스 그룹 이름, Azure 위치 및 저장된 JSON 파일의 폴더를 입력하고 다음 명령을 실행합니다.

	$deployName="<deployment name>"
	$RGName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$folderName="<folder name, such as C:\Azure\Templates\availability>"
	$templateFile= $folderName + "\azuredeploy.json"
	New-AzureResourceGroup –Name $RGName –Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateFile $templateFile

**New-AzureResourceGroupDeployment** 명령을 실행하면 JSON 파일의 **"parameters"** 섹션에 매개 변수 값을 제공하라는 메시지가 나타납니다. 매개 변수 값을 제공하면 리소스 그룹 및 가용성 집합이 생성됩니다.

다음은 템플릿에 대한 PowerShell 명령 집합의 예제입니다.

	$deployName="TestDeployment"
	$RGName="TestRG"
	$locname="West US"
	$folderName="C:\Azure\Templates[thing]"
	$templateFile= $folderName + "\azuredeploy.json"
	New-AzureResourceGroup –Name $RGName –Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateFile $templateFile

다음과 같이 표시됩니다.

	cmdlet New-AzureResourceGroup at command pipeline position 1
	Supply values for the following parameters:
	(Type !? for Help.)
	newStorageAccountName: saTest
	dnsNameForPublicIP: 131.107.89.211
	adminUserName: WebAdmin1
	adminPassword: *******
	vmSourceImageName: a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201503.01-en.us-127GB.vhd
	...

이 리소스 그룹과 모든 해당 리소스(저장소 계정, 가상 컴퓨터 및 가상 네트워크)를 제거하려면 다음 명령을 사용합니다.

	Remove-AzureResourceGroup –Name "<resource group name>"


## Azure CLI 사용

Azure CLI 명령과 Github 템플릿 리포지토리의 리소스 관리자 템플릿을 사용하여 가용성 집합을 만들려면 다음 단계를 따릅니다.

	azure group deployment create <my-resource-group> <my-deployment-name> --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-2-vms-2-FDs-no-resource-loops/azuredeploy.json

<!---HONumber=July15_HO2-->