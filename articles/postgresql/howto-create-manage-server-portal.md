---
title: Azure Database for PostgreSQL 관리 - Azure Portal
description: Azure Portal에서 Azure Database for PostgreSQL 서버를 관리하는 방법을 알아봅니다.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 11/20/2019
ms.openlocfilehash: 4b581ac137a4172ab70e4fb3fb2a75e268115c06
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105604127"
---
# <a name="manage-an-azure-database-for-postgresql-server-using-the-azure-portal"></a>Azure Portal을 사용하여 Azure Database for PostgreSQL 서버 관리

이 문서에서는 Azure Database for PostgreSQL 서버를 관리하는 방법을 보여 줍니다. 관리 작업에는 컴퓨팅 및 스토리지 크기 조정, 관리자 암호 초기화 및 서버 세부 정보 보기가 포함됩니다.

## <a name="sign-in"></a>로그인

[Azure Portal](https://portal.azure.com)에 로그인합니다.

## <a name="create-a-server"></a>서버 만들기

Azure Database for PostgreSQL 서버를 만들고 시작하는 방법을 알아보려면 [빠른 시작](quickstart-create-server-database-portal.md)을 참조하세요.

## <a name="scale-compute-and-storage"></a>컴퓨팅 및 스토리지 스케일링

서버를 만든 후에는 요구 사항이 변경됨에 따라 범용과 메모리 최적화 계층 간에 크기를 조정할 수 있습니다. vCore를 늘리거나 줄여서 컴퓨팅과 메모리 크기를 조정할 수도 있습니다. 스토리지는 확장할 수 있습니다(그러나 스토리지 축소는 불가능함).

### <a name="scale-between-general-purpose-and-memory-optimized-tiers"></a>범용 및 메모리 최적화 계층 간 크기 조정

범용에서 메모리 최적화로, 또는 그 반대로 크기를 조정할 수 있습니다. 서버 만들기가 지원되지 않게 되면 기본 계층을 변경하십시오.

1. Azure Portal에서 서버를 선택합니다. **설정** 섹션에 있는 **가격 책정 계층** 을 선택합니다.

2. 크기를 조정하는 대상에 따라 **범용** 또는 **메모리 최적화** 를 선택합니다.

   :::image type="content" source="./media/howto-create-manage-server-portal/change-pricing-tier.png" alt-text="Azure Database for PostgreSQL에서 기본, 범용 또는 메모리 최적화 계층을 선택하는 Azure Portal 스크린샷":::

   > [!NOTE]
   > 계층을 변경하면 서버가 다시 시작됩니다.

3. **확인** 을 선택하여 변경 사항을 저장합니다.

### <a name="scale-vcores-up-or-down"></a>VCores 확장 또는 축소

1. Azure Portal에서 서버를 선택합니다. **설정** 섹션에 있는 **가격 책정 계층** 을 선택합니다.

2. 슬라이더를 원하는 값으로 이동하여 **vCore** 설정을 변경합니다.

   :::image type="content" source="./media/howto-create-manage-server-portal/scaling-compute.png" alt-text="Azure Database for PostgreSQL에서 vCore 옵션을 선택하는 Azure Portal 스크린샷":::

   > [!NOTE]
   > vCore를 스케일링하면 서버가 다시 시작됩니다.

3. **확인** 을 선택하여 변경 사항을 저장합니다.

### <a name="scale-storage-up"></a>스토리지 확장

1. Azure Portal에서 서버를 선택합니다. **설정** 섹션에 있는 **가격 책정 계층** 을 선택합니다.

2. 슬라이더를 원하는 값으로 이동하여 **스토리지** 설정을 변경합니다.

   :::image type="content" source="./media/howto-create-manage-server-portal/scaling-storage.png" alt-text="Azure Database for PostgreSQL에서 스토리지 크기 조정을 선택하는 Azure Portal 스크린샷":::

   > [!NOTE]
   > 스토리지를 축소할 수는 없습니다.

3. **확인** 을 선택하여 변경 사항을 저장합니다.

## <a name="update-admin-password"></a>관리자 암호 업데이트

Azure Portal을 사용하여 관리자 역할의 암호를 변경할 수 있습니다.

1. Azure Portal에서 서버를 선택합니다. **개요** 창에서 **암호 초기화** 를 선택합니다.

   :::image type="content" source="./media/howto-create-manage-server-portal/overview-reset-password.png" alt-text="Azure Database for PostgreSQL에서 암호를 초기화하는 Azure Portal 스크린샷":::

2. 새 암호를 입력하고 암호를 확인합니다. 텍스트 상자에 암호 복잡성 요구 사항에 대한 메시지가 표시됩니다.

   :::image type="content" source="./media/howto-create-manage-server-portal/reset-password.png" alt-text="Azure Database for PostgreSQL에서 암호를 초기화하고 저장하는 Azure Portal 스크린샷":::

3. **확인** 을 클릭하여 새 암호를 저장합니다.

## <a name="delete-a-server"></a>서버 삭제

더 이상 필요하지 않은 경우 서버를 삭제할 수 있습니다. 

1. Azure Portal에서 서버를 선택합니다. **개요** 창에서 **삭제** 를 선택합니다.

   :::image type="content" source="./media/howto-create-manage-server-portal/overview-delete.png" alt-text="Azure Database for PostgreSQL에서 서버를 삭제하는 Azure Portal 스크린샷":::

2. 입력 상자에 서버 이름을 입력하여 서버 삭제를 확인합니다.

   :::image type="content" source="./media/howto-create-manage-server-portal/confirm-delete.png" alt-text="Azure Database for PostgreSQL에서 서버 삭제를 확인하는 Azure Portal 스크린샷":::

   > [!NOTE]
   > 서버를 삭제한 후에는 되돌릴 수 없습니다.

3. **삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

- [백업 및 서버 복원](howto-restore-server-portal.md)에 대한 자세한 정보
- [Azure Database for PostgreSQL의 튜닝 및 모니터링 옵션](concepts-monitoring.md)에 대한 자세한 정보
