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
ms.openlocfilehash: 1e4ae81c85d8de22bc87f22538c85cdc4c15b79c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96027045"
---
| 리소스 | 기본 제한 | 최대 제한 |
| --- | --- | --- |
| 메트릭 경고 수(클래식) |구독당 100개 활성 경고 규칙 | 지원 요청 |
| 메트릭 경고 |구독당 5,000개 활성 경고 규칙(Azure 퍼블릭, Azure 중국 21Vianet 및 Azure Government 클라우드 기준) 이 제한에 도달하면 [동일한 유형의 다중 리소스 경고](../articles/azure-monitor/platform/alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor)를 사용할 수 있는지 살펴보세요.   | 지원 요청 |
| 활동 로그 경고 | 구독당 100개 활성 경고 규칙 | 기본값과 동일 |
| 로그 경고 | 구독당 512개 활성 경고 규칙 리소스당 200개 활성 경고 규칙 | 지원 요청 |
| 작업 그룹 |구독당 2,000개 작업 그룹 | 지원 요청 |
| 경고 규칙 및 작업 규칙 설명 길이| 로그 검색 경고 4096자<br/>다른 모든 경고 2048자 | 기본값과 동일 |