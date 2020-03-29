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
ms.openlocfilehash: ee80c04a6365c2cf337c4033a90df8d2993c299d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "76845990"
---
다음 제한은 이벤트 도메인이 *아닌* Azure Event Grid 시스템 토픽 및 사용자 지정 토픽에 적용됩니다.

| 리소스 | 제한 |
| --- | --- |
| Azure 구독당 사용자 지정 토픽 | 100 |
| 토픽당 이벤트 구독 | 500 |
| 사용자 지정 토픽에 대한 게시 비율(수신) | 토픽별 초당 5,000개 이벤트 |
| 요청 게시 | 초당 250 |
| 이벤트 크기 | 1 MB(64KB 이벤트 중 으로 청구됨) |

다음 제한은 이벤트 도메인에만 적용됩니다.

| 리소스 | 제한 |
| --- | --- |
| 이벤트 도메인별 토픽 | 100,000 |
| 도메인 내의 토픽당 이벤트 구독 | 500 |
| 도메인 범위 이벤트 구독 | 50 |
| 이벤트 도메인에 대한 게시 속도(침투) | 초당 5,000개의 이벤트 |
| 요청 게시 | 초당 250 |
| Azure 구독당 이벤트 도메인 | 100 |