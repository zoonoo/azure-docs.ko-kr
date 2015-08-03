<properties 
   pageTitle="Azure DNS 개요 | Microsoft Azure" 
   description="Microsoft Azure의 Azure DNS 호스팅 서비스 및 Microsoft Azure에서 도메인 호스팅 시작에 대한 개요입니다." 
   services="dns" 
   documentationCenter="na" 
   authors="joaoma" 
   manager="adinah" 
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="05/01/2015"
   ms.author="joaoma"/>

# Azure DNS 개요

인터넷에서 모든 웹 사이트 또는 서비스 뒤에는 IP 주소가 있습니다. 예를 들어 www.microsoft.com은 IP 주소 134.170.185.46을 사용합니다. Domain Name System, 즉 DNS는 웹 사이트 또는 서비스 이름을 해당 IP 주소로 변환(또는 확인)합니다.

Azure DNS는 DNS 도메인에 대한 호스팅 서비스로, Microsoft Azure 인프라를 사용하여 이름 확인을 제공합니다. Azure에 도메인을 호스트하면 다른 Azure 서비스와 동일한 자격 증명, API, 도구 및 대금 청구를 사용하여 DNS 레코드를 관리할 수 있습니다.

Azure DNS의 DNS 도메인은 DNS 이름 서버의 Azure 글로벌 네트워크에 호스트됩니다. 사용 가능한 가장 가까운 DNS 서버에서 각 DNS 쿼리에 응답하도록 애니캐스트 네트워킹이 사용됩니다. 이렇게 하면 도메인에 대해 빠른 성능과 고가용성이 제공됩니다.

서비스는 ARM(Azure 리소스 관리자)을 기반으로 합니다. Azure 리소스 관리자 REST API, .NET SDK, PowerShell cmdlet 및 명령줄 인터페이스를 통해 도메인과 레코드를 관리할 수 있습니다. Azure DNS는 현재 미리 보기 상태이며, Azure 관리 포털에서 아직 지원되지 않습니다.<BR><BR> Azure DNS는 현재 도메인 이름 구입을 지원하지 않습니다. 도메인을 구입하려면 일반적으로 소액의 연간 요금을 부과하는 타사 도메인 이름 등록 기관을 사용해야 합니다. 그런 다음 DNS 레코드의 관리를 위해 Azure DNS에 해당 도메인을 호스트할 수 있습니다. 자세한 내용은 [Azure DNS에 도메인 위임](dns-domain-delegation.md)을 참조하세요.


## 다음 단계

[DNS 영역 만들기 시작](dns-getstarted-create-dnszone.md)

[.NET SDK로 Azure 작업 자동화](../dns-sdk)

[Azure DNS REST API 참조](https://msdn.microsoft.com/library/azure/mt163862.aspx)




 

<!---HONumber=July15_HO4-->