---
title: 파일 포함
description: 포함 파일
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 10/01/2020
ms.author: robb
ms.custom: include file
ms.openlocfilehash: 6f9405e1b402b029b628821824a1727dd825a031
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101734047"
---
| 리소스 | 기본 제한 | 최대 제한 |
| --- | --- | --- |
| Azure 앱 푸시 | 작업 그룹당 10개의 Azure 앱 작업. | 기본값과 동일 |
| 메일 | 작업 그룹당 1000개의 이메일 작업.<br>시간당 메일 100개 이하.<br>또한 [속도 제한 정보](../articles/azure-monitor/alerts/alerts-rate-limiting.md)를 참조하세요. | 기본값과 동일 |
| ITSM | 작업 그룹당 10개의 ITSM 작업. | 기본값과 동일 | 
| 논리 앱 | 작업 그룹당 10개 논리 앱 작업. | 기본값과 동일 |
| Runbook | 작업 그룹당 10개의 Runbook 작업. | 기본값과 동일 |
| SMS | 작업 그룹당 10개의 SMS 작업.<br>5분마다 1개 이하의 SMS 메시지.<br>또한 [속도 제한 정보](../articles/azure-monitor/alerts/alerts-rate-limiting.md)를 참조하세요. | 기본값과 동일 |
| 음성 | 작업 그룹당 10개의 음성 작업.<br>5분마다 1개 이하의 음성 통화.<br>또한 [속도 제한 정보](../articles/azure-monitor/alerts/alerts-rate-limiting.md)를 참조하세요. | 기본값과 동일 |
| 웹후크 | 작업 그룹당 10개의 webhook 작업.  최대 웹후크 호출 수는 구독당 분당 1500개입니다. [작업별 정보](../articles/azure-monitor/alerts/action-groups.md#action-specific-information)에서 다른 제한을 확인할 수 있습니다.  | 기본값과 동일 |