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
ms.openlocfilehash: 610713286e3cb7a084b2e81260797d4cac0ddad7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91642676"
---
| 리소스 | 기본 제한 | 최대 제한 |
| --- | --- | --- |
| Azure 앱 푸시 | 작업 그룹당 10개의 Azure 앱 작업. | 지원 요청 |
| 메일 | 작업 그룹당 1000개의 이메일 작업.<br>시간당 메일 100개 이하.<br>또한 [속도 제한 정보](../articles/azure-monitor/platform/alerts-rate-limiting.md)를 참조하세요. | 기본값과 동일 |
| ITSM | 작업 그룹당 10개의 ITSM 작업. | 지원 요청 | 
| 논리 앱 | 작업 그룹당 10개 논리 앱 작업. | 지원 요청 |
| Runbook | 작업 그룹당 10개의 Runbook 작업. | 지원 요청 |
| SMS | 작업 그룹당 10개의 SMS 작업.<br>5분마다 1개 이하의 SMS 메시지.<br>또한 [속도 제한 정보](../articles/azure-monitor/platform/alerts-rate-limiting.md)를 참조하세요. | 지원 요청 |
| 음성 | 작업 그룹당 10개의 음성 작업.<br>5분마다 1개 이하의 음성 통화.<br>또한 [속도 제한 정보](../articles/azure-monitor/platform/alerts-rate-limiting.md)를 참조하세요. | 지원 요청 |
| 웹후크 | 작업 그룹당 10개의 webhook 작업.  최대 웹후크 호출 수는 구독당 분당 1500개입니다. [작업별 정보](../articles/azure-monitor/platform/action-groups.md#action-specific-information)에서 다른 제한을 확인할 수 있습니다.  | 지원 요청 |
