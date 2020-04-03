---
title: 애플리케이션 게이트웨이 대량 지원
description: 이 문서에서는 높은 네트워크 트래픽 볼륨 시나리오를 지원 하기 위해 Azure 응용 프로그램 게이트웨이를 구성 하는 지침을 제공 합니다.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 03/24/2020
ms.author: caya
ms.openlocfilehash: 65f404b52a5fc06d8fa5bb5aad291e57fde8caba
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80617195"
---
# <a name="application-gateway-high-traffic-support"></a>Application Gateway 높은 트래픽 지원

>[!NOTE]
> 이 문서에서는 COVID-19 위기로 인해 발생할 수 있는 트래픽량이 많기 때문에 추가 트래픽을 처리하도록 응용 프로그램 게이트웨이를 설정하는 데 도움이 되는 몇 가지 권장 지침을 설명합니다.

WAF(웹 응용 프로그램 방화벽)가 있는 응용 프로그램 게이트웨이를 사용하여 웹 응용 프로그램에 대한 트래픽을 확장 가능하고 안전하게 관리할 수 있습니다.

다음 제안사항을 통해 추가 트래픽을 처리하기 위해 WAF를 사용하여 응용 프로그램 게이트웨이를 설정할 수 있습니다.

## <a name="use-the-v2-sku-over-v1-for-its-autoscaling-capabilities-and-performance-benefits"></a>v2 SKU를 v1 이상 사용하여 자동 크기 조정 기능 및 성능 이점을 제공합니다.
v2 SKU는 트래픽이 증가함에 따라 애플리케이션 게이트웨이를 확장할 수 있도록 자동 크기 조정을 제공합니다. 또한 v1과 비교할 때 5배 향상된 TLS 오프로드 성능, 더 빠른 배포 및 업데이트 시간, 영역 중복성 등과 같은 기타 중요한 성능 이점을 제공합니다. 자세한 내용은 [v2 설명서를](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant)참조하십시오. 

## <a name="set-maximum-instance-count-to-the-maximum-possible-125"></a>최대 인스턴스 수를 최대 인스턴스 수로 설정(125)
 
응용 프로그램 게이트웨이 v2 SKU가 있다고 가정하면 최대 인스턴스 수를 최대 가능한 값 125로 설정하면 필요에 따라 응용 프로그램 게이트웨이를 확장할 수 있습니다. 이렇게 하면 응용 프로그램에 대한 트래픽 증가를 처리할 수 있습니다. 사용하는 용량 단위(CC)에 대해서만 요금이 부과됩니다.  

## <a name="set-your-minimum-instance-count-based-on-your-average-cu-usage"></a>평균 CU 사용량을 기준으로 최소 인스턴스 수 설정

응용 프로그램 게이트웨이 v2 SKU가 있다고 가정하면 자동 크기 조정을 확장하는 데 6~7분이 걸립니다. 최소 인스턴스 수가 많을수록 트래픽이 급증할 수록 자동 크기 조정 작업이 필요하지 않으므로 로드가 증가할 때 Application Gateway가 트래픽을 더 잘 처리할 수 있습니다.  

## <a name="alert-if-a-certain-metric-surpasses-75-of-average-cu-utilization"></a>특정 메트릭이 평균 CU 사용률의 75%를 초과하는 경우 경고 
메트릭 및 기타 연습에 대한 자세한 설명은 [응용 프로그램 게이트웨이 메트릭 설명서를](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#metrics-visualization) 참조하십시오. 

### <a name="example-setting-up-an-alert-on-75-of-average-cu-usage"></a>예: 평균 CU 사용량의 75%에 대한 경고 설정

이 예제에서는 Azure 포털을 사용하여 평균 CU 사용량의 75%에 도달할 때 경고를 설정하는 방법을 보여 주며, 이 예제에서는 경고를 설정하는 방법을 보여 주며, 이 예제에서는 Azure Portal을 사용하는 방법을 보여 주며, 이 예제에서는 평균 CU 사용량의 75%에 도달할 때 경고를 설정하는 방법을 보여 주며, 이 예제에서는 Azure Portal을 사용하는 방법을 보여 주며, 이 에 
1. **응용 프로그램 게이트웨이로**이동합니다.
2. 왼쪽 패널에서 **모니터링** 탭 에서 **메트릭을 선택합니다.** 
3. 평균 현재 **계산 단위에 대한 메트릭을 추가합니다.** 
![WAF 메트릭 설정](./media/application-gateway-covid-guidelines/waf-setup-metrics.png)
4. 최소 인스턴스 수를 평균 CU 사용량으로 설정한 경우 최소 인스턴스의 75%가 사용 중일 때 경고를 설정합니다. 예를 들어 평균 사용량이 10개의 CPU인 경우 7.5C에 경고를 설정합니다. 이렇게 하면 사용량이 증가하는 경우 이를 알리고 응답할 시간을 제공합니다. 이 트래픽이 지속될 것으로 생각되면 트래픽이 증가할 수 있음을 알리기 위해 최소한을 올릴 수 있습니다. 
![WAF 경고 설정](./media/application-gateway-covid-guidelines/waf-setup-monitoring-alert.png)

> [!NOTE]
> 잠재적인 트래픽 급증에 얼마나 민감하든 에 따라 CU 사용률이 낮거나 더 높은 경우 경고가 발생하도록 설정할 수 있습니다.

## <a name="set-up-waf-with-geofiltering-and-bot-protection-to-stop-attacks"></a>공격을 막기 위해 지오 필터링 및 봇 보호 기능을 갖춘 WAF 설정
응용 프로그램 앞에 추가 보안 계층을 원하는 경우 WAF 기능에 대한 응용 프로그램 게이트웨이 WAF_v2 SKU를 사용합니다. 특정 국가 또는 국가에서만 응용 프로그램에 대한 액세스를 허용하도록 v2 SKU를 구성할 수 있습니다. 지리적 위치에 따라 트래픽을 명시적으로 허용하거나 차단하도록 WAF 사용자 지정 규칙을 설정합니다. 자세한 내용은 [사용자 지정 규칙 및](https://docs.microsoft.com/azure/web-application-firewall/ag/geomatch-custom-rules) [PowerShell을 통해 응용 프로그램 게이트웨이 WAF_v2 SKU에서 사용자 지정 규칙을 구성하는 방법을](https://docs.microsoft.com/azure/web-application-firewall/ag/configure-waf-custom-rules)참조하세요.

봇 보호를 사용하여 알려진 악성 봇을 차단합니다. 이렇게 하면 응용 프로그램에 대한 트래픽의 양이 줄어듭니다. 자세한 내용은 [설정 지침이 있는 봇 보호를](https://docs.microsoft.com/azure/web-application-firewall/ag/configure-waf-custom-rules)참조하십시오.

## <a name="turn-on-diagnostics-on-application-gateway-and-waf"></a>응용 프로그램 게이트웨이 및 WAF에서 진단 기능을 켜십시오.

진단 로그를 사용하면 방화벽 로그, 성능 로그 및 액세스 로그를 볼 수 있습니다. Azure에서 이러한 로그를 사용하여 응용 프로그램 게이트웨이를 관리하고 해결할 수 있습니다. 자세한 내용은 진단 [설명서를](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#diagnostic-logging)참조하십시오. 

## <a name="set-up-an-tls-policy-for-extra-security"></a>추가 보안을 위한 TLS 정책 설정
최신 TLS 정책[버전(AppGwSlPolicy20170401S)을](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-policy-overview#appgwsslpolicy20170401s)사용하고 있는지 확인합니다. 이렇게 하면 TLS 1.2와 더 강력한 암호가 적용됩니다. 자세한 내용은 [PowerShell을 통해 TLS 정책 버전 및 암호 제품군 구성을](https://docs.microsoft.com/azure/application-gateway/application-gateway-configure-ssl-policy-powershell)참조하십시오.
