<properties
   pageTitle="Azure VPN 게이트웨이 재설정 | Microsoft Azure"
   description="이 문서에서는 Azure VPN 게이트웨이를 재설정하는 과정을 안내합니다. 이 문서는 클래식 및 Resource Manager 배포 모델의 VPN Gateway에 모두 적용됩니다."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager,azure-service-management"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/23/2016"
   ms.author="cherylmc"/>

# PowerShell을 사용하여 Azure VPN 게이트웨이 재설정


이 문서에서는 PowerShell cmdlet을 사용하여 Azure VPN 게이트웨이를 다시 설정하는 과정을 안내합니다. 지침에는 클래식 배포 모델과 Resource Manager 배포 모델이 모두 포함되어 있습니다.

Azure VPN 게이트웨이 재설정은 하나 이상의 S2S VPN 터널에서 크로스-프레미스 VPN 연결을 잃는 경우 유용합니다. 이 상황에서 온-프레미스 VPN 장치는 모두 올바르게 작동하지만 Azure VPN 게이트웨이와 IPsec 터널을 설정할 수 없습니다.

각 Azure VPN 게이트웨이는 활성-대기 구성에서 실행 중인 두 VM 인스턴스로 구성됩니다. PowerShell cmdlet을 사용하여 게이트웨이를 다시 설정할 경우 게이트웨이가 다시 부팅된 후 크로스-프레미스 구성이 다시 적용됩니다. 게이트웨이는 기존의 공용 IP 주소를 유지합니다. 즉, Azure VPN 게이트웨이에 대한 새 공용 IP 주소로 VPN 라우터 구성을 업데이트할 필요가 없습니다.

명령이 실행되면 Azure VPN Gateway의 현재 활성 인스턴스가 즉시 재부팅됩니다. 활성 인스턴스(재부팅)에서 대기 인스턴스로 장애 조치(Failover) 중에 잠깐의 간격이 있습니다. 이 간격은 1분 미만이어야 합니다.

첫 번째 재부팅 후 연결이 복원되지 않으면 같은 명령을 다시 실행하여 두 번째 VM인스턴스(새 활성 게이트웨이)를 재부팅합니다. 두 번의 재부팅이 연속으로 요청되는 경우 두 VM 인스턴스(활성 및 대기)가 재부팅되는 기간이 약간 길어집니다. 이로 인해 VM에서 재부팅을 완료하는 데 VPN 연결에 최대 2~4분까지 긴 간격이 발생합니다.

두 번의 재부팅 후 크로스-프레미스 연결 문제가 여전히 발생하는 경우 Azure Portal에서 지원 요청을 여세요.

## 시작하기 전에

게이트웨이를 재설정하기 전에 각 IPsec 사이트 간(S2S) VPN 터널에 대해 아래 나열된 키 항목을 확인합니다. 일치하지 않는 항목은 S2S VPN 터널에서 연결이 끊깁니다. 온-프레미스 및 Azure VPN Gateway에 대한 구성을 확인 및 수정하면 게이트웨이에서 작동 중인 기타 연결에 대해 불필요한 재부팅 및 중단을 피할 수 있습니다.

게이트웨이를 재설정하기 전에 다음 항목을 확인합니다.

- Azure VPN 게이트웨이 및 온-프레미스 VPN 게이트웨이 모두에 대한 인터넷 IP 주소(VIP)가 Azure 및 온-프레미스 VPN 정책에서 바르게 구성되어 있습니다.
- 미리 공유한 키가 Azure와 온-프레미스 VPN 게이트웨이에서 동일해야 합니다.
- 암호화, 해시 알고리즘 및 전달 완전 보안(PFS)과 같은 특정 IPsec/IKE 구성을 적용하는 경우 Azure와 온-프레미스 VPN 게이트웨이에서 동일한 구성을 포함하는지 확인하세요.

## 리소스 관리 배포 모델을 사용하여 VPN Gateway 다시 설정

게이트웨이를 다시 설정하기 위한 PowerShell Resource Manager cmdlet은 `Reset-AzureRmVirtualNetworkGateway`입니다. 다음 예제에서는 "TestRG1" 리소스 그룹에서 Azure VPN Gateway "VNet1GW"를 다시 설정합니다.

	$gw = Get-AzureRmVirtualNetworkGateway -Name VNet1GW -ResourceGroup TestRG1
	Reset-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw

## 클래식 배포 모델을 사용하여 VPN Gateway 다시 설정

Azure VPN Gateway를 재설정하기 위한 PowerShell cmdlet은 `Reset-AzureVNetGateway`니다. 다음 예제에서는 "ContosoVNet"이라는 가상 네트워크에 대한 Azure VPN 게이트웨이를 다시 설정합니다.
 
	Reset-AzureVNetGateway –VnetName “ContosoVNet” 

결과:

	Error          :
	HttpStatusCode : OK
	Id             : f1600632-c819-4b2f-ac0e-f4126bec1ff8
	Status         : Successful
	RequestId      : 9ca273de2c4d01e986480ce1ffa4d6d9
	StatusCode     : OK


## 다음 단계
	
자세한 내용은 [PowerShell 서비스 관리 cmdlet 참조](https://msdn.microsoft.com/library/azure/mt617104.aspx) 및 [PowerShell Resource Manager cmdlet 참조](http://go.microsoft.com/fwlink/?LinkId=828732)를 참조하세요.

<!---HONumber=AcomDC_0928_2016-->