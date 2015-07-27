<properties 
   pageTitle="응용 프로그램 게이트웨이 소개 | Microsoft Azure"
   description="이 페이지는 게이트웨이 크기, HTTP 부하 분산, 쿠키 기반의 세션 선호도, 및 SSL 오프로드를 비롯하여 응용 프로그램 게이트웨이 서비스 계층 7 부하 분산의 개요를 제공합니다."
   documentationCenter="na"
   services="application-gateway"
   authors="joaoma"
   manager="jdial"
   editor="tysonn"/>
<tags 
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="06/24/2015"
   ms.author="joaoma"/>

# 응용 프로그램 게이트웨이 기술 개요 


Microsoft Azure 응용 프로그램 게이트웨이는 Azure VPN 게이트웨이 서비스와 비슷한 관리 서비스입니다. 응용 프로그램 게이트웨이는 IIS 응용 프로그램 요청 라우팅(ARR)에 기반을 둔 Azure-관리 HTTP 부하 분산 솔루션을 제공합니다. 응용 프로그램 게이트웨이 서비스는 항상 사용 가능하고 요금제입니다. SLA 및 가격은 [SLA](http://azure.microsoft.com/support/legal/sla/) 및 [가격 책정](https://azure.microsoft.com/pricing/details/application-gateway/)페이지를 참고하세요.

현재 응용 프로그램 게이트웨이는 다음과 같은 layer7 응용 프로그램을 지원합니다.

- HTTP 부하 분산
- 쿠키 기반 세션 선호도
- SSL 오프로드

![응용 프로그램 게이트웨이](./media/application-gateway-introduction/appgateway1.png)

## HTTP 계층 7 부하 분산
Azure는 소프트웨어 부하 분산 장치를 통해 분산 계층 4 부하를 제공합니다. 이것은 암시적으로 모든 클라우드 서비스하는 부하 분산된 VIP(공용 또는 내부)입니다. 그러나 많은 응용 프로그램이 계층 (HTTP) 7 기반 부하 분산을 사용할 수 있습니다.


HTTP 계층 7 부하 분산은 다음에 유용합니다.


- 동일한 백엔드 VM에 도달하기 위해 동일한 사용자/클라이언트 세션의 요청을 필요로 하는 응용 프로그램입니다. 이 작업의 예는 쇼핑 카트 앱 및 웹 메일 서버가 될 것입니다.
- 응용 프로그램은 SSL 종료 오버헤드에서 웹 서버 팜을 무료로 원합니다.
- CDN 같은 응용 프로그램은 다른 서버로 라우팅 및 부하를 분산하기 위해 여러 HTTP 요청을 동일한 장기 실행 TCP 연결에서 요구합니다.

## 게이트웨이 크기 및 인스턴스

응용 프로그램 게이트웨이는 현재 소형, 중형 및 대형의 3가지 크기를 제공합니다. 소규모 인스턴스 크기는 개발 및 테스트 시나리오를 위해 사용 됩니다.

구독 당 최대 10개의 응용 프로그램 게이트웨이 만들고 각 응용 프로그램 게이트웨이는 최대 10개의 인스턴스를 가질 수 있습니다. Azure 관리 서비스와 비슷한 부하 분산 응용 프로그램 게이트웨이는 Azure 소프트웨어 부하 분산 뒤에서 계층 7 부하 분산 장치를 프로비전하는 것을 허용합니다.

## 구성 및 관리

응용 프로그램 게이트웨이는 REST API 및 PowerShell cmdlet을 사용하여 생성하고 관리할 수 있습니다.

## 다음 단계

응용 프로그램 게이트웨이를 만듭니다. [응용 프로그램 게이트웨이 만들기](application-gateway-create-gateway.md)를 참조하세요.

SSL 오프로드를 구성 합니다. [응용 프로그램 게이트웨이를 사용하여 SSL 오프로드 구성](application-gateway-ssl.md)을 참조합니다.

<!---HONumber=July15_HO3-->