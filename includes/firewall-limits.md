---
title: 포함 파일
description: 포함 파일
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 11/26/2018
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 8b236bc1f0089b89aca90e7c69e9b445b01a374b
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/27/2018
ms.locfileid: "52440294"
---
| 리소스 | 기본 제한 |
| --- | --- |
| 처리된 데이터 |1000TB/방화벽/월 <sup>1</sup> |
|규칙|10k - 모든 규칙 유형 결합됨|
|글로벌 피어링|지원되지 않습니다. 지역당 하나 이상의 방화벽 배포가 있어야 합니다.|
|단일 네트워크 규칙의 최대 포트|15<br>포트 범위(예: 2-10)는 2로 계산됩니다.
|AzureFirewallSubnet 최소 크기 |/26


<sup>1</sup> 이러한 제한을 늘려야 하는 경우 Azure 지원에 문의하세요.
