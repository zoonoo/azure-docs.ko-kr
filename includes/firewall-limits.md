---
title: 포함 파일
description: 포함 파일
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 7/30/2018
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 0a4a66543b323b1dbe56158851dafcb1dfe695f0
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47185993"
---
| 리소스 | 기본 제한 |
| --- | --- |
| 처리된 데이터 |1000TB/방화벽/월 <sup>1</sup> |
|규칙|10,000개의 응용 프로그램 규칙, 10,000개의 네트워크 규칙|
|VNet 피어링|허브 및 스포크 구현의 경우, 스포크 VNet 최대 50개.|
|글로벌 피어링|지원되지 않습니다. 지역당 하나 이상의 방화벽 배포가 있어야 합니다.|
|단일 네트워크 규칙의 최대 포트|15<br>포트 범위(예: 2-10)는 2로 계산됩니다.


<sup>1</sup> 이러한 제한을 늘려야 하는 경우 Azure 지원에 문의하세요.
