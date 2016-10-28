<properties 
   pageTitle="CLI를 사용하여 DNS 영역 관리 | Microsoft Azure" 
   description="Azure CLI를 사용하여 DNS 영역을 관리할 수 있습니다. Azure DNS에서 DNS 영역을 업데이트, 삭제 및 만드는 방법입니다." 
   services="dns" 
   documentationCenter="na" 
   authors="cherylmc" 
   manager="carmonm" 
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="08/16/2016"
   ms.author="cherylmc"/>

# CLI를 사용하여 DNS 영역을 관리하는 방법

> [AZURE.SELECTOR]
- [Azure CLI](dns-operations-dnszones-cli.md)
- [PowerShell](dns-operations-dnszones.md)


이 가이드에서는 플랫폼 간 Azure CLI를 사용하여 DNS 영역 리소스를 관리하는 방법을 보여 줍니다.

이 지침은 Microsoft Azure CLI를 사용합니다. Azure DNS 명령을 사용하려면 최신 Azure CLI(0.9.8 이상)로 업데이트해야 합니다. `azure -v`를 입력하여 현재 컴퓨터에 설치되어 있는 Azure CLI 버전을 확인합니다. Windows, Linux 또는 MAC용 Azure CLI를 설치할 수 있습니다. 자세한 내용은 [Azure CLI 설치](../xplat-cli-install.md)를 참조하세요.

Azure DNS는 Azure 리소스 관리자 전용 서비스입니다. 여기에는 ASM API가 없습니다. Azure CLI가 리소스 관리자 모드를 사용하도록 구성되었는지 확인해야 합니다. `azure config mode arm` 명령을 사용하여 이 작업을 수행할 수 있습니다.<BR> "*오류: 'dns'가 azure 명령이 아닙니다*"라는 메시지가 표시되면 Azure CLI를 리소스 관리자 모드가 아닌 ASM 모드에서 사용하고 있기 때문입니다.

## 새 DNS 영역 만들기

도메인을 호스트할 DNS 영역을 새로 만들려면 [CLI를 사용하여 Azure DNS 영역 만들기](dns-getstarted-create-dnszone-cli.md)를 참조하세요.

## DNS 영역 가져오기

DNS 영역을 가져오려면 `azure network dns zone show`를 사용합니다.

	azure network dns zone show myresourcegroup contoso.com

이 작업은 해당 ID, 레코드 집합 및 태그 번호와 함께 DNS 영역을 반환합니다.


## DNS 영역 나열

리소스 그룹 내 DNS 영역을 가져오려면 `azure network dns zone list`를 사용합니다.

	azure network dns zone list myresourcegroup

## DNS 영역 업데이트

`azure network dns zone set`를 사용하여 DNS 영역 리소스를 변경할 수 있습니다. 이 작업은 영역 내의 DNS 레코드 집합을 업데이트하지 않습니다([DNS 레코드를 관리하는 방법](dns-operations-recordsets.md) 참조). 영역 리소스 자체의 속성을 업데이트하는 데만 사용됩니다. 이 작업은 현재 영역 리소스에 대한 Azure 리소스 관리자 '태그'로 제한됩니다. 자세한 내용은 [Etag 및 태그](dns-getstarted-create-dnszone.md#tagetag)를 참조하세요.

	azure network dns zone set myresourcegroup contoso.com -t prod=value2

## DNS 영역 삭제

`azure network dns zone delete`를 사용하여 DNS 영역을 삭제할 수 있습니다. 이 작업에는 DNS 영역을 제거할 것인지 확인하는 메시지를 표시하지 않는 선택적 *-q* 스위치가 있습니다.
 
Azure DNS에서 DNS 영역을 삭제하기 전에 영역을 만들 때 자동으로 생성된, 영역 루트에 있는 NS 및 SOA 레코드를 제외한 모든 레코드 집합을 삭제해야 합니다.

	azure network dns zone delete myresourcegroup contoso.com 



## 다음 단계
DNS 영역을 만든 후에는 [레코드 집합 및 레코드](dns-getstarted-create-recordset-cli.md)를 만들어 인터넷 도메인에 대한 이름 확인을 시작해야 합니다.

<!---HONumber=AcomDC_0817_2016-->