---
title: "Azure Machine Learning 데이터 준비에 사용 가능한 지원되는 검사기 | Microsoft Docs"
description: "이 문서에서는 Azure ML 데이터 준비에 사용할 수 있는 검사기의 전체 목록을 제공합니다"
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/11/2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: bc14c051fb0f518b1cff2236a61d24cb052700f0
ms.contentlocale: ko-kr
ms.lasthandoff: 09/25/2017

---

# <a name="supported-inspectors-for-this-preview"></a>이 미리 보기에 지원되는 검사기
이 문서에서는 이 미리 보기에서 사용할 수 있는 검사기 집합을 간략하게 설명합니다.

## <a name="the-halo-effect"></a>Halo 효과 
일부 검사기는 Halo 효과를 지원합니다. 이 효과는 두 개의 다른 색상을 사용하여 변환 시 즉시 변경 사항을 시각적으로 보여 줍니다. 회색은 마지막 변환 이전의 값을 나타내고 파란색은 현재 값을 나타냅니다. 이 효과는 옵션에서 사용/사용 안 함으로 설정할 수 있습니다.

## <a name="graphical-filtering"></a>그래픽 필터링 
일부 검사기는 편집기로 사용되어 데이터 필터링을 지원합니다. 그렇게 하면 그래픽 요소 선택을 포함한 다음 검사기 창 오른쪽 상단의 도구 모음을 사용하여 선택한 값을 필터링합니다. 

## <a name="column-statistics"></a>열 통계
숫자 열의 경우 이 검사기는 열에 대한 다양한 통계를 제공합니다. 통계는 다음과 같습니다.
- 최소
- 하한 사분위수
- 중앙값
- 상한 사분위수
- 최대
- 평균
- 표준 편차


### <a name="options"></a>옵션 
- 없음

## <a name="histogram"></a>히스토그램 
단일 숫자 열의 히스토그램을 계산하고 표시합니다. 기본 버킷 수는 Scott 규칙을 사용하여 계산되고 규칙은 옵션을 통해 재정의할 수 있습니다.
이 검사기는 Halo 효과를 지원합니다.


### <a name="options"></a>옵션
- 최소 버킷 수(기본 버킷팅이 선택된 경우에도 적용)
- 기본 버킷 수(Scott 규칙) 
- Halo 표시
- 커널 밀도 플롯 오버레이(가우스 커널) 


### <a name="actions"></a>작업
이 검사기는 버킷(단일 또는 여러 버킷 선택)을 통해 필터링을 지원하고 앞에서 설명한 대로 필터를 적용합니다.

## <a name="value-counts"></a>값 수
이 검사기는 현재의 선택 열에 대한 값의 빈도 표를 보여 줍니다. 상위 6개를 기본으로 표시하고 제한 방법을 임의 숫자로 변경하거나 상위가 아닌 하위 개수로 변경할 수 있습니다. 이 검사기는 Halo 효과를 지원합니다.

### <a name="options"></a>옵션 
- 상위 값의 수
- 내림차순
- Null/오류 값 포함
- Halo 표시


### <a name="actions"></a>작업 
이 검사기는 막대(단일 또는 여러 막대 선택)를 통해 필터링을 지원하며 앞에서 설명한 대로 필터를 적용할 수 있습니다.

## <a name="box-plot"></a>상자 플롯 
숫자 열의 상자 수염 플롯

### <a name="options"></a>옵션 
- 열별 그룹화

## <a name="scatter-plot"></a>산점도
두 숫자 열에 대한 분산형 플롯인 데이터는 성능상의 이유로 다운 샘플링되고 샘플 크기는 옵션에서 재정의할 수 있습니다.

### <a name="options"></a>옵션  
- X-축 열
- Y-축 열
- 샘플 크기
- 열별 그룹화


## <a name="time-series"></a>시계열
X-축에서 시간을 인식하는 선 그래프

### <a name="options"></a>옵션
- 날짜 열
- 숫자 열
- 샘플 크기


### <a name="actions"></a>작업
이 검사기는 클릭한 채 끌어서 선택하는 방법으로 필터링하여 그래프의 범위를 선택할 수 있습니다. 선택 영역을 완료한 후 이전에 설명한 대로 필터를 적용합니다.


## <a name="map"></a>Map 
위도 및 경도가 지정되었다고 가정하고 점으로 표시된 맵입니다. 위도를 먼저 선택해야 합니다.

### <a name="options"></a>옵션
- 위도 열
- 경도 열
- 클러스터링 설정
- 열별 그룹화


### <a name="actions"></a>작업
이 검사기는 맵에서 지점 선택을 통한 필터링을 지원합니다. Ctrl 키를 누른 채 클릭하고 지점 주변의 정사각형 형태로 마우스로 끌어옵니다. 그런 다음 앞에서 설명한 대로 필터를 적용합니다.
가능한 모든 지점이 표시되도록 맵의 크기를 신속하게 조정할 수 있으며 더 이상 맵 왼쪽의 **E** 키를 누르지 않아도 됩니다.

