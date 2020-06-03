---
title: 포함 파일
description: 포함 파일
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 05/18/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 7e7a0424e4454639211c6494aab0700e75269361
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83720895"
---
이벤트 도메인이 *아닌* Azure Event Grid 시스템 토픽 및 사용자 지정 토픽에는 다음과 같은 제한이 적용됩니다.

| 리소스 | 제한 |
| --- | --- |
| Azure 구독당 사용자 지정 토픽 | 100 |
| 토픽당 이벤트 구독 | 500 |
| 사용자 지정 토픽에 대한 게시 비율(수신) | 토픽별 초당 5,000개 이벤트 |
| 이벤트 크기 | 1MB 작업에는 64KB 단위로 요금이 부과됩니다. 따라서 64KB를 초과하는 이벤트는 여러 이벤트인 것처럼 작업 요금이 발생합니다. 예를 들어 130KB인 이벤트는 별도의 3개 이벤트처럼 작업을 발생시킵니다.  |
| 이벤트 도메인당 토픽 | 100,000 |
| 도메인 내 토픽당 이벤트 구독 | 500 |
| 도메인 범위 이벤트 구독 | 50 |
| 이벤트 도메인에 대한 게시 비율(수신) | 초당 5,000개 이벤트 |
| Azure 구독당 이벤트 도메인 | 100 |
| 토픽 또는 도메인당 프라이빗 엔드포인트 연결 | 64 | 
| 토픽 또는 도메인당 IP Firewall 규칙 | 16 | 