---
title: Azure 웹 응용 프로그램 방화벽과 함께 Azure 센티널 사용
description: 이 문서에서는 azure WAF (웹 응용 프로그램 방화벽)에서 Azure 센티널를 사용 하는 방법을 보여 줍니다.
services: web-application-firewall
author: TreMansdoerfer
ms.service: web-application-firewall
ms.date: 10/12/2020
ms.author: victorh
ms.topic: how-to
ms.openlocfilehash: 525ec334e73ca010d319b40ab864d08dae32f493
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91997386"
---
# <a name="using-azure-sentinel-with-azure-web-application-firewall"></a>Azure 웹 응용 프로그램 방화벽과 함께 Azure 센티널 사용

Azure 센티널과 결합 된 azure WAF (웹 응용 프로그램 방화벽)는 WAF 리소스에 대 한 보안 정보 이벤트 관리를 제공할 수 있습니다. Azure 센티널은 WAF 데이터를 쉽게 분할 하 고 볼 수 있는 Log Analytics를 사용 하 여 보안 분석을 제공 합니다. 센티널을 사용 하 여 미리 작성 된 통합 문서에 액세스 하 고 조직의 요구 사항에 가장 적합 하도록 수정할 수 있습니다. 통합 문서는 CDN (Azure Content Delivery Network)의 WAF, Azure Front 문에 WAF의 분석, 여러 구독 및 작업 영역에서 Application Gateway의 waf에 대 한 분석을 표시할 수 있습니다.

## <a name="waf-log-analytics-categories"></a>WAF log analytics 범주

WAF log analytics는 다음과 같은 범주로 분류 됩니다.  

- 수행 된 모든 WAF 작업 
- 상위 40 차단 된 요청 URI 주소 
- 상위 50 이벤트 트리거,  
- 시간별 메시지 
- 전체 메시지 정보 
- 메시지의 공격 이벤트  
- 시간별 공격 이벤트 
- 추적 ID 필터 
- ID 메시지 추적 
- 상위 10 개 공격 IP 주소 
- IP 주소의 메시지 공격 

## <a name="waf-workbook-examples"></a>WAF 통합 문서 예제

다음 WAF 통합 문서 예제는 샘플 데이터를 보여 줍니다.

:::image type="content" source="media//waf-sentinel/waf-actions-filter.png" alt-text="WAF 작업 필터":::

:::image type="content" source="media//waf-sentinel/top-50-event-trigger.png" alt-text="WAF 작업 필터":::

:::image type="content" source="media//waf-sentinel/attack-events.png" alt-text="WAF 작업 필터":::

:::image type="content" source="media//waf-sentinel/top-10-attacking-ip-address.png" alt-text="WAF 작업 필터":::

## <a name="launch-a-waf-workbook"></a>WAF 통합 문서 시작

WAF 통합 문서는 모든 Azure Front 도어, Application Gateway 및 CDN WAFs에 대해 작동 합니다. 이러한 리소스의 데이터를 연결 하기 전에 리소스에서 log analytics를 사용 하도록 설정 해야 합니다. 

각 리소스에 대해 log analytics를 사용 하도록 설정 하려면 개별 Azure Front 도어, Application Gateway 또는 CDN 리소스로 이동 합니다.

1. **진단 설정**을 선택합니다.
2. **+ 진단 설정 추가**를 선택합니다. 
3. 진단 설정 페이지에서 다음을 수행 합니다.
   1. 이름을 입력합니다. 
   1. **Log Analytics에 보내기**를 선택합니다. 
   1. 로그 대상 작업 영역을 선택 합니다. 
   1. 분석할 로그 유형을 선택 합니다.
      1. Application Gateway: ' ApplicationGatewayAccessLog ' 및 ' ApplicationGatewayFirewallLog '
      1. Azure Front 도어: ' FrontDoorAccessLog ' 및 ' FrontDoorFirewallLog '
      1. CDN: ' AzureCdnAccessLog '
   1. **저장**을 선택합니다.

   :::image type="content" source="media//waf-sentinel/diagnostics-setting.png" alt-text="WAF 작업 필터":::

4. Azure 홈 페이지의 검색 창에서 **Azure 센티널** 을 입력 하 고 **azure 센티널** 리소스를 선택 합니다. 
2. 이미 활성 작업 영역을 선택 하거나 센티널에서 새 작업 영역을 만듭니다. 
3. **구성** 아래의 왼쪽 패널에서 **데이터 커넥터**를 선택 합니다.
4. **Microsoft 웹 응용 프로그램 방화벽** 을 검색 하 고 **microsoft waf (웹 응용 프로그램 방화벽)** 를 선택 합니다. 오른쪽 아래에서 **커넥터 페이지 열기** 를 선택 합니다.

   :::image type="content" source="media//waf-sentinel/data-connectors.png" alt-text="WAF 작업 필터":::

8. 이전에 수행 하지 않은 경우 로그 분석 데이터를 포함 하려는 각 WAF 리소스에 대 한 **구성** 의 지침을 따릅니다.
6. 개별 WAF 리소스 구성이 완료 되 면 **다음 단계** 탭을 선택 합니다. 권장 되는 통합 문서 중 하나를 선택 합니다. 이 통합 문서는 이전에 사용 하도록 설정 된 모든 로그 분석 데이터를 사용 합니다. 이제 WAF 리소스에 대해 작동 하는 WAF 통합 문서가 있어야 합니다.

   :::image type="content" source="media//waf-sentinel/waf-workbooks.png" alt-text="WAF 작업 필터":::


## <a name="next-steps"></a>다음 단계

- [Azure 센티널에 대해 자세히 알아보기](../sentinel/overview.md)
- [Azure Monitor 통합 문서에 대 한 자세한 정보](../azure-monitor/platform/workbooks-overview.md)
