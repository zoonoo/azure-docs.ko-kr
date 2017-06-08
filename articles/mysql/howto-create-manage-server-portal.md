---
title: "Azure Portal을 사용한 MySQL용 Azure Database 서버 만들기 및 관리 | Microsoft Docs"
description: "이 문서에서는 Azure Portal을 사용하여 신속하게 새로운 MySQL용 Azure Database 서버를 만들고 해당 서버를 관리하는 방법을 설명합니다."
services: mysql
author: v-chenyh
ms.author: nolanwu
editor: jasonh
manager: jhubbard
ms.assetid: 
ms.service: mysql-database
ms.tgt_pltfrm: portal
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: cbf1bcd90c53a7221773c7dc7731b37cf762e953
ms.contentlocale: ko-kr
ms.lasthandoff: 05/10/2017

---

# <a name="create-and-manage-azure-database-for-mysql-server-using-azure-portal"></a>Azure Portal을 사용한 MySQL용 Azure Database 서버 만들기 및 관리
이 문서에서는 Azure Portal을 사용하여 신속하게 새로운 MySQL용 Azure Database 서버를 만들고 해당 서버를 관리하는 방법을 설명합니다. 서버 관리에는 서버 세부 정보 및 데이터베이스를 보고, 암호를 다시 설정하고 해당 서버를 삭제하는 것을 포함합니다.

## <a name="log-in-to-the-azure-portal"></a>Azure 포털에 로그인
[Azure 포털](https://portal.azure.com)에 로그인합니다.

## <a name="create-an-azure-database-for-mysql-server"></a>MySQL용 Azure Database 서버 만들기
“mysqlserver4demo”라는 MySQL용 Azure Database 서버를 만들려면 다음과 같은 단계를 수행합니다.

1 - Azure Portal의 왼쪽 위에 있는 **새로 만들기** 단추를 클릭합니다.

2 - 새로 만들기 페이지에서 **데이터베이스**를 선택하고 데이터베이스 페이지에서 **MySQL용 Azure Database**를 선택합니다.

> MySQL용 Azure Database 서버는 일련의 정의된 [계산 및 저장소](./concepts-compute-unit-and-storage.md) 리소스를 사용하여 만들어집니다. 데이터베이스는 Azure 리소스 그룹 및 MySQL용 Azure Database 서버에서 만들어집니다.

![create-new-server](./media/howto-create-manage-server-portal/create-new-server.png)

3 - 다음 정보에 대한 MySQL용 Azure Database 양식을 작성합니다.

| **양식 필드** | **필드 설명** |
|----------------|-----------------------|
| *서버 이름* | azure-mysql(서버 이름은 전역적으로 고유함) |
| *구독* | MySQLaaS(드롭다운 목록에서 선택) |
| *리소스 그룹* | myresource(새 리소스 그룹을 만들거나 기존 그룹 선택) |
| *서버 관리자 로그인* | myadmin(설치 관리자 계정 이름) |
| *암호* | 암호 관리자 계정 암호 |
| *암호 확인* | 관리자 계정 암호 확인 |
| *위치*: | 북유럽(북유럽 및 미국 서부 중 선택) |
| *버전* | 5.6(MySQL용 Azure Database 서버 버전 선택) |

4- **가격 책정 계층**을 클릭하고 새 서버의 서비스 계층 및 성능 수준을 지정합니다. 계산 단위는 기본 계층 50에서 100 및 표준 계층 100에서 200 사이로 구성할 수 있으며, 저장소는 포함된 금액에 따라 추가할 수 있습니다. 이 방법 가이드에서는 계산 단위 50 및 50GB를 선택합니다. **확인**을 클릭하여 선택 내용을 저장합니다.
![create-server-pricing-tier](./media/howto-create-manage-server-portal/create-server-pricing-tier.png)

5- **만들기**를 클릭하여 서버를 프로비전합니다. 프로비전하는 데 몇 분이 걸립니다.

> 배포를 쉽게 추적할 수 있는 **대시보드에 고정** 옵션을 확인합니다.
> [!NOTE]
> 공개 미리 보기를 위해 기본 계층에서 최대 1000GB 및 표준 계층에서 10000GB 저장소가 지원되며 최대 저장소는 일시적으로 여전히 1000GB로 제한됩니다. 
</Include>

## <a name="update-an-azure-database-for-mysql-server"></a>MySQL용 Azure Database 서버 업데이트
새 서버가 프로비전되면 사용자는 사용자 암호를 다시 설정하거나 계산 단위를 변경하여 서버를 강화/규모 축소하는 등의 기존 서버를 편집할 수 있는 2가지 옵션을 사용할 수 있습니다.

### <a name="change-the-administrator-user-password"></a>관리자 암호 변경
1- 서버 **개요** 블레이드에서 **암호 재설정**을 클릭하여 암호 입력 및 확인 창을 채웁니다.

2- 새 암호를 입력하고 아래와 같이 창에서 암호를 확인합니다. ![reset-password](./media/howto-create-manage-server-portal/reset-password.png)

3- **확인**을 클릭하여 새 암호를 저장합니다.

### <a name="scale-updown-by-changing-compute-units"></a>계산 단위를 변경하여 강화/규모 축소

1- 서버 블레이드의 **설정**에서 **가격 책정 계층**을 클릭하여 MySQL용 Azure Database 서버에 대한 가격 책정 계층 블레이드를 엽니다.

2- **MySQL용 Azure Database 서버 만들기**의 4단계에 따라 동일한 가격 책정 계층의 계산 단위를 변경합니다.

## <a name="delete-an-azure-database-for-mysql-server"></a>MySQL용 Azure Database 서버 삭제

1- 서버 **개요** 블레이드에서 **삭제** 명령 단추를 클릭하여 삭제 확인 블레이드를 엽니다.

2- 이중 확인을 위해 블레이드의 입력 상자에 올바른 서버 이름을 입력합니다.

3- **삭제** 단추를 다시 클릭하여 삭제 작업을 확인하고 알림 표시줄에 “삭제 성공” 메시지가 표시되길 기다립니다.

## <a name="list-the-azure-database-for-mysql-databases"></a>MySQL용 Azure Database 데이터베이스 나열
서버 **개요** 블레이드에서 맨 아래에 데이터베이스 타일이 표시될 때까지 아래로 스크롤합니다. 모든 데이터베이스가 테이블에 나열됩니다. **삭제** 명령 단추를 클릭하여 삭제 확인 블레이드를 엽니다.

![show-databases](./media/howto-create-manage-server-portal/show-databases.png)

## <a name="show-details-of-an-azure-database-for-mysql-server"></a>MySQL용 Azure Database 서버의 세부 정보 보기
서버 블레이드의 **설정**에서 **속성**을 클릭하면 **속성** 블레이드가 열립니다. 그러면 서버에 대한 자세한 모든 정보가 표시됩니다.

## <a name="next-steps"></a>다음 단계

[빠른 시작: Azure Portal을 사용한 MySQL용 Azure Database 서버 만들기 및 관리](./quickstart-create-mysql-server-database-using-azure-portal.md)

