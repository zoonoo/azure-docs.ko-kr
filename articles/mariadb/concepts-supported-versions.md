---
title: 지원 버전 - Azure Database for MariaDB
description: Azure Database for MariaDB 서비스에서 지원되는 버전의 MariaDB 서버에 대해 알아봅니다.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 7/20/2020
ms.openlocfilehash: dbf32626714200e6712c67b701ebc597c4a7ba7e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98662443"
---
# <a name="supported-azure-database-for-mariadb-server-versions"></a>지원되는 Azure Database for MariaDB 서버 버전

Azure Database for MariaDB는 InnoDB 엔진을 사용하여 오픈 소스 [MariaDB 서버](https://downloads.mariadb.org/)에서 개발되었습니다.

MariaDB는 X.Y.Z 명명 스키마를 사용합니다. X는 주 버전, Y는 부 버전이며, Z는 패치 버전입니다.

> [!NOTE]
> 서비스에서 게이트웨이를 사용하여 새 인스턴스로 연결을 리디렉션합니다. 연결이 설정되면 MySQL 클라이언트는 MariaDB 서버 인스턴스에서 실행 중인 실제 버전이 아닌 게이트웨이에서 설정된 MariaDB 버전을 표시합니다. MariaDB 서버 인스턴스 버전을 확인하려면 `SELECT VERSION();` 명령을 사용합니다.

Azure Database for MariaDB는 현재 다음 버전을 지원합니다.

## <a name="mariadb-version-102"></a>MariaDB 버전 10.2

패치 버전: 10.2.32

이 버전의 개선 및 수정 사항에 대해 알아보려면 [MariaDB 설명서](https://mariadb.com/kb/en/mariadb-10232-release-notes/)를 참조하세요.

## <a name="mariadb-version-103"></a>MariaDB 버전 10.3

패치 버전: 10.3.23

이 버전의 개선 및 수정 사항에 대해 알아보려면 [MariaDB 설명서](https://mariadb.com/kb/en/mariadb-10323-release-notes/)를 참조하세요.

## <a name="managing-updates-and-upgrades"></a>업데이트 및 업그레이드 관리
서비스는 패치 업데이트에 대한 업그레이드를 자동으로 관리합니다. 예: 10.2.21 to 10.2.23.  

현재 주 및 부 버전 업그레이드는 지원되지 않습니다. 예를 들어 MariaDB 10.2에서 MariaDB 10.3으로의 업그레이드는 지원되지 않습니다. 10.2에서 10.3으로 업그레이드하려는 경우 새 엔진 버전을 사용하여 만든 서버에 [덤프를 생성하고 복원](./howto-migrate-dump-restore.md)하세요.

## <a name="next-steps"></a>다음 단계

- **서비스 계층** 에 따른 특정 리소스 할당량 및 제한 사항에 대한 자세한 내용은 [서비스 계층](./concepts-pricing-tiers.md)을 참조하세요.
