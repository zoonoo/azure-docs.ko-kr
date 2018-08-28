---
title: 포함 파일
description: 포함 파일
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 08/16/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: 0e5d1214a8e1af8299cb40d1a3b31ff6eafc8c5c
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2018
ms.locfileid: "40184919"
---
Azure Policy의 각 개체 형식에 대한 최대 수가 있습니다. _범위_ 항목은 구독 또는 [관리 그룹](../articles/azure-resource-manager/management-groups-overview.md)을 의미합니다.

| Where | 대상 | 최대 수 |
|---|---|---|
| 범위 | 정책 정의 | 250 |
| 범위 | 이니셔티브 정의 | 100 |
| 테넌트 | 이니셔티브 정의 | 1000 |
| 범위 | 정책/이니셔티브 할당 | 100 |
| 정책 정의 | 매개 변수 | 20 |
| 이니셔티브 정의 | 정책 | 100 |
| 이니셔티브 정의 | 매개 변수 | 100 |
| 정책/이니셔티브 할당 | 제외(notScopes) | 100 |
| 정책 규칙 | 중첩된 조건부 | 512 |
