---
title: 포함 파일
description: 포함 파일
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/23/2020
ms.author: dacoulte
ms.openlocfilehash: e9faea1d5913a19dfdeff662e26992529dc1b22d
ms.sourcegitcommit: ba8df8424d73c8c4ac43602678dae4273af8b336
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/05/2020
ms.locfileid: "84466810"
---
Azure Policy의 각 개체 형식에 대한 최대 수가 있습니다. _범위_ 항목은 구독 또는 [관리 그룹](../articles/governance/management-groups/overview.md)을 의미합니다.

| Where | 대상 | 최대 수 |
|---|---|---|
| 범위 | 정책 정의 | 500 |
| 범위 | 이니셔티브 정의 | 100 |
| 테넌트 | 이니셔티브 정의 | 2,500 |
| 범위 | 정책 또는 이니셔티브 할당 | 100 |
| 정책 정의 | 매개 변수 | 20 |
| 이니셔티브 정의 | 정책 | 100 |
| 이니셔티브 정의 | 매개 변수 | 100 |
| 정책 또는 이니셔티브 할당 | 제외(notScopes) | 400 |
| 정책 규칙 | 중첩된 조건부 | 512 |
| 수정 작업 | 리소스 | 500 |
