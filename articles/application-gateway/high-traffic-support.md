---
title: Application Gateway 높은 트래픽 볼륨 지원
description: 이 문서에서는 높은 네트워크 트래픽 볼륨 시나리오를 지원하도록 Azure Application Gateway를 구성하기 위한 지침을 제공합니다.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: caya
ms.openlocfilehash: 48730d03e9a578fb26b691577fa033e5f7bb4d19
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397487"
---
# <a name="application-gateway-high-traffic-support"></a>Application Gateway 높은 트래픽 지원

>[!NOTE]
> 이 문서에서는 COVID-19 위기 때문에 발생할 수 있는 높은 트래픽 볼륨으로 인해 추가 트래픽을 처리하도록 Application Gateway를 설정하는 데 도움이 되는 몇 가지 권장 지침을 설명합니다.

웹 애플리케이션에 대한 트래픽을 관리하는 확장 가능하고 안전한 방법으로 WAF(웹 애플리케이션 방화벽)에서 Application Gateway를 사용할 수 있습니다.

다음 제안 사항은 추가 트래픽을 처리하도록 WAF를 사용하여 Application Gateway를 설정하는 데 유용합니다.

## <a name="use-the-v2-sku-over-v1-for-its-autoscaling-capabilities-and-performance-benefits"></a>자동 크기 조정 기능 및 성능 혜택을 위해 v1에서 v2 SKU 사용
v2 SKU는 트래픽이 늘어남에 따라 Application Gateway 확장할 수 있도록 자동 크기 조정을 제공합니다. 또한 v1과 비교할 때 5배 더 나은 TLS 오프로드 성능, 더 빠른 배포 및 업데이트 시간, 영역 중복성 등의 기타 중요한 성능상의 이점을 제공합니다. 자세한 내용은 [v2 설명서](./application-gateway-autoscaling-zone-redundant.md)를 참조하세요. 

## <a name="set-maximum-instance-count-to-the-maximum-possible-125"></a>최대 인스턴스 수를 가능한 최대 수(125)로 설정
 
Application Gateway v2 SKU를 사용하는 경우 최대 인스턴스 수를 가능한 최대 수인 125로 설정하면 Application Gateway에서 필요에 따라 스케일 아웃할 수 있습니다. 이렇게 하면 애플리케이션에 대한 트래픽 증가를 처리할 수 있습니다. 사용한 CU(용량 단위)에 대해서만 요금이 부과됩니다.  

## <a name="set-your-minimum-instance-count-based-on-your-average-cu-usage"></a>평균 CU 사용량을 기준으로 최소 인스턴스 수 설정

Application Gateway v2 SKU를 사용하는 경우 자동 크기 조정 기능에 따라 스케일 아웃하는 데 6~7분이 걸립니다. 트래픽 급증 시 자동 크기 조정 작업이 필요하지 않기 때문에 최소 인스턴스 수가 많을 경우 부하가 증가할 때 Application Gateway에서 트래픽을 더 잘 처리할 수 있습니다.  

## <a name="alert-if-a-certain-metric-surpasses-75-of-average-cu-utilization"></a>특정 메트릭이 평균 CU 사용률의 75%를 초과하는 경우 경고 
메트릭 및 기타 연습에 대한 자세한 설명은 [Application Gateway 메트릭 설명서](./application-gateway-metrics.md#metrics-visualization)를 참조하세요. 

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

## <a name="set-up-waf-with-geofiltering-and-bot-protection-to-stop-attacks"></a>지역 필터링 및 봇 보호로 공격을 중지하도록 WAF 설정
애플리케이션 앞에 추가 보안 계층이 필요한 경우 WAF용 Application Gateway WAF_v2 SKU 기능을 사용합니다. 지정된 국가/지역에서만 애플리케이션에 대한 액세스를 허용하도록 v2 SKU를 구성할 수 있습니다. 지리적 위치에 따라 트래픽을 명시적으로 허용하거나 차단하도록 WAF 사용자 지정 규칙을 설정합니다. 자세한 내용은 [지역 필터링 사용자 지정 규칙](../web-application-firewall/ag/geomatch-custom-rules.md) 및 [PowerShell을 통해 Application Gateway WAF_v2 SKU에서 사용자 지정 규칙을 구성하는 방법](../web-application-firewall/ag/configure-waf-custom-rules.md)을 참조하세요.

봇 방지를 사용하도록 설정하여 알려진 잘못된 봇을 차단합니다. 이렇게 하면 애플리케이션에 도달하는 트래픽 양이 줄어듭니다. 자세한 내용은 [설정에 따른 봇 보호 지침](../web-application-firewall/ag/configure-waf-custom-rules.md)을 참조하세요.

## <a name="turn-on-diagnostics-on-application-gateway-and-waf"></a>Application Gateway 및 WAF에서 진단 켜기

진단 로그를 사용하여 방화벽 로그, 성능 로그 및 액세스 로그를 볼 수 있습니다. Azure에서 이러한 로그를 사용하여 Application Gateway를 관리하고 문제를 해결할 수 있습니다. 자세한 내용은 [진단 설명서](./application-gateway-diagnostics.md#diagnostic-logging)를 참조하세요. 

## <a name="set-up-an-tls-policy-for-extra-security"></a>추가 보안을 위해 TLS 정책 설정
최신 TLS 정책 버전([AppGwSslPolicy20170401S](./application-gateway-ssl-policy-overview.md#appgwsslpolicy20170401s))을 사용하고 있는지 확인합니다. 이를 통해 TLS 1.2 이상의 강력한 암호가 적용됩니다. 자세한 내용은 [PowerShell을 통해 TLS 정책 버전 및 암호 그룹 구성](./application-gateway-configure-ssl-policy-powershell.md)을 참조하세요.