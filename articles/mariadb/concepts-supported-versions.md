---
title: Azure Database for MariaDB에서 지원되는 버전
description: Azure Database for MariaDB에서 지원되는 버전에 대해 설명합니다.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: 950294094584958e83f6a16630a6e1f897785e46
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69897282"
---
# <a name="supported-azure-database-for-mariadb-server-versions"></a>지원되는 Azure Database for MariaDB 서버 버전

Azure Database for MariaDB는 InnoDB 엔진을 사용 하 여 오픈 소스 [Aadb 서버](https://downloads.mariadb.org/)에서 개발 되었습니다. 

MariaDB는 X-y 명명 스키마를 사용 합니다. X는 주 버전이 고 Y는 부 버전이 며 Z는 패치 버전입니다.

> [!NOTE]
> 서비스에서 게이트웨이를 사용하여 새 인스턴스로 연결을 리디렉션합니다. 연결이 설정되면 MySQL 클라이언트는 MariaDB 서버 인스턴스에서 실행 중인 실제 버전이 아닌 게이트웨이에서 설정된 MariaDB 버전을 표시합니다. Mariadb 서버 인스턴스의 버전을 확인 하려면 `SELECT VERSION();` 명령을 사용 합니다.

Azure Database for MariaDB는 현재 다음 버전을 지원합니다.

## <a name="mariadb-version-102"></a>MariaDB 버전 10.2

패치 버전: 10.2.23

해결 방법에 대 한 자세한 내용은 10.2.23 Iadb [설명서](https://mariadb.com/kb/en/library/mariadb-10223-release-notes/) 를 참조 하세요.

## <a name="mariadb-version-103"></a>MariaDB 버전 10.3

패치 버전: 10.3.14

해결 방법에 대 한 자세한 내용은 10.3.14 Iadb [설명서](https://mariadb.com/kb/en/library/mariadb-10314-release-notes/) 를 참조 하세요.

## <a name="managing-updates-and-upgrades"></a>업데이트 및 업그레이드 관리
서비스는 패치 업데이트에 대 한 업그레이드를 자동으로 관리 합니다. 예를 들어 10.2.21를 10.2.23로 변환할 수 있습니다.  

현재 주 및 부 버전 업그레이드는 지원되지 않습니다. 예를 들어, MariaDB 10.2에서 MariaDB 10.3로 업그레이드 하는 것은 지원 되지 않습니다. 10.2에서 10.3로 업그레이드 하려는 경우 덤프를 사용 하 여 새 엔진 버전으로 만든 서버에 [복원](./howto-migrate-dump-restore.md) 합니다.

## <a name="next-steps"></a>다음 단계

- **서비스 계층**에 따른 특정 리소스 할당량 및 제한 사항에 대한 자세한 내용은 [서비스 계층](./concepts-pricing-tiers.md)을 참조하세요.
