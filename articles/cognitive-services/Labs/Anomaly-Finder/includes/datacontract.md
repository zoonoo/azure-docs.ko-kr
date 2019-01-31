---
title: 포함 파일
description: 포함 파일
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.subservice: anomaly-finder
ms.topic: include
ms.date: 04/13/2018
ms.author: chliang
ms.custom: include file
ms.openlocfilehash: 3cc0e521e43f6855397a19fe34fce99da3e20494
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55228862"
---
[Anomaly Finder API](https://labs.cognitive.microsoft.com/en-us/project-anomaly-finder)를 사용하면 JSON 형식의 시계열 데이터를 API 엔드포인트에 업로드한 다음, API 응답에서 결과를 읽을 수 있습니다. 시계열 데이터를 업로드할 수 있으며, 각 데이터 요소에는 다음이 포함됩니다.  
* Timestamp - 데이터 요소의 타임스탬프입니다. UTC 날짜 시간 문자열을 사용합니다(예: “2017-08-01T00:00:00Z”).
* Value - 해당 데이터 요소의 측정값입니다.

결과는 다음으로 구성됩니다.
* Period - API가 이상을 감지하는 데 사용하는 주기입니다.
* WarningText - 가능한 경고 정보입니다.
* ExpectedValue - 학습 기반 모델에서 예측된 값입니다.
* IsAnomaly - 데이터 요소가 양방향(급증 또는 급감)에서 모두 이상인지 여부에 대한 결과입니다.
* IsAnomaly_Neg - 데이터 요소가 음수 방향(급감)에서 이상인지 여부에 대한 결과입니다.
* IsAnomaly_Pos - 데이터 요소가 양수 방향(급증)에서 이상인지 여부에 대한 결과입니다.
* UpperMargin - ExpectedValue 및 UpperMargin의 합계는 데이터 요소가 여전히 정상으로 간주되는 상한을 결정합니다.
* LowerMargin - (ExpectedValue - LowerMargin) 데이터 요소가 여전히 정상으로 간주되는 하한을 결정합니다.

데이터 계약의 세부 정보는 [여기](../apiref.md)에서 확인할 수 있습니다.

