---
title: Azure Monitor 통합 문서 개요
description: 미리 빌드되고 사용자 지정 매개 변수가 있는 통합 문서를 사용하여 복잡한 보고 간소화
author: mrbullwinkle
manager: carmonm
services: azure-monitor
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 29e675f3ae35df9211f58d45ad8450566d67a588
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658237"
---
# <a name="azure-monitor-workbooks"></a>Azure Monitor 통합 문서

통합 문서는 Azure 포털 내에서 데이터 분석 및 풍부한 시각적 보고서를 만들기 위한 유연한 캔버스를 제공합니다. 이를 통해 Azure 전체의 여러 데이터 원본을 탭하고 통합 대화형 환경으로 결합할 수 있습니다. 

## <a name="data-sources"></a>데이터 원본

통합 문서는 Azure 내의 여러 원본에서 데이터를 쿼리할 수 있습니다. 통합 문서 작성자는 이 데이터를 변환하여 기본 구성 요소의 가용성, 성능, 사용 및 전반적인 상태여부에 대한 통찰력을 제공할 수 있습니다. 예를 들어 가상 시스템의 성능 로그를 분석하여 CPU가 많거나 메모리가 부족한 인스턴스를 식별하고 대화형 보고서에 결과를 그리드로 표시합니다.
  
그러나 통합 문서의 진정한 힘은 단일 보고서 내에서 서로 다른 소스의 데이터를 결합하는 기능입니다. 이를 통해 복합 리소스 보기 또는 리소스 간에 조인을 생성할 수 있으므로 불가능한 풍부한 데이터와 통찰력을 얻을 수 있습니다.

통합 문서는 현재 다음 데이터 원본과 호환됩니다.

* [로그](workbooks-data-sources.md#logs)
* [메트릭](workbooks-data-sources.md#metrics)
* [Azure 리소스 그래프](workbooks-data-sources.md#azure-resource-graph)
* [경고(미리 보기)](workbooks-data-sources.md#alerts-preview)
* [워크로드 상태(미리 보기)](workbooks-data-sources.md#workload-health-preview)
* [Azure 리소스 상태(미리 보기)](workbooks-data-sources.md#azure-resource-health)
* [Azure 데이터 탐색기(미리 보기)](workbooks-data-sources.md#azure-data-explorer-preview)

## <a name="visualizations"></a>시각화

통합 문서는 데이터를 시각화할 수 있는 다양한 기능을 제공합니다. 각 시각화 유형에 대한 자세한 예제는 아래 예제 링크를 참조할 수 있습니다.

* [텍스트](workbooks-visualizations.md#text)
* [차트](workbooks-visualizations.md#charts)
* [격자](workbooks-visualizations.md#grids)
* [타일](workbooks-visualizations.md#tiles)
* [Trees](workbooks-visualizations.md#trees)
* [그래프](workbooks-visualizations.md#graphs)

![통합 문서 시각화 예제](./media/workbooks-overview/visualizations.png)

## <a name="getting-started"></a>시작

통합 문서 환경을 탐색하려면 먼저 Azure Monitor 서비스로 이동합니다. 이 작업은 Azure 포털의 검색 상자에 **모니터를** 입력하여 수행할 수 있습니다.

그런 다음 **통합 문서(미리 보기)를 선택합니다.**

![통합 문서 미리 보기 단추의 스크린샷이 빨간색 상자에 강조 표시되어 있습니다.](./media/workbooks-overview/workbooks-preview.png)

### <a name="gallery"></a>갤러리

이렇게 하면 통합 문서 갤러리로 이동합니다.

![Azure 모니터 통합 문서 갤러리 보기의 스크린샷](./media/workbooks-overview/gallery.png)

### <a name="workbooks-versus-workbook-templates"></a>통합 문서 와 통합 문서 템플릿

_통합 문서를_ 녹색으로 표시하고 여러 _통합 문서 템플릿을 자주색으로_ 볼 수 있습니다. 템플릿은 여러 사용자와 팀에서 유연하게 재사용할 수 있도록 설계된 선별된 보고서역할을 합니다. 템플릿을 열면 템플릿의 내용으로 채워진 일시적인 통합 문서를 만듭니다. 

템플릿 기반 통합 문서의 매개 변수를 조정하고 동료의 향후 보고 환경을 위반할 까봐 두려워하지 않고 분석을 수행할 수 있습니다. 템플릿을 열고 일부 조정한 다음 저장 아이콘을 선택하면 템플릿을 통합 문서로 저장한 다음 원본 템플릿을 그대로 두고 녹색으로 표시됩니다. 

템플릿은 저장된 통합 문서와도 다릅니다. 통합 문서를 저장하면 연결된 Azure 리소스 관리자 리소스가 만들어지지만 템플릿을 열 때 생성된 일시적인 통합 책에는 연결된 고유 리소스가 없습니다. 통합 문서에서 액세스 제어를 관리하는 방법에 대한 자세한 내용은 [통합 문서 액세스 제어 문서를](workbooks-access-control.md)참조하십시오.

### <a name="exploring-a-workbook-template"></a>통합 문서 템플릿 탐색

**응용 프로그램 오류 분석을** 선택하여 기본 응용 프로그램 통합 문서 템플릿 중 하나를 확인합니다.

![응용 프로그램 오류 분석 템플릿의 스크린샷](./media/workbooks-overview/failure-analysis.png)

앞서 설명한 대로 템플릿을 열면 상호 작용할 수 있는 임시 통합 문서를 만듭니다. 기본적으로 통합 문서 읽기 모드에서 열리며 원래 템플릿 작성자가 만든 의도된 분석 경험에 대한 정보만 표시합니다.

이 특정 통합 문서의 경우 환경은 대화형입니다. 구독, 대상 앱 및 표시할 데이터의 시간 범위를 조정할 수 있습니다. 이러한 선택을 한 후에는 HTTP 요청의 그리드도 대화형으로 전환되어 개별 행을 선택하면 보고서 맨 아래에 있는 두 차트에서 렌더링되는 데이터가 변경됩니다.

### <a name="editing-mode"></a>편집 모드

이 통합 문서 템플릿을 함께 넣는 방법을 이해하려면 **편집을**선택하여 편집 모드로 바꿔야 합니다. 

![응용 프로그램 오류 분석 템플릿의 스크린샷](./media/workbooks-overview/edit.png)

편집 모드로 전환하면 통합 문서의 각 개별 측면과 해당하는 여러 **편집** 상자가 오른쪽에 나타납니다.

![편집 버튼의 스크린샷](./media/workbooks-overview/edit-mode.png)

요청 데이터의 격자 바로 아래에서 편집 단추를 선택하면 통합 문서의 이 부분이 Application Insights 리소스의 데이터에 대한 Kusto 쿼리로 구성되는 것을 볼 수 있습니다.

![기본 Kusto 쿼리의 스크린샷](./media/workbooks-overview/kusto.png)

오른쪽에 있는 다른 **편집** 단추를 클릭하면 마크다운 기반 [텍스트 상자,](workbooks-visualizations.md#text) [매개 변수 선택](workbooks-parameters.md) UI 요소 및 기타 [차트/시각화 유형과](workbooks-visualizations.md)같은 통합 문서를 구성하는 여러 핵심 구성 요소가 표시됩니다. 

편집 모드에서 미리 빌드된 템플릿을 탐색한 다음 필요에 맞게 수정하고 사용자 지정 통합 문서를 저장하는 것은 Azure Monitor 통합 문서를 사용하여 가능한 사항에 대해 알아볼 수 있는 훌륭한 방법입니다.

## <a name="pinning-visualizations"></a>시각화 고정

통합 문서의 텍스트, 쿼리 및 메트릭 단계는 통합 문서의 핀 모드에 있는 동안 또는 통합 문서 작성자가 해당 요소에 대한 설정을 활성화하여 핀 아이콘을 표시하도록 설정한 경우 해당 항목의 핀 단추를 사용하여 고정할 수 있습니다. 

핀 모드에 액세스하려면 **편집을** 클릭하여 편집 모드로 전환하고 상단 막대에서 파란색 핀 아이콘을 선택합니다. 그러면 개별 핀 아이콘이 화면 오른쪽에 있는 각 통합 문서 파트의 *편집* 상자 위에 표시됩니다.

![핀 경험](./media/workbooks-overview/pin-experience.png)

> [!NOTE]
> 통합 문서의 상태는 핀 시에 저장되며 기본 통합 문서를 수정하면 대시보드에 고정된 통합 문서는 업데이트되지 않습니다. 고정된 통합 문서 부분을 업데이트하려면 해당 부품을 삭제하고 다시 고정해야 합니다.

## <a name="dashboard-time-ranges"></a>대시보드 시간 범위

고정된 통합 문서 쿼리 부분은 고정된 항목이 *시간 범위* 매개 변수를 사용하도록 구성된 경우 대시보드의 시간 범위를 존중합니다. 대시보드의 시간 범위 값은 시간 범위 매개 변수의 값으로 사용되며 대시보드 시간 범위가 변경되면 고정된 항목이 업데이트됩니다. 고정된 부품이 대시보드의 시간 범위를 사용하는 경우 시간 범위가 변경될 때마다 고정된 부품 업데이트의 자막이 표시되어 대시보드의 시간 범위를 표시합니다. 

또한 시간 범위 매개 변수를 사용하여 고정된 통합 문서 부분은 대시보드의 시간 범위에 의해 결정된 속도로 자동으로 새로 고침됩니다. 마지막으로 쿼리가 실행된 시간은 고정된 부품의 자막에 나타납니다.

고정된 단계에 명시적으로 설정된 시간 범위(시간 범위 매개 변수를 사용하지 않음)가 있는 경우 해당 시간 범위는 대시보드 설정에 관계없이 항상 대시보드에 사용됩니다. 고정된 부품의 자막에는 대시보드의 시간 범위가 표시되지 않으며 쿼리는 대시보드에서 자동으로 새로 고쳐지지 않습니다. 자막은 쿼리가 마지막으로 실행된 시간을 표시합니다.

> [!NOTE]
> *대시보드에* 고정할 때 병합 데이터 원본을 사용하는 쿼리는 현재 지원되지 않습니다.

## <a name="sharing-workbook-templates"></a>통합 문서 템플릿 공유

사용자 고유의 통합 문서 템플릿을 만들기 시작하면 더 넓은 커뮤니티와 공유할 수 있습니다. 자세한 내용을 알아보고 기본 Azure Monitor 갤러리 보기의 일부가 아닌 다른 템플릿을 탐색하려면 [GitHub 리포지토리를](https://github.com/Microsoft/Application-Insights-Workbooks/blob/master/README.md)방문하십시오. 기존 통합 문서를 찾아보시려면 GitHub의 [통합 문서 라이브러리를](https://github.com/microsoft/Application-Insights-Workbooks/tree/master/Workbooks) 방문하십시오.

## <a name="next-step"></a>다음 단계

* 통합 문서에 대해 자세히 알아보고 다양한 다양한 시각화 옵션을 [알아보세요.](workbooks-visualizations.md)
* 통합 문서 리소스에 대한 액세스를 [제어하고](workbooks-access-control.md) 공유합니다.
