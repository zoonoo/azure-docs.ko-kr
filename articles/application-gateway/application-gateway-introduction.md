<properties
   pageTitle="응용 프로그램 게이트웨이 소개 | Microsoft Azure"
   description="이 페이지는 게이트웨이 크기, HTTP 부하 분산, 쿠키 기반의 세션 선호도 및 SSL 오프로드를 비롯하여 응용 프로그램 게이트웨이 서비스 계층 7 부하 분산의 개요를 제공합니다."
   documentationCenter="na"
   services="application-gateway"
   authors="joaoma"
   manager="carmonm"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/09/2015"
   ms.author="joaoma"/>

# 응용 프로그램 게이트웨이란?


Microsoft Azure 응용 프로그램 게이트웨이는 계층 7 부하 분산을 기반으로 한 Azure-관리 HTTP 부하 분산 솔루션을 제공합니다.

응용 프로그램 부하 분산을 통해 IT 관리자 및 개발자가 HTTP 기반 네트워크 트래픽에 대한 라우팅 규칙을 만들 수 있습니다. 응용 프로그램 게이트웨이 서비스는 가용성이 높으며 데이터 통신을 사용합니다. SLA 및 가격 책정은 [SLA](http://azure.microsoft.com/support/legal/sla/) 및 [가격 책정](https://azure.microsoft.com/pricing/details/application-gateway/) 페이지를 참조하세요.

현재 응용 프로그램 게이트웨이는 다음과 같은 계층 7 응용 프로그램 전달을 지원합니다.

- HTTP 부하 분산
- 쿠키 기반 세션 선호도
- SSL(Secure Sockets Layer) 오프로드

![응용 프로그램 게이트웨이](./media/application-gateway-introduction/appgateway1.png)

HTTP 계층 7 부하 분산은 다음에 유용합니다.

- 동일한 백엔드 VM에 도달하기 위해 동일한 사용자/클라이언트 세션의 요청을 필요로 하는 응용 프로그램입니다. 이 작업의 예는 쇼핑 카트 앱 및 웹 메일 서버가 될 것입니다.
- 응용 프로그램은 SSL 종료 오버헤드에서 웹 서버 팜을 무료로 원합니다.
- 콘텐츠 배달 네트워크와 같은 응용 프로그램은 다른 서버로 라우팅 또는 부하 분산을 위해 여러 HTTP 요청을 동일한 장기 실행 TCP 연결에서 요구합니다.


## 게이트웨이 크기 및 인스턴스

응용 프로그램 게이트웨이는 현재 소형, 중형 및 대형의 3가지 크기를 제공합니다. 소규모 인스턴스 크기는 개발 및 테스트 시나리오를 위해 사용 됩니다.

구독당 최대 50개의 응용 프로그램 게이트웨이를 만들 수 있으며 각 응용 프로그램 게이트웨이에는 최대 10개의 인스턴스가 있을 수 있습니다. Azure 관리 서비스와 비슷한 부하 분산 응용 프로그램 게이트웨이는 Azure 소프트웨어 부하 분산 뒤에서 계층 7 부하 분산 장치를 프로비전하는 것을 허용합니다.

아래 표에서는 각 응용 프로그램 게이트웨이 인스턴스의 평균 성능 처리량을 보여 줍니다.


| 백 엔드 페이지 응답 | 작음 | 중간 | 큼|
|---|---|---|---|
| 6K | 7\.5Mbps | 13Mbps | 50Mbps |
|100K | 35Mbps | 100Mbps| 200Mbps |


>[AZURE.NOTE]이는 응용 프로그램 게이트웨이 처리량에 대한 대략적인 지침입니다. 실제 처리량은 평균 페이지 크기, 백 엔드 인스턴스의 위치 및 페이지 처리 시간 등 다양한 환경 세부 사항에 따라 달라집니다.

## 상태 모니터링


Azure 응용 프로그램 게이트웨이는 백 엔드 인스턴스의 상태를 자동으로 모니터링합니다. 자세한 내용은 [응용 프로그램 게이트웨이 상태 모니터링 개요](application-gateway-probe-overview.md)를 참조하세요.

## 구성 및 관리

응용 프로그램 게이트웨이는 REST API 및 PowerShell cmdlet을 사용하여 만들고 관리할 수 있습니다.



## 다음 단계

응용 프로그램 게이트웨이를 만듭니다. [응용 프로그램 게이트웨이 만들기](application-gateway-create-gateway.md)를 참조하세요.

SSL 오프로드를 구성 합니다. [응용 프로그램 게이트웨이를 사용하여 SSL 오프로드 구성](application-gateway-ssl.md)을 참조하세요.

<!---HONumber=AcomDC_0114_2016-->