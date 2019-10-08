---
title: Azure Data Factory Mapping Data Flow 조건부 분할 변환
description: Azure Data Factory Data Flow 조건부 분할 변환
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: fec2b09b9dc471135d9cdd00ac2465728a47bdbf
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72026924"
---
# <a name="mapping-data-flow-conditional-split-transformation"></a>데이터 흐름 조건부 분할 변환 매핑



![조건부 분할 도구 상자](media/data-flow/conditionalsplit2.png "조건부 분할 도구 상자")

조건부 분할 변환은 데이터 내용에 따라 데이터 행을 다른 스트림으로 라우팅할 수 있습니다. 조건부 분할 변환의 구현은 프로그래밍 언어의 CASE 결정 구조체와 비슷합니다. 변환은 식을 평가하고, 결과에 따라 데이터 행을 지정된 스트림으로 보냅니다. 이 변환은 기본 출력도 제공하므로, 행과 일치하는 식이 없을 경우 기본 출력으로 보내집니다.

![조건부 분할](media/data-flow/conditionalsplit1.png "조건부 분할 옵션")

## <a name="multiple-paths"></a>여러 경로

조건을 더 추가하려면 맨 아래 구성 창에서 “스트림 추가”를 선택하고 식 작성기 텍스트 상자를 클릭하여 식을 작성합니다.

![조건부 분할 다중](media/data-flow/conditionalsplit3.png "조건부 분할 다중")

## <a name="next-steps"></a>다음 단계

조건부 분할에 사용 되는 일반적인 데이터 흐름 변환: [조인 변환](data-flow-join.md), [조회 변환](data-flow-lookup.md), [변환 선택](data-flow-select.md)
