<properties 
   pageTitle="리소스 관리자 배포 모델 및 Azure 포털을 사용하여 가상 네트워크를 Express 경로 회로에 연결 | Microsoft Azure"
   description="이 문서는 가상 네트워크(VNets)를 Express 경로 회로에 연결하는 방법에 대한 개요를 제공합니다."
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/14/2016"
   ms.author="cherylmc" />

# 가상 네트워크를 Express 경로 회로에 연결합니다.

> [AZURE.SELECTOR]
- [Azure 포털 - 리소스 관리자](expressroute-howto-linkvnet-portal-resource-manager.md)
- [PowerShell - Resource Manager](expressroute-howto-linkvnet-arm.md)
- [PowerShell - 클래식](expressroute-howto-linkvnet-classic.md)



이 문서를 참조하면 리소스 관리자 배포 모델 및 Azure 포털을 사용하여 VNet(가상 네트워크)을 Express 경로 회로에 연결할 수 있습니다. 가상 네트워크는 같은 구독에 있을 수도 있고 다른 구독의 일부일 수도 있습니다.


**Azure 배포 모델 정보**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## 필수 구성 요소

- 구성을 시작하기 전에 [필수 조건](expressroute-prerequisites.md) 페이지, [라우팅 요구 사항](expressroute-routing.md) 페이지 및 [워크플로](expressroute-workflows.md) 페이지를 검토했는지 확인합니다.
- 활성화된 Express 경로 회로가 있어야 합니다. 
	- 지침을 수행하여 [Express 경로 회로를 만들고](expressroute-howto-circuit-arm.md) 연결 공급자를 통해 회로를 사용하도록 설정합니다. 
	
	- 회로에 구성된 Azure 개인 피어링이 있는지 확인합니다. 라우팅 지침에 대한 문서는 [라우팅 구성](expressroute-howto-routing-portal-resource-manager.md)을 참조하세요.
	
	- Azure 개인 피어링을 구성해야 하며 네트워크와 Microsoft 간의 BGP 피어링은 종단 간 연결을 사용하도록 작동 상태여야 합니다.
	
	- 가상 네트워크 및 가상 네트워크 게이트웨이를 만들고 완전히 프로비전해야 합니다. [VPN 게이트웨이](../articles/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)를 만들려면 지침을 따르세요(1-5단계만 수행).

최대 10개의 가상 네트워크를 Express 경로 회로에 연결할 수 있습니다. 모든 Express 경로 회로는 같은 지역에 있어야 합니다. Express 경로 프리미엄 추가 기능을 사용하도록 설정하면 Express 경로 회로에 많은 수의 가상 네트워크를 연결할 수 있습니다. 프리미엄 추가 기능에 대한 자세한 내용은 [FAQ](expressroute-faqs.md)에서 확인하세요.

## 동일한 구독에 있는 VNet을 회로에 연결


### 연결을 만들려면

1. Express 경로 회로 및 Azure 개인 피어링이 성공적으로 구성되었는지 확인합니다. 이 문서의 지침에 따라 [Express 경로 회로를 만들](expressroute-howto-circuit-arm.md)고 [라우팅을 구성](expressroute-howto-routing-arm.md)합니다. Express 경로 회로가 아래 이미지와 같이 표시됩니다.

	![](./media/expressroute-howto-linkvnet-portal-resource-manager/routing1.png)

	>[AZURE.NOTE] 피어링이 레이어 3 공급자에 의해 구성된 경우 BGP 구성 정보가 표시되지 않습니다. 회로가 프로비전된 상태인 경우 연결을 만들 수 있어야 합니다.

2. 이제 연결 프로비전을 시작하여 VNet 게이트웨이를 Express 경로 회로에 연결할 수 있습니다. **연결** **>** **추가**를 클릭하여 **연결 추가** 블레이드를 연 다음 값을 구성합니다. 아래 참조 예제를 참조하세요.
	

	![](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub1.png)
 
	
3. 연결이 성공적으로 구성되면 연결 개체가 연결에 대한 정보를 표시합니다.

	![](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub2.png)


### 연결을 삭제하려면

연결에 대한 블레이드에서 **삭제** 아이콘을 선택하여 연결을 삭제할 수 있습니다.

## 다른 구독에 있는 VNet을 회로에 연결

현재 Azure 포털을 사용하여 구독에서 가상 네트워크를 연결할 수 없습니다. 그러나 PowerShell을 사용하여 이 작업을 수행할 수 있습니다. 자세한 내용은 [PowerShell](expressroute-howto-linkvnet-arm.md) 문서를 참조하세요.

## 다음 단계

Express 경로에 대한 자세한 내용은 [Express 경로 FAQ](expressroute-faqs.md)를 참조하세요.

<!---HONumber=AcomDC_0420_2016-->