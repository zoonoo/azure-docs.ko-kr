---
title: 가동 중지 시간, SLA 및 중단 통합 문서 - Application Insights
description: Application Insights 리소스 및 Azure 구독 전체에서 단일 창을 통해 웹 테스트에 대한 SLA를 계산하고 보고할 수 있습니다.
ms.topic: conceptual
ms.date: 05/4/2021
ms.openlocfilehash: 4063cd28111dd0ea3c3d0cabe44f884f6605c063
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108750541"
---
# <a name="downtime-sla-and-outages-workbook"></a>가동 중지 시간, SLA 및 중단 통합 문서

Application Insights 리소스 및 Azure 구독 전체에서 단일 창을 통해 웹 테스트에 대한 SLA(서비스 수준 계약)를 계산하고 보고하는 간단한 방법을 소개합니다. 가동 중지 시간 및 중단 보고서는 미리 작성된 강력한 쿼리 및 데이터 시각화를 제공하여 고객의 연결, 일반적인 애플리케이션 응답 시간 및 경험한 가동 중지 시간에 대한 이해도를 높여줍니다.

SLA 통합 문서 템플릿은 Application Insights 리소스의 통합 문서 갤러리를 통해 액세스할 수 있으며, 맨 위에서 **SLA 보고서** 를 선택하여 가용성 탭을 통해서도 액세스할 수 있습니다.
:::image type="content" source="./media/sla-report/availability.png" alt-text="SLA 보고서가 강조 표시된 가용성 탭의 스크린샷" lightbox="./media/sla-report/availability.png":::

:::image type="content" source="./media/sla-report/workbook-gallery.png" alt-text="가동 중지 시간 및 중단 통합 문서가 강조 표시된 통합 문서 갤러리의 스크린샷" lightbox ="./media/sla-report/workbook-gallery.png":::

## <a name="parameter-flexibility"></a>매개 변수 유연성

통합 문서에 설정된 매개 변수는 보고서의 나머지 부분에 영향을 줍니다.

:::image type="content" source="./media/sla-report/parameters.png" alt-text="매개 변수의 스크린샷" lightbox= "./media/sla-report/parameters.png":::

`Subscriptions`,  `App Insights Resources`,  `Web Test`  매개 변수는 높은 수준의 리소스 옵션을 결정합니다. 이러한 매개 변수는 Log Analytics 쿼리를 기반으로 하며 모든 보고서 쿼리에 사용됩니다.

`Failure Threshold`  및  `Outage Window` 를 사용하면 서비스 중단에 대한 사용자 고유의 기준을 결정할 수 있습니다. 예를 들어 선택한 기간 동안 실패한 위치 카운터를 기반으로 하는 App Insights 가용성 경고에 대한 기준을 결정할 수 있습니다. 일반적인 임계값은 5분 동안 3개의 위치입니다.

`Maintenance Period` 를 사용하면 일반적인 유지 관리 빈도를 선택할 수 있으며,  `Maintenance Window` 는 유지 관리 기간에 대한 날짜/시간 선택기입니다. 식별된 기간 동안 발생하는 모든 데이터는 결과에서 무시됩니다.

`Availability Target %` 는 대상 목표를 지정하고 사용자 지정 값을 사용합니다.

## <a name="overview-page"></a>개요 페이지

개요 페이지에는 총 SLA(정의된 경우 유지 관리 기간 제외), 엔드투엔드 인스턴스 및 애플리케이션 가동 중지 시간에 대한 높은 수준의 정보가 포함되어 있습니다. 중단 인스턴스는 테스트가 실패하기 시작하는 시점부터 중단 매개 변수를 기준으로 테스트가 성공할 때까지로 정의됩니다. 테스트가 오전 8시에 실패하고 오전 10시에 다시 성공하는 경우 전체 데이터 기간이 동일한 중단으로 간주됩니다.

:::image type="content" source="./media/sla-report/overview.gif" alt-text="테스트별 개요 표를 보여주는 개요 페이지의 GIF" lightbox="./media/sla-report/overview.gif":::

보고 기간 동안 발생한 가장 긴 중단을 조사할 수도 있습니다.

일부 테스트는 추가 조사를 위해 Application Insights 리소스에 다시 연결할 수 있지만 [작업 영역 기반 Application Insights 리소스](create-workspace-resource.md)에서만 가능합니다.

## <a name="downtime-outages-and-failures"></a>가동 중지 시간, 중단 및 실패

**중단 및 가동 중지 시간** 탭에는 테스트별로 세분화된 총 중단 인스턴스 및 총 가동 중지 시간에 대한 정보가 있습니다. **위치별 실패** 탭에는 잠재적인 문제 연결 영역을 식별하는 데 도움이 되는 실패한 테스트 위치의 지역 지도가 있습니다.

:::image type="content" source="./media/sla-report/outages-failures.gif" alt-text="가동 중지 시간 및 중단 통합 문서에서 중단 및 가동 중지 시간 탭과 위치별 실패 탭을 보여주는 GIF" lightbox="./media/sla-report/outages-failures.gif":::

## <a name="edit-the-report"></a>보고서 편집

다른 [Azure Monitor 통합 문서](../visualize/workbooks-overview.md)와 같이 보고서를 편집할 수 있습니다. 팀의 요구 사항에 따라 쿼리 또는 시각화를 사용자 지정할 수 있습니다.

:::image type="content" source="./media/sla-report/edit.gif" alt-text="편집 단추를 선택하여 시각화를 원형 차트로 변경하는 GIF" lightbox="./media/sla-report/edit.gif":::

### <a name="log-analytics"></a>Log Analytics

쿼리는 모두 [Log Analytics](../logs/log-analytics-overview.md)에서 실행되고 다른 보고서 또는 대시보드에서 사용할 수 있습니다. 매개 변수 제한을 제거하고 핵심 쿼리를 다시 사용합니다.

:::image type="content" source="./media/sla-report/logs.gif" alt-text="로그 쿼리의 GIF" lightbox="./media/sla-report/logs.gif":::

## <a name="access-and-sharing"></a>액세스 및 공유

보고서를 팀, 경영진과 공유하거나 대시보드에 고정하여 추가로 사용할 수 있습니다. 사용자는 실제 통합 문서가 저장된 Applications Insights 리소스에 대한 읽기 권한/액세스 권한이 있어야 합니다.

:::image type="content" source="./media/sla-report/share.png" alt-text="이 템플릿 공유의 스크린샷" lightbox= "./media/sla-report/share.png":::

## <a name="next-steps"></a>다음 단계

- [Log Analytics 쿼리 최적화 팁](../logs/query-optimization.md).
- [통합 문서에서 차트를 만드는 방법](../visualize/workbooks-chart-visualizations.md)을 알아봅니다.
- [가용성 테스트](monitor-web-app-availability.md)를 사용하여 웹 사이트를 모니터링하는 방법을 알아봅니다.