<properties 
   pageTitle="응용 프로그램 게이트웨이 소개 | Microsoft Azure"
   description="이 페이지는 게이트웨이 크기, HTTP 부하 분산, 쿠키 기반의 세션 선호도, 및 SSL 오프로드를 비롯하여 응용 프로그램 게이트웨이 서비스 계층 7 부하 분산의 개요를 제공합니다."
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


Microsoft Azure 응용 프로그램 게이트웨이는 레이어 7 부하 분산에 기반을 둔 Azure-관리 HTTP 부하 분산 솔루션을 제공합니다.

응용 프로그램 부하 분산을 통해 IT 관리자 및 개발자가 HTTP 기반 네트워크 트래픽에 대한 라우팅 규칙을 만들 수 있습니다. 응용 프로그램 게이트웨이 서비스는 가용성이 높으며 데이터 통신을 사용합니다. SLA 및 가격은 [SLA](http://azure.microsoft.com/support/legal/sla/) 및 [가격 책정](https://azure.microsoft.com/pricing/details/application-gateway/) 페이지를 참조하세요.

현재 응용 프로그램 게이트웨이는 다음과 같은 layer7 응용 프로그램을 지원합니다.

- HTTP 부하 분산
- 쿠키 기반 세션 선호도
- SSL 오프로드

![응용 프로그램 게이트웨이](./media/application-gateway-introduction/appgateway1.png)

## HTTP 계층 7 부하 분산

Azure는 전송 수준(TCP/UDP)에서 작동하며 응용 프로그램 게이트웨이 서비스로 모든 수신 네트워크 트래픽이 부하 분산되는 Azure 부하 분산 장치를 통해 계층 4 부하 분산을 제공합니다. 응용 프로그램 게이트웨이는 라우팅 규칙을 HTTP 트래픽에 적용하며 수준 7(HTTP) 부하 분산을 제공합니다. 응용 프로그램 게이트웨이를 만들 때 끝점(VIP)이 연결되며 수신 네트워크 트래픽에 대한 공용 IP로 사용됩니다.

응용 프로그램 게이트웨이는 가상 컴퓨터, 클라우드 서비스, 웹앱 또는 외부 IP 주소인 해당 구성에 따라 HTTP 트래픽을 라우팅합니다.

아래 다이어그램에서는 응용 프로그램 게이트웨이에 대한 트래픽 흐름 방식을 설명합니다.

 
![응용 프로그램 게이트웨이2](./media/application-gateway-introduction/appgateway2.png)

HTTP 계층 7 부하 분산은 다음에 유용합니다.


- 동일한 백엔드 VM에 도달하기 위해 동일한 사용자/클라이언트 세션의 요청을 필요로 하는 응용 프로그램입니다. 이 작업의 예는 쇼핑 카트 앱 및 웹 메일 서버가 될 것입니다.
- 응용 프로그램은 SSL 종료 오버헤드에서 웹 서버 팜을 무료로 원합니다.
- CDN 같은 응용 프로그램은 다른 서버로 라우팅 및 부하를 분산하기 위해 여러 HTTP 요청을 동일한 장기 실행 TCP 연결에서 요구합니다.

## 게이트웨이 크기 및 인스턴스

응용 프로그램 게이트웨이는 현재 소형, 중형 및 대형의 3가지 크기를 제공합니다. 소규모 인스턴스 크기는 개발 및 테스트 시나리오를 위해 사용 됩니다.

구독당 최대 50개의 응용 프로그램 게이트웨이를 만들 수 있으며 각 응용 프로그램 게이트웨이의 인스턴스는 최대 10개일 수 있습니다. Azure 관리 서비스와 비슷한 부하 분산 응용 프로그램 게이트웨이는 Azure 소프트웨어 부하 분산 뒤에서 계층 7 부하 분산 장치를 프로비전하는 것을 허용합니다.

아래 표에서는 각 응용 프로그램 게이트웨이 인스턴스의 평균 성능 처리량을 보여 줍니다.


| 백 엔드 페이지 응답 | 작음 | 중간 | 큼|
|---|---|---|---|
| 6K | 7\.5Mbps | 13Mbps | 50Mbps |
|100k | 35Mbps | 100Mbps| 200Mbps |


>[AZURE.NOTE]이는 응용 프로그램 게이트웨이 처리량에 대한 대략적인 지침입니다. 실제 처리량은 평균 페이지 크기, 백 엔드 인스턴스의 위치, 페이지 처리 시간 등 다양한 환경 세부 사항에 따라 달라집니다.

## 상태 모니터링
 

Azure 응용 프로그램 게이트웨이는 30초마다 백 엔드 인스턴스의 상태를 모니터링합니다. 또한 구성의 *BackendHttpSettings* 요소에 구성된 포트에서 각 인스턴스에 HTTP 상태 검색 요청을 보냅니다. 상태 검색은 응답 상태 코드가 200-399 범위인 성공적인 HTTP 응답을 기대합니다.

성공적인 HTTP 응답을 받으면 백 엔드 서버는 정상으로 표시되고 Azure 응용 프로그램 게이트웨이에서 트래픽을 계속 받습니다. 검색이 실패하는 경우 백 엔드 인스턴스는 정상 풀에서 제거되고 트래픽이 더 이상 이 서버로 이동하지 않습니다. 현재 상태를 확인하기 위해 실패한 백 엔드 인스턴스에 대해 상태 검색이 30초마다 계속됩니다. 백 엔드 인스턴스가 상태 검색에 성공적으로 응답하는 경우 백 엔드 풀에 정상으로 다시 추가되고, 트래픽이 이 인스턴스로 다시 이동하기 시작합니다.

## 구성 및 관리

응용 프로그램 게이트웨이는 REST API 및 PowerShell cmdlet을 사용하여 만들고 관리할 수 있습니다.



## 다음 단계

응용 프로그램 게이트웨이를 만듭니다. [응용 프로그램 게이트웨이 만들기](application-gateway-create-gateway.md)를 참조하세요.

SSL 오프로드를 구성 합니다. [응용 프로그램 게이트웨이를 사용하여 SSL 오프로드 구성](application-gateway-ssl.md)을 참조하세요.

<!---HONumber=Nov15_HO3-->