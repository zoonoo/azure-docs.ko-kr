---
title: 가동 중지 시간, SLA 및 중단 통합 문서-Application Insights
description: Application Insights 리소스 및 Azure 구독 전체에서 단일 창을 통해 웹 테스트에 대 한 SLA를 계산 하 고 보고 합니다.
ms.topic: conceptual
ms.date: 02/8/2021
ms.openlocfilehash: d225627a27bffd9088956e5aee37ca543e528d4a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101714054"
---
# <a name="downtime-sla-and-outages-workbook"></a>가동 중지 시간, SLA 및 중단 통합 문서

Application Insights 리소스 및 Azure 구독 전체에서 단일 창을 통해 웹 테스트에 대 한 SLA (서비스 수준 계약)를 계산 하 고 보고 하는 간단한 방법을 소개 합니다. 가동 중지 및 중단 보고서는 강력 하 게 미리 작성 된 쿼리 및 데이터 시각화를 제공 하 여 고객의 연결, 일반적인 응용 프로그램 응답 시간 및 숙련 된 시간에 대 한 이해를 향상 시킵니다.

SLA 통합 문서 템플릿은 Application Insights 리소스의 통합 문서 갤러리를 통해 액세스할 수 있으며, 맨 위에서 **Sla 보고서** 를 선택 하 여 가용성 탭을 통해 액세스할 수 있습니다.
:::image type="content" source="./media/sla-report/availability.png" alt-text="SLA 보고서가 강조 표시 된 가용성 탭의 스크린샷" lightbox="./media/sla-report/availability.png":::

:::image type="content" source="./media/sla-report/workbook-gallery.png" alt-text="가동 중지 및 중단 통합 문서가 강조 표시 된 통합 문서 갤러리의 스크린샷" lightbox ="./media/sla-report/workbook-gallery.png":::

## <a name="parameter-flexibility"></a>매개 변수 유연성

통합 문서에 설정 된 매개 변수는 보고서의 나머지 부분에 영향을 줍니다.

:::image type="content" source="./media/sla-report/outages.png" alt-text=" 가동 중지 시간 및 중단 통합 문서에 있는 중단/유지 관리 매개 변수 탭의 스크린샷" lightbox ="./media/sla-report/outages.png":::

`Subscriptions`, `App Insights Resources` 및 `Web Test` 매개 변수는 높은 수준의 리소스 옵션을 결정 합니다. 이러한 매개 변수는 log analytics 쿼리를 기반으로 하며 모든 보고서 쿼리에 사용 됩니다.

`Failure Threshold` 및를 사용 하 여 `Outage Window` 서비스 중단에 대 한 사용자 고유의 기준을 결정할 수 있습니다. 예를 들어 선택한 기간 동안 실패 한 위치 카운터를 기반으로 하는 App Insights 가용성 경고에 대 한 기준을 결정할 수 있습니다. 일반적인 임계값은 5 분 동안 3 개의 위치입니다.

`Maintenance Period` 일반적인 유지 관리 빈도를 선택할 수 있으며, `Maintenance Window` 예를 들어 유지 관리 기간에 대 한 날짜/시간 선택기입니다. 식별 된 기간 동안 발생 하는 모든 데이터는 결과에서 무시 됩니다.

`Availability Target 9s` 대상 9 목표를 두 9에서 5 9로 지정 합니다.

## <a name="overview-page"></a>개요 페이지

개요 페이지에는 총 SLA (정의 된 경우 유지 관리 기간 제외), 종단 간 중단 인스턴스 및 응용 프로그램 가동 중지 시간에 대 한 높은 수준의 정보가 포함 되어 있습니다. 중단 인스턴스는 중단 매개 변수를 기반으로 성공할 때까지 테스트가 실패할 때에 의해 정의 됩니다. 테스트가 오전 8:00에 실패 하 고 오전 10:00에 다시 성공할 경우 전체 데이터 기간이 동일한 중단으로 간주 됩니다.

:::image type="content" source="./media/sla-report/overview.gif" alt-text=" 개요 페이지에는 테스트 별 개요 테이블을 보여 줍니다." lightbox="./media/sla-report/overview.gif":::

보고 기간 동안 발생 한 가장 긴 가동 중단을 조사할 수도 있습니다.

일부 테스트는 추가 조사를 위해 Application Insights 리소스로 다시 linkable [작업 영역 기반 Application Insights 리소스](create-workspace-resource.md)에서만 가능 합니다.

## <a name="downtime-outages-and-failures"></a>가동 중지 시간, 중단 시간 및 오류

**작동 중단 및 가동 중지** 시간 탭에는 총 중단 인스턴스 및 테스트에 의해 중단 된 총 중단 시간에 대 한 정보가 있습니다. 위치에의 한 오류 탭에는 잠재적인 문제 연결 영역을 식별 하는 데 도움이 **되** 는 실패 한 테스트 위치의 지역 맵이 있습니다.

:::image type="content" source="./media/sla-report/outages-failures.gif" alt-text=" 가동 중지 및 중단 통합 문서에서 위치 탭에의 한 중단 및 가동 중지 시간 탭 및 실패의 GIF" lightbox="./media/sla-report/outages-failures.gif":::

## <a name="edit-the-report"></a>보고서 편집

다른 [Azure Monitor 통합 문서](../visualize/workbooks-overview.md)와 같이 보고서를 편집할 수 있습니다. 팀의 요구에 따라 쿼리나 시각화를 사용자 지정할 수 있습니다.

:::image type="content" source="./media/sla-report/edit.gif" alt-text=" 편집 단추를 선택 하 여 시각화를 원형 차트로 변경 하는 GIF" lightbox="./media/sla-report/edit.gif":::

### <a name="log-analytics"></a>Log Analytics

쿼리는 [Log Analytics](../logs/log-analytics-overview.md) 에서 실행 되 고 다른 보고서 나 대시보드에서 사용할 수 있습니다. 매개 변수 제한을 제거 하 고 핵심 쿼리를 다시 사용 합니다.

:::image type="content" source="./media/sla-report/logs.gif" alt-text=" 로그 쿼리의 GIF입니다." lightbox="./media/sla-report/logs.gif":::

## <a name="access-and-sharing"></a>액세스 및 공유

보고서는 팀과 공유 하거나, 리더십 하거나, 추가 사용을 위해 대시보드에 고정할 수 있습니다. 사용자는 실제 통합 문서가 저장 된 Application Insights 리소스에 대 한 읽기 권한/액세스 권한이 있어야 합니다.

:::image type="content" source="./media/sla-report/share.png" alt-text=" 이 템플릿 공유의 스크린샷" lightbox= "./media/sla-report/share.png":::

## <a name="next-steps"></a>다음 단계

- [쿼리 최적화 팁을 Log Analytics](../logs/query-optimization.md)합니다.
- [통합 문서에서 차트를 만드는](../visualize/workbooks-chart-visualizations.md)방법에 대해 알아봅니다.
- [가용성 테스트](monitor-web-app-availability.md)를 사용 하 여 웹 사이트를 모니터링 하는 방법을 알아봅니다.