---
title: 전용 SQL 풀에 대 한 사용자 정의 복원 지점 만들기
description: 전용 SQL 풀의 복원 지점을 만드는 방법
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
ms.openlocfilehash: c0835fb48d00fe5277732f34fd6b0de1448f6a78
ms.sourcegitcommit: 46c5ffd69fa7bc71102737d1fab4338ca782b6f1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2020
ms.locfileid: "94332134"
---
# <a name="user-defined-restore-points"></a>사용자 정의 복원 지점이

이 문서에서는 Azure Portal를 사용 하 여 Azure Synapse Analytics에서 전용 SQL 풀에 대해 새 사용자 정의 복원 지점을 만드는 방법을 배웁니다.

## <a name="create-user-defined-restore-points-through-the-azure-portal"></a>Azure Portal를 통해 사용자 정의 복원 시점 만들기

Azure Portal를 통해 사용자 정의 복원 지점이 만들어질 수도 있습니다.

1. [Azure Portal](https://portal.azure.com/) 계정에 로그인 합니다.

2. 복원 지점을 만들려는 전용 SQL 풀로 이동 합니다.

3. 왼쪽 창에서 **개요** 를 선택 하 고 **+ 새 복원 지점** 을 선택 합니다. 새 복원 지점 단추를 사용할 수 없는 경우 전용 SQL 풀이 일시 중지 되지 않았는지 확인 합니다.

    ![새 복원 지점](../media/sql-pools/create-sqlpool-restore-point-01.png)

4. 사용자 정의 복원 지점의 이름을 지정 하 고 **적용** 을 클릭 합니다. 사용자 정의 복원 지점의 기본 보존 기간은 7 일입니다.

    ![복원 지점에 대한 이름](../media/sql-pools/create-sqlpool-restore-point-02.png)

## <a name="next-steps"></a>다음 단계

- [기존 전용 SQL 풀 복원](restore-sql-pool.md)

