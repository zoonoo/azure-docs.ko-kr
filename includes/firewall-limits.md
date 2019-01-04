---
title: 포함 파일
description: 포함 파일
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 12/14/2018
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 7d905550114bb76a0a091146b3972bab4a652022
ms.sourcegitcommit: b254db346732b64678419db428fd9eb200f3c3c5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/14/2018
ms.locfileid: "53430007"
---
| 리소스 | 기본 제한 |
| --- | --- |
| 처리된 데이터 |1000TB/방화벽/월 <sup>1</sup> |
|규칙|10k - 모든 규칙 유형 결합됨|
|글로벌 피어링|지원되지 않습니다. 지역당 하나 이상의 방화벽 배포가 있어야 합니다.|
|단일 네트워크 규칙의 최대 포트|15<br>포트 범위(예: 2 - 10)는 2로 계산됩니다.
|AzureFirewallSubnet 최소 크기 |/26|
|네트워크 및 애플리케이션 규칙의 포트 범위|0-64,000 이 제한을 완화하는 작업이 진행 중입니다.|
|


<sup>1</sup> 이러한 제한을 늘려야 하는 경우 Azure 지원에 문의하세요.
