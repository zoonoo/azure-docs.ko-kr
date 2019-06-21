---
title: Azure Data Factory Mapping Data Flow 디버그 모드
description: 데이터 흐름을 빌드할 때 대화형 디버그 세션 시작
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: d86725718217caf7fd1d9dd6d5d67362e5de7270
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147382"
---
# <a name="mapping-data-flow-debug-mode"></a>Mapping Data Flow 디버그 모드

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

디자인 화면의 맨 위에 있는 "데이터 흐름 디버깅" 단추를 사용 하 여 azure 데이터 팩터리 매핑 데이터 흐름의 디버그 모드 전환할 수 있습니다. 경우 디버그 모드를 설정 하 고 데이터 흐름을 디자인 하면 대화형으로 데이터를 시청 하려면 빌드 및 데이터 흐름을 디버깅 하는 동안 변환 셰이프. 데이터 흐름 디자인 세션에서와 데이터 흐름 파이프라인 디버그 실행 하는 동안 디버그 세션을 사용할 수 있습니다.

![디버그 단추](media/data-flow/debugbutton.png "디버그 단추")

## <a name="overview"></a>개요
디버그 모드에 있으면 현재 Spark 클러스터를 사용 하 여 데이터 흐름을 대화형으로 빌드합니다. Azure Data Factory에서 디버그를 끄면 세션이 닫힙니다. 디버그 세션이 켜진 시간 동안 Azure Databricks에서 부과되는 시간당 요금에 유의해야 합니다.

대부분의 경우에서는 비즈니스 논리의 유효성을 검사 하 고 Azure Data Factory에서 작업을 게시 하기 전에 데이터 변환을 볼 수 있도록 디버그 모드에서 데이터 흐름을 작성 하는 것이 좋습니다. 파이프라인 내에서 데이터 흐름을 테스트 하려면 파이프라인 패널에서 "디버그" 단추를 사용 합니다.

> [!NOTE]
> 디버그 모드 라이트 Data Factory 도구 모음에서 녹색 상태인 부과 됩니다 8 코어 시간당 일반 계산의 데이터 흐름 디버그 요금으로 60 분을 사용 하 여 활성 시간 

> [!NOTE]
>Data Flow에서 디버그 모드로 실행하는 경우에는 데이터가 싱크 변환에 기록되지 않습니다. 디버그 세션에 변환에 대 한 테스트 장비로 사용할 것입니다. 디버그 중에는 싱크가 필요하지 않으며 데이터 흐름에서 무시됩니다. 테스트 데이터 싱크에 작성 하려는 경우 Azure Data Factory 파이프라인에서 데이터 흐름을 실행 하 고 파이프라인에서 디버그 실행을 사용 합니다.

## <a name="debug-settings"></a>디버그 설정
디버그 설정 데이터 흐름 캔버스 도구 모음에서 "디버그 설정"을 클릭 하 여 편집할 수 있습니다. 제한 및/또는 각 소스 변환을 여기에 사용할 소스 파일을 선택할 수 있습니다. 이 설정에서 행 제한은 현재 디버그 세션에 대해서만 합니다. 또한 SQL DW 소스에 사용 되는 스테이징 연결 된 서비스를 선택할 수 있습니다. 

![디버그 설정](media/data-flow/debug-settings.png "디버그 설정")

## <a name="cluster-status"></a>클러스터 상태
클러스터 디버그 준비가 되 면 녹색으로 바뀌도록는 디자인 화면의 맨 위에 있는 클러스터 상태 표시기가 있습니다. 클러스터가 이미 웜 상태이면 녹색 표시기가 거의 즉시 나타납니다. 클러스터 되지 않은 이미 실행 중인 경우 디버그 모드를 입력 했을 때를 스핀업 하는 데 클러스터에 대 일 분 대기할 수 있습니다. 표시기는 해당 준비 될 때까지 회전 합니다.

완료 했으면 디버깅을 해제 디버그 스위치를 더 이상 요금이 청구 됩니다 디버그 작업에 대 한 및 Azure Databricks 클러스터 종료 될 수 있도록 합니다.

## <a name="data-preview"></a>데이터 미리 보기
디버그를 켜면 데이터 미리 보기 탭이 맨 아래 패널에 표시됩니다. 디버그 모드를 켜지 않으면 각 변환에 들어오고 나가는 현재 메타데이터만 Data Flow의 검사 탭에 표시됩니다. 데이터 미리 보기에서는 디버그 설정에서 제한으로 설정 된 행 수를 쿼리하여만 합니다. 데이터 미리 보기를 새로 고치기 위해 “데이터 페치”를 클릭해야 할 수도 있습니다.

![데이터 미리 보기](media/data-flow/datapreview.png "데이터 미리 보기")

## <a name="data-profiles"></a>데이터 프로필
개별 열에 데이터 미리 보기 탭에서 선택 하면 각 필드에 대 한 자세한 통계를 사용 하 여 데이터 표의 맨 오른쪽에 있는 차트 팝업 됩니다. Azure Data Factory는 표시할 차트 유형의 데이터 샘플링에 따라 결정을 내립니다. 높은 카디널리티 필드는 기본적으로 /NOT NULL 차트 카디널리티가 낮은 데이터 범주 및 숫자 데이터 값의 빈도 보여 주는 가로 막대형 차트를 표시 됩니다. 문자열 필드의 숫자 필드, 표준 개발, 백분위 수, 개수 및 평균에서 최소/최대 값 길이 최대/len도 볼 수 있습니다. 

![열 통계](media/data-flow/stats.png "열 통계")

## <a name="next-steps"></a>다음 단계

빌드 및 디버깅 데이터 흐름이 완료 되 면 [파이프라인에서 실행 합니다.](control-flow-execute-data-flow-activity.md)

데이터 흐름을 사용 하 여 파이프라인을 테스트할 때 사용 하 여 파이프라인 [디버그 실행 옵션을 실행 합니다.](iterative-development-debugging.md)
