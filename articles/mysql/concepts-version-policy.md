---
title: 버전 지원 정책-Azure Database for MySQL-단일 서버 및 유연한 서버 (미리 보기)
description: Azure Database for MySQL의 MySQL 주 버전 및 부 버전에 대 한 정책을 설명 합니다.
author: sr-msft
ms.author: srranga
ms.service: mysql
ms.topic: conceptual
ms.date: 11/03/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 8ad79f2f27864b4fbc78b7c104828230ff7f93bc
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/15/2021
ms.locfileid: "103465660"
---
# <a name="azure-database-for-mysql-version-support-policy"></a>Azure Database for MySQL 버전 지원 정책

이 페이지에서는 Azure Database for MySQL 버전 관리 정책에 대해 설명 하 고 Azure Database for MySQL 단일 서버 및 Azure Database for MySQL 유연한 서버 (미리 보기) 배포 모드에 적용 됩니다.

## <a name="supported--mysql-versions"></a>지원 되는 MySQL 버전

Azure Database for MySQL는 InnoDB 저장소 엔진을 사용 하 여 [MySQL Community Edition](https://www.mysql.com/products/community/)에서 개발 되었습니다. 이 서비스는 커뮤니티에서 지원 되는 현재 주 버전 (MySQL 5.6, 5.7 및 8.0)을 모두 지원 합니다. MySQL은 X-y 명명 스키마를 사용 합니다. 여기서 X는 주 버전이 고 Y는 부 버전이 며 Z는 버그 수정 릴리스입니다. 스키마에 대한 자세한 내용은 [MySQL 설명서](https://dev.mysql.com/doc/refman/5.7/en/which-version.html)를 참조하세요.

> [!NOTE]
> 단일 서버 배포 옵션에서 게이트웨이는 서버 인스턴스에 대 한 연결을 리디렉션하는 데 사용 됩니다. 연결이 설정되면 MySQL 클라이언트는 MySQL Server 인스턴스에서 실행 중인 실제 버전이 아닌 게이트웨이에서 설정된 MySQL 버전을 표시합니다. MySQL Server 인스턴스의 버전을 확인하려면 MySQL 프롬프트에서 `SELECT VERSION();` 명령을 사용합니다.

현재 Azure Database for MySQL는 다음과 같은 주 버전 및 부 버전의 MySQL을 지원 합니다.

| Version | 단일 서버 <br/> 현재 부 버전 |유연한 서버(미리 보기) <br/> 현재 부 버전  |
|:-------------------|:-------------------------------------------|:---------------------------------------------|
|MySQL 버전 5.6 |  [5.6.47](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-47.html)(사용 되지 않음) | 지원되지 않음|
|MySQL 버전 5.7 | [5.7.29](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html) | [5.7.29](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html)|
|MySQL 버전 8.0 | [8.0.15](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-15.html) | [8.0.21](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-21.html)|

버전 [지원 정책 설명서](concepts-version-policy.md#retired-mysql-engine-versions-not-supported-in-azure-database-for-mysql) 에서 사용 중지 된 버전에 대 한 버전 지원 정책을 참조 하세요.

## <a name="major-version-support"></a>주 버전 지원
각 주 버전의 MySQL은 버전 [관리 정책](https://www.mysql.com/support/eol-notice.html)에 제공 된 대로 mysql 커뮤니티에서 버전을 사용 중지할 때까지 Azure에서 버전을 지원 하기 시작 하는 날짜의 Azure Database for MySQL에서 지원 됩니다.

## <a name="minor-version-support"></a>부 버전 지원
정기적 유지 관리의 일환으로 Azure 기본 설정 MySQL 버전으로 자동으로 부 버전 업그레이드를 수행 Azure Database for MySQL 합니다. 

## <a name="major-version-retirement-policy"></a>주 버전 사용 중지 정책
다음 표에서는 MySQL 주 버전에 대 한 사용 중지 정보를 제공 합니다. 날짜는 [MySQL 버전 관리 정책](https://www.mysql.com/support/eol-notice.html)을 따릅니다.

| Version | What's New | Azure 지원 시작 날짜 | 사용 중지 날짜|
| ----- | ----- | ------ | ----- |
| [MySQL 5.6](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/)| [기능](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-49.html)  | 2018년 3월 20일 | 2021년 2월
| [MySQL 5.7](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/) | [기능](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-31.html) | 2018년 3월 20일 | 10 월 2023
| [MySQL 8](https://mysqlserverteam.com/whats-new-in-mysql-8-0-generally-available/) | [기능](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-21.html)) | 2019년 12월 11일 | 4 월 2026


## <a name="retired-mysql-engine-versions-not-supported-in-azure-database-for-mysql"></a>사용 중지 된 MySQL 엔진 버전은 Azure Database for MySQL에서 지원 되지 않습니다.

각 MySQL 데이터베이스 버전에 대 한 사용 중지 날짜 이후에는 사용 중지 된 버전을 계속 실행 하는 경우 다음 제한 사항을 확인 하세요.
- 커뮤니티는 추가 버그 수정 또는 보안 Azure Database for MySQL 수정 사항을 릴리스할 수 없으므로 버그 또는 보안 문제에 대 한 사용 중지 된 데이터베이스 엔진을 패치 하거나 사용 중지 된 데이터베이스 엔진과 관련 하 여 보안 조치를 취합니다. 그러나 Azure는 호스트, OS, 컨테이너 및 기타 서비스 관련 구성 요소에 대해 정기적으로 유지 관리 및 패치를 계속 수행 합니다.
- 지원 문제가 발생할 수 있는 경우 MySQL 데이터베이스와 관련 하 여 지원을 제공 하지 못할 수 있습니다. 이러한 경우에는 지원을 제공 하기 위해 데이터베이스를 업그레이드 해야 합니다.
<!-- - You will not be able to create new database servers for the retired version. However, you will be able to perform point-in-time recoveries and create read replicas for your existing servers. -->
- Azure Database for MySQL에서 개발한 새 서비스 기능은 지원 되는 데이터베이스 서버 버전 에서만 사용할 수 있습니다.
- 작동 시간 Sla는 Azure Database for MySQL 서비스 관련 문제에만 적용 되며 데이터베이스 엔진 관련 버그로 인 한 가동 중지 시간에는 적용 되지 않습니다.  
- 사용 중지 된 데이터베이스 버전에서 식별 된 MySQL 데이터베이스 엔진 취약성으로 인해 발생 하는 서비스에 심각한 위협이 발생 하는 극단적인 경우 Azure는 먼저 서비스를 보호 하기 위해 데이터베이스 서버의 계산 노드를 중지 하도록 선택할 수 있습니다. 서버를 온라인 상태로 전환 하기 전에 서버를 업그레이드 하 라는 메시지가 표시 됩니다. 업그레이드 프로세스 중에는 원하는 경우 이전 버전으로 복원 하는 데 사용할 수 있는 서비스에서 수행 되는 자동 백업을 사용 하 여 데이터가 항상 보호 됩니다. 



## <a name="next-steps"></a>다음 단계
- Azure Database for MySQL-단일 서버 [지원 버전](./concepts-supported-versions.md) 을 참조 하세요.
- Azure Database for MySQL-유연한 서버 (미리 보기) [지원 버전](flexible-server/concepts-supported-versions.md) 을 참조 하세요.
- 업그레이드를 수행 하려면 MySQL [덤프 및 복원](./concepts-migrate-dump-restore.md) 을 참조 하세요.
