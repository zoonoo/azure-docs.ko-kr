---
title: 포함 파일
description: 포함 파일
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 10/01/2020
ms.author: robb
ms.custom: include file
ms.openlocfilehash: 071f2849a877f4ea1e8a84eff6ccfb8343be3ec7
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101734067"
---
| 리소스 | 기본 제한 | 최대 제한 |
| --- | --- | --- |
| 메트릭 경고 수(클래식) |구독당 100개 활성 경고 규칙 | 지원 요청 |
| 메트릭 경고 |구독당 5,000개 활성 경고 규칙(Azure 퍼블릭, Azure 중국 21Vianet 및 Azure Government 클라우드 기준) 이 제한에 도달하면 [동일한 유형의 다중 리소스 경고](../articles/azure-monitor/alerts/alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor)를 사용할 수 있는지 살펴보세요.<br/>경고 규칙당 5000개의 메트릭 시계열. | 지원 요청 |
| 활동 로그 경고 | 구독당 100개 활성 경고 규칙(늘릴 수 없음) | 기본값과 동일 |
| 로그 경고 | 구독당 512개 활성 경고 규칙 리소스당 200개 활성 경고 규칙 | 지원 요청 |
| 경고 규칙 및 작업 규칙 설명 길이| 로그 검색 경고 4096자<br/>다른 모든 경고 2048자 | 기본값과 동일 |