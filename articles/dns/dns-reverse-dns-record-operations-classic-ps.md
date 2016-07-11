<properties 
   pageTitle="클래식 배포 모델에서 PowerShell을 사용하여 서비스에 대한 역방향 DNS 레코드를 관리하는 방법 | Microsoft Azure"
   description="클래식 배포 모델에서 PowerShell을 사용하여 Azure 서비스에 대한 역방향 DNS 레코드 또는 PTR 레코드를 관리하는 방법 "
   services="DNS"
   documentationCenter="na"
   authors="s-malone"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="DNS"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/09/2016"
   ms.author="s-malone" />

# PowerShell을 사용하여 서비스(클래식)에 대한 역방향 DNS 레코드를 관리하는 방법

[AZURE.INCLUDE [dns-reverse-dns-record-operations-arm-selectors-include.md](../../includes/dns-reverse-dns-record-operations-arm-selectors-include.md)] <BR> [AZURE.INCLUDE [DNS-reverse-dns-record-operations-intro-include.md](../../includes/dns-reverse-dns-record-operations-intro-include.md)] <BR> [AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager 모델을 사용하여 이러한 단계를 수행하는](dns-reverse-dns-record-operations-ps.md) 방법을 알아봅니다.

## 역방향 DNS 레코드의 유효성 검사
타사에서 DNS 도메인에 매핑하는 역방향 DNS 레코드를 만들 수 없도록 하기 위해 Azure에서는 다음 중 하나에 해당하는 역방향 DNS 레코드만 만들도록 허용합니다.

- 역방향 DNS FQDN이 지정되어 있는 클라우드 서비스의 이름이거나, 동일한 구독 내의 클라우드 서비스 이름입니다. 예를 들어 역방향 DNS가 "contosoapp1.cloudapp.net."입니다.
- 역방향 DNS FQDN 정방향이 지정되어 있는 클라우드 서비스의 이름 또는 IP로 확인되거나, 동일한 구독 내의 클라우드 서비스 이름 또는 IP로 확인됩니다. 예를 들어 역방향 DNS가 contosoapp1.cloudapp.net에 대한 CName 별칭인 "app1.contoso.com."입니다.

유효성 검사는 클라우드 서비스의 역방향 DNS 속성이 설정되거나 수정되는 경우에만 수행됩니다. 유효성 재검사는 정기적으로 수행되지 않습니다.

## 기존 클라우드 서비스에 역방향 DNS 추가
"Set-AzureService" cmdlet을 사용하여 기존 클라우드 서비스에 역방향 DNS 레코드를 추가할 수 있습니다.

	PS C:\> Set-AzureService –ServiceName “contosoapp1” –Description “App1 with Reverse DNS” –ReverseDnsFqdn “contosoapp1.cloudapp.net.”

## 역방향 DNS를 사용하여 클라우드 서비스 만들기  
"Set-AzureService" cmdlet을 사용하여 지정된 역방향 DNS 속성을 사용하여 새 클라우드 서비스를 추가할 수 있습니다.

	PS C:\> New-AzureService –ServiceName “contosoapp1” –Location “West US” –Description “App1 with Reverse DNS” –ReverseDnsFqdn “contosoapp1.cloudapp.net.”

## 기존 클라우드 서비스에 대한 역방향 DNS 보기
"Get-AzureService" cmdlet을 사용하여 기존 클라우드 서비스에 대해 구성된 값을 볼 수 있습니다.

	PS C:\> Get-AzureService "contosoapp1"

## 기존 클라우드 서비스에서 역방향 DNS 제거
"Set-AzureService" cmdlet을 사용하여 기존 클라우드 서비스에서 역방향 DNS 속성을 제거할 수 있습니다. 역방향 DNS 속성 값을 공백으로 설정하면 됩니다.

	PS C:\> Set-AzureService –ServiceName “contosoapp1” –Description “App1 with Reverse DNS” –ReverseDnsFqdn “”

[AZURE.INCLUDE [FAQ](../../includes/dns-reverse-dns-record-operations-faq-asm-include.md)]

<!---HONumber=AcomDC_0629_2016-->