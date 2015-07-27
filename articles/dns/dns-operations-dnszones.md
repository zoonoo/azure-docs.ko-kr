<properties 
   pageTitle="DNS 영역에 대한 작업 | Microsoft Azure" 
   description="Azure Powershell cmdlet을 사용하여 DNS 영역을 관리할 수 있습니다. Azure DNS에서 DNS 영역을 업데이트, 삭제 및 만드는 방법입니다." 
   services="dns" 
   documentationCenter="na" 
   authors="joaoma" 
   manager="Adinah" 
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="05/01/2015"
   ms.author="joaoma"/>

# DNS 영역을 관리하는 방법

이 가이드에서는 DNS 영역을 관리하는 방법을 보여 줍니다. DNS 영역을 관리하기 위해 수행하는 작업 시퀀스를 이해하는 데 도움이 됩니다.

## 새 DNS 영역 만들기

도메인을 호스트할 새 DNS 영역을 만들려면 New-AzureDnsZone cmdlet을 사용합니다.

		PS C:> $zone = New-AzureDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup [–Tag $tags] 

이 작업은 Azure DNS에 새 DNS 영역을 만들고 영역에 해당하는 로컬 개체를 반환합니다. 필요한 경우 Azure 리소스 관리자 태그 배열을 지정할 수도 있습니다. 자세한 내용은 [Etag 및 태그](../dns-getstarted-create-dnszone#Etags-and-tags)를 참조하세요.

영역 이름은 리소스 그룹 내에서 고유해야 하며, 영역이 존재해서는 안 됩니다. 그렇지 않으면 작업이 실패합니다.

서로 다른 리소스 그룹이나 Azure 구독에서는 동일한 영역 이름을 다시 사용할 수 있습니다. 여러 영역이 동일한 이름을 공유하는 경우 각 인스턴스에 다른 이름 서버 주소가 할당되며, 하나의 인스턴스만 부모 도메인에서 위임할 수 있습니다. 자세한 내용은 [Azure DNS에 도메인 위임](../dns-domain-delegation)을 참조하세요.

## DNS 영역 가져오기

DNS 영역을 가져오려면 Get-AzureDnsZone cmdlet을 사용합니다.

		PS C:> $zone = Get-AzureDnsZone -Name contoso.com –ResourceGroupName MyAzureResourceGroup

이 작업은 Azure DNS의 기존 영역에 해당하는 DNS 영역 개체를 반환합니다. 이 개체는 영역에 대한 데이터(예: 레코드 집합 수)를 포함하지만 레코드 집합 자체는 포함하지 않습니다.

## DNS 영역 나열
Get-AzureDnsZone에서 영역 이름을 생략하면 리소스 그룹의 모든 영역을 열거할 수 있습니다.

	PS C:> $zoneList = Get-AzureDnsZone -ResourceGroupName MyAzureResourceGroup
이 작업은 영역 개체의 배열을 반환합니다.

## DNS 영역 업데이트
Set-AzureDnsZone을 사용하여 DNS 영역 리소스를 변경할 수 있습니다. 이 작업은 영역 내의 DNS 레코드 집합을 업데이트하지 않습니다([DNS 레코드를 관리하는 방법](../dns-operations-recordsets) 참조). 영역 리소스 자체의 속성을 업데이트하는 데만 사용됩니다. 이 작업은 현재 영역 리소스에 대한 Azure 리소스 관리자 '태그'로 제한됩니다. 자세한 내용은 [Etag 및 태그](../dns-getstarted-create-dnszone#Etags-and-tags)를 참조하세요.

다음 두 가지 방법 중 하나를 사용하여 DNS 영역을 업데이트합니다.

### 옵션 1
 
영역 이름 및 리소스 그룹을 사용하여 영역을 지정합니다.

	PS C:> Set-AzureDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup [-Tag $tags]

### 옵션 2
Get-AzureDnsZone에서 $zone 개체를 사용하여 영역을 지정합니다.

	PS C:> $zone = Get-AzureDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
	PS C:> <..modify $zone.Tags here...>
	PS C:> Set-AzureDnsZone -Zone $zone [-Overwrite]

$zone 개체와 함께 Set-AzureDnsZone을 사용하는 경우 동시 변경 내용을 덮어쓰지 않도록 'Etag' 검사가 사용됩니다. 선택적 '-Overwrite' 스위치를 사용하여 이러한 검사를 무시할 수 있습니다. 자세한 내용은 [Etag 및 태그](../dns-getstarted-create-dnszone#Etags-and-tags)를 참조하세요.

## DNS 영역 삭제

Remove-AzureDnsZone cmdlet을 사용하여 DNS 영역을 삭제할 수 있습니다.
 
Azure DNS에서 DNS 영역을 삭제하기 전에 영역을 만들 때 자동으로 생성된, 영역 루트에 있는 NS 및 SOA 레코드를 제외한 모든 레코드 집합을 삭제해야 합니다.

다음 두 가지 방법 중 하나를 사용하여 DNS 영역을 제거합니다.

### 옵션 1

영역 이름 및 리소스 그룹 이름을 사용하여 영역을 지정합니다.

	PS C:> Remove-AzureDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup [-Force] 

이 작업에는 DNS 영역을 제거할 것인지 확인하는 메시지를 표시하지 않는 선택적 '-Force' 스위치가 있습니다.
### 옵션 2

Get-AzureDnsZone에서 $zone 개체를 사용하여 영역을 지정합니다.

	PS C:> $zone = Get-AzureDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
	PS C:> Remove-AzureDnsZone -Zone $zone [-Force] [-Overwrite]

'-Force' 스위치는 옵션 1과 동일합니다.

'Set-AzureDnsZone'과 마찬가지로, $zone 개체를 사용하여 영역을 지정하면 동시 변경 내용을 삭제하지 않도록 'Etag' 검사가 사용됩니다. <BR> 선택적 '-Overwrite' 플래그를 사용하면 이러한 검사가 무시됩니다. 자세한 내용은 [Etag 및 태그](../dns-getstarted-create-dnszone#Etags-and-tags)를 참조하세요.

영역 개체를 매개 변수로 전달하는 대신 파이프할 수도 있습니다.

	PS C:> Get-AzureDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureDnsZone [-Force] [-Overwrite]

## 다음 단계


[DNS 레코드 관리](../dns-operations-recordsets)

[.NET SDK로 작업 자동화](../dns-sdk)

<!---HONumber=July15_HO3-->