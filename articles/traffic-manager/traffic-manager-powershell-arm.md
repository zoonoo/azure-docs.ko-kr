<properties
   pageTitle="트래픽 관리자에 대한 Azure 리소스 관리자 지원 미리 보기 | Microsoft Azure"
   description="ARM(Azure 리소스 관리자)와 함께 트래픽 관리자용 powershell 사용(미리 보기)"
   services="traffic-manager"
   documentationCenter="na"
   authors="joaoma"
   manager="adinah"
   editor="tysonn" />
<tags
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/19/2015"
   ms.author="joaoma" />





# Azure 트래픽 관리자에 대한 Azure 리소스 관리자 지원 미리 보기
ARM(Azure 리소스 관리자)은 Azure 서비스에 대한 새로운 관리 프레임워크입니다. 이제 Azure 리소스 관리자 기반 API 및 도구를 사용하여 Azure 트래픽 관리자 프로파일을 관리할 수 있습니다. Azure 리소스 관리자에 대한 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리](../azure-preview-portal-using-resource-groups.md)를 참조하세요.

>[AZURE.NOTE]REST API, Azure PowerShell, Azure CLI 및 .NET SDK를 포함하여 트래픽 관리자에 대한 ARM 지원은 현재 미리 보기 상태입니다.



## 리소스 모델

Azure 트래픽 관리자는 트래픽 관리자 프로필을 호출하는 설정 모음을 사용하여 구성됩니다. 여기에는 DNS 설정, 트래픽 라우팅 설정, 끝점 모니터링 설정 및 트래픽이 라우팅되는 서비스 끝점 목록이 포함됩니다.

ARM에서 각각의 트래픽 관리자 프로필은 ‘Microsoft.Network’ 리소스 공급자를 통해 관리되는 ‘TrafficManagerProfiles’ 유형의 ARM 리소스에서 표시됩니다. REST API 수준에서 각 프로필의 URI는 다음과 같습니다.

	https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/trafficManagerProfiles/{profile-name}?api-version={api-version}

## Azure 트래픽 관리자 서비스 관리 API와의 비교

ARM을 사용하여 트래픽 관리자 프로필을 구성하면 아래 나열된 미리 보기 제한 사항의 예외와 함께 (비 ARM) 서비스 관리 API와 동일한 트래픽 관리자 기능 세트에 대한 액세스를 제공합니다.

하지만 기능이 동일하게 유지되는 동안 다음과 같이 일부 기술은 변경됩니다.

- 트래픽 관리자가 특정 DNS 요청에 응답 시 트래픽을 라우팅할 끝점을 선택하는 방법을 결정하는 ‘부하 분산 방법’은 ‘트래픽 라우팅 메서드’라는 이름으로 변경되었습니다.

- ‘라운드 로빈’ 트래픽 라우팅 메서드는 ‘가중’이라는 이름으로 변경되었습니다.

- ‘장애 조치’ 트래픽 라우팅 메서드는 ‘우선 순위’라는 이름으로 변경되었습니다.

## 미리 보기 제한 사항
트래픽 관리자에 대한 Azure 리소스 관리자 지원은 미리 보기 서비스이므로 현재 적은 수의 제한 사항이 있습니다.

- 트래픽 관리자 프로필은 기존(비 ARM) 서비스 관리 API를 사용하여 만었으며 도구 및 포털은 ARM을 통해 사용할 수 없고 그 반대도 마찬가지입니다. 비 ARM에서 ARM API로의 프로필 마이그레이션은 현재 지원되지 않습니다.

- 	REST API는 트래픽 관리자 프로필의 패치를 지원하지 않습니다. 프로필 속성을 업데이트하려면 프로필을 가져와 수정된 프로필을 배치해야 하니다.
- 	‘외부’ 끝점만 지원됩니다. 이는 Azure 기반 서비스와 함께 트래픽 관리자를 사용하는 데 사용될 수 있으며 해당 끝점을 수행하면 내부 끝점 요금으로 청구됩니다. (외부 끝점 사용 시 유일하게 영향을 받는 점은 끝점을 수동으로 사용하지 않도록 설정하거나 삭제해야 하는 대신 기본 Azure 서비스를 사용하지 않도록 설정하거나 삭제하는 경우 자동으로 외부 끝점이 사용하지 않도록 설정되거나 삭제된다는 것입니다.)
-	Azure 트래픽 관리자는 아직 Azure 포털에서 사용할 수 없으며 클래식 포털에서만 사용할 수 있습니다.

## Azure PowerShell 설정

이러한 지침은 아래 단계를 사용하여 구성해야 하는 Microsoft Azure PowerShell을 사용합니다.

PowerShell이 아닌 사용자의 경우에도 다른 인터페이스를 통해 동일한 작업을 실행할 수 있습니다.

### 1단계
Azure 다운로드 페이지에서 사용할 수 있는 최신 Azure PowerShell을 설치합니다.
### 2단계
ARM cmdlet을 사용하려면 PowerShell 모드를 전환합니다. 자세한 내용은 리소스 관리자에서 Windows PowerShell 사용을 참조하세요.

	PS C:\> Switch-AzureMode -Name AzureResourceManager
### 3단계
Azure 계정에 로그인

	PS C:\> Add-AzureAccount

자격 증명을 사용하여 인증하라는 메시지가 표시됩니다.

### 4단계
사용할 Azure 구독을 선택합니다.

	PS C:\> Select-AzureSubscription -SubscriptionName "MySubscription"

사용 가능한 구독 목록을 보려면 ‘Get-AzureSubscription’ cmdlet을 사용합니다.

### 5단계

 트래픽 관리자는 Microsoft.Network 리소스 공급자에 의해 관리됩니다. ARM을 통해 트래픽 관리자를 사용하려면 먼저 이 리소스 공급자를 사용하도록 Azure 구독을 등록해야 합니다. 이 작업은 각 구독에 대해 한 번만 수행하면 됩니다.

	PS C:\> Register-AzureProvider –ProviderNamespace Microsoft.Network

### 6단계
리소스 그룹을 만듭니다. 기존 리소스 그룹을 사용하는 경우에는 이 단계를 건너뛰세요.

	PS C:\> New-AzureResourceGroup -Name MyAzureResourceGroup -location "West US"

Azure 리소스 관리자를 사용하려면 모든 리소스 그룹이 위치를 지정해야 합니다. 이 위치는 해당 리소스 그룹에서 리소스의 기본 위치로 사용됩니다. 하지만 모든 트래픽 관리자 프로필 리소스는 영역별이 아니라 전역이므로 리소스 그룹의 위치 선택이 Azure 트래픽 관리자에 영향을 주지 않습니다.

## 트래픽 관리자 프로필 만들기

트래픽 관리자 프로필을 만들려면 New-AzureTrafficManagerProfile cmdlet을 사용합니다.

	PS C:\> $profile = New-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyAzureResourceGroup -TrafficRoutingMethod Performance -RelativeDnsName contoso -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"

매개 변수는 다음과 같습니다.

- 이름: 트래픽 관리자 프로필 리소스의 ARM 리소스 이름입니다. 동일한 리소스 그룹의 프로필 이름은 고유해야 합니다. 이 이름은 DNS 쿼리에 사용되는 DNS 이름과 구분됩니다.

-	ResourceGroupName: 프로필 리소스를 포함하는 ARM 리소스 그룹의 이름입니다.

-	TrafficRoutingMethod: 들어오는 DNS 쿼리에 대한 응답으로 반환되는 끝점을 결정하는 데 사용되는 트래픽을 라우팅 메서드를 지정합니다. 가능한 값은 '성능', '가중' 또는 '우선 순위'입니다.

-	RelativeDnsName:이 트래픽 관리자 프로필을 통해 제공되는 관련 DNS 이름을 지정합니다. 이 값은 프로필의 FQDN(정규화된 도메인 이름)을 형성하여 Azure 트래픽 관리자가 사용하는 DNS 도메인 이름과 결합됩니다. 예를 들어, ‘contoso’ 값은 ‘contoso.trafficmanager.net’의 정규화 된 이름이 있는 트래픽 관리자 프로필을 제공합니다.

-	TTL: 초 단위로 DNS TTL(Time-to-Live)을 지정합니다. 로컬 DNS 확인자 및 DNS 클라이언트에게 이 트래픽 관리자 프로필이 제공한 DNS 응답을 캐시하는 데 걸린 시간을 알려줍니다.

-	MonitorProtocol: 끝점 상태를 모니터링 하는 데 사용할 프로토콜을 지정합니다. 가능한 값은 ‘HTTP’ 및 ‘HTTPS’입니다.

-	MonitorPort: 끝점 상태를 모니터링 하는 데 사용할 TCP 포트를 지정합니다.

-	MonitorPath: 끝점 상태를 조사하는 데 사용되는 끝점 도메인 이름에 대한 상대 경로를 지정합니다.

cmdlet는 트래픽 관리자 프로필 Azure 트래픽 관리자에서 만들고 해당 프로필 개체를 반환합니다. 이 때, 프로필에는 끝점이 포함되지 않습니다. 트래픽 관리자 프로필에 끝점을 포함하는 자세한 방법은 [트래픽 관리자 프로필 업데이트](#update-a-traffic-manager-profile)를 참조하세요.

## 트래픽 관리자 프로필 가져오기

기존 트래픽 관리자 프로필 개체를 검색하려면 Get-AzureTrafficManagerProfle cmdlet을 사용합니다.

	PS C:\> $profile = Get-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyAzureResourceGroup

이 cmdlet은 트래픽 관리자 프로필 개체를 반환합니다.

## 트래픽 관리자 프로필 업데이트 [](#update-traffic-manager-profile)

트래픽 관리자 프로필을 수정할 때 예를 들어, 끝점을 추가 또는 제거하거나 프로필 설정을 수정하려면 3단계 과정을 따르세요.

1.	Get-AzureTrafficManagerProfile을 사용하는 프로필을 검색합니다(또는 New-AzureTrafficManagerProfile이 반환하는 프로필 사용).

2.	끝점을 추가하거나, 제거하거나, 끝점 매개 변수를 변경하거나, 프로필 매개 변수를 변경하여 프로필을 수정합니다. 이러한 변경은 오프라인 작업이므로 프로필을 나타내는 로컬 개체만 변경됩니다.

3.	Set-AzureTrafficManagerProfile cmdlet을 사용하여 변경 내용을 커밋합니다. 이렇게 하면 Azure 트래픽 관리자의 기존 프로필을 제공된 프로필과 바꿉니다.

아래 예시를 사용하여 더 자세히 설명됩니다.

### 프로필에 끝점 추가

‘Add-AzureTrafficManagerEndpointConfig’ cmdlet을 사용하여 트래픽 관리자 프로필에 끝점을 추가할 수 있습니다.

	PS C:\> $profile = Get-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyAzureResourceGroup
	PS C:\> Add-AzureTrafficManagerEndpointConfig –EndpointName site1 –TrafficManagerProfile $profile –Type ExternalEndpoints –Target site1.contoso.com –EndpointStatus Enabled –Weight 10 –Priority 1 –EndpointLocation “West US”
	PS C:\> Set-AzureTrafficManagerProfile –TrafficManagerProfile $profile

Add-AzureTrafficManagerEndpointConfig에 대한 매개 변수는 다음과 같습니다.

- EndpointName: 끝점의 이름입니다. 동일한 프로필의 끝점에는 고유한 이름이 있어야 합니다. 서비스 관리 작업 중에 끝점을 참조하는 데 사용되며 끝점의 DNS 이름이 아닙니다.

-	TrafficManagerProfile: 끝점에 추가되는 트래픽 관리자 프로필 개체입니다.

-	형식: 트래픽 관리자 끝점의 형식입니다. 현재 ARM API를 통해 ‘ExternalEndpoint’ 형식만 지원됩니다([미리 보기 제한 사항](#preview-limitations) 참조).

-	대상: 끝점의 정규화된 DNS 이름입니다. 트래픽 관리자는 이 끝점에 트래픽을 보내도록 DNS 응답에서 이 값을 반환합니다.

-	EndpointStatus: 끝점의 상태를 지정합니다. 끝점을 사용하도록 설정된 경우 끝점 상탱에 대해 조사되며 트래픽 라우팅 메서드에 포함됩니다. 가능한 값은 ‘사용’ 또는 ‘사용 안 함’입니다.

-	가중: 끝점에 할당된 가중치를 지정합니다. '가중' 트래픽 라우팅 메서드를 사용하도록 트래픽 관리자 프로필을 구성한 경우에만 사용됩니다. 가능한 값은 1~1000입니다.

-	우선 순위: ‘우선 순위’ 트래픽 라우팅 메서드를 사용하는 경우 이 끝점의 우선 순위를 지정합니다. 우선 순위는 범위 1~1000 사이여야 합니다. 값이 낮을수록 더 높은 우선 순위를 나타냅니다.

-	EndpointLocation: ‘성능’ 트래픽 라우팅 메서드와 함께 사용하기 위해 외부 끝점의 위치를 지정합니다. 가능한 위치 목록은 Get-AzureLocation을 참조하세요.

끝점 상태, 가중 및 우선 순위는 선택적 매개 변수입니다. 생략하는 경우 PowerShell이 전달하지 않으며 서버 쪽 기본값이 적용됩니다.

### 프로필에서 끝점을 제거합니다.

프로필에서 끝점을 제거하려면 제거할 끝점 이름을 지정하는 ‘Remove-AzureTrafficmanagerEndpointConfig’를 사용합니다.

	PS C:\> $profile = Get-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyAzureResourceGroup
	PS C:\> Remove-AzureTrafficManagerEndpointConfig –EndpointName site1 –TrafficManagerProfile $profile
	PS C:\> Set-AzureTrafficManagerProfile –TrafficManagerProfile $profile

끝점을 추가 또는 제거하는 작업 순서는 매개 변수 대신 파이프를 통해 프로필 개체를 전달하여 ‘파이프’될 수도 있습니다. 예:

	PS C:\> Get-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyAzureResourceGroup | Remove-AzureTrafficManagerEndpointConfig –EndpointName site1 | Set-AzureTrafficManagerProfile

### 프로필 또는 끝점 설정 변경

프로필 및 끝점 매개 변수 모두 오프라인으로 변경할 수 있으며 변경 내용은 Set-AzureTrafficManagerProfile을 사용하여 커밋됩니다. 유일한 예외는 RelativeDnsName 프로필은 프로필을 만든 후에는 변경할 수 없다는 점입니다(이 값을 변경하려면 해당 프로필을 삭제하고 다시 만들어야 함). 예를 들어 프로필 TTL 및 첫 번째 끝점의 상태를 변경하려면:

	PS C:\> $profile = Get-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyAzureResourceGroup
	PS C:\> $profile.Ttl = 300
	PS C:\> $profile.Endpoints[0].EndpointStatus = "Disabled"
	PS C:\> Set-AzureTrafficManagerProfile –TrafficManagerProfile $profile

### 트래픽 관리자 프로필 삭제
트래픽 관리자 프로필을 삭제하려면 프로필 이름 및 리소스 그룹 이름을 지정하는 Remove-AzureTrafficManagerProfile cmdlet을 사용합니다.

	PS C:\> Remove-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyAzureResourceGroup [-Force]

이 cmdlet은 확인 메시지를 표시합니다. 옵션 ‘-Force’ 스위치를 사용하여 이 메시지가 표시되지 않도록 할 수 있습니다. 삭제할 프로필은 프로필 개체를 사용하여 지정할 수도 있습니다.

	PS C:\> $profile = Get-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyAzureResourceGroup
	PS C:\> Remove-AzureTrafficManagerProfile –TrafficManagerProfile $profile [-Force]

이 순서는 파이프될 수도 있습니다.

	PS C:\> Get-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyAzureResourceGroup | Remove-AzureTrafficManagerProfile [-Force]


## 참고 항목

[트래픽 관리자란?](traffic-manager-overview.md)

[Azure cmdlet 시작](https://msdn.microsoft.com/library/jj554332.aspx)
 

<!---HONumber=Oct15_HO3-->