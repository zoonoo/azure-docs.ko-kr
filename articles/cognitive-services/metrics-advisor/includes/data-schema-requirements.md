---
title: 데이터 스키마 요구 사항
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/10/2020
ms.author: aahi
ms.openlocfilehash: a64bb5b28a06d9a013d59e022047f5e2841126ab
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90940133"
---
Metrics Monitor는 시계열 변칙 검색, 진단 및 분석을 위한 서비스입니다. AI 지원 서비스로, 데이터를 사용하여 사용 모델을 학습시킵니다. 이 서비스는 다음 열을 사용하여 집계된 데이터의 테이블을 허용합니다.

* **측정값**(필수): 숫자 값을 포함하는 하나 이상의 열
* **타임스탬프**(옵션): `DateTime` 또는 `String` 형식의 열 0개 또는 1개. 이 열이 설정되지 않은 경우 타임스탬프는 각 수집 기간의 시작 시간으로 설정됩니다. 타임스탬프를 `yyyy-MM-ddTHH:mm:ssZ` 형식으로 지정합니다. 
  * **타임스탬프는 메트릭의 세분성과 일치해야 합니다. 예를 들어 일별 메트릭은 타임스탬프의 시간, 분 및 초에 `00:00:00`** 레이블이 지정되어야 합니다.
* **차원**(옵션): 열은 모든 데이터 형식일 수 있습니다. 대량의 열과 값으로 작업하는 경우에는 너무 많은 차원이 처리되지 않도록 주의하세요.

> [!Note]
> 각 메트릭에는 하나의 차원 조합에 해당하는 측정값당 하나의 타임스탬프만 있어야 합니다. 온보딩 전에 데이터를 집계하거나 쿼리를 사용하여 수집할 데이터를 지정합니다.