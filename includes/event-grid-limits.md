---
title: 파일 포함
description: 포함 파일
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 02/17/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 2f52e8a89ec9dd78a1951836053cb2c698310bbd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100645385"
---
다음 제한은 Azure Event Grid **토픽**(시스템, 사용자 지정 및 파트너 항목)에 적용됩니다. 

| 리소스 | 제한 |
| --- | --- |
| Azure 구독당 사용자 지정 토픽 | 100 |
| 토픽당 이벤트 구독 | 500 |
| 사용자 지정 또는 파트너 토픽에 대한 게시 비율(수신) | 5000개 이벤트/초 또는 5MB/초(둘 중 먼저 충족되는 것) |
| 이벤트 크기 | 1MB  |
| 토픽당 프라이빗 엔드포인트 연결  | 64 | 
| 토픽당 IP Firewall 규칙 | 16 | 

Azure Event Grid **도메인** 에는 다음과 같은 제한이 적용됩니다. 

| 리소스 | 제한 |
| --- | --- |
| 이벤트 도메인당 토픽 | 100,000 |
| 도메인 내 토픽당 이벤트 구독 | 500 |
| 도메인 범위 이벤트 구독 | 50 |
| 이벤트 도메인에 대한 게시 비율(수신) | 5000개 이벤트/초 또는 5MB/초(둘 중 먼저 충족되는 것) |
| Azure 구독당 이벤트 도메인 | 100 |
| 도메인당 프라이빗 엔드포인트 연결 | 64 | 
| 도메인당 IP 방화벽 규칙 | 16 | 


