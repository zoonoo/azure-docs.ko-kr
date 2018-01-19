---
title: "MySQL용 Azure 데이터베이스의 제한 사항 | Microsoft Docs"
description: "MySQL용 Azure 데이터베이스의 미리 보기 제한 사항을 설명합니다."
services: mysql
author: jasonh
ms.author: kamathsun
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 01/11/2018
ms.openlocfilehash: f0f9a10f987f19d8ae77a07038cffe23446856fd
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/12/2018
---
# <a name="limitations-in-azure-database-for-mysql"></a>Azure Database for MySQL의 제한 사항
MySQL용 Azure 데이터베이스 서비스는 공개 미리 보기 상태입니다. 다음 섹션에서는 데이터베이스 서비스의 용량, 저장소 엔진 지원, 권한 지원, 데이터 조작 명령문 지원 및 기능 제한 사항에 대해 설명합니다. 또한 MySQL 데이터베이스 엔진에 적용할 수 있는 [일반적인 제한 사항](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.6/en/limits.html)도 참조하세요.

## <a name="service-tier-maximums"></a>서비스 계층 최대값
Azure Database for MySQL에는 서버를 만들 때 선택할 수 있는 여러 서비스 계층이 있습니다. 자세한 내용은 [각 서비스 계층에서 사용할 수 있는 기능 이해](concepts-service-tiers.md)를 참조하세요.  

다음과 같이 미리 보기 동안 각 서비스 계층에는 연결, Compute 단위 및 저장소의 최대 수가 지정되어 있습니다. 

|                            |                   |
| :------------------------- | :---------------- |
| **최대 연결**        |                   |
| 기본 50 Compute 단위     | 50개 연결    |
| 기본 100 Compute 단위    | 100개 연결   |
| 표준 100 Compute 단위 | 200개 연결   |
| 표준 200 Compute 단위 | 400개 연결   |
| 표준 400 Compute 단위 | 800개 연결   |
| 표준 800 Compute 단위 | 1600개 연결  |
| **최대 Compute 단위**      |                   |
| 기본 서비스 계층         | 100 Compute 단위 |
| 표준 서비스 계층      | 800 Compute 단위 |
| **최대 저장소**            |                   |
| 기본 서비스 계층         | 1TB              |
| 표준 서비스 계층      | 1TB              |

너무 많은 연결에 도달하면 다음 오류가 나타날 수 있습니다.
> 오류 1040(08004): 너무 많은 연결이 있습니다.

## <a name="storage-engine-support"></a>저장소 엔진 지원

### <a name="supported"></a>지원됨
- [InnoDB](https://dev.mysql.com/doc/refman/5.7/en/innodb-introduction.html)
- [MEMORY](https://dev.mysql.com/doc/refman/5.7/en/memory-storage-engine.html)

### <a name="unsupported"></a>지원되지 않음
- [MyISAM](https://dev.mysql.com/doc/refman/5.7/en/myisam-storage-engine.html)
- [BLACKHOLE](https://dev.mysql.com/doc/refman/5.7/en/blackhole-storage-engine.html)
- [ARCHIVE](https://dev.mysql.com/doc/refman/5.7/en/archive-storage-engine.html)
- [FEDERATED](https://dev.mysql.com/doc/refman/5.7/en/federated-storage-engine.html)

## <a name="privilege-support"></a>권한 지원

### <a name="unsupported"></a>지원되지 않음
- DBA 역할 - 많은 서버 매개 변수 및 설정으로 인해 실수로 서버 성능이 저하되거나 DBMS의 ACID 속성이 무효화될 수 있습니다. 따라서 제품 수준에서 서비스 무결성 및 SLA를 유지하기 위해 DBA 역할을 고객에게 공개하지 않습니다. 고객은 새 데이터베이스 인스턴스를 만들 때 생성되는 기본 사용자 계정을 통해 관리되는 데이터베이스 인스턴스에서 대부분의 DDL 및 DML 문을 수행할 수 있습니다. 
- SUPER 권한 - 마찬가지로 [SUPER 권한](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_super)도 제한됩니다.

## <a name="data-manipulation-statement-support"></a>데이터 조작 명령문 지원

### <a name="supported"></a>지원됨
- LOAD DATA INFILE - 지원되지만, UNC 경로(XSMB를 통해 탑재된 Azure 저장소)로 전달되는 [LOCAL] 매개 변수를 지정해야 합니다.

### <a name="unsupported"></a>지원되지 않음
- SELECT ... INTO OUTFILE

## <a name="preview-functional-limitations"></a>미리 보기 기능 제한 사항

### <a name="scale-operations"></a>크기 조정 작업
- 서비스 계층 간 서버의 동적 크기 조정은 현재 지원되지 않습니다. 즉, 기본 및 표준 서비스 계층 간 전환은 가능하지 않습니다.
- 미리 생성된 서버에서 필요 시 저장소의 동적 증가는 현재 지원되지 않습니다.
- 서버 저장소 크기를 줄이는 것은 지원되지 않습니다.

### <a name="server-version-upgrades"></a>서버 버전 업그레이드
- 주 데이터베이스 엔진 버전 간에 자동화된 마이그레이션은 현재 지원되지 않습니다.

### <a name="point-in-time-restore"></a>특정 시점 복원
- 다른 서비스 계층 및/또는 Compute 단위 및 저장소 크기로 복원할 수 없습니다.
- 삭제된 서버 복원은 지원되지 않습니다.

## <a name="functional-limitations"></a>기능 제한 사항

### <a name="subscription-management"></a>구독 관리
- 구독 및 리소스 그룹에서 미리 생성된 서버를 동적으로 이동하는 것은 현재 지원되지 않습니다.

## <a name="current-known-issues"></a>현재 알려진 문제
- 연결이 설정된 후에 MySQL 서버 인스턴스에서 잘못된 서버 버전을 표시합니다. 올바른 서버 인스턴스 버전 관리를 가져오려면 MySQL 프롬프트에서 select version(); 명령을 입력합니다.

## <a name="next-steps"></a>다음 단계
- [각 서비스 계층에서 사용할 수 있는 기능](concepts-service-tiers.md)
- [지원되는 MySQL 데이터베이스 버전](concepts-supported-versions.md)
