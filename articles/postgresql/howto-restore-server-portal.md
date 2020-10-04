---
title: 백업 및 복원-Azure Portal-Azure Database for PostgreSQL-단일 서버
description: 이 문서에서는 Azure Portal를 사용 하 여 Azure Database for PostgreSQL 단일 서버에서 서버를 복원 하는 방법을 설명 합니다.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 6/30/2020
ms.openlocfilehash: debdbf6e08af7b9005336231abd6c998a871c525
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/04/2020
ms.locfileid: "91708087"
---
# <a name="how-to-backup-and-restore-a-server-in-azure-database-for-postgresql---single-server-using-the-azure-portal"></a>Azure Portal를 사용 하 여 Azure Database for PostgreSQL 단일 서버에서 서버를 백업 및 복원 하는 방법

## <a name="backup-happens-automatically"></a>자동으로 수행되는 백업
Azure Database for PostgreSQL 서버는 정기적으로 백업되어 복원 기능을 사용하도록 설정할 수 있습니다. 이 기능을 사용하면 서버 및 모든 데이터베이스를 이전 특정 시점으로 새 서버에 복원할 수 있습니다.

## <a name="set-backup-configuration"></a>백업 구성 설정

서버를 만들 때 **가격 책정 계층** 창에서 로컬 중복 백업 또는 지역 중복 백업을 위한 서버 구성 중에서 선택할 수 있습니다.

> [!NOTE]
> 서버가 만들어지면 지리적으로 중복되거나 로컬로 중복된 중복 형식은 전환할 수 없습니다.
>

Azure Portal을 통해 서버를 만드는 중에 **가격 책정 계층** 창에서 서버에 대해 **로컬 중복** 또는 **지역 중복** 백업을 선택합니다. 또한 이 창에서는 **백업 보존 기간**(서버 백업을 저장하려는 기간(일))도 선택할 수 있습니다.

   :::image type="content" source="./media/howto-restore-server-portal/pricing-tier.png" alt-text="가격 책정 계층 - 백업 중복 선택":::

만드는 중에 이러한 값을 설정하는 방법에 대한 자세한 내용은 [Azure Database for PostgreSQL 서버 빠른 시작](quickstart-create-server-database-portal.md)을 참조하세요.

서버의 백업 보존 기간은 다음 단계를 통해 변경할 수 있습니다.
1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. Azure Database for PostgreSQL 서버를 선택합니다. 이 작업은 **개요** 페이지를 엽니다.
3. **설정** 아래의 메뉴에서 **가격 책정 계층**을 선택합니다. 슬라이더를 사용하여 **백업 보존 기간**을 7일에서 35일까지 원하는 대로 변경할 수 있습니다.
아래 스크린샷에서는 34일로 늘렸습니다.
:::image type="content" source="./media/howto-restore-server-portal/3-increase-backup-days.png" alt-text="가격 책정 계층 - 백업 중복 선택":::

4. **확인**을 클릭하여 변경 내용을 확인합니다.

백업 보존 기간은 사용 가능한 백업을 기반으로 하기 때문에 특정 시점 복원을 검색할 수 있는 시간을 제어합니다. 특정 시점 복원은 다음 섹션에서 자세히 설명합니다. 

## <a name="point-in-time-restore"></a>지정 시간 복원
Azure Database for PostgreSQL을 사용하면 특정 시점의 서버를 서버의 새 복사본으로 다시 복원할 수 있습니다. 이 새 서버를 사용하여 데이터를 복구하거나 클라이언트 애플리케이션에서 이 새 서버를 가리키도록 할 수 있습니다.

예를 들어 오늘 정오에 실수로 테이블을 삭제한 경우 정오 바로 전으로 복원하고 누락된 테이블과 데이터를 서버의 새로운 복사본에서 검색할 수 있습니다. 특정 시점 복원은 데이터베이스 수준이 아닌 서버 수준에 있습니다.

다음 단계는 샘플 서버를 특정 시점으로 복원합니다.
1. Azure Portal에서 Azure Database for PostgreSQL 서버를 선택합니다. 

2. 서버 **개요** 페이지의 도구 모음에서 **복원**을 선택합니다.

   :::image type="content" source="./media/howto-restore-server-portal/2-server.png" alt-text="가격 책정 계층 - 백업 중복 선택":::

3. 필요한 정보로 복원 양식을 채웁니다.

   :::image type="content" source="./media/howto-restore-server-portal/3-restore.png" alt-text="가격 책정 계층 - 백업 중복 선택":::
   - **복원 지점**: 복원하려는 특정 시점을 선택합니다.
   - **대상 서버**: 새 서버에 대한 이름을 제공합니다.
   - **위치**: 하위 지역을 선택할 수 없습니다. 기본적으로 원본 서버와 동일합니다.
   - **가격 책정 계층**: 특정 시점 복원을 수행할 때 이러한 매개 변수는 변경할 수 없습니다. 원본 서버와 동일합니다. 

4. **확인**을 클릭하여 특정 시점으로 복원할 서버를 복원합니다. 

5. 복원이 완료되면 예상대로 데이터가 복원되었는지 확인하기 위해 생성하였던 새 서버를 찾습니다.

특정 시점 복원으로 만든 새 서버에는 선택한 특정 시점 당시의 기존 서버에 유효한 동일한 서버 관리자 로그인 이름과 암호가 있습니다. 암호는 새 서버의 **개요** 페이지에서 변경할 수 있습니다.

복원 중에 만든 새 서버에는 원래 서버에 존재했던 방화벽 규칙 또는 VNet 서비스 엔드포인트가 없습니다. 이러한 규칙은 새 서버에 대해 개별적으로 설정돼야 합니다.

## <a name="geo-restore"></a>지역 복원

서버를 지리적으로 중복된 백업으로 구성한 경우 기존 서버의 백업에서 새 서버를 만들 수 있습니다. 이 새 서버는 Azure Database for PostgreSQL을 사용할 수 있는 모든 지역에서 만들 수 있습니다.  

1. 포털의 왼쪽 상단 모서리에서 **리소스 만들기** 단추(+)를 선택합니다. **데이터베이스** > **PostgreSQL용 Azure Database**를 차례로 선택합니다.

   :::image type="content" source="./media/howto-restore-server-portal/1-navigate-to-postgres.png" alt-text="가격 책정 계층 - 백업 중복 선택":::

2. **단일 서버** 배포 옵션을 선택합니다.

   :::image type="content" source="./media/howto-restore-server-portal/2-select-deployment-option.png" alt-text="가격 책정 계층 - 백업 중복 선택":::
 
3. 새 서버의 구독, 리소스 그룹 및 이름을 제공 합니다. 

4. **데이터 원본**으로 **백업** 을 선택 합니다. 이 작업은 지역 중복 백업을 사용 하도록 설정 된 서버 목록을 제공 하는 드롭다운을 로드 합니다.
   
   :::image type="content" source="./media/howto-restore-server-portal/4-geo-restore.png" alt-text="가격 책정 계층 - 백업 중복 선택":::
    
   > [!NOTE]
   > 서버가 처음 생성될 때는 지역 복원에 즉시 사용 가능하지 않을 수 있습니다. 필요한 메타데이터를 채우는 데 몇 시간 정도 걸릴 수 있습니다.
   >

5. **백업** 드롭다운을 선택 합니다.
   
   :::image type="content" source="./media/howto-restore-server-portal/5-geo-restore-backup.png" alt-text="가격 책정 계층 - 백업 중복 선택":::

6. 복원할 원본 서버를 선택 합니다.
   
   :::image type="content" source="./media/howto-restore-server-portal/6-select-backup.png" alt-text="가격 책정 계층 - 백업 중복 선택":::

7. 서버는 **vcores**수, **백업 보존 기간**, **백업 중복성 옵션**, **엔진 버전**및 **관리자 자격 증명**에 대 한 값을 기본값으로 표시 합니다. **계속**을 선택합니다. 
   
   :::image type="content" source="./media/howto-restore-server-portal/7-accept-backup.png" alt-text="가격 책정 계층 - 백업 중복 선택":::

8. 나머지 양식은 기본 설정으로 작성합니다. **위치**를 선택할 수 있습니다.

    위치를 선택한 후 **서버 구성** 을 선택 하 여 **계산 생성** (선택한 지역에서 사용 가능한 경우), **Vcores**수, **백업 보존 기간**및 **백업 중복성 옵션**을 업데이트할 수 있습니다. **가격 책정 계층**(기본, 범용 또는 메모리 최적화) 또는 **스토리지** 크기는 복원하는 동안 변경할 수 없습니다.

   :::image type="content" source="./media/howto-restore-server-portal/8-create.png" alt-text="가격 책정 계층 - 백업 중복 선택"::: 

9. **검토 + 만들기**를 선택하여 선택 사항을 검토합니다. 

10. **만들기**를 선택하여 서버를 프로비전합니다. 이 작업은 몇 분 정도 걸릴 수 있습니다.

지역 복원으로 만든 새 서버에는 복원이 시작된 당시의 기존 서버에 유효한 동일한 서버 관리자 로그인 이름 및 암호가 있습니다. 암호는 새 서버의 **개요** 페이지에서 변경할 수 있습니다.

복원 중에 만든 새 서버에는 원래 서버에 존재했던 방화벽 규칙 또는 VNet 서비스 엔드포인트가 없습니다. 이러한 규칙은 새 서버에 대해 개별적으로 설정돼야 합니다.


## <a name="next-steps"></a>다음 단계
- 서비스의 [백업](concepts-backup.md)을 자세히 알아봅니다.
- [비즈니스 연속성](concepts-business-continuity.md) 옵션을 자세히 알아봅니다.