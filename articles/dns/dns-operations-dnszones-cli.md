<properties 
   pageTitle="DNS 영역에 대한 작업 | Microsoft Azure"
	description="Azure Powershell cmdlet 또는 CLI를 사용하여 DNS 영역을 관리할 수 있습니다. Azure DNS에서 DNS 영역을 업데이트, 삭제 및 만드는 방법입니다."
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
	ms.date="09/02/2015"
	ms.author="joaoma"/>

# DNS 영역을 관리하는 방법

> [AZURE.SELECTOR]
- [Azure CLI](dns-operations-dnszones-cli.md)
- [Azure Powershell](dns-operations-dnszones.md)

이 가이드에서는 DNS 영역을 관리하는 방법을 보여 줍니다. DNS 영역을 관리하기 위해 수행하는 작업 시퀀스를 이해하는 데 도움이 됩니다.

## 새 DNS 영역 만들기

도메인을 호스트할 새 DNS 영역을 만들려면 `azure network dns zone create`를 사용합니다.

		Azure network dns zone create -n contoso.com -g myresourcegroup -t "project=demo";"env=test"

이 작업으로 Azure DNS에 새 DNS 영역이 만들어집니다. 필요한 경우 Azure 리소스 관리자 태그 배열을 지정할 수도 있습니다. 자세한 내용은 [Etag 및 태그](dns-getstarted-create-dnszone.md#Etags-and-tags)를 참조하세요.

영역 이름은 리소스 그룹 내에서 고유해야 하며, 영역이 존재해서는 안 됩니다. 그렇지 않으면 작업이 실패합니다.

서로 다른 리소스 그룹이나 Azure 구독에서는 동일한 영역 이름을 다시 사용할 수 있습니다. 여러 영역이 동일한 이름을 공유하는 경우 각 인스턴스에 다른 이름 서버 주소가 할당되며, 하나의 인스턴스만 부모 도메인에서 위임할 수 있습니다. 자세한 내용은 [Azure DNS에 도메인 위임](dns-domain-delegation.md)을 참조하세요.

## DNS 영역 가져오기

DNS 영역을 가져오려면 `azure network dns zone show`를 사용합니다.

	azure network dns zone show myresourcegroup contoso.com

이 작업은 해당 ID, 레코드 집합 및 태그 번호와 함께 DNS 영역을 반환합니다.


## DNS 영역 나열

리소스 그룹 내 DNS 영역을 가져오려면 `azure network dns zone list`를 사용합니다.

	azure network dns zone list myresourcegroup


## DNS 영역 업데이트

`azure network dns zone set`를 사용하여 DNS 영역 리소스를 변경할 수 있습니다. 이 작업은 영역 내의 DNS 레코드 집합을 업데이트하지 않습니다([DNS 레코드를 관리하는 방법](dns-operations-recordsets.md) 참조). 영역 리소스 자체의 속성을 업데이트하는 데만 사용됩니다. 이 작업은 현재 영역 리소스에 대한 Azure 리소스 관리자 '태그'로 제한됩니다. 자세한 내용은 [Etag 및 태그](dns-getstarted-create-dnszone.md#Etags-and-tags)를 참조하세요.

	azure network dns zone set myresourcegroup contoso.com -t prod=value2

## DNS 영역 삭제

`azure network dns zone delete`를 사용하여 DNS 영역을 삭제할 수 있습니다.
 
Azure DNS에서 DNS 영역을 삭제하기 전에 영역을 만들 때 자동으로 생성된, 영역 루트에 있는 NS 및 SOA 레코드를 제외한 모든 레코드 집합을 삭제해야 합니다.

	azure network dns zone delete myresourcegroup contoso.com 

이 작업에는 DNS 영역을 제거할 것인지 확인하는 메시지를 표시하지 않는 선택적 '-q' 스위치가 있습니다.


## 다음 단계


[DNS 레코드 관리](dns-operations-recordsets-cli.md)

[.NET SDK로 작업 자동화](dns-sdk.md)

<!---HONumber=September15_HO1-->