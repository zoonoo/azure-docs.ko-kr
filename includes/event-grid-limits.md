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
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845990"
---
다음 제한은 이벤트 도메인이 *아닌* 시스템 항목 및 사용자 지정 항목 Azure Event Grid에 적용 됩니다.

| 리소스 | 제한 |
| --- | --- |
| Azure 구독당 사용자 지정 토픽 | 100 |
| 토픽당 이벤트 구독 | 500 |
| 사용자 지정 토픽에 대한 게시 비율(수신) | 토픽별 초당 5,000개 이벤트 |
| 게시 요청 | 초당 250 |
| 이벤트 크기 | 1mb (여러 64 이벤트로 청구 됨) |

다음 제한은 이벤트 도메인에만 적용 됩니다.

| 리소스 | 제한 |
| --- | --- |
| 이벤트 도메인당 항목 | 100,000개의 |
| 도메인 내의 토픽 당 이벤트 구독 | 500 |
| 도메인 범위 이벤트 구독 | 50 |
| 이벤트 도메인의 게시 빈도 (수신) | 초당 5000 이벤트 |
| 게시 요청 | 초당 250 |
| Azure 구독 당 이벤트 도메인 | 100 |