---
title: 지원 되는 버전-Azure Database for MariaDB
description: Azure Database for MariaDB service에서 지원 되는 모든 버전의 Aadb 서버에 대해 알아봅니다.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/12/2019
ms.openlocfilehash: 950f8600dac6fccfa72f5dea6e900a210572ee38
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973529"
---
# <a name="supported-azure-database-for-mariadb-server-versions"></a>지원되는 Azure Database for MariaDB 서버 버전

Azure Database for MariaDB는 InnoDB 엔진을 사용 하 여 오픈 소스 [Aadb 서버](https://downloads.mariadb.org/)에서 개발 되었습니다.

MariaDB는 X-y 명명 스키마를 사용 합니다. X는 주 버전이 고 Y는 부 버전이 며 Z는 패치 버전입니다.

> [!NOTE]
> 서비스에서 게이트웨이를 사용하여 새 인스턴스로 연결을 리디렉션합니다. 연결이 설정되면 MySQL 클라이언트는 MariaDB 서버 인스턴스에서 실행 중인 실제 버전이 아닌 게이트웨이에서 설정된 MariaDB 버전을 표시합니다. MariaDB 서버 인스턴스의 버전을 확인 하려면 `SELECT VERSION();` 명령을 사용 합니다.

Azure Database for MariaDB는 현재 다음 버전을 지원합니다.

## <a name="mariadb-version-102"></a>MariaDB 버전 10.2

패치 버전: 10.2.25

이 버전의 향상 된 기능 및 수정 내용에 대 한 자세한 내용은 [Aadb 설명서](https://mariadb.com/kb/en/library/mariadb-10225-release-notes/) 를 참조 하세요.

## <a name="mariadb-version-103"></a>MariaDB 버전 10.3

패치 버전: 10.3.16

이 버전의 향상 된 기능 및 수정 내용에 대 한 자세한 내용은 [Aadb 설명서](https://mariadb.com/kb/en/library/mariadb-10316-release-notes/) 를 참조 하세요.

## <a name="managing-updates-and-upgrades"></a>업데이트 및 업그레이드 관리
서비스는 패치 업데이트에 대 한 업그레이드를 자동으로 관리 합니다. 예를 들어 10.2.21를 10.2.23로 변환할 수 있습니다.  

현재 주 및 부 버전 업그레이드는 지원되지 않습니다. 예를 들어 MariaDB 10.2에서 MariaDB 10.3으로의 업그레이드는 지원되지 않습니다. 10.2에서 10.3로 업그레이드 하려는 경우 덤프를 사용 하 여 새 엔진 버전으로 만든 서버에 [복원](./howto-migrate-dump-restore.md) 합니다.

## <a name="next-steps"></a>다음 단계

- **서비스 계층**에 따른 특정 리소스 할당량 및 제한 사항에 대한 자세한 내용은 [서비스 계층](./concepts-pricing-tiers.md)을 참조하세요.
