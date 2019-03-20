---
title: 포함 파일
description: 포함 파일
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 04/30/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 443db1b4609e62fb7c57de417e42a2b4d0737ada
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57554032"
---
Azure Event Grid 시스템 항목 및 사용자 지정 항목에는 다음 제한이 적용 *되지* 이벤트 도메인입니다.

| 리소스 | 제한 |
| --- | --- |
| Azure 구독당 사용자 지정 토픽 | 100 |
| 토픽당 이벤트 구독 | 500 |
| 사용자 지정 토픽에 대한 게시 비율(수신) | 토픽별 초당 5,000개 이벤트 |

다음 제한은 이벤트 도메인 에서만 적용 됩니다.

| 리소스 | 제한 |
| --- | --- |
| 이벤트 도메인당 항목 | 공개 미리 보기 중 1,000개 |
| 도메인 내에서 항목당 이벤트 구독 | 공개 미리 보기 중 50개 |
| 도메인 범위 이벤트 구독 | 공개 미리 보기 중 50개 |
| 이벤트 도메인 (수신) 속도 게시 합니다. | 공개 미리 보기 중 초당 5,000개 이벤트 |