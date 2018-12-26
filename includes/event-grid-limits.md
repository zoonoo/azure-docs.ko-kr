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
ms.openlocfilehash: 2425d9455606f5eabba4b89cfa238b7a928be30e
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2018
ms.locfileid: "51285723"
---
####

이벤트 도메인이 ‘아닌’ Event Grid 시스템 토픽 및 사용자 지정 토픽에는 다음과 같은 제한이 적용됩니다.

| 리소스 | 제한 |
| --- | --- |
| Azure 구독당 사용자 지정 토픽 | 100 |
| 토픽당 이벤트 구독 | 500 |
| 사용자 지정 토픽에 대한 게시 비율(수신) | 토픽별 초당 5,000개 이벤트 |

####

다음 제한은 이벤트 도메인에만 적용됩니다.

| 리소스 | 제한 |
| --- | --- |
| 이벤트 도메인당 토픽 | 공개 미리 보기 중 1,000개 |
| 도메인 내 토픽당 이벤트 구독 | 공개 미리 보기 중 50개 |
| 도메인 범위 이벤트 구독 | 공개 미리 보기 중 50개 |
| 이벤트 도메인에 대한 게시 비율(수신) | 공개 미리 보기 중 초당 5,000개 이벤트 |