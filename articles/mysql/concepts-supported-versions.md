---
title: Azure Database for MySQL에서 지원되는 버전
description: MySQL용 Azure 데이터베이스에서 지원되는 버전에 대해 설명합니다.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/13/2019
ms.openlocfilehash: e7e81632b2be135fb74d375ab8a11f1b4b3ef39d
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "60525928"
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>지원되는 MySQL용 Azure 데이터베이스 서버 버전

Azure Database for MySQL은 [MySQL Community Edition](https://www.mysql.com/products/community/)에서 InnoDB 엔진을 사용하여 개발되었습니다.

MySQL은 X.Y.Z 명명 체계를 사용합니다. X는 주 버전, Y는 부 버전이며, Z는 버그 수정 릴리스입니다. 스키마에 대한 자세한 내용은 [MySQL 설명서](https://dev.mysql.com/doc/refman/5.7/en/which-version.html)를 참조하세요.

Azure Database for MySQL은 현재 다음 버전을 지원합니다.

## <a name="mysql-version-56"></a>MySQL 버전 5.6

버그 수정 릴리스: 5.6.42

MySQL 5.6.42의 향상된 기능 및 수정에 대한 자세한 내용은 Mysql [릴리스 노트](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-42.html) 를 참조합니다.

## <a name="mysql-version-57"></a>MySQL 버전 5.7

버그 수정 릴리스: 5.7.24

ySQL 5.7.24의 향상된 기능 및 수정 사항에 대한 자세한 내용은 Mysql [릴리스 노트](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-24.html) 를 참조합니다.

> [!NOTE]
> 서비스에서 게이트웨이를 사용하여 새 인스턴스로 연결을 리디렉션합니다. 연결이 설정되면 MySQL 클라이언트는 MySQL Server 인스턴스에서 실행 중인 실제 버전이 아닌 게이트웨이에서 설정된 MySQL 버전을 표시합니다. MySQL Server 인스턴스의 버전을 확인하려면 MySQL 프롬프트에서 `SELECT VERSION();` 명령을 사용합니다.

## <a name="managing-updates-and-upgrades"></a>업데이트 및 업그레이드 관리
서비스는 버그 수정 버전 업데이트에 대한 패치를 자동으로 관리합니다. 예: 5.7.20~5.7.21  

현재 주 및 부 버전 업그레이드는 지원되지 않습니다. 예를 들어 MySQL 5.6에서 MySQL 5.7로의 업그레이드는 지원되지 않습니다. 5.6에서 5.7로 업그레이드하려는 경우 새 엔진 버전을 사용하여 만든 서버에 부 버전을 [덤프 및 복원](./concepts-migrate-dump-restore.md)합니다.

## <a name="next-steps"></a>다음 단계

**서비스 계층**에 따른 특정 리소스 할당량 및 제한 사항에 대한 자세한 내용은 [서비스 계층](./concepts-pricing-tiers.md)을 참조하세요.
