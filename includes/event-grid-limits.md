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
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/10/2019
ms.locfileid: "66376913"
---
다음 제한은 이벤트 도메인이 *아닌* 시스템 항목 및 사용자 지정 항목 Azure Event Grid에 적용 됩니다.

| 리소스 | 제한 |
| --- | --- |
| Azure 구독당 사용자 지정 토픽 | 100 |
| 토픽당 이벤트 구독 | 500 |
| 사용자 지정 토픽에 대한 게시 비율(수신) | 토픽별 초당 5,000개 이벤트 |
| 게시 요청 | 초당 250 |
| 이벤트 크기 | GA (일반 공급)에서 64 KB 지원. 1mb에 대 한 지원은 현재 미리 보기 상태입니다. |

다음 제한은 이벤트 도메인에만 적용 됩니다.

| 리소스 | 제한 |
| --- | --- |
| 이벤트 도메인당 항목 | 공개 미리 보기 중 1,000개 |
| 도메인 내의 토픽 당 이벤트 구독 | 공개 미리 보기 중 50개 |
| 도메인 범위 이벤트 구독 | 공개 미리 보기 중 50개 |
| 이벤트 도메인의 게시 빈도 (수신) | 공개 미리 보기 중 초당 5,000개 이벤트 |
| 게시 요청 | 초당 250 |