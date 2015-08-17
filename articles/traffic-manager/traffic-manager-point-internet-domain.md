<properties
   pageTitle="회사 인터넷 도메인이 트래픽 관리자 도메인을 가리키도록 설정"
   description="이 문서에서는 사용자의 회사 도메인 이름이 트래픽 관리자 도메인 이름을 가리킵니다."
   services="traffic-manager"
   documentationCenter=""
   authors="joaoma"
   manager="adinah"
   editor="tysonn" />
<tags
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/27/2015"
   ms.author="joaoma;cherylmc" />

# 회사 인터넷 도메인이 트래픽 관리자 도메인을 가리키도록 설정

사용자의 회사 도메인 이름이 트래픽 관리자 도메인 이름을 가리키도록 회사 도메인 이름이 트래픽 관리자 프로필의 도메인 이름으로 매핑하는 CNAME 레코드 종류를 사용하여 인터넷 DNS 서버의 DNS 리소스 레코드를 수정합니다. 트래픽 관리자 프로필의 구성 페이지의 **일반** 섹션에서 트래픽 관리자 도메인 이름을 볼 수 있습니다.

예를 들어, 회사 도메인 이름 **www.contoso.com**을 트래픽 관리자 도메인 이름 **contoso.trafficmanager.net**으로 가리키도록 하려면 DNS 리소스 레코드를 다음과 같이 업데이트하세요.

    www.contoso.com IN CNAME contoso.trafficmanager.net

이제 *www.contoso.com*으로 요청되는 모든 트래픽이 *contoso.trafficmanager.net*으로 이동됩니다.

>[AZURE.IMPORTANT]*contoso.com*과 같은 두 번째 수준의 도메인이 트래픽 관리자 도메인을 가리킬 수 없습니다. DNS 프로토콜의 제한사항이며, CNAME 레코드를 두 번째 수준 도메인 이름으로 허용하지 않습니다.

## 참고 항목

[트래픽 관리자 부하 분산 방법 정보](traffic-manager-load-balancing-methods.md)

[트래픽 관리자 구성 작업](https://msdn.microsoft.com/library/azure/hh744830.aspx)

[트래픽 관리자 개요](traffic-manager-overview.md)
 

<!---HONumber=August15_HO6-->