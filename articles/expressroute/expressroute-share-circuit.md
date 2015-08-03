<properties 
   pageTitle="여러 구독에서 Express 경로 회로 공유 | Microsoft Azure"
   description="이 문서에서는 여러 Azure 구독에서 Express 경로 회로를 공유하는 과정을 안내합니다."
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
   ms.date="07/20/2015"
   ms.author="cherylmc" />

# 여러 구독에서 Express 경로 회로 공유

여러 구독에서 단일 Express 경로 회로를 공유할 수 있습니다. **그림 1**에는 여러 구독 작업에서 Express 경로 회로를 공유하는 방법의 간단한 계통도가 나와 있습니다.

큰 구름 안에 있는 각각의 작은 구름은 한 조직 내의 여러 부서에 속하는 구독을 나타내는 데 사용됩니다. 조직 내의 각 부서는 자체 구독을 사용하여 서비스를 배포하되, 단일 Express 경로 전용 회로를 공유하여 회사 네트워크로 다시 연결할 수 있습니다. 단일 부서(이 예제에서는 IT)가 Express 경로 전용 회로를 소유할 수 있습니다. 전용 회로에 대한 연결 및 대역폭 요금은 전용 회로 소유자에게 적용됩니다. 조직 내의 기타 구독도 Express 경로 회로를 사용할 수 있습니다.

**그림 1**

![구독 공유](./media/expressroute-share-circuit/IC766124.png)

## 관리

*회로 소유자*는 Express 경로 전용 회로를 만드는 구독의 관리자/공동 관리자입니다. 회로 소유자는 자신이 소유한 전용 회로를 워크플로 다이어그램에서 *회로 사용자*로 지칭되는 다른 구독의 관리자/공동 관리자가 사용하도록 권한을 부여할 수 있습니다. 조직의 전용 회로 사용 권한을 부여받은 회로 사용자는 권한이 부여된 후 구독의 VNet을 전용 회로에 연결할 수 있습니다.

회로 소유자는 언제든지 부여된 권한을 수정하고 해지할 수 있습니다. 권한 부여를 해지하면 액세스가 해지된 구독에서 모든 링크가 삭제됩니다.

## 워크플로

1. 회로 소유자는 다른 구독 관리자에게 지정한 회로를 사용할 수 있는 권한을 부여합니다. 아래 예제에서 회로(Contoso IT) 관리자는 다른 구독(Contoso Sales) 관리자가 Microsoft ID를 지정하여 회로에 대해 최대 2개의 VNET 연결을 설정할 수 있도록 합니다. 다음 cmdlet은 지정된 Microsoft ID로 전자 메일을 보내지 않습니다. 회로 소유자는 권한 부여가 완료된 사실을 다른 구독 소유자에게 명시적으로 알려야 합니다.

		PS C:\> New-AzureDedicatedCircuitLinkAuthorization -ServiceKey '6ed7e310-1a02-4261-915f-6ccfedc416f1' -Description 'SalesTeam' -Limit 2 -MicrosoftIds 'salesadmin@contoso.com'
		
		Description         : SalesTeam 
		Limit               : 2 
		LinkAuthorizationId : e2bc2645-6fd4-44a4-94f5-f2e43e6953ed 
		MicrosoftIds        : salesadmin@contoso.com 
		Used                : 0

1. 회로 소유자가 알림을 받으면, 허가된 구독의 관리자는 다음 cmdlet을 실행하여 회로의 서비스 키를 검색할 수 있습니다. 이 예에서 Contoso Sales의 관리자는 지정된 Microsoft ID(salesadmin@contoso.com)를 사용하여 먼저 로그인해야 합니다.

		PS C:\> Get-AzureAuthorizedDedicatedCircuit
		
		Bandwidth                        : 100
		CircuitName                      : ContosoIT
		Location                         : Washington DC
		MaximumAllowedLinks              : 2
		ServiceKey                       : 6ed7e310-1a02-4261-915f-6ccfedc416f1
		ServiceProviderName              : ###########
		ServiceProviderProvisioningState : Provisioned
		Status                           : Enabled
		UsedLinks                        : 0

1. 허가된 구독의 관리자는 다음 cmdlet을 실행하여 링크 작업을 완료합니다.

		PS C:\> New-AzureDedicatedCircuitLink –servicekey 6ed7e310-1a02-4261-915f-6ccfedc416f1 –VnetName 'SalesVNET1' 
		
			State VnetName 
			----- -------- 
			Provisioned SalesVNET1

지금까지 전반적인 내용을 알아보았습니다. Azure에서 Conto의 Sales VNet은 이제 ContosoIT에서 만들고 소유하는 회로에 연결됩니다.

## 권한 부여 관리

회로 소유자는 Azure 구독과 회로를 최대 10개까지 공유할 수 있습니다. 회로 소유자는 회로 사용이 허가된 사용자를 확인할 수 있습니다. 소유자는 언제든지 권한 부여를 취소할 수 있습니다. 회로 소유자가 LinkAuthorizationId로 식별되는 권한 부여를 해지하면 해당 권한 부여로 허가된 모든 링크가 즉시 삭제됩니다. 연결된 VNET은 Express 경로 회로를 통해 연결된 온-프레미스 네트워크에서 연결이 끊어집니다.

	PS C:\> Get-AzureDedicatedCircuitLinkAuthorization -ServiceKey: 6ed7e310-1a02-4261-915f-6ccfedc416f1 
	
	Description         : EngineeringTeam 
	Limit               : 3 
	LinkAuthorizationId : cc958457-c8c1-4f16-af09-e7f099da64bf 
	MicrosoftIds        : engadmin@contoso.com 
	Used                : 1 
	
	Description         : MarketingTeam 
	Limit               : 1 
	LinkAuthorizationId : d972726f-c7b9-4658-8598-ad3208ac9348 
	MicrosoftIds        : marketingadmin@contoso.com 
	Used                : 0 
	
	Description         : SalesTeam 
	Limit               : 2 
	LinkAuthorizationId : e2bc2645-6fd4-44a4-94f5-f2e43e6953ed 
	MicrosoftIds        : salesadmin@contoso.com 
	Used                : 2 
	
	PS C:\> Remove-AzureDedicatedCircuitLinkAuthorization -ServiceKey '6ed7e310-1a02-4261-915f-6ccfedc416f1' -AuthorizationId 'e2bc2645-6fd4-44a4-94f5-f2e43e6953ed'


아래 다이어그램에서는 권한 부여 워크플로 보여 줍니다.

![구독 공유 워크플로](./media/expressroute-share-circuit/IC759525.png)

## 다음 단계

Express 경로에 대한 자세한 내용은 [Express 경로 개요](expressroute-introduction.md)를 참조하세요.

<!---HONumber=July15_HO4-->