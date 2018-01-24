---
title: "Azure Database for MySQL로 최소 가동 중지 시간 마이그레이션 | Microsoft Docs"
description: "이 문서에서는 가동 중지 시간을 최소화하고 MySQL 데이터베이스를 Azure Database for MySQL로 마이그레이션하는 방법과 Attunity Replicate for Microsoft Migrations를 사용하여 초기 로드 및 원본 데이터베이스에서 대상 데이터베이스로의 지속적인 데이터 동기화를 설정하는 방법을 설명합니다."
services: mysql
author: HJToland3
ms.author: jtoland
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 01/04/2018
ms.openlocfilehash: 429f5c9f939a802184a6513a63fb9115abf4b235
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/03/2018
---
# <a name="minimal-downtime-migration-to-azure-database-for-mysql"></a>Azure Database for MySQL로 최소 가동 중지 시간 마이그레이션
Attunity와 Microsoft에서 공동으로 후원하며 Microsoft 고객에게 추가 비용 없이 Azure Database Migration Service와 함께 제공되는 제품인 Attunity Replicate for Microsoft Migrations를 사용하여 기존 MySQL 데이터베이스를 Azure Database for MySQL로 마이그레이션할 수 있습니다. 또한 Attunity Replicate for Microsoft Migrations를 사용하면 데이터베이스 마이그레이션 시 가동 중지 시간이 최소화되며, 원본 데이터베이스가 마이그레이션 프로세스 중에 계속 작동합니다.

Attunity Replicate는 다양한 원본과 대상 간에 데이터를 동기화하여 스키마 생성 스크립트 및 각 데이터베이스 테이블과 연결된 데이터를 전파할 수 있게 해주는 데이터 복제 도구입니다. Attunity Replicate는 다른 아티팩트(예: SP, 트리거, 함수 등)를 전파하거나 이러한 아티팩트에 호스트되는 PL/SQL 코드를 T-SQL로 변환하지 않습니다.

> [!NOTE]
> Attunity Replicate는 광범위한 마이그레이션 시나리오를 지원하지만, Attunity Replicate for Microsoft Migrations는 원본/대상 쌍의 특정 하위 집합에 대한 지원에 초점을 두고 있습니다.

최소 가동 중지 시간 마이그레이션을 수행하기 위한 대략적인 프로세스는 다음과 같습니다.

1. [MySQL Workbench](https://www.mysql.com/products/workbench/)를 사용하여 Azure Database for MySQL 데이터베이스로 **MySQL 원본 스키마를 마이그레이션**합니다.

2. Attunity Replicate for Microsoft Migrations를 사용하여 **초기 로드 및 원본 데이터베이스에서 대상 데이터베이스로의 지속적 데이터 동기화를 설정**합니다. 이렇게 하면 Azure에서 응용 프로그램을 대상 MySQL 데이터베이스로 전환하기 위한 준비를 할 때 원본 데이터베이스를 읽기 전용으로 설정해야 하는 시간이 최소화됩니다.

Attunity Replicate for Microsoft Migrations에 대한 자세한 내용은 다음 리소스를 참조하세요.
 - Attunity Replicate for Microsoft Migrations [웹 페이지](https://aka.ms/attunity-replicate)
 - Attunity Replicate for Microsoft Migrations [다운로드](http://discover.attunity.com/download-replicate-microsoft-lp6657.html)
 - 빠른 시작 가이드, 자습서 및 지원이 포함된 Attunity Replicate [커뮤니티](https://microsoft.attunity.com/)
 - Attunity를 사용하여 MySQL에서 Azure Database for MySQL로 마이그레이션하는 방법에 대한 단계별 지침은 [Database Migration Guide](https://datamigration.microsoft.com/scenario/mysql-to-azuremysql)(데이터베이스 마이그레이션 가이드)를 참조하세요.