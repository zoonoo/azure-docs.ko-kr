---
title: "Azure에서 MySQL 데이터베이스 만들기 및 연결"
description: "Azure 포털을 사용하여 MySQL 데이터베이스를 만든 후 Azure의 PHP 웹앱에서 연결하는 방법을 알아봅니다."
documentationcenter: php
services: app-service\web
author: cephalin
manager: erikre
editor: 
tags: mysql
ms.assetid: 55465a9a-7e65-4fd9-8a65-dd83ee41f3e5
ms.service: multiple
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 04/25/2017
ms.author: robmcm;cephalin
ms.openlocfilehash: c072cb3a7d376d1e3c2b9f741f5410106e701256
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-connect-to-a-mysql-database-in-azure"></a>Azure에서 MySQL 데이터베이스 만들기 및 연결
이 자습서에서는 [Azure Portal](https://portal.azure.com)(공급자가 [ClearDB](http://www.cleardb.com/))에서 MySQL 데이터베이스를 만들고 [Azure App Service](app-service/app-service-web-overview.md)에서 실행 중인 PHP 웹앱에서 연결하는 방법을 보여 줍니다.

> [!NOTE]
> <a href="https://portal.azure.com/#create/WordPress.WordPress" target="_blank">마켓플레이스 앱 템플릿</a>의 일부로 MySQL 데이터베이스를 만들 수도 있습니다.
>
>

## <a name="create-a-mysql-database-in-azure-portal"></a>Azure 포털에서 MySQL 데이터베이스 만들기
Azure 포털에서 MySQL 데이터베이스를 만들려면 다음을 수행합니다.

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
2. 왼쪽 메뉴에서 **새로 만들기** > **데이터 + 저장소** > **MySQL 데이터베이스**를 클릭합니다.

    ![Azure에서 MySQL 데이터베이스 만들기 - 시작](./media/store-php-create-mysql-database/create-db-1-start.png)
3. 새 MySQL 데이터베이스 [블레이드](azure-portal-overview.md)에서 다음과 같이 새 MySQL 데이터베이스를 구성합니다(*블레이드*: 가로로 열리는 포털 페이지).

   * **데이터베이스 이름**: 고유하게 식별 가능한 이름을 입력합니다.
   * **구독**: 사용할 구독을 선택합니다.
   * **데이터베이스 유형**: 저비용 또는 무료 계층의 경우 **공유**를, 전용 리소스를 가져오려면 **전용**을 선택합니다.
   * **리소스 그룹**: 기존 [리소스 그룹](azure-resource-manager/resource-group-overview.md) 에 MySQL 데이터베이스를 추가하거나 새 그룹에 넣습니다. 동일한 그룹의 리소스는 쉽게 함께 관리할 수 있습니다.
   * **위치**: 가까운 위치를 선택합니다. 기존 리소스 그룹에 추가할 때 리소스 그룹의 위치로 고정됩니다.
   * **가격 책정 계층**: **가격 책정 계층**을 클릭한 후 가격 책정 옵션(**Mercury** 계층은 무료)을 선택한 후 **선택**을 클릭합니다.
   * **약관**: **약관**을 클릭하고 구매 정보를 검토한 후 **구매**를 클릭합니다.
   * **대시보드에 고정**: 대시보드에서 직접 액세스하려는 경우 선택합니다. 포털 탐색에 아직 익숙하지 않은 경우 특히 유용합니다.

     다음 스크린 샷은 MySQL 데이터베이스를 구성하는 방법을 예로 든 것 뿐입니다.  
     ![Azure에서 MySQL 데이터베이스 만들기 - 구성](./media/store-php-create-mysql-database/create-db-2-configure.png)
4. 구성을 완료하면 **만들기**를 클릭합니다.

    ![Azure에서 MySQL 데이터베이스 만들기 - 만들기](./media/store-php-create-mysql-database/create-db-3-create.png)

    배포가 시작되었음을 알려주는 팝업 알림이 표시됩니다.

    ![Azure에서 MySQL 데이터베이스 만들기 - 진행 중](./media/store-php-create-mysql-database/create-db-4-started-status.png)

    배포에 성공하면 다른 팝업을 받게 됩니다. 포털에서 MySQL 데이터베이스 블레이드가 자동으로 열립니다.

<a name="connect"></a>

## <a name="connect-to-your-mysql-database"></a>MySQL 데이터베이스 연결
새 MySQL 데이터베이스에 대한 연결 정보를 보려면 웹앱의 블레이드에서 **속성** 을 클릭하면 됩니다.

![Azure에서 MySQL 데이터베이스 만들기- MySQL 데이터베이스 블레이드](./media/store-php-create-mysql-database/create-db-5-finished-db-blade.png)

이제 모든 웹앱에서 연결 정보를 사용할 수 있습니다. 간단한 PHP 앱에서 연결 정보를 사용하는 방법을 보여 주는 샘플은 [여기](https://github.com/WindowsAzure/azure-sdk-for-php-samples/tree/master/tasklist-mysql)에서 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계
자세한 내용은 [PHP 개발자 센터](/develop/php/)를 참조하세요.
