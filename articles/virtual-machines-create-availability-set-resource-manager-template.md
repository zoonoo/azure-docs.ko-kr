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
	ms.date="04/20/2015" 
	ms.author="kathydav"/>

# Azure 리소스 관리자 템플릿을 사용하여 가용성 집합 만들기

Azure PowerShell 또는 xplat-cli 및 리소스 관리자 템플릿을 사용하여 가상 컴퓨터에 대한 가용성 집합을 쉽게 만들 수 있습니다. 이 템플릿은 가용성 집합을 만듭니다.
 
시작하기 전에 Azure PowerShell과 xplat-cli가 구성 및 준비되어 있는지 확인합니다.

[AZURE.INCLUDE [arm-getting-setup-powershell](../includes/arm-getting-setup-powershell.md)]

[AZURE.INCLUDE [xplat-getting-set-up](../includes/xplat-getting-set-up.md)]


## Azure PowerShell을 사용하여 리소스 관리자 템플릿으로 [do something]

다음 단계에 따라 Azure PowerShell을 사용하여 Github 템플릿 리포지토리의 리소스 관리자 템플릿으로 [do something] 있습니다.

### 1단계: JSON 파일 다운로드

로컬 폴더를 JSON 템플릿 파일의 위치로 지정하고 해당 폴더\(예: C:\\Azure\\Templates\[thing\]\)를 만듭니다.

폴더 이름을 바꾸고 다음 명령을 복사한 후 실행합니다.

	$folderName="<folder name, such as C:\Azure\Templates\[thing]>"
	$webclient = New-Object System.Net.WebClient
	$url = "[Writers: add the URL to the RAW version of the target template in GitHub]"
	$filePath = $folderName + "\azuredeploy.json"
	$webclient.DownloadFile($url,$filePath) 

### 2단계: \(선택 사항\) 매개 변수 보기

템플릿으로 [do something] 경우 구성 매개 변수 집합을 지정해야 합니다. 가상 컴퓨터를 만드는 명령을 실행하기 전에 로컬 JSON 파일에서 템플릿을 지정하는 데 필요한 매개 변수를 확인하려면 선택한 도구 또는 텍스트 편집기에서 JSON 파일을 엽니다. 파일의 위쪽에 있는 "parameters" 섹션을 찾습니다. 이 섹션은 템플릿에서 가상 컴퓨터를 구성하는 데 필요한 매개 변수 집합을 나열합니다. 다음은 azuredeploy.json 템플릿의 **"parameters"** 섹션입니다.

[작성자를 위한 참고 사항: azuredeploy.json의 "parameters" 섹션에 붙여 넣고 형식을 코드로 지정합니다.]

### 3단계: [템플릿을 완료하는 데 필요한 정보] 가져오기

[작성자를 위한 참고 사항: 필요한 경우 매개 변수 값을 수집하는 선택적 섹션입니다.]

### 4단계: 템플릿으로 [do the thing]

Azure 배포 이름, 리소스 그룹 이름, Azure 위치 및 저장된 JSON 파일의 폴더를 입력하고 다음 명령을 실행합니다.

	$deployName="<deployment name>"
	$RGName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$folderName="<folder name, such as C:\Azure\Templates\[thing]>" 
	$templateFile= $folderName + "\azuredeploy.json"
	New-AzureResourceGroup –Name $RGName –Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateFile $templateFile

**New-AzureResourceGroupDeployment** 명령을 실행하면 JSON 파일의 **"parameters"** 섹션에 매개 변수 값을 제공하라는 메시지가 나타납니다. 매개 변수 값을 제공하면 리소스 그룹 및 가용성 집합이 생성됩니다.

다음은 템플릿에 대한 PowerShell 명령 집합의 예제입니다.

	$deployName="TestDeployment"
	$RGName="TestRG"
	$locname="West US"
	$folderName="C:\Azure\Templates\[thing]"
	$templateFile= $folderName + "\azuredeploy.json"
	New-AzureResourceGroup –Name $RGName –Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateFile $templateFile

다음과 유사한 결과가 표시됩니다.

[작성자를 위한 참고 사항: 메시지가 표시된 처음 몇 개의 매개 변수에 대한 PowerShell 화면에 다음을 바꿔 붙여 넣습니다.]

	cmdlet New-AzureResourceGroup at command pipeline position 1
	Supply values for the following parameters:
	(Type !? for Help.)
	newStorageAccountName: saTest
	dnsNameForPublicIP: 131.107.89.211
	adminUserName: WebAdmin1
	adminPassword: *******
	vmSourceImageName: a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201503.01-en.us-127GB.vhd
	...

이 리소스 그룹과 모든 해당 리소스\(저장소 계정, 가상 컴퓨터 및 가상 네트워크\)를 제거하려면 다음 명령을 사용합니다.

	Remove-AzureResourceGroup –Name "<resource group name>"


## xplat-cli를 사용하여 리소스 관리자 템플릿으로 [do something]

다음 단계에 따라 xplat-cli를 사용하여 Github 템플릿 리포지토리의 리소스 관리자 템플릿으로 [do something] 있습니다.

### 1단계: 템플릿의 JSON 파일 다운로드

로컬 폴더를 JSON 템플릿 파일의 위치로 지정하고 해당 폴더\(예: C:\\Azure\\Templates\[thing\]\)를 만듭니다.

폴더 이름을 입력하고 다음 명령을 실행합니다.

[템플릿 파일을 다운로드하는 xplat 명령]

### 2단계: \(선택 사항\) 템플릿의 매개 변수 보기

템플릿으로 [do something] 경우 구성 매개 변수 집합을 지정해야 합니다. 가상 컴퓨터를 만드는 명령을 실행하기 전에 로컬 JSON 파일에서 템플릿을 지정하는 데 필요한 매개 변수를 확인하려면 선택한 도구 또는 텍스트 편집기에서 JSON 파일을 엽니다. 파일의 위쪽에 있는 "parameters" 섹션을 찾습니다. 이 섹션은 템플릿에서 가상 컴퓨터를 구성하는 데 필요한 매개 변수 집합을 나열합니다. 다음은 azuredeploy.json 템플릿의 **"parameters"** 섹션입니다.

[작성자를 위한 참고 사항: azuredeploy.json의 "parameters" 섹션에 붙여 넣고 형식을 코드로 지정합니다.]

### 3단계: [템플릿을 완료하는 데 필요한 정보] 가져오기

[작성자를 위한 참고 사항: 필요한 경우 매개 변수 값을 수집하는 선택적 섹션입니다.]

### 4단계: 템플릿으로 [do the thing]

\[needed info} 정보를 입력하고 다음 명령을 실행합니다.

[템플릿 파일을 실행하는 xplat 명령]

[xplat에서 템플릿을 실행하는 방법에 대한 설명]


다음은 템플릿에 대한 xplat-cli 명령 집합의 예제입니다.

[xplat 명령 예제]

다음과 유사한 결과가 표시됩니다.

[작성자를 위한 참고 사항: 메시지가 표시된 처음 몇 개의 매개 변수에 대한 xplat 화면에 붙여 넣습니다.]


이 리소스 그룹과 모든 해당 리소스\([리소스 그룹의 항목]\)를 제거하려면 다음 명령을 사용합니다.

[xplat 명령]




<!--HONumber=52-->
