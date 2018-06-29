---
title: AzureStack에 MySQL 어댑터 RP에서 제공 하는 데이터베이스를 사용 하 여 | Microsoft Docs
description: 만들고 어댑터 MySQL 리소스 공급자를 사용 하 여 사용자를 프로 비전 하는 MySQL 데이터베이스를 관리 하는 방법
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/26/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 0a900d75315fd0015633c036877faef84c48d65b
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37031833"
---
# <a name="create-mysql-databases"></a>MySQL 데이터베이스 만들기

만들 수 있으며 사용자 포털에서 데이터베이스 셀프 서비스 관리. Azure 스택 사용자 구독 MySQL 데이터베이스 서비스를 포함 하는 제품에 필요 합니다.

## <a name="test-your-deployment-by-creating-a-mysql-database"></a>MySQL 데이터베이스를 만들고 배포를 테스트 합니다.

1. Azure 스택 사용자 포털에 로그인 합니다.
2. 선택 **새 +** > **데이터 + 저장소** > **MySQL 데이터베이스** > **추가**합니다.
3. 아래 **MySQL 데이터베이스 만들기**데이터베이스 이름을 입력 하 고 사용자 환경의 필요에 따라 다른 설정을 구성 합니다.

    ![테스트 MySQL 데이터베이스 만들기](./media/azure-stack-mysql-rp-deploy/mysql-create-db.png)

4. 아래 **Create Database**선택, **SKU**합니다. 아래 **MySQL SKU 선택**, 데이터베이스에 대 한 SKU를 선택 합니다.

    ![MySQL SKU를 선택 합니다.](./media/azure-stack-mysql-rp-deploy/mysql-select-a-sku.png)

    >[!Note]
    >호스팅 서버는 Azure 스택에 추가 되 면 SKU는 할당 하는 있습니다. SKU의 서버를 호스팅하는 풀에서 데이터베이스가 만들어집니다.

5. 아래 **로그인**선택, ***필요한 설정 구성***합니다.
6. 아래 **로그인을 선택**를 선택 하거나 기존 로그인을 선택할 수 있습니다 **새 로그인을 만듭니다 +** 새 로그인을 설정 하 합니다.  입력 한 **데이터베이스 로그인** 이름 및 **암호**를 선택한 후 **확인**합니다.

    ![새 데이터베이스 로그인 생성](./media/azure-stack-mysql-rp-deploy/create-new-login.png)

    >[!NOTE]
    >데이터베이스 로그인 이름의 길이 MySQL 5.7 32 자를 초과할 수 없습니다. 이전 버전에서 16 자를 초과할 수는 없습니다.

7. 선택 **만들기** 데이터베이스 설치를 마칩니다.

데이터베이스를 배포한 후 기록해는 **연결 문자열** 아래 **Essentials**합니다. MySQL 데이터베이스에 액세스 하는 모든 응용 프로그램에서이 문자열을 사용할 수 있습니다.

![MySQL 데이터베이스에 대 한 연결 문자열을 가져옵니다.](./media/azure-stack-mysql-rp-deploy/mysql-db-created.png)

## <a name="update-the-administrative-password"></a>관리자 암호를 업데이트 합니다.

MySQL 서버 인스턴스에서 변경 하 여 암호를 수정할 수 있습니다.

1. 선택 **관리 리소스** > **MySQL 호스팅 서버**합니다. 호스팅 서버를 선택 합니다.
2. 아래 **설정**선택, **암호**합니다.
3. 아래 **암호**, 새 암호를 입력 한 다음 선택 **저장**합니다.

![관리자 암호를 업데이트 합니다.](./media/azure-stack-mysql-rp-deploy/mysql-update-password.png)

## <a name="next-steps"></a>다음 단계

[MySQL 리소스 공급자를 유지 관리](azure-stack-mysql-resource-provider-maintain.md)