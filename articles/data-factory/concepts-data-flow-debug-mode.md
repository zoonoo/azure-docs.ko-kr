---
title: Azure Data Factory Mapping Data Flow 디버그 모드
description: 데이터 흐름을 빌드할 때 대화형 디버그 세션 시작
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: 567f64b8b720588807caeb5e49bae15f14c5b0a7
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/16/2019
ms.locfileid: "56329799"
---
# <a name="mapping-data-flow-debug-mode"></a>Mapping Data Flow 디버그 모드

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Azure Data Factory Mapping Data Flow에는 디자인 화면의 맨 위에 있는 디버그 단추로 켤 수 있는 디버그 모드가 있습니다. 데이터 흐름을 디자인할 때 디버그 모드를 켜면 데이터 흐름을 빌드 및 디버그하는 동안 데이터 셰이프 변환을 대화형으로 감시할 수 있습니다.

<img src="media/data-flow/debugbutton.png" width="400">

## <a name="overview"></a>개요
디버그 모드가 켜진 경우 실행 중인 Azure Databricks 대화형 클러스터를 사용하여 데이터 흐름을 대화형으로 빌드합니다. Azure Data Factory에서 디버그를 끄면 세션이 닫힙니다. 디버그 세션이 켜진 시간 동안 Azure Databricks에서 부과되는 시간당 요금에 유의해야 합니다.

대부분의 경우 Azure Data Factory에 작업을 게시하기 전에 비즈니스 논리의 유효성을 검사하고 데이터 변환을 볼 수 있도록 디버그 모드에서 데이터 흐름을 빌드하는 것이 좋습니다.

## <a name="debug-mode-on"></a>디버그 모드 켬
디버그 모드를 켜면 대화형 Azure Databricks 클러스터를 가리키고 원본 샘플링에 대한 옵션을 선택하도록 요청하는 사이드 패널 양식이 표시됩니다. Azure Databricks의 대화형 클러스터를 사용하고 각 원본 변환에서 샘플링 크기를 선택하거나 테스트 데이터에 사용할 텍스트 파일을 선택해야 합니다.

<img src="media/data-flow/upload.png" width="400">

> [!NOTE]
>Data Flow에서 디버그 모드로 실행하는 경우에는 데이터가 싱크 변환에 기록되지 않습니다. 디버그 세션은 변환에 대한 테스트 도구 역할을 수행하기 위한 것입니다. 디버그 중에는 싱크가 필요하지 않으며 데이터 흐름에서 무시됩니다. 싱크에 데이터를 쓰는 작업을 테스트하려면 Azure Data Factory 파이프라인에서 Data Flow를 실행하고 파이프라인에서 디버그 실행을 사용합니다.

## <a name="debug-settings"></a>디버그 설정
디버그 설정은 사이드 패널에 표시되는 데이터 흐름의 각 원본일 수 있으며, Data Flow 디자인 도구 모음에서 “원본 설정”을 선택하여 편집할 수도 있습니다. 여기에서 각 원본 변환에 사용할 한도 및/또는 파일 원본을 선택할 수 있습니다. 디버그에 사용할 Databricks 클러스터도 선택할 수 있습니다.

## <a name="cluster-status"></a>클러스터 상태
클러스터가 디버그 준비가 되면 녹색으로 바뀌는 클러스터 상태 표시기가 디자인 화면 맨 위에 있습니다. 클러스터가 이미 웜 상태이면 녹색 표시기가 거의 즉시 나타납니다. 디버그 모드로 전환할 때 클러스터가 아직 실행되지 않은 경우에는 클러스터가 실행될 때까지 5-7분 정도 기다려야 합니다. 준비가 완료될 때까지 표시등은 황색입니다. 클러스터가 Data Flow 디버그 준비가 되면 표시등이 녹색으로 바뀝니다.

디버깅을 마쳤으면 Azure Databricks 클러스터가 종료될 수 있도록 디버그 스위치를 끕니다.

<img src="media/data-flow/datapreview.png" width="400">

## <a name="data-preview"></a>데이터 미리 보기
디버그를 켜면 데이터 미리 보기 탭이 맨 아래 패널에 표시됩니다. 디버그 모드를 켜지 않으면 각 변환에 들어오고 나가는 현재 메타데이터만 Data Flow의 검사 탭에 표시됩니다. 데이터 미리 보기는 원본 설정에서 한도로 설정된 개수의 행만 쿼리합니다. 데이터 미리 보기를 새로 고치기 위해 “데이터 페치”를 클릭해야 할 수도 있습니다.

<img src="media/data-flow/stats.png" width="400">

## <a name="data-profiles"></a>데이터 프로필
데이터 미리 보기 탭에서 개별 열을 선택하면 각 필드에 대한 자세한 통계가 포함된 차트가 데이터 그리드의 맨 오른쪽에 팝업됩니다. Azure Data Factory는 표시할 차트 유형의 데이터 샘플링에 따라 결정을 내립니다. 카디널리티가 낮은 범주 및 숫자 데이터는 데이터 값 빈도를 보여 주는 막대형 차트를 표시하는 반면, 카디널리티가 높은 필드는 기본적으로 NULL/NOT NULL 차트로 설정됩니다. 문자열 필드의 max/len 길이, 숫자 필드의 min/max 값, 표준 편차, 백분위수, 개수 및 평균도 표시됩니다. 

<img src="media/data-flow/chart.png" width="400">
