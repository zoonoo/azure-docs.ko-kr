<properties
	pageTitle="Azure 리소스 관리에서 Mac, Linux 및 Windows용 Microsoft Azure CLI 사용 | Microsoft Azure"
	description="Azure 리소스 관리에서 Mac, Linux 및 Windows용 Microsoft Azure CLI 사용"
	editor="tysonn"
	manager="timlt"
	documentationCenter=""
	authors="dlepow"
	services="virtual-machines"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="command-line-interface" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/09/2015" 
	ms.author="danlep"/>

# Azure 리소스 관리에서 Mac, Linux 및 Windows용 Azure CLI 사용

> [AZURE.SELECTOR]
- [Azure PowerShell](../powershell-azure-resource-manager.md)
- [Azure CLI](xplat-cli-azure-resource-manager.md)


이 항목에서는 **arm** 모드로 Mac, Linux 및 Windows용 Azure CLI를 사용하여 Azure 리소스 및 VM을 생성하고, 관리하고, 삭제하는 방법에 대해 설명합니다.

>[AZURE.NOTE]명령줄에서 Azure 리소스를 만들고 관리하려면 Azure 계정이 필요합니다([무료 평가판 위치](http://azure.microsoft.com/pricing/free-trial/)). 또한 [Azure CLI를 설치](../xplat-cli-install.md)하고 [로그온해야 계정과 연결된 Azure 리소스를 사용](../xplat-cli-connect.md)할 수 있습니다. 이러한 작업이 완료되면 사용할 준비가 된 것입니다.

## Azure 리소스

리소스 관리자를 통해 _리소스_(가상 컴퓨터, 데이터베이스 서버, 데이터베이스 또는 웹 사이트와 같은 사용자 관리 엔터티) 그룹을 단일 논리 단위 또는 _리소스 그룹_으로 관리할 수 있습니다. 이러한 리소스는 **asm** 모드에서와 마찬가지로 명령줄에서 명령을 통해 만들고, 관리하고, 삭제할 수 있습니다.

**arm** 모드를 사용하면 JSON *템플릿*에서 배포 가능한 리소스 그룹의 구조와 관계를 설명하여 _선언적_ 방식으로 Azure 리소스를 관리할 수도 있습니다. 템플릿에서는 명령을 실행할 때 인라인으로 채워지거나 별도의 JSON **azuredeploy-parameters.json** 파일에 저장될 수 있는 매개 변수를 설명합니다. 이를 통해 간단히 다른 매개 변수를 지정하여 동일한 템플릿을 사용하는 새 리소스를 쉽게 만들 수 있습니다. 예를 들어 웹 사이트를 만드는 템플릿에는 사이트 이름 및 웹 사이트가 위치할 지역에 대한 매개 변수와 기타 일반 매개 변수가 있습니다.

그룹을 수정 또는 생성하는 데 템플릿을 사용한 경우 _배포_가 만들어져서 그룹에 적용됩니다. 리소스 관리자에 대한 자세한 내용은 [Azure 리소스 관리자 개요](../resource-group-overview.md)를 참조하세요.

## 인증

Azure CLI를 통해 리소스 관리자에 대한 작업을 하려면 회사 또는 학교 계정을 사용하여 Microsoft Azure에 인증해야 합니다. .publishsettings 파일을 통해 설치한 인증서를 사용하여 인증할 수는 없습니다.

회사 또는 학교 계정을 사용하여 인증하는 방법에 대한 자세한 내용은 [Azure CLI에서 Azure 구독에 연결](../xplat-cli-connect.md)을 참조하세요.

> [AZURE.NOTE]Azure Active Directory에서 관리하는 회사 또는 학교 계정을 사용하므로 Azure RBAC(역할 기반 액세스 제어)를 사용하여 Azure 리소스 액세스 및 사용을 관리할 수도 있습니다. 자세한 내용은 [리소스에 대한 액세스 관리 및 감사](../resource-group-rbac.md)를 참조하세요.

## **arm** 모드 설정

리소스 관리자 모드는 기본적으로 사용하도록 설정되어 있지 않으므로 다음 명령으로 Azure CLI 리소스 관리자 명령을 사용하도록 설정해야 합니다.

	azure config mode arm

>[AZURE.NOTE]Azure 리소스 관리자 모드 및 Azure 서비스 관리 모드는 함께 사용할 수 없습니다. 즉, 한 모드에서 만든 리소스는 다른 모드에서 관리할 수 없습니다.

## 위치 찾기

대부분의 **arm** 명령은 리소스를 만들거나 찾을 올바른 위치가 필요합니다. 명령을 사용하여 모든 사용 가능한 위치를 찾을 수 있습니다.

	azure location list

"미국 서부", "미국 동부" 등 지역에 관련된 위치가 나열됩니다.

## 리소스 그룹 만들기

리소스 그룹은 네트워크, 저장소 및 기타 리소스의 논리적 그룹화입니다. **arm** 모드의 거의 모든 명령에 리소스 그룹이 필요합니다. 예를 들어 다음 명령을 사용하여 _testrg_라는 리소스 그룹을 만들 수 있습니다.

	azure group create -n "testrg" -l "West US"

그런 다음 이 그룹에 리소스를 추가하고 이 그룹을 사용하여 새 가상 컴퓨터를 구성할 수 있습니다.

## 가상 컴퓨터 만들기

**arm** 모드에서 가상 컴퓨터를 만드는 방법은 다음 두 가지입니다.

1. 개별 Azure CLI 명령 사용
2. 리소스 그룹 템플릿 사용

이러한 방법을 사용하여 시작하기 전에 리소스 그룹을 하나 이상 만들어야 합니다.

### 개별 Azure CLI 명령 사용

요구 사항에 맞는 가상 컴퓨터를 구성하고 만드는 데 기본적으로 사용되는 접근 방식입니다. **arm** 모드에서는 네트워킹과 같은 일부 필수 리소스를 먼저 구성해야 **vm create** 명령을 사용할 수 있습니다.

>[AZURE.NOTE]명령줄에서 처음으로 구독에 대해 리소스를 만드는 경우 특정 리소스 공급자를 등록하라는 메시지가 표시될 수 있습니다. 이 경우 간단히 해당 공급자를 등록하고 실패한 명령을 다시 시도하세요. 예:
>
> `azure provider register Microsoft.Storage`
>
> 다음을 실행하여 구독에 대해 등록된 공급자 목록을 확인할 수 있습니다.
>
> `azure provider list`


#### 공용 IP 리소스 만들기

모든 의미 있는 작업에 대해 새 가상 컴퓨터에 SSH를 설치하려면 공용 IP를 만들어야 합니다. 공용 IP를 만드는 작업은 간단합니다. 명령에는 리소스 그룹, 공용 IP 리소스의 이름 및 위치가 순서대로 있어야 합니다. 예:

	azure network public-ip create "testrg" "testip" "westus"

#### 네트워크 인터페이스 카드 리소스 만들기

네트워크 인터페이스 카드 또는 NIC의 경우 서브넷 및 가상 네트워크를 먼저 만들어야 합니다. **network vnet create**를 사용하여 특정 위치 및 리소스 그룹에 가상 네트워크를 만듭니다.

	azure network vnet create "testrg" "testvnet" "westus"

그러면 다음 예에서처럼 **network vnet subnet create**를 사용하여 이 가상 네트워크에 서브넷을 만들 수 있습니다.

	azure network vnet subnet create "testrg" "testvnet" "testsubnet"

이러한 리소스와 **network nic create**를 사용하여 NIC를 만들 수 있습니다.

	azure network nic create "testrg" "testnic" "westus" -k "testsubnet" -m "testvnet" -p "testip"

>[AZURE.NOTE]선택 사항이지만 **network nic create** 명령에 공용 IP 이름을 매개 변수로 전달하는 것이 매우 중요합니다. 이 IP에 NIC를 바인딩하며 나중에 이 NIC를 사용하여 만드는 가상 컴퓨터에 SSH를 설치하는 데 사용하기 때문입니다.

**network** 명령에 대한 자세한 내용은 명령줄 도움말 또는 [Azure 리소스 관리와 함께 Azure CLI 사용](azure-cli-arm-commands.md)을 참조하세요.

#### 운영 체제 이미지 찾기

현재는 이미지 게시자에 따라서만 운영 체제를 찾을 수 있습니다. 즉, 원하는 위치에서 OS 이미지 게시자 목록을 찾으려면 다음 명령을 실행해야 합니다.

	azure vm image list-publishers "westus"

그런 다음 목록에서 게시자를 선택하고 다음과 같은 명령을 실행하여 해당 게시자의 이미지 목록을 찾습니다.

	azure vm image list "westus" "CoreOS"

마지막으로, 다음과 유사한 목록에서 OS 이미지를 선택합니다.

	info:    Executing command **vm image list**
	warn:    The parameters --offer and --sku if specified will be ignored
	+ Getting virtual machine image offers (Publisher: "Canonical" Location: "westus")
	data:    Publisher  Offer        Sku          Version          Location  Urn

	data:    ---------  -----------  -----------  ---------------  --------- ----------------------------------------
	data:    CoreOS     CoreOS       Alpha        475.1.0          westus    CoreOS:CoreOS:Alpha:475.1.0
	data:    CoreOS     CoreOS       Alpha        490.0.0          westus    CoreOS:CoreOS:Alpha:490.0.0

가상 컴퓨터에서 로드하려는 이미지의 URN을 기록해 둡니다.

#### 가상 컴퓨터 만들기

이제 **vm create** 명령을 실행하고 필요한 정보를 전달하여 가상 컴퓨터를 만들 준비가 되었습니다. 이 단계에서는 공용 IP 전달이 선택 사항입니다. NIC에 이 정보가 이미 있기 때문입니다. 명령은 다음과 유사하며 여기서 _testvm_은 _testrg_ 리소스 그룹에서 만들어진 가상 컴퓨터의 이름입니다.

	azure-cli@0.8.0:/# azure vm create "testrg" "testvm" "westus" "Linux" -Q "CoreOS:CoreOS:Alpha:660.0.0" -u "azureuser" -p "Pass1234!" -N "testnic"
	info:    Executing command vm create
	+ Looking up the VM "testvm"
	info:    Using the VM Size "Standard_A1"
	info:    The [OS, Data] Disk or image configuration requires storage account
	+ Retrieving storage accounts
	info:    Could not find any storage accounts in the region "westus", trying to create new one
	+ Creating storage account "cli02f696bbfda6d83414300" in "westus"
	+ Looking up the storage account cli02f696bbfda6d83414300
	+ Looking up the NIC "testnic"
	+ Creating VM "testvm"
	info:    vm create command OK

다음 명령을 실행하여 이 가상 컴퓨터를 시작할 수 있습니다.

	azure vm start "testrg" "testvm"

또한 **ssh username@ipaddress** 명령을 사용하여 가상 컴퓨터에 SSH를 설치할 수 있습니다. 공용 IP 리소스의 IP 주소를 빠르게 찾으려면 다음 명령을 사용하세요.

	azure network public-ip show "testrg" "testip"

이 가상 컴퓨터는 **vm** 명령을 사용하여 쉽게 관리할 수 있습니다. 자세한 내용은 [Azure 리소스 관리와 함께 Azure CLI 사용](azure-cli-arm-commands.md)을 참조하세요.

### **vm quick-create** 바로 가기

새 **vm quick-create** 바로 가기는 명령을 통해 VM을 만드는 단계의 대부분을 생략합니다. 이 바로 가기는 간단한 가상 컴퓨터를 만들어 보거나 네트워킹 구성을 고려하지 않을 경우 유용합니다. 대화형 명령이며 실행하기 전 OS 이미지 URN만 찾으면 됩니다.

	azure-cli@0.8.0:/# azure vm quick-create
	info:  Executing command vm quick-create
	Resource group name: CLIRG
	Virtual machine name: myqvm
	Location name: westus
	Operating system Type [Windows, Linux]: Linux
	ImageURN (format: "publisherName:offer:skus:version"): CoreOS:CoreOS:Alpha:660.0.0
	User name: azureuser
	Password: ********
	Confirm password: ********

Azure CLI는 기본 VM 크기로 가상 컴퓨터를 만듭니다. 또한 저장소 계정, NIC, 가상 네트워크 및 서브넷, 공용 IP도 만듭니다. 가상 컴퓨터를 부팅한 후 공용 IP를 사용하여 가상 컴퓨터에 SSH를 설치할 수 있습니다.

### 리소스 그룹 템플릿 사용

#### 리소스 그룹 템플릿 찾기 및 구성

1. 템플릿 작업을 할 경우 고유한 템플릿을 만들거나 템플릿 갤러리의 템플릿을 사용하거나 [github](https://github.com/azurermtemplates/azurermtemplates)에 제공된 템플릿을 사용할 수 있습니다. 먼저 템플릿 갤러리의 **CoreOS.CoreOSStable.0.2.40-preview** 템플릿을 사용해보겠습니다. 갤러리의 사용 가능한 템플릿을 나열하려면 다음 명령을 사용합니다. 수천 개의 템플릿을 사용할 수 있으므로 결과에 페이지를 매기거나, Windows에서 **grep** 또는 **findstr**을 사용하거나 즐겨찾기 문자열 검색 명령을 사용하여 흥미로운 템플릿을 찾을 수 있습니다. 또는 쉽게 검색할 수 있도록 **--json** 옵션을 사용하고 전체 목록을 JSON 형식으로 다운로드할 수 있습니다.

		azure group template list

	응답에 게시자와 템플릿 이름이 훨씬 더 많이 있어도 다음과 비슷하게 나열됩니다.

		data:    Publisher               Name
		data:    ----------------------------------------------------------------------------
		data:    CoreOS                  CoreOS.CoreOSStable.0.2.40-preview
		data:    CoreOS                  CoreOS.CoreOSAlpha.0.2.39-preview
		data:    SUSE                    SUSE.SUSELinuxEnterpriseServer12.2.0.36-preview
		data:    SUSE                    SUSE.SUSELinuxEnterpriseServer11SP3PremiumImage0.2.54-preview

2. 템플릿에 대한 세부 정보를 보려면 다음 명령을 사용합니다.

		azure group template show CoreOS.CoreOSStable.0.2.40-preview

	템플릿에 대한 설명 정보가 반환됩니다. 현재 사용하고 있는 템플릿은 Linux 가상 컴퓨터를 만듭니다.

3. 템플릿을 선택한 후 다음 명령을 사용하여 해당 템플릿을 다운로드할 수 있습니다.

		azure group template download CoreOS.CoreOSStable.0.2.40-preview

	템플릿을 다운로드하면 요구 사항에 맞게 해당 템플릿을 사용자 지정할 수 있습니다. 예를 들어 다른 리소스를 템플릿에 추가할 수 있습니다.

	>[AZURE.NOTE]템플릿을 수정하는 경우 해당 템플릿을 사용하여 기존 리소스 그룹을 만들거나 수정하기 전에 `azure group template validate` 명령을 사용하여 템플릿의 유효성을 검사합니다.

4. 사용할 리소스 그룹 템플릿을 구성하려면 템플릿 파일을 텍스트 편집기에서 엽니다. 위쪽에 있는 **parameters** JSON 컬렉션을 확인합니다. 여기에는 템플릿별로 설명된 리소스를 만들기 위해 이 템플릿에 있어야 하는 매개 변수 목록이 있습니다. 일부 매개 변수는 기본값이 있을 수 있습니다. 다른 매개 변수는 값 형식 또는 허용되는 값의 범위를 지정합니다.

	템플릿을 사용할 경우 명령줄 매개 변수의 일부로 매개 변수를 지정하거나 매개 변수 값이 포함된 파일을 지정하여 매개 변수를 지정할 수 있습니다. 템플릿의 **parameters** 섹션에 직접 **value** 필드를 쓸 수도 있지만 이렇게 하면 템플릿이 특정 배포에 강력하게 바인딩되어 템플릿을 다시 사용하기가 어려워집니다. 어느 방법을 사용하든 매개 변수가 JSON 형식이어야 하며, 기본값이 없는 그러한 키에 대한 고유한 값을 제공해야 합니다.

	예를 들어 **CoreOS.CoreOSStable.0.2.40-preview** 템플릿의 매개 변수가 포함된 파일을 만들려면 다음 데이터를 사용하여 이름이 **params.json**인 파일을 만듭니다. 이 샘플에 사용된 값을 고유한 값으로 바꾸세요. **Location**은 **North Europe** 또는 **South Central US**와 같이 가까이 있는 Azure 지역을 지정해야 합니다. 이 예에서는 **West US**를 사용합니다.

		{
		  "newStorageAccountName": {
			"value": "testStorage"
		  },
		  "newDomainName": {
			"value": "testDomain"
		  },
		  "newVirtualNetworkName": {
			"value": "testVNet"
		  },
		  "vnetAddressSpace": {
			"value": "10.0.0.0/11"
		  },
		  "hostName": {
			"value": "testHost"
		  },
		  "userName": {
			"value": "azureUser"
		  },
		  "password": {
			"value": "Pass1234!"
		  },
		  "location": {
			"value": "West US"
		  },
		  "hardwareSize": {
			"value": "Medium"
		  }
	    }

5. **params.json** 파일을 저장한 후 다음 명령을 사용하여 템플릿에 따라 새 리소스 그룹을 만듭니다. `-e` 매개 변수는 이전 단계에서 만든 **params.json** 파일을 지정합니다. **testRG**는 사용하려는 그룹 이름으로 바꾸고 **testDeploy**는 배포 이름으로 바꿉니다. 위치는 **params.json** 템플릿 매개 변수 파일에 지정된 것과 동일해야 합니다.

		azure group create "testRG" "West US" -f CoreOS.CoreOSStable.0.2.40-preview.json -d "testDeploy" -e params.json

	이 명령은 배포가 업로드된 후, 그룹의 리소스에 적용되기 전에 OK를 반환합니다. 배포 상태를 확인하려면 다음 명령을 사용합니다.

		azure group deployment show "testRG" "testDeploy"

	**ProvisioningState**에서 배포 상태를 보여 줍니다.

	배포에 성공하면 아래와 비슷한 출력이 표시 됩니다.

		azure-cli@0.8.0:/# azure group deployment show testRG testDeploy
		info:    Executing command group deployment show
		+ Getting deployments
		data:    DeploymentName     : testDeploy
		data:    ResourceGroupName  : testRG
		data:    ProvisioningState  : Running
		data:    Timestamp          : 2015-04-27T07:49:18.5237635Z
		data:    Mode               : Incremental
		data:    Name                   Type          Value
		data:    ---------------------  ------------  ----------------
		data:    newStorageAccountName  String        testStorage
		data:    newDomainName          String        testDomain
		data:    newVirtualNetworkName  String        testVNet
		data:    vnetAddressSpace       String        10.0.0.0/11
		data:    hostName               String        testHost
		data:    userName               String        azureUser
		data:    password               SecureString  undefined
		data:    location               String        West US
		data:    hardwareSize           String        Medium
		info:    group deployment show command OK

	>[AZURE.NOTE]구성이 올바르지 않아서 장기 실행 배포를 중지해야 하는 경우 다음 명령을 사용합니다.
	>
	> `azure group deployment stop "testRG" "testDeploy"`
	>
	> 배포 이름을 지정하지 않은 경우 이름이 템플릿 파일의 이름을 기반으로 하여 자동으로 만들어집니다. 그리고 `azure group create` 명령 출력의 일부로 반환됩니다.

6. 그룹을 보려면 다음 명령을 사용합니다.

		azure group show "testRG"

	이 명령을 사용하면 그룹의 리소스에 대한 정보가 반환됩니다. 그룹이 여러 개인 경우 `azure group list` 명령을 사용하여 그룹 이름 목록을 검색한 후 `azure group show` 명령을 사용하여 특정 그룹의 세부 정보를 확인할 수 있습니다.

7. 템플릿 라이브러리에서 다운로드하지 않고 github에서 직접 최신 템플릿을 사용할 수도 있습니다. [Github.com](http://www.github.com)을 열고 AzureRmTemplates를 검색하세요. AzureRmTemplates 리포지토리를 선택하고 관심 있는 템플릿, 예를 들어 _101-simple-vm-from-image_를 찾습니다. 템플릿을 클릭하면 여러 파일 중 **azuredeploy.json**이 템플릿에 포함되어 있는 것을 볼 수 있습니다. 이것이 **--template-url** 옵션을 통해 명령에서 사용할 템플릿입니다. 이 템플릿을 _raw_ 모드로 열고 브라우저의 주소 표시줄에 표시되는 URL 복사합니다. 그러면 템플릿 라이브러리에서 다운로드하지 않고 다음과 유사한 명령에서 직접 이 URL을 사용하여 배포를 만들 수 있습니다.

		azure group deployment create "testDeploy" -g "testResourceGroup" --template-uri https://raw/githubusercontent.com/azurermtemplates/azurermtemplates/master/101-simple-vm-from-image/azuredeploy.json

	> [AZURE.NOTE]json 템플릿을 _raw_ 모드로 여는 것이 중요합니다. 브라우저의 주소 표시줄에 표시되는 URL이 일반 모드에서 표시되는 URL과 다릅니다. 파일을 _원시_ 모드로 열려면 github에서 파일을 볼 때 화면 위의 오른쪽 모서리에서 _원시_ 단추를 클릭하세요.

#### 리소스 작업

템플릿을 통해 구성에서 그룹 전체에 대한 변경을 선언할 수 있지만 특정 리소스에 대해서만 작업해야 하는 경우도 있습니다. `azure resource` 명령을 사용하여 이 작업을 수행할 수 있습니다.

> [AZURE.NOTE]`list` 명령이 아닌 `azure resource` 명령을 사용할 경우 `-o` 매개 변수를 사용하여 작업 중인 리소스의 API 버전을 지정해야 합니다. 어떤 API 버전을 사용해야 하는지 확실하지 않은 경우 템플릿 파일을 참조하여 리소스의 **apiVersion** 필드를 찾아봅니다.

1. 그룹의 모든 리소스를 나열하려면 다음 명령을 사용합니다.

		azure resource list "testRG"

2. 그룹 내의 개별 리소스를 보려면 다음 명령을 사용합니다.

		azure resource show "testRG" "testHost" Microsoft.ClassicCompute/virtualMachines -o "2014-06-01"

	**Microsoft.ClassicCompute/virtualMachines** 매개 변수에 유의하세요. 이 매개 변수는 정보를 요청하는 대상 리소스의 유형을 나타냅니다. 이전에 다운로드한 템플릿 파일을 살펴보면 템플릿에서 설명한 가상 컴퓨터 리소스의 유형을 정의하는 데 동일한 값을 사용한 것을 알 수 있습니다.

	이 명령을 실행하면 가상 컴퓨터 관련 정보가 반환됩니다.

3. 리소스에 대한 세부 정보를 확인하는 경우 `--json` 매개 변수를 사용하면 일부 값이 중첩 구조 또는 컬렉션으로 표시되어 더 읽기 쉽게 출력되므로 상당히 유용합니다. 다음은 show 명령의 결과를 JSON 문서로 반환하는 명령의 예입니다.

		azure resource show "testRG" "testHost" Microsoft.ClassicCompute/virtualMachines -o "2014-06-01" --json

	>[AZURE.NOTE]&gt; 문자를 사용하여 출력을 파일에 파이프하면 JSON 데이터를 파일에 저장할 수 있습니다. 예:
	>
	> `azure resource show "testRG" "testHost" Microsoft.ClassicCompute/virtualMachines -o "2014-06-01" --json > myfile.json`

4. 기존 리소스를 삭제하려면 다음 명령을 사용합니다.

		azure resource delete "testRG" "testHost" Microsoft.ClassicCompute/virtualMachines -o "2014-06-01"

## 로깅

그룹에 수행한 작업에 대해 기록된 정보를 보려면 `azure group log show` 명령을 사용합니다. 기본적으로 이 명령은 그룹에서 수행한 마지막 작업을 나열합니다. 모든 작업을 보려면 `--all` 매개 변수 옵션을 사용합니다. 마지막 배포의 경우 `--last-deployment`를 사용합니다. 특정 배포의 경우 `--deployment`를 사용하고 배포 이름을 지정합니다. 다음 예제를 실행하면 그룹 'MyGroup'에 대해 수행한 모든 작업의 로그가 반환됩니다.

	azure group log show mygroup --all

## 다음 단계

* Azure CLI(Azure 명령줄 인터페이스) 사용에 대한 자세한 내용은 [Azure CLI 설치 및 구성][clisetup]을 참조하세요.
* Azure PowerShell을 사용하는 리소스 관리자 작업에 대한 내용은 [Azure 리소스 관리자와 함께 Azure PowerShell 사용](../powershell-azure-resource-manager.md)을 참조하세요.
* Azure 포털의 리소스 관리자 작업에 대한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리][psrm]를 참조하세요.

[signuporg]: http://www.windowsazure.com/documentation/articles/sign-up-organization/
[adtenant]: http://technet.microsoft.com/library/jj573650#createAzureTenant
[portal]: https://manage.windowsazure.com/
[clisetup]: ../xplat-cli.md
[psrm]: http://go.microsoft.com/fwlink/?LinkId=394760
 

<!---HONumber=62-->