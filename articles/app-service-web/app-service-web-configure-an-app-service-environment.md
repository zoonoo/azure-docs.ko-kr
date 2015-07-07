<properties 
	pageTitle="앱 서비스 환경을 구성하는 방법" 
	description="앱 서비스 환경 구성, 관리 및 모니터링" 
	services="app-service\web" 
	documentationCenter="" 
	authors="ccompy" 
	manager="stefsch" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/27/2015" 
	ms.author="ccompy"/>

# 앱 서비스 환경 구성 #

## 개요 ##

앱 서비스 환경은 미리 보기에서 제공되는 새로운 프리미엄 계층 기능입니다. 새 크기 조정 및 네트워크 액세스 기능을 제공합니다. 이 새 크기 조정 기능을 통해 Azure 앱 서비스의 인스턴스를 VNET에 배치할 수 있습니다. ASE(앱 서비스 환경) 기능을 잘 모르는 경우 [앱 서비스 환경 정의] 문서(/app-service-app-service-environment-intro.md)를 참조하세요. ASE를 만드는 방법에 대한 자세한 내용은 [앱 서비스 환경을 만드는 방법](app-service-web-how-to-create-an-app-service-environment.md) 문서를 참조하세요.

앱 서비스 환경은 크게 몇 가지 주요 구성 요소로 이루어져 있습니다.

- Azure 앱 환경 호스팅 서비스에서 실행되는 계산 리소스
- 저장소
- 데이터베이스
- 하나 이상의 서브넷이 있는 가상 네트워크
- Azure 앱 환경 호스팅 서비스를 실행 중인 서브넷

앱 서비스 환경을 손쉽게 관리하고 모니터링하기 위해 Azure Preview 포털의 찾아보기 -> 앱 서비스 환경에서 해당 목적에 적합한 UI에 액세스할 수 있습니다. 초기 릴리스에는 시스템을 관리하는 데 필요한 항목이 포함되어 있으며, 앞으로 몇 주 이내에 추가 기능으로 계속 개선될 예정입니다.

![][1]

## 모니터링 ##

초기 미리 보기 릴리스에는 사용 가능한 메트릭 기능이 많지 않지만 곧 출시될 예정입니다. 이러한 메트릭 기능을 통해 시스템 관리자는 시스템 크기 조정 및 작업에 대한 의사를 손쉽게 결정할 수 있습니다.

지금도 포털에서는 ASE의 모든 앱 서비스 계획 및 앱 서비스 환경의 모든 웹앱을 나열할 수 있습니다. 목록을 보려면 설정으로 이동하여 관심 있는 항목을 선택합니다.

![][3]

두 목록 모두에서 인스턴스 수 및 사용 중인 계산 리소스의 크기와 함께 작업자 풀 할당을 볼 수 있습니다. 개별 앱 서비스 계획 내의 성능에 대한 자세한 내용은 일반적으로 앱 서비스 계획 UI를 열어 확인할 수 있습니다.

![][4]

## 계산 리소스 ##

계산 리소스, 저장소 및 데이터베이스는 모든 Azure 앱 서비스에 의해 운영됩니다. 그러나 계산 리소스의 수량 및 크기는 사용자가 결정합니다.

계산 리소스의 크기에 상관없이 최소 공간에는 2개의 프런트 엔드 서버와 2개의 작업자가 있습니다. 최대 총 55개의 계산 리소스를 사용하도록 앱 서비스 환경을 구성할 수 있습니다. 55개의 계산 리소스 중 50개만 작업을 호스트하는 데 사용할 수 있습니다. 이는 두 가지 이유 때문입니다. 프런트 엔드 계산 리소스는 최소 2개가 있어야 하므로 작업자 풀 할당을 지원할 수 있는 계산 리소스는 최대 53개가 남습니다. 그러나 내결함성을 제공하기 위해 다음 규칙에 따라 추가 계산 리소스를 할당해야 합니다.

- 각 작업자 풀에 작업을 할당할 수 없는 하나 이상의 추가 계산 리소스가 필요합니다.
- 풀에 있는 계산 리소스의 양이 특정 값을 초과하는 경우 다른 계산 리소스가 필요합니다.

단일 작업자 풀 내에서 내결함성 요구 사항은 작업자 풀에 할당된 X개의 리소스에 대한 지정된 값입니다.

- X가 2~20인 경우 작업에 사용할 수 있는 계산 리소스 양은 X-1입니다.
- X가 21~40인 경우 작업에 사용할 수 있는 계산 리소스 양은 X-2입니다.
- X가 41~53인 경우 작업에 사용할 수 있는 계산 리소스 양은 X-3입니다.

지정된 풀에 할당할 수 있는 계산 리소스의 양을 관리하는 것 외에 크기를 제어할 수도 있습니다. 앱 서비스 환경에서는 P1~P4의 레이블이 지정된 네 가지 크기 중에서 선택할 수 있습니다. 이러한 크기 및 해당 가격에 대한 자세한 내용은 [앱 서비스 가격](../app-service/app-service-value-prop-what-is.md)을 참조하세요. P1~P3 계산 리소스 크기는 일반적으로 사용할 수 있는 것과 동일합니다. P4 계산 리소스는 14GB RAM의 8개 코어를 제공하며, 앱 서비스 환경에서만 사용할 수 있습니다.

앞서 설명한 대로 앱 서비스 환경 기능은 현재 미리 보기로 제공되며 여전히 개선될 여지가 있습니다. 추가 모니터링 기능 외에도 앱 서비스 환경이 GA로 이동할 대 더 많은 관리 기능이 출시될 예정입니다. 현재는 다음과 같은 몇 가지 사항만 이 인터페이스에서 관리할 수 있습니다.

- 각 풀의 계산 리소스 수
- 각 풀의 계산 리소스 크기
- 사용 가능한 IP 주소 수

이러한 사항을 제어하려면 맨 위에서 크기 조정 구성 항목을 선택합니다.

![][2]

각 풀의 계산 리소스 양과 해당 크기는 여기에서 조정할 수 있습니다. 그러나 변경하기 전에 다음 몇 가지 사항에 유의해야 합니다.

- 요청된 변경의 크기에 다라 변경 작업을 완료하는 데 몇 시간이 걸릴 수 있습니다.
- 이미 작동 중인 앱 서비스 환경 구성 변경이 있는 경우 다른 변경을 시작할 수 없습니다.
- 작업자 풀에서 사용되는 계산 리소스의 크기를 변경할 경우 해당 작업자 풀에서 실행 중인 웹앱이 중단될 수 있습니다.

작업자 풀에 인스턴스를 추가하는 것은 무해한 작업이며, 시스템에 영향을 주지 않습니다. 그러나 작업자 풀에서 사용되는 계산 리소스의 크기를 변경하는 것은 그렇지 않습니다. 크기를 변경하는 동안 앱 작동 중단을 방지하려면 다음과 같이 하는 것이 가장 좋습니다.

- 사용하지 않는 작업자 풀을 사용하여 필요한 크기의 인스턴스를 불러옵니다.
- 앱 서비스 계획을 새 작업자 풀로 확장합니다.  
 
이렇게 하면 작업을 실행 중인 계산 리소스의 크기를 변경하는 것보다 실행 중인 작업에 미치는 영향이 훨씬 적습니다. 앱 서비스 환경에서 웹앱의 크기를 조정하는 방법에 대한 자세한 내용은 [앱 서비스 환경에서 웹앱 크기 조정](app-service-web-scale-a-web-app-in-an-app-service-environment.md)을 참조하세요.

## 가상 네트워크 ##

[가상 네트워크][virtualnetwork]와 서브넷은 모든 사용자가 제어합니다. 앱 서비스 환경은 몇 가지 네트워크 요구 사항이 있지만 나머지는 사용자에 의해 제어됩니다. 이러한 ASE 요구 사항은 다음과 같습니다.

- 512개 이상의 주소가 있는 VNET
- 256개 이상의 주소가 있는 서브넷 
- VNET이 지역 VNET이어야 함  
 
VNET 관리는 일반적인 가상 네트워크 UI를 통해 수행됩니다.

이 기능은 Azure 앱 서비스를 VNET에 두기 때문에 ASE에서 호스트되는 앱이 ExpressRoute 또는 사이트 간 VPN을 통해 사용할 수 있는 리소스에 직접 액세스할 수 있습니다. 앱 서비스 환경 내의 앱은 추가 네트워킹 기능 없이도 앱 서비스 환경을 호스트하는 VNET에서 사용할 수 있는 리소스에 액세스할 수 있습니다.

또한 필요한 경우 네트워크 보안 그룹을 사용하여 액세스를 제어할 수도 있습니다. 이 기능을 사용하면 제한하려는 IP 주소로만 앱 서비스 환경을 잠글 수 있습니다. 이 작업을 수행하는 방법에 대한 자세한 내용은 [앱 서비스 환경에서 인바운드 트래픽을 제어하는 방법](app-service-app-service-environment-control-inbound-traffic.md)을 참조하세요.

## 앱 서비스 환경 삭제 ##

앱 서비스 환경을 삭제하려면 앱 서비스 환경 블레이드의 위쪽에 있는 삭제 작업을 사용하면 됩니다. 그러나 콘텐츠가 있는 ASE는 삭제할 수 없습니다. 앱 서비스 환경을 삭제하려면 모든 웹앱 및 앱 서비스 계획을 제거해야 합니다.

## 시작

앱 서비스 환경을 시작하려면 [앱 서비스 환경을 만드는 방법](app-service-web-how-to-create-an-app-service-environment.md)을 참조하세요.

Azure 앱 서비스 플랫폼에 대한 자세한 내용은 [Azure 앱 서비스](../app-service/app-service-value-prop-what-is.md)를 참조하세요.

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-web-configure-an-app-service-environment/configureaseblade.png
[2]: ./media/app-service-web-configure-an-app-service-environment/configurescale.png
[3]: ./media/app-service-web-configure-an-app-service-environment/configureasplist.png
[4]: ./media/app-service-web-configure-an-app-service-environment/configurewebapplist.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[Appserviceplans]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[HowtoCreateASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[HowtoScale]: http://azure.microsoft.com/documentation/articles/app-service-web-scale-a-web-app-in-an-app-service-environment/
[ControlInbound]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[virtualnetwork]: https://msdn.microsoft.com/library/azure/dn133803.aspx
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
 

<!---HONumber=62-->