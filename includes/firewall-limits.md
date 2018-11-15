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
ms.openlocfilehash: f1fd32fc02bee47d229093680166e6a13a5ba721
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/08/2018
ms.locfileid: "49458851"
---
| 리소스 | 기본 제한 |
| --- | --- |
| 처리된 데이터 |1000TB/방화벽/월 <sup>1</sup> |
|규칙|10k - 모든 규칙 유형 결합됨|
|VNet 피어링|허브 및 스포크 구현의 경우, 스포크 VNet 최대 50개.|
|글로벌 피어링|지원되지 않습니다. 지역당 하나 이상의 방화벽 배포가 있어야 합니다.|
|단일 네트워크 규칙의 최대 포트|15<br>포트 범위(예: 2-10)는 2로 계산됩니다.


<sup>1</sup> 이러한 제한을 늘려야 하는 경우 Azure 지원에 문의하세요.
