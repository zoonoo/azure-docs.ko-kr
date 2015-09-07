<properties 
	pageTitle="Express 경로를 사용하기 위한 네트워크 구성 세부 정보"
	description="가상 네트워크에서 앱 서비스 환경을 실행하기 위해 네트워크 구성 세부 정보를 Express 경로 회로에 연결합니다."
	services="app-service\web"
	documentationCenter=""
	authors="stefsch"
	manager="nirma"
	editor=""/>

<tags 
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/30/2015"
	ms.author="stefsch"/>

# Express 경로를 사용하는 앱 서비스 환경에 대한 네트워크 구성 세부 정보 

## 개요 ##
고객은 [Azure Express 경로][ExpressRoute] 회로를 가상 네트워크 인프라로 연결 할 수 있습니다. 따라서 Azure로 온-프레미스 네트워크를 확장합니다. 앱 서비스 환경은 [가상 네트워크][virtualnetwork]인프라의 서브넷에서 만들수 있습니다. 다음 앱 서비스 환경에서 실행 중인 앱은 Express 경로 연결을 통해서만 액세스할 수 있는 백엔드 리소스에 대한 보안 연결을 설정할 수 있습니다.

## 필요한 네트워크 연결 ##
Express 루트에 연결된 가상 네트워크에서 처음에 충족되지 않는 앱 서비스 환경에 대한 네트워크 연결 요구 사항이 있습니다.

앱 서비스 환경은 기능을 제대로 작동시키기 위해 다음 사항을 전부 요구합니다.


-  앱 서비스 환경처럼 동일한 지역에 위치한 Azure 저장소 및 SQL 데이터베이스 리소스 모두에 대한 아웃바운드 네트워크 연결. 네트워크 경로는 내부 회사 프록시를 통과할 수 없는 이유는, 통과하게 되면 아웃바운드 네트워크 트래픽의 효과적인 NAT 주소가 변경 될수 있기 때문입니다. Azure 저장소와 SQL 데이터베이스 긑점에 연결된 앱 서비스 환경의 아웃바운드 네트워크 트래픽의 NAT 주소 변경은 연결 실패를 초래합니다.
-  DNS 구성에 대한 가상 네트워크는 **.file.core.windows.net*, **.blob.core.windows.net* 및 **.database.windows.net*과 같은 Azure 제어 도메인 내에서 끝점을 해결할 수 있어야 합니다.
-  가상 네트워크의 DNS 구성은 앱 서비스 환경이 생성될 때마다 안정적으로 유지되어야 하며, 재구성 및 앱 서비스 환경 규모 변경 동안에도 마찬가지입니다.   
-  앱 서비스 환경에 대한 포트를 필요로 하는 인바운드 네티워크는 [문서][requiredports] 내에서 설명한 항목들을 허용해야 합니다.

DNS 요구 사항은 가상 네트워크의 유효한 DNS 구성을 확인하여 충족시킬 수 있습니다.

인바운드 네트워크 액세스 요구 사항은 이 [문서][requiredports]에 설명된 대로 앱 서비스 환경의 서브넷에서 필요한 액세스를 허용하는 [네트워크 보안 그룹][NetworkSecurityGroups]을 구성하여 충족시킬 수 있습니다.

## 앱 서비스 환경에 대한 아웃바운드 네트워크 연결을 사용하도록 설정##
기본적으로, 새로 생성된 Express Route 회로는 아웃바운드 인터넷 연결을 허용하는 기본 경로를 알립니다. 이 앱 서비스 환경 구성을 사용하여 다른 Azure 끝점에 연결할 수 있게 만듭니다.

그러나 고객의 일반적인 구성은 고객의 프록시/방화벽 인프라를 통한 온-프레미스 흐름 대신 아웃바운드 인터넷 트래픽을 강제하는 기본 경로로 정의되어 있습니다. 이 트래픽 흐름이 앱 서비스 환경을 계속 중단시키는 이유는 아웃바운드 트래픽은 온-프레미스를 막거나 다양한 Azure 끝점에서 더이상 작동하지 않는 주소의 인식 불가능한 집합을 NAT하기 때문입니다.

해결책은 하나의(혹은 그이상) 사용자 정의 루트(UDRs)를 앱 서비스 환경을 포함하는 서브넷에 정의하는 것입니다. UDR이 정의한 특정 서브넷 경로는 기본 경로대신 적용됩니다.

사용자 정의 경로의 배경 정보는 [개요][UDROverview]에서 사용 가능합니다.

사용자 정의 경로 생성 및 구성의 세부 정보는 [방법 가이드][UDRHowTo]에서 사용 가능합니다.

## 앱 서비스 환경에 대한 UDR 구성 예 ##

**필수 구성 요소**

1. [Azure 다운로드 페이지][AzureDownloads]에서 가장 최신 Azure Powershell을 설치합니다(2015년 6월 이후 버전). "명령줄 도구"> “설치”링크> Windows PowerShell은 최신 Powershell cmdletsfmf 설치합니다.

2. 앱 서비스 환경에 의해 배타적 사용으로 만들어진 고유한 서브넷을 권장합니다. UDRL이 오직 앱 서비스 환경에 대한 아웃바운드 트래픽만을 여는 서브넷에 적용되는지 확인합니다.
3. **중요**: 다음 구성단계를 수행한 **후**까지는 앱 서비스 환경을 배포하지 않습니다. 아웃바운드 네트워크 연결이 앱 서비스 환경을 배포하기 전에 사용할 수 있는지 확인합니다.

**1 단계: 명명된 경로 테이블 만들기**

다음 코드 조각으로 서쪽 미국 Azure 지역에서 "DirectInternetRouteTable"이라고 하는 경로 테이블을 만듭니다.

    New-AzureRouteTable -Name 'DirectInternetRouteTable' -Location uswest

**2 단계: 경로 테이블에서 하나 이상의 경로 만들기**

아웃 바운드 인터넷 액세스를 사용 하기 위해 경로 테이블에 하나 이상의 경로 추가 해야 합니다. 다음 예제에서는 미국 서부 지역에서 사용 가능한 모든 Azure 주소에 적용하는 충분한 경로를 추가합니다.

    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 1' -AddressPrefix 23.0.0.0/8 -NextHopType Internet
    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 2' -AddressPrefix 40.0.0.0/8 -NextHopType Internet
    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 3' -AddressPrefix 65.0.0.0/8 -NextHopType Internet
    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 4' -AddressPrefix 104.0.0.0/8 -NextHopType Internet
    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 5' -AddressPrefix 137.0.0.0/8 -NextHopType Internet
    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 6' -AddressPrefix 138.0.0.0/8 -NextHopType Internet
    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 7' -AddressPrefix 157.0.0.0/8 -NextHopType Internet
    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 8' -AddressPrefix 168.0.0.0/8 -NextHopType Internet
    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 9' -AddressPrefix 191.0.0.0/8 -NextHopType Internet


Azure에 의해 사용되고 있는 CIDR 범위의 포괄적이고 업데이트된 목록에 대해서는 [Microsoft 다운로드 센터][DownloadCenterAddressRanges]에서 모든 범위를 포함한 Xml 파일을 다운로드할 수 있습니다.

**참고:**일부 지점의 약식된 0.0.0.0/0의 CIDR 약칭은 *AddressPrefix*매개 변수로 사용할 수 있습니다. 이 약칭은 "모든 인터넷 주소"와 같습니다. 이제 개발자가 CIDR 범위의 broad set 대신 앱 서비스 환경이 배포된 지역 안에서 사용 가능한 모든 Azure 주소 범위를 사용해야 합니다.

**3 단계: 앱 서비스 환경에 포함된 서브넷에 경로 테이블 연결**

마지막으로 앱 서비스 환경이 배포될 서브넷에 경로 테이블을 연결하는 구성 단계입니다. 다음 명령은 "DirectInternetRouteTable"를 "ASESubnet"에 연결하여 결국 앱 서비스 환경을 포함합니다.

    Set-AzureSubnetRouteTable -VirtualNetworkName 'YourVirtualNetworkNameHere' -SubnetName 'ASESubnet' -RouteTableName 'DirectInternetRouteTable'


**4 단계: 최종 단계**

경로 테이블이 서브넷에 바인딩된 후 첫번째로 테스트 하면서 원하는 효과를 확인하는 것을 권장합니다. 예를 들어, 서브넷에 가상 컴퓨터를 배포하고 확인합니다.


- Azure 끝점의 아웃 바운드 트래픽은 Express 경로 회로 아래로 흐르지 않습니다.
- Azure 끝점에 대한 DNS 조회를 적절하게 확인합니다. 

위의 단계가 확인되면 앱 서비스 환경을 만드는 진행할 수 있습니다!

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
 

<!-- IMAGES -->

<!---HONumber=August15_HO9-->