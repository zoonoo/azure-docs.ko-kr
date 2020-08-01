---
title: 데이터 흐름 매핑
description: Azure Data Factory에서 데이터 흐름을 매핑하는 방법에 대 한 개요
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 06/09/2020
ms.openlocfilehash: 850879675d4554329f24c86f2ac28660b303084c
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87475569"
---
# <a name="what-are-mapping-data-flows"></a>데이터 흐름 매핑이란?

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

데이터 흐름 매핑은 Azure Data Factory에서 시각적으로 디자인 된 데이터 변환입니다. 데이터 흐름을 통해 데이터 엔지니어는 코드를 작성 하지 않고도 그래픽 데이터 변환 논리를 개발할 수 있습니다. 결과 데이터 흐름은 확장 Apache Spark 클러스터를 사용 하는 Azure Data Factory 파이프라인 내에서 작업으로 실행 됩니다. 데이터 흐름 작업은 기존 Data Factory 예약, 제어, 흐름 및 모니터링 기능을 통해 사용할 수 있습니다.

데이터 흐름을 매핑하면 코딩 없이도 완전 한 시각적 환경을 제공 합니다. 데이터 흐름은 확장 된 데이터 처리를 위해 실행 클러스터에서 실행 됩니다. Azure Data Factory는 모든 코드 변환, 경로 최적화 및 데이터 흐름 작업의 실행을 처리 합니다.

![아키텍처](media/data-flow/adf-data-flows.png "Architecture")

## <a name="getting-started"></a>시작

데이터 흐름을 만들려면 **팩터리 리소스**아래에서 더하기 기호를 선택 하 고 **데이터 흐름**을 선택 합니다. 

![새 데이터 흐름](media/data-flow/newdataflow2.png "새 데이터 흐름")

이 작업을 수행 하면 변환 논리를 만들 수 있는 데이터 흐름 캔버스로 이동 합니다. 원본 **추가** 를 선택 하 여 원본 변환 구성을 시작 합니다. 자세한 내용은 [원본 변환](data-flow-source.md)을 참조 하세요.

## <a name="data-flow-canvas"></a>데이터 흐름 캔버스

데이터 흐름 캔버스는 위쪽 막대, 그래프 및 구성 패널의 세 부분으로 구분 됩니다. 

![캔버스](media/data-flow/canvas1.png "캔버스")

### <a name="graph"></a>Graph

그래프는 변환 스트림을 표시 합니다. 하나 이상의 싱크로 전달 되는 원본 데이터의 계보를 보여 줍니다. 새 원본을 추가 하려면 **원본 추가**를 선택 합니다. 새 변환을 추가 하려면 기존 변환의 오른쪽 아래에 있는 더하기 기호를 선택 합니다.

![캔버스](media/data-flow/canvas2.png "캔버스")

### <a name="azure-integration-runtime-data-flow-properties"></a>Azure integration runtime 데이터 흐름 속성

![디버그 단추](media/data-flow/debugbutton.png "디버그 단추")

ADF에서 데이터 흐름에 대 한 작업을 시작 하는 경우 브라우저 UI의 맨 위에 있는 데이터 흐름에 대 한 "디버그" 스위치를 설정 하는 것이 좋습니다. 그러면 대화형 디버깅, 데이터 미리 보기 및 파이프라인 디버그 실행에 사용할 Spark 클러스터가 회전 합니다. 사용자 지정 [Azure Integration Runtime](concepts-integration-runtime.md)를 선택 하 여 사용 되는 클러스터의 크기를 설정할 수 있습니다. 디버그 세션은 마지막 데이터 미리 보기 또는 마지막 디버그 파이프라인 실행 후 최대 60 분 동안 활성 상태로 유지 됩니다.

데이터 흐름 활동을 사용 하 여 파이프라인을 운영 ADF는 "실행" 속성의 [활동과](control-flow-execute-data-flow-activity.md) 연결 된 Azure Integration Runtime를 사용 합니다.

기본 Azure Integration Runtime는 데이터를 미리 보고 최소한의 비용으로 디버그 파이프라인을 신속 하 게 실행할 수 있는 작은 4 코어 단일 작업자 노드 클러스터입니다. 큰 데이터 집합에 대해 작업을 수행 하는 경우 더 큰 Azure IR 구성을 설정 합니다.

Azure IR 데이터 흐름 속성에서 TTL을 설정 하 여 Vm (클러스터 리소스)의 풀을 유지 하도록 ADF에 지시할 수 있습니다. 이 작업을 수행 하면 후속 작업에서 작업 실행 속도가 빨라집니다.

#### <a name="azure-integration-runtime-and-data-flow-strategies"></a>Azure 통합 런타임 및 데이터 흐름 전략

##### <a name="execute-data-flows-in-parallel"></a>병렬로 데이터 흐름 실행

파이프라인에서 병렬로 데이터 흐름을 실행 하는 경우 ADF는 각 작업에 연결 된 Azure Integration Runtime의 설정에 따라 각 작업 실행에 대해 별도의 Spark 클러스터를 회전 합니다. ADF 파이프라인에서 병렬 실행을 디자인 하려면 UI에서 선행 제약 조건 없이 데이터 흐름 작업을 추가 합니다.

이러한 세 가지 옵션 중에서이 옵션은 가장 짧은 시간에 실행 될 수 있습니다. 그러나 각 병렬 데이터 흐름은 별도의 클러스터에서 동시에 실행 되므로 이벤트 순서는 비결 정적입니다.

파이프라인 내에서 데이터 흐름 작업을 병렬로 실행 하는 경우 TTL을 사용 하지 않는 것이 좋습니다. 이 작업은 동일한 Azure Integration Runtime를 사용 하 여 데이터 흐름을 동시에 실행 하면 데이터 팩터리에 대 한 여러 웜 풀 인스턴스가 생성 되기 때문입니다.

##### <a name="overload-single-data-flow"></a>단일 데이터 흐름 오버 로드

모든 논리를 단일 데이터 흐름 내에 배치 하면 ADF는 단일 Spark 클러스터 인스턴스에서 동일한 작업 실행 컨텍스트를 실행 합니다.

비즈니스 규칙 및 비즈니스 논리를 함께 jumbled 수 있으므로이 옵션을 수행 하 고 문제를 해결 하는 것이 더 어려울 수 있습니다. 이 옵션은 과도 한 재사용도 제공 하지 않습니다.

##### <a name="execute-data-flows-sequentially"></a>순차적으로 데이터 흐름 실행

파이프라인에서 데이터 흐름 작업을 순서 대로 실행 하 고 Azure IR 구성에 TTL을 설정 하는 경우 ADF는 계산 리소스 (Vm)를 다시 사용 하 여 후속 실행 시간을 단축 합니다. 각 실행에 대해 새 Spark 컨텍스트를 계속 받게 됩니다.

이러한 세 가지 옵션 중에서이 작업을 수행 하는 데는 종단 간 실행 시간이 오래 걸릴 수 있습니다. 그러나 각 데이터 흐름 단계에서 논리적 작업을 명확 하 게 분리 합니다.

### <a name="configuration-panel"></a>구성 패널

구성 패널에는 현재 선택한 변환과 관련 된 설정이 표시 됩니다. 선택 된 변환이 없는 경우 데이터 흐름이 표시 됩니다. 전체 데이터 흐름 구성에서는 **일반** 탭에서 이름 및 설명을 편집 하거나 **매개 변수** 탭을 통해 매개 변수를 추가할 수 있습니다. 자세한 내용은 [데이터 흐름 매개 변수 매핑](parameters-data-flow.md)을 참조 하세요.

각 변환에는 네 개 이상의 구성 탭이 포함 되어 있습니다.

#### <a name="transformation-settings"></a>변환 설정

각 변환의 구성 창에 있는 첫 번째 탭에는 해당 변환과 관련 된 설정이 포함 되어 있습니다. 자세한 내용은 해당 변형의 설명서 페이지를 참조 하세요.

![원본 설정 탭](media/data-flow/source1.png "원본 설정 탭")

#### <a name="optimize"></a>최적화

**최적화** 탭에는 파티션 구성표를 구성 하는 설정이 포함 되어 있습니다. 데이터 흐름을 최적화 하는 방법에 대 한 자세한 내용은 [데이터 흐름 매핑 성능 가이드](concepts-data-flow-performance.md)를 참조 하세요.

![Optimize](media/data-flow/optimize.png "최적화")

#### <a name="inspect"></a>검사

**검사** 탭은 변환 중인 데이터 스트림의 메타 데이터에 대 한 뷰를 제공 합니다. 열 개수, 변경 된 열, 추가 된 열, 데이터 형식, 열 순서 및 열 참조를 볼 수 있습니다. **검사** 는 메타 데이터의 읽기 전용 뷰입니다. **검사** 창에서 메타 데이터를 보려면 디버그 모드를 사용 하도록 설정 하지 않아도 됩니다.

![검사](media/data-flow/inspect1.png "검사")

변환을 통해 데이터의 모양을 변경 하면 **검사** 창에 메타 데이터 변경 흐름이 표시 됩니다. 원본 변환에 정의 된 스키마가 없는 경우 **검사** 창에 메타 데이터가 표시 되지 않습니다. 메타 데이터 부족은 스키마 드리프트 시나리오에서 일반적입니다.

#### <a name="data-preview"></a>데이터 미리 보기

디버그 모드가 on 이면 **데이터 미리 보기** 탭에서 각 변환 시 데이터의 대화형 스냅숏을 제공 합니다. 자세한 내용은 [디버그 모드에서 데이터 미리 보기](concepts-data-flow-debug-mode.md#data-preview)를 참조 하세요.

### <a name="top-bar"></a>위쪽 막대

위쪽 막대에는 저장 및 유효성 검사와 같은 전체 데이터 흐름에 영향을 주는 동작이 포함 되어 있습니다. 그래프 **표시** 및 **그래프 숨기기** 단추를 사용 하 여 그래프와 구성 모드 사이를 전환할 수도 있습니다.

![그래프 숨기기](media/data-flow/hideg.png "그래프 숨기기")

그래프를 숨기면 **이전** 및 **다음** 단추를 통해 수평 변환 노드를 탐색할 수 있습니다.

![이전 및 다음 단추](media/data-flow/showhide.png "이전 및 다음 단추")

## <a name="next-steps"></a>다음 단계

* [원본 변환을](data-flow-source.md)만드는 방법을 알아봅니다.
* [디버그 모드](concepts-data-flow-debug-mode.md)에서 데이터 흐름을 빌드하는 방법에 대해 알아봅니다.
