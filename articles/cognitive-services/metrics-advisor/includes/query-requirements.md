---
title: 쿼리 요구 사항
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 09/30/2020
ms.author: aahi
ms.openlocfilehash: 518865f78c170f1fbe4e65b96dc149c1b449a88b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91631378"
---
쿼리 내에서 `@StartTime` 매개 변수를 사용하여 단일 타임스탬프에 대한 메트릭 데이터를 가져옵니다. 메트릭 관리자가 쿼리를 실행할 때 매개 변수를 `yyyy-MM-ddTHH:mm:ss` 형식 문자열로 바꿉니다.

> [!IMPORTANT]
> 각 타임스탬프에서 쿼리는 각 차원 조합에 대해 최대 하나의 레코드를 반환해야 합니다. 그리고 쿼리에서 반환된 모든 레코드의 타임스탬프는 동일해야 합니다. 메트릭 관리자는 각 타임스탬프에 대해 이 쿼리를 실행하여 데이터를 수집합니다. 자세한 정보와 예는 [쿼리에 대한 FAQ 섹션](../faq.md#how-do-i-write-a-valid-query-for-ingesting-my-data)을 참조하세요. 