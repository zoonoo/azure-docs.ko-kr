<properties
   pageTitle="클래식에서 리소스 관리자로 Express 경로 회로 이동 | Microsoft Azure"
   description="이 페이지는 클래식 및 리소스 관리자 배포 모델 연결에 대해 알아야 할 내용을 간략히 설명합니다."
   documentationCenter="na"
   services="expressroute"
   authors="ganesr"
   manager="carmonm"
   editor=""/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/01/2016"
   ms.author="ganesr"/>

# 클래식에서 리소스 관리자 배포 모델로 Express 경로 회로 이동

이 문서에서는 클래식에서 리소스 관리자 배포 모델로 Express 경로 회로 이동의 의미에 대해 간략히 설명합니다.

[AZURE.INCLUDE [vpn-gateway-sm-rm](../../includes/vpn-gateway-sm-rm-include.md)]

클래식 및 리소스 관리자 배포 모델에 배포된 가상 네트워크(VNet)에 연결하는 데 단일 Express 경로 회로를 사용할 수 있습니다. 이제 Express 경로 회로 생성 방법에 관계 없이 두 배포 모델 간의 가상 네트워크에 연결할 수 있습니다.

![](./media/expressroute-move/expressroute-move-1.png)

## 클래식 배포 모델에서 만든 Express 경로 회로

클래식 배포 모델에서 만든 Express 경로 회로를 먼저 리소스 관리자 배포 모델로 이동하여 클래식 및 리소스 관리자 배포 모델에 연결을 설정해야 합니다. 연결을 이동하는 경우 연결 손실 또는 중단은 없습니다. 클래식 배포 모델의 모든 회로-vnet 연결(동일한 구독 및 구독 간)이 유지 됩니다. 이동이 성공적으로 완료되면 Express 경로 회로는 리소스 관리자 배포 모델에서 만든 Express 경로와 똑같은 모양 및 느낌을 지니며 동일하게 수행합니다. 이제 리소스 관리자 배포 모델에 가상 네트워크에 대한 연결을 만들 수 있습니다.

Express 경로 회로가 리소스 관리자 배포 모델로 이동한 후에는 리소스 관리자 배포 모델만 사용하여 Express 경로의 수명 주기를 관리할 수 있습니다. 즉, 피어링 추가/업데이트/삭제, 회로 속성(예: 대역폭, SKU, 청구서 유형) 업데이트 및 회로 삭제와 같은 작업은 리소스 관리자 배포 모델에서만 수행할 수 있습니다. 두 배포 모델에서 액세스를 관리하는 방법에 대한 자세한 내용은 아래 리소스 관리자 배포 모델에서 만든 회로에 대한 섹션을 참조하세요.

이동을 수행하는 데 연결 공급자는 포함하지 않아도 됩니다.

## 리소스 관리자 배포 모델에서 만든 Express 경로 회로

두 배포 모델에서 모두 액세스할 수 있도록 리소스 관리자 배포 모델에서 만든 Express 경로 회로를 설정할 수 있습니다. 두 배포 모델에서 모두 액세스할 수 있도록 구독에 있는 모든 Express 경로 회로를 설정할 수 있습니다.

- 기본적으로 리소스 관리자 배포 모델에서 만든 Express 경로 회로는 클래식 배포 모델에 액세스할 수 없습니다.
- 기본적으로 클래식 배포 모델에서 리소스 관리자 배포 모델로 이동한 Express 경로 회로는 두 배포 모델에서 액세스할 수 있습니다.
- Express 경로 회로는 리소스 관리자 배포 모델 또는 클래식 배포 모델에서 생성되었는지 관계 없이 항상 리소스 관리자 배포 모델에 액세스할 수 있습니다. 즉, [가상 네트워크를 연결하는 방법](expressroute-howto-linkvnet-arm.md)에 대한 지침에 따라 리소스 관리자 배포 모델에서 만든 가상 네트워크로의 연결을 만들 수 있습니다. 
- 클래식 배포 모델에 대한 액세스는 Express 경로 회로의 "allowClassicOperations" 매개 변수에 의해 제어됩니다. 

>[AZURE.IMPORTANT] [서비스 제한](../azure-subscription-service-limits.md) 페이지에 문서화된 모든 할당량이 적용됩니다. 예를 들어 표준 회로는 클래식 및 리소스 관리자 배포 모델 간에 최대 10개의 VNet 링크/연결을 포함할 수 있습니다.


### 클래식 배포 모델에 대한 액세스 제어

Express 경로 회로의 "allowClassicOperations" 매개 변수를 설정하여 두 배포 모델에서 VNet에 연결할 단일 Express 경로 회로를 설정할 수 있습니다.

"allowClassicOperations"를 TRUE로 설정하면 두 배포 모델에서 Express 경로 회로로 vnet을 연결할 수 있습니다. [클래식 배포 모델에서 가상 네트워크를 연결하는 방법](expressroute-howto-linkvnet-classic.md)의 지침에 따라 클래식 배포 모델에서 VNet에 연결할 수 있습니다. [리소스 관리자 배포 모델에서 가상 네트워크를 연결하는 방법](expressroute-howto-linkvnet-arm.md)의 지침에 따라 리소스 관리자 배포 모델에서 VNet에 연결할 수 있습니다.

"allowClassicOperations"을 FALSE로 설정하면 클래식 배포 모델에서 회로에 대한 액세스가 차단됩니다. 그러나 클래식 배포 모델에 있는 모든 vnet 연결은 유지됩니다. 이 경우 Express 경로 회로가 클래식 배포 모델에 표시되지 않습니다.

### 클래식 배포 모델에서 지원되는 작업

"allowClassicOperations"가 TRUE로 설정된 경우 Express 경로 회로에서 다음 클래식 작업이 지원됩니다.

 - Express 경로 회로 정보 가져오기
 - 클래식 VNet에 대한 VNet 연결 만들기/업데이트/가져오기/삭제
 - 구독 간 연결에 대한 VNet 연결 권한 부여 만들기/업데이트/가져오기/삭제

"allowClassicOperations"가 TRUE로 설정된 경우 다음 클래식 작업을 수행할 수 없습니다.

 - Azure 개인, Azure 공용 및 Microsoft 피어링에 대한 BGP 피어링 만들기/업데이트/가져오기/삭제
 - Express 경로 회로 삭제

## 클래식 - 리소스 관리자 통신

Express 경로 회로는 클래식 및 리소스 관리자 배포 모델 간의 브리지 역할을 합니다. 클래식 배포 모델의 vnet에 배포된 가상 컴퓨터와 리소스 배포 모델의 vnet에 배포된 가상 컴퓨터 간의 트래픽은 두 vnet이 동일한 Express 경로 회로에 연결된 경우 Express 경로를 통해 이동합니다. 집계 처리량은 가상 네트워크 게이트웨이 처리 용량으로 제한됩니다. 트래픽은 연결 공급자 또는 이 경우 네트워크로 들어가지 않습니다. VNet 간의 트래픽 흐름은 Microsoft 네트워크 내에 완전히 포함됩니다.

## Azure 공용 네트워크 및 Microsoft 피어링 리소스 액세스

작업 중단 없이 Azure 공용 피어링 및 Microsoft 피어링을 통해 일반적으로 액세스할 수 있는 리소스에 계속해서 액세스할 수 있습니다.

## 지원되는 내용

이 섹션에서는 이 기능을 통해 지원되는 내용을 설명합니다.

 - 클래식 및 리소스 관리자 배포 모델에 배포된 vnet에 액세스하는 데 단일 Express 경로 회로를 사용할 수 있습니다.
 - 클래식에서 리소스 관리자 배포 모델로 Express 경로 회로를 이동할 수 있습니다. 이동한 후에는 Express 경로 회로는 리소스 관리자 배포 모델에서 만든 모든 다른 Express 경로와 똑같은 모양 및 느낌을 지니며 동일하게 수행합니다.
 - Express 경로 회로만 이동할 수 있습니다. 회로 링크, VNet 및 VPN 게이트웨이는 이 작업을 통해 이동되지 않습니다.
 - Express 경로 회로가 리소스 관리자 배포 모델로 이동한 후에는 리소스 관리자 배포 모델만 사용하여 Express 경로의 수명 주기를 관리할 수 있습니다. 즉, 피어링 추가/업데이트/삭제, 회로 속성(예: 대역폭, SKU, 청구서 유형) 업데이트 및 회로 삭제와 같은 작업은 리소스 관리자 배포 모델에서만 수행할 수 있습니다.
 - Express 경로 회로는 클래식 및 리소스 관리자 배포 모델 간의 브리지 역할을 합니다. 클래식 배포 모델의 vnet에 배포된 가상 컴퓨터와 리소스 배포 모델의 vnet에 배포된 가상 컴퓨터 간의 트래픽은 두 vnet이 동일한 Express 경로 회로에 연결된 경우 Express 경로를 통해 이동합니다. 
 - 클래식 및 리소스 관리자 배포 모델의 구독 간 연결입니다.

## 지원되지 않는 내용

이 섹션에서는 이 기능을 통해 지원되지 않는 내용을 설명합니다.

 - 회로 연결, 게이트웨이 및 가상 네트워크를 클래식에서 리소스 관리자 배포 모델로 이동합니다.
 - 클래식 배포 모델에서 Express 경로 회로의 수명 주기를 관리합니다.
 - 클래식 배포 모델을 위한 RBAC 지원. 클래식 배포 모델에서 회로에 대한 RBAC 제어를 수행할 수 없습니다. 구독의 모든 관리자/공동 관리자는 회로에 대한 vnet을 연결/연결 해제할 수 있습니다.

## 구성

[클래식에서 리소스 관리자로 Express 경로 회로 이동](expressroute-howto-move-arm.md)에 설명된 지침을 따릅니다.

## 다음 단계

- 워크플로 정보는 [Express 경로 회로 프로비전 워크플로 및 회로 상태](expressroute-workflows.md)를 참조하세요.
- Express 경로 연결을 구성합니다.

	- [Express 경로 회로 만들기](expressroute-howto-circuit-arm.md)
	- [라우팅 구성](expressroute-howto-routing-arm.md)
	- [VNet을 Express 경로 회로에 연결](expressroute-howto-linkvnet-arm.md)

<!---HONumber=AcomDC_0406_2016-->