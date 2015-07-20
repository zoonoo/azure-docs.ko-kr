<properties
   pageTitle="Operations Manager에서 경고 보기"
   description="인프라에서 모니터링되는 서버에 대한 Operations Manager에서의 관리 경고에 대한 학습"
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="" />
<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/02/2015"
   ms.author="banders" />



# Operations Manager 경고 보기

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Microsoft Azure Operational Insights에서 경고 관리를 사용하려면 먼저 솔루션이 설치되어 있어야 합니다. 솔루션 설치에 대한 더 알려면 [솔루션 갤러리를 사용하여 솔루션 추가 및 제거](operational-insights-add-solution.md)를 참조하십시오. 서버를 Operations Manager 에이전트에서 모니터링하는 경우 솔루션만이 작동합니다. Operational Insights로 Operations Manager 사용에 대한 추가 정보는 [System Center Operations Manager에서 Operational Insights에 연결](operational-insights-connect-scom.md)을 참조하십시오.

솔루션이 설치된 후, Operational Insights의 **개요** 대시보드에서 **경고 관리** 타일을 사용하여 모니터링된 서버에 대한 경고를 볼 수 있습니다.

![경고 관리 타일의 이미지](./media/operational-insights-alerts/overview-alert.png)


**경고** 대시보드에서 Microsoft Azure Operational Insights 경고를 보고 관리할 수 있습니다. 대시보드에서, 경고 정보는 다음 범주에 표시됩니다.

- 활성 경고
	- 중요한 알림
	- 경고 알림
	- 경고 원본
- 모든 활성 경고
	- 위험, 경고 및 정보인 경고의 백분율을 표시합니다.
- 일반 경고 쿼리
	- 이 영역에는 Operational Insights 소프트웨어 개발 팀이 경고를 사용하여 시작할 수 있도록 작성한 미리 작성된 쿼리가 포함됩니다.


경고에는 발견한 문제점, 경고에 영향을 주는 서버, 우선순위 및 경고의 이름이 표시됩니다.

![경고 대시보드의 이미지](./media/operational-insights-alerts/alert-drilldown1.png)

![경고 대시보드의 이미지](./media/operational-insights-alerts/alert-drilldown2.png)


**경고 관리** 대시보드에서, Microsoft Azure Operational Insights가 발견한 모든 경고를 볼 수 있습니다.

## Operational Insights 경고를 보려면

1. **개요** 페이지에서 **경고 관리** 타일을 클릭합니다.
2. **경고 관리** 대시보드에서 경고 범주를 확인하고 작업할 경고를 선택합니다.
3. 타일 또는 모든 항목에 대한 자세한 정보를 보려면 해당 항목을 **검색** 페이지에서 클릭합니다.
4. 발견한 정보를 사용하여 경고를 조사할 수 있으며 문제를 해결하기 위해 수행해야 할 추가 동작을 결정할 수 있습니다.

<!---HONumber=July15_HO2-->