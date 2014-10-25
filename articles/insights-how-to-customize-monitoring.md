<properties title="How to customize monitoring" pageTitle="How to customize monitoring" description="Learn how to customize monitoring charts in Azure." authors="stepsic"  />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="stepsic"></tags>

# 모니터링 사용자 지정

Azure 포털 Preview에서 이제 시간 범위를 사용자 지정하고 더 많은 메트릭을 선택하는 것을 포함하여 이전보다 더 다양한 방법으로 모니터링 데이터를 볼 수 있습니다.

1.  [Azure 포털 Preview][]에서 **찾아보기**를 클릭한 후 모니터링할 리소스를 클릭합니다.
2.  **모니터링** 렌즈에는 각 Azure 리소스에 대한 가장 중요한 메트릭이 포함됩니다. 예를 들어 웹 사이트에는 요청, 오류 [웹 테스트][] 및 [분석][]이 있습니다. **오늘의 요청 및 오류** 파트를 클릭하면 **메트릭** 블레이드가 표시됩니다.
    ![모니터링 렌즈][]
3.  **메트릭** 블레이드에는 선택한 메트릭에 대한 자세한 정보가 표시됩니다. 블레이드 맨 위에는 그래프가 있고, 그 아래에 평균, 최소값, 최대값 등 이 메트릭의 집계를 보여 주는 테이블이 있습니다. 그 아래에는 정의한 경고의 목록이 블레이드에 표시되는 메트릭으로 필터링되어 나옵니다. 이 덕분에, 경고가 많은 경우 관련 있는 것만 여기서 확인하게 됩니다. **웹 사이트** 블레이드에서 **경고 규칙** 파트를 클릭하여 웹 사이트의 모든 경고를 볼 수 있습니다.
    ![메트릭 블레이드][]
4.  표시되는 메트릭을 사용자 지정하려면 차트를 마우스 오른쪽 단추로 클릭하고 **쿼리 편집**을 선택합니다.<br />
    ![쿼리 편집][]
5.  쿼리 편집 블레이드에서 2가지 작업, 즉 시간 범위 변경 및 다양한 메트릭 선택을 할 수 있습니다.<br />
    ![쿼리 편집][1]
6.  시간 범위를 변경하는 것은 다른 범위(예: **Past Hour**)를 선택하고 블레이드 맨 아래에 있는 **저장**을 클릭하는 것만큼 쉽습니다. 포털 Preview의 새로운 기능으로, **사용자 지정**을 선택할 수 있습니다.<br />
    ![사용자 지정 시간 범위][]
7.  사용자 지정을 사용하면 지난 2주 중의 기간을 선택할 수 있습니다. 예를 들어 2주 전체를 선택하거나 어제 중 1시간만 선택할 수도 있습니다. 다른 시간을 입력하려면 텍스트 상자에 입력합니다.
8.  시간 범위 아래에서 차트에 표시할 메트릭 수를 제한 없이 선택할 수 있습니다. 몇 가지 새로운 메트릭, 즉 **Memory working set** 및 **Average memory working set**을 볼 수 있습니다.

9.  저장을 클릭하면 웹 사이트 블레이드에서 나갈 때까지 변경 내용이 유지됩니다. 나중에 돌아오면 원래 메트릭 및 시간 범위가 다시 표시됩니다.

## 새 리소스 모니터링

Azure 포털 Preview에서는 다음을 포함하여 다양한 새 리소스에 대한 성능 메트릭을 모니터링할 수 있는 새로운 기능을 제공합니다.

-   웹 호스팅 계획
-   Redis 캐시
-   DocumentDB 계정

웹 호스팅 계획은 **웹 사이트**가 실행되는 인스턴스의 성능을 나타내기 때문에 다른 리소스보다 조금 더 복잡합니다. 웹 호스팅 계획 메트릭에 액세스하려면 웹 사이트에 대한 요약 렌즈에서 웹 호스팅 계획 아이콘을 클릭합니다.

![웹 호스팅 계획][]

**모니터링** 렌즈에서 차트를 볼 수 있습니다. 이 차트는 웹 사이트 블레이드에 있는 차트와 같으며 다음과 같은 새 메트릭을 볼 수 있다는 점이 다릅니다.

-   CPU 비율
-   메모리 비율
-   HTTP 큐 크기
-   디스크 큐 크기

## 병렬 차트 만들기

Azure 포털 Preview의 강력한 사용자 지정을 사용하여 사용자 지정을 위한 병렬 차트를 만들 수 있습니다.

1.  먼저, 시작할 차트를 마우스 오른쪽 단추로 클릭하고 **사용자 지정**을 선택합니다.<br />
    ![차트 사용자 지정][]
2.  그런 다음 **...** 메뉴에서 **클론**을 클릭하여 해당 파트를 복사합니다.<br />
    ![클론 파트][]
3.  마지막으로, 화면 맨 위의 도구 모음에서 **완료**를 클릭합니다. 이제 이 파트를 일반적인 메트릭 파트처럼 처리할 수 있습니다. 표시되는 메트릭을 마우스 오른쪽 단추로 클릭하여 변경하는 경우 동시에 2개의 다른 메트릭을 병렬로 표시할 수 있습니다.<br />
    ![2개의 병렬 메트릭][]

포털에서 나가면 차트 시간 범위 및 선택한 메트릭이 다시 설정됩니다.

  [Azure 포털 Preview]: https://portal.azure.com/
  [웹 테스트]: http://go.microsoft.com/fwlink/?LinkID=394528&clcid=0x409
  [분석]: http://go.microsoft.com/fwlink/?LinkID=394529&clcid=0x409
  [모니터링 렌즈]: ./media/insights-how-to-customize-monitoring/Insights_MonitoringChart.png
  [메트릭 블레이드]: ./media/insights-how-to-customize-monitoring/Insights_MetricBlade.png
  [쿼리 편집]: ./media/insights-how-to-customize-monitoring/Insights_MetricMenu.png
  [1]: ./media/insights-how-to-customize-monitoring/Insights_EditQuery.png
  [사용자 지정 시간 범위]: ./media/insights-how-to-customize-monitoring/Insights_CustomTime.png
  [웹 호스팅 계획]: ./media/insights-how-to-customize-monitoring/Insights_WHPSelect.png
  [차트 사용자 지정]: ./media/insights-how-to-customize-monitoring/Insights_Customize.png
  [클론 파트]: ./media/insights-how-to-customize-monitoring/Insights_ClonePart.png
  [2개의 병렬 메트릭]: ./media/insights-how-to-customize-monitoring/Insights_SideBySide.png
