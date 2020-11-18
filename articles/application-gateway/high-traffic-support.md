---
title: Application Gateway 높은 트래픽 볼륨 지원
description: 이 문서에서는 높은 네트워크 트래픽 볼륨 시나리오를 지원하도록 Azure Application Gateway를 구성하기 위한 지침을 제공합니다.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: caya
ms.openlocfilehash: 3854e7f3c19f1724a2df1508c9fa519809e07ba9
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658675"
---
# <a name="application-gateway-high-traffic-support"></a>Application Gateway 높은 트래픽 지원

>[!NOTE]
> 이 문서에서는 발생할 수 있는 높은 트래픽 볼륨에 대 한 추가 트래픽을 처리 하도록 Application Gateway를 설정 하는 데 도움이 되는 몇 가지 권장 지침을 설명 합니다. 경고 임계값은 전적으로 제안 사항이 며 본질적으로 제네릭인 것입니다. 사용자는 워크 로드 및 사용률 기대치에 따라 경고 임계값을 결정할 수 있습니다.

웹 애플리케이션에 대한 트래픽을 관리하는 확장 가능하고 안전한 방법으로 WAF(웹 애플리케이션 방화벽)에서 Application Gateway를 사용할 수 있습니다.

트래픽 증가 또는 급증에 대비 하 여 QoS에 포함 될 수 있는 영향을 최소화 하도록 트래픽 및 버퍼에 따라 Application Gateway 크기를 조정 하는 것이 중요 합니다. 다음 제안 사항은 추가 트래픽을 처리하도록 WAF를 사용하여 Application Gateway를 설정하는 데 유용합니다.

Application Gateway에서 제공 하는 메트릭의 전체 목록은 [메트릭 설명서](./application-gateway-metrics.md) 를 확인 하세요. 메트릭에 대 한 경고를 설정 하는 방법에 대 한 자세한 내용은 Azure Portal [메트릭 시각화](./application-gateway-metrics.md#metrics-visualization) 및 [Azure monitor 설명서](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric) 를 참조 하세요.

## <a name="scaling-for-application-gateway-v1-sku-standardwaf-sku"></a>Application Gateway v1 SKU에 대 한 크기 조정 (표준/WAF SKU)

### <a name="set-your-instance-count-based-on-your-peak-cpu-usage"></a>최고 CPU 사용량을 기준으로 인스턴스 수를 설정 합니다.
V1 SKU 게이트웨이를 사용 하는 경우 크기를 조정 하기 위해 Application Gateway 최대 32 인스턴스로 설정할 수 있습니다. 80% 이상의 급증에 대해 지난 한 달 동안 Application Gateway의 CPU 사용률을 확인 합니다 .이를 모니터링 하는 메트릭으로 사용할 수 있습니다. 트래픽 급증을 고려 하 여 최대 사용량에 따라 인스턴스 수를 설정 하 고 10%에서 20%까지 추가 버퍼를 설정 하는 것이 좋습니다.

### <a name="use-the-v2-sku-over-v1-for-its-autoscaling-capabilities-and-performance-benefits"></a>자동 크기 조정 기능 및 성능 혜택을 위해 v1에서 v2 SKU 사용
v2 SKU는 트래픽이 늘어남에 따라 Application Gateway 확장할 수 있도록 자동 크기 조정을 제공합니다. 또한 v1과 비교할 때 5배 더 나은 TLS 오프로드 성능, 더 빠른 배포 및 업데이트 시간, 영역 중복성 등의 기타 중요한 성능상의 이점을 제공합니다. 자세한 내용은 [v2 설명서](./application-gateway-autoscaling-zone-redundant.md) 를 참조 하 고 v1에서 v2로 [마이그레이션 설명서](./migrate-v1-v2.md) 를 참조 하 여 기존 v1 SKU 게이트웨이를 v2 sku로 마이그레이션하는 방법에 대해 알아보세요. 

## <a name="autoscaling-for-application-gateway-v2-sku-standard_v2waf_v2-sku"></a>Application Gateway v2 SKU에 대 한 자동 크기 조정 (Standard_v2/WAF_v2 SKU)

### <a name="set-maximum-instance-count-to-the-maximum-possible-125"></a>최대 인스턴스 수를 가능한 최대 수(125)로 설정
 
Application Gateway v2 SKU의 경우 최대 인스턴스 수를 125의 최대 값으로 설정 하면 Application Gateway 필요에 따라 규모를 확장할 수 있습니다. 이렇게 하면 애플리케이션에 대한 트래픽 증가를 처리할 수 있습니다. 사용한 CU(용량 단위)에 대해서만 요금이 부과됩니다. 

서브넷의 서브넷 크기와 사용 가능한 IP 주소 수를 확인 하 고이에 따라 최대 인스턴스 수를 설정 해야 합니다. 서브넷에 수용할 공간이 충분 하지 않은 경우에는 충분 한 용량이 있는 동일한 서브넷 이나 다른 서브넷에 게이트웨이를 다시 만들어야 합니다. 

### <a name="set-your-minimum-instance-count-based-on-your-average-compute-unit-usage"></a>평균 계산 단위 사용량을 기준으로 최소 인스턴스 수를 설정 합니다.

Application Gateway v2 SKU의 경우 자동 크기 조정을 통해 트래픽을 확장 하 고 추가 인스턴스 집합을 프로 비전 하는 데 사용할 수 있는 추가 인스턴스 집합을 프로 비전 합니다. 그때까지 트래픽이 급증 하는 경우에는 기존 게이트웨이 인스턴스가 스트레스를 받을 수 있으며이로 인해 예기치 않은 대기 시간 또는 트래픽의 손실이 발생할 수 있습니다. 

최소 인스턴스 수를 최적 수준으로 설정 하는 것이 좋습니다. 예를 들어 최대 부하에서 트래픽을 처리 하는 데 50 인스턴스가 필요한 경우 최소 25를 30으로 설정 하는 것이 <10이 아닌 적절 한 개념입니다 .이는 트래픽 급증이 발생 하는 경우에도 Application Gateway를 처리 하 고 자동 크기 조정을 사용 하 여 응답할 수 있는 충분 한 시간을 제공 합니다.

지난 1 개월 동안 계산 단위 메트릭을 확인 합니다. Compute unit 메트릭은 게이트웨이의 CPU 사용률을 표시 하 고 최고 사용량을 10으로 나눈 값을 기준으로 필요한 최소 인스턴스 수를 설정할 수 있습니다. 1 개 응용 프로그램 게이트웨이 인스턴스는 최소 10 개의 compute 단위를 처리할 수 있습니다.

## <a name="manual-scaling-for-application-gateway-v2-sku-standard_v2waf_v2"></a>Application Gateway v2 SKU에 대 한 수동 크기 조정 (Standard_v2/WAF_v2)

### <a name="set-your-instance-count-based-on-your-peak-compute-unit-usage"></a>최고 계산 단위 사용량을 기준으로 인스턴스 수를 설정 합니다. 

자동 크기 조정과 달리 수동 크기 조정에서는 트래픽 요구 사항에 따라 응용 프로그램 게이트웨이의 인스턴스 수를 수동으로 설정 해야 합니다. 트래픽 급증을 고려 하 여 최대 사용량에 따라 인스턴스 수를 설정 하 고 10%에서 20%까지 추가 버퍼를 설정 하는 것이 좋습니다. 예를 들어 트래픽이 최대 50 인스턴스를 필요로 하는 경우 60 인스턴스에 55를 프로 비전 하 여 발생할 수 있는 예기치 않은 트래픽 급증을 처리 합니다.

지난 1 개월 동안 계산 단위 메트릭을 확인 합니다. Compute unit 메트릭은 게이트웨이의 CPU 사용률을 표시 하 고 최고 사용량을 10으로 나눈 값을 기반으로 하며, 1 개 응용 프로그램 게이트웨이 인스턴스가 최소 10 개의 compute 단위를 처리할 수 있으므로 필요한 인스턴스 수를 설정할 수 있습니다.

## <a name="monitoring-and-alerting"></a>모니터링 및 경고

트래픽 또는 사용 변칙에 대 한 알림을 받으려면 특정 메트릭에 대 한 경고를 설정할 수 있습니다. Application Gateway에서 제공 하는 메트릭의 전체 목록은 [메트릭 설명서](./application-gateway-metrics.md) 를 참조 하세요. 메트릭에 대 한 경고를 설정 하는 방법에 대 한 자세한 내용은 Azure Portal [메트릭 시각화](./application-gateway-metrics.md#metrics-visualization) 및 [Azure monitor 설명서](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric) 를 참조 하세요.

## <a name="alerts-for-application-gateway-v1-sku-standardwaf"></a>Application Gateway v1 SKU에 대 한 경고 (표준/WAF)

### <a name="alert-if-average-cpu-utilization-crosses-80"></a>평균 CPU 사용률이 80%를 초과 하는 경우 경고

정상적인 조건에서 CPU 사용량은 Application Gateway 뒤에 호스트되는 웹 사이트에서 대기 시간이 발생하고 클라이언트 경험에 방해가 될 수 있으므로 일반적으로 90%를 초과해서는 안 됩니다. 인스턴스 수를 늘리거나 더 큰 SKU 크기로 이동 하거나 둘 모두를 수행 하 여 Application Gateway 구성을 수정 하 여 CPU 사용률을 간접적으로 제어 하거나 개선할 수 있습니다. CPU 사용률 메트릭이 80% 평균을 초과 하는 경우 경고를 설정 합니다.

### <a name="alert-if-unhealthy-host-count-crosses-threshold"></a>비정상 호스트 수가 임계값을 초과 하는 경우 경고

이 메트릭은 application gateway에서 검색할 수 없는 백 엔드 서버 수를 나타냅니다. 이렇게 하면 Application gateway 인스턴스가 백 엔드에 연결할 수 없는 문제를 파악할 수 있습니다. 이 수가 백 엔드 용량의 20%를 초과 하면 경고를 표시 합니다. 예: 현재 백 엔드 풀에 30 백 엔드 서버가 있는 경우 비정상 호스트 수가 6 개를 초과 하면 경고를 설정 합니다.

### <a name="alert-if-response-status-4xx-5xx-crosses-threshold"></a>응답 상태 (4xx, 5xx)가 임계값을 교차 하는 경우 경고 

Application Gateway 응답 상태가 4xx 또는 5xx 인 경우 경고를 만듭니다. 일시적인 문제 때문에 가끔 4xx 또는 5xx 응답이 표시 될 수 있습니다. 프로덕션에서 게이트웨이를 관찰 하 여 정적 임계값을 확인 하거나 경고에 대해 동적 임계값을 사용 해야 합니다.

### <a name="alert-if-failed-requests-crosses-threshold"></a>실패 한 요청이 교차 임계값을 초과 하는 경우 경고 

실패 한 요청 메트릭이 임계값을 교차 하는 경우 경고를 만듭니다. 프로덕션에서 게이트웨이를 관찰 하 여 정적 임계값을 확인 하거나 경고에 대해 동적 임계값을 사용 해야 합니다.

## <a name="alerts-for-application-gateway-v2-sku-standard_v2waf_v2"></a>Application Gateway v2 SKU에 대 한 경고 (Standard_v2/WAF_v2)

### <a name="alert-if-compute-unit-utilization-crosses-75-of-average-usage"></a>계산 단위 사용률이 평균 사용량의 75%를 초과 하는 경우 경고 

Compute unit은 Application Gateway 계산 사용률을 측정 한 것입니다. 지난 1 개월 동안의 평균 계산 단위 사용량을 확인 하 고 75%를 초과 하는 경우 경고를 설정 합니다. 예를 들어 평균 사용량이 10 compute 단위인 경우 7.5 CUs에 경고를 설정 합니다. 사용량이 늘어나면 경고하여 사용자에게 대응할 시간을 줍니다. 트래픽이 증가할 수 있음을 경고하기 위해 이 트래픽이 지속되는 것으로 판단되는 경우 최솟값을 늘릴 수 있습니다. 위의 크기 조정 제안에 따라 필요에 따라 규모를 확장 합니다.

### <a name="example-setting-up-an-alert-on-75-of-average-cu-usage"></a>예제: 평균 CU 사용량 75%에 대해 경고 설정

이 예제에서는 평균 CU 사용량의 75%에 도달할 때 Azure Portal을 사용하여 경고를 설정하는 방법을 보여 줍니다. 
1. **Application Gateway** 로 이동합니다.
2. 왼쪽 패널의 **모니터링** 탭에서 **메트릭** 을 선택합니다. 
3. **평균 현재 컴퓨팅 단위** 에 대한 메트릭을 추가합니다. 
![WAF 메트릭 설정](./media/application-gateway-covid-guidelines/waf-setup-metrics.png)
4. 최소 인스턴스 수를 평균 CU 사용량으로 설정한 경우 계속 진행하여 최소 인스턴스 중 75%가 사용 중일 때 경고를 설정합니다. 예를 들어 평균 사용량이 10 CU인 경우 7.5CU에 대한 경고를 설정합니다. 사용량이 늘어나면 경고하여 사용자에게 대응할 시간을 줍니다. 트래픽이 증가할 수 있음을 경고하기 위해 이 트래픽이 지속되는 것으로 판단되는 경우 최솟값을 늘릴 수 있습니다. 
![WAF 경고 설정](./media/application-gateway-covid-guidelines/waf-setup-monitoring-alert.png)

> [!NOTE]
> 잠재적인 트래픽 급증에 대한 심각도에 따라 더 낮거나 더 높은 CU 사용률에서 경고가 발생하도록 설정할 수 있습니다.

### <a name="alert-if-capacity-unit-utilization-crosses-75-of-peak-usage"></a>용량 단위 사용률이 최고 사용량의 75%를 초과 하는 경우 경고 

용량 단위는 처리량, 계산 및 연결 수 측면에서 전체 게이트웨이 사용률을 나타냅니다. 지난 한 달의 최대 용량 단위 사용량을 확인 하 고 75%를 교차 하는 경우 경고를 설정 합니다. 예를 들어 최대 사용량이 100 용량 단위인 경우 75 CUs에 대 한 경고를 설정 합니다. 필요한 경우 위의 두 제안에 따라 규모를 확장 합니다.

### <a name="alert-if-unhealthy-host-count-crosses-threshold"></a>비정상 호스트 수가 임계값을 초과 하는 경우 경고 

이 메트릭은 application gateway에서 검색할 수 없는 백 엔드 서버 수를 나타냅니다. 이렇게 하면 Application gateway 인스턴스가 백 엔드에 연결할 수 없는 문제를 파악할 수 있습니다. 이 수가 백 엔드 용량의 20%를 초과 하면 경고를 표시 합니다. 예: 현재 백 엔드 풀에 30 백 엔드 서버가 있는 경우 비정상 호스트 수가 6 개를 초과 하면 경고를 설정 합니다.

### <a name="alert-if-response-status-4xx-5xx-crosses-threshold"></a>응답 상태 (4xx, 5xx)가 임계값을 교차 하는 경우 경고 

Application Gateway 응답 상태가 4xx 또는 5xx 인 경우 경고를 만듭니다. 일시적인 문제 때문에 가끔 4xx 또는 5xx 응답이 표시 될 수 있습니다. 프로덕션에서 게이트웨이를 관찰 하 여 정적 임계값을 확인 하거나 경고에 대해 동적 임계값을 사용 해야 합니다.

### <a name="alert-if-failed-requests-crosses-threshold"></a>실패 한 요청이 교차 임계값을 초과 하는 경우 경고 

실패 한 요청 메트릭이 임계값을 교차 하는 경우 경고를 만듭니다. 프로덕션에서 게이트웨이를 관찰 하 여 정적 임계값을 확인 하거나 경고에 대해 동적 임계값을 사용 해야 합니다.

### <a name="alert-if-backend-last-byte-response-time-crosses-threshold"></a>백 엔드 마지막 바이트 응답 시간이 임계값을 초과 하는 경우 경고 

이 메트릭은 백 엔드 서버에 대 한 연결을 설정 하 고 응답 본문의 마지막 바이트를 받는 시작 사이의 시간 간격을 나타냅니다. 백 엔드 응답 대기 시간이 일반적인 특정 임계값 보다 더 많은 경우 경고를 만듭니다. 예를 들어 백 엔드 응답 대기 시간이 일반 값의 30% 이상 증가 하는 경우 경고를 표시 하도록 설정 합니다.

### <a name="alert-if-application-gateway-total-time-crosses-threshold"></a>Application Gateway 총 시간이 임계값을 초과 하는 경우 경고

이는 Application Gateway 마지막 응답 바이트가 클라이언트로 전송 된 시간에 대 한 HTTP 요청의 첫 번째 바이트를 수신 하는 시간 간격입니다. 백 엔드 응답 대기 시간이 일반적인 특정 임계값 보다 더 많은 경우에서 경고를 생성 해야 합니다. 예를 들어이 값을 설정 하 여 총 시간 대기 시간이 일반 값의 30% 이상 증가 하는 경우 경고가 표시 되도록 설정할 수 있습니다.

## <a name="set-up-waf-with-geofiltering-and-bot-protection-to-stop-attacks"></a>지역 필터링 및 봇 보호로 공격을 중지하도록 WAF 설정
애플리케이션 앞에 추가 보안 계층이 필요한 경우 WAF용 Application Gateway WAF_v2 SKU 기능을 사용합니다. 지정된 국가/지역에서만 애플리케이션에 대한 액세스를 허용하도록 v2 SKU를 구성할 수 있습니다. 지리적 위치에 따라 트래픽을 명시적으로 허용하거나 차단하도록 WAF 사용자 지정 규칙을 설정합니다. 자세한 내용은 [지역 필터링 사용자 지정 규칙](../web-application-firewall/ag/geomatch-custom-rules.md) 및 [PowerShell을 통해 Application Gateway WAF_v2 SKU에서 사용자 지정 규칙을 구성하는 방법](../web-application-firewall/ag/configure-waf-custom-rules.md)을 참조하세요.

봇 방지를 사용하도록 설정하여 알려진 잘못된 봇을 차단합니다. 이렇게 하면 애플리케이션에 도달하는 트래픽 양이 줄어듭니다. 자세한 내용은 [설정에 따른 봇 보호 지침](../web-application-firewall/ag/configure-waf-custom-rules.md)을 참조하세요.

## <a name="turn-on-diagnostics-on-application-gateway-and-waf"></a>Application Gateway 및 WAF에서 진단 켜기

진단 로그를 사용하여 방화벽 로그, 성능 로그 및 액세스 로그를 볼 수 있습니다. Azure에서 이러한 로그를 사용하여 Application Gateway를 관리하고 문제를 해결할 수 있습니다. 자세한 내용은 [진단 설명서](./application-gateway-diagnostics.md#diagnostic-logging)를 참조하세요. 

## <a name="set-up-an-tls-policy-for-extra-security"></a>추가 보안을 위해 TLS 정책 설정
최신 TLS 정책 버전([AppGwSslPolicy20170401S](./application-gateway-ssl-policy-overview.md#appgwsslpolicy20170401s))을 사용하고 있는지 확인합니다. 이를 통해 TLS 1.2 이상의 강력한 암호가 적용됩니다. 자세한 내용은 [PowerShell을 통해 TLS 정책 버전 및 암호 그룹 구성](./application-gateway-configure-ssl-policy-powershell.md)을 참조하세요.