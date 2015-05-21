<properties 
	pageTitle="앱 서비스 환경에서 백 엔드 리소스에 안전하게 연결" 
	description="앱 서비스 환경에서 백 엔드 리소스에 안전하게 연결하는 방법에 대해 알아봅니다." 
	services="app-service" 
	documentationCenter="" 
	authors="ccompy" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/14/2015" 
	ms.author="stefsh"/>

# 앱 서비스 환경에서 백 엔드 리소스에 안전하게 연결 #

## 개요 ##
앱 서비스 환경은 항상 지역 [가상 네트워크][virtualnetwork]의 서브넷에 만들어지므로 앱 서비스 환경에서 다른 백 엔드 리소스로의 아웃바운드 연결은 가상 네트워크를 통해서만 이동할 수 있습니다.

예를 들어 잠긴 포트 1433을 통해 가상 컴퓨터의 클러스터에서 실행되는 SQL Server가 있을 수 있습니다. 끝점은 동일한 가상 네트워크에 있는 다른 리소스의 액세스만 허용하도록 ACL에 포함될 수 있습니다.

또 다른 예로, 중요한 끝점은 온-프레미스에서 실행되고 [사이트 간][SiteToSite] 또는 [Azure ExpressRoute][ExpressRoute] 연결을 통해 Azure에 연결될 수 있습니다. 따라서 사이트 간 또는 ExpressRoute 터널에 연결된 가상 네트워크의 리소스만 온-프레미스 끝점에 액세스할 수 있습니다.

이 모든 시나리오에 대해 앱 서비스 환경에서 실행되는 앱은 다양한 서버 및 리소스에 안전하게 연결할 수 있습니다. 앱 서비스 환경에서 실행되는 앱에서 동일한 가상 네트워크에 있는\(또는 동일한 가상 네트워크에 연결된\) 개인 끝점으로의 아웃바운드 트래픽은 가상 네트워크를 통해서만 이동합니다. 개인 끝점으로의 아웃바운드 트래픽은 공용 인터넷을 통해 이동하지 않습니다.


## SQL Server 가상 컴퓨터에 연결
일반적인 SQL Server 구성에는 포트 1433에서 수신 대기하는 끝점이 있습니다.

![SQL Server 끝점][SqlServerEndpoint]

이 끝점으로 트래픽을 제한하는 두 가지 방법이 있습니다.


- [네트워크 액세스 제어 목록][NetworkAccessControlLists](네트워크 ACL\)

- [네트워크 보안 그룹][NetworkSecurityGroups]


## 네트워크 ACL을 사용하여 액세스 제한

네트워크 액세스 제어 목록을 사용하여 포트 1433의 보안을 유지할 수 있습니다. 아래 예제에서는 가상 네트워크 내부에서 시작되는 클라이언트 주소는 허용하고 다른 모든 클라이언트에 대한 액세스는 차단합니다.

![네트워크 액세스 제어 목록 예][NetworkAccessControlListExample]

SQL Server와 동일한 가상 네트워크의 앱 서비스 환경에서 실행되는 모든 응용 프로그램은 SQL Server 가상 컴퓨터에 대한 **VNet 내부** IP 주소를 사용하여 SQL Server 인스턴스에 연결할 수 있습니다.

아래 예제 연결 문자열은 해당 개인 IP 주소를 사용하여 SQL Server를 참조합니다.

    Server=tcp:10.0.1.6;Database=MyDatabase;User ID=MyUser;Password=PasswordHere;provider=System.Data.SqlClient

가상 컴퓨터에 공용 끝점도 있지만 공용 IP 주소를 사용한 연결 시도는 네트워크 ACL 때문에 거부됩니다.

## 네트워크 보안 그룹을 사용하여 액세스 제한
액세스를 보호하는 또 다른 방법은 네트워크 보안 그룹을 사용하는 것입니다. 개별 가상 컴퓨터 또는 가상 컴퓨터가 포함된 서브넷에 네트워크 보안 그룹을 적용할 수 있습니다.

먼저 네트워크 보안 그룹을 만들어야 합니다.

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"

VNet 내부 트래픽으로만 액세스를 제한하는 작업은 네트워크 보안 그룹을 사용할 경우 매우 간단합니다. 네트워크 보안 그룹의 기본 규칙은 동일한 가상 네트워크에 있는 다른 네트워크 클라이언트의 액세스만 허용합니다.

따라서 SQL Server에 대한 액세스를 잠그는 것은 SQL Server를 실행하는 가상 컴퓨터 또는 가상 컴퓨터가 포함된 서브넷에 네트워크 보안 그룹을 해당 기본 규칙으로 적용하는 것만큼 간단합니다.

아래 샘플에서는 서브넷에 네트워크 보안 그룹을 적용합니다.

    Get-AzureNetworkSecurityGroup -Name "testNSGExample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-1'
    
최종 결과는 VNet 내부 액세스는 허용하는 반면, 외부 액세스는 차단하는 보안 규칙 집합입니다.

![기본 네트워크 보안 규칙][DefaultNetworkSecurityRules]


## 시작

앱 서비스 환경을 시작하려면 [앱 서비스 환경 소개][IntroToAppServiceEnvironment]를 참조하세요.

앱 서비스 환경으로의 인바운드 트래픽을 제어하는 방법에 대한 자세한 내용은 [앱 서비스 환경으로의 인바운드 트래픽 제어][ControlInboundASE]를 참조하세요.

Azure 앱 서비스 플랫폼에 대한 자세한 내용은 [Azure 앱 서비스][AzureAppService]를 참조하세요.

<!-- LINKS -->
[virtualnetwork]: https://msdn.microsoft.com/library/azure/dn133803.aspx
[ControlInboundTraffic]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[SiteToSite]: https://msdn.microsoft.com/library/azure/dn133795.aspx
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[NetworkAccessControlLists]: https://msdn.microsoft.com/library/azure/dn376541.aspx
[NetworkSecurityGroups]: https://msdn.microsoft.com/library/azure/dn848316.aspx
[IntroToAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[ControlInboundASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/

<!-- IMAGES -->
[SqlServerEndpoint]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/SqlServerEndpoint01.png
[NetworkAccessControlListExample]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/NetworkAcl01.png
[DefaultNetworkSecurityRules]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/DefaultNetworkSecurityRules01.png
<!--HONumber=52-->
