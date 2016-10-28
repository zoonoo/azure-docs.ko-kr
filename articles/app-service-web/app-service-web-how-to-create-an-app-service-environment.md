<properties 
	pageTitle="앱 서비스 환경을 만드는 방법" 
	description="앱 서비스 환경에 대한 만들기 흐름 설명" 
	services="app-service" 
	documentationCenter="" 
	authors="ccompy" 
	manager="stefsch" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/12/2016" 
	ms.author="ccompy"/>

# 앱 서비스 환경을 만드는 방법 #

앱 서비스 환경(ASE)은 다중 테넌트 스탬프에서 사용할 수 없는 향상된 구성 기능을 제공하는 Azure 앱 서비스의 프리미엄 서비스 옵션입니다. ASE 기능은 기본적으로 고객의 가상 네트워크에 Azure 앱 서비스를 배포합니다. 앱 서비스 환경에서 제공되는 기능에 대한 자세한 내용은 [앱 서비스 환경 정의][WhatisASE] 설명서를 참조하세요.


### 개요 ###

ASE는 프런트 엔드 및 작업자 계산 리소스로 구성됩니다. 프런트 엔드는 HTTP/HTTPS 끝점으로 작동하여 앱 호스트 역할을 갖는 작업자로 트래픽을 전송합니다.

ASE 만들기에는 다음 정보를 제공하는 고객이 필요합니다.

- ASE의 이름
- ASE에 사용할 구독
- 리소스 그룹
- ASE에서 사용할 8개 이상의 주소와 1개의 서브넷을 포함하는 Azure 가상 네트워크(VNet)
- VIP 유형(외부 또는 내부)
- ASE 리소스 풀 정의


각 해당 항목에 몇 가지 중요한 세부 정보가 있습니다.

- 해당 ASE에 만든 모든 앱에 대한 하위 도메인에서 ASE의 이름이 사용됩니다(외부 VIP로 구성된 경우).
- 외부 유형의 ASE는 인터넷에서 액세스할 수 있는 앱을 호스트합니다. 내부 VIP를 포함하는 ASE는 ILB(내부 부하 분산 장치)를 사용합니다.
- ASE에 만든 모든 앱은 ASE 자체와 동일한 구독에 포함됩니다.
- ASE를 만드는 데 사용된 구독에 액세스할 수 없는 경우 ASE는 앱을 만드는 데 사용할 수 없습니다.
- ASE를 호스트하는 데 사용되는 VNet은 지역별 VNET이어야 합니다. 클래식 또는 Resource Manager VNet을 사용할 수 있습니다.
- **ASE를 호스트하는 데 사용되는 서브넷은 다른 계산 리소스를 포함해서는 안 됩니다.**
- 하나의 ASE만 서브넷에 있을 수 있습니다.
- 이제 공용 주소 범위 *또는* RFC1918 주소 공간(즉, 개인 주소) *중 하나*를 사용하는 가상 네트워크에 ASE를 배포할 수 있습니다. 공용 주소 범위를 가진 가상 네트워크를 사용하기 위해 사전에 VNet 및 서브넷을 만들고 ASE 만들기 UX에서 서브넷을 선택해야 합니다.


각 ASE 배포는 Azure에서 관리 및 유지 보수하는 호스팅 서비스입니다. 고객은 인스턴스의 양과 크기를 관리할 수 있지만 ASE 시스템 역할을 호스트하는 계산 리소스에는 액세스할 수 없습니다.

ASE 만들기 UI에 액세스하는 방법에는 두 가지가 있습니다. Azure 마켓플레이스에서 ***앱 서비스 환경***을 검색하거나, 새로 만들기 -> 웹 + 모바일로 이동하여 찾을 수 있습니다.

VNet이 ASE에서 별도 리소스 그룹을 가지려면 먼저 VNet을 별도로 만든 다음 ASE를 만드는 동안 VNet을 선택해야 합니다. 또한 ASE를 만드는 동안 기존 VNet에서 서브넷을 만들려면 ASE는 VNet과 동일한 리소스 그룹에 있어야 합니다.


### 빨리 만들기 ###
ASE에 대한 생성 환경에는 빠른 생성 환경을 사용할 수 있는 기본값 집합을 가지고 있습니다. 간단히 배포 이름을 입력하여 ASE를 신속하게 만들 수 있습니다. 그러면 다음을 이용하여 사용자와 가장 가까운 지역에서 ASE를 만듭니다.

- RFC1918 개인 주소 공간을 사용하여 512개의 주소를 가진 VNet
- 256 주소를 가진 서브넷
- 외부 VIP
- 2 P2 계산 리소스를 가진 프런트 엔드 풀
- 2 P1 계산 리소스를 가진 작업자 풀
- IP SSL에 사용할 단일 IP 주소

프런트 엔드 풀은 P2 이상이 필요합니다. ASE를 두려는 구독을 선택할 때는 주의가 필요합니다. ASE를 사용하여 콘텐츠를 호스트할 수 있는 계정은 ASE를 만드는 데 사용된 구독 내의 계정이어야 합니다.

![][1]

지정한 ASE 이름은 ASE에서 만드는 앱에 사용됩니다. ASE 이름이 appsvcenvdemo인 경우 하위 도메인 이름은 .*appsvcenvdemo.p.azurewebsites.net*입니다. 따라서 *mytestapp*이라는 앱을 만든 경우 해당 주소는 *mytestapp.appsvcenvdemo.p.azurewebsites.net*으로 지정될 수 있습니다. ASE의 이름에 공백을 사용할 수 없습니다. 이름에 대문자를 사용한 경우 전체 도메인 이름은 해당 이름의 소문자 버전이 됩니다. ILB를 사용하는 경우 ASE 이름이 하위 도메인에서 사용되지 않고 대신 ASE를 만드는 동안에 명시적으로 지정됩니다.

기본값이 있으면 특정 개수의 상황에 매우 유용하지만 자주 무언가를 조정해야 합니다. 다음 섹션은 ASE와 관련된 구성 섹션 각각을 안내합니다.


### 가상 네트워크 ###
ASE 만들기 프로세스 중에는 기존의 클래식 또는 Resource Manager VNet 중에서 선택하거나 새 클래식 VNet을 만들 수 있습니다.

기존 VNet을 선택하려는 경우 클래식 및 Resource Manager VNet이 둘 다 목록에 표시됩니다. 클래식 VNet의 위치 옆에는 클래식이라는 단어가 표시됩니다. 이러한 단어가 표시되지 않으면 Resource Manager VNet인 것입니다.

![][2]


VNet 만들기 UI로 이동하려면 다음을 제공해야 합니다.

- VNet 이름
- CIDR 표기법의 VNet 주소 범위
- 위치

VNet의 위치는 ASE의 위치입니다. 이 경우 Resource Manager VNet이 아닌 클래식 VNet이 생성됩니다.

공용 주소 범위 *또는* RFC1918 주소 공간(즉, 개인 주소) *중 하나*를 사용하는 가상 네트워크에 ASE를 배포할 수 있습니다. 공용 주소 범위를 가진 가상 네트워크를 사용하기 위해 사전에 서브넷을 만들고 ASE 만들기 UX에서 서브넷을 선택해야 합니다.

기존 VNET을 선택한 경우에는 사용할 서브넷을 지정하거나 새 서브넷을 만들어야 합니다. 서브넷은 8개 이상의 주소가 있어야 하고 이미 있는 것과 다른 리소스를 가질 수 없습니다. 할당된 VM이 이미 있는 서브넷을 사용하려면 ASE 생성은 실패합니다.

VNet을 지정하거나 선택한 후에 서브넷을 적절하게 만들거나 선택해야 합니다. 다음은 제공해야 할 세부 정보입니다.

- 서브넷 이름
- CIDR 표기법의 서브넷 범위

CIDR(Classless Inter-domain Routing) 표기에 대해 잘 모른다면 CIDR 값에서 슬래시로 구분되는 IP 주소 형태를 사용합니다. *10.0.0.0/22*과 같이 표시됩니다. CIDR 값은 표시된 IP 주소에 대해 마스킹된 주도 비트 수를 나타냅니다. 여기의 개념을 쉽게 표현하는 방법은 CIDR 값이 IP 범위를 제공하는 것입니다. 이 예제에서 10.0.0.0/22는 1024개의 주소 범위 또는 10.0.0.0~10.0.3.255.의 주소 범위를 의미합니다. /23은 512개의 주소 등을 의미합니다.

기존 VNet에서 서브넷을 만들려는 경우 ASE가 VNet과 동일한 리소스 그룹에 있게 됩니다. VNet에서 별도 리소스 그룹에 ASE을 두려면 단순히 VNet 및 서브넷을 모두 ASE를 만들기 전에 개별적으로 만듭니다.


#### 외부 또는 내부 VIP ####

기본적으로 VNet 구성은 외부 VIP 유형 및 IP 주소 1개로 설정됩니다. 외부 VIP 대신 ILB를 사용하려는 경우 VNet 구성으로 이동한 다음 VIP 유형을 내부로 변경합니다. 기본적으로 외부 VIP가 사용됩니다. VIP 유형을 내부로 변경할 때는 ASE에 대해 하위 도메인을 지정해야 합니다. ASE에 대한 VIP로 ILB를 사용하는 경우 몇 가지 장단점이 있습니다. 자세한 내용은 [앱 서비스 환경에서 내부 부하 분산 장치 사용][ILBASE]을 참조하세요.

![][4]

### 계산 리소스 풀 ###

ASE를 만드는 동안 각 리소스 풀에 해당 크기에 맞는 리소스의 수를 설정할 수 있습니다. ASE를 만들 때 리소스 풀의 크기를 설정할 수 있지만 또한 나중에 수동 크기 조정 옵션 또는 자동 크기 조정 옵션을 사용하여 조정할 수 있습니다.

앞에서 설명한 것처럼 ASE는 프런트 엔드 서버와 작업자로 구성됩니다. 프런트 엔드 서버는 앱 연결 부하를 처리하고 작업자는 앱 코드를 실행합니다. 프런트 엔드 서버는 전용 계산 리소스 풀에서 관리됩니다. 또한 작업자는 이름이 다음과 같은 3개의 개별 계산 리소스 풀에서 관리됩니다.

- 작업자 풀 1
- 작업자 풀 2
- 작업자 풀 3

간단한 웹앱에 대한 요청이 많은 경우 프런트 엔드를 확장하고 작업자를 줄일 수 있습니다. 트래픽이 적고 CPU 또는 메모리 사용량이 많은 웹앱이 있는 경우에는 많은 프런트 엔드가 필요 없지만 더 많거나 더 큰 작업자가 필요합니다.

![][3]

계산 리소스의 크기에 상관없이 최소 공간에는 2개의 프런트 엔드 서버와 2개의 작업자가 있습니다. 최대 총 55개의 계산 리소스를 사용하도록 ASE를 구성할 수 있습니다. 55개의 계산 리소스 중 50개만 워크로드를 호스트하는 데 사용할 수 있습니다. 이는 두 가지 이유 때문입니다. 프런트 엔드 계산 리소스는 최소 2개가 있어야 하므로 작업자 풀 할당을 지원할 수 있는 계산 리소스는 최대 53개가 남습니다. 내결함성을 제공하기 위해 다음 규칙에 따라 추가 계산 리소스를 할당해야 합니다.

- 각 작업자 풀에 워크로드를 할당할 수 없는 하나 이상의 추가 계산 리소스가 필요합니다.
- 풀에 있는 계산 리소스의 양이 특정 값을 초과하는 경우 다른 계산 리소스가 필요합니다.

단일 작업자 풀 내에서 내결함성 요구 사항은 작업자 풀에 할당된 X개의 리소스에 대한 지정된 값입니다.

- X가 2~20인 경우 워크로드에 사용할 수 있는 계산 리소스 양은 X-1입니다.
- X가 21~40인 경우 워크로드에 사용할 수 있는 계산 리소스 양은 X-2입니다.
- X가 41~53인 경우 워크로드에 사용할 수 있는 계산 리소스 양은 X-3입니다.

지정된 풀에 할당할 수 있는 계산 리소스의 양을 관리하는 것 외에 크기를 제어할 수도 있습니다. 앱 서비스 환경에서는 P1~P4의 레이블이 지정된 네 가지 크기 중에서 선택할 수 있습니다. 이러한 크기 및 해당 가격에 대한 자세한 내용은 [앱 서비스 가격][AppServicePricing]을 참조하세요. P1~P3 계산 리소스 크기는 다중 테넌트 환경에서 사용 할 수 있는 것과 동일합니다. P4 계산 리소스는 14GB RAM의 8개 코어를 제공하며, 앱 서비스 환경에서만 사용할 수 있습니다.

앱 서비스 환경의 가격은 할당된 계산 리소스에 따라 책정됩니다. 워크로드를 호스트하는지 여부에 상관없이 앱 서비스 환경에 할당된 계산 리소스에 대한 비용을 지불합니다.

기본적으로 ASE에는 IP SSL에 사용할 수 있는 IP 주소 하나가 제공됩니다. 추가 IP 주소가 필요한 경우 여기에서 지정하거나 만들기 후 관리할 수 있습니다.
  
### 앱 서비스 환경을 만든 후 ###

ASE를 만든 후 다음을 조정할 수 있습니다.

- 프런트 엔드 수(최소: 2개)
- 작업자 수(최소: 2개)
- IP SSL에 사용 가능한 IP 주소 수
- 프런트 엔드 또는 작업자에서 사용되는 계산 리소스 크기(프런트 엔드 최소 크기는 P2)

다음 사항은 변경할 수 없습니다.

- 위치
- 구독
- 리소스 그룹
- 사용되는 VNET
- 사용되는 서브넷

수동 크기 조정, 앱 서비스 환경 관리 및 모니터링에 대한 자세한 내용은 [앱 서비스 환경을 구성하는 방법][ASEConfig]을 참조하세요.

자동 크기 조정에 대한 정보는 여기에 가이드가 있습니다. [앱 서비스 환경에 대한 자동 크기 조정을 구성하는 방법][ASEAutoscale]

데이터베이스 및 저장소와 같은 사용자 지정 항목에 사용할 수 없는 추가 종속성이 있습니다. 이러한 종속성은 Azure에 의해 처리되며 시스템과 함께 제공됩니다. 시스템 저장소는 전체 앱 서비스 환경에 대해 최대 500GB를 지원하고 데이터베이스는 시스템 규모의 필요에 따라 Azure에서 조정됩니다.


## 시작
앱 서비스 환경에 대한 모든 문서와 지침은 [응용 프로그램 서비스 환경의 추가 정보](../app-service/app-service-app-service-environments-readme.md)에 있습니다.

앱 서비스 환경을 시작하려면 [앱 서비스 환경 소개][WhatisASE]를 참조하세요.

Azure 앱 서비스 플랫폼에 대한 자세한 내용은 [Azure 앱 서비스][AzureAppService]를 참조하세요.

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]
 

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-basecreateblade.png
[2]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-vnetcreation.png
[3]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-resources.png
[4]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-externalvip.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[ASEConfig]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[ASEAutoscale]: http://azure.microsoft.com/documentation/articles/app-service-environment-auto-scale/
[ILBASE]: http://azure.microsoft.com/documentation/articles/app-service-environment-with-internal-load-balancer/

<!---HONumber=AcomDC_0713_2016-->