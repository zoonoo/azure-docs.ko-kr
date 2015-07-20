<properties 
   pageTitle="Express 경로 프리미엄 추가 기능 사용 또는 사용하지 않도록 설정하는 방법 | Microsoft Azure"
   description="Express 경로 회로에 대해 Express 경로 프리미엄 추가 기능 사용 또는 사용하지 않도록 설정하는 방법 Express 경로 프리미엄으로 Express 경로 회로에 대해 최대 10개의 가상 네트워크 및 공용 및 개인 피어링에 대해 최대 10, 000개의 경로를 추가할 수 있습니다. 또한 한 지역의 가상 네트워크를 다른 지역의 Express 경로 회로에 연결할 수 있습니다."
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
   manager="jdial"
   editor="tysonn" />
<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/02/2015"
   ms.author="cherylmc" />

# Express 경로 회로에 대한 Express 경로 프리미엄 추가 기능 구성

Express 경로 프리미엄은 아래 표시된 기능의 컬렉션입니다.

 - 증가된 라우팅 테이블은 공용 피어링 및 개인 피어링에 대해 4000개의 경로에서 경로 10, 000개의 경로로 제한합니다.
 - Express 경로 회로에 연결할 수 있는 가상 네트워크(VNets) 수가 증가합니다(기본값은 10). 
 - Microsoft 핵심 네트워크를 통해 전역 연결합니다. 이제 다른 지역의 Express 경로 회로를 지역의 VNet에 연결할 수 있습니다. **예:** 실리콘밸리에서 만든 Express 경로 회로에 서부 유럽에서에서 만든 VNet을 연결할 수 있습니다.

Express 경로 프리미엄 추가 기능에 대한 자세한 내용은 [Express 경로 FAQ](expressroute-faqs.md) 페이지를 참조하세요. 비용은 [가격 정보](http://azure.microsoft.com/pricing/details/expressroute/) 페이지를 참조하세요.

이러한 지침은 다음을 수행하는데 도움이 됩니다.

- 활성화된 프리미엄 추가 기능으로 Express 경로 회로를 만듭니다.
- 프리미엄 추가 기능을 사용하도록 기존 Express 경로 회로를 업데이트합니다.
- 회로에 대해 Express 경로 프리미엄 추가 기능을 사용하지 않도록 설정합니다.


## 활성화된 프리미엄 추가 기능으로 Express 경로 회로를 만들려면

###  시작하기 전에

구성을 시작하기 전에 다음 전제 조건을 충족하는지 확인합니다.

- Azure 구독
- 최신 버전의 Azure PowerShell

###  1. Express 경로에 대한 PowerShell 모듈 가져오기

Azure PowerShell은 Azure에서 작업의 배포와 관리를 제어하고 자동화하기 위해 사용할 수 있는 강력한 스크립팅 환경입니다. 자세한 내용은 [MSDN](https://msdn.microsoft.com/library/windowsazure/jj156055.aspx)의 PowerShell 설명서를 참조하세요.

아래 cmdlets을 사용하여 Express 경로에 대한 PowerShell 모듈을 가져옵니다.


	    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
	    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'


### 2. 활성화된 프리미엄 추가 기능으로 새 Express 경로 회로 구성

생성 시 활성화 프리미엄 추가 기능으로는 새 Express 경로 회로를 만들 수 있습니다. [NSPs](expressroute-configuring-nsps.md) 또는 [EXPs](expressroute-configuring-exps.md)로 Express 경로 회로를 만드는 방법은 지침을 따릅니다. SKU를 지정할 수 있는 새로운 AzureDedicatedCircuit cmdlet에 새로운 선택적 매개변수가 있습니다. SKU는 표준 또는 프리미엄일 수 있습니다. 기본값은 표준입니다. 프리미엄으로 SKU에서 전달하면 프리미엄 추가 기능으로 회로를 사용할 수 있습니다.


		New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location -Sku Premium


### 3. Express 경로 프리미엄 추가 기능이 사용되는지 확인합니다.
Express 경로 프리미엄 추가 기능을 회로에 사용할 수 있는지 확인할 수 있습니다. 아래 예제에서는 Express 경로 회로는 Express 경로 프리미엄 추가 기능을 사용 하지 않습니다. 추가 기능이 사용되는 경우 SKU는 ***프리미엄***으로 표시됩니다.

		PS C:> Get-AzureDedicatedCircuit -ServiceKey *********************************

		Bandwidth                        : 200
		CircuitName                      : TestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Standard
		Status                           : Enabled




## 기존 Express 경로 회로에 Express 경로 프리미엄 추가 기능을 사용하려면
이미 만든 모든 Express 경로 회로에 대한 Express 경로 프리미엄 추가 기능을 사용할 수 있습니다.


1. **Express 경로 회로의 세부 정보 가져오기**

	다음 PowerShell cmdlet을 사용하여 Express 경로 회로에 대한 세부 정보를 얻을 수 있습니다.
		

    	PS C:> Get-AzureDedicatedCircuit
	
	이 명령은 구독에서 만든 모든 회로 목록을 반환합니다. 서비스 키가 있는 경우, 특정 Express 경로 회로에 대한 세부 정보를 얻으려면 다음 명령을 사용할 수 있습니다.

		 PS C:> Get-AzureDedicatedCircuit -ServiceKey <skey>

	<skey>를 실제 서비스 키로 대체합니다.
	
		PS C:> Get-AzureDedicatedCircuit -ServiceKey *********************************

		Bandwidth                        : 200
		CircuitName                      : TestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Standard
		Status                           : Enabled


2. **회로에 대해 Express 경로 프리미엄 추가 기능 사용**


	다음 PowerShell cmdlet을 사용하여 기존 회로에 대해 Express 경로 프리미엄 추가 기능을 활성화할 수 있습니다.
	
		PS C:> Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Premium
		
		Bandwidth                        : 1000
		CircuitName                      : TestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Premium
		Status                           : Enabled

	이제 Express 경로 프리미엄 추가 기능을 사용할 수 있게 됩니다. 명령이 성공적으로 실행되는 즉시 프리미엄 추가 기능에 대한 청구가 시작됩니다.


## Express 경로 회로에 대해 Express 경로 프리미엄 추가 기능을 사용하려면

프리미엄 추가 기능을 사용하는 Express 경로 회로에 대해 Express 경로 프리미엄 추가 기능을 사용하지 않도록 설정할 수 있습니다.

**참고**: 이렇게 설정하기 전에 프리미엄 추가 기능 목록에 나열된 기능을 사용 하여 중지했는지 확인해야 합니다. 10 Vnet 보다 많이 회로에 연결되어 있는 경우 프리미엄 추가 기능을 사용하지 않도록 설정하는 요청이 실패합니다. 또한 프리미엄 추가 기능을 사용하지 않도록 설정하면 5000개 보다 많은 라우트를 보급하는 경우 BGP 세션이 짧아지게 됩니다.

1. **Express 경로 회로의 세부 정보 가져오기**

	다음 PowerShell cmdlet을 사용하여 Express 경로 회로에 대한 세부 정보를 얻을 수 있습니다.
		

    	PS C:> Get-AzureDedicatedCircuit
	
	이 명령은 구독에서 만든 모든 회로 목록을 반환합니다. 서비스 키가 있는 경우, 특정 Express 경로 회로에 대한 세부 정보를 얻으려면 다음 명령을 사용할 수 있습니다.

		 PS C:> Get-AzureDedicatedCircuit -ServiceKey <skey>

	<skey>를 실제 서비스 키로 대체합니다.
	
		PS C:> Get-AzureDedicatedCircuit -ServiceKey *********************************

		Bandwidth                        : 200
		CircuitName                      : TestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Premium
		Status                           : Enabled


3. **회로에 대해 Express 경로 프리미엄 추가 기능 사용 안함**


	다음 PowerShell cmdlet을 사용하여 기존 회로에 대해 Express 경로 프리미엄 추가 기능을 사용하지 않도록 설정할 수 있습니다.
	
		PS C:> Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Standard
		
		Bandwidth                        : 1000
		CircuitName                      : TestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Premium
		Status                           : Standard

	이제 Express 경로 프리미엄 추가 기능을 사용하지 않도록 설정했습니다.


 

<!---HONumber=July15_HO2-->