---
title: Azure Monitor 통합 문서 개요
description: 통합 문서에서 데이터 분석을 위한 유연한 캔버스를 제공 하 고 Azure Portal 내에서 풍부한 시각적 보고서를 만드는 방법에 대해 알아봅니다.
manager: carmonm
services: azure-monitor
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: 5dd553f0a41f82991c467ab256a87beabbff25ee
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2020
ms.locfileid: "92143626"
---
# <a name="azure-monitor-workbooks"></a>Azure Monitor 통합 문서

통합 문서는 Azure Portal 내에서 데이터를 분석하고 풍부한 시각적 보고서를 생성할 수 있는 유연한 캔버스를 제공합니다. 이를 통해 Azure에서 여러 데이터 원본을 탭 하 여 통합 된 대화형 환경으로 결합할 수 있습니다. 

다음은 통합 문서를 만드는 비디오 연습입니다.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4B4Ap]

## <a name="data-sources"></a>데이터 원본

통합 문서는 Azure 내의 여러 원본에서 데이터를 쿼리할 수 있습니다. 통합 문서 작성자는 이 데이터를 변환하여 기본 구성 요소의 가용성, 성능, 사용량 및 전반적 상태에 대한 인사이트를 제공할 수 있습니다. 예를 들어, 가상 컴퓨터에서 성능 로그를 분석 하 여 높은 CPU 또는 메모리 부족 인스턴스를 식별 하 고 결과를 대화형 보고서에 표 형태로 표시 합니다.
  
그러나 통합 문서의 진정한 강점은 단일 보고서 내에서 서로 다른 원본의 데이터를 결합하는 기능입니다. 이렇게 하면 복잡 한 데이터 및 정보를 허용 하지 않는 복잡 한 데이터 및 정보를 사용 하 여 리소스에 대 한 복합 리소스 뷰 또는 조인을 만들 수 있습니다.

통합 문서는 현재 다음 데이터 원본과 호환됩니다.

* [로그](workbooks-data-sources.md#logs)
* [메트릭](workbooks-data-sources.md#metrics)
* [Azure Resource Graph](workbooks-data-sources.md#azure-resource-graph)
* [경고 (미리 보기)](workbooks-data-sources.md#alerts-preview)
* [작업 상태](workbooks-data-sources.md#workload-health)
* [Azure Resource Health](workbooks-data-sources.md#azure-resource-health)
* [Azure Data Explorer](workbooks-data-sources.md#azure-data-explorer)

## <a name="visualizations"></a>시각화

통합 문서는 데이터를 시각화 하기 위한 풍부한 기능 집합을 제공 합니다. 각 시각화 유형의 자세한 예는 아래 예제 링크를 참조할 수 있습니다.

* [Text](workbooks-text-visualizations.md)
* [차트](workbooks-chart-visualizations.md)
* [배경](workbooks-grid-visualizations.md)
* [Tile](workbooks-tile-visualizations.md)
* [Trees](workbooks-tree-visualizations.md)
* [그래프](workbooks-graph-visualizations.md)
* [복합 막대](workbooks-composite-bar.md)

![예제 통합 문서 시각화](./media/workbooks-overview/visualizations.png)

## <a name="getting-started"></a>시작

통합 문서 환경을 탐색 하려면 먼저 Azure Monitor 서비스로 이동 합니다. 이 작업은 Azure Portal의 검색 상자에 **Monitor** 를 입력 하 여 수행할 수 있습니다.

그런 다음 **통합 문서**를 선택 합니다.

![빨간색 상자에 강조 표시 된 통합 문서 단추의 스크린샷](./media/workbooks-overview/workbooks.png)

### <a name="gallery"></a>갤러리

그러면 통합 문서 갤러리로 이동 합니다.

![Azure Monitor 통합 문서 갤러리 보기의 스크린샷](./media/workbooks-overview/gallery.png)

### <a name="workbooks-versus-workbook-templates"></a>통합 문서 및 통합 문서 템플릿

자주 _통합 문서_ 는 녹색으로 표시 되 고 많은 _통합 문서 템플릿은_ 자주 볼 수 있습니다. 템플릿은 여러 사용자 및 팀에서 유연 하 게 다시 사용할 수 있도록 설계 된 큐 레이트 보고서 역할을 합니다. 템플릿을 열면 템플릿 콘텐츠로 채워진 임시 통합 문서가 만들어집니다. 

템플릿 기반 통합 문서의 매개 변수를 조정 하 고 동료를 위한 향후 보고 환경을 손상 시 키 지 않고 분석을 수행할 수 있습니다. 템플릿을 열고 몇 가지를 조정한 다음 저장 아이콘을 선택 하면 해당 템플릿이 통합 문서로 저장 되며, 원래 템플릿이 그대로 유지 되는 것을 녹색으로 표시 합니다. 

내부적으로 템플릿은 저장 된 통합 문서와도 다릅니다. 통합 문서를 저장 하면 연결 된 Azure Resource Manager 리소스가 생성 되는 반면, 템플릿을 열 때 생성 된 임시 통합 문서에는 연결 된 고유 리소스가 없습니다. 통합 문서에서 액세스 제어를 관리 하는 방법에 대 한 자세한 내용은 [통합 문서 access control 문서](workbooks-access-control.md)를 참조 하세요.

### <a name="exploring-a-workbook-template"></a>통합 문서 템플릿 탐색

**응용 프로그램 오류 분석** 을 선택 하 여 기본 응용 프로그램 통합 문서 템플릿 중 하나를 확인 합니다.

![응용 프로그램 오류 분석 템플릿의 스크린샷](./media/workbooks-overview/failure-analysis.png)

앞에서 설명한 것 처럼 템플릿을 열면 사용자가 상호 작용할 수 있는 임시 통합 문서가 만들어집니다. 기본적으로 통합 문서는 원래 템플릿 작성자가 만든 계획 된 분석 환경에 대 한 정보만 표시 하는 읽기 모드로 열립니다.

이 특정 통합 문서의 경우에는 대화형 환경이 있습니다. 구독, 대상 앱 및 표시할 데이터의 시간 범위를 조정할 수 있습니다. 이러한 선택을 완료 한 후에는 HTTP 요청 그리드도 대화형으로 수행 되며, 개별 행을 선택 하면 보고서 아래쪽의 두 차트에서 렌더링 되는 데이터가 변경 됩니다.

### <a name="editing-mode"></a>편집 모드

이 통합 문서 템플릿이 어떻게 배치 되는지 이해 하려면 **편집**을 선택 하 여 편집 모드로 전환 해야 합니다.

![통합 문서에 있는 편집 단추의 스크린샷](./media/workbooks-overview/edit.png)

편집 모드로 전환 하면 통합 문서의 각 개별 요소에 해당 하는 여러 개의 **편집** 상자가 오른쪽에 표시 됩니다.

![편집 단추의 스크린샷](./media/workbooks-overview/edit-mode.png)

요청 데이터의 그리드 바로 아래에 있는 편집 단추를 선택 하는 경우 통합 문서의이 부분이 Application Insights 리소스의 데이터에 대해 쿼리 하는 Kusto 구성 된 것을 볼 수 있습니다.

![기본 Kusto 쿼리 스크린샷](./media/workbooks-overview/kusto.png)

오른쪽의 다른 **편집** 단추를 클릭 하면 markdown 기반 [텍스트 상자](workbooks-text-visualizations.md), [매개 변수 선택](workbooks-parameters.md) UI 요소 및 기타 [차트/시각화 형식과](#visualizations)같은 통합 문서를 구성 하는 몇 가지 핵심 구성 요소가 표시 됩니다. 

편집 모드에서 미리 작성 된 템플릿을 탐색 한 다음 요구 사항에 맞게 수정 하 고 사용자 지정 통합 문서를 저장 하는 것이 Azure Monitor 통합 문서를 사용 하 여 가능한 작업에 대 한 학습을 시작 하는 좋은 방법입니다.

## <a name="pinning-visualizations"></a>시각화 고정

통합 문서에 있는 텍스트, 쿼리 및 메트릭 단계는 통합 문서가 고정 모드에 있는 동안 해당 항목의 고정 단추를 사용 하 여 고정 하거나 통합 문서 작성자가 해당 요소에 대 한 설정을 사용 하도록 설정 하 여 고정 아이콘이 표시 되도록 할 수 있습니다. 

Pin 모드에 액세스 하려면 **편집** 을 클릭 하 여 편집 모드로 전환 하 고 위쪽 막대에서 파란색 핀 아이콘을 선택 합니다. 그러면 개별 핀 아이콘이 화면 오른쪽의 해당 통합 문서 파트의 *편집* 상자 위에 표시 됩니다.

![고정 환경](./media/workbooks-overview/pin-experience.png)

> [!NOTE]
> 통합 문서를 수정 하는 경우 통합 문서의 상태는 pin의 시간에 저장 되 고 대시보드의 고정 된 통합 문서는 업데이트 되지 않습니다. 고정 된 통합 문서 파트를 업데이트 하려면 해당 파트를 삭제 하 고 다시 고정 해야 합니다.

## <a name="dashboard-time-ranges"></a>대시보드 시간 범위

고정 된 통합 문서 쿼리 파트는 고정 된 항목이 *시간 범위* 매개 변수를 사용 하도록 구성 된 경우 대시보드의 시간 범위를 고려 합니다. 대시보드의 시간 범위 값은 시간 범위 매개 변수의 값으로 사용 되 고, 대시보드 시간 범위를 변경 하면 고정 된 항목이 업데이트 됩니다. 고정 된 부분이 대시보드의 시간 범위를 사용 하는 경우 시간 범위가 변경 될 때마다 대시보드의 시간 범위를 표시 하는 고정 된 파트 업데이트의 부제목이 표시 됩니다. 

또한 시간 범위 매개 변수를 사용 하 여 고정 된 통합 문서 부분은 대시보드의 시간 범위에 따라 결정 되는 속도에서 자동으로 새로 고쳐집니다. 쿼리가 마지막으로 실행 된 시간이 고정 된 부분의 부제목에 표시 됩니다.

고정 된 단계에 명시적으로 설정 된 시간 범위 (시간 범위 매개 변수를 사용 하지 않음)가 있는 경우 대시보드의 설정에 관계 없이 대시보드에 항상 해당 시간 범위가 사용 됩니다. 고정 된 부분의 부제목은 대시보드의 시간 범위를 표시 하지 않으며 대시보드는 쿼리를 자동으로 새로 고치지 않습니다. 부제목은 쿼리가 마지막으로 실행 된 시간을 표시 합니다.

> [!NOTE]
> *병합* 데이터 원본을 사용 하는 쿼리는 현재 대시보드에 고정할 때 지원 되지 않습니다.

## <a name="sharing-workbook-templates"></a>통합 문서 템플릿 공유

사용자 고유의 통합 문서 템플릿을 만든 후에는 더 광범위 한 커뮤니티와 공유할 수 있습니다. 자세히 알아보고 기본 Azure Monitor 갤러리 보기에 포함 되지 않은 다른 템플릿을 탐색 하려면 [GitHub 리포지토리](https://github.com/Microsoft/Application-Insights-Workbooks/blob/master/README.md)를 방문 하세요. 기존 통합 문서를 찾아보려면 GitHub의 [통합 문서 라이브러리](https://github.com/microsoft/Application-Insights-Workbooks/tree/master/Workbooks) 를 참조 하세요.

## <a name="next-step"></a>다음 단계

* 통합 문서에 대 한 자세한 내용은 다양 한 기능을 갖춘 시각화 옵션을 [시작](#visualizations) 하세요.
* 통합 문서 리소스에 대 한 액세스를 [제어](workbooks-access-control.md) 하 고 공유 합니다.