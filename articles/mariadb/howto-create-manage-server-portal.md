---
title: Azure Portal를 사용 하 여 Azure Database for MariaDB 서버 만들기 및 관리
description: 이 문서에서는 Azure Portal을 사용 하 여 새 Azure Database for MariaDB 서버를 신속 하 게 만들고 서버를 관리할 수 있는 방법을 설명 합니다.
author: ambhatna
ms.author: ambhatna
ms.service: mariadb
ms.topic: conceptual
ms.date: 08/09/2019
ms.openlocfilehash: 5aae3eb0582956ccb45cc41d8400f489f1077bad
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/29/2019
ms.locfileid: "70143441"
---
# <a name="create-and-manage-azure-database-for-mariadb-server-using-azure-portal"></a>Azure Portal를 사용 하 여 Azure Database for MariaDB 서버 만들기 및 관리
이 항목에서는 새 Azure Database for MariaDB 서버를 신속 하 게 만들 수 있는 방법에 대해 설명 합니다. 또한 Azure Portal을 사용하여 서버를 관리하는 방법에 대한 정보를 포함합니다. 서버 관리에는 서버 세부 정보 및 데이터베이스를 보고, 암호를 다시 설정하고, 리소스 크기를 조정하고, 서버를 삭제하는 것이 포함됩니다.

## <a name="log-in-to-the-azure-portal"></a>Azure Portal에 로그인
[Azure Portal](https://portal.azure.com)에 로그인합니다.

## <a name="create-an-azure-database-for-mariadb-server"></a>Azure Database for MariaDB 서버 만들기
"Mydemoserver" 라는 Azure Database for MariaDB 서버를 만들려면 다음 단계를 수행 합니다.

1. Azure Portal의 왼쪽 위 모서리에 있는 **리소스 만들기** 단추를 클릭합니다.

2. 새로 만들기 페이지에서 **데이터베이스**를 선택 하 고 데이터베이스 페이지에서 **Azure Database for MariaDB**를 선택 합니다.

    > 정의 된 [계산 및 저장소](./concepts-pricing-tiers.md) 리소스 집합을 사용 하 여 Azure Database for MariaDB 서버가 만들어집니다. 데이터베이스는 Azure 리소스 그룹 및 Azure Database for MariaDB 서버 내에서 만들어집니다.

   ![create-new-server](./media/howto-create-manage-server-portal/create-new-server.png)

3. 다음 정보를 사용 하 여 Azure Database for MariaDB 양식을 작성 합니다.

    | **양식 필드** | **필드 설명** |
    |----------------|-----------------------|
    | *서버 이름* | mydemoserver(서버 이름은 전역적으로 고유함) |
    | *구독* | mysubscription(드롭다운 메뉴에서 선택) |
    | *리소스 그룹* | myresourcegroup(새 리소스 그룹을 만들거나 기존 그룹 선택) |
    | *원본 선택* | Blank (빈 MariaDB 서버 만들기) |
    | *서버 관리자 로그인* | myadmin(관리자 계정 이름을 설정함) |
    | *암호* | 관리자 계정 암호 설정 |
    | *암호 확인* | 관리자 계정 암호를 확인합니다. |
    | *위치* | 동남 아시아(북유럽과 미국 서부 사이에서 선택) |
    | *Version* | 10.3 (Azure Database for MariaDB 서버 버전 선택) |

   ![create-new-server](./media/howto-create-manage-server-portal/form-field.png)

4. **서버 구성** 을 클릭 하 여 새 서버에 대 한 서비스 계층 및 성능 수준을 지정 합니다. **범용** 탭을 선택합니다. *5세대*, *vCore 4개*, *100GB* 및 *7일*은 **세대 컴퓨팅**, **vCore**, **스토리지** 및 **백업 보존 기간**에 대한 기본 값입니다. 이러한 슬라이더는 그대로 둘 수 있습니다. 지역 중복 스토리지에서 서버 백업을 사용하도록 설정하려면 **백업 중복 옵션**에서 **지역 중복**을 선택합니다.

   ![create-server-pricing-tier](./media/howto-create-manage-server-portal/create-server-pricing-tier.png)

5. **검토 + 만들기** 를 클릭 하 여 검토 화면으로 이동 하 고 모든 세부 정보를 확인 합니다. **만들기**를 클릭하여 서버를 프로비전합니다. 프로비전하는 데 몇 분이 걸립니다.

    > 배포를 쉽게 추적할 수 있도록 **대시보드에 고정** 옵션을 선택합니다.

## <a name="update-an-azure-database-for-mariadb-server"></a>Azure Database for MariaDB 서버 업데이트
새 서버가 프로 비전 되 면 사용자는 관리자 암호 재설정, 가격 책정 계층 변경, vCore 또는 storage를 변경 하 여 서버 확장 또는 축소 등 기존 서버를 구성 하기 위한 몇 가지 옵션을 사용할 수 있습니다.

### <a name="change-the-administrator-user-password"></a>관리자 암호 변경
1. 서버 **개요**에서 **암호 다시 설정**을 클릭하여 암호 재설정 창을 표시합니다.

   ![개요](./media/howto-create-manage-server-portal/overview.png)

2. 아래와 같이 창에 새 암호를 입력하고 암호를 확인합니다.

   ![reset-password](./media/howto-create-manage-server-portal/reset-password.png)

3. **확인**을 클릭하여 새 암호를 저장합니다.

### <a name="change-the-pricing-tier"></a>가격 책정 계층 변경
> [!NOTE]
> 크기 조정은 범용에서 메모리 액세스에 최적화 된 서비스 계층으로만 지원 되며 그 반대의 경우도 마찬가지입니다. 서버를 만든 후에 기본 가격 책정 계층으로 변경 하는 것은 Azure Database for MariaDB에서 지원 되지 않습니다.
> 
1. **설정** 아래에 있는 **가격 책정 계층**을 클릭합니다.
2. 변경 하려는 **가격 책정 계층** 을 선택 합니다.

    ![변경-가격 책정 계층](./media/howto-create-manage-server-portal/change-pricing-tier.png)

4. **확인**을 클릭하여 변경 내용을 저장합니다. 

### <a name="scale-vcores-updown"></a>vCore 규모 확장/축소

1. **설정** 아래에 있는 **가격 책정 계층**을 클릭합니다.

2. 슬라이더를 원하는 값으로 이동하여 **vCore** 설정을 변경합니다.

    ![scale-compute](./media/howto-create-manage-server-portal/scale-compute.png)

3. **확인**을 클릭하여 변경 내용을 저장합니다.

### <a name="scale-storage-up"></a>Storage 확장

1. **설정** 아래에 있는 **가격 책정 계층**을 클릭합니다.

2. 슬라이더를 원하는 값으로 이동하여 **Storage** 설정을 변경합니다.

    ![scale-storage](./media/howto-create-manage-server-portal/scale-storage.png)

3. **확인**을 클릭하여 변경 내용을 저장합니다.

## <a name="delete-an-azure-database-for-mariadb-server"></a>Azure Database for MariaDB 서버 삭제

1. 서버 **개요**에서 **삭제** 단추를 클릭하여 삭제 확인 프롬프트를 엽니다.

    ![삭제](./media/howto-create-manage-server-portal/delete.png)

2. 이중 확인을 위해 입력 상자에 서버 이름을 입력합니다.

    ![confirm-delete](./media/howto-create-manage-server-portal/confirm.png)

3. **삭제** 단추를 클릭하여 서버 삭제를 확인합니다. 알림 표시줄에 "완료 된 Aadb 서버를 삭제 했습니다." 팝업 창이 표시 될 때까지 기다립니다.

## <a name="list-the-azure-database-for-mariadb-databases"></a>Azure Database for MariaDB 데이터베이스 나열
서버 **개요**에서 맨 아래 데이터베이스 타일이 보일 때까지 아래로 스크롤합니다. 서버의 모든 데이터베이스가 테이블에 나열되어 있습니다.

   ![show-databases](./media/howto-create-manage-server-portal/show-databases.png)

## <a name="show-details-of-an-azure-database-for-mariadb-server"></a>Azure Database for MariaDB 서버에 대 한 세부 정보 표시
서버에 대한 세부 정보를 보려면 **설정** 아래 있는 **속성**을 클릭합니다.

![속성](./media/howto-create-manage-server-portal/properties.png)

## <a name="next-steps"></a>다음 단계

[빠른 시작: Azure Portal을 사용한 Azure Database for MariaDB 서버 만들기](./quickstart-create-mariadb-server-database-using-azure-portal.md)