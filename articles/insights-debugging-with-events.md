<properties title="How to debug with events" pageTitle="How to debug with events" description="Learn how to see events in Azure." authors="hanikn"  />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="hanikn"></tags>

# Azure 리소스 또는 리소스 그룹에 영향을 미치는 이벤트 모니터링

1.  [Azure 포털 Preview][Azure 포털 Preview]에 로그인합니다.
2.  왼쪽 탐색 모음(**표시줄**이라고도 함)에서 **찾아보기** 단추를 클릭합니다.<br />
    ![찾아보기 허브][찾아보기 허브]
3.  그런 다음 관심 있는 이벤트에 따라 리소스를 선택합니다. 자세히 설명하기 위해 이 문서의 스크린샷에 리소스 그룹의 이벤트를 포함했습니다.
4.  **리소스 그룹** 블레이드에서 리소스 그룹의 이름을 클릭합니다. 그러면 리소스 그룹 블레이드로 이동합니다.
    ![리소스 그룹][리소스 그룹]
5.  리소스 그룹 블레이드에는 **Events in the past week**라는 파트가 있습니다. 이 파트의 각 표시줄은 지난주의 각 날짜에 발생한 이벤트의 수를 나타냅니다. 각 표시줄에는 2개의 색, 즉 파란색과 분홍색이 있습니다. 분홍색은 해당 날짜의 **실패** 이벤트를 나타내고, 파란색은 다른 모든 이벤트를 나타냅니다.<br />
    ![리소스 그룹][1]
6.  이제 **Events in the past week** 파트를 클릭합니다. 지난주에 리소스 그룹에 영향을 미친 모든 이벤트를 포함하는 **이벤트** 블레이드라는 새 블레이드가 표시됩니다.
    ![리소스 그룹][2]
7.  이벤트 중 하나를 클릭합니다.<br />
    ![리소스 그룹][3]<br />
    이벤트에 대한 다양한 세부 정보가 포함된 새 블레이드가 열립니다. **실패** 이벤트의 경우 이 페이지에는 일반적으로 디버깅 목적의 유용한 세부 정보가 포함된 **하위 상태** 및 **속성** 섹션이 있습니다.

  [Azure 포털 Preview]: https://portal.azure.com/
  [찾아보기 허브]: ./media/insights-debugging-with-events/Insights_Browse.png
  [리소스 그룹]: ./media/insights-debugging-with-events/Insights_SelectRG.png
  [1]: ./media/insights-debugging-with-events/Insights_RGBlade.png
  [2]: ./media/insights-debugging-with-events/Insights_AllEvents.png
  [3]: ./media/insights-debugging-with-events/Insights_EventDetails.png
