---
title: Azure Portal을 사용하여 Azure Database for MySQL 유연한 서버를 복원합니다.
description: 이 문서에서는 Azure Portal을 통해 Azure Database for MySQL 유연한 서버에서 복원 작업을 수행하는 방법을 설명합니다.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 04/01/2021
ms.openlocfilehash: 962a2cbdbcc238517616c9ade235eed9b8cae6f7
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502048"
---
# <a name="point-in-time-restore-of-a-azure-database-for-mysql---flexible-server-preview-using-azure-portal"></a>Azure Portal을 사용하여 Azure Database for MySQL - 유연한 서버(미리 보기)의 특정 시점 복원


> [!IMPORTANT]
> Azure Database for MySQL - 유연한 서버는 현재 공개 미리 보기로 제공됩니다.

이 문서에서는 백업을 사용하여 유연한 서버에서 지정 시간 복구를 수행하는 단계별 절차를 제공합니다.

## <a name="prerequisites"></a>사전 요구 사항

이 방법 가이드를 완료하려면 다음이 필요합니다.

-   Azure Database for MySQL 유연한 서버가 있어야 합니다.

## <a name="restore-to-the-latest-restore-point"></a>최신 복원 지점으로 복원

가장 빠른 기존 백업을 사용하여 유연한 서버를 복원하려면 다음 단계를 수행합니다.

1.  [Azure Portal](https://portal.azure.com/)에서 백업을 복원할 유연한 서버를 선택합니다.

2.  왼쪽 패널에서 **개요** 를 클릭합니다.

3.  개요 페이지에서 **복원** 을 클릭합니다.

4.  **최신 복원 지점** 과 사용자 지정 복원 지점 중에서 선택할 수 있는 옵션이 있는 복원 페이지가 표시됩니다.

5.  **최신 복원 지점** 을 선택합니다.

6.  **새 서버에 복원** 필드에 새 서버 이름을 제공합니다.

    :::image type="content" source="./media/concept-backup-restore/restore-blade-latest.png" alt-text="가장 빠른 복원 시간":::

8.  **확인** 을 클릭합니다.

9.  복원 작업이 시작되었다는 알림이 표시됩니다.

## <a name="restoring-to-a-custom-restore-point"></a>사용자 지정 복원 지점으로 복원

가장 빠른 기존 백업을 사용하여 유연한 서버를 복원하려면 다음 단계를 수행합니다.

1.  [Azure Portal](https://portal.azure.com/)에서 백업을 복원할 유연한 서버를 선택합니다.

2.  개요 페이지에서 **복원** 을 클릭합니다.

3.  가장 빠른 복원 지점과 사용자 지정 복원 지점 중에서 선택할 수 있는 옵션이 있는 복원 페이지가 표시됩니다.

4.  **사용자 지정 복원 지점** 을 선택합니다.

5.  날짜 및 시간을 선택합니다.

6.  **새 서버에 복원** 필드에 새 서버 이름을 제공합니다.

6.  **새 서버에 복원** 필드에 새 서버 이름을 제공합니다.

    :::image type="content" source="./media/concept-backup-restore/restore-blade-custom.png" alt-text="개요 보기":::

7.  **확인** 을 클릭합니다.

8.  복원 작업이 시작되었다는 알림이 표시됩니다.


## <a name="perform-post-restore-tasks"></a>복원 후 작업 수행
복원이 완료된 후 다음 작업을 수행하여 사용자와 애플리케이션을 다시 실행되도록 해야 합니다.

- 새 서버가 원래 서버를 교체하기 위한 것이라면 클라이언트와 클라이언트 애플리케이션을 새 서버로 리디렉션합니다.
- 사용자가 연결할 수 있도록 적합한 VNet 규칙이 설정되었는지 확인합니다. 이러한 규칙은 원래 서버에서 복사되지 않습니다.
- 적절한 로그인 및 데이터베이스 수준 권한이 있는지 확인합니다.
- 새로운 복원 서버에 대해 적절한 경고를 구성합니다.


## <a name="next-steps"></a>다음 단계
[비즈니스 연속성](concepts-business-continuity.md) 알아보기
