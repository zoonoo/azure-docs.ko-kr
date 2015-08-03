<properties
	pageTitle="Operational Insights 대시보드"
	description="Operational Insights의 기본 대시보드 사용에 대한 정보 문서"
	services="operational-insights"
	documentationCenter=""
	authors="ehissey"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="operational-insights"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/21/2015"
	ms.author="evanhi"/>

# Operational Insights 대시보드

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

이 가이드는 Operational Insights 대시보드가 저장된 모든 로그 검색을 시각화하여 환경을 보는 단일 렌즈를 제공하는 방법을 이해하는 데 도움이 됩니다.

![예제 대시보드](./media/operational-insights-use-dashboards/example-dash.png)

## 내 대시보드를 만드는 방법

먼저 왼쪽 탐색 모음에서 개요 단추를 클릭하여 Azure Operational Insights 개요로 이동합니다. 왼쪽에 "내 대시보드" 타일이 표시됩니다. 타일을 클릭하여 대시보드로 드릴다운합니다.

![개요](./media/operational-insights-use-dashboards/overview.png)



## 타일 추가

대시보드에서 타일은 저장된 로그 검색을 기반으로 합니다. Operational Insights에는 여러 저장된 로그 검색이 미리 만들어져 있으므로 바로 시작할 수 있습니다. 시작하는 방법을 간략하게 설명하는 다음 그림이 표시됩니다.

![그림](./media/operational-insights-use-dashboards/pictorial.png)

내 대시보드 뷰에서 페이지 맨 아래에 있는 '사용자 지정' 기어를 클릭하여 사용자 지정 모드로 전환합니다. 페이지 오른쪽에 열리는 패널에는 작업 영역의 저장된 로그 검색이 모두 표시됩니다.

![타일 추가 1](./media/operational-insights-use-dashboards/add-tile1.png)

저장된 로그 검색을 타일로 시각화하려면 왼쪽의 빈 공간으로 끌어다 놓습니다. 저장된 검색을 끌면 타일로 바뀝니다.

![타일 추가 2](./media/operational-insights-use-dashboards/add-tile2.png)

![타일 추가 3](./media/operational-insights-use-dashboards/add-tile3.png)


## 타일 편집

내 대시보드 뷰에서 페이지 맨 아래에 있는 '사용자 지정' 기어를 클릭하여 사용자 지정 모드로 전환합니다. 편집하려는 타일을 클릭합니다. 오른쪽 패널이 편집으로 바뀌고 다양한 옵션을 제공합니다. ![타일 편집](./media/operational-insights-use-dashboards/edit-tile.png)

### 타일 시각화#
다음 두 종류의 타일 시각화 중에서 선택할 수 있습니다.

**가로 막대형 차트** <p> ![가로 막대형 차트](./media/operational-insights-use-dashboards/bar-chart.png)

로그 검색 결과가 필드에 따라 집계되는지 여부에 따라 저장된 로그 검색 결과의 타임라인 또는 필드별 결과 목록이 표시됩니다.

**메트릭** <p> ![메트릭](./media/operational-insights-use-dashboards/metric.png)

총 로그 검색 결과 적중 횟수가 타일에 숫자로 표시됩니다. 메트릭 타일을 사용하면 임계값에 도달할 때 타일이 강조 표시되도록 임계값을 설정할 수 있습니다.

### 임계값
메트릭 시각화를 사용하여 타일에 임계값을 만들 수 있습니다. 타일에 임계값을 만들려면 선택합니다. 값이 선택된 임계값을 초과하거나 미만일 때 타일을 강조 표시할지 여부를 선택하고 아래에서 임계값을 설정합니다.

## 대시보드 구성
대시보드를 구성하려면 내 대시보드 뷰로 이동한 다음 페이지 맨 아래에 있는 '사용자 지정' 기어를 클릭하여 사용자 지정 모드로 전환합니다. 이동할 타일을 클릭하고 끌어서 원하는 위치로 타일을 이동합니다.

![대시보드 구성](./media/operational-insights-use-dashboards/organize.png)

## 타일 제거
타일을 제거하려면 내 대시보드 뷰로 이동한 다음 페이지 맨 아래에 있는 **사용자 지정** 기어를 클릭하여 사용자 지정 모드로 전환합니다. 제거할 타일을 선택한 다음 오른쪽 패널에서 **타일 제거**를 선택합니다. ![타일 제거](./media/operational-insights-use-dashboards/remove-tile.png)

<!---HONumber=July15_HO4-->