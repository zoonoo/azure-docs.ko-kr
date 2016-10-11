<properties
   pageTitle="CLI를 사용하여 DNS 영역에 대한 레코드 집합 및 레코드 만들기 | Microsoft Azure"
   description="Azure DNS에 대한 호스트 레코드를 만드는 방법입니다. CLI를 사용하여 레코드 집합 및 레코드를 설정합니다."
   services="dns"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/16/2016"
   ms.author="sewhee"/>

# CLI를 사용하여 DNS 레코드 집합 및 레코드 만들기

> [AZURE.SELECTOR]
- [Azure 포털](dns-getstarted-create-recordset-portal.md)
- [PowerShell](dns-getstarted-create-recordset.md)
- [Azure CLI](dns-getstarted-create-recordset-cli.md)


이 문서는 CLI를 사용하여 레코드 및 레코드 집합을 만드는 과정을 안내합니다. DNS 영역을 만든 후 도메인에 대한 DNS 레코드를 추가해야 합니다. 이 작업을 수행하려면 먼저 DNS 레코드 및 레코드 집합을 이해해야 합니다.

[AZURE.INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

## 레코드 집합 및 레코드 만들기

이 섹션에서는 레코드 집합 및 레코드를 만드는 방법을 보여 줍니다. 이 예제에서는 DNS 영역 "contoso.com"에 상대적 이름 "www"가 포함된 레코드 집합을 만듭니다. 레코드의 정규화된 이름은 "www.contoso.com"입니다. 레코드 형식은 "A"이고 TTL(Time to Live)은 60초입니다. 이 단계를 완료하면 빈 레코드 집합이 생성됩니다.

영역의 구로에서 레코드 집합을 만들려면(이 경우 "contoso.com"), 따옴표를 포함한 레코드 이름 "@"를 사용합니다. 이것이 일반적인 DNS 규칙입니다.

### 1\. 레코드 집합 만들기

레코드 집합을 만들려면 `azure network dns record-set create`을 사용합니다. 리소스 그룹, 영역 이름, 레코드 집합의 상대적 이름, 레코드 형식 및 TTL을 지정합니다. `--ttl` 매개 변수가 정의되지 않은 경우 기본값은 4(초)입니다. 이 단계를 완료하면 빈 "www" 레코드 집합이 생성됩니다.

*사용법: network dns record-set create <resource-group> <dns-zone-name> <name> <type> <ttl>*

	azure network dns record-set create myresourcegroup  contoso.com  www A  60

### 2\. 레코드 추가

새로 생성된 "www" 레코드 집합을 사용하려면 여기에 레코드를 추가해야 합니다. `azure network dns record-set add-record`를 사용하여 레코드 집합에 레코드를 추가합니다.

레코드 집합에 레코드를 추가하기 위한 매개 변수는 레코드 집합 형식에 따라 달라집니다. 예를 들어 "A" 형식의 레코드 집합을 사용하는 경우 `-a <IPv4 address>` 매개 변수를 사용해야만 레코드를 지정할 수 있습니다.

다음 명령을 사용하여 "www" 레코드 집합에 IPv4 *A* 레코드를 추가할 수 있습니다.

*사용법: network dns record-set add-record <resource-group> <dns-zone-name> <record-set-name> <type>*

	azure network dns record-set add-record myresourcegroup contoso.com  www A  -a 134.170.185.46

## 추가 레코드 형식 예제

다음 예제에서는 각 레코드 형식의 레코드 집합을 만드는 방법을 보여 줍니다. 각 레코드 집합은 단일 레코드를 포함합니다.

[AZURE.INCLUDE [dns-add-record-cli-include](../../includes/dns-add-record-cli-include.md)]

## 다음 단계

레코드 집합과 레코드를 관리하려면 [CLI를 사용하여 DNS 레코드 및 레코드 집합 관리](dns-operations-recordsets-portal.md)를 참조하세요.

Azure DNS에 대한 자세한 내용은 [Azure DNS 개요](dns-overview.md)를 참조하세요.

<!---HONumber=AcomDC_1005_2016-->