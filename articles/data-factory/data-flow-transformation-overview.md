---
title: 매핑 데이터 흐름 변환 개요
description: 매핑 데이터 흐름에서 사용할 수 있는 다양 한 변환의 개요
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 0442c701f39fd3e84361ad0201aaf8d1840d8851
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "81606277"
---
# <a name="mapping-data-flow-transformation-overview"></a>매핑 데이터 흐름 변환 개요

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)] 

다음은 현재 매핑 데이터 흐름에서 지원 되는 변환 목록입니다. 각 변환을 클릭 하 여 구성 세부 정보를 알아보세요.

| 속성 | Category | 설명 |
| ---- | -------- | ----------- |
| [집계](data-flow-aggregate.md) | Schema 한정자 | SUM, MIN, MAX 및 COUNT와 같은 여러 유형의 집계를 기존 또는 계산 열로 그룹화 하 여 정의 합니다. | 
| [행 변경](data-flow-alter-row.md) | Row 한정자 | 행에 대 한 insert, delete, update 및 upsert 정책을 설정 합니다. |
| [조건부 분할](data-flow-conditional-split.md) | 여러 입력/출력 | 일치 조건에 따라 데이터 행을 서로 다른 스트림으로 라우팅합니다. |
| [파생 열](data-flow-derived-column.md) | Schema 한정자 | 데이터 흐름 식 언어를 사용 하 여 새 열을 생성 하거나 기존 필드를 수정 합니다. | 
| [Exists](data-flow-exists.md) | 여러 입력/출력 | 데이터가 다른 원본 또는 스트림에 있는지 확인 합니다. | 
| [필터](data-flow-filter.md) | Row 한정자 | 조건에 따라 행을 필터링 합니다. |
| [평면화(flatten)](data-flow-flatten.md) | Schema 한정자 |  JSON과 같은 계층 구조 내에서 배열 값을 가져와 개별 행으로 언 롤. |
| [Join](data-flow-join.md) | 여러 입력/출력 |  두 원본 또는 스트림의 데이터를 결합 합니다. |
| [조회](data-flow-lookup.md) | 여러 입력/출력 | 다른 소스에서 데이터를 참조 합니다. |
| [새 분기](data-flow-new-branch.md) | 여러 입력/출력 | 동일한 데이터 스트림에 대해 여러 작업 및 변환 집합을 적용 합니다. |
| [피벗](data-flow-pivot.md) | Schema 한정자 | 하나 이상의 그룹화 열에 개별 열로 변환 된 고유 행 값이 있는 집계입니다. |
| [Select](data-flow-select.md) | Schema 한정자 | 열 및 스트림 이름 별칭을 만들고 열을 삭제 하거나 순서를 변경 합니다. |
| [싱크](data-flow-sink.md) | - | 데이터의 최종 대상 |
| [정렬](data-flow-sort.md) | Row 한정자 | 현재 데이터 스트림의 들어오는 행 정렬 |
| [소스](data-flow-source.md) | - | 데이터 흐름에 대 한 데이터 원본 |
| [서로게이트 키](data-flow-surrogate-key.md) | Schema 한정자 | 비즈니스에 대 한 증분이 아닌 임의 키 값 추가 |
| [Union](data-flow-union.md) | 여러 입력/출력 | 여러 데이터 스트림을 세로로 결합 |
| [피벗 해제](data-flow-unpivot.md) | Schema 한정자 | 열을 행 값으로 피벗 |
| [창](data-flow-window.md) | Schema 한정자 |  데이터 스트림에서 열의 창 기반 집계를 정의 합니다. |
