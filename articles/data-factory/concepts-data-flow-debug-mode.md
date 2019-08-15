---
title: Azure Data Factory Mapping Data Flow 디버그 모드
description: 데이터 흐름을 빌드할 때 대화형 디버그 세션 시작
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: 945d123c0901722a527e7cc8181c91f09e4e95ec
ms.sourcegitcommit: fe50db9c686d14eec75819f52a8e8d30d8ea725b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2019
ms.locfileid: "69014518"
---
# <a name="mapping-data-flow-debug-mode"></a>Mapping Data Flow 디버그 모드

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

## <a name="overview"></a>개요

Azure Data Factory 매핑 데이터 흐름의 디버그 모드는 디자인 화면 맨 위에 있는 "데이터 흐름 디버그" 단추로 전환할 수 있습니다. 데이터 흐름을 디자인할 때 디버그 모드를 설정 하면 데이터 흐름을 빌드하고 디버그 하는 동안 대화형으로 데이터 셰이프 변환을 볼 수 있습니다. 디버그 세션은 데이터 흐름의 파이프라인 디버그 실행 중 뿐만 아니라 데이터 흐름 디자인 세션에서 모두 사용할 수 있습니다.

![디버그 단추](media/data-flow/debugbutton.png "디버그 단추")

디버그 모드를 설정 하면 활성 Spark 클러스터로 데이터 흐름을 대화형으로 작성할 수 있습니다. Azure Data Factory에서 디버그를 끄면 세션이 닫힙니다. 디버그 세션이 켜진 시간 동안 Azure Databricks에서 부과되는 시간당 요금에 유의해야 합니다.

대부분의 경우에는 Azure Data Factory 작업을 게시 하기 전에 비즈니스 논리의 유효성을 검사 하 고 데이터 변환을 볼 수 있도록 디버그 모드에서 데이터 흐름을 작성 하는 것이 좋습니다. 파이프라인 패널의 "디버그" 단추를 사용 하 여 파이프라인의 데이터 흐름을 테스트 합니다.

> [!NOTE]
> Data Factory 도구 모음에서 디버그 모드 표시등이 녹색 인 동안 60 분 ttl이 있는 일반 계산의 8 개 코어/시간에 대 한 데이터 흐름 디버그 속도로 요금이 청구 됩니다. 

## <a name="cluster-status"></a>클러스터 상태

클러스터가 디버그 될 준비가 되 면 디자인 화면 위쪽의 클러스터 상태 표시기가 녹색으로 바뀝니다. 클러스터가 이미 웜 상태이면 녹색 표시기가 거의 즉시 나타납니다. 디버그 모드로 전환 했을 때 클러스터가 아직 실행 되지 않은 경우 클러스터를 실행 하는 데 5-7 분 정도 기다려야 합니다. 표시기가 준비 될 때까지 회전 합니다.

디버깅이 완료 되 면 Azure Databricks 클러스터가 종료 될 수 있도록 디버그 스위치를 해제 하 고 디버그 작업에 대해 더 이상 요금이 청구 되지 않습니다.

## <a name="debug-settings"></a>디버그 설정

데이터 흐름 캔버스 도구 모음에서 "디버그 설정"을 클릭 하 여 디버그 설정을 편집할 수 있습니다. 여기에서 각 소스 변환에 사용할 행 제한 또는 파일 원본을 선택할 수 있습니다. 이 설정의 행 제한은 현재 디버그 세션에만 적용 됩니다. SQL DW 원본에 사용할 준비 연결 된 서비스를 선택할 수도 있습니다. 

![디버그 설정](media/data-flow/debug-settings.png "디버그 설정")

데이터 흐름 또는 참조 된 데이터 집합에 매개 변수가 있는 경우 **매개 변수** 탭을 선택 하 여 디버깅 중에 사용할 값을 지정할 수 있습니다.

![디버그 설정 매개 변수](media/data-flow/debug-settings2.png "디버그 설정 매개 변수")

## <a name="data-preview"></a>데이터 미리 보기

디버그를 켜면 데이터 미리 보기 탭이 맨 아래 패널에 표시됩니다. 디버그 모드를 켜지 않으면 각 변환에 들어오고 나가는 현재 메타데이터만 Data Flow의 검사 탭에 표시됩니다. 데이터 미리 보기는 디버그 설정에서 제한으로 설정한 행의 수만 쿼리 합니다. **새로 고침** 을 클릭 하 여 데이터 미리 보기를 가져옵니다.

![데이터 미리 보기](media/data-flow/datapreview.png "데이터 미리 보기")

Data Flow에서 디버그 모드로 실행하는 경우에는 데이터가 싱크 변환에 기록되지 않습니다. 디버그 세션은 변환에 대 한 테스트 도구 역할을 하기 위한 것입니다. 디버그 중에는 싱크가 필요하지 않으며 데이터 흐름에서 무시됩니다. 싱크에서 데이터 작성을 테스트 하려는 경우 Azure Data Factory 파이프라인에서 데이터 흐름을 실행 하 고 파이프라인에서 디버그 실행을 사용 합니다.

### <a name="quick-actions"></a>빠른 작업

데이터 미리 보기가 표시 되 면 빠른 변환을 생성 하 여 열을 형식 캐스팅, 제거 또는 수정할 수 있습니다. 열 머리글을 클릭 한 다음 데이터 미리 보기 도구 모음에서 옵션 중 하나를 선택 합니다.

![빠른 작업](media/data-flow/quick-actions1.png "빠른 작업")

수정을 선택 하면 데이터 미리 보기가 즉시 새로 고쳐집니다. 오른쪽 위 모서리에서 **확인** 을 클릭 하 여 새 변환을 생성 합니다.

![빠른 작업](media/data-flow/quick-actions2.png "빠른 작업")

**형식 캐스팅** 및 **Modify** 는 파생 열 변환을 생성 하 고 **Remove** 는 Select 변환을 생성 합니다.

![빠른 작업](media/data-flow/quick-actions3.png "빠른 작업")

> [!NOTE]
> 데이터 흐름을 편집 하는 경우 빠른 변환을 추가 하기 전에 데이터 미리 보기를 다시 인출 해야 합니다.

### <a name="data-profiling"></a>데이터 프로 파일링

데이터 미리 보기 탭에서 열을 선택 하 고 데이터 미리 보기 도구 모음에서 **통계** 를 클릭 하면 각 필드에 대 한 자세한 통계가 포함 된 데이터 그리드 오른쪽 맨 위에 차트가 표시 됩니다. Azure Data Factory는 표시할 차트 유형의 데이터 샘플링에 따라 결정을 내립니다. 높은 카디널리티 필드는 기본적으로 NULL/NOT NULL 차트로 표시 되 고, 카디널리티가 낮은 범주 및 숫자 데이터는 데이터 값 빈도를 표시 하는 가로 막대형 차트를 표시 합니다. 문자열 필드의 max/len 길이, 숫자 필드의 최소/최대 값, 표준 dev, 백분위 수, 개수 및 평균도 볼 수 있습니다.

![열 통계](media/data-flow/stats.png "열 통계")

## <a name="next-steps"></a>다음 단계

* 데이터 흐름의 빌드 및 디버깅이 완료 되 면 [파이프라인에서 실행 합니다.](control-flow-execute-data-flow-activity.md)
* 데이터 흐름을 사용 하 여 파이프라인을 테스트 하는 경우 파이프라인 [디버그 실행 실행 옵션을 사용 합니다.](iterative-development-debugging.md)
