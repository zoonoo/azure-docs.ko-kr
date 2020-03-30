---
title: 서버 관리 - Azure 포털 - MySQL용 Azure 데이터베이스
description: Azure 포털에서 MySQL 서버에 대한 Azure 데이터베이스를 관리하는 방법을 알아봅니다.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: c0bee0b628a49746a19545d14b8b8761d0e880d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062413"
---
# <a name="manage-an-azure-database-for-mysql-server-using-the-azure-portal"></a>Azure 포털을 사용하여 MySQL 서버에 대한 Azure 데이터베이스 관리
이 문서에서는 MySQL 서버에 대한 Azure 데이터베이스를 관리하는 방법을 보여 줍니다. 관리 작업에는 계산 및 저장소 확장, 관리자 암호 재설정 및 서버 세부 정보 보기가 포함됩니다.

## <a name="sign-in"></a>로그인
[Azure 포털에](https://portal.azure.com)로그인합니다.

## <a name="create-a-server"></a>서버 만들기
빠른 [시작을](quickstart-create-mysql-server-database-using-azure-portal.md) 방문하여 MySQL 서버용 Azure 데이터베이스를 만들고 시작하는 방법을 알아봅니다.

## <a name="scale-compute-and-storage"></a>컴퓨팅 및 스토리지 확장

서버를 만든 후 필요에 따라 범용 및 메모리 최적화 계층 간에 확장할 수 있습니다. vCore를 늘리거나 줄임으로써 계산 및 메모리의 크기를 조정할 수도 있습니다. 스토리지를 확장할 수 있습니다(그러나 저장소를 축소할 수는 없습니다).

### <a name="scale-between-general-purpose-and-memory-optimized-tiers"></a>범용 및 메모리 최적화 계층 간 배율 조정

범용 에서 메모리 최적화로 확장할 수 있으며 그 반대의 경우도 마찬가지입니다. 서버 를 만든 후 기본 계층으로 변경은 지원되지 않습니다. 

1. Azure 포털에서 서버를 선택합니다. **설정** 섹션에 있는 **가격 책정 계층을**선택합니다.

2. 크기 조정 내용에 따라 **범용** 또는 **메모리 최적화를**선택합니다. 

    ![변경 가격 책정 계층](./media/howto-create-manage-server-portal/change-pricing-tier.png)

    > [!NOTE]
    > 계층을 변경하면 서버가 다시 시작됩니다.

4. 변경 내용을 저장하려면 **확인을** 선택합니다.


### <a name="scale-vcores-up-or-down"></a>vCore 확장 또는 축소

1. Azure 포털에서 서버를 선택합니다. **설정** 섹션에 있는 **가격 책정 계층을**선택합니다.

2. 슬라이더를 원하는 값으로 이동하여 **vCore** 설정을 변경합니다.

    ![scale-compute](./media/howto-create-manage-server-portal/scaling-compute.png)

    > [!NOTE]
    > vCore를 확장하면 서버가 다시 시작됩니다.

3. 변경 내용을 저장하려면 **확인을** 선택합니다.


### <a name="scale-storage-up"></a>스토리지 확장

1. Azure 포털에서 서버를 선택합니다. **설정** 섹션에 있는 **가격 책정 계층을**선택합니다.

2. 슬라이더를 원하는 값으로 이동하여 **저장소** 설정을 변경합니다.

    ![scale-storage](./media/howto-create-manage-server-portal/scaling-storage.png)

    > [!NOTE]
    > 저장소를 축소할 수 없습니다.

3. 변경 내용을 저장하려면 **확인을** 선택합니다.


## <a name="update-admin-password"></a>관리자 암호 업데이트
Azure 포털을 사용하여 관리자 역할의 암호를 변경할 수 있습니다.

1. Azure 포털에서 서버를 선택합니다. **개요** 창에서 **암호 재설정을**선택합니다.

   ![개요](./media/howto-create-manage-server-portal/overview-reset-password.png)

2. 새 암호를 입력하고 암호를 확인합니다. 텍스트 상자는 암호 복잡성 요구 사항에 대해 묻는 메시지가 표시됩니다.

   ![reset-password](./media/howto-create-manage-server-portal/reset-password.png)

3. 새 암호를 저장하려면 **확인을** 선택합니다.


## <a name="delete-a-server"></a>서버 삭제

더 이상 필요하지 않은 경우 서버를 삭제할 수 있습니다. 

1. Azure 포털에서 서버를 선택합니다. **개요** 창에서 **삭제를**선택합니다.

    ![delete](./media/howto-create-manage-server-portal/overview-delete.png)

2. 서버 이름을 입력 상자에 입력하여 삭제하려는 서버임을 확인합니다.

    ![confirm-delete](./media/howto-create-manage-server-portal/confirm-delete.png)

    > [!NOTE]
    > 서버삭제는 되돌릴 수 없습니다.

3. **삭제를 선택합니다.**


## <a name="next-steps"></a>다음 단계
- 백업 [및 서버 복원에](howto-restore-server-portal.md) 대해 알아보기
- [MySQL용 Azure 데이터베이스에서 튜닝 및 모니터링 옵션에 대해](concepts-monitoring.md) 알아보기
