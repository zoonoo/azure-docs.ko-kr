---
title: 주 버전 업그레이드-Azure Portal-Azure Database for MySQL-단일 서버
description: 이 문서에서는를 사용 하 여 Azure Database for MySQL 단일 서버에 대 한 주 버전을 업그레이드할 수 있는 방법에 대해 설명 Azure Portal
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 11/16/2020
ms.openlocfilehash: 78c35e42cefa8897d9f93c3a941b4c0e8b81e5f9
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94686956"
---
# <a name="major-version-upgrade-in-azure-database-for-mysql-single-server-using-the-azure-portal"></a>Azure Portal를 사용 하 Azure Database for MySQL 단일 서버에서 주 버전 업그레이드

> [!IMPORTANT]
> Azure database for MySQL 단일 서버에 대 한 주 버전 업그레이드는 공개 미리 보기 상태입니다.

이 문서에서는 Azure Database for MySQL 단일 서버에서 MySQL 주 버전을 현재 위치로 업그레이드 하는 방법을 설명 합니다.

이 기능을 통해 고객은 데이터 이동 이나 응용 프로그램 연결 문자열 변경 없이 단추를 클릭 하 여 MySQL 5.6 서버의 전체 업그레이드를 MySQL 5.7로 수행할 수 있습니다.

> [!Note]
> * 주 버전 업그레이드는 MySQL 5.6에서 MySQL 5.7으로 주 버전 업그레이드 하는 경우에만 사용할 수 있습니다.<br>
> * 주 버전 업그레이드는 아직 복제 서버에서 지원 되지 않습니다.

## <a name="prerequisites"></a>사전 요구 사항
이 방법 가이드를 완료하려면 다음이 필요합니다.
- [단일 서버 Azure Database for MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="perform-major-version-upgrade-from-mysql-56-to-mysql-57"></a>MySQL 5.6에서 MySQL 5.7로 주 버전 업그레이드 수행

MySQL 5.6 서버의 Azure 데이터베이스에 대 한 주 버전 업그레이드를 수행 하려면 다음 단계를 따르세요.

1. [Azure Portal](https://portal.azure.com/)에서 기존 Azure Database for MySQL 5.6 서버를 선택 합니다.

2. **개요** 페이지의 도구 모음에서 **업그레이드** 단추를 클릭 합니다.

3. **업그레이드** 섹션에서 **확인** 을 선택 하 여 Azure database for MySQL 5.6 서버를 5.7 서버로 업그레이드 합니다.

    :::image type="content" source="./media/how-to-major-version-upgrade-portal/upgrade.png" alt-text="Azure Database for MySQL-개요-업그레이드":::

4. 알림이 성공적으로 업그레이드 되었는지 확인 합니다.

## <a name="next-steps"></a>다음 단계

[Azure Database for MySQL 버전 관리 정책](concepts-version-policy.md)에 대해 알아봅니다.