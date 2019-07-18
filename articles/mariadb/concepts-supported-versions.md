---
title: Azure Database for MariaDB에서 지원되는 버전
description: Azure Database for MariaDB에서 지원되는 버전에 대해 설명합니다.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: 760cb39ea1f3980faba348c7aa1de68a66b20a8d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67065716"
---
# <a name="supported-azure-database-for-mariadb-server-versions"></a>지원되는 Azure Database for MariaDB 서버 버전

Azure Database for MariaDB는 InnoDB 엔진을 사용하여 오픈 소스 [MariaDB 서버](https://downloads.mariadb.org/)에서 개발되었습니다. Azure Database for MariaDB는 현재 다음 버전을 지원합니다.

## <a name="mariadb-version-102"></a>MariaDB 버전 10.2

참조를 [MariaDB 설명서](https://mariadb.com/kb/en/library/mariadb-10223-release-notes/) 개선 사항 및 MariaDB 10.2.23에서에서 수정 하는 방법에 대 한 자세한 내용을 보려면.

## <a name="mariadb-version-103"></a>MariaDB 버전 10.3

참조를 [MariaDB 설명서](https://mariadb.com/kb/en/library/mariadb-10314-release-notes/) 개선 사항 및 MariaDB 10.3.14에서에서 수정 하는 방법에 대 한 자세한 내용을 보려면.

> [!NOTE]
> 서비스에서 게이트웨이를 사용하여 새 인스턴스로 연결을 리디렉션합니다. 연결이 설정되면 MySQL 클라이언트는 MariaDB 서버 인스턴스에서 실행 중인 실제 버전이 아닌 게이트웨이에서 설정된 MariaDB 버전을 표시합니다. MariaDB 서버 인스턴스의 버전을 확인하려면 MySQL 프롬프트에서 `SELECT VERSION();` 명령을 사용합니다.

## <a name="managing-updates-and-upgrades"></a>업데이트 및 업그레이드 관리

서비스는 부 버전 업데이트에 대한 패치를 자동으로 관리합니다.

## <a name="next-steps"></a>다음 단계

- **서비스 계층**에 따른 특정 리소스 할당량 및 제한 사항에 대한 자세한 내용은 [서비스 계층](./concepts-pricing-tiers.md)을 참조하세요.
