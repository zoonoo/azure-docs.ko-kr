---
title: 데이터 흐름
description: Azure Synapse Analytics의 데이터 흐름 개요
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: synapse-analytics
ms.subservice: pipeline
ms.topic: conceptual
ms.custom: references_regions
ms.date: 12/16/2020
ms.openlocfilehash: 525fed571576f6d5fc49d68abbbe26e46cf3291e
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122537391"
---
# <a name="data-flows-in-azure-synapse-analytics"></a>Azure Synapse Analytics의 데이터 흐름

## <a name="what-are-data-flows"></a>데이터 흐름이란?

데이터 흐름은 Azure Synapse Analytics에서 시각적으로 디자인된 데이터 변환입니다. 데이터 흐름을 통해 데이터 엔지니어는 코드를 작성하지 않고도 데이터 변환 논리를 개발할 수 있습니다. 결과 데이터 흐름은 스케일 아웃 Apache Spark 클러스터를 사용하는 Azure Synapse Analytics 파이프라인 내에서 작업으로 실행됩니다. 데이터 흐름 작업은 기존 Azure Synapse Analytics 일정, 제어, 흐름, 모니터링 기능을 사용해 운용할 수 있습니다.

데이터 흐름은 코딩 없이도 완전히 시각적인 환경을 제공합니다. 데이터 흐름은 스케일 아웃 데이터 처리를 위해 Synapse로 관리되는 실행 클러스터에서 실행됩니다. Azure Synapse Analytics는 모든 코드 변환, 경로 최적화, 데이터 흐름 작업의 실행을 처리합니다.

## <a name="getting-started"></a>시작

데이터 흐름은 Synapse 스튜디오의 개발 창에서 만들어집니다. 데이터 흐름을 만들려면 **개발** 옆에 있는 더하기 기호를 선택한 다음, **데이터 흐름** 을 선택합니다. 

![새 데이터 흐름](media/data-flow/new-data-flow.png)

이 작업을 수행하면 변환 논리를 만들 수 있는 데이터 흐름 캔버스로 이동합니다. **원본 추가** 를 선택하여 원본 변환 구성을 시작합니다. 자세한 내용은 [원본 변환](../data-factory/data-flow-source.md?context=/azure/synapse-analytics/context/context)을 참조하세요.

## <a name="authoring-data-flows"></a>작성 데이터 흐름

데이터 흐름에는 변환 논리를 쉽게 빌드할 수 있도록 디자인된 고유한 제작 캔버스가 있습니다. 데이터 흐름 캔버스는 위쪽 막대, 그래프, 구성 패널의 세 부분으로 구분됩니다. 

![위쪽 막대, 그래프, 구성 패널이 있는 데이터 흐름 캔버스를 보여주는 스크린샷입니다.](media/data-flow/canvas-1.png)

### <a name="graph"></a>그래프

그래프는 변환 스트림을 표시합니다. 원본 데이터가 하나 이상의 싱크로 이동함에 따라 원본 데이터의 계보를 보여줍니다. 새 원본을 추가하려면 **원본 추가** 를 선택합니다. 새 변환을 추가하려면 기존 변환의 오른쪽 아래에 있는 더하기 기호를 선택합니다. [데이터 흐름 그래프를 관리](../data-factory/concepts-data-flow-manage-graph.md?context=/azure/synapse-analytics/context/context)하는 방법에 관해 자세히 알아봅니다.

![검색 텍스트 상자가 있는 캔버스의 그래프 부분을 보여주는 스크린샷입니다.](media/data-flow/canvas-2.png)

### <a name="configuration-panel"></a>구성 패널

구성 패널에는 현재 선택된 변환과 관련된 설정이 표시됩니다. 선택된 변환이 없는 경우 데이터 흐름이 표시됩니다. 전체 데이터 흐름 구성에서는 **매개 변수** 탭을 통해 매개 변수를 추가할 수 있습니다. 자세한 내용은 [데이터 흐름 매개 변수](../data-factory/parameters-data-flow.md?context=/azure/synapse-analytics/context/context)를 참조하세요.

각 변환에는 네 개 이상의 구성 탭이 포함됩니다.

#### <a name="transformation-settings"></a>변환 설정

각 변환의 구성 창에 있는 첫 번째 탭에는 해당 변환과 관련된 설정이 포함됩니다. 자세한 내용은 해당 변형의 설명서 페이지를 참조하세요.

![원본 설정 탭](media/data-flow/source-1.png)

#### <a name="optimize"></a>최적화

**최적화** 탭에는 파티션 구성표를 구성하기 위한 설정이 포함됩니다. 데이터 흐름을 최적화하는 방법에 관해 자세히 알아보려면 [매핑 데이터 흐름 성능 가이드](../data-factory/concepts-data-flow-performance.md?context=/azure/synapse-analytics/context/context)를 참조하세요.

![최적화 탭을 표시하는 스크린샷](media/data-flow/optimize.png)

#### <a name="inspect"></a>검사

**검사** 탭에서는 변환 중인 데이터 스트림의 메타데이터를 볼 수 있습니다. 열 개수, 변경된 열, 추가된 열, 데이터 형식, 열 순서 및 열 참조를 볼 수 있습니다. **검사** 는 메타데이터의 읽기 전용 보기입니다. **검사** 창에서 메타데이터를 보기 위해 디버그 모드를 사용하도록 설정하지 않아도 됩니다.

![검사 탭](media/data-flow/inspect.png)

변환을 통해 데이터의 셰이프를 변경하면 **검사** 창에서 메타데이터가 흐름을 변경하는 것을 확인할 수 있습니다. 원본 변환에 정의된 스키마가 없으면 메타데이터가 **검사** 창에 표시되지 않습니다. 스키마 드리프트 시나리오에서는 메타데이터가 없는 것이 일반적입니다.

#### <a name="data-preview"></a>데이터 미리 보기

디버그 모드가 켜지면 **데이터 미리 보기** 탭에서는 각 변환 시 데이터의 대화형 스냅샷을 제공합니다. 자세한 내용은 [디버그 모드의 데이터 미리 보기](../data-factory/concepts-data-flow-debug-mode.md?context=/azure/synapse-analytics/context/context#data-preview)를 참조하세요.

### <a name="top-bar"></a>위쪽 막대

위쪽 막대에는 유효성 검사 및 디버그 설정과 같이 전체 데이터 흐름에 영향을 주는 작업이 포함됩니다. 변환 논리의 기본 JSON 코드 및 데이터 흐름 스크립트도 볼 수 있습니다.

## <a name="available-transformations"></a>사용 가능한 변환

[매핑 데이터 흐름 변환 개요](../data-factory/data-flow-transformation-overview.md?context=/azure/synapse-analytics/context/context)를 확인하여 사용 가능한 변환 목록을 가져옵니다.

## <a name="data-flow-activity"></a>데이터 흐름 작업

데이터 흐름은 [데이터 흐름 작업](../data-factory/control-flow-execute-data-flow-activity.md?context=/azure/synapse-analytics/context/context)을 사용하여 Azure Synapse Analytics 파이프라인 내에서 조작 가능합니다. 사용자는 매개 변수 값을 사용하고 전달할 통합 런타임만 지정하면 됩니다. 자세한 내용은 [Azure 통합 런타임](../data-factory/concepts-integration-runtime.md?context=/azure/synapse-analytics/context/context#azure-integration-runtime)을 참조하세요.

## <a name="debug-mode"></a>디버그 모드

디버그 모드에서는 데이터 흐름을 빌드하고 디버그하는 동안 각 변환 단계의 결과를 대화형으로 볼 수 있습니다. 디버그 세션은 데이터 흐름 논리를 빌드할 경우와 데이터 흐름 작업을 사용하여 파이프라인 디버그 실행을 실행하는 경우에 둘 다 사용할 수 있습니다. 자세히 알아보려면 [디버그 모드 설명서](../data-factory/concepts-data-flow-debug-mode.md?context=/azure/synapse-analytics/context/context)를 참조하세요.

## <a name="monitoring-data-flows"></a>데이터 흐름 모니터링

데이터 흐름은 기존 Azure Synapse Analytics 모니터링 기능과 통합됩니다. 데이터 흐름 모니터링 출력을 이해하는 방법을 알아보려면 [매핑 데이터 흐름 모니터링](../data-factory/concepts-data-flow-monitoring.md?context=/azure/synapse-analytics/context/context)을 참조하세요.

Azure Synapse Analytics 팀은 비즈니스 논리를 빌드한 후 데이터 흐름의 실행 시간을 최적화하는 데 도움이 되는 [성능 조정 가이드](../data-factory/concepts-data-flow-performance.md?context=/azure/synapse-analytics/context/context)를 만들었습니다.

## <a name="next-steps"></a>다음 단계

* [원본 변환](../data-factory/data-flow-source.md?context=/azure/synapse-analytics/context/context)을 만드는 방법을 알아봅니다.
* [디버그 모드](../data-factory/concepts-data-flow-debug-mode.md?context=/azure/synapse-analytics/context/context)에서 데이터 흐름을 빌드하는 방법을 알아봅니다.
