---
title: "스트림 분석은 테이블을 제한합니다."
description: "스트림 분석 구성 요소 및 연결에 대한 시스템 제한 및 권장 크기에 대해 설명합니다."
services: stream-analytics
documentationcenter: NA
author: jeffstokes72
manager: paulettm
editor: cgronlun
ms.service: stream-analytics
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: big-data
ms.date: 07/25/2016
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: e664ce9426a2852a35dfdade5d41a9ce8b37a3b7
ms.openlocfilehash: b4cfc4663dbef820c866846d65b9fa8512c7dcff


---
| 제한 식별자 | 제한 | 설명 |
| --- | --- | --- |
| 지역당 구독당 최대 스트리밍 단위 수 |50 |구독의 스트리밍 단위를 50을 초과하도록 요청하려면 [Microsoft 지원](https://support.microsoft.com/en-us)에 문의하세요. |
| 스트리밍 단위의 최대 처리량 |1MB/s* |SU당 최대 처리량은 시나리오에 따라 달라집니다. 실제 처리량은 더 낮을 수 있고 쿼리 복잡성 및 분할에 따라 달라질 수 있습니다. 자세한 내용은 [처리량을 높이기 위한 Azure 스트림 분석 작업 규모 지정](../articles/stream-analytics/stream-analytics-scale-jobs.md) 문서에서 찾을 수 있습니다. |
| 작업당 입력의 최대 수 |60 |스트림 분석 작업당 60개의 입력 하드 제한이 있습니다. |
| 작업당 출력의 최대 수 |60 |스트림 분석 작업당 60개의 출력 하드 제한이 있습니다. |
| 작업당 함수의 최대 수 |60 |스트림 분석 작업당 함수 60개의 출력 하드 제한이 있습니다. |
| 지역당 최대 작업 수 |1500 |각 구독에는 지역당 최대 1500개의 작업이 있을 수 있습니다. |




<!--HONumber=Nov16_HO3-->


