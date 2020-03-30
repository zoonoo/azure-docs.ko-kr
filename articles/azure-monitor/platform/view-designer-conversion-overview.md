---
title: 통합 문서 전환 가이드에 Azure 모니터 보기 디자이너
description: ''
author: austonli
ms.author: aul
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 234da921b4f0d1243ca8cfdb12ba2d851db2b43f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658696"
---
# <a name="azure-monitor-view-designer-to-workbooks-transition-guide"></a>통합 문서 전환 가이드에 Azure 모니터 보기 디자이너
[뷰 디자이너는](view-designer.md) 차트, 목록 및 타임라인을 사용하여 Log Analytics 작업 영역에서 데이터를 시각화하는 데 도움이 되는 사용자 지정 보기를 만들 수 있는 Azure Monitor의 기능입니다. 단계적으로 폐지되고 추가 기능을 제공하는 통합 문서로 대체됩니다. 이 문서에서는 기존 보기를 통합 문서로 변환하는 프로세스에 대한 개요를 제공합니다.

## <a name="workbooks-overview"></a>통합 문서 개요
[통합 문서는](../insights/vminsights-workbooks.md) 텍스트, [로그 쿼리,](../log-query/query-language.md)메트릭 및 매개 변수를 풍부한 대화형 보고서로 결합합니다. Azure 리소스에 대한 액세스 권한이 동일한 팀 멤버도 통합 문서를 편집할 수 있습니다.

통합 문서는 다음과 같은 시나리오에 유용합니다.

-   CPU 사용률, 디스크 공간, 메모리, 네트워크 종속성 등 관심 메트릭을 미리 모르는 경우 가상 시스템의 사용을 탐색합니다. 다른 사용 현황 분석 도구와 달리 통합 문서를 사용하면 여러 종류의 시각화 및 분석을 결합할 수 있으므로 이러한 종류의 자유형 탐색에 적합합니다.
-   키 카운터 및 기타 로그 이벤트에 대한 메트릭을 표시하여 최근에 프로비전된 VM의 성능을 팀에 설명합니다.
-   VM의 크기 조정 실험 결과를 팀의 다른 구성원과 공유합니다. 텍스트로 실험의 목표를 설명한 다음 실험을 평가하는 데 사용되는 각 사용 메트릭 및 분석 쿼리와 각 측정항목이 목표 이상 또는 아래에 있는지 여부에 대한 명확한 호출 을 표시할 수 있습니다.
-   중단이 VM 사용에 미치는 영향을 보고하고, 데이터, 텍스트 설명 및 향후 중단을 방지하기 위한 다음 단계에 대한 토론을 결합합니다.


## <a name="why-convert-view-designer-dashboards-to-workbooks"></a>뷰 디자이너 대시보드를 통합 문서로 변환해야 하는 이유

뷰 디자이너는 다양한 쿼리 기반 보기 및 시각화를 생성하는 기능을 제공합니다. 그러나 그리드 및 타일 레이아웃 서식 지정 또는 데이터를 나타내는 대체 그래픽 선택과 같은 많은 고급 사용자 지정은 제한되어 있습니다. 뷰 디자이너는 데이터를 나타내는 총 9개의 고유 타일로 제한됩니다.

통합 문서는 데이터의 잠재력을 최대한 활용하는 플랫폼입니다. 통합 문서는 모든 기능을 유지할 뿐만 아니라 텍스트, 메트릭, 매개 변수 등을 통해 추가 기능을 지원합니다. 예를 들어 통합 문서를 사용하면 조밀한 그리드를 통합하고 검색 막대를 추가하여 데이터를 쉽게 필터링하고 분석할 수 있습니다. 

### <a name="advantages-of-using-workbooks-over-view-designer"></a>뷰 디자이너를 통해 통합 문서를 사용할 때의 장점

* 로그와 메트릭을 모두 지원합니다.
* 개별 액세스 제어 및 공유 통합 문서 보기에 대한 개인 보기를 모두 허용합니다.
* 탭, 크기 조정 및 컨트롤 크기 조정이 있는 사용자 지정 레이아웃 옵션입니다.
* 여러 로그 분석 작업 영역, 애플리케이션 인사이트 응용 프로그램 및 구독에서 쿼리할 수 있습니다.
* 연결된 차트 및 시각화를 동적으로 업데이트하는 사용자 지정 매개 변수를 활성화합니다.
* 공용 GitHub의 템플릿 갤러리 지원.

이 가이드에서는 일반적으로 사용되는 여러 뷰 디자이너 뷰를 직접 다시 만드는 간단한 단계를 제공하지만 통합 문서를 사용하면 사용자가 자신의 사용자 지정 시각화 및 메트릭을 자유롭게 만들고 디자인할 수 있습니다. 다음 스크린샷은 [Workspace 사용 템플릿에서](https://go.microsoft.com/fwlink/?linkid=874159&resourceId=Azure%20Monitor&featureName=Workbooks&itemId=community-Workbooks%2FAzure%20Monitor%20-%20Workspaces%2FWorkspace%20Usage&workbookTemplateName=Workspace%20Usage&func=NavigateToPortalFeature&type=workbook) 나온 것이며 통합 문서를 만들 수 있는 예제를 보여 주며 다음과 같습니다.


![통합 문서 응용 프로그램의 예](media/view-designer-conversion-overview/workbook-template-example.jpg)


## <a name="how-to-start-using-workbooks"></a>통합 문서 사용을 시작하는 방법
통합 문서의 열린 통합 문서는 보기 디자이너 위치 바로 아래 측면 탐색 모음의 항목으로 Log Analytics 작업 공간에서 사용할 수 있습니다.

![통합 문서 탐색](media/view-designer-conversion-overview/workbooks-nav.png)

선택한 후에는 작업 영역에 저장된 모든 통합 문서와 템플릿을 나열하는 갤러리가 표시됩니다.

![통합 문서 갤러리](media/view-designer-conversion-overview/workbooks-gallery.png)

새 통합 문서를 시작하려면 **빠른 시작**에서 **빈** 템플릿또는 상단 탐색 모음의 **새** 아이콘을 선택할 수 있습니다. 템플릿을 보거나 저장된 통합 문서로 돌아가려면 갤러리에서 항목을 선택하거나 검색 모음에서 이름을 검색합니다.

통합 문서를 저장하려면 특정 제목, 구독, 리소스 그룹 및 위치로 보고서를 저장해야 합니다.
통합 문서LA 작업 영역과 동일한 설정으로 자동 채워지며 동일한 구독, 리소스 그룹이 있지만 사용자는 이러한 보고서 설정을 변경할 수 있습니다. 통합 문서는 기본적으로 *내 보고서에*저장되며 개별 사용자만 액세스할 수 있습니다. 또한 공유 보고서에 직접 저장하거나 나중에 공유할 수도 있습니다.

![통합 문서 저장](media/view-designer-conversion-overview/workbooks-save.png)

## <a name="next-steps"></a>다음 단계

- [변환 옵션](view-designer-conversion-options.md)
