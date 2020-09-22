---
title: 쿼리 요구 사항
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/10/2020
ms.author: aahi
ms.openlocfilehash: 23da214f4ffc9e589523f7ada00a77494183eb15
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90940144"
---
쿼리 내에서 `@StartTime` 매개 변수를 사용하여 특정 타임스탬프에 대한 메트릭 데이터를 가져옵니다. 이는 `yyyy-MM-ddTHH:mm:ss` 형식 문자열로 바뀝니다. 

> [!IMPORTANT]
> 쿼리에서 **단일 타임스탬프**의 메트릭 데이터만 반환됩니다. Metrics Advisor는 모든 타임스탬프에 대해 쿼리를 실행하여 해당 메트릭 데이터를 가져옵니다. 예를 들어 세분성이 *매일*인 메트릭에 대한 쿼리는 쿼리를 한 번 실행할 때 `2020-06-21T00:00:00Z` 같은 단일 타임스탬프를 하나만 포함해야 합니다. 
