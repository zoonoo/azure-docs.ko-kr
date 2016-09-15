<properties
   pageTitle="Resource Manager에서 Azure CLI를 사용하여 서비스에 대한 역방향 DNS 레코드를 관리하는 방법 | Microsoft Azure"
   description="Resource Manager에서 Azure CLI를 사용하여 Azure 서비스에 대한 역방향 DNS 레코드 또는 PTR 레코드를 관리하는 방법"
   services="DNS"
   documentationCenter="na"
   authors="s-malone"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags
   ms.service="DNS"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/09/2016"
   ms.author="smalone" />

# Azure CLI를 사용하여 서비스에 대한 역방향 DNS 레코드를 관리하는 방법

[AZURE.INCLUDE [DNS-reverse-dns-record-operations-arm-selectors-include.md](../../includes/dns-reverse-dns-record-operations-arm-selectors-include.md)]
<BR>
[AZURE.INCLUDE [DNS-reverse-dns-record-operations-intro-include.md](../../includes/dns-reverse-dns-record-operations-intro-include.md)]
<BR>
[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](dns-reverse-dns-record-operations-classic-ps.md).

## 역방향 DNS 레코드의 유효성 검사
타사에서 DNS 도메인에 매핑하는 역방향 DNS 레코드를 만들 수 없도록 하기 위해 Azure에서는 다음 중 하나에 해당하는 역방향 DNS 레코드만 만들도록 허용합니다.

- "ReverseFqdn"이 지정되어 있는 공용 IP 주소 리소스에 대한 "Fqdn"이거나, 동일한 구독 내의 공용 IP 주소에 대한 "Fqdn"과 같습니다. 예를 들어 "ReverseFqdn"이 "contosoapp1.northus.cloudapp.azure.com."입니다.

- "ReverseFqdn" 정방향이 지정되어 있는 공용 IP 주소의 이름 또는 IP 주소로 확인되거나, 동일한 구독 내의 공용 IP 주소 "Fqdn" 또는 IP로 확인됩니다. 예를 들어 "ReverseFqdn"이 "contosoapp1.northus.cloudapp.azure.com."에 대한 CName 별칭인 "app1.contoso.com."입니다.

유효성 검사는 공용 IP 주소의 역방향 DNS 속성이 설정되거나 수정되는 경우에만 수행됩니다. 유효성 재검사는 정기적으로 수행되지 않습니다.

## 기존 공용 IP 주소에 역방향 DNS 추가
azure network public-ip set을 사용하여 기존 공용 IP 주소에 역방향 DNS를 추가할 수 있습니다.

	azure network public-ip set -n PublicIp -g NRP-DemoRG-PS -f contosoapp1.westus.cloudapp.azure.com.

DNS 이름이 없는 기존 공용 IP 주소에 역방향 DNS를 추가하려면 DNS 이름도 지정해야 합니다. azure network public-ip set을 사용하여 추가할 수 있습니다.

	azure network public-ip set -n PublicIp -g NRP-DemoRG-PS -d contosoapp1 -f contosoapp1.westus.cloudapp.azure.com.

## 역방향 DNS를 사용하여 공용 IP 주소 만들기
azure network public-ip create를 사용하여 지정된 역방향 DNS 속성을 사용하여 새 공용 IP 주소를 추가할 수 있습니다.

	azure network public-ip create -n PublicIp3 -g NRP-DemoRG-PS -l westus -d contosoapp3 -f contosoapp3.westus.cloudapp.azure.com.

## 기존 공용 IP 주소에 대한 역방향 DNS 보기
azure network public-ip show를 사용하여 기존 공용 IP 주소에 구성된 값을 볼 수 있습니다.

	azure network public-ip show -n PublicIp3 -g NRP-DemoRG-PS

## 기존 공용 IP 주소에서 역방향 DNS 제거
azure network public-ip set을 사용하여 기존 공용 IP 주소에서 역방향 DNS 속성을 제거할 수 있습니다. ReverseFqdn 속성 값을 공백으로 설정하면 됩니다.

	azure network public-ip set -n PublicIp3 -g NRP-DemoRG-PS –f “”

[AZURE.INCLUDE [FAQ](../../includes/dns-reverse-dns-record-operations-faq-arm-include.md)]

<!---HONumber=AcomDC_0824_2016-->