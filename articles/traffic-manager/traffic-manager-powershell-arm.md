<properties
   pageTitle="트래픽 관리자에 대한 Azure 리소스 관리자 지원 미리 보기 | Microsoft Azure"
   description="ARM(Azure 리소스 관리자)와 함께 트래픽 관리자용 powershell 사용(미리 보기)"
   services="traffic-manager"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/19/2015"
   ms.author="joaoma" />



# Azure 트래픽 관리자에 대한 Azure 리소스 관리자 지원 미리 보기
ARM(Azure 리소스 관리자)은 Azure 서비스에 대한 새로운 관리 프레임워크입니다. 이제 Azure 리소스 관리자 기반 API 및 도구를 사용하여 Azure 트래픽 관리자 프로파일을 관리할 수 있습니다. Azure 리소스 관리자에 대한 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리](../azure-preview-portal-using-resource-groups.md)를 참조하세요.

>[AZURE.NOTE]REST API, Azure PowerShell, Azure CLI 및 .NET SDK를 포함하여 트래픽 관리자에 대한 ARM 지원은 현재 미리 보기 상태입니다.



## 리소스 모델

Azure 트래픽 관리자는 트래픽 관리자 프로필을 호출하는 설정 모음을 사용하여 구성됩니다. 여기에는 DNS 설정, 트래픽 라우팅 설정, 끝점 모니터링 설정 및 트래픽이 라우팅되는 서비스 끝점 목록이 포함됩니다.

ARM에서 각각의 트래픽 관리자 프로필은 ‘Microsoft.Network’ 리소스 공급자를 통해 관리되는 ‘TrafficManagerProfiles’ 유형의 ARM 리소스에서 표시됩니다. REST API 수준에서 각 프로필의 URI는 다음과 같습니다.

	https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/trafficManagerProfiles/{profile-name}?api-version={api-version}

## Azure 트래픽 관리자 서비스 관리 API와의 비교

ARM을 사용하여 트래픽 관리자 프로필을 구성하면 아래 나열된 미리 보기 제한 사항의 예외와 함께 Azure 서비스 관리(ASM) API와 동일한 트래픽 관리자 기능 세트에 대한 액세스를 제공합니다.

하지만 기능이 동일하게 유지되는 동안 다음과 같이 일부 기술은 변경됩니다.

- 트래픽 관리자가 특정 DNS 요청에 응답 시 트래픽을 라우팅할 끝점을 선택하는 방법을 결정하는 ‘부하 분산 방법’은 ‘트래픽 라우팅 메서드’라는 이름으로 변경되었습니다.

- ‘라운드 로빈’ 트래픽 라우팅 메서드는 ‘가중’이라는 이름으로 변경되었습니다.

- ‘장애 조치’ 트래픽 라우팅 메서드는 ‘우선 순위’라는 이름으로 변경되었습니다.

## 미리 보기 제한 사항
트래픽 관리자에 대한 Azure 리소스 관리자 지원은 미리 보기 서비스이므로 현재 적은 수의 제한 사항이 있습니다.

- 트래픽 관리자 프로필은 기존(비 ARM) Azure 서비스 관리(ASM) API를 사용하여 만들었으며 도구 및 '클래식' 포털은 ARM을 통해 사용할 수 없고 그 반대도 마찬가지입니다. ASM에서 ARM API로 프로필 마이그레이션은 프로필 삭제 및 다시 만드는 방법 외에는 현재 지원되지 않습니다.

- '중첩된' 트래픽 관리자 끝점은 현재 ARM API에서 지원되지 않습니다.

- Azure 트래픽 관리자는 아직 Azure '미리 보기' 포털에서 사용할 수 없으며 '클래식' 포털에서만 사용할 수 있습니다.

## Azure PowerShell 설정

이러한 지침은 아래 단계를 사용하여 구성해야 하는 Microsoft Azure PowerShell을 사용합니다.

PowerShell이 아닌 사용자 또는 Windows가 아닌 사용자의 경우 Azure CLI를 통해 유사한 작업을 실행할 수 있습니다.

### 1단계
Azure 다운로드 페이지에서 사용할 수 있는 최신 Azure PowerShell을 설치합니다.

### 2단계
Azure 계정에 로그인

	PS C:\> Login-AzureRmAccopunt

자격 증명을 사용하여 인증하라는 메시지가 표시됩니다.

### 3단계
사용할 Azure 구독을 선택합니다.

	PS C:\> Select-AzureRmContext -SubscriptionName "MySubscription"

사용 가능한 구독 목록을 보려면 ‘Get-AzureRmSubscription’ cmdlet을 사용합니다.

### 4단계

트래픽 관리자는 Microsoft.Network 리소스 공급자에 의해 관리됩니다. ARM을 통해 트래픽 관리자를 사용하려면 먼저 이 리소스 공급자를 사용하도록 Azure 구독을 등록해야 합니다. 이 작업은 각 구독에 대해 한 번만 수행하면 됩니다.

	PS C:\> Register-AzureRmResourceProvider –ProviderNamespace Microsoft.Network

### 5단계
리소스 그룹을 만듭니다. 기존 리소스 그룹을 사용하는 경우에는 이 단계를 건너뛰세요.

	PS C:\> New-AzureRmResourceGroup -Name MyAzureResourceGroup -Location "West US"

Azure 리소스 관리자를 사용하려면 모든 리소스 그룹이 위치를 지정해야 합니다. 이 위치는 해당 리소스 그룹에서 리소스의 기본 위치로 사용됩니다. 하지만 모든 트래픽 관리자 프로필 리소스는 영역별이 아니라 전역이므로 리소스 그룹의 위치 선택이 Azure 트래픽 관리자에 영향을 주지 않습니다.

## 트래픽 관리자 프로필 만들기

트래픽 관리자 프로필을 만들려면 New-AzureRmTrafficManagerProfile cmdlet을 사용합니다.

	PS C:\> $profile = New-AzureRmTrafficManagerProfile –Name MyProfile -ResourceGroupName MyAzureResourceGroup -TrafficRoutingMethod Performance -RelativeDnsName contoso -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"

매개 변수는 다음과 같습니다.

- 이름: 트래픽 관리자 프로필 리소스의 ARM 리소스 이름입니다. 동일한 리소스 그룹의 프로필 이름은 고유해야 합니다. 이 이름은 DNS 쿼리에 사용되는 DNS 이름과 구분됩니다.

- ResourceGroupName: 프로필 리소스를 포함하는 ARM 리소스 그룹의 이름입니다.

- TrafficRoutingMethod: 들어오는 DNS 쿼리에 대한 응답으로 반환되는 끝점을 결정하는 데 사용되는 트래픽을 라우팅 메서드를 지정합니다. 가능한 값은 '성능', '가중' 또는 '우선 순위'입니다.

- RelativeDnsName:이 트래픽 관리자 프로필을 통해 제공되는 관련 DNS 이름을 지정합니다. 이 값은 프로필의 FQDN(정규화된 도메인 이름)을 형성하여 Azure 트래픽 관리자가 사용하는 DNS 도메인 이름과 결합됩니다. 예를 들어, ‘contoso’ 값은 ‘contoso.trafficmanager.net’의 정규화된 이름이 있는 트래픽 관리자 프로필을 제공합니다.

- TTL: 초 단위로 DNS TTL(Time-to-Live)을 지정합니다. 로컬 DNS 확인자 및 DNS 클라이언트에게 이 트래픽 관리자 프로필이 제공한 DNS 응답을 캐시하는 데 걸린 시간을 알려줍니다.

- MonitorProtocol: 끝점 상태를 모니터링 하는 데 사용할 프로토콜을 지정합니다. 가능한 값은 ‘HTTP’ 및 ‘HTTPS’입니다.

- MonitorPort: 끝점 상태를 모니터링 하는 데 사용할 TCP 포트를 지정합니다.

- MonitorPath: 끝점 상태를 조사하는 데 사용되는 끝점 도메인 이름에 대한 상대 경로를 지정합니다.

cmdlet는 트래픽 관리자 프로필 Azure 트래픽 관리자에서 만들고 해당 프로필 개체를 반환합니다. 이 때, 프로필에는 끝점이 포함되지 않습니다. 트래픽 관리자 프로필에 끝점을 포함하는 자세한 방법은 [트래픽 관리자 끝점 추가](#adding-traffic-manager-endpoints)를 참조하세요.

## 트래픽 관리자 프로필 가져오기

기존 트래픽 관리자 프로필 개체를 검색하려면 Get-AzureRmTrafficManagerProfle cmdlet을 사용합니다.

	PS C:\> $profile = Get-AzureRmTrafficManagerProfile –Name MyProfile -ResourceGroupName MyAzureResourceGroup

이 cmdlet은 트래픽 관리자 프로필 개체를 반환합니다.

## 트래픽 관리자 프로필 업데이트 [](#update-traffic-manager-profile)

트래픽 관리자 프로필을 수정할 때 예를 들어, 끝점을 추가 또는 제거하거나 프로필 설정을 수정하려면 3단계 과정을 따르세요.

1.	Get-AzureRmTrafficManagerProfile을 사용하는 프로필을 검색합니다(또는 New-AzureRmTrafficManagerProfile이 반환하는 프로필 사용).

2.	끝점을 추가하거나, 제거하거나, 끝점 매개 변수를 변경하거나, 프로필 매개 변수를 변경하여 프로필을 수정합니다. 이러한 변경은 오프라인 작업이므로 프로필을 나타내는 로컬 개체만 변경됩니다.

3.	Set-AzureRmTrafficManagerProfile cmdlet을 사용하여 변경 내용을 커밋합니다. 이렇게 하면 Azure 트래픽 관리자의 기존 프로필을 제공된 프로필과 바꿉니다.

모든 프로필 속성을 변경할 수 있습니다. 유일한 예외는 RelativeDnsName 프로필은 프로필을 만든 후에는 변경할 수 없다는 점입니다(이 값을 변경하려면 해당 프로필을 삭제하고 다시 만들어야 함).

예를 들어 프로필 TTL을 변경하려면:

	PS C:\> $profile = Get-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyAzureResourceGroup
	PS C:\> $profile.Ttl = 300
	PS C:\> Set-AzureTrafficManagerProfile –TrafficManagerProfile $profile

## 트래픽 관리자 끝점 추가 [](#adding-traffic-manager-endpoints)
트래픽 관리자 끝점에는 세 가지 종류가 있습니다: 1. Azure 끝점: Azure에서 호스팅되는 서비스를 나타냅니다. 2. 외부 끝점: Azure 외부에서 호스팅되는 서비스를 나타냅니다. 3. 중첩된 끝점: 좀 더 복잡한 응용 프로그램에 대한 고급 트래픽 라우팅 구성을 사용하도록 트래픽 관리자 프로필의 중첩된 계층 구조를 생성하는 데 사용됩니다. 아직 ARM API를 통해 지원되지 않습니다.

세 가지 경우 모두 끝점은 두 가지 방법으로 추가될 수 있습니다: 1. 3단계 프로세스 사용은 [트래픽 관리자 프로필 업데이트](#update-traffic-manager-profile)에서 설명된 것과 유사합니다: Get-AzureRmTrafficManagerProfile을 사용하여 프로필 개체 가져오기; 끝점을 추가하도록 오프라인으로 업데이트, Add-AzureRmTrafficManagerEndpointConfig 사용; Set-AzureRmTrafficManagerProfile를 사용하여 Azure 트래픽 관리자에 변경 내용 업로드. 이 방법의 장점은 끝점 변경 내용의 횟수를 단일 업데이트에서 지정할 수 있다는 점입니다. 2. New-AzureRmTrafficManagerEndpoint cmdlet 사용. 끝점을 단일 작업의 기존 트래픽 관리자 프로필에 추가합니다.

### Azure 끝점 추가
Azure 끝점은 Azure에서 호스팅되는 다른 서비스를 나타냅니다. 현재 다음 3가지 유형의 Azure 끝점이 지원됩니다. 1. Azure 웹앱 2. '클래식' 클라우드 서비스(PaaS 서비스 또는 IaaS 가상 컴퓨터를 포함할 수 있음) 3. ARM Microsoft.Network/publicIpAddress 리소스(부하 분산 장치 또는 가상 컴퓨터 NIC에 연결할 수 있음). publicIpAddress에는 트래픽 관리자에서 사용되도록 지정된 DNS 이름이 있어야 합니다.

각각의 경우에서: - 서비스는 Add-AzureRmTrafficManagerEndpointConfig 또는 New-AzureRmTrafficManagerEndpoint의 'targetResourceId' 매개 변수를 사용하여 지정됩니다. - 'Target' 및 'EndpointLocation'은 지정하지 않아야 하며 위의 'Weight'를 선택적으로 지정에서 지정된 TargetResourceId에서 암시된 것입니다. 가중치는 '가중' 트래픽 라우팅 메서드를 사용하도록 프로필을 구성한 경우에만 사용됩니다. 그렇지 않으면 무시됩니다. 지정한 경우 1...1000의 범위에 있어야 합니다. 기본값은 '1'입니다. - '우선 순위' 지정은 선택 사항입니다. 우선 순위는 '우선 순위' 트래픽 라우팅 메서드를 사용하도록 프로필을 구성한 경우에만 사용됩니다. 그렇지 않으면 무시됩니다. 유효한 값은 1~1000입니다(낮은 값은 더 높은 우선 순위임). 한 끝점에 대해 지정한 경우 모든 끝점에 대해 지정되어야 합니다. 생략한 경우 1, 2, 3 등으로 시작하는 기본값이 끝점이 제공된 순서대로 적용됩니다.

#### 예제 1: AzureRmTrafficManagerEndpointConfig를 사용하여 웹앱 끝점 추가
이 예제에서는 새 트래픽 관리자 프로필을 만들고 Add-AzureRmTrafficManagerEndpointConfig cmdlet을 사용하여 두 개의 웹앱 끝점을 추가한 다음 Set-AzureRmTrafficManagerProfile을 사용하여 업데이트된 프로필을 Azure 트래픽 관리자에 커밋합니다.

	PS C:\> $profile = New-AzureRmTrafficManagerProfile –Name myprofile -ResourceGroupName myrg -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
	PS C:\> $webapp1 = Get-AzureRMWebApp -Name webapp1
	PS C:\> Add-AzureRmTrafficManagerEndpointConfig –EndpointName webapp1ep –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp1.Id –EndpointStatus Enabled
	PS C:\> $webapp2 = Get-AzureRMWebApp -Name webapp2
	PS C:\> Add-AzureRmTrafficManagerEndpointConfig –EndpointName webapp2ep –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp2.Id –EndpointStatus Enabled
	PS C:\> Set-AzureRmTrafficManagerProfile –TrafficManagerProfile $profile  

#### 예제 2: New-AzureRmTrafficManagerEndpoint를 사용하여 '클래식' 클라우드 서비스 끝점 추가
이 예제에서는 '클래식' 클라우드 서비스 끝점이 트래픽 관리자 프로필에 추가됩니다. 이 경우 프로필 개체를 전달하는 대신 프로필 이름 및 리소스 그룹 이름을 사용하여 프로필을 지정하도록 선택했습니다(두 가지 방법 모두 지원).

	PS C:\> $cloudService = Get-AzureRmResource -ResourceName MyCloudService -ResourceType "Microsoft.ClassicCompute/domainNames" -ResourceGroupName MyCloudService
	PS C:\> New-AzureRmTrafficManagerEndpoint –Name MyCloudServiceEndpoint –ProfileName MyProfile -ResourceGroupName MyRG –Type AzureEndpoints -TargetResourceId $cloudService.Id –EndpointStatus Enabled

#### 예제 3: New-AzureRmTrafficManagerEndpoint를 사용하여 publicIpAddress 끝점 추가
이 예제에서는 ARM 공용 IP 주소 리소스가 트래픽 관리자 프로필에 추가됩니다. 공용 IP 주소는 구성된 DNS 이름이 있어야 하고 VM의 NIC 또는 부하 분산 장치에 바인딩될 수 있습니다.

	PS C:\> $ip = Get-AzureRmPublicIpAddress -Name MyPublicIP -ResourceGroupName MyResourceGroup
	PS C:\> New-AzureRmTrafficManagerEndpoint –Name MyIpEndpoint –ProfileName MyProfile -ResourceGroupName MyRG –Type AzureEndpoints -TargetResourceId $ip.Id –EndpointStatus Enabled

### 외부 끝점 추가
트래픽 관리자는 외부 끝점을 사용하여 Azure 외부에서 호스팅되는 서비스에 트래픽을 보냅니다. Azure 끝점과 마찬가지로 외부 끝점은 Set-AzureRmTrafficManagerProfile이 뒤에 오는 Add-AzureRmTrafficManagerEndpointConfig 또는 New-AzureRMTrafficManagerEndpoint를 사용하여 추가할 수 있습니다.

외부 끝점을 지정하는 경우: - 끝점 도메인 이름은 '대상' 매개 변수를 사용하여 지정되어야 합니다. - '성능' 트래픽 라우팅 메서드를 사용하는 경우 'EndpointLocation'이 필수이고 그렇지 않으면 선택 사항입니다. 값은 [유효한 Azure 지역 이름](http://azure.microsoft.com/regions/)이어야 합니다. - '가중치' 및 '우선 순위'는 Azure 끝점의 경우와 마찬가지로 선택 사항입니다.

#### 예제 1: Add-AzureRmTrafficManagerEndpointConfig 및 Set-AzureRmTrafficManagerProfile을 사용하여 외부 끝점 추가
이 예제에서는 새 트래픽 관리자 프로필을 만들고 두 개의 외부 끝점을 추가하고 변경 내용을 커밋합니다.

	PS C:\> $profile = New-AzureRmTrafficManagerProfile –Name myprofile -ResourceGroupName myrg -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
	PS C:\> Add-AzureRmTrafficManagerEndpointConfig –EndpointName eu-endpoint –TrafficManagerProfile $profile –Type ExternalEndpoints -Target app-eu.contoso.com –EndpointStatus Enabled
	PS C:\> Add-AzureRmTrafficManagerEndpointConfig –EndpointName us-endpoint –TrafficManagerProfile $profile –Type ExternalEndpoints -Target app-us.contoso.com –EndpointStatus Enabled
	PS C:\> Set-AzureRmTrafficManagerProfile –TrafficManagerProfile $profile  

#### 예제 2: New-AzureRmTrafficManagerEndpoint를 사용하여 외부 끝점 추가
이 예제에서는 프로필 이름 및 리소스 그룹 이름을 사용하여 지정된 외부 끝점을 기존 프로필에 추가합니다.

	PS C:\> New-AzureRmTrafficManagerEndpoint –Name eu-endpoint –ProfileName MyProfile -ResourceGroupName MyRG –Type ExternalEndpoints -Target app-eu.contoso.com –EndpointStatus Enabled

## 트래픽 관리자 끝점 업데이트
기존 트래픽 관리자 끝점을 업데이트하는 방법은 두 가지입니다. 1. Get-AzureRmTrafficManagerProfile을 사용하여 트래픽 관리자 프로필을 가져오고 프로필 내의 끝점 속성을 업데이트하고 Set-AzureRmTrafficManagerProfile을 사용하여 변경 내용을 커밋합니다. 이 메서드는 한 번에 둘 이상의 끝점을 업데이트할 수 있다는 장점이 있습니다. 2. Get-AzureRmTrafficManagerEndpoint를 사용하여 트래픽 관리자 끝점을 가져오고 끝점 속성을 업데이트하고 Set-AzureRmTrafficManagerEndpoint를 사용하여 변경 내용을 커밋합니다. 이 메서드는 프로필에서 끝점 배열로 인덱싱하지 않아도 되므로 훨씬 간단합니다.

#### 예제 1: Get-AzureRmTrafficManagerProfile 및 Set-AzureRmTrafficManagerProfile을 사용하여 끝점 업데이트
이 예제에서는 기존 프로필 내에서 두 개의 끝점에 대한 우선 순위를 수정합니다.

	PS C:\> $profile = Get-AzureRmTrafficManagerProfile –Name myprofile -ResourceGroupName myrg
	PS C:\> $profile.Endpoints[0].Priority = 2
	PS C:\> $profile.Endpoints[1].Priority = 1
	PS C:\> Set-AzureRmTrafficManagerProfile –TrafficManagerProfile $profile

#### 예제 2: Get-AzureRmTrafficManagerEndpoint 및 Set-AzureRmTrafficManagerEndpoint을 사용하여 끝점 업데이트
이 예제에서는 기존 프로필에 있는 단일 끝점의 가중치를 수정합니다.

	PS C:\> $endpoint = Get-AzureRmTrafficManagerEndpoint -Name myendpoint -ProfileName myprofile -ResourceGroupName myrg -Type ExternalEndpoints
	PS C:\> $endpoint.Weight = 20
	PS C:\> Set-AzureRmTrafficManagerEndpoint -TrafficManagerEndpoint $endpoint

## 끝점 및 프로필 활성화 및 비활성화
트래픽 관리자는 전체 프로필의 활성화 및 비활성화를 허용할 뿐 아니라 개별 끝점의 활성화 및 비활성화를 허용합니다. 끝점 또는 프로필 리소스 가져오기/업데이트/설정으로 변경할 수 있습니다. 이러한 일반적인 작업을 간소화하기 위해 전용 cmdlet을 통해 지원됩니다.

#### 예제 1: 트래픽 관리자 프로필 활성화 및 비활성화
트래픽 관리자 프로필을 활성화하려면 Enable-AzureRmTrafficManagerProfile을 사용합니다. 프로필 개체(파이프라인을 통해 또는 '-TrafficManagerProfile' 매개 변수를 사용하여 전달된)를 사용하거나 이 예제와 같이 프로필 이름 및 리소스 그룹 이름을 직접 지정하여 프로필을 지정할 수 있습니다.

	PS C:\> Enable-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup

마찬가지로 트래픽 관리자 프로필을 비활성화하려면:

	PS C:\> Disable-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup

Disable-AzureRmTrafficManagerProfile cmdlet은 확인을 위해 메시지를 표시하며 이 프롬프트는 '-Force' 매개 변수를 사용하여 표시하지 않을 수 있습니다.

#### 예제 2: 트래픽 관리자 끝점 활성화 및 비활성화
트래픽 관리자 끝점을 활성화하려면 Enable-AzureRmTrafficManagerEndpoint를 사용합니다. TrafficManagerEndpoint 개체(파이프라인을 통해 또는 '-TrafficManagerEndpoint' 매개 변수를 사용하여 전달된)를 사용하거나 끝점 이름, 끝점 형식, 프로필 이름 및 리소스 그룹 이름을 사용하여 끝점을 지정할 수 있습니다.

	PS C:\> Enable-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyResourceGroup

마찬가지로 트래픽 관리자 프로필을 비활성화하려면:

 	PS C:\> Disable-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyResourceGroup -Force

Disable-AzureRmTrafficManagerProfile과 마찬가지로 Disable-AzureRmTrafficManagerEndpoint cmdlet은 '-Force' 매개 변수를 사용하여 표시하지 않을 수 있는 확인 프롬프트를 포함합니다.

## 트래픽 관리자 끝점 삭제
트래픽 관리자 끝점을 삭제하는 한 가지 방법은 프로필 개체(Get-AzureRmTrafficManagerProfile 사용)를 검색하고 로컬 프로필 개체 내에서 끝점 목록을 업데이트하고 변경 내용을 커밋하는 것입니다(Set-AzureRmTrafficManagerProfile 사용). 이 메서드를 통해 여러 끝점 변경 내용을 함께 커밋할 수 있습니다.

개별 끝점을 제거하는 다른 방법은 Remove-AzureRmTrafficManagerEndpoint cmdlet을 사용하는 것입니다.

	PS C:\> Remove-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyResourceGroup
	
이 cmdlet은 '-Force' 매개 변수가 프롬프트를 표시하지 않도록 사용되지 않는 한 확인을 위한 메시지를 표시합니다.

## 트래픽 관리자 프로필 삭제
트래픽 관리자 프로필을 삭제하려면 프로필 이름 및 리소스 그룹 이름을 지정하는 Remove-AzureRmTrafficManagerProfile cmdlet을 사용합니다.

	PS C:\> Remove-AzureRmTrafficManagerProfile –Name MyProfile -ResourceGroupName MyAzureResourceGroup [-Force]

이 cmdlet은 확인 메시지를 표시합니다. 옵션 ‘-Force’ 스위치를 사용하여 이 메시지가 표시되지 않도록 할 수 있습니다. 삭제할 프로필은 프로필 개체를 사용하여 지정할 수도 있습니다.

	PS C:\> $profile = Get-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyAzureResourceGroup
	PS C:\> Remove-AzureTrafficManagerProfile –TrafficManagerProfile $profile [-Force]

이 순서는 파이프될 수도 있습니다.

	PS C:\> Get-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyAzureResourceGroup | Remove-AzureTrafficManagerProfile [-Force]

## 다음 단계

[트래픽 관리자 모니터링](traffic-manager-monitoring.md)

[트래픽 관리자 성능 고려 사항](traffic-manager-performance-considerations.md)
 

<!---HONumber=AcomDC_1125_2015-->