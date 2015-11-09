<properties 
	pageTitle="Express 경로를 사용하기 위한 네트워크 구성 세부 정보" 
	description="가상 네트워크에서 앱 서비스 환경을 실행하기 위해 네트워크 구성 세부 정보를 Express 경로 회로에 연결합니다." 
	services="app-service" 
	documentationCenter="" 
	authors="stefsch" 
	manager="nirma" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/23/2015" 
	ms.author="stefsch"/>

# Express 경로를 사용하는 앱 서비스 환경에 대한 네트워크 구성 세부 정보 

## 개요 ##
고객은 [Azure Express 경로][ExpressRoute] 회로를 가상 네트워크 인프라로 연결 할 수 있습니다. 따라서 Azure로 온-프레미스 네트워크를 확장합니다. 앱 서비스 환경은 [가상 네트워크][virtualnetwork]인프라의 서브넷에서 만들 수 있습니다. 다음 앱 서비스 환경에서 실행 중인 앱은 Express 경로 연결을 통해서만 액세스할 수 있는 백엔드 리소스에 대한 보안 연결을 설정할 수 있습니다.

**참고:** "v2" 가상 네트워크에는 앱 서비스 환경을 만들 수 없습니다. 앱 서비스 환경은 현재 클래식 "v1" 가상 네트워크에서만 지원됩니다.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## 필요한 네트워크 연결 ##
Express 루트에 연결된 가상 네트워크에서 처음에 충족되지 않는 앱 서비스 환경에 대한 네트워크 연결 요구 사항이 있습니다. 앱 서비스 환경은 기능을 제대로 작동시키기 위해 다음 사항을 전부 요구합니다.


-  전세계 Azure 저장소 끝점에 아웃바운드 네트워크 연결. 앱 서비스 환경과 동일한 지역에 있는 끝점 뿐만 아니라 **다른** Azure 지역에 있는 저장소 끝점을 포함합니다. 다음 DNS 도메인에서 Azure 저장소 끝점은 다음을 확인합니다. *table.core.windows.net*, *blob.core.windows.net*, *queue.core.windows.net* 및 *file.core.windows.net*.  
-  앱 서비스 환경처럼 동일한 지역에 위치한 Sql DB 끝점에 대한 아웃바운드 네트워크 연결. SQl DB 끝점은 다음 도메인에서 해결합니다. *database.windows.net*
-  Azure 관리 평면 끝점에 아웃바운드 네트워크 연결(ASM 및 ARM 끝점 모두) *management.core.windows.net* 및 *management.azure.com* 모두에 아웃 바운드 연결을 포함합니다. 
-  아웃바운드 네트워크 연결을 *mscrl.microsoft.com* 및 *crl.microsoft.com*. SSL 기능을 지원하는 데 필요합니다.
-  가상 네트워크에 대한 DNS 구성은 이전 시점에 언급된 끝점 및 도메인을 모두 해결할 수 있어야 합니다. 이러한 끝점을 해결할 수 없으면 앱 서비스 환경 만들기 시도는 실패하고 기존 앱 서비스 환경도 비정상으로 표시됩니다.
-  사용자 지정 DNS 서버가 VPN 게이트웨이의 반대쪽 끝에 있는 경우 앱 서비스 환경을 포함하는 서브넷에서 DNS 서버에 연결할 수 있어야 합니다. 
-  아웃바운드 네트워크 경로는 내부 회사 프록시를 통과할 수 없고 온-프레미스에 강제로 터널링될 수 없습니다. 이렇게 하면 앱 서비스 환경에서 아웃바운드 네트워크 트래픽의 효율적인 NAT 주소를 변경합니다. 앱 서비스 환경의 아웃바운드 네트워크 트래픽의 NAT 주소 변경은 위에 나열된 끝점 대부분에 연결 실패를 초래합니다. 실패한 앱 서비스 환경 만들기 시도라는 결과로 나타날 뿐만 아니라 이전의 정상 앱 서비스 환경도 비정상으로 표시됩니다.  
-  이 [문서][requiredports]에 설명된 대로 앱 서비스 환경에 필요한 포트에 대한 인바운드 네트워크 액세스가 허용되어야 합니다.

유효한 DNS 인프라를 구성하고 가상 네트워크에 유지 관리하여 DNS 요구를 충족할 수 있습니다. 앱 서비스 환경을 만든 후에 어떤 이유로든 DNS 구성이 변경되면 개발자는 앱 서비스 환경을 강제하여 새 DNS 구성을 선택할 수 있습니다. [새 관리 포털][NewPortal]에서 앱 서비스 환경 관리 블레이드의 상단에 있는 "다시 시작" 아이콘을 사용하여 롤링 환경의 트리거를 다시 부팅하면 새 DNS 구성을 선택하는 환경이 발생합니다.

인바운드 네트워크 액세스 요구 사항은 이 [문서][requiredports]에 설명된 대로 앱 서비스 환경의 서브넷에서 필요한 액세스를 허용하는 [네트워크 보안 그룹][NetworkSecurityGroups]을 구성하여 충족시킬 수 있습니다.

## 앱 서비스 환경에 대한 아웃바운드 네트워크 연결을 사용하도록 설정##
기본적으로, 새로 생성된 Express Route 회로는 아웃바운드 인터넷 연결을 허용하는 기본 경로를 알립니다. 이 앱 서비스 환경 구성을 사용하여 다른 Azure 끝점에 연결할 수 있게 만듭니다.

그러나 고객의 일반적인 구성은 온-프레미스 흐름 대신 아웃바운드 인터넷 트래픽을 강제하는 기본 경로(0.0.0.0/0)로 정의되어 있습니다. 이 트래픽 흐름이 앱 서비스 환경을 계속 중단시키는 이유는 아웃바운드 트래픽은 온-프레미스를 막거나 다양한 Azure 끝점에서 더이상 작동하지 않는 주소의 인식 불가능한 집합을 NAT하기 때문입니다.

해결책은 하나의(혹은 그이상) 사용자 정의 루트(UDRs)를 앱 서비스 환경을 포함하는 서브넷에 정의하는 것입니다. UDR이 정의한 특정 서브넷 경로는 기본 경로대신 적용됩니다.

가능하면 다음 구성을 사용하는 것이 좋습니다.

- Express 경로 구성은 0.0.0.0/0을 보급하고 기본적으로 모든 아웃바운드 트래픽 온-프레미스를 강제로 터널링합니다.
- 앱 서비스 환경을 포함하는 서브넷에 적용된 UDR은 인터넷의 다음 홉 형식을 사용하여 0.0.0.0/0를 정의합니다.(이 예제는 이 문서에서 아래쪽에 있음)

이러한 단계의 결합된 효과는 서브넷 수준 UDR이 강제된 터널링에 Express 경로를 담당하고 앱 서비스 환경에서 아웃바운드 인터넷 액세스를 보장합니다.

**중요:** UDR에 정의된 경로는 Express 경로 구성에서 보급된 경로에 우선 하도록 충분히 구체적**이어야** 합니다. 아래 예제에서는 광범위한 0.0.0.0/0 주소 범위를 사용하고 따라서 잠재적으로 보다 구체적인 주소 범위를 사용하는 경로 알림에서 실수로 재정의될 수 있습니다.

**매우 중요:** 앱 서비스 환경은 **공용 피어링 경로에서 개인 피어링 경로로 경로의 교차 보급을 잘못**한 Express 경로 구성으로 지원되지 않습니다. 구성된 공용 피어링이 있는 Express 경로 구성은 다양한 Microsoft Azure IP 주소 범위 집합에 대해 Microsoft에서 경로 보급을 받습니다. 이러한 주소 범위의 교차 보급을 개인 피어링 경로에 잘못한 경우 앱 서비스 환경의 서브넷에서 모든 아웃바운드 네트워크 패킷이 고객의 온-프레미스 네트워크 인프라에 강제 터널링되는 잘못된 최종 결과를 발생시킵니다. 이 네트워크 흐름은 앱 서비스 환경을 중단시킵니다. 이 문제를 해결하려면 공용 피어링 경로에서 개인 피어링 경로로 이어진 교차 보급 경로를 중지합니다.

사용자 정의 경로의 배경 정보는 [개요][UDROverview]에서 사용 가능합니다.

사용자 정의 경로 생성 및 구성의 세부 정보는 [방법 가이드][UDRHowTo]에서 사용 가능합니다.

## 앱 서비스 환경에 대한 UDR 구성 예 ##

**필수 구성 요소**

1. [Azure 다운로드 페이지][AzureDownloads]에서 가장 최신 Azure Powershell을 설치합니다(2015년 6월 이후 버전). "명령줄 도구"> “설치”링크> Windows PowerShell은 최신 Powershell cmdletsfmf 설치합니다.

2. 앱 서비스 환경에 의해 배타적 사용으로 만들어진 고유한 서브넷을 권장합니다. UDR이 오직 앱 서비스 환경에 대한 아웃바운드 트래픽만을 여는 서브넷에 적용되는지 확인합니다.
3. **중요**: 다음 구성 단계를 수행하기 **전**에는 앱 서비스 환경을 배포하지 마세요. 아웃바운드 네트워크 연결이 앱 서비스 환경을 배포하기 전에 사용할 수 있는지 확인합니다.

**1 단계: 명명된 경로 테이블 만들기**

다음 코드 조각으로 서쪽 미국 Azure 지역에서 "DirectInternetRouteTable"이라고 하는 경로 테이블을 만듭니다.

    New-AzureRouteTable -Name 'DirectInternetRouteTable' -Location uswest

**2 단계: 경로 테이블에서 하나 이상의 경로 만들기**

아웃 바운드 인터넷 액세스를 사용 하기 위해 경로 테이블에 하나 이상의 경로 추가 해야 합니다.

인터넷에 대한 아웃바운드 액세스를 구성하려면 아래와 같이 0.0.0.0/0에 대한 경로를 정의하는 것이 좋습니다.
  
    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 0' -AddressPrefix 0.0.0.0/0 -NextHopType Internet

0\.0.0.0/0가 광범위한 주소 범위이고 따라서 Express 경로에서 보급되는 보다 구체적인 주소 범위로 재정의된다는 점에 주의하세요. 다시 이전 권장 사항을 반복하려면 0.0.0.0/0 경로를 가진 UDR은 0.0.0.0/0을 보급하는 Exress 경로 구성과 함께 사용해야 합니다.

대신 Azure에서 사용하는 CIDR 범위의 포괄적이고 업데이트된 목록을 다운로드할 수 있습니다. 모든 Azure IP 주소 범위를 포함하는 Xml 파일은 [Microsoft 다운로드 센터][DownloadCenterAddressRanges]에서 사용할 수 있습니다.

그러나 이러한 범위가 시간이 지나면 변경되고, 따라서 UDR에 대한 주기적인 수동 업데이트는 동기화 상태를 유지할 필요가 있습니다. 또한 단일 UDR에서 상한이 100개의 경로이기 때문에 Azure IP 주소 범위를 "요약"하여 100개의 경로 제한에 맞추어야 하며 이 때 UDR 경로는 Express 경로에서 보급된 경로 보다 정확해야 한다는 점을 염두합니다.


**3 단계: 앱 서비스 환경에 포함된 서브넷에 경로 테이블 연결**

마지막으로 앱 서비스 환경이 배포될 서브넷에 경로 테이블을 연결하는 구성 단계입니다. 다음 명령은 "DirectInternetRouteTable"를 "ASESubnet"에 연결하여 결국 앱 서비스 환경을 포함합니다.

    Set-AzureSubnetRouteTable -VirtualNetworkName 'YourVirtualNetworkNameHere' -SubnetName 'ASESubnet' -RouteTableName 'DirectInternetRouteTable'


**4 단계: 최종 단계**

경로 테이블이 서브넷에 바인딩된 후 첫번째로 테스트 하면서 원하는 효과를 확인하는 것을 권장합니다. 예를 들어, 서브넷에 가상 컴퓨터를 배포하고 확인합니다.


- 이 문서의 앞에서 언급한 Azure 및 비 Azure 끝점 모두에 대한 아웃바운드 트래픽은 Express 경로 회로로 이동하지 **않습니다**. 서브넷에서 아웃바운드 트래픽이 강제로 터널링된 온-프레미스인 경우 앱 서비스 환경을 만드는 데 실패하기 때문에 이 동작을 확인하는 것이 매우 중요합니다. 
- 앞서 언급된 끝점에 대한 DNS 조회는 모두 적절하게 해결합니다. 

위의 단계가 확인되면 서브넷이 앱 서비스 환경을 만들 때 "비어" 있어야 하기 때문에 가상 컴퓨터를 삭제해야 합니다.
 
그런 다음 앱 서비스 환경을 계속 만듭니다!

## 시작

앱 서비스 환경을 시작하려면 [앱 서비스 환경 소개][IntroToAppServiceEnvironment]를 참조하세요.

Azure 앱 서비스 플랫폼에 대한 자세한 내용은 [Azure 앱 서비스][AzureAppService]를 참조하세요.

<!-- LINKS -->
[virtualnetwork]: http://azure.microsoft.com/services/virtual-network/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[requiredports]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[NetworkSecurityGroups]: http://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[UDROverview]: http://azure.microsoft.com/documentation/articles/virtual-networks-udr-overview/
[UDRHowTo]: http://azure.microsoft.com/documentation/articles/virtual-networks-udr-how-to/
[HowToCreateAnAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[AzureDownloads]: http://azure.microsoft.com/downloads/
[DownloadCenterAddressRanges]: http://www.microsoft.com/download/details.aspx?id=41653
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[IntroToAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[NewPortal]: https://portal.azure.com
 

<!-- IMAGES -->

<!---HONumber=Nov15_HO1-->