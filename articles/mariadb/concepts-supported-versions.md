---
title: 지원되는 버전 - MariaDB용 Azure 데이터베이스
description: MariaDB 서비스에 대한 Azure 데이터베이스에서 지원되는 MariaDB 서버 버전을 알아봅니다.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 361ba17532d27a7020be1b6874993da999f48604
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79527710"
---
# <a name="supported-azure-database-for-mariadb-server-versions"></a>지원되는 Azure Database for MariaDB 서버 버전

MariaDB용 Azure 데이터베이스는 InnoDB 엔진을 사용하여 오픈 소스 [MariaDB 서버에서](https://downloads.mariadb.org/)개발되었습니다.

MariaDB는 X.Y.Z 명명 체계를 사용합니다. X는 주 버전이고 Y는 마이너 버전이고 Z는 패치 버전입니다.

> [!NOTE]
> 서비스에서 게이트웨이를 사용하여 새 인스턴스로 연결을 리디렉션합니다. 연결이 설정되면 MySQL 클라이언트는 MariaDB 서버 인스턴스에서 실행 중인 실제 버전이 아닌 게이트웨이에서 설정된 MariaDB 버전을 표시합니다. MariaDB 서버 인스턴스의 버전을 확인하려면 `SELECT VERSION();` 명령을 사용합니다.

Azure Database for MariaDB는 현재 다음 버전을 지원합니다.

## <a name="mariadb-version-102"></a>마리아DB 버전 10.2

패치 버전: 10.2.25

이 버전의 개선 사항 및 수정 사항에 대해 자세히 알아보려면 [MariaDB 문서를](https://mariadb.com/kb/en/library/mariadb-10225-release-notes/) 참조하십시오.

## <a name="mariadb-version-103"></a>마리아DB 버전 10.3

패치 버전: 10.3.16

이 버전의 개선 사항 및 수정 사항에 대해 자세히 알아보려면 [MariaDB 문서를](https://mariadb.com/kb/en/library/mariadb-10316-release-notes/) 참조하십시오.

## <a name="managing-updates-and-upgrades"></a>업데이트 및 업그레이드 관리
서비스는 패치 업데이트에 대한 업그레이드를 자동으로 관리합니다. 예를 들어 10.2.21 ~ 10.2.23입니다.  

현재 주 및 부 버전 업그레이드는 지원되지 않습니다. 예를 들어 MariaDB 10.2에서 MariaDB 10.3으로의 업그레이드는 지원되지 않습니다. 10.2에서 10.3으로 업그레이드하려면 [덤프를](./howto-migrate-dump-restore.md) 사용하여 새 엔진 버전으로 만든 서버로 복원합니다.

## <a name="next-steps"></a>다음 단계

- **서비스 계층에**따라 특정 리소스 할당량 및 제한 사항에 대한 자세한 내용은 [서비스 계층을](./concepts-pricing-tiers.md)참조하십시오.
