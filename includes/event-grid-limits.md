---
title: 파일 포함
description: 포함 파일
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 05/22/2019
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 5992726893b722b0aa46c976a0167793f5ee6bb4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82131547"
---
다음 제한은 이벤트 도메인이 *아닌* 시스템 항목 및 사용자 지정 항목 Azure Event Grid에 적용 됩니다.

| 리소스 | 제한 |
| --- | --- |
| Azure 구독당 사용자 지정 토픽 | 100 |
| 토픽당 이벤트 구독 | 500 |
| 사용자 지정 토픽에 대한 게시 비율(수신) | 토픽별 초당 5,000개 이벤트 |
| 게시 요청 | 초당 250 |
| 이벤트 크기 | 1mb 작업에는 64 KB 씩 요금이 부과 됩니다. 따라서 64 KB를 초과 하는 이벤트는 여러 이벤트 처럼 작업 요금을 발생 시킵니다. 예를 들어 130 KB 인 이벤트는 별도의 3 개 이벤트 처럼 작업을 발생 시킵니다.  |

다음 제한은 이벤트 도메인에만 적용 됩니다.

| 리소스 | 제한 |
| --- | --- |
| 이벤트 도메인당 항목 | 100,000 |
| 도메인 내의 토픽 당 이벤트 구독 | 500 |
| 도메인 범위 이벤트 구독 | 50 |
| 이벤트 도메인의 게시 빈도 (수신) | 초당 5000 이벤트 |
| 게시 요청 | 초당 250 |
| Azure 구독 당 이벤트 도메인 | 100 |