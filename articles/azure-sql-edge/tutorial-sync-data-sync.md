---
title: SQL 데이터 동기화를 사용하여 Azure SQL Edge에서 데이터 동기화
description: Azure SQL 데이터 동기화를 사용하여 Azure SQL Edge에서 데이터를 동기화하는 방법 알아보기
keywords: SQL Edge, SQL Edge에서 데이터 동기화, SQL Edge 데이터 동기화
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 5659ce5fa6f99463f58a33662563d768248fd8cb
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93394903"
---
# <a name="tutorial-sync-data-from-sql-edge-to-azure-sql-database-by-using-sql-data-sync"></a>자습서: SQL 데이터 동기화를 사용하여 SQL Edge에서 Azure SQL Database로 데이터 동기화

이 자습서에서는 Azure SQL 데이터 동기화 *동기화 그룹* 을 사용하여 Azure SQL Edge에서 Azure SQL Database로 데이터를 증분 방식으로 동기화하는 방법에 대해 설명합니다. SQL 데이터 동기화는 Azure SQL Database의 여러 데이터베이스 및 SQL Server 인스턴스 간에 양방향으로 선택한 데이터를 동기화할 수 있는 Azure SQL Database에 기반한 서비스입니다. SQL 데이터 동기화에 대한 자세한 내용은 [Azure SQL 데이터 동기화](../azure-sql/database/sql-data-sync-data-sql-server-sql-database.md)를 참조하세요.

SQL Edge는 최신 버전의 [SQL Server 데이터베이스 엔진](/sql/sql-server/sql-server-technical-documentation/)에서 빌드되므로 SQL Server 인스턴스에 적용 가능한 모든 데이터 동기화 메커니즘을 사용하여 에지 디바이스에서 실행되는 SQL Edge 인스턴스와 데이터를 서로 동기화할 수도 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

이 자습서를 사용하려면 [Azure SQL 데이터 동기화용 데이터 동기화 에이전트](../azure-sql/database/sql-data-sync-agent-overview.md)를 통해 구성된 Windows 컴퓨터가 필요합니다.

## <a name="before-you-begin"></a>시작하기 전에

* Azure SQL Database에서 데이터베이스를 만듭니다. Azure Portal을 사용하여 데이터베이스를 만드는 방법에 대한 자세한 내용은 [Azure SQL Database의 단일 데이터베이스 만들기](../azure-sql/database/single-database-create-quickstart.md?tabs=azure-portal)를 참조하세요.

* Azure SQL Database 배포에 테이블 및 기타 필요한 개체를 만듭니다.

* Azure SQL Edge 배포에 필요한 테이블 및 개체를 만듭니다. 자세한 내용은 [SQL Edge에서 SQL Database DAC 패키지 사용](deploy-dacpac.md)을 참조하세요.

* Azure SQL Edge 인스턴스를 Azure SQL 데이터 동기화용 데이터 동기화 에이전트에 등록합니다. 자세한 내용은 [SQL Server 데이터베이스 추가](../azure-sql/database/sql-data-sync-sql-server-configure.md#add-on-prem)를 참조하세요.

## <a name="sync-data-between-a-database-in-azure-sql-database-and-sql-edge"></a>Azure SQL Database의 데이터베이스와 SQL Edge 간에 데이터 동기화

SQL 데이터 동기화를 사용하여 Azure SQL Database의 데이터베이스와 SQL Edge 인스턴스 간에 동기화를 설정하려면 다음과 같은 세 가지 주요 단계가 필요합니다.  


1. Azure Portal을 사용하여 동기화 그룹을 만듭니다. 자세한 내용은 [동기화 그룹 만들기](../azure-sql/database/sql-data-sync-sql-server-configure.md#create-sync-group)를 참조하세요. 단일 *허브* 데이터베이스를 사용하여 다양한 SQL Edge 인스턴스의 데이터를 Azure SQL Database에 있는 하나 이상의 데이터베이스와 동기화하는 여러 동기화 그룹을 만들 수 있습니다. 

2. 동기화 그룹에 동기화 구성원을 추가합니다. 자세한 내용은 [동기화 구성원 추가](../azure-sql/database/sql-data-sync-sql-server-configure.md#add-sync-members)를 참조하세요.

3. 동기화 그룹을 설정하여 동기화에 포함될 테이블을 선택합니다. 자세한 내용은 [동기화 그룹 구성](../azure-sql/database/sql-data-sync-sql-server-configure.md#add-sync-members)을 참조하세요.

위의 단계를 완료하면 Azure SQL Database의 데이터베이스와 SQL Edge 인스턴스를 포함하는 동기화 그룹이 만들어집니다.

SQL 데이터 동기화에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure SQL 데이터 동기화용 데이터 동기화 에이전트](../azure-sql/database/sql-data-sync-agent-overview.md)

* [모범 사례](../azure-sql/database/sql-data-sync-best-practices.md) 및 [Azure SQL 데이터 동기화 관련 문제를 해결하는 방법](../azure-sql/database/sql-data-sync-troubleshoot.md)

* [Azure Monitor 로그를 사용하여 SQL 데이터 동기화 모니터링](../azure-sql/database/monitor-tune-overview.md)

* [Transact-SQL](../azure-sql/database/sql-data-sync-update-sync-schema.md) 또는 [PowerShell](../azure-sql/database/scripts/update-sync-schema-in-sync-group.md)을 사용하여 동기화 스키마 업데이트

## <a name="next-steps"></a>다음 단계


* [PowerShell을 사용하여 Azure SQL Database와 Azure SQL Edge 간에 동기화](../azure-sql/database/scripts/sql-data-sync-sync-data-between-azure-onprem.md)합니다. 이 자습서에서는 `OnPremiseServer` 데이터베이스 세부 정보를 Azure SQL Edge 세부 정보로 바꿉니다.