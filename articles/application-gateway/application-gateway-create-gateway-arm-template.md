
<properties
   pageTitle="Azure Resource Manager 템플릿을 사용하여 응용 프로그램 게이트웨이 만들기 | Microsoft Azure"
   description="이 페이지에서는 Azure 리소스 관리자 템플릿을 사용하여 Azure 응용 프로그램 게이트웨이를 만드는 지침을 제공합니다."
   documentationCenter="na"
   services="application-gateway"
   authors="georgewallace"
   manager="jdial"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/09/2016"
   ms.author="gwallace"/>


# Azure 리소스 관리자 템플릿을 사용하여 응용 프로그램 게이트웨이 만들기

Azure 응용 프로그램 게이트웨이는 계층 7 부하 분산 장치입니다. 클라우드 또는 온-프레미스이든 상관없이 서로 다른 서버 간에 장애 조치(Failover), 성능 라우팅 HTTP 요청을 제공합니다. 응용 프로그램 게이트웨이의 응용 프로그램 전달 기능에는 HTTP 부하 분산, 쿠키 기반 세션 선호도, SSL(Secure Sockets Layer) 오프로드 등이 있습니다.

> [AZURE.SELECTOR]
- [Azure 포털](application-gateway-create-gateway-portal.md)
- [Azure Resource Manager PowerShell](application-gateway-create-gateway-arm.md)
- [Azure 클래식 PowerShell](application-gateway-create-gateway.md)
- [Azure Resource Manager 템플릿](application-gateway-create-gateway-arm-template.md)

<BR>

GitHub에서 기존 Azure Resource Manager 템플릿을 다운로드한 후 수정하고 GitHub, PowerShell 및 Azure CLI에서 템플릿을 배포하는 방법에 알아봅니다.

변경하지 않고 GitHub에서 직접 Azure 리소스 관리자 템플릿을 배포하는 경우 GitHub에서 템플릿 배포로 건너뜁니다.


## 시나리오

이 시나리오에서는

- 두 인스턴스를 사용하여 응용 프로그램 게이트웨이를 만듭니다.
- 예약된 CIDR 블록이 10.0.0.0/16이고 이름이 VirtualNetwork1인 가상 네트워크를 만듭니다.
- CIDR 블록으로 10.0.0.0/28을 사용하는 Appgatewaysubnet이라고 하는 서브넷을 만듭니다.
- 트래픽을 부하 분산하려는 웹 서버에 대해 이전에 구성된 백 엔드 IP 2개를 설정합니다. 이 템플릿 예제에서 백 엔드 IP는 10.0.1.10 및 10.0.1.11이 됩니다.

>[AZURE.NOTE] 이러한 설정은 이 템플릿에 대한 매개 변수입니다. 템플릿을 사용자 지정하려면 규칙, 수신기 및 azuredeploy.json을 여는 SSL을 변경할 수 있습니다.



![시나리오](./media/application-gateway-create-gateway-arm-template/scenario-arm.png)



## Azure 리소스 관리자 템플릿 다운로드 및 이해

GitHub에서 가상 네트워크 및 두 개의 서브넷을 만들기 위한 기존 Azure 리소스 관리자 템플릿을 다운로드하고 원하는 대로 변경한 후 다시 사용할 수 있습니다. 이 작업을 수행하려면 다음 단계를 따르세요.

1. [응용 프로그램 게이트웨이 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/101-application-gateway-create)로 이동합니다.
2. **azuredeploy.json**을 클릭하고 **RAW**를 클릭합니다.
3. 파일을 컴퓨터의 로컬 폴더에 저장합니다.
4. Azure 리소스 관리자 템플릿에 익숙한 경우 7단계로 건너뜁니다.
5. 저장한 파일을 열고 줄 5에 있는 **parameters** 아래의 내용을 확인합니다. Azure 리소스 관리자 템플릿 매개 변수는 배포하는 동안 채울 수 있는 값에 대한 자리 표시자를 제공합니다.

	| 매개 변수 | 설명 |
	|---|---|
	| **위치** | 응용 프로그램 게이트웨이를 만들 Azure 지역 |
	| **VirtualNetwork1** | 새 가상 네트워크에 대한 이름 |
	| **addressPrefix** | CIDR 형식의 가상 네트워크에 대한 주소 공간 |
	| **ApplicationGatewaysubnet** | 응용 프로그램 게이트웨이 서브넷의 이름 |
	| **subnetPrefix** | 응용 프로그램 게이트웨이 서브넷에 대한 CIDR 블록 |
	| **skuname** | SKU 인스턴스 크기 |
	| **용량** | 인스턴스 수 |
	| **backendaddress1** | 첫 번째 웹 서버의 IP 주소 |
	| **backendaddress2** | 두 번째 웹 서버의 IP 주소 |


>[AZURE.IMPORTANT] GitHub에서 유지 관리되는 Azure 리소스 관리자 템플릿은 시간이 지나면서 달라질 수 있습니다. 템플릿을 사용하기 전에 확인합니다.

6. **resources** 아래의 내용을 확인하고 다음을 검토합니다.

	- **type**. 템플릿에 의해 생성되는 리소스의 유형입니다. 이 경우, 형식은 응용 프로그램 게이트웨이를 나타내는 **Microsoft.Network/applicationGateways**입니다.
	- **name**. 리소스의 이름입니다. **[parameters('applicationGatewayName')]**이 사용됩니다. 이것은 해당 이름이 배포 중에 사용자 또는 매개 변수 파일에 의한 입력으로 제공됨을 의미합니다.
	- **properties**. 리소스의 속성 목록입니다. 이 템플릿은 응용 프로그램 게이트웨이를 만드는 동안 가상 네트워크 및 공용 IP 주소를 사용합니다.

7. [https://github.com/Azure/azure-quickstart-templates/blob/master/101-application-gateway-create/](https://github.com/Azure/azure-quickstart-templates/blob/master/101-application-gateway-create)로 이동합니다.
8. **azuredeploy-paremeters.json**을 클릭하고 **RAW**를 클릭합니다.
9. 파일을 컴퓨터의 로컬 폴더에 저장합니다.
10. 저장한 파일을 열고 매개 변수 값을 편집합니다. 다음 값을 사용하여 이 시나리오에 설명된 응용 프로그램 게이트웨이를 배포합니다.

		{
		  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
		{
    	"location" : {
        "value" : "West US"
    	},
    	"addressPrefix": {
        "value": "10.0.0.0/16"
    	},
    	"subnetPrefix": {
        "value": "10.0.0.0/24"
    	},
    	"skuName": {
        "value": "Standard_Small"
    	},
    	"capacity": {
        "value": 2
    	},
    	"backendIpAddress1": {
        "value": "10.0.1.10"
    	},
    	"backendIpAddress2": {
        "value": "10.0.1.11"
    	}
		}

11. 파일을 저장합니다. [JSlint.com](http://www.jslint.com/) 같은 JSON 유효성 검사 도구를 사용하여 JSON 템플릿과 매개 변수 템플릿을 테스트할 수 있습니다.

## PowerShell을 사용하여 Azure 리소스 관리자 템플릿 배포

Azure PowerShell을 처음 사용하는 경우 [Azure PowerShell을 설치 및 구성하는 방법](../powershell-install-configure.md)을 참조하고 지침을 따르면서 Azure에 로그인하고 구독을 선택합니다.

### 1단계

	Login-AzureRmAccount



### 2단계

계정에 대한 구독을 확인합니다.

	Get-AzureRmSubscription

자격 증명을 사용하여 인증하라는 메시지가 표시됩니다.<BR>

### 3단계

사용할 Azure 구독을 선택합니다. <BR>


	Select-AzureRmSubscription -Subscriptionid "GUID of subscription"


### 4단계


필요한 경우 **New-AzureResourceGroup** cmdlet을 사용하여 리소스 그룹을 만듭니다. 아래 예제에서 미국 동부 위치에 AppgatewayRG라고 하는 새 리소스 그룹을 만듭니다.

	New-AzureRmResourceGroup -Name AppgatewayRG -Location "East US"
		VERBOSE: 5:38:49 PM - Created resource group 'AppgatewayRG' in location 'eastus'


		ResourceGroupName : AppgatewayRG
		Location          : eastus
		ProvisioningState : Succeeded
		Tags              :
		Permissions       :
	                 Actions  NotActions
	                 =======  ==========
	                  *

		ResourceId        : /subscriptions/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx/resourceGroups/AppgatewayRG

**New-AzureRmResourceGroupDeployment** cmdlet을 실행하고 위에서 다운로드한 후 수정한 이전의 템플릿 및 매개 변수 파일을 사용하여 새 가상 네트워크를 배포합니다.

	New-AzureRmResourceGroupDeployment -Name TestAppgatewayDeployment -ResourceGroupName AppgatewayRG `
 		-TemplateFile C:\ARM\azuredeploy.json -TemplateParameterFile C:\ARM\azuredeploy-parameters.json

명령줄에 의해 생성된 출력은 다음과 같습니다.

	DeploymentName    : testappgatewaydeployment
	ResourceGroupName : appgatewayRG
	ProvisioningState : Succeeded
	Timestamp         : 9/19/2015 1:49:41 AM
	Mode              : Incremental
	TemplateLink      :
	Parameters        :
				Name             Type                       Value
				===============  =========================  ==========
				location         String                     East US
				addressPrefix    String                     10.0.0.0/16
				subnetPrefix     String                     10.0.0.0/24
				skuName          String                     Standard_Small
				capacity         Int                        2
				backendIpAddress1  String                     10.0.1.10
				backendIpAddress2  String                     10.0.1.11

	Outputs           :


## Azure CLI를 사용하여 Azure 리소스 관리자 템플릿 배포

Azure CLI를 사용하여 다운로드한 Azure 리소스 관리자 템플릿을 배포하려면 다음 단계를 수행합니다.

### 1단계

Azure CLI를 처음 사용하는 경우 [Azure CLI 설치 및 구성](../xplat-cli-install.md)을 참조하고 Azure 계정 및 구독을 선택하는 부분까지 관련 지침을 따릅니다.
### 2단계

아래와 같이 **azure config mode** 명령을 실행하여 Resource Manager 모드로 전환합니다.

	azure config mode arm

다음은 위의 명령에 대해 예상된 출력입니다.

	info:	New mode is arm

### 3단계

필요한 경우 아래와 같이 **azure group create** 명령을 실행하여 새 리소스 그룹을 만듭니다. 명령의 출력을 확인합니다. 출력 다음에 표시되는 목록은 사용되는 매개 변수를 설명합니다. 리소스 그룹에 대한 자세한 내용은 [Azure Resource Manager 개요](../resource-group-overview.md)를 참조하세요.

	azure group create -n appgatewayRG -l eastus

**-n (or --name)**. 새 리소스 그룹의 이름입니다. 이 시나리오에서는 *appgatewayRG*입니다.

**-l(또는 --location)**. 새 리소스 그룹이 생성되는 Azure 지역입니다. 이 시나리오에서는 *eastus*입니다.

### 4단계

**azure group deployment create** cmdlet을 실행하고 위에서 다운로드한 후 수정한 템플릿 및 매개 변수를 사용하여 새 가상 네트워크를 배포합니다. 출력 다음에 표시되는 목록은 사용되는 매개 변수를 설명합니다.

	azure group deployment create -g appgatewayRG -n TestAppgatewayDeployment -f C:\ARM\azuredeploy.json -e C:\ARM\azuredeploy-parameters.json

다음은 위의 명령에 대해 예상된 출력입니다.

	azure group deployment create -g appgatewayRG -n TestAppgatewayDeployment -f C:\ARM\azuredeploy.json -e C:\ARM\azuredeploy-parameters.json
	info:    Executing command group deployment create
	+ Initializing template configurations and parameters
	+ Creating a deployment
	info:    Created template deployment "TestAppgatewayDeployment"
	+ Waiting for deployment to complete
	data:    DeploymentName     : TestAppgatewayDeployment
	data:    ResourceGroupName  : appgatewayRG
	data:    ProvisioningState  : Succeeded
	data:    Timestamp          : 2015-09-21T20:50:27.5129912Z
	data:    Mode               : Incremental
	data:    Name               Type    Value
	data:    -----------------  ------  --------------
	data:    location           String  East US
	data:    addressPrefix      String  10.0.0.0/16
	data:    subnetPrefix       String  10.0.0.0/24
	data:    skuName            String  Standard_Small
	data:    capacity           Int     2
	data:    backendIpAddress1  String  10.0.1.10
	data:    backendIpAddress2  String  10.0.1.11
	info:    group deployment create command OK

**-g (or --resource-group)**. 새 가상 네트워크가 만들어지는 리소스 그룹의 이름입니다.

**-f(또는 --template-file)**. Azure 리소스 관리자 템플릿 파일에 대한 경로입니다.

**-e(또는 --parameters-file)**. Azure 리소스 관리자 매개 변수 파일에 대한 경로입니다.

## 클릭하여 배포를 사용하여 Azure 리소스 관리자 템플릿 배포

클릭하여 배포는 Azure 리소스 관리자 템플릿을 사용하는 다른 방법입니다. Azure 포털에서 템플릿을 사용하는 쉬운 방법입니다.


### 1단계
[공용 IP를 사용하여 응용 프로그램 게이트웨이 만들기](https://azure.microsoft.com/documentation/templates/101-application-gateway-public-ip/)로 이동합니다.


### 2단계

**Deploy to Azure**를 클릭합니다.

![Azure에 배포](./media/application-gateway-create-gateway-arm-template/deploytoazure.png)

### 3단계

포털에서 배포 템플릿에 대한 매개 변수를 채우고 **확인**을 클릭합니다.

![매개 변수](./media/application-gateway-create-gateway-arm-template/ibiza1.png)

### 4단계

**약관**을 선택하고 **구입**을 클릭합니다.

### 5단계

사용자 지정 배포 블레이드에서 **만들기**를 클릭합니다.



## 다음 단계

SSL 오프로드를 구성하려는 경우 [SSL 오프로드에 대해 응용 프로그램 게이트웨이 구성](application-gateway-ssl.md)을 참조하세요.

내부 부하 분산 장치에서 사용되도록 응용 프로그램 게이트웨이를 구성하려면 [ILB(내부 부하 분산 장치)를 사용하여 응용 프로그램 게이트웨이 만들기](application-gateway-ilb.md)를 참조하세요.

보다 자세한 내용을 원한다면 일반적 부하 분산 옵션을 참조:

- [Azure 부하 분산 장치](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Azure 트래픽 관리자](https://azure.microsoft.com/documentation/services/traffic-manager/)

<!---HONumber=AcomDC_0824_2016-->