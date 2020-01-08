---
title: Application Gateway에 대 한 Azure Monitor 메트릭
description: 메트릭을 사용 하 여 application gateway의 성능을 모니터링 하는 방법을 알아봅니다.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 8/29/2019
ms.author: absha
ms.openlocfilehash: 12ecacf1266c0d8211f5928a933cfd4acf8c49f0
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/31/2019
ms.locfileid: "75551389"
---
# <a name="metrics-for-application-gateway"></a>Application Gateway에 대 한 메트릭

Application Gateway는 메트릭 이라는 데이터 요소를 게시 하 여 Application Gateway 및 백엔드 인스턴스의 성능을 [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) 합니다. 이러한 메트릭은 특정 시간에 응용 프로그램 게이트웨이의 일부 측면을 설명 하는 시계열 데이터의 정렬 된 집합에 있는 숫자 값입니다. Application Gateway을 통해 흐르는 요청이 있는 경우 해당 메트릭을 측정 하 고 60 초 간격으로 보냅니다. Application Gateway를 통해 전달 되는 요청이 없거나 메트릭에 대 한 데이터가 없는 경우 메트릭은 보고 되지 않습니다. 자세한 내용은 [Azure Monitor 메트릭](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics)을 참조하세요.

## <a name="metrics-supported-by-application-gateway-v2-sku"></a>Application Gateway V2 SKU에서 지원 되는 메트릭

### <a name="timing-metrics"></a>타이밍 메트릭

요청 및 응답의 타이밍과 관련 된 다음 메트릭을 사용할 수 있습니다. 특정 수신기에 대해 이러한 메트릭을 분석 하 여 WAN, Application Gateway, Application Gateway와 백 엔드 응용 프로그램 간 네트워크 또는 백 엔드 응용 프로그램 성능으로 인해에서 응용 프로그램의 속도가 느려지는 지 여부를 확인할 수 있습니다.

> [!NOTE]
>
> Application Gateway에 수신기가 두 개 이상인 경우에는 항상 *수신기* 차원을 기준으로 필터링 하 여 의미 있는 유추를 얻기 위해 다른 대기 시간 메트릭을 비교 합니다.

- **클라이언트 RTT**

  클라이언트와 Application Gateway 사이의 평균 왕복 시간입니다. 이 메트릭은 연결을 설정 하 고 승인을 반환 하는 데 걸리는 시간을 나타냅니다. 

- **Application gateway 총 시간**

  요청을 처리 하는 데 걸리는 평균 시간 및 응답을 보내는 데 걸리는 시간입니다. 이는 Application Gateway에서 HTTP 요청의 첫 번째 바이트를 수신 하는 시점부터 응답 전송 작업이 완료 되는 시간 까지의 평균 간격으로 계산 됩니다. 일반적으로 처리 시간, 요청 및 응답 패킷이 네트워크를 통해 이동 하는 시간 및 백 엔드 서버에서 응답 하는 데 걸린 시간을 Application Gateway 포함 하는 것이 중요 합니다.
  
*클라이언트 RTT* 가 *Application gateway 총 시간*보다 훨씬 많은 경우 클라이언트에서 관찰 한 대기 시간이 클라이언트와 Application Gateway 간의 네트워크 연결로 인해 발생 하는 것으로 추론할 수 있습니다. 두 대기 시간을 모두 비교할 수 있는 경우에는 Application Gateway, Application Gateway와 백 엔드 응용 프로그램 간의 네트워크 또는 백 엔드 응용 프로그램 성능 중 하나가 원인일 수 있습니다.

- **백 엔드 첫 번째 바이트 응답 시간**

  백 엔드 서버에 대 한 연결을 설정 하 고 응답 헤더의 첫 번째 바이트를 수신 하는 시작 사이의 시간 간격 (백 엔드 서버의 처리 시간에 가까운)

- **백 엔드 마지막 바이트 응답 시간**

  백 엔드 서버에 대 한 연결을 설정 하 고 응답 본문의 마지막 바이트를 받는 시작 사이의 시간 간격입니다.
  
*응용 프로그램 게이트웨이 총 시간이* 특정 수신기의 *백 엔드 마지막 바이트 응답 시간* 보다 훨씬 많은 경우 Application Gateway 대기 시간이 긴 것으로 추론할 수 있습니다. 반면에 두 메트릭을 비교할 수 있는 경우 Application Gateway와 백 엔드 응용 프로그램 간의 네트워크 또는 백 엔드 응용 프로그램의 성능 때문에 문제가 될 수 있습니다.

- **백 엔드 연결 시간**

  백 엔드 응용 프로그램과의 연결을 설정 하는 데 걸린 시간입니다. SSL의 경우 핸드셰이크에 소요 된 시간을 포함 합니다. 이 메트릭은 연결 시간을 측정 하 여 다른 대기 시간과 직접 비교할 수 없기 때문에 다른 대기 시간 메트릭과는 다릅니다. 그러나 *백엔드 연결 시간* 패턴을 다른 대기 시간 패턴과 비교 하면 응용 프로그램 gateway 및 백 엔드 응용 프로그램의 변형으로 인해 다른 대기 시간의 증가가 추론 될 수 있는지 여부를 나타낼 수 있습니다. 
  

### <a name="application-gateway-metrics"></a>Application Gateway 메트릭

Application Gateway에는 다음 메트릭이 지원됩니다.

- **받은 바이트 수**

   클라이언트에서 Application Gateway 받은 바이트 수

- **보낸 바이트 수**

   Application Gateway 클라이언트에 보낸 바이트 수

- **클라이언트 TLS 프로토콜**

   Application Gateway 연결 된 클라이언트에서 시작 된 tls 및 비 TLS 요청 수입니다. TLS 프로토콜 배포를 보려면 차원 TLS 프로토콜을 기준으로 필터링 합니다.

- **현재 용량 단위**

   소비 된 용량 단위 개수입니다. 용량 단위는 고정 비용 외에도 요금이 부과 되는 소비 기반 비용을 측정 합니다. 용량 단위 계산 단위, 영구 연결 및 처리량의 세 가지 택배 있습니다. 각 용량 단위는 1 개의 compute 단위 또는 2500 영구 연결 또는 2.22 Mbps 처리량으로 구성 됩니다.

- **현재 계산 단위**

   사용 된 프로세서 용량 개수입니다. 계산 단위에 영향을 주는 요소는 TLS 연결/초, URL 재작성 계산 및 WAF 규칙 처리입니다. 

- **현재 연결**

   Application Gateway와 설정된 현재 연결 수

- **실패한 요청**

   Application Gateway에서 처리 한 실패 한 요청의 수입니다. 각/특정 백 엔드 풀-http 설정 조합의 수를 표시 하도록 요청 수를 추가로 필터링 할 수 있습니다.


- **응답 상태**

   Application Gateway에서 반환 된 HTTP 응답 상태입니다. 2xx, 3xx, 4xx 및 5xx 범주로 응답을 표시하도록 응답 상태 코드 분산을 더욱 세분화할 수 있습니다.

- **처리량**

   Application Gateway에서 제공하는 초당 바이트 수

- **총 요청 수**

   Application Gateway에서 처리 한 성공한 요청 수입니다. 각/특정 백 엔드 풀-http 설정 조합의 수를 표시 하도록 요청 수를 추가로 필터링 할 수 있습니다.

- **웹 응용 프로그램 방화벽 일치 규칙**

- **웹 응용 프로그램 방화벽 트리거 규칙**

### <a name="backend-metrics"></a>백 엔드 메트릭

Application Gateway에는 다음 메트릭이 지원됩니다.

- **백 엔드 응답 상태**

  백 엔드에서 반환 된 HTTP 응답 상태 코드의 수입니다. Application Gateway에서 생성 된 응답 코드는 여기에 포함 되지 않습니다. 2xx, 3xx, 4xx 및 5xx 범주로 응답을 표시하도록 응답 상태 코드 분산을 더욱 세분화할 수 있습니다.

- **정상 호스트 수**

  상태 프로브에서 정상으로 확인 된 백 엔드 수입니다. 특정 백 엔드 풀의 정상/비정상 호스트를 표시하도록 백 엔드 풀 기준으로 필터링할 수 있습니다.

- **비정상 호스트 수**

  상태 프로브에 의해 비정상으로 확인 된 백 엔드의 수입니다. 특정 백 엔드 풀에 비정상 호스트를 표시 하도록 백 엔드 풀 기준으로 필터링 할 수 있습니다.

## <a name="metrics-supported-by-application-gateway-v1-sku"></a>Application Gateway V1 SKU에서 지원 되는 메트릭

### <a name="application-gateway-metrics"></a>Application Gateway 메트릭

Application Gateway에는 다음 메트릭이 지원됩니다.

- **CPU 사용률**

  Application Gateway 할당 된 Cpu의 사용률을 표시 합니다.  정상적인 조건에서 CPU 사용량은 Application Gateway에 호스트 되는 웹 사이트에서 대기 시간이 발생 하 고 클라이언트 환경에 방해가 될 수 있으므로 정기적으로 90%를 초과 해서는 안 됩니다. 인스턴스 수를 늘리거나 더 큰 SKU 크기로 이동 하거나 둘 모두를 수행 하 여 Application Gateway 구성을 수정 하 여 CPU 사용률을 간접적으로 제어 하거나 향상 시킬 수 있습니다.

- **현재 연결**

  Application Gateway와 설정된 현재 연결 수

- **실패한 요청**

  Application Gateway에서 처리 한 실패 한 요청의 수입니다. 각/특정 백 엔드 풀-http 설정 조합의 수를 표시 하도록 요청 수를 추가로 필터링 할 수 있습니다.

- **응답 상태**

  Application Gateway에서 반환 된 HTTP 응답 상태입니다. 2xx, 3xx, 4xx 및 5xx 범주로 응답을 표시하도록 응답 상태 코드 분산을 더욱 세분화할 수 있습니다.

- **처리량**

  Application Gateway에서 제공하는 초당 바이트 수

- **총 요청 수**

  Application Gateway에서 처리 한 성공한 요청 수입니다. 각/특정 백 엔드 풀-http 설정 조합의 수를 표시 하도록 요청 수를 추가로 필터링 할 수 있습니다.

- **웹 응용 프로그램 방화벽 일치 규칙**

- **웹 응용 프로그램 방화벽 트리거 규칙**

### <a name="backend-metrics"></a>백 엔드 메트릭

Application Gateway에는 다음 메트릭이 지원됩니다.

- **정상 호스트 수**

  상태 프로브에서 정상으로 확인 된 백 엔드 수입니다. 특정 백 엔드 풀의 정상/비정상 호스트를 표시하도록 백 엔드 풀 기준으로 필터링할 수 있습니다.

- **비정상 호스트 수**

  상태 프로브에 의해 비정상으로 확인 된 백 엔드의 수입니다. 특정 백 엔드 풀에 비정상 호스트를 표시 하도록 백 엔드 풀 기준으로 필터링 할 수 있습니다.

## <a name="metrics-visualization"></a>메트릭 시각화

Application gateway로 이동 하 고 **모니터링** 에서 **메트릭**을 선택 합니다. 사용 가능한 값을 보려면 **메트릭** 드롭다운 목록을 선택합니다.

다음 이미지에서는 최근 30분 동안 표시된 세 가지 메트릭을 포함한 예제가 표시됩니다.

[![](media/application-gateway-diagnostics/figure5.png "Metric view")](media/application-gateway-diagnostics/figure5-lb.png#lightbox)

현재 지원되는 메트릭 목록을 보려면 [Azure Monitor에서 지원되는 메트릭](../azure-monitor/platform/metrics-supported.md)을 참조하세요.

### <a name="alert-rules-on-metrics"></a>메트릭에 대 한 경고 규칙

리소스에 대한 메트릭을 기반으로 하는 경고 규칙을 시작할 수 있습니다. 예를 들어 애플리케이션 게이트웨이의 처리량이 지정된 기간 동안 임계값보다 높거나 낮거나 같을 때 경고에서 웹후크를 호출하거나 관리자에게 전자 메일을 보낼 수 있습니다.

다음 예제에서는 처리량이 임계값을 위반하면 관리자에게 전자 메일을 보내는 경고 규칙을 만드는 과정을 설명합니다.

1. **메트릭 경고 추가** 를 선택 하 여 **규칙 추가** 페이지를 엽니다. 메트릭 페이지에서이 페이지에 연결할 수도 있습니다.

   !["메트릭 경고 추가" 단추][6]

2. **규칙 추가** 페이지에서 이름, 조건 및 알림 섹션을 입력 하 고 **확인**을 선택 합니다.

   * **조건** 선택기에서 **보다 큼**, **보다 크거나 같음**, **보다 작음**, **보다 작거나 같음**의 네 가지 값 중 하나를 선택합니다.

   * **기간** 선택기에서 5분에서 6시간 사이의 기간까지 선택합니다.

   * **전자 메일 소유자, 참가자 및 읽기 권한자**를 선택하면 해당 리소스에 액세스할 수 있는 사용자에 따라 동적으로 전자 메일을 보낼 수 있습니다. 그렇지 않으면 **추가 관리자 전자 메일** 상자에서 쉼표로 구분된 사용자 목록을 제공할 수도 있습니다.

   ![규칙 추가 페이지][7]

임계값을 위반하면 다음 이미지와 비슷한 전자 메일이 도착합니다.

![위반된 임계값에 대한 전자 메일][8]

메트릭 경고를 만들면 경고 목록이 표시됩니다. 모든 경고 규칙에 대한 개요도 제공됩니다.

![경고 및 규칙 목록][9]

경고 알림에 대한 자세한 내용은 [경고 알림 받기](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)를 참조하세요.

웹후크에 대한 자세한 내용 및 경고와 함께 웹후크를 사용하는 방법을 알아보려면 [Azure 메트릭 경고에 대한 웹후크 구성](../azure-monitor/platform/alerts-webhooks.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Azure Monitor 로그](../azure-monitor/insights/azure-networking-analytics.md)를 사용하여 카운터 및 이벤트 로그를 시각화합니다.
* [Power BI를 사용하여 Azure 활동 로그 시각화](https://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) 블로그 게시물
* [Power BI 등에서 Azure 활동 로그 보기 및 분석](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) 블로그 게시물

[1]: ./media/application-gateway-diagnostics/figure1.png
[2]: ./media/application-gateway-diagnostics/figure2.png
[3]: ./media/application-gateway-diagnostics/figure3.png
[4]: ./media/application-gateway-diagnostics/figure4.png
[5]: ./media/application-gateway-diagnostics/figure5.png
[6]: ./media/application-gateway-diagnostics/figure6.png
[7]: ./media/application-gateway-diagnostics/figure7.png
[8]: ./media/application-gateway-diagnostics/figure8.png
[9]: ./media/application-gateway-diagnostics/figure9.png
[10]: ./media/application-gateway-diagnostics/figure10.png
