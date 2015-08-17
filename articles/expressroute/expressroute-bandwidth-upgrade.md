<properties 
   pageTitle="동적으로 Express 경로 대역폭 업그레이드 | Microsoft Azure"
   description="가동 중지 시간 없이 동적으로 Express 경로 회로의 대역폭 크기를 늘리는 방법."
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
   ms.date="06/03/2015"
   ms.author="cherylmc" />

# 가동 중지 시간 없이 동적으로 Express 경로 회로 대역폭 업그레이드

중단 시간 없이 Express 경로 로의 크기를 늘릴 수 있습니다. 이러한 지침에는 PowerShell을 사용하여 Express 경로 회로의 대역폭을 업데이트 하는데 도움이 됩니다. 제한 및 제한 사항에 대한 자세한 내용은 [Express 경로 FAQ](expressroute-faqs.md) 페이지를 참조하세요.

##  필수 구성 요소

구성을 시작하기 전에 다음 전제 조건을 충족하는지 확인합니다.

- Azure 구독
- 최신 버전의 Azure PowerShell
- 구성되어 작동 중인 활성 Express 경로 회로


##  PowerShell을 사용하여 설정 구성

Azure PowerShell은 Azure에서 작업의 배포와 관리를 제어하고 자동화하기 위해 사용할 수 있는 강력한 스크립팅 환경입니다. 자세한 내용은 [MSDN](https://msdn.microsoft.com/library/windowsazure/jj156055.aspx)의 PowerShell 설명서를 참조하세요.

1. **ExpressRoute에 대한 PowerShell 모듈을 가져옵니다.**

	    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
	    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

2. **Express 경로 회로의 세부 정보 가져오기**

	다음 PowerShell Commandlet을 사용하여 Express 경로 회로에 대한 세부 정보를 얻을 수 있습니다.
		

    	PS C:\> Get-AzureDedicatedCircuit
	
	이 명령은 구독에서 만든 모든 회로 목록을 반환합니다. 서비스 키가 있는 경우, 특정 Express 경로 회로에 대한 세부 정보를 얻으려면 다음 명령을 사용할 수 있습니다.

		 PS C:\> Get-AzureDedicatedCircuit -ServiceKey <skey>

	<skey>를 실제 서비스 키로 대체합니다.
	
		PS C:\> Get-AzureDedicatedCircuit -ServiceKey *********************************

		Bandwidth                        : 200
		CircuitName                      : TestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Standard
		Status                           : Enabled


3. **Microsoft 측 Express 경로 회로의 대역폭 증가**
	
	공급자에 대해 지원되는 대역폭 옵션은 [Express 경로 FAQ](expressroute-faqs.md) 페이지를 확인하세요. 기존 회로의 크기보다 큰 모든 크기를 선택할 수 있습니다. 필요한 크기를 선택하면, 다음 명령을 사용하여 회로 크기를 조정할 수 있습니다.

		PS C:\> Set-AzureDedicatedCircuitProperties -ServiceKey ********************************* -Bandwidth 1000
		
		Bandwidth                        : 1000
		CircuitName                      : TestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Standard
		Status                           : Enabled

	회로의 크기는 Microsoft 쪽에서 조정됩니다. 이 지점에서 업데이트된 대역폭 옵션에 대한 요금을 청구하기 시작합니다.

4. **서비스 제공자 측의 Express 경로 회로 대역폭 증가**

	연결 공급자(NSP/EXP)에게 문의하고 업데이트된 대역폭에 대한 정보를 제공합니다. 작업을 완료하려면 서비스 공급자가 규정 한 주문 업데이트 프로세스를 따릅니다.

 

<!---HONumber=August15_HO6-->