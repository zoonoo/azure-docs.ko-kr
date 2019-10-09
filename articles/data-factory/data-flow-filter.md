---
title: Azure Data Factory Mapping Data Flow 필터 변환
description: Azure Data Factory Mapping Data Flow 필터 변환
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: dd29753ff14d16081a46eebbc2ea02d94e5985f1
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029350"
---
# <a name="azure-data-factory-filter-transformation"></a>Azure 데이터 팩터리 필터 변환



필터 변환은 행 필터링을 제공합니다. 필터 조건을 정의하는 식을 작성합니다. 텍스트 상자를 클릭하여 식 작성기를 시작합니다. 식 작성기 내에서 필터 식을 작성하여 현재 데이터 스트림에서 다음 변환으로 통과(필터)할 수 있는 행을 제어합니다. 필터 변환을 SQL 문의 WHERE 절로 생각 합니다.

## <a name="filter-on-loan_status-column"></a>Loan_status 열에 대해 필터링 합니다.

```
in([‘Default’, ‘Charged Off’, ‘Fully Paid’], loan_status).
```

Movies 데모의 연도 열 기준 필터링:

```
year > 1980
```

## <a name="next-steps"></a>다음 단계

열 필터링 변환을 시도 하 여 [선택 변환](data-flow-select.md)
