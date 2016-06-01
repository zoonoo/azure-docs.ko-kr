<properties
   pageTitle="클래식에서 리소스 관리자로 Express 경로 회로 이동 | Microsoft Azure"
   description="이 페이지는 클래식 회로를 리소스 관리자 배포 모델로 이동하는 방법을 설명합니다."
   documentationCenter="na"
   services="expressroute"
   authors="ganesr"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/05/2016"
   ms.author="ganesr"/>


# 클래식에서 리소스 관리자 배포 모델로 Express 경로 회로 이동

## 필수 구성 요소

- Azure PowerShell 모듈의 최신 버전(버전 1.0 이상)이 필요합니다.
- 구성을 시작하기 전에 [필수 조건](expressroute-prerequisites.md), [라우팅 요구 사항](expressroute-routing.md) 및 [워크플로](expressroute-workflows.md)를 검토했는지 확인합니다.
- 계속 진행하기 전에 [클래식에서 리소스 관리자로 Express 경로 회로 이동](expressroute-move.md)에서 제공되는 정보를 검토합니다. 가능한 제한 및 제한 사항에 대해 완전히 파악해야 합니다.
- 클래식 배포 모델에서 Azure Resource Manager 배포 모델로 Azure Express 경로 회로를 이동하려는 경우 클래식 배포 모델로 완전히 구성되어 작동 가능한 회로가 있어야 합니다.
- 리소스 관리자 배포 모델로 만든 리소스 그룹이 있는지 확인합니다.

## 리소스 관리자 배포 모델로 Express 경로 회로 이동

클래식 및 리소스 관리자 배포 모델 모두에서 사용할 수 있도록 Express 경로 회로를 리소스 관리자 배포 모델로 이동해야 합니다. 다음 PowerShell 명령을 실행하여 이 작업을 수행할 수 있습니다.

### 1단계: 클래식 배포 모델에서 회로 세부 정보 수집

먼저 Express 경로 회로에 대한 정보를 수집해야 합니다.

Azure 클래식 환경에 로그인하고 서비스 키를 수집합니다. 다음 PowerShell 코드 조각을 사용하여 정보를 수집할 수 있습니다.

	# Sign in to your Azure account
	Add-AzureAccount

	# Select the appropriate Azure subscription
	Select-AzureSubscription "<Enter Subscription Name here>"

	# Import the PowerShell modules for Azure and ExpressRoute
	Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
	Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

	# Get the service keys of all your ExpressRoute circuits
	Get-AzureDedicatedCircuit

리소스 관리자 배포 모델로 이동하려는 회로의 **서비스 키**를 복사합니다.

### 2단계: 리소스 관리자 환경에 로그인하고 새 리소스 그룹 만들기

다음 코드 조각을 사용하여 새로운 리소스 그룹을 만들 수 있습니다.

	# Sign in to your Azure Resource Manager environment
	Login-AzureRmAccount

	# Select the appropriate Azure subscription
	Get-AzureRmSubscription -SubscriptionName "<Enter Subscription Name here>" | Select-AzureRmSubscription

	#Create a new resource group if you don't already have one
	New-AzureRmResourceGroup -Name "DemoRG" -Location "West US"

리소스 그룹이 이미 있는 경우 기존 리소스 그룹을 사용할 수도 있습니다.

### 3단계: 리소스 관리자 배포 모델로 Express 경로 회로 이동

이제 클래식에서 리소스 관리자 배포 모델로 Express 경로 회로를 이동할 준비가 되었습니다. 계속 진행하기 전에 [클래식에서 리소스 관리자 배포 모델로 Express 경로 회로 이동](expressroute-move.md)에서 제공되는 정보를 검토합니다.

다음 코드 조각을 실행하여 이 작업을 수행할 수 있습니다.

	Move-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "DemoRG" -Location "West US" -ServiceKey "<Service-key>"

>[AZURE.NOTE] 이동이 완료되면 이전 cmdlet에 나열된 새 이름을 사용하여 리소스 주소를 지정합니다. 회로는 기본적으로 이름이 바뀝니다.

## 두 배포 모델에 대한 Express 경로 회로 사용

배포 모델에 대한 액세스를 제어하기 전에 Express 경로 회로를 리소스 관리자 배포 모델로 이동해야 합니다.

두 배포 모델에 모두 액세스하려면 다음 cmdlet을 실행합니다.

    # Get details of the ExpressRoute circuit
    $ckt = Get-AzureRmExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"

    #Set "Allow Classic Operations" to TRUE
    $ckt.AllowClassicOperations = $true

    # Update circuit
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

이 작업이 성공적으로 완료되면 클래식 배포 모델의 회로를 볼 수 있습니다.

Express 경로 회로의 세부 정보를 가져오려면 다음을 실행합니다.

    get-azurededicatedcircuit

나열된 서비스 키를 볼 수 있어야 합니다. 이제 클래식 VNet에 대한 표준 클래식 배포 모델 명령 및 ARM VNET에 대한 표준 ARM 명령을 사용하여 Express 경로 회로에 대한 링크를 관리할 수 있습니다. 다음 문서에서는 Express 경로 회로에 대한 링크를 관리하는 방법을 안내합니다.

- [리소스 관리자 배포 모델에서 가상 네트워크를 Express 경로 회로에 연결](expressroute-howto-linkvnet-arm.md)
- [클래식 배포 모델에서 가상 네트워크를 Express 경로 회로에 연결](expressroute-howto-linkvnet-classic.md)


## 클래식 배포 모델에 대해 Express 경로 회로 사용 안 함

클래식 배포 모델에 대한 액세스를 사용하지 않도록 설정하려면 다음 cmdlet을 실행합니다.

    # Get details of the ExpressRoute circuit
    $ckt = Get-AzureRmExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"

    #Set "Allow Classic Operations" to FALSE
    $ckt.AllowClassicOperations = $false

    # Update circuit
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

이 작업이 성공적으로 완료되면 클래식 배포 모델의 회로를 볼 수 없습니다.

## 다음 단계

회로를 만든 후 다음을 수행합니다.

- [Express 경로 회로의 라우팅 만들기 및 수정](expressroute-howto-routing-arm.md)
- [가상 네트워크를 Express 경로 회로에 연결](expressroute-howto-linkvnet-arm.md)

<!---HONumber=AcomDC_0518_2016-->