<properties
   pageTitle="Express 경로 도입을 위한 필수 구성 요소"
   description="이 페이지에는 Express 경로 회로를 주문하기 전에 충족해야하는 요구 사항 목록을 제공합니다."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="adinah"
   editor="tysonn"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/06/2015"
   ms.author="cherylmc"/>


# Express 경로 필수 구성 요소  

Express 경로를 사용하여 Microsoft 클라우드 서비스에 연결하려면 다음 필수 조건을 충족하는지 확인해야 합니다.

## 연결에 대한 필수 구성 요소

- 유효한 활성 Microsoft Azure 계정
- [지원 목록](expressroute-locations.md)에서 네트워크 서비스 공급자(NSP) 또는 exchange 공급자(EXP)와의 관계는 업체를 통해 쉽게 연결할 수 있어야 합니다. 네트워크 서비스 공급자 또는 exchange 공급자와 기존의 비즈니스 관계가 있어야 합니다. 사용하는 서비스가 Express 경로와 호환 되는지 확인해야 합니다.
- 네트워크 서비스 공급자를 사용하고 네트워크 서비스 공급자가 위의 목록에 없는 경우에도 Azure에 대한 연결을 설정할 수 있습니다.
	- 위에 나열된 Exchange 위치 중 하나에 있는지 확인하려면 네트워크 공급자를 확인합니다.
	- 네트워크 공급자가 선택한 Exchange 위치로 네트워크를 확장합니다.
	- Azure에 연결하려면 Exchange 공급자를 통해 Express 경로 회로를 주문합니다.
- 서비스 공급자의 인프라에 연결합니다. 나열된 다음 항목 중 하나 이상에 대한 조건을 충족해야 합니다.
	- 네트워크 서비스 공급자의 VPN 고객이고 네트워크 서비스 공급자의 VPN 인프라에 연결된 온-프레미스 사이트가 하나 이상 있습니다. 네트워크 서비스 공급자는 VPN 서비스가 Express 경로에 대한 요구 사항을 만족하는 지 확인합니다.
	- 인프라는 exchange 공급자의 데이터 센터에 배치됩니다.
	- Exchange 공급자의 이더넷 교환 인프라에 대해 이더넷에 연결됩니다.
- 라우팅 구성을 위한 IP 주소 및 AS 번호입니다.
	- 개인 AS 번호를 사용하여 Azure 개인 피어링 라우팅 도메인에 연결할 수 있습니다. 작업을 수행하려는 경우 65000 이상이어야 합니다. AS 번호에 대한 자세한 내용은 [AS(자치 시스템) 번호](http://www.iana.org/assignments/as-numbers/as-numbers.xhtml)를 참조하세요.
	- 라우팅을 구성하는 IP 주소. /28 서브넷이 필요합니다. Azure 또는 온-프레미스에 사용된 모든 IP 주소 범위와 겹치지 않아야 합니다.
	- Azure 공용 서비스와 BGP 세션을 구성하기 위해서는 사용자 고유의 공용 AS 번호를 사용해야 합니다.

## 다음 단계

- Express 경로에 대한 자세한 내용은 [Express 경로 FAQ](expressroute-faqs.md)를 참조하세요.
- Express 경로 연결 구성에 대한 자세한 내용은 다음을 참조하세요.
	- [네트워크 서비스 공급자를 통해 Express 경로 연결 구성](expressroute-configuring-nsps.md)
	- [Exchange 공급자를 통해 Express 경로 연결 구성](expressroute-configuring-exps.md)
 

<!---HONumber=July15_HO4-->