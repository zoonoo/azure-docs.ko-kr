---
title: 파일 포함
description: 포함 파일
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 02/07/2019
ms.author: robb
ms.custom: include file
ms.openlocfilehash: 2922a1fb92f3df07429d3463b8bf639f336776af
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/26/2020
ms.locfileid: "67305307"
---
| 리소스 | 기본 제한 | 최대 제한 |
| --- | --- | --- |
| Azure 앱 푸시 | 작업 그룹당 10 개의 Azure 앱 작업. | 지원 요청 |
| 메일 | 1000 작업 그룹에서 전자 메일 작업을 수행 합니다.<br>시간당 메일 100개 이하.<br>또한 [rate 제한 정보](../articles/azure-monitor/platform/alerts-rate-limiting.md)를 참조 하세요. | 지원 요청 |
| ITSM | 작업 그룹의 10 ITSM 작업 | 지원 요청 | 
| 논리 앱 | 작업 그룹에서 10 개의 논리 앱 작업 | 지원 요청 |
| Runbook | 작업 그룹의 runbook 작업 10 개 | 지원 요청 |
| SMS | 작업 그룹에서 SMS 작업 10 개<br>5 분 마다 SMS 메시지는 2 개이 하입니다.<br>또한 [rate 제한 정보](../articles/azure-monitor/platform/alerts-rate-limiting.md)를 참조 하세요. | 지원 요청 |
| 음성 | 작업 그룹의 음성 작업 10 개<br>5 분 마다 음성 통화를 1 개 이하로 합니다.<br>또한 [rate 제한 정보](../articles/azure-monitor/platform/alerts-rate-limiting.md)를 참조 하세요. | 지원 요청 |
| 웹후크 | 작업 그룹에서 webhook 작업 10 개  최대 webhook 호출 수는 구독 당 분당 1500입니다. 다른 한도는 작업별 [정보](../articles/azure-monitor/platform/action-groups.md#action-specific-information)에서 사용할 수 있습니다.  | 지원 요청 |
