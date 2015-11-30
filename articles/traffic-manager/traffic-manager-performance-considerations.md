<properties
   pageTitle="Azure 트래픽 관리자 성능 고려 사항 | Microsoft Azure"
   description="트래픽 관리자의 성능 및 트래픽 관리자 사용 시 웹 사이트의 성능을 테스트 하는 방법에 대한 이"
   services="traffic-manager"
   documentationCenter=""
   authors="kwill-MSFT"
   manager="adinah"
   editor="joaoma" />

<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/12/2015"
   ms.author="joaoma" />


# 트래픽 관리자 성능 고려 사


Azure 트래픽 관리자 관련 일반적인 질문은 발생할 수 있는 잠재적 성능 문제를 다룹니다. 일반적으로 “트래픽 관리자가 내 웹 사이트에 추가하는 데 발생하는 지연이 어느 정도인가요?”, “내 모니터링 사이트에서 내 웹 사이트가 어제 2시간 동안 느려졌다고 하는데, 그 시간에 트래픽 관리자 문제가 있었나요?”, “트래픽 관리자 서버는 어디에 있나요? 트래픽 관리자 서버가 내 웹 사이트와 동일한 데이터센터에 있어서 성능에 영향을 받지 않았으면 합니다.”와 같은 질문입니다.

이 페이지에서는 트래픽 관리자가 웹 사이트에 일으킬 수 있는 직접적인 성능 영향에 대해 다룹니다. 미국 동부에 웹 사이트가 있고 아시아에도 웹 사이트가 있는데 미국 동부에서 트래픽 관리자 검색에 문제가 있는 경우, 모든 사용자를 아시아 웹 사이트로 안내하여 성능 영향을 확인하게 됩니다. 하지만 이 성능 영향은 트래픽 관리자 자체와는 아무런 관련이 없습니다.

  

## 트래픽 관리자 작동 방식에 대한 중요 정보

[트래픽 관리자 개요](traffic-manager-overview.md)는 트래픽 관리자 작동 방식에 대해 배울 수 있는 탁월한 리소스지만 해당 페이지에는 매우 많은 정보가 있어 성능 관련 주요 정보를 골라내기가 어려울 수 있습니다. MSDN 설명서에서 살펴볼 중요한 포인트는 이미지 3에서 #5 및 #6단계이며, 여기에서 다 자세히 설명할 것입니다.

- 트래픽 관리자는 기본적으로 DNS 확인 한 가만 수행합니다. 이는 트래픽 관리자가 웹 사이트에 미칠 수 있는 유일한 성능 영향은 초기 DNS 조회라는 뜻입니다.
- 트래픽 관리자 DNS 조회에 대한 확인 지점입니다. 트래픽 관리자는 정보를 표시하고 정기적으로 업데이트하며, 일반 Microsoft DNS 루트 서버는 사용자 정책 및 검색 결과에 기반합니다. 초기 DNS 조회 중에도 DNS 요청은 일반 Microsoft DNS 루트 서버가 처리하기 때문에 트래픽 관리자가 관여할 부분은 없습니다. 트래픽 관리자가 ‘다운'되는 경우(예: 정책 검색 및 DNS 업데이트를 수행하는 VM의 오류) Microsoft DNS 서버의 항목이 여전히 보존되기 때문에 트래픽 관리자 DNS 이름에 영향을 미치지 않습니다. 유일한 영향은 정책에 기반한 검색 및 업데이트가 일어나지 않는다는 점입니다(예: 기본 사이트가 다운되는 경우 사이트 장애 조치에 조첨을 맞추기 위해 트래픽 관리자는 DNS를 업데이트할 수 없음).
- 트래픽은 트래픽 관리자를 통해 전달되지 않습니다. 트래픽 관리자 서버는 클라이언트와 Azure 호스티드 서비스 간의 중재자 역할을 하지 않습니다. DNS 조회가 완료되면 트래픽 관리자가 클라이언트와 서버 간의 통신을 완전히 제거합니다.
- DNS 조회는 매우 빠르며 캐시됩니다. 초기 DNS 조회는 클라이언트 및 구성되는 해당 DNS 서버에 따라 다르며 일반적으로 클라이언트는 50ms 이내에 DNS 조회를 수행합니다(http://www.solvedns.com/dns-comparison/) 참조). 첫 번째 조화가 완료되면 DNS TTL에 대해 결과가 캐시되며, 트래픽 관리자의 경우 기본 300초입니다.
- 사용자가 선택하는 트래픽 관리자(성능, 장애 조치, 라운드 로빈)는 DNS 성능에 영향을 주지 않습니다. 성능 정책은 사용자 경험에 부정적인 영향을 줄 수 있습니다. 예를 들어 아시아서 호스팅된 서비스로 미국 사용자를 보내는 경우 이 성능 문제는 트래픽 관리자에 의해 발생되지 않습니다.

  

## 트래픽 관리자 성능 테스트

트래픽 관리자 성능 및 동작을 결정하는 데 사용할 수 있는 공개적으로 사용 가능한 몇 가지 웹 사이트가 있습니다. 이러한 사이트는 DNS 지연 및 호스티드 서비스 중 어느 곳에 전 세계의 사용자를 안내할 것인지 결정하는 데 유용합니다. 이러한 도구 대부분은 DNS 결과를 캐시하지 않기 때문에 테스트를 여러 번 실행하면 전체 DNS 조회를 보여줍니다. 반면 트래픽 관리자 끝점에 연결된 클라이언트는 TTL 기간 동안 전체 DNS 조회 성능 영향을 한 번만 볼 수 있습니다.


## 성능을 측정하는 샘플 도구


가장 간단한 도구 중 하나는 WebSitePulse입니다. URL을 입력하고 DNS 확인 시간, 첫 번째 바이트, 마지막 바이트 및 기타 성능 통계와 같은 통계가 표시됩니다. 3가지 다른 위치 중에서 사이트를 테스트하는 위치를 선택할 수 있습니다. 이 예에서는 첫 번째 실행에서 첫 번째 DNS 조회에 0.204초가 소요됨을 보여줍니다. 동일한 트래픽 관리자 끝점에서 이 테스트를 두 번째로 실행할 때에는 결과가 이미 캐시되었기 때문에 DNS 조회에 0.002초가 소요됩니다.

http://www.websitepulse.com/help/tools.php


![pulse1](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse.png)

캐시될 때 DNS 시간:


![pulse2](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse2.png)



여러 지역에서 동시에 DNS 확인 시간을 파악하는 데 유용한 또 다른 도구는 Watchmouse의 Check Website 도구입니다. URL을 입력하면 DNS 확인 시간, 연결 시간 및 여러 지역에서의 속도를 볼 수 있습니다. 전 세계의 다른 사용자가 보내지는 호스티드 서비스를 보기 위해 트래픽 관리자 성능 정책을 테스트하기도 쉽습니다.

http://www.watchmouse.com/en/checkit.php


![pulse1](./media/traffic-manager-performance-considerations/traffic-manager-web-site-watchmouse.png)

http://tools.pingdom.com/ - 웹 사이트를 테스트 하고 시각적 그래프로 페이지에 각 요소에 대한 성능 통계를 제공합니다. 페지이 분석 탭으로 전환하면 DNS 조회에 소요된 시간의 백분율을 볼 수 있습니다.

 

http://www.whatsmydns.net/ – 이 사이트는 20곳의 서로 다른 지리적 위치에서 DNS 조회를 수행하여 지도에 결과를 표시합니다. 클라이언트를 연결할 호스티드 서비스를 결정하는 데 도움이 되는 훌륭한 시각적 표현입니다.

 

http://www.digwebinterface.com – Watchmouse 사이트와 유사하지만, CNAME 및 A 레코드를 비롯한 자세한 DNS 정보를 보여줍니다. 'Colorize output' 및 'Stats' 옵션을 확인하고 네임서버에서 ‘All’을 선택합니다.

## 결론

위의 정보를 통해 알 수 있는 사실은 한 웹 사이트에서 트래픽 관리자에게 미치는 유일한 성능 영향은 첫 번째 DNS 조회(다양한 시간, 평균 50ms 이내)이며, DNS TTL 기간 동안(기본 300초)의 성능 영향은 0이고, TTL 만료 후 DNS 캐시 새로 고침이라는 점입니다. 따라서 “트래픽 관리자가 내 웹 사이트에 추가하는 데 발생하는 지연이 어느 정도인가요?”라는 질문에 대한 대답은 기본적으로 0입니다.


## 다음 단계


[트래픽 관리자 트래픽 라우팅 방법 정보](traffic-manager-load-balancing-methods.md)

[트래픽 관리자 설정 테스트](traffic-manager-testing-settings.md)

[트래픽 관리자 작업(REST API 참조)](http://go.microsoft.com/fwlink/?LinkId=313584)

[Azure 트래픽 관리자 cmdlet](http://go.microsoft.com/fwlink/p/?LinkId=400769)
 

<!---HONumber=Nov15_HO4-->