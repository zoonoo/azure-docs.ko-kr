<properties 
	pageTitle="앱 서비스 환경으로의 인바운드 트래픽을 제어하는 방법"
	description="앱 서비스 환경으로의 인바운드 트래픽을 제어하도록 네트워크 보안 규칙을 구성하는 방법에 알아봅니다."
	services="app-service\web"
	documentationCenter=""
	authors="ccompy"
	manager="wpickett"
	editor=""/>

<tags 
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/30/2015"
	ms.author="stefsh"/>

# 앱 서비스 환경으로의 인바운드 트래픽을 제어하는 방법

## 개요 ##
앱 서비스 환경은 항상 지역 [가상 네트워크][virtualnetwork]의 서브넷에 만듭니다. 앱 서비스 환경을 만들 때 새 지역 가상 네트워크 및 새 서브넷을 정의할 수 있습니다. 또는 기존 지역 가상 네트워크 및 기존 서브넷에 앱 서비스 환경을 만들 수 있습니다. 앱 서비스 환경을 만드는 방법에 대한 자세한 내용은 [앱 서비스 환경을 만드는 방법][HowToCreateAnAppServiceEnvironment]을 참조하세요.

앱 서비스 환경은 항상 서브넷 내에 만들어야 합니다. 서브넷은 특정 업스트림 IP 주소의 HTTP 및 HTTPS 트래픽만 허용되도록 업스트림 장치 및 서비스 뒤의 인바운드 트래픽을 잠그는 데 사용할 수 있는 네트워크 경계를 제공하기 때문입니다.

서브넷의 인바운드 및 아웃바운드 네트워크 트래픽은 [네트워크 보안 그룹][NetworkSecurityGroups]을 사용하여 제어합니다. 인바운드 트래픽을 제어하려면 네트워크 보안 그룹에 네트워크 보안 규칙을 만든 다음 앱 서비스 환경이 포함된 서브넷에 네트워크 보안 그룹을 할당해야 합니다.

네트워크 보안 그룹을 서브넷에 할당하면 네트워크 보안 그룹에 정의된 허용 및 거부 규칙에 따라 앱 서비스 환경의 앱에 대한 인바운드 트래픽이 허용/거부됩니다.

## 앱 서비스 환경에서 사용되는 네트워크 포트 ##
네트워크 보안 그룹을 사용하여 인바운드 네트워크 트래픽을 잠그기 전에 앱 서비스 환경에서 사용되는 필수 및 선택적 네트워크 포트 집합을 알아야 합니다. 일부 포트에 대한 트래픽을 실수로 닫으면 앱 서비스 환경의 기능이 손실될 수 있습니다.

다음은 앱 서비스 환경에서 사용되는 포트 목록입니다.

- 454: Azure 인프라에서 앱 서비스 환경을 관리 및 유지 보수하는 데 사용되는 **필수 포트**입니다. 이 포트를 차단하지 마세요.
- 455: Azure 인프라에서 앱 서비스 환경을 관리 및 유지 보수하는 데 사용되는 **필수 포트**입니다. 이 포트를 차단하지 마세요.
- 80: 앱 서비스 환경의 앱 서비스 계획에서 실행되는 앱에 대한 인바운드 HTTP 트래픽의 기본 포트입니다.
- 443: 앱 서비스 환경의 앱 서비스 계획에서 실행되는 앱에 대한 인바운드 SSL 트래픽의 기본 포트입니다.
- 21: FTP에 대한 컨트롤 채널입니다. FTP를 사용하지 않는 경우 이 포트를 안전하게 차단할 수 있습니다.
- 10001~10020: FTP에 대한 데이터 채널입니다. 컨트롤 채널과 마찬가지로 FTP를 사용하지 않는 경우 이러한 포트를 안전하게 차단할 수 있습니다(**참고:** FTP 데이터 채널은 미리 보기 중 변경될 수 있음).
- 4016: Visual Studio 2012를 통한 원격 디버깅에 사용됩니다. 이 기능을 사용하지 않는 경우 이 포트를 안전하게 차단할 수 있습니다.
- 4018: Visual Studio 2013을 통한 원격 디버깅에 사용됩니다. 이 기능을 사용하지 않는 경우 이 포트를 안전하게 차단할 수 있습니다.
- 4020: Visual Studio 2015를 통한 원격 디버깅에 사용됩니다. 이 기능을 사용하지 않는 경우 이 포트를 안전하게 차단할 수 있습니다.

## 아웃 바운드 연결 및 DNS 요구 사항 ##
앱 서비스 환경이 제대로 작동하려면, 동일한 Azure 지역의 SQL 데이터베이스 같은 Azure 저장소의 아웃바운드 액세스 또한 필요합니다. 가상 네트워크에서 아웃바운드 인터넷 액세스가 차단 될 경우, 앱 서비스 환경은 이러한 Azure 끝점에 액세스 할 수 없습니다.

고객들은 가상 네트워크에서 사용자 지정 DNS 서버를 구성할 수도 있습니다. 앱 서비스 환경은 Azure 끝점을 *.database.windows.net, *.file.core.windows.net 및 *.blob.core.windows.net에서 해결할 수 있어야 합니다.

Vnet의 모든 사용자 지정 DNS 서버는 앱 서비스 환경 생성보다 미리 설치하는 것을 권장합니다. 앱 서비스 환경이 만들어질 때 가상 네트워크의 DNS 구성이 변경될 경우, 앱 서비스 환경 생성 과정에 장애가 발생합니다.

## 네트워크 보안 그룹 만들기 ##
네트워크 보안 그룹의 작동 방식에 대한 자세한 내용은 다음 [정보][NetworkSecurityGroups]를 참조하세요. 다음은 네트워크 보안 그룹을 구성하고 앱 서비스 환경이 포함된 서브넷에 적용하는 방법을 중심으로 설명된 네트워크 보안 그룹에 대한 세부 정보입니다.

네트워크 보안 그룹은 먼저 구독과 연결된 독립 실행형 엔터티로 만들어집니다. 네트워크 보안 그룹은 Azure 지역에 만들어지므로 앱 서비스 환경과 동일한 지역에 만들어졌는지 확인해야 합니다.

네트워크 보안 그룹을 만드는 방법은 다음과 같습니다.

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"

네트워크 보안 그룹을 만들면 하나 이상의 네트워크 보안 규칙이 해당 그룹에 추가됩니다. 규칙 집합은 시간이 지남에 따라 변경될 수 있으므로 시간이 지남에 따라 추가 규칙을 쉽게 삽입할 수 있도록 규칙 우선 순위에 사용되는 번호 매기기 체계를 지정하는 것이 좋습니다.

아래 예제에서는 Azure 인프라에서 앱 서비스 환경을 관리 및 유지 보수하는 데 필요한 관리 포트에 대한 액세스 권한을 명시적으로 부여하는 규칙을 보여 줍니다. 모든 관리 트래픽은 SSL을 통해 흐르며 클라이언트 인증서로 보안이 유지되므로 포트가 열려 있는 경우에도 Azure 관리 인프라 이외의 엔터티는 액세스할 수 없습니다.


    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP
    

포트 80 및 443에 대한 액세스를 잠가 업스트림 장치 또는 서비스 뒤의 앱 서비스 환경을 “숨길" 경우 업스트림 IP 주소를 알아야 합니다. 예를 들어 WAF(웹 응용 프로그램 방화벽)를 사용하는 경우 WAF에는 다운스트림 앱 서비스 환경으로 트래픽을 프록시할 때 사용하는 고유한 IP 주소가 있습니다. 이 IP 주소를 네트워크 보안 규칙의 *SourceAddressPrefix* 매개 변수에서 사용해야 합니다.

아래 예제에서는 특정 업스트림 IP 주소의 인바운드 트래픽이 명시적으로 허용됩니다. 주소 *1.2.3.4*는 업스트림 WAF의 IP 주소에 대한 자리 표시자로 사용됩니다. 업스트림 장치 또는 서비스에서 사용하는 주소와 일치하도록 값을 변경합니다.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTP" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTPS" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP
    
FTP 지원이 필요한 경우 다음 규칙을 템플릿으로 사용하여 FTP 제어 포트 및 데이터 채널 포트에 대한 액세스 권한을 부여할 수 있습니다. FTP는 상태 저장 프로토콜이므로 기존 HTTP/HTTPS 방화벽 또는 프록시 장치를 통해 FTP 트래픽을 라우팅하지 못할 수 있습니다. 이 경우 *SourceAddressPrefix*를 다른 값(예: FTP 클라이언트가 실행되는 개발자 또는 배포 컴퓨터의 IP 주소 범위)으로 설정해야 합니다.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPCtrl" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '21' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPDataRange" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '10001-10020' -Protocol TCP

(**참고:** 데이터 채널 포트 범위는 미리 보기 기간 동안 변경될 수 있습니다.)

Visual Studio를 통한 원격 디버깅을 사용하는 경우 다음과 같은 규칙에 따라 액세스 권한을 부여할 수 있습니다. 버전마다 다른 포트를 원격 디버깅에 사용하기 때문에 지원되는 각 버전의 Visual Studio에 대한 별도의 규칙이 있습니다. FTP 액세스와 마찬가지로 원격 디버깅 트래픽은 기존 WAF 또는 프록시 장치를 통해 제대로 흐르지 않을 수 있습니다. 대신 *SourceAddressPrefix*를 Visual Studio를 실행하는 개발자 컴퓨터의 IP 주소 범위로 설정할 수 있습니다.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2012" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4016' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2013" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4018' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2015" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4020' -Protocol TCP

## 서브넷에 네트워크 보안 그룹 할당 ##
네트워크 보안 그룹에는 모든 외부 트래픽에 대한 액세스를 거부하는 기본 보안 규칙이 있습니다. 위에 설명된 네트워크 보안 규칙과 인바운드 트래픽을 차단하는 기본 보안 규칙을 결합한 결과는 *허용* 작업과 연결된 원본 주소 범위의 트래픽만 앱 서비스 환경에서 실행되는 앱으로 보낼 수 있습니다.

네트워크 보안 그룹을 보안 규칙으로 채운 후에는 앱 서비스 환경이 포함된 서브넷에 할당해야 합니다. 할당 명령은 앱 서비스 환경이 있는 가상 네트워크의 이름과 앱 서비스 환경을 만든 서브넷의 이름을 둘 다 참조합니다.

아래 예제에서는 서브넷 및 가상 네트워크에 할당되는 네트워크 보안 그룹을 보여 줍니다.


    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

네트워크 보안 그룹 할당에 성공하면(할당은 장기 실행 작업이며 완료되는 데 몇 분 정도 걸릴 수 있음) *허용* 규칙과 일치하는 인바운드 트래픽만 앱 서비스 환경의 앱에 성공적으로 도달합니다.

완전성을 위해 다음 예제에는 서브넷에서 네트워크 보안 그룹을 제거하고 연결을 해제하는 방법을 보여 줍니다.


    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Remove-AzureNetworkSecurityGroupFromSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

## 명시적 IP-SSL에 대한 특별 고려 사항 ##
앱이 앱 서비스 환경의 기본 IP 주소 대신 명시적 IP 주소로 구성된 경우 HTTP 및 HTTPS 트래픽 모두 포트 80 및 443이 아닌 다른 포트 집합을 통해 서브넷으로 흐릅니다.

앱 서비스 환경의 초기 미리 보기 중에는 IP-SSL에서 사용하는 특정 포트를 확인할 수 없습니다. 그러나 이 정보가 포털, 명령줄 도구 및 REST API를 통해 노출되면 개발자는 이러한 포트를 통한 트래픽도 제어하도록 네트워크 보안 그룹을 구성할 수 있습니다.

## 시작

앱 서비스 환경을 시작하려면 [앱 서비스 환경 소개][IntroToAppServiceEnvironment]를 참조하세요.

앱 서비스 환경에서 백 엔드 리소스에 앱을 안전하게 연결하는 방법에 대한 자세한 내용은 [앱 서비스 환경에서 백 엔드 리소스에 안전하게 연결][SecurelyConnecttoBackend]을 참조하세요.

Azure 앱 서비스 플랫폼에 대한 자세한 내용은 [Azure 앱 서비스][AzureAppService]를 참조하세요.

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[HowToCreateAnAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[IntroToAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[SecurelyConnecttoBackend]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-securely-connecting-to-backend-resources/

<!-- IMAGES -->

<!---HONumber=August15_HO9-->