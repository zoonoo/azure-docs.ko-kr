---
title: 데이터 흐름 변환 개요 매핑
description: 매핑 데이터 흐름에서 사용할 수 있는 다양한 변환에 대한 개요
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 94f7277c1b741fb5ef6d2807fabbb266c0109415
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79086927"
---
# <a name="mapping-data-flow-transformation-overview"></a>데이터 흐름 변환 개요 매핑

다음은 매핑 데이터 흐름에서 현재 지원되는 변환 목록입니다. 각 변환을 클릭하여 구성 세부 정보를 알아봅니다.

| 이름 | Category | 설명 |
| ---- | -------- | ----------- |
| [집계](data-flow-aggregate.md) | 스키마 수정자 | 기존 또는 계산된 열로 그룹화된 SUM, MIN, MAX 및 COUNT와 같은 다양한 유형의 집계를 정의합니다. | 
| [행 변경](data-flow-alter-row.md) | 행 수정자 | 행에 삽입, 삭제, 업데이트 및 upsert 정책을 설정합니다. |
| [조건부 분할](data-flow-conditional-split.md) | 다중 입력/출력 | 일치하는 조건에 따라 데이터 행을 다른 스트림으로 라우팅합니다. |
| [파생 열](data-flow-derived-column.md) | 스키마 수정자 | 데이터 흐름 표현 언어에서 새 열을 생성하거나 기존 필드를 수정합니다. | 
| [존재](data-flow-exists.md) | 다중 입력/출력 | 데이터가 다른 소스 또는 스트림에 있는지 확인합니다. | 
| [필터](data-flow-filter.md) | 행 수정자 | 조건에 따라 행을 필터링합니다. |
| [평평](data-flow-flatten.md) | 스키마 수정자 |  JSON과 같은 계층 구조 내에서 배열 값을 가져 와서 개별 행으로 풀어줍니다. |
| [연결](data-flow-join.md) | 다중 입력/출력 |  두 소스 또는 스트림의 데이터를 결합합니다. |
| [조회](data-flow-lookup.md) | 다중 입력/출력 | 다른 원본의 데이터를 참조합니다. |
| [새 분기](data-flow-new-branch.md) | 다중 입력/출력 | 동일한 데이터 스트림에 여러 작업 및 변환 집합을 적용합니다. |
| [피벗](data-flow-pivot.md) | 스키마 수정자 | 하나 이상의 그룹화 열에 고유한 행 값이 개별 열로 변환되는 집계입니다. |
| [선택](data-flow-select.md) | 스키마 수정자 | 별칭 열 및 스트림 이름, 열 삭제 또는 순서 변경 |
| [싱크](data-flow-sink.md) | - | 데이터의 최종 목적지 |
| [Sort](data-flow-sort.md) | 행 수정자 | 현재 데이터 스트림에서 들어오는 행 정렬 |
| [원본](data-flow-source.md) | - | 데이터 흐름에 대한 데이터 원본 |
| [서로게이트 키](data-flow-surrogate-key.md) | 스키마 수정자 | 비비즈니스 임의 키 값 증분 추가 |
| [연합](data-flow-union.md) | 다중 입력/출력 | 여러 데이터 스트림을 수직으로 결합 |
| [Unpivot](data-flow-unpivot.md) | 스키마 수정자 | 행 값으로 열 피벗 |
| [창](data-flow-window.md) | 스키마 수정자 |  데이터 스트림에서 열의 창 기반 집계를 정의합니다. |
