---
title: 애플리케이션 변경 분석 시각화 - Azure Monitor
description: Azure Monitor의 애플리케이션 변경 분석 시각화를 사용하는 방법을 알아봅니다.
ms.topic: conceptual
author: cawams
ms.author: cawa
ms.date: 02/11/2021
ms.openlocfilehash: 8319885de26bf79f5e402c4d06b29e9dd94894de
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104655854"
---
# <a name="visualizations-for-application-change-analysis-preview"></a>애플리케이션 변경 분석의 시각화(미리 보기)

## <a name="standalone-ui"></a>독립 실행형 UI

Azure Monitor에는 애플리케이션 변경 분석에 대한 독립 실행형 창이 있으며, 여기에서 애플리케이션 종속성 및 리소스 정보를 포함하여 모든 변경 사항을 확인할 수 있습니다.

시작하려면 Azure Portal의 검색 창에서 변경 분석을 검색합니다.

![Azure Portal에서 변경 분석을 검색하는 스크린샷](./media/change-analysis/search-change-analysis.png)

지난 24시간의 변경 사항과 함께 선택한 구독의 모든 리소스가 표시됩니다. 모든 변경 사항은 이전 값 및 새 값과 함께 표시되어 한 눈에 인사이트를 제공합니다.

![Azure Portal의 변경 분석 블레이드 스크린샷](./media/change-analysis/change-analysis-standalone-blade.png)

변경 사항을 클릭하여 전체 리소스 관리자 코드 조각 및 기타 속성을 볼 수 있습니다.

![변경 세부 정보 스크린샷](./media/change-analysis/change-details.png)

피드백이 있는 경우 피드백 보내기 단추나 이메일 changeanalysisteam@microsoft.com을 사용합니다.

![변경 분석 탭의 피드백 단추 스크린샷](./media/change-analysis/change-analysis-feedback.png)

### <a name="multiple-subscription-support"></a>여러 구독 지원

UI는 여러 구독을 선택하여 리소스 변경 사항을 볼 수 있도록 지원합니다. 구독 필터를 사용합니다.

![여러 구독 선택을 지원하는 구독 필터 스크린샷](./media/change-analysis/multiple-subscriptions-support.png)


## <a name="application-change-analysis-in-the-diagnose-and-solve-problems-tool"></a>진단 및 문제 해결 도구에서 애플리케이션 변경 분석

애플리케이션 변경 분석은 웹 앱 진단 및 문제 해결 도구의 독립 실행형 탐지기입니다. 또한 이는 **애플리케이션 크래시** 및 **웹 앱 작동 중지 탐지기** 에서 집계됩니다. 진단 및 문제 해결 도구에 입력하면 **Microsoft.ChangeAnalysis** 리소스 공급자가 자동으로 등록됩니다. 웹 앱 게스트 내 변경 사항 추적을 사용하도록 설정하려면 다음 지침을 따르세요.

1. **가용성 및 성능** 을 선택합니다.

    !["가용성 및 성능" 문제 해결 옵션 스크린샷](./media/change-analysis/availability-and-performance.png)

2. **애플리케이션 변경** 을 선택합니다. **애플리케이션 크래시** 에서도 이 기능을 사용할 수 있습니다.

   ![애플리케이션 크래시 단추 스크린샷](./media/change-analysis/application-changes.png)

3. 이 링크를 누르면 애플리케이션 변경 분석 UI 범위가 웹 앱으로 지정됩니다. 웹 앱에서 게스트 내 변경 사항 추적을 사용하도록 설정하지 않은 경우 배너를 따라 파일 및 앱 설정 변경 사항을 가져옵니다.

   ![애플리케이션 크래시 옵션 스크린샷](./media/change-analysis/enable-changeanalysis.png)

4. **변경 분석** 을 켜고 **저장** 을 선택합니다. 도구에 App Service 요금제의 모든 웹 앱이 표시됩니다. 요금제 수준 스위치를 사용하여 요금제의 모든 웹 앱에 대한 변경 분석을 켤 수 있습니다.

    !["변경 분석 사용" 사용자 인터페이스 스크린샷](./media/change-analysis/change-analysis-on.png)

5. 변경 데이터는 **웹 앱 작동 중지** 및 **애플리케이션 크래시** 탐지기에서도 사용할 수 있습니다. 시간에 따른 변경 사항 유형과 해당 변경 사항에 대한 세부 정보를 요약하는 그래프가 표시됩니다. 기본적으로 즉각적인 문제를 해결할 수 있도록 지난 24시간 동안의 변경 사항이 표시됩니다.

     ![변경 사항 차이 뷰 스크린샷](./media/change-analysis/change-view.png)

## <a name="diagnose-and-solve-problems-tool"></a>진단 및 문제 해결 도구
변경 분석은 문제 진단 및 해결 도구에서 인사이트 카드로 사용할 수 있습니다. 리소스에 문제가 발생했으며 지난 72시간 동안 변경 사항이 검색된 경우 인사이트 카드에 변경 사항 개수가 표시됩니다. 변경 세부 사항 보기 링크를 클릭하면 변경 분석 독립 실행형 UI의 필터링된 보기가 표시됩니다.

![진단 및 문제 해결 도구에서 변경 정보를 확인하는 스크린샷.](./media/change-analysis/change-insight-diagnose-and-solve.png)



## <a name="virtual-machine-diagnose-and-solve-problems"></a>Virtual Machine 진단 및 문제 해결

Virtual Machine에 대한 진단 및 문제 해결 도구로 이동합니다.  **문제 해결 도구** 로 이동하여 페이지를 탐색하고 **최근 변경 사항 분석** 을 선택하여 Virtual Machine에 대한 변경 사항을 확인합니다.

![VM 진단 및 문제 해결 스크린샷](./media/change-analysis/vm-dnsp-troubleshootingtools.png)

![문제 해결 도구의 분석기 변경](./media/change-analysis/analyze-recent-changes.png)

## <a name="activity-log-change-history"></a>활동 로그 변경 내역

활동 로그의 [변경 내역 보기](../essentials/activity-log.md#view-change-history) 기능을 사용하면 애플리케이션 변경 분석 서비스 백 엔드를 호출하여 작업과 연결된 변경 사항을 가져올 수 있습니다. **변경 내용** 은 [Azure Resource Graph](../../governance/resource-graph/overview.md)를 직접 호출하는 데 사용되지만 애플리케이션 변경 분석을 호출하도록 백 엔드를 교환하여 돌아온 변경 사항에 [Azure Resource Graph](../../governance/resource-graph/overview.md)의 리소스 수준 변경 사항, [Azure Resource Manager](../../azure-resource-manager/management/overview.md)의 리소스 속성, App Services 웹앱과 같은 PaaS 서비스의 게스트 내 변경 사항이 포함됩니다. 애플리케이션 변경 분석 서비스에서 사용자 구독의 변경 사항을 검색할 수 있도록 하려면 리소스 공급자를 등록해야 합니다. **변경 내역** 탭을 처음으로 입력하면 도구에서 자동으로 **Microsoft.ChangeAnalysis** 리소스 공급자를 등록하기 시작합니다. 등록 후 **Azure Resource Graph** 의 변경 사항이 즉시 표시되며 범위에 지난 14일이 포함됩니다. 구독을 온보딩한 후 4시간 이내에 다른 원본의 변경 사항을 확인할 수 있습니다.

![활동 로그 변경 내역 통합](./media/change-analysis/activity-log-change-history.png)

## <a name="vm-insights-integration"></a>VM Insights 통합

[VM Insights](../vm/vminsights-overview.md)를 사용하도록 설정된 사용자는 CPU나 메모리와 같은 메트릭 차트에서 급증의 원인이 될 수 있는 가상 머신의 변경 사항을 확인할 수 있습니다. 변경 데이터는 VM Insights 측 탐색 모음에 통합되어 있습니다. 사용자는 VM에서 변경된 내용이 있는지 확인하고 **변경 내용 조사** 를 선택하여 애플리케이션 변경 분석 독립 실행형 UI에서 변경 정보를 볼 수 있습니다.

[![VM Insights 통합](./media/change-analysis/vm-insights.png)](./media/change-analysis/vm-insights.png#lightbox)

## <a name="next-steps"></a>다음 단계

- [변경 분석 문제를 해결하는 방법](change-analysis-troubleshoot.md) 알아보기
