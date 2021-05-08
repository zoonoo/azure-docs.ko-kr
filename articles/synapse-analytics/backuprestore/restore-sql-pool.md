---
title: 기존 전용 SQL 풀 복원
description: 기존 전용 SQL 풀을 복원하기 위한 방법 가이드입니다.
author: joannapea
manager: igorstan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 10/29/2020
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: f2fb6f809794781559683907a806e6d30ca9bed6
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567981"
---
# <a name="restore-an-existing-dedicated-sql-pool"></a>기존 전용 SQL 풀 복원

이 문서에서는 Azure Portal 및 Synapse Studio를 사용하여 Azure Synapse Analytics에서 기존 전용 SQL 풀을 복원하는 방법을 알아봅니다. 이 문서는 복원 및 지역 복원 모두에 적용됩니다. 

## <a name="restore-an-existing-dedicated-sql-pool-through-the-synapse-studio"></a>Synapse Studio를 통해 기존 전용 SQL 풀 복원

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. Synapse 작업 영역으로 이동합니다. 
3. 시작 -> Synapse Studio 열기에서 **열기** 를 선택합니다.

    ![ Synapse Studio](../media/sql-pools/open-synapse-studio.png)
4. 왼쪽의 탐색 창에서 **데이터** 를 선택합니다.
5. **풀 관리** 를 선택합니다. 
6. 새 전용 SQL 풀을 만들려면 **+ 새로 만들기** 를 선택합니다. 
7. 추가 설정 탭에서 복원할 복원 지점을 선택합니다. 

    지역 복원을 수행하려면 복구할 작업 영역과 전용 SQL 풀을 선택합니다. 

8. **자동 복원 지점** 또는 **사용자 정의 복원 지점** 중 하나를 선택합니다. 

    ![복원 지점](../media/sql-pools/restore-point.PNG)

    전용 SQL 풀에 자동 복원 지점이 없는 경우 복원하기 전에 몇 시간 동안 기다리거나 사용자 정의 복원 지점을 만듭니다. 사용자 정의 복원 지점의 경우 기존 복원 지점을 선택하거나 새로 만듭니다.

    지역 백업을 복원하는 경우 원본 지역에 있는 작업 영역과 복원할 전용 SQL 풀을 선택하기만 하면 됩니다. 

9. **검토 + 만들기** 를 선택합니다.

## <a name="restore-an-existing-dedicated-sql-pool-through-the-azure-portal"></a>Azure Portal를 통해 기존 전용 SQL 풀 복원

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. 복원하려는 전용 SQL 풀로 이동합니다.
3. [개요] 블레이드의 위쪽에서 **복원** 을 선택합니다.

    ![ 복원 개요](../media/sql-pools/restore-sqlpool-01.png)

4. **자동 복원 지점** 또는 **사용자 정의 복원 지점** 중 하나를 선택합니다. 

    전용 SQL 풀에 자동 복원 지점이 없는 경우 복원하기 전에 몇 시간 동안 기다리거나 사용자 정의 복원 지점을 만듭니다. 

    지역 복원을 수행하려면 복구할 작업 영역과 전용 SQL 풀을 선택합니다. 

5. **검토 + 만들기** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

- [복원 지점 만들기](sqlpool-create-restore-point.md)
