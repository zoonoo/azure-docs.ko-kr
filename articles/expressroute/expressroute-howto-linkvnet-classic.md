<properties 
   pageTitle="가상 네트워크를 Express 경로 회로에 연결하기 | Microsoft Azure"
   description="이 문서는 가상 네트워크(VNets)를 Express 경로 회로에 연결하는 방법에 대한 개요를 제공합니다."
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
   manager="carolz"
   editor=""
   tags="azure-service-management"/>
<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/21/2015"
   ms.author="cherylmc" />

# Express 경로 회로에 Vnet 연결

이 문서는 가상 네트워크(VNets)를 Express 경로 회로에 연결하는 방법에 대한 개요를 제공합니다. 가상 네트워크는 같은 구독에 있을 수도 있고 다른 구독의 일부일 수도 있습니다. 이 문서는 클래식 배포 모델을 사용하여 배포된 VNet에 적용됩니다.

>[AZURE.IMPORTANT]Azure가 현재 두 가지 배포 모델인 리소스 관리자 및 클래식 모드에서 작동한다는 것을 알아야 합니다. 구성을 시작하기 전에 배포 모델 및 도구를 이해해야 합니다. 배포 모델에 대한 자세한 내용은 [Azure 배포 모델](../azure-classic-rm.md)을 참조하세요.

## 필수 구성 요소

- Azure PowerShell 모듈의 최신 버전이 필요합니다. [Azure 다운로드 페이지](http://azure.microsoft.com/downloads)의 PowerShell 섹션에서 최신 PowerShell 모듈을 다운로드할 수 있습니다. 컴퓨터를 구성하여 Azure PowerShell 모듈을 사용하는 방법에 대한 단계별 지침은 [Azure PowerShell 설치 및 구성 방법](../powershell-install-configure.md) 페이지의 지침을 수행합니다. 
- 구성을 시작 하기 전에 [필수 구성 요소](expressroute-prerequisites.md) 페이지, [라우팅 요구 사항](expressroute-routing.md) 페이지 및 [워크플로](expressroute-workflows.md) 페이지를 검토했는지 확인합니다.
- 활성화된 Express 경로 회로가 있어야 합니다. 
	- 지침을 수행하여 [Express 경로 회로를 만들고](expressroute-howto-circuit-classic.md) 연결 공급자에서 회로를 사용하도록 설정합니다. 
	- 회로에 구성된 Azure 개인 피어링이 있는지 확인합니다. 라우팅 지침에 대한 문서는 [라우팅 구성](expressroute-howto-routing-classic.md)을 참조하세요. 
	- Azure 개인 피어링을 구성해야 하며 네트워크와 Microsoft 간의 BGP 피어링은 종단 간 연결을 사용하도록 작동 상태여야 합니다.

최대 10개의 VNet을 Express 경로 회로에 연결할 수 있습니다. 모든 Express 경로 회로는 같은 지역에 있어야 합니다. Express 경로 프리미엄 추가 기능을 사용하도록 설정하면 Express 경로 회로에 많은 수의 가상 네트워크를 연결할 수 있습니다. 프리미엄 추가 기능에 대한 자세한 내용은 [FAQ](expressroute-faqs.md)을 확인합니다.

## 동일한 Azure 구독에 있는 VNet을 Express 경로 회로에 연결하기

다음 cmdlet을 사용하여 Express 경로 회로에 가상 네트워크를 연결할 수 있습니다. cmdlet을 실행하기 전에 가상 네트워크 게이트웨이가 연결을 위해 생성되고 준비되었는지 확인합니다.

	C:\> New-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
	Provisioned

## 다른 Azure 구독에 있는 VNet을 Express 경로 회로에 연결하기

여러 구독에서 Express 경로 회로를 공유할 수 있습니다. 아래 그림에는 여러 구독 작업에서 Express 경로 회로를 공유하는 방법의 간단한 계통도가 나와 있습니다. 큰 구름 안에 있는 각각의 작은 구름은 한 조직 내의 여러 부서에 속하는 구독을 나타내는 데 사용됩니다. 조직 내의 각 부서는 자체 구독을 사용하여 서비스를 배포하되, 단일 Express 경로 회로를 공유하여 온-프레미스 네트워크로 다시 연결할 수 있습니다. 단일 부서(이 예제에서는 IT)가 Express 경로 회로를 소유할 수 있습니다. 조직 내의 기타 구독도 Express 경로 회로를 사용할 수 있습니다.

>[AZURE.NOTE]전용 회로에 대한 연결 및 대역폭 요금은 Express 경로 회로 소유자에게 적용됩니다. 모든 가상 네트워크는 동일한 대역폭을 공유합니다.

![구독 간 연결](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### 관리

*회로 소유자*는 Express 경로 회로를 만드는 구독의 관리자/공동 관리자입니다. 회로 소유자는 자신이 소유한 전용 회로를 워크플로 다이어그램에서 *회로 사용자*로 지칭되는 다른 구독의 관리자/공동 관리자가 사용하도록 권한을 부여할 수 있습니다. 조직의 Express 경로 회로 사용 권한을 부여받은 회로 사용자는 권한이 부여된 후 구독의 VNet을 Express 경로 회로에 연결할 수 있습니다.

회로 소유자는 언제든지 부여된 권한을 수정하고 해지할 수 있습니다. 권한 부여를 해지하면 액세스가 해지된 구독에서 모든 링크가 삭제됩니다.

### 회로 소유자 작업 

#### 권한 부여 만들기
	
회로 소유자는 다른 구독 관리자에게 지정한 회로를 사용할 수 있는 권한을 부여합니다. 아래 예제에서 회로(Contoso IT) 관리자는 다른 구독(Dev-Test) 관리자가 Microsoft ID를 지정하여 회로에 대해 최대 2개의 VNET 연결을 설정할 수 있도록 합니다. 다음 cmdlet은 지정된 Microsoft ID로 전자 메일을 보내지 않습니다. 회로 소유자는 권한 부여가 완료된 사실을 다른 구독 소유자에게 명시적으로 알려야 합니다.

		PS C:\> New-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -Description "Dev-Test Links" -Limit 2 -MicrosoftIds 'devtest@contoso.com'
		
		Description         : Dev-Test Links 
		Limit               : 2 
		LinkAuthorizationId : ********************************** 
		MicrosoftIds        : devtest@contoso.com 
		Used                : 0

#### 권한 부여 검토

회로 소유자는 다음 cmdlet을 실행하여 특정 회로에 발급한 모든 권한 부여를 검토할 수 있습니다.

	PS C:\> Get-AzureDedicatedCircuitLinkAuthorization -ServiceKey: "**************************"
	
	Description         : EngineeringTeam 
	Limit               : 3 
	LinkAuthorizationId : #################################### 
	MicrosoftIds        : engadmin@contoso.com 
	Used                : 1 
	
	Description         : MarketingTeam 
	Limit               : 1 
	LinkAuthorizationId : @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@ 
	MicrosoftIds        : marketingadmin@contoso.com 
	Used                : 0 
	
	Description         : Dev-Test Links 
	Limit               : 2 
	LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&& 
	MicrosoftIds        : salesadmin@contoso.com 
	Used                : 2 
	

#### 권한 부여 업데이트하기

회로 소유자는 다음 cmdlet을 사용하여 권한 부여를 수정할 수 있습니다.

	PS C:\> set-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -AuthorizationId "&&&&&&&&&&&&&&&&&&&&&&&&&&&&"-Limit 5
		
	Description         : Dev-Test Links 
	Limit               : 5 
	LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&& 
	MicrosoftIds        : devtest@contoso.com 
	Used                : 0


#### 권한 부여 삭제하기

회로 소유자는 다음 cmdlet을 실행하여 권한 부여를 취소/삭제할 수 있습니다.

	PS C:\> Remove-AzureDedicatedCircuitLinkAuthorization -ServiceKey "*****************************" -AuthorizationId "###############################"


### 회로 사용자 작업

#### 권한 부여 검토

회로 사용자는 다음 cmdlet을 사용하여 권한 부여를 검토할 수 있습니다.

	PS C:\> Get-AzureAuthorizedDedicatedCircuit
		
	Bandwidth                        : 200
	CircuitName                      : ContosoIT
	Location                         : Washington DC
	MaximumAllowedLinks              : 2
	ServiceKey                       : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
	ServiceProviderName              : equinix
	ServiceProviderProvisioningState : Provisioned
	Status                           : Enabled
	UsedLinks                        : 0

#### 링크 권한 부여 사용하기

회로 사용자는 다음 cmdlet을 실행하여 링크 권한 부여를 사용할 수 있습니다.

	PS C:\> New-AzureDedicatedCircuitLink –servicekey "&&&&&&&&&&&&&&&&&&&&&&&&&&" –VnetName 'SalesVNET1' 
		
	State VnetName 
	----- -------- 
	Provisioned SalesVNET1

## 다음 단계

Express 경로에 대한 자세한 내용은 [Express 경로 FAQ](expressroute-faqs.md)를 참조하세요.

<!---HONumber=Oct15_HO1-->