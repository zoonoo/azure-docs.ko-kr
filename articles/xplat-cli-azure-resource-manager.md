<properties
	pageTitle="리소스 관리자와 Azure CLI | Microsoft Azure"
	description="Mac, Linux 및 Windows용 Azure CLI를 사용하여 여러 리소스를 리소스 그룹으로 배포하세요."
	editor=""
	manager="timlt"
	documentationCenter=""
	authors="dlepow"
	services="azure-resource-manager"/>

<tags
	ms.service="azure-resource-manager"
	ms.workload="multiple"
	ms.tgt_pltfrm="command-line-interface"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/26/2015"
	ms.author="danlep"/>

# Azure 리소스 관리자에서 Mac, Linux 및 Windows용 Azure CLI 사용

> [AZURE.SELECTOR]
- [Azure CLI](xplat-cli-azure-resource-manager.md)
- [Azure PowerShell](powershell-azure-resource-manager.md)



이 문서에서는 Azure 리소스 관리자 모드에서 Mac, Linux 및 Windows용 Azure CLI(명령줄 인터페이스)를 사용하여 Azure 리소스를 만들고 관리하는 방법을 설명합니다.

>[AZURE.NOTE]명령줄에서 Azure 리소스를 만들고 관리하려면 Azure 계정이 필요합니다([무료 평가판 얻기](http://azure.microsoft.com/pricing/free-trial/)). 또한 [Azure CLI를 설치](xplat-cli-install.md)하고 [로그온해야 계정과 연결된 Azure 리소스를 사용](xplat-cli-connect.md)할 수 있습니다. 이러한 작업이 완료되면 사용할 준비가 된 것입니다.

## Azure 리소스

Azure 리소스 관리자를 통해 _리소스_(가상 컴퓨터, 데이터베이스 서버, 데이터베이스 또는 웹 사이트와 같은 사용자 관리 엔터티) 그룹을 만들어서 단일 논리 단위 또는 _리소스 그룹_으로 관리할 수 있습니다.

Azure 리소스 관리자의 장점 중 하나로 JSON *템플릿*에서 배포 가능한 리소스 그룹의 구조와 관계를 설명하여 _선언적_ 방식으로 Azure 리소스를 만들 수 있습니다. 템플릿에서는 명령을 실행할 때 인라인으로 채워지거나 별도의 JSON azuredeploy-parameters.json 파일에 저장될 수 있는 매개 변수를 식별합니다. 이를 통해 간단히 다른 매개 변수를 지정하여 동일한 템플릿을 사용하는 새 리소스를 쉽게 만들 수 있습니다. 예를 들어 웹 사이트를 만드는 템플릿에는 사이트 이름 및 웹 사이트가 위치할 지역에 대한 매개 변수와 기타 일반 설정이 있습니다.

그룹을 수정 또는 생성하는 데 템플릿을 사용한 경우 _배포_가 만들어져서 그룹에 적용됩니다. Azure 리소스 관리자에 대한 자세한 내용은 [Azure 리소스 관리자 개요](../resource-group-overview.md)를 참조하세요.

배포를 만든 후에는 클래식(서비스 관리) 배포 모델과 마찬가지로 명령줄에서 명령을 통해 개별 리소스를 관리할 수 있습니다. 예를 들어 Azure 리소스 관리자 CLI 명령을 사용하여 [Azure 리소스 관리자 가상 컴퓨터](../virtual-machines/virtual-machines-deploy-rmtemplates-azure-cli.md) 같은 리소스를 시작, 중지 또는 삭제할 수 있습니다.

## 인증

Azure CLI를 통해 Azure 리소스 관리자 작업을 수행하려면 회사 또는 학교 계정(조직 계정)이나 Microsoft 계정(CLI 버전 0.9.10부터 해당)을 사용하여 Microsoft Azure에 인증해야 합니다. .publishsettings 파일을 통해 설치한 인증서를 사용하여 인증하는 방법은 이 모드에서 사용할 수 없습니다.

Microsoft Azure에 인증하는 방법에 대한 자세한 내용은 [Azure CLI에서 Azure 구독에 연결](xplat-cli-connect.md)을 참조하세요.

>[AZURE.NOTE]Azure Active Directory에서 관리하는 회사 또는 학교 계정을 사용하는 경우 Azure RBAC(역할 기반 액세스 제어)를 사용하여 Azure 리소스 액세스 및 사용을 관리할 수도 있습니다. 자세한 내용은 [Azure 역할 기반 액세스 제어](./active-directory/role-based-access-control-configure.md)를 참조하세요.

## Azure 리소스 관리자 모드 설정

Azure 리소스 관리자 모드는 기본적으로 사용되지 않으므로 다음 명령을 통해 Azure CLI 리소스 관리자 명령을 사용하도록 설정해야 합니다.

	azure config mode arm

>[AZURE.NOTE]Azure 리소스 관리자 모드 및 Azure 서비스 관리 모드는 함께 사용할 수 없습니다. 즉, 한 모드에서 만든 리소스는 다른 모드에서 관리할 수 없습니다.

## 위치 찾기

대부분의 Azure 리소스 관리자 명령은 리소스를 만들거나 찾을 올바른 위치가 필요합니다. 다음 명령을 통해 여러 Azure 리소스에 사용 가능한 모든 위치를 찾을 수 있습니다.

	azure location list

이 명령은 "미국 서부", "미국 동부" 등과 같이 Azure 리소스 및 Azure 리소스를 사용 가능한 Azure 지역을 나열합니다.

## 리소스 그룹 만들기

리소스 그룹은 네트워크, 저장소 및 기타 리소스의 논리적 그룹화입니다. Azure 리소스 관리자 모드의 거의 모든 명령에 리소스 그룹이 필요합니다. 예를 들어 다음 명령을 사용하여 _testRG_라는 리소스 그룹을 만들 수 있습니다.

	azure group create -n "testRG" -l "West US"

그런 다음 이 그룹에 리소스를 추가하고 이 그룹을 사용하여 새 가상 컴퓨터 같은 리소스를 구성할 수 있습니다.


## 리소스 그룹 템플릿 사용

### 리소스 그룹 템플릿을 찾아서 구성

템플릿으로 작업할 경우 [고유한 템플릿을 만들거나](resource-group-authoring-templates.md) [템플릿 갤러리](https://azure.microsoft.com/documentation/templates/)의 템플릿 중 하나를 사용할 수 있습니다. 템플릿 갤러리의 템플릿은 [GitHub](https://github.com/Azure/azure-quickstart-templates)에서도 제공됩니다.

새 템플릿 만들기는 이 기사의 범위를 벗어나기 때문에 우선 [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-linux-vm)에서 제공하는 _101-simple-vm-from-image_ 템플릿을 사용하겠습니다. 기본적으로 이 템플릿은 미국 서부 지역의 단일 서브넷을 사용하여 새로운 가상 네트워크에 단일 Ubuntu 14.04.2-LTS 가상 컴퓨터를 만듭니다. 사용자는 이 템플릿을 사용할 수 있도록 다음 매개 변수 몇 개만 지정하면 됩니다.

* 고유한 저장소 계정 이름
* VM의 관리 사용자 이름
* 암호
* VM의 도메인 이름

>[AZURE.TIP]다음 단계는 Azure CLI를 통해 VM 템플릿을 사용하는 여러 방법 중 하나입니다. 다른 예는 [Azure 리소스 관리자 템플릿 및 Azure CLI를 사용하여 가상 컴퓨터 배포 및 관리](../virtual-machines/virtual-machines-deploy-rmtemplates-azure-cli.md)를 참조하세요.

1. [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-linux-vm)에서 로컬 컴퓨터의 작업 폴더로 azuredeploy.json 및 azuredeploy.parameters.json 파일을 다운로드합니다.

2. 텍스트 편집기에서 azuredeploy.parameters.json 파일을 열고 사용 중인 환경에 적합한 매개 변수 값을 입력합니다(**ubuntuOSVersion** 값은 변경하지 않고 그대로 유지).

		{
	  	"$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
	  	"contentVersion": "1.0.0.0",
	  	"parameters": {
		    "newStorageAccountName": {
		      "value": "MyStorageAccount"
		    },
		    "adminUsername": {
		      "value": "MyUserName"
		    },
		    "adminPassword": {
		      "value": "MyPassword"
		    },
		    "dnsNameForPublicIP": {
		      "value": "MyDomainName"
		    },
		    "ubuntuOSVersion": {
		      "value": "14.04.2-LTS"
		    }
		  }
		}
	```
3. azuredeploy.parameters.json 파일을 저장한 후 다음 명령을 사용하여 템플릿에 따라 새 리소스 그룹을 만듭니다. `-e` 옵션은 이전 단계에서 수정한 azuredeploy.parameters.json 파일을 지정합니다. *testRG*는 사용하려는 그룹 이름으로 바꾸고 *testDeploy*는 선택한 배포 이름으로 바꿉니다. 위치는 템플릿 매개 변수 파일에 지정된 것과 동일해야 합니다.

		azure group create "testRG" "West US" -f azuredeploy.json -d "testDeploy" -e azuredeploy.parameters.json

	이 명령은 배포가 업로드된 후, 배포가 그룹의 리소스에 적용되기 전에 OK를 반환합니다.

4. 배포 상태를 확인하려면 다음 명령을 사용합니다.

		azure group deployment show "testRG" "testDeploy"

	**ProvisioningState**에서 배포 상태를 보여 줍니다.

	배포에 성공하면 다음과 유사한 출력이 표시됩니다.

		azure-cli@0.8.0:/# azure group deployment show testRG testDeploy
		info:    Executing command group deployment show
		+ Getting deployments
		+ Getting deployments
		data:    DeploymentName     : testDeploy
		data:    ResourceGroupName  : testRG
		data:    ProvisioningState  : Running
		data:    Timestamp          : 2015-10-26T16:15:29.5562024Z
		data:    Mode               : Incremental
		data:    Name                   Type          Value
		data:    ---------------------  ------------  ---------------------
		data:    newStorageAccountName  String        MyStorageAccount
		data:    adminUsername          String        MyUserName
		data:    adminPassword          SecureString  undefined
		data:    dnsNameForPublicIP     String        MyDomainName
		data:    ubuntuOSVersion        String        14.04.2-LTS
		info:    group deployment show command OK

	>[AZURE.NOTE]구성이 올바르지 않아서 장기 실행 배포를 중지해야 하는 경우 다음 명령을 사용합니다.
	>
	> `azure group deployment stop "testRG" "testDeploy"`
	>
	> 배포 이름을 지정하지 않은 경우 이름이 템플릿 파일의 이름을 기반으로 하여 자동으로 만들어집니다. 그리고 `azure group create` 명령 출력의 일부로 반환됩니다.

	이제 지정한 도메인 이름을 사용하여 VM에 대해 SSH를 실행할 수 있습니다. VM에 연결할 때 양식 `<domainName>.<region>.cloudapp.azure.com`의 정규화된 도메인 이름(예: `MyDomainName.westus.cloudapp.azure.com`)을 사용해야 합니다.

5. 그룹을 보려면 다음 명령을 사용합니다.

		azure group show "testRG"

	이 명령을 사용하면 그룹의 리소스에 대한 정보가 반환됩니다. 그룹이 여러 개인 경우 `azure group list` 명령을 사용하여 그룹 이름 목록을 검색한 후 `azure group show` 명령을 사용하여 특정 그룹의 세부 정보를 확인합니다.

또한 컴퓨터에 템플릿을 다운로드하는 대신 [GitHub](https://github.com/Azure/azure-quickstart-templates)에서 바로 템플릿을 사용할 수도 있습니다. 이렇게 하려면 **--template-url** 옵션을 사용하여 명령에서 템플릿의 azuredeploy.json 파일에 URL을 전달하세요. URL을 가져오려면 GitHub에서 azuredeploy.json을 _원시_ 모드로 열고 브라우저의 주소 표시줄에 표시되는 URL을 복사합니다. 그런 후 다음과 비슷한 명령으로 이 URL을 직접 사용하여 배포를 만들 수 있습니다.

	azure group deployment create "testDeploy" -g "testResourceGroup" --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-linux-vm/azuredeploy.json
필요한 템플릿 매개 변수를 입력하라는 메시지가 표시 됩니다.

> [AZURE.NOTE]JSON 템플릿을 _원시_ 모드로 여는 것이 중요합니다. 브라우저의 주소 표시줄에 표시되는 URL이 일반 모드에서 표시되는 URL과 다릅니다. GitHub에서 파일을 볼 때 파일을 _원시_ 모드로 열려면 오른쪽 위 모서리에 있는 **원시**를 클릭합니다.

## 리소스 작업

템플릿을 통해 구성에서 그룹 전체에 대한 변경을 선언할 수 있지만 특정 리소스에 대해서만 작업해야 하는 경우도 있습니다. `azure resource` 명령을 사용하여 이 작업을 수행할 수 있습니다.

> [AZURE.NOTE]`list` 명령이 아닌 `azure resource` 명령을 사용할 경우 `-o` 매개 변수를 사용하여 작업 중인 리소스의 API 버전을 지정해야 합니다. 어떤 API 버전을 사용해야 하는지 확실하지 않은 경우 템플릿 파일을 참조하여 리소스의 **apiVersion** 필드를 찾아봅니다.

1. 그룹의 모든 리소스를 나열하려면 다음 명령을 사용합니다.

		azure resource list "testRG"

2. 그룹 내의 개별 리소스를 보려면 다음과 비슷한 명령을 사용합니다.

		azure resource show "testRG" "MyUbuntuVM" Microsoft.Compute/virtualMachines -o "2015-06-15"

	**Microsoft.Compute/virtualMachines** 매개 변수에 유의하세요. 이 매개 변수는 정보를 요청하는 대상 리소스의 유형을 나타냅니다. 이전에 다운로드한 템플릿 파일을 살펴보면 템플릿에서 설명한 가상 컴퓨터 리소스의 유형을 정의하는 데 동일한 값이 사용된 것을 확인할 수 있습니다.

	이 명령을 실행하면 가상 컴퓨터 관련 정보가 반환됩니다.

3. `--json` 매개 변수는 보통 리소스 세부 정보를 볼 때 유용하게 사용됩니다. 일부 값이 중첩된 구조이거나 컬렉션이기 때문에 이 매개 변수를 사용하면 출력을 읽기가 훨씬 수월합니다. 다음 예제에서는 **show** 명령의 결과를 JSON 문서로 반환하는 방법을 보여 줍니다.

		azure resource show "testRG" "MyUbuntuVM" Microsoft.Compute/virtualMachines -o "2015-06-15" --json

	>[AZURE.NOTE]&gt; 문자를 사용하여 출력을 파일에 파이프하면 JSON 데이터를 파일에 저장할 수 있습니다. 예를 들면 다음과 같습니다.
	>
	> `azure resource show "testRG" "MyUbuntuVM" Microsoft.Compute/virtualMachines -o "2015-06-15" --json > myfile.json`

4. 기존 리소스를 삭제하려면 다음과 비슷한 명령을 사용합니다.

		azure resource delete "testRG" "MyUbuntuVM" Microsoft.Compute/virtualMachines -o "2015-06-15"

## 로깅

그룹에 수행한 작업에 대해 기록된 정보를 보려면 `azure group log show` 명령을 사용합니다. 기본적으로 이 명령은 그룹에 대해 수행한 마지막 작업을 나열합니다. 모든 작업을 보려면 `--all` 매개 변수 옵션을 사용합니다. 마지막 배포의 경우 `--last-deployment`를 사용합니다. 특정 배포의 경우 `--deployment`를 사용하고 배포 이름을 지정합니다. 다음 예제를 실행하면 *MyGroup* 그룹에 대해 수행한 모든 작업의 로그가 반환됩니다.

	azure group log show MyGroup --all

## 다음 단계

* Azure PowerShell을 사용하여 Azure 리소스 관리자 작업을 수행하는 방법에 대한 자세한 내용은 [Azure 리소스 관리자에서 Azure PowerShell 사용](../powershell-azure-resource-manager.md)을 참조하세요.
* Azure 포털에서 Azure 리소스 관리자 작업을 수행하는 방법에 대한 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리][psrm]를 참조하세요.

[signuporg]: http://www.windowsazure.com/documentation/articles/sign-up-organization/
[adtenant]: http://technet.microsoft.com/library/jj573650#createAzureTenant
[psrm]: http://go.microsoft.com/fwlink/?LinkId=394760

<!---HONumber=AcomDC_1223_2015-->