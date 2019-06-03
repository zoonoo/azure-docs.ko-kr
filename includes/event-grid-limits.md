---
title: 포함 파일
description: 포함 파일
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 05/22/2019
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 3f94481e6a8550479788d92c744327e1dc3b58c4
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66376913"
---
Azure Event Grid 시스템 항목 및 사용자 지정 항목에는 다음 제한이 적용 *되지* 이벤트 도메인입니다.

| Resource | 제한 |
| --- | --- |
| Azure 구독당 사용자 지정 토픽 | 100 |
| 토픽당 이벤트 구독 | 500 |
| 사용자 지정 토픽에 대한 게시 비율(수신) | 토픽별 초당 5,000개 이벤트 |
| 요청 게시 | 초당 250 |
| 이벤트 크기 | 에 대 한 지원 64KB 일반적 가용성 (GA). 1MB에 대 한 지원은 현재 미리 보기로 제공에서 됩니다. |

다음 제한은 이벤트 도메인 에서만 적용 됩니다.

| Resource | 제한 |
| --- | --- |
| 이벤트 도메인당 항목 | 공개 미리 보기 중 1,000개 |
| 도메인 내에서 항목당 이벤트 구독 | 공개 미리 보기 중 50개 |
| 도메인 범위 이벤트 구독 | 공개 미리 보기 중 50개 |
| 이벤트 도메인 (수신) 속도 게시 합니다. | 공개 미리 보기 중 초당 5,000개 이벤트 |
| 요청 게시 | 초당 250 |