---
title: Application Gateway 높은 트래픽 볼륨 지원
description: 이 문서에서는 높은 네트워크 트래픽 볼륨 시나리오를 지원 하도록 Azure 애플리케이션 Gateway를 구성 하는 지침을 제공 합니다.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 03/24/2020
ms.author: caya
ms.openlocfilehash: 65f404b52a5fc06d8fa5bb5aad291e57fde8caba
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80617195"
---
# <a name="application-gateway-high-traffic-support"></a>Application Gateway 높은 트래픽 지원

>[!NOTE]
> 이 문서에서는 COVID-19 위기 때문에 발생할 수 있는 높은 트래픽 볼륨으로 인해 추가 트래픽을 처리 하도록 Application Gateway를 설정 하는 데 도움이 되는 몇 가지 권장 지침을 설명 합니다.

웹 응용 프로그램에 대 한 트래픽을 관리 하는 확장 가능 하 고 안전한 방법으로 WAF (웹 응용 프로그램 방화벽)와 함께 Application Gateway를 사용할 수 있습니다.

다음 제안 사항은 추가 트래픽을 처리 하도록 WAF를 사용 하 여 Application Gateway를 설정 하는 데 도움이 됩니다.

## <a name="use-the-v2-sku-over-v1-for-its-autoscaling-capabilities-and-performance-benefits"></a>자동 크기 조정 기능 및 성능 혜택을 위해 v1에서 v2 SKU 사용
V2 SKU는 트래픽이 늘어남에 따라 Application Gateway 확장할 수 있도록 자동 크기 조정을 제공 합니다. 또한 v1과 비교 했을 때 5 배 더 나은 TLS 오프 로드 성능, 더 빠른 배포 및 업데이트 시간, 영역 중복성 등의 다른 중요 한 성능상의 이점을 제공 합니다. 자세한 내용은 [v2 설명서](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant)를 참조 하세요. 

## <a name="set-maximum-instance-count-to-the-maximum-possible-125"></a>최대 인스턴스 수를 최대 가능 (125)으로 설정 합니다.
 
Application Gateway v2 SKU를 사용 하는 경우 최대 인스턴스 수를 125의 최대 값으로 설정 하면 Application Gateway 필요에 따라 규모를 확장할 수 있습니다. 이렇게 하면 응용 프로그램에 대 한 트래픽 증가를 처리할 수 있습니다. 사용한 CUs (용량 단위)에 대해서만 요금이 부과 됩니다.  

## <a name="set-your-minimum-instance-count-based-on-your-average-cu-usage"></a>평균 CU 사용량을 기준으로 최소 인스턴스 수를 설정 합니다.

Application Gateway v2 SKU를 사용 하는 경우 자동 크기 조정을 확장 하는 데 6 ~ 7 분이 걸립니다. 트래픽 급증 시 자동 크기 조정 작업이 필요 하지 않기 때문에 최소 인스턴스 수가 많을 수록 부하가 증가 하는 경우에는 Application Gateway에서 트래픽을 더 잘 처리할 수 있습니다.  

## <a name="alert-if-a-certain-metric-surpasses-75-of-average-cu-utilization"></a>특정 메트릭이 평균 CU 사용률의 75%를 초과 경고 
메트릭 및 기타 연습에 대 한 자세한 설명은 [Application Gateway 메트릭 설명서](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#metrics-visualization) 를 참조 하세요. 

### <a name="example-setting-up-an-alert-on-75-of-average-cu-usage"></a>예: 평균 CU 사용의 75%에 대 한 경고 설정

이 예제에서는 평균 CU 사용의 75%에 도달 했을 때 Azure Portal를 사용 하 여 경고를 설정 하는 방법을 보여 줍니다. 
1. **Application Gateway**로 이동 합니다.
2. 왼쪽 패널의 **모니터링** 탭에서 **메트릭** 을 선택 합니다. 
3. **평균 현재 계산 단위**에 대 한 메트릭을 추가 합니다. 
![WAF 메트릭 설정](./media/application-gateway-covid-guidelines/waf-setup-metrics.png)
4. 최소 인스턴스 수를 평균 CU 사용으로 설정한 경우 계속 진행 하 여 최소 인스턴스 중 75%가 사용 중일 때 경고를 설정 합니다. 예를 들어 평균 사용량이 10 CUs 인 경우 7.5 CUs에 대 한 경고를 설정 합니다. 이는 사용량이 늘어나고 응답 하는 시간을 제공 하는 경우 경고를 표시 합니다. 트래픽이 증가 하는 것을 경고 하기 위해이 트래픽이 지속 되는 것으로 판단 되는 경우 최소값을 높일 수 있습니다. 
![WAF 경고 설정](./media/application-gateway-covid-guidelines/waf-setup-monitoring-alert.png)

> [!NOTE]
> 잠재적인 트래픽 급증에 대 한 중요 한 정도에 따라 낮은 또는 더 높은 CU 사용률에서 발생 하도록 경고를 설정할 수 있습니다.

## <a name="set-up-waf-with-geofiltering-and-bot-protection-to-stop-attacks"></a>Geofiltering을 사용 하 여 WAF를 설정 하 고 공격을 중지 하려면 bot 보호
응용 프로그램 앞에 추가 보안 계층이 필요한 경우 WAF 기능을 위해 Application Gateway WAF_v2 SKU를 사용 합니다. 지정 된 국가 또는 국가에서 응용 프로그램에 대 한 액세스만 허용 하도록 v2 SKU를 구성할 수 있습니다. 지리적 위치에 따라 트래픽을 명시적으로 허용 하거나 차단 하도록 WAF 사용자 지정 규칙을 설정 합니다. 자세한 내용은 [geofiltering 사용자 지정 규칙](https://docs.microsoft.com/azure/web-application-firewall/ag/geomatch-custom-rules) 및 [PowerShell을 통해 Application Gateway WAF_v2 SKU에서 사용자 지정 규칙을 구성 하는 방법](https://docs.microsoft.com/azure/web-application-firewall/ag/configure-waf-custom-rules)을 참조 하세요.

Bot 방지를 사용 하 여 알려진 잘못 된 봇을 차단 합니다. 이렇게 하면 응용 프로그램에 도달 하는 트래픽 양이 줄어듭니다. 자세한 내용은 [설정 지침을 포함 하는 bot 보호](https://docs.microsoft.com/azure/web-application-firewall/ag/configure-waf-custom-rules)를 참조 하세요.

## <a name="turn-on-diagnostics-on-application-gateway-and-waf"></a>Application Gateway 및 WAF에서 진단 설정

진단 로그를 사용 하 여 방화벽 로그, 성능 로그 및 액세스 로그를 볼 수 있습니다. Azure에서 이러한 로그를 사용 하 여 응용 프로그램 게이트웨이를 관리 하 고 문제를 해결할 수 있습니다. 자세한 내용은 [진단 설명서](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#diagnostic-logging)를 참조 하세요. 

## <a name="set-up-an-tls-policy-for-extra-security"></a>추가 보안을 위해 TLS 정책 설정
최신 TLS 정책 버전 ([AppGwSslPolicy20170401S](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-policy-overview#appgwsslpolicy20170401s))을 사용 하 고 있는지 확인 합니다. 이를 통해 TLS 1.2 및 더 강력한 암호화가 적용 됩니다. 자세한 내용은 [PowerShell을 통해 TLS 정책 버전 및 암호 그룹 구성](https://docs.microsoft.com/azure/application-gateway/application-gateway-configure-ssl-policy-powershell)을 참조 하세요.
