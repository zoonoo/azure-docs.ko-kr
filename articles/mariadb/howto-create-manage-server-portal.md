---
title: 서버 관리 - Azure Portal - Azure Database for MariaDB
description: Azure Portal에서 Azure Database for MariaDB 서버를 관리하는 방법을 알아봅니다.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: ade70c884a3ef01db45273e4789f34b629473661
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98664404"
---
# <a name="manage-an-azure-database-for-mariadb-server-using-the-azure-portal"></a>Azure Portal을 사용하여 Azure Database for MariaDB 서버 관리
이 문서에서는 Azure Database for MariaDB 서버를 관리하는 방법을 설명합니다. 관리 작업에는 컴퓨팅 및 스토리지 스케일링, 관리자 암호 재설정, 서버 세부 정보 보기 등이 포함됩니다.

## <a name="sign-in"></a>로그인
[Azure Portal](https://portal.azure.com)에 로그인합니다.

## <a name="create-a-server"></a>서버 만들기
[빠른 시작](quickstart-create-mariadb-server-database-using-azure-portal.md)을 방문하여 Azure Database for MariaDB 서버를 만들고 시작하는 방법을 알아보세요.

## <a name="scale-compute-and-storage"></a>컴퓨팅 및 스토리지 스케일링

서버를 만든 후에는 요구 사항이 변경됨에 따라 범용과 메모리 최적화 계층 간에 크기를 조정할 수 있습니다. vCore를 늘리거나 줄여서 컴퓨팅과 메모리 크기를 조정할 수도 있습니다. 스토리지는 확장할 수 있습니다(그러나 스토리지 축소는 불가능함).

### <a name="scale-between-general-purpose-and-memory-optimized-tiers"></a>범용 및 메모리 최적화 계층 간 크기 조정

범용에서 메모리 최적화로, 또는 그 반대로 크기를 조정할 수 있습니다. 서버 만들기가 지원되지 않게 되면 기본 계층을 변경하십시오. 

1. Azure Portal에서 서버를 선택합니다. **가격 책정 계층** 을 **설정** 섹션에서 선택합니다.

2. 스케일링하는 대상에 따라 **범용** 또는 **메모리 최적화** 를 선택합니다. 

    ![가격 책정 계층이 선택되고 메모리 최적화 값이 선택된 Azure Portal의 스크린샷.](./media/howto-create-manage-server-portal/change-pricing-tier.png)

    > [!NOTE]
    > 계층을 변경하면 서버가 다시 시작됩니다.

4. **확인** 을 선택하여 변경 사항을 저장합니다.


### <a name="scale-vcores-up-or-down"></a>VCores 확장 또는 축소

1. Azure Portal에서 서버를 선택합니다. **가격 책정 계층** 을 **설정** 섹션에서 선택합니다.

2. 슬라이더를 원하는 값으로 이동하여 **vCore** 설정을 변경합니다.

    ![scale-compute](./media/howto-create-manage-server-portal/scaling-compute.png)

    > [!NOTE]
    > vCore를 스케일링하면 서버가 다시 시작됩니다.

3. **확인** 을 선택하여 변경 사항을 저장합니다.


### <a name="scale-storage-up"></a>스토리지 확장

1. Azure Portal에서 서버를 선택합니다. **가격 책정 계층** 을 **설정** 섹션에서 선택합니다.

2. 슬라이더를 원하는 값으로 이동하여 **스토리지** 설정을 변경합니다.

    ![scale-storage](./media/howto-create-manage-server-portal/scaling-storage.png)

    > [!NOTE]
    > 스토리지를 축소할 수는 없습니다.

3. **확인** 을 선택하여 변경 사항을 저장합니다.


## <a name="update-admin-password"></a>관리자 암호 업데이트
Azure Portal을 사용하여 관리자 역할의 암호를 변경할 수 있습니다.

1. Azure Portal에서 서버를 선택합니다. **개요** 창에서 **암호 재설정** 을 선택합니다.

   ![개요](./media/howto-create-manage-server-portal/overview-reset-password.png)

2. 새 암호를 입력하고 암호를 확인합니다. 텍스트 상자에 암호 복잡성 요구 사항에 관한 메시지가 표시됩니다.

   ![암호 및 암호 확인이 표시된 암호 재설정 대화 상자를 보여주는 스크린샷.](./media/howto-create-manage-server-portal/reset-password.png)

3. **확인** 을 선택하여 새 암호를 저장합니다.


## <a name="delete-a-server"></a>서버 삭제

더 이상 필요하지 않은 경우 서버를 삭제할 수 있습니다. 

1. Azure Portal에서 서버를 선택합니다. **개요** 창에서 **삭제** 를 선택합니다.

    ![delete](./media/howto-create-manage-server-portal/overview-delete.png)

2. 입력 상자에 서버 이름을 입력하여 서버 삭제를 확인합니다.

    ![스크린샷에는 데이터베이스를 삭제할지 여부를 확인하는 대화 상자가 표시됩니다. 이 대화 상자는 취소할 수 없습니다.](./media/howto-create-manage-server-portal/confirm-delete.png)

    > [!NOTE]
    > 서버를 삭제한 후에는 되돌릴 수 없습니다.

3. **삭제** 를 선택합니다.


## <a name="next-steps"></a>다음 단계
- [백업 및 서버 복원](howto-restore-server-portal.md) 에 대한 자세한 정보
- [Azure Database for MariaDB의 튜닝 및 모니터링 옵션](concepts-monitoring.md) 에 대해 알아봅니다
