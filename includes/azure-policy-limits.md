---
title: 포함 파일
description: 포함 파일
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/30/2019
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: 7907504401f4b47aafe6032ea895d9647e6c303c
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66420787"
---
Azure Policy의 각 개체 형식에 대한 최대 수가 있습니다. _범위_ 항목은 구독 또는 [관리 그룹](../articles/governance/management-groups/overview.md)을 의미합니다.

| Where | 대상 | 최대 수 |
|---|---|---|
| 범위 | 정책 정의 | 250 |
| 범위 | 이니셔티브 정의 | 100 |
| 테넌트 | 이니셔티브 정의 | 1,000 |
| 범위 | 정책 또는 이니셔티브 할당 | 100 |
| 정책 정의 | 매개 변수 | 20 |
| 이니셔티브 정의 | 정책 | 100 |
| 이니셔티브 정의 | 매개 변수 | 100 |
| 정책 또는 이니셔티브 할당 | 제외(notScopes) | 400 |
| 정책 규칙 | 중첩된 조건부 | 512 |
