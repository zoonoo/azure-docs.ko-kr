---
title: 기존 전용 SQL 풀 복원
description: 기존 전용 SQL 풀을 복원 하기 위한 방법 가이드입니다.
services: synapse-analytics
author: joannapea
manager: igorstan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 10/29/2020
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 86150107273b247bef2c74e5b8c5272d7148587e
ms.sourcegitcommit: 46c5ffd69fa7bc71102737d1fab4338ca782b6f1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2020
ms.locfileid: "94332100"
---
# <a name="restore-an-existing-dedicated-sql-pool"></a>기존 전용 SQL 풀 복원

이 문서에서는 Azure Portal 및 Synapse Studio를 사용 하 여 Azure Synapse Analytics에서 기존 전용 SQL 풀을 복원 하는 방법에 대해 알아봅니다. 이 문서는 복원과 지역 복원 모두에 적용 됩니다. 

## <a name="restore-an-existing-dedicated-sql-pool-through-the-synapse-studio"></a>Synapse Studio를 통해 기존 전용 SQL 풀 복원

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. Synapse 작업 영역으로 이동 합니다. 
3. 시작-Synapse Studio > 열기에서 **열기** 를 선택 합니다.

    ![ Synapse Studio](../media/sql-pools/open-synapse-studio.png)
4. 왼쪽 탐색 창에서 **데이터** 를 선택 합니다.
5. **풀 관리** 를 선택 합니다. 
6. **+ 새로** 만들기를 선택 하 여 새 전용 SQL 풀을 만듭니다. 
7. 추가 설정 탭에서 복원할 복원 지점을 선택 합니다. 

    지역 복원을 수행 하려면 복구 하려는 작업 영역 및 전용 SQL 풀을 선택 합니다. 

8. **자동 복원 지점** 또는 **사용자 정의 복원 지점** 중 하나를 선택합니다. 

    ![복원 지점](../media/sql-pools/restore-point.PNG)

    전용 SQL 풀에 자동 복원 지점이 없는 경우 몇 시간을 기다리거나 복원 하기 전에 사용자 정의 복원 지점을 만듭니다. User-Defined 복원 지점의 경우 기존 항목을 선택 하거나 새로 만듭니다.

    지역 백업을 복원 하는 경우 원본 지역 및 복원 하려는 전용 SQL 풀에 있는 작업 영역을 선택 하면 됩니다. 

9. **검토 + 만들기** 를 선택합니다.

## <a name="restore-an-existing-dedicated-sql-pool-through-the-azure-portal"></a>Azure Portal를 통해 기존 전용 SQL 풀 복원

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. 복원 하려는 전용 SQL 풀로 이동 합니다.
3. [개요] 블레이드의 위쪽에서 **복원** 을 선택합니다.

    ![ 복원 개요](../media/sql-pools/restore-sqlpool-01.png)

4. **자동 복원 지점** 또는 **사용자 정의 복원 지점** 중 하나를 선택합니다. 

    전용 SQL 풀에 자동 복원 지점이 없는 경우 몇 시간을 기다리거나 복원 하기 전에 사용자 정의 복원 지점을 만듭니다. 

    지역 복원을 수행 하려면 복구 하려는 작업 영역 및 전용 SQL 풀을 선택 합니다. 

5. **검토 + 만들기** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

- [복원 지점 만들기](sqlpool-create-restore-point.md)
