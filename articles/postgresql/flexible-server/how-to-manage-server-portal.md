---
title: 서버 관리-Azure Portal-Azure Database for PostgreSQL 유연한 서버
description: Azure Portal에서 Azure Database for PostgreSQL 유연한 서버를 관리 하는 방법을 알아봅니다.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.custom: mvc
ms.openlocfilehash: 1ac418d855696138341115412dc7e2601d4cf3a1
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91961411"
---
# <a name="manage-an-azure-database-for-postgresql---flexible-server-using-the-azure-portal"></a>Azure Portal를 사용 하 여 Azure Database for PostgreSQL 유연한 서버 관리

> [!IMPORTANT]
> Azure Database for PostgreSQL - 유연한 서버는 미리 보기로 제공됨

이 문서에서는 Azure Database for PostgreSQL 유연한 서버를 관리 하는 방법을 보여 줍니다. 관리 작업에는 계산 및 저장소 크기 조정, 관리자 암호 재설정 및 서버 세부 정보 보기가 포함 됩니다.

## <a name="sign-in"></a>로그인

[Azure Portal](https://portal.azure.com)에 로그인합니다. Azure Portal에서 유연한 서버 리소스로 이동 합니다.

## <a name="scale-compute-and-storage"></a>계산 및 저장소 크기 조정

서버를 만든 후에는 요구 사항이 변경 됨에 따라 다양 한 [가격 책정 계층](https://azure.microsoft.com/pricing/details/postgresql/) 을 확장할 수 있습니다. VCores를 늘리거나 줄여서 계산 및 메모리를 확장 하거나 축소할 수도 있습니다.

> [!NOTE]
> 저장소를 낮은 값으로 축소할 수 없습니다.

1. Azure Portal에서 서버를 선택 합니다. **설정** 섹션에 있는 **Compute + Storage**를 선택 합니다.
2. **계산 계층** , vcore, **저장소** 를 변경 하 여 더 높은 계산 계층을 사용 하 여 서버를 확장 하거나, 저장소 또는 **vcore**를 원하는 값으로 증가 시켜 동일한 계층 내에서 확장할 수 있습니다.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/howto-manage-server-portal/scale-server.png" alt-text="저장소 유연한 서버 크기 조정":::

> [!Important]
> - 저장소 크기를 축소할 수 없습니다.
> - VCores 크기를 조정 하면 서버를 다시 시작 합니다.

3. **확인** 을 선택 하 여 변경 내용을 저장 합니다.

## <a name="reset-admin-password"></a>관리자 암호 다시 설정

Azure Portal를 사용 하 여 관리자 역할의 암호를 변경할 수 있습니다.

1. Azure Portal에서 서버를 선택 합니다. **개요** 창에서 **암호 재설정**을 선택 합니다.
2. 새 암호를 입력하고 암호를 확인합니다. 텍스트 상자에 암호 복잡성 요구 사항에 대 한 메시지가 표시 됩니다.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/howto-manage-server-portal/reset-password.png" alt-text="저장소 유연한 서버 크기 조정":::

3. **저장** 을 선택 하 여 새 암호를 저장 합니다.

## <a name="delete-a-server"></a>서버 삭제

서버를 더 이상 필요 하지 않은 경우 삭제할 수 있습니다.

1. Azure Portal에서 서버를 선택 합니다. **개요** 창에서 **삭제**를 선택 합니다.
2. 입력 상자에 서버 이름을 입력 하 여 서버 삭제를 확인 합니다.

   :::image type="content" source="./media/howto-manage-server-portal/delete-server.png" alt-text="저장소 유연한 서버 크기 조정":::

   > [!IMPORTANT]
   > 서버 삭제는 취소할 수 없습니다.

  > [!div class="mx-imgBorder"]
  > ![유연한 서버 삭제](./media/howto-manage-server-portal/delete-server.png)  

3. **삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

- [백업 및 복원 개념 이해](concepts-backup-restore.md)
- [서버 튜닝 및 모니터링](concepts-monitoring.md)
