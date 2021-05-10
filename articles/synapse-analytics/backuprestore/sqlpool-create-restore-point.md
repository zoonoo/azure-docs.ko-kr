---
title: 전용 SQL 풀에 대한 사용자 정의 복원 지점 만들기
description: Azure Portal을 사용하여 Azure Synapse Analytics에서 전용 SQL 풀에 대한 사용자 정의 복원 지점을 만드는 방법을 알아봅니다.
author: joannapea
manager: igorstan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 10/29/2020
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: b695f6c7aabc21541fcc48efed54bbecd022f65a
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567930"
---
# <a name="user-defined-restore-points"></a>사용자 정의 복원 지점

이 문서에서는 Azure Portal을 사용하여 Azure Synapse Analytics에서 전용 SQL 풀에 대해 새 사용자 정의 복원 지점을 만드는 방법을 알아봅니다.

## <a name="create-user-defined-restore-points-through-the-azure-portal"></a>Azure Portal을 통해 사용자 정의 복원 지점 만들기

Azure Portal을 통해 사용자 정의 복원 지점을 만들 수도 있습니다.

1. [Azure Portal](https://portal.azure.com/) 계정에 로그인합니다.

2. 복원 지점을 만들려는 전용 SQL 풀로 이동합니다.

3. 왼쪽 창에서 **개요** 를 선택하고 **+ 새 복원 지점** 을 선택합니다. 새 복원 지점 단추를 사용할 수 없는 경우 전용 SQL 풀이 일시 중지되지 않았는지 확인합니다.

    ![새 복원 지점](../media/sql-pools/create-sqlpool-restore-point-01.png)

4. 사용자 정의 복원 지점의 이름을 지정하고 **적용** 을 클릭합니다. 사용자 정의 복원 지점의 기본 보존 기간은 7일입니다.

    ![복원 지점에 대한 이름](../media/sql-pools/create-sqlpool-restore-point-02.png)

## <a name="next-steps"></a>다음 단계

- [기존 전용 SQL 풀 복원](restore-sql-pool.md)

