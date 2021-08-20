---
title: 데이터 스키마 요구 사항
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: applied-ai-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 09/10/2020
ms.author: mbullwin
ms.openlocfilehash: f00c25396405678312e4c18a345840d628c15848
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/15/2021
ms.locfileid: "114340921"
---
Metrics Advisor는 시계열 변칙 검색, 진단 및 분석을 위한 서비스입니다. AI 지원 서비스로, 데이터를 사용하여 사용 모델을 학습시킵니다. 이 서비스는 다음 열을 사용하여 집계된 데이터의 테이블을 허용합니다.

* **측정값**(필수): 측정값은 기본 또는 단위 관련 용어이며 메트릭의 수량화 가능한 값입니다. 숫자 값을 포함하는 하나 이상의 열을 의미합니다.
* **타임스탬프**(옵션): `DateTime` 또는 `String` 형식의 열 0개 또는 1개. 이 열이 설정되지 않은 경우 타임스탬프는 각 수집 기간의 시작 시간으로 설정됩니다. 타임스탬프를 `yyyy-MM-ddTHH:mm:ssZ` 형식으로 지정합니다. 
* **차원**(선택 사항): 차원은 하나 이상의 범주 값입니다. 이러한 값의 조합은 특정 일변량 시계열(국가, 언어, 테넌트 등)을 식별합니다. 차원 열은 모든 데이터 형식일 수 있습니다. 대량의 열과 값으로 작업하는 경우에는 너무 많은 차원이 처리되지 않도록 주의하세요.

다음은 예상 메트릭 스키마의 예입니다. 

<!-- ![Screenshot of metrics schema example](../media/tutorial/metric-schema.png) -->

파일을 메트릭 입력으로 사용하는 ADLS, Azure Blob과 같은 데이터 원본을 사용하는 경우 예상 메트릭 스키마에 맞게 데이터를 미리 집계합니다. 그러나 원본에서 메트릭 데이터를 가져오기 위해 Azure SQL 서버, Azure Data Explorer와 같은 데이터 원본 또는 쿼리 실행을 지원하는 기타 원본을 사용하는 경우 집계 함수를 사용하여 데이터를 예상 스키마로 집계할 수 있습니다.
