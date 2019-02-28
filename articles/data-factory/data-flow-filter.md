---
title: Azure Data Factory Mapping Data Flow 필터 변환
description: Azure Data Factory Mapping Data Flow 필터 변환
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: b7e7b123560aae3a2d3086c8536969297d31f7ba
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2019
ms.locfileid: "56271410"
---
# <a name="azure-data-factory-mapping-data-flow-filter-transformation"></a>Azure Data Factory Mapping Data Flow 필터 변환

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

필터 변환은 행 필터링을 제공합니다. 필터 조건을 정의하는 식을 작성합니다. 텍스트 상자를 클릭하여 식 작성기를 시작합니다. 식 작성기 내에서 필터 식을 작성하여 현재 데이터 스트림에서 다음 변환으로 통과(필터)할 수 있는 행을 제어합니다.

즉, loan_status 열을 기준으로 필터링합니다.

```
in([‘Default’, ‘Charged Off’, ‘Fully Paid’], loan_status).
```

Movies 데모의 연도 열 기준 필터링:

```
year > 1980
```
