---
title: Azure Data Factory Mapping Data Flow 필터 변환
description: Azure Data Factory Mapping Data Flow 필터 변환
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: e0b41850c149ff7095333cf77b780dec1f03b882
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66234423"
---
# <a name="azure-data-factory-filter-transformation"></a>Azure 데이터 팩터리 필터 변환

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

필터 변환은 행 필터링을 제공합니다. 필터 조건을 정의하는 식을 작성합니다. 텍스트 상자를 클릭하여 식 작성기를 시작합니다. 식 작성기 내에서 필터 식을 작성하여 현재 데이터 스트림에서 다음 변환으로 통과(필터)할 수 있는 행을 제어합니다. SQL 문의 WHERE 절과 필터 변환 생각할 수 있습니다.

## <a name="filter-on-loanstatus-column"></a>Loan_status 열을 필터링 합니다.

```
in([‘Default’, ‘Charged Off’, ‘Fully Paid’], loan_status).
```

Movies 데모의 연도 열 기준 필터링:

```
year > 1980
```

## <a name="next-steps"></a>다음 단계

열 변환, 필터링을 시도 합니다 [변환 선택](data-flow-select.md)
