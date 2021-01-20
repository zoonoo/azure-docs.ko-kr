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
ms.openlocfilehash: 963e49ea0e5536be0fca6d565b439aef4a08793d
ms.sourcegitcommit: 65cef6e5d7c2827cf1194451c8f26a3458bc310a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/19/2021
ms.locfileid: "98605139"
---
| 리소스 | 기본 제한 | 최대 제한 |
| --- | --- | --- |
| Azure 앱 푸시 | 작업 그룹당 10개의 Azure 앱 작업. | 기본값과 동일 |
| 메일 | 작업 그룹당 1000개의 이메일 작업.<br>시간당 메일 100개 이하.<br>또한 [속도 제한 정보](../articles/azure-monitor/platform/alerts-rate-limiting.md)를 참조하세요. | 기본값과 동일 |
| ITSM | 작업 그룹당 10개의 ITSM 작업. | 기본값과 동일 | 
| 논리 앱 | 작업 그룹당 10개 논리 앱 작업. | 기본값과 동일 |
| Runbook | 작업 그룹당 10개의 Runbook 작업. | 기본값과 동일 |
| SMS | 작업 그룹당 10개의 SMS 작업.<br>5분마다 1개 이하의 SMS 메시지.<br>또한 [속도 제한 정보](../articles/azure-monitor/platform/alerts-rate-limiting.md)를 참조하세요. | 기본값과 동일 |
| 음성 | 작업 그룹당 10개의 음성 작업.<br>5분마다 1개 이하의 음성 통화.<br>또한 [속도 제한 정보](../articles/azure-monitor/platform/alerts-rate-limiting.md)를 참조하세요. | 기본값과 동일 |
| 웹후크 | 작업 그룹당 10개의 webhook 작업.  최대 웹후크 호출 수는 구독당 분당 1500개입니다. [작업별 정보](../articles/azure-monitor/platform/action-groups.md#action-specific-information)에서 다른 제한을 확인할 수 있습니다.  | 기본값과 동일 |
