<properties 
	pageTitle="앱 서비스 환경을 구성하는 방법" 
	description="앱 서비스 환경 구성, 관리 및 모니터링" 
	services="app-service" 
	documentationCenter="" 
	authors="ccompy" 
	manager="stefsch" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/26/2015" 
	ms.author="ccompy"/>


# 앱 서비스 환경 구성 #

## 개요 ##

앱 서비스 환경은 새로운 확장 및 네트워크 액세스 기능을 제공하는 Azure 앱 서비스의 프리미엄 계층 기능입니다. 이 새 크기 조정 기능을 통해 Azure 앱 서비스의 인스턴스를 VNET에 배치할 수 있습니다. 이 기능은 웹앱, 모바일 앱 및 API 앱을 호스팅할 수 있습니다. 논리 앱은 아직 ASE에서 실행하지 마세요.

ASE(앱 서비스 환경) 기능을 잘 모르는 경우 [앱 서비스 환경 정의](app-service-app-service-environment-intro.md) 문서를 참조하세요. ASE를 만드는 방법에 대한 자세한 내용은 [앱 서비스 환경을 만드는 방법](app-service-web-how-to-create-an-app-service-environment.md) 문서를 참조하세요.

앱 서비스 환경은 크게 몇 가지 주요 구성 요소로 이루어져 있습니다.

- Azure 앱 환경 호스티드 서비스에서 실행되는 계산 리소스
- 저장소
- 데이터베이스
- 하나 이상의 서브넷이 있는 클래식 "v1" 가상 네트워크
- Azure 앱 환경 호스티드 서비스를 실행 중인 서브넷

계산 리소스는 4개의 리소스 풀에 사용됩니다. 각 앱 서비스 환경에는 프런트 엔드 및 3개의 작업자 풀 집합이 있습니다. 3개의 작업자 풀을 모두 사용할 필요는 없으며 원하는 경우 하나만 사용할 수 있습니다. 프런트 엔드는 ASE에 보관된 앱에 대한 HTTP 끝점입니다. 작업자는 앱이 실제로 실행되는 위치입니다. 프런트 엔드 또는 작업자를 더 추가해야 하는 경우 과학은 ASE에 넣은 앱이 수행하는 방법에 연결됩니다. 예를 들어 ASE에 하나의 앱만 존재하며 방대한 수의 요청을 가져오는 hello world 앱이라고 가정해 보겠습니다. 이 경우 HTTP 부하를 처리하도록 프런트 엔드를 확장해야 하지만 반대로 작업자를 확장해야 하는 것은 아닙니다. 특히 각 ASE에 다양한 성능 조건을 가지고 실행 중인 앱이 혼합되어 있다는 점을 고려할 때 이 모든 작업을 직접 처리하기는 다소 어렵습니다. 다행히 앱 서비스 환경에 자동 크기 조정을 추가했고 이를 사용하면 훨씬 더 쉽게 작업할 수 있습니다. 앱 서비스 환경의 크기 조정 및 자동 크기 조정에 대한 자세한 내용은 [앱 서비스 환경에서 자동 크기 조정을 구성하는 방법][ASEAutoscale]에서 링크를 따라갑니다.

ASE 각각은 500Gb의 저장소로 구성됩니다. 이 공간은 ASE의 모든 앱에서 사용됩니다. 이 저장소 공간은 ASE의 일부이며 현재 고객의 저장소 공간을 사용하도록 전환할 수 없습니다.

데이터베이스는 내부에서 실행 중인 앱의 환경 뿐만 아니라 세부 정보를 정의하는 정보를 가집니다. 이것 또한 Azure가 보유한 구독의 일부이며 고객이 직접 조작할 수 있는 것은 아닙니다.

ASE와 함께 사용되는 가상 네트워크는 ASE를 만들 때 만들었거나 미리 있던 것일 수 있습니다. ASE를 ASE에 사용된 리소스 그룹과 다른 별도의 리소스 그룹에 두려는 경우 ASE 만들기 흐름에서 별도로 VNET을 만들어야 합니다. ASE을 만드는 동안 서브넷을 만들면 ASE가 VNET과 동일한 리소스 그룹에 있도록 강제되기 때문에 동시에 사용하려는 서브넷을 만드는 것이 좋습니다. 현재 V1 "클래식" VNET만 지원합니다.

앱 서비스 환경을 관리하고 모니터링하는 UI는 Azure 포털에서 사용할 수 있습니다. ASE가 있는 경우 사이드바에서 앱 서비스 기호를 확인할 수 있습니다. 이 기호는 Azure 포털에서 앱 서비스 환경을 나타내는 데 사용됩니다.

![][1]

아이콘을 사용하고 또는 사이드바의 아래 쪽에서 펼침 단추(기호 보다 큼) 를 선택하며 앱 서비스 환경을 선택할 수 있습니다. 둘 모두 앱 서비스 환경을 나열하는 UI를 여는 동일한 작업을 수행합니다. 나열된 ASE 중 하나를 선택하면 모니터링하고 관리하는 데 사용되는 UI를 엽니다.

![][2]

이 첫 번째 블레이드는 리소스 풀 단위당 메트릭 차트와 함께 ASE의 속성 일부를 보여줍니다. 또한 Essentials 블록에 표시된 속성 일부에는 연관된 블레이드를 열 수 있는 하이퍼링크가 있습니다. 예를 들어 VNET 이름을 선택하여 ASE가 실행되는 VNET에 연결된 UI를 열 수 있습니다. 앱 서비스 계획 및 앱은 각각 ASE에 있는 이러한 항목을 나열하는 블레이드를 엽니다.

## 모니터링 ##

차트는 각 리소스 풀에서 다양한 성능 메트릭을 참조하는 기능을 제공합니다. 프런트 엔드 풀의 경우 평균 CPU 및 메모리를 모니터링하는 점에서 합리적입니다. 프런트 엔드는 일반적인 성능에 대해 잘 살펴볼 수 있는 평균을 확인하여 분산 부하를 얻습니다. 그러나 작업자 풀은 동일하지 않습니다. 여러 앱 서비스 계획은 작업자 풀에서 작업자를 사용할 수 있습니다. 그런 경우 CPU 및 메모리 사용량은 유용한 정보를 제공하지 않습니다. 특히 타인이 해당 시스템을 사용하도록 관리하려면 작업자 및 사용가능한 작업자의 수를 추적하는 것이 중요합니다.

또한 차트에서 추적할 수 있는 모든 메트릭은 경고를 설정하는 데 사용할 수 있습니다. 경고 설정은 앱 서비스 내의 다른 곳에서와 동일하게 작동합니다. 또는 경고 UI 일부 또는 메트릭 UI 파악과 경고 추가 도달에서 경고를 설정할 수 있습니다.
 
![][3]

방금 설명한 메트릭은 앱 서비스 환경 메트릭입니다. 또한 앱 서비스 계획 수준에서 메트릭을 사용할 수 있습니다. CPU 및 메모리의 모니터링이 합리적입니다. ASE에서 모든 ASP는 전용 ASP입니다. 즉, 해당 ASP에 할당된 호스트에서 실행 중인 유일한 앱은 해당 ASP에 있는 앱입니다. ASP의 세부 정보를 확인하려면 단순히 ASE UI의 목록 중 하나 또는 이들 모두를 나열하는 앱 서비스 계획에서 ASP를 불러옵니다.

ASE 외부에서 ASP에 사용할 수 있는 자동 크기 조정 및 리소스에 사용할 수 있는 메트릭을 활용하는 방법에 이미 익숙할 수도 있습니다. 또한 앱 서비스 환경의 자동 크기 조정의 경우에도 마찬가지입니다. ASE의 메트릭을 기반으로 ASP를 자동 크기 조정할 수 있을 뿐만 아니라 ASE 자체에 대한 자동 크기 조정 규칙을 설정할 수도 있습니다. 자동 크기 조정을 설정하는 자세한 내용은 [앱 서비스 환경에서 자동 크기 조정][ASEAutoscale]에 자세한 가이드가 있습니다.


## 속성 ##

ASE 블레이드를 불러오면 설정 블레이드가 자동으로 열립니다. 맨 위쪽에 속성이 있습니다. 여기에 Essentials에 표시된 것과 중복되는 여러 항목이 있지만 아웃바운드 IP 주소 뿐만 아니라 VIP 주소가 매우 유용합니다. 지금은 한 가지로 동일하지만 나중에 특정 시점에서 VIP 주소와 별도의 아웃바운드 IP 주소가 있을 수 있기 때문에 모두 여기에 적어둡니다. 따라서 SSL을 설정하고 ASE의 단일 앱에 대해 IP 주소를 추가하지 않았다면 ASE에 만든 앱에 설정한 DNS의 IP는 여기 속성에서 표시되는 VIP 주소입니다.

![][4]

## IP 주소 ##

사용할 앱에 대한 ASE에 더 많은 IP 주소를 추가할 수 있는 위치입니다. IP SSL을 설정하려는 ASE의 앱을 만들 때 해당 앱에 대해 예약된 IP 주소가 있어야 합니다. 그렇게 하려면 ASE에는 할당될 수 있도록 소유한 일부 IP가 있어야 합니다. ASE를 만들 때 이 목적을 위해 주소 1이 있습니다. 더 필요한 경우 여기로 이동하고 더 추가합니다. 이제 더 추가하려는 경우 최대 할당량에 단순히 드래그하기 전에 추가 IP 주소는 비용이 청구된다는 점에 유의합니다. 얼마나 추적되는지에 대한 자세한 내용은 다음의 가격 책정 페이지를 참조하세요. [앱 서비스 가격][AppServicePricing] SSL 연결의 섹션까지 아래로 스크롤합니다. 추가 가격은 IP SSL 가격입니다.

**참고:** 더 많은 IP 주소를 추가하는 경우 규모 작업이라는 점에 유의합니다. 한 번에 진행 중인 규모 작업이 하나만 있을 수 있습니다. 세 가지 규모 작업이 있습니다.

- IP SSL 사용에 사용할 수 있는 ASE에서 IP 주소의 수 변경
- 리소스 풀에서 사용된 계산 리소스의 크기 변경
- 수동으로 또는 자동 크기 조정을 통해 리소스 풀에 사용되는 계산 리소스의 수 변경

## 리소스 풀 ##

설정에서 프런트 엔드 풀 또는 작업자 풀 UI를 얻을 수 있습니다. 이러한 리소스 풀 블레이드는 각각 완벽하게 해당 리소스 풀의 크기를 조정하도록 제어를 제공하는 것 외에도 해당 리소스 풀에 대한 정보를 확인하는 기능을 제공합니다.

각 리소스 풀에 대한 기본 블레이드는 해당 리소스 풀에 대한 메트릭을 사용하여 차트를 제공합니다. ASE 블레이드의 차트와 마찬가지로 차트로 이동하고 원하는 대로 경고를 설정합니다. 특정 리소스 풀에 대한 ASE 블레이드에서 경고를 설정하는 작업은 리소스 풀에서의 작업 수행과 동일합니다. 작업자 풀 설정 블레이드에서 이 작업자 풀에 실행 중인 모든 앱 또는 앱 서비스 계획을 나열하는 데 액세스합니다.

![][5]

### 계산 리소스 수량 규모 ###

ASE의 앱 크기 조정에 대한 더 나은 관점을 제공하려면 다음에 가이드가 있습니다. [앱 서비스 환경에서 앱 크기 조정](app-service-web-scale-a-web-app-in-an-app-service-environment.md) ASE 리소스 풀에 대한 자동 크기 조정을 구성하는 방법을 더 알아보려면 다음부터 시작합니다. [앱 서비스 환경에서 자동 크기 조정][ASEAutoscale] 이 설명은 리소스 풀에 대한 수동 크기 조정 작업을 자세히 설명합니다.

리소스 풀, 프런트 엔드 및 작업자는 테넌트에 직접 액세스할 수 없습니다. 즉, RDP를 하거나 해당 프로비전을 변경하고 관리자의 역할을 할 수 없습니다. Azure에서 작업이 수행되고 유지 관리됩니다. 덧붙이자면 계산 리소스의 수량 및 크기는 사용자가 결정합니다.

실제로 리소스 풀에서 가진 많은 서버를 제어할 수 있는 세 가지 방법이 있습니다. - 맨 위쪽의 기본 ASE 블레이드에서 크기 조정 작업 - 설정에 있는 개별 리소스 풀 크기 조정 블레이드에서 수동 크기 조정 작업 - 개별 리소스 풀 크기 조정 블레이드에서 설치할 자동 크기 조정

ASE 블레이드에서 크기 조정 작업을 사용하려면 클릭한 다음 슬라이더를 원하는 수량만큼 끌어 놓고 저장합니다. 또한 이 UI는 크기 변경을 지원합니다.

![][6]

특정 리소스 풀에서 수동 또는 자동 크기 조정 기능을 사용하려면 ASE 블레이드에서 먼저 시작하고 설정으로 이동합니다. 설정에서 원하는 프런트 엔드 풀 또는 작업자 풀을 열고 변경하려는 풀을 엽니다. 설정에 규모 펼침 단추가 있습니다. 열린 블레이드는 수동 또는 자동 크기 조정에 사용될 수 있습니다.

![][7]

최대 총 55개의 계산 리소스를 사용하도록 앱 서비스 환경을 구성할 수 있습니다. 55개의 계산 리소스 중 50개만 워크로드를 호스트하는 데 사용할 수 있습니다. 이는 두 가지 이유 때문입니다. 프런트 엔드 계산 리소스는 최소 2개가 있어야 하므로 작업자 풀 할당을 지원할 수 있는 계산 리소스는 최대 53개가 남습니다. 내결함성을 제공하기 위해 다음 규칙에 따라 추가 계산 리소스를 할당해야 합니다.

- 각 작업자 풀에 워크로드를 할당하는 데 사용할 수 없는 하나 이상의 추가 계산 리소스가 필요합니다.
- 작업자 풀에 있는 계산 리소스의 양이 특정 값을 초과하는 경우 내결함성에 다른 계산 리소스가 필요합니다. 프런트 엔드 풀에는 적용되지 않습니다.

단일 작업자 풀 내에서 내결함성 요구 사항은 작업자 풀에 할당된 X개의 리소스에 대한 지정된 값입니다.

- X가 2~20인 경우 워크로드에 사용할 수 있는 계산 리소스 양은 X-1입니다.
- X가 21~40인 경우 워크로드에 사용할 수 있는 계산 리소스 양은 X-2입니다.
- X가 41~53인 경우 워크로드에 사용할 수 있는 계산 리소스 양은 X-3입니다.

최소한의 공간에는 2개의 프런트 엔드 서버 및 2개의 작업자가 있습니다. 다음은 위의 문을 사용하여 명확히 하는 몇 가지 예제입니다.

- 단일 풀에 30개의 작업자가 있는 경우 그 중 28개는 워크로드를 호스트하는 데 사용될 수 있습니다. 
- 단일 풀에 2개의 작업자가 있는 경우 1개는 워크로드를 호스트하는 데 사용될 수 있습니다.
- 단일 풀에 20개의 작업자가 있는 경우 19개는 워크로드를 호스트하는 데 사용될 수 있습니다.  
- 여전히 단일 풀에 21개의 작업자가 있는 경우 19개는 워크로드를 호스트하는 데 사용될 수 있습니다.  

내결함성 측면은 중요하지만 특정 임계값 보다 높은 눈금을 염두해야 합니다. 20개의 인스턴스에서 더 많은 용량을 추가하려는 경우 22 또는 21 보다 많더라도 용량을 더 추가하지 못합니다. 40 이상에서 용량을 추가하는 다음 번호가 42인 점도 사실입니다.

### 계산 리소스 크기 조정 ###

지정된 풀에 할당할 수 있는 계산 리소스의 양을 관리하는 것 외에 크기를 제어할 수도 있습니다. 앱 서비스 환경에서는 P1~P4의 레이블이 지정된 네 가지 크기 중에서 선택할 수 있습니다. 이러한 크기 및 해당 가격에 대한 자세한 내용은 [앱 서비스 가격](../app-service/app-service-value-prop-what-is.md)을 참조하세요. P1~P3 계산 리소스 크기는 일반적으로 사용할 수 있는 것과 동일합니다. P4 계산 리소스는 14GB RAM의 8개 코어를 제공하며, 앱 서비스 환경에서만 사용할 수 있습니다.

풀에 사용되는 계산 리소스의 크기를 변경하려는 경우 두 가지 방법이 있습니다. ASE 블레이드에서 사용할 수 있는 크기 조정 명령 및 개별 리소스 풀의 설정으로 이동하여 얻을 수 있는 가격 책정 계층 블레이드도 있습니다.

![][8]

그러나 변경하기 전에 다음 몇 가지 사항에 유의해야 합니다.

- 변경의 크기에 따라 변경 작업을 완료하는 데 잠재적으로 몇 시간이 걸릴 수 있습니다.
- 이미 작동 중인 앱 서비스 환경 구성 변경이 있는 경우 다른 변경을 시작할 수 없습니다.
- 작업자 풀에서 사용되는 계산 리소스의 크기를 변경할 경우 해당 작업자 풀에서 실행 중인 앱이 중단될 수 있습니다.

작업자 풀에 인스턴스를 추가하는 것은 무해한 작업이며 해당 풀에서 리소스에 대한 앱 중단을 일으키지 않습니다. 그러나 작업자 풀에서 사용되는 계산 리소스의 크기를 변경하는 것은 그렇지 않습니다. 작업자 풀의 크기를 변경하는 동안 앱 작동 중단을 방지하려면 다음과 같이 하는 것이 좋습니다.

- 사용하지 않는 작업자 풀을 사용하여 필요한 크기의 인스턴스를 불러옵니다.
- 앱 서비스 계획을 새 작업자 풀로 확장합니다.  
 
이렇게 하면 워크로드를 실행 중인 계산 리소스의 크기를 변경하는 것보다 실행 중인 워크로드에 미치는 영향이 훨씬 적습니다. 앱 서비스 환경에서 앱의 크기를 조정하는 방법에 대한 자세한 내용은 [앱 서비스 환경에서 앱 크기 조정](app-service-web-scale-a-web-app-in-an-app-service-environment.md)을 참조하세요.

## 가상 네트워크 ##

ASE를 포함하는 호스티드 서비스와 달리 [가상 네트워크][virtualnetwork] 및 서브넷은 모두 사용자가 제어하고 있습니다. 앱 서비스 환경은 몇 가지 네트워크 요구 사항이 있지만 나머지는 사용자에 의해 제어됩니다. 이러한 ASE 요구 사항은 다음과 같습니다.

- 클래식 "v1" VNET 
- 8개 이상의 주소가 있는 서브넷 
- VNET이 지역 VNET이어야 함  
 
VNET 관리는 가상 네트워크 UI 또는 Powershell을 통해 수행됩니다.

이 기능은 Azure 앱 서비스를 VNET에 두기 때문에 ASE에서 호스트되는 앱이 Express 경로 또는 사이트 간 VPN을 통해 사용할 수 있는 리소스에 직접 액세스할 수 있습니다. 앱 서비스 환경 내의 앱은 추가 네트워킹 기능 없이도 앱 서비스 환경을 호스트하는 VNET에서 사용할 수 있는 리소스에 액세스할 수 있습니다. 즉, VNET 통합 또는 하이브리드 연결을 사용하여 리소스를 가져오거나 VNET에 연결할 필요가 없습니다. 여전히 이러한 기능을 사용하여 VNET에 연결되지 않은 네트워크의 리소스에 액세스할 수 있습니다. 예를 들어 VNET 통합을 사용하여 구독에 있는 VNET과 통합할 수 있지만 ASE에 있는 VNET에 연결되지 않습니다. 또한 여전히 하이브리드 연결을 사용하여 일반적인 상황과 마찬가지로 다른 네트워크의 리소스에 액세스할 수 있습니다.

Express 경로 VPN을 사용하여 구성된 VNET이 없는 경우 라우팅 일부는 ASE에 있는 VNET이 필요합니다. ASE와 호환되지 않는 일부 사용자 정의 경로(UDR) 구성이 있습니다. Express 경로를 사용하는 VNET에서 ASE의 실행에 대한 자세한 내용은 다음 문서를 참조하세요. [Express 경로를 사용하는 VNET에서 앱 서비스 환경 실행][ExpressRoute]

또한 네트워크 보안 그룹을 사용하여 앱에 액세스를 제어할 수도 있습니다. 이 기능을 사용하면 제한하려는 IP 주소로만 앱 서비스 환경을 잠글 수 있습니다. 이 작업을 수행하는 방법에 대한 자세한 내용은 [앱 서비스 환경에서 인바운드 트래픽을 제어하는 방법](app-service-app-service-environment-control-inbound-traffic.md)을 참조하세요.

## 앱 서비스 환경 삭제 ##

앱 서비스 환경을 삭제하려면 앱 서비스 환경 블레이드의 위쪽에 있는 삭제 작업을 사용하면 됩니다. 이렇게 하면 이 작업을 수행할지 확인하기 위해 앱 서비스 환경의 이름을 입력하라는 메시지가 표시됩니다. 참고: 앱 서비스 환경을 삭제하는 경우 포함된 내용도 모두 삭제됩니다.

![][9]

## 시작

앱 서비스 환경을 시작하려면 [앱 서비스 환경을 만드는 방법](app-service-web-how-to-create-an-app-service-environment.md)을 참조하세요.

Azure 앱 서비스 플랫폼에 대한 자세한 내용은 [Azure 앱 서비스](../app-service/app-service-value-prop-what-is.md)를 참조하세요.

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-web-configure-an-app-service-environment/ase-icon.png
[2]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-aseblade.png
[3]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolchart.png
[4]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-properties.png
[5]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolblade.png
[6]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-scalecommand.png
[7]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolscale.png
[8]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-pricingtiers.png
[9]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-deletease.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[Appserviceplans]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[HowtoCreateASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[HowtoScale]: http://azure.microsoft.com/documentation/articles/app-service-web-scale-a-web-app-in-an-app-service-environment/
[ControlInbound]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[ASEAutoscale]: http://azure.microsoft.com/documentation/articles/app-service-environment-auto-scale/
[ExpressRoute]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-configuration-expressroute/

<!---HONumber=Nov15_HO1-->