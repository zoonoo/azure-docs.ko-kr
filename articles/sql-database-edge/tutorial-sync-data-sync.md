---
title: SQL 데이터 동기화를 사용하여 Azure SQL Database Edge에서 데이터 동기화 | Microsoft Docs
description: Azure SQL 데이터 동기화를 사용하여 Azure SQL Database Edge 간의 데이터를 동기화하는 방법 알아보기
keywords: sql database edge, sql database edge의 데이터 동기화, sql database edge 데이터 동기화
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: tutorial
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 857cee30ac4c1002fb7ca57d6be5fa461a14e9ee
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73509197"
---
# <a name="tutorial-sync-data-from-sql-database-edge-to-azure-sql-database-using-sql-data-sync"></a>자습서: SQL 데이터 동기화를 사용하여 SQL Database Edge에서 Azure SQL Database로 데이터 동기화

이 자습서에서는 SQL 데이터 동기화 *동기화 그룹*을 사용하여 Azure SQL Database Edge에서 Azure SQL Database로 데이터를 증분 방식으로 동기화하는 방법에 대해 설명합니다. SQL 데이터 동기화는 여러 SQL 데이터베이스 및 SQL Server 인스턴스 간에 양방향으로 선택한 데이터를 동기화할 수 있는 Azure SQL Database에 기반한 서비스입니다. Azure SQL 데이터 동기화에 대한 자세한 내용은 [Azure SQL 데이터 동기화](../sql-database/sql-database-sync-data.md)를 참조하세요.

Azure SQL Database Edge는 최신 버전의 [Microsoft SQL Server 데이터베이스 엔진](/sql/sql-server/sql-server-technical-documentation/)에서 빌드되므로 온-프레미스 SQL Server 인스턴스에 적용 가능한 모든 데이터 동기화 메커니즘을 사용하여 에지 디바이스에서 실행되는 SQL Database Edge 인스턴스와 데이터를 서로 동기화할 수도 있습니다.

## <a name="prerequisites"></a>필수 조건

이 자습서를 사용하려면 [Azure SQL 데이터 동기화 에이전트](../sql-database/sql-database-data-sync-agent.md)를 통해 구성된 Windows 컴퓨터가 필요합니다.

## <a name="before-you-begin"></a>시작하기 전에

* Azure SQL Database를 만듭니다. Azure Portal을 사용하여 Azure SQL Database를 만드는 방법에 대한 자세한 내용은 [Azure SQL Database의 단일 데이터베이스 만들기](../sql-database/sql-database-single-database-get-started.md?tabs=azure-portal)를 참조하세요.

* Azure SQL Database 배포에 테이블 및 기타 필요한 개체를 만듭니다.

* Azure SQL Database Edge 배포에 필요한 테이블 및 개체를 만듭니다. 자세한 정보는 [SQL Database Edge에서 SQL Database DAC 패키지 사용](stream-analytics.md)을 참조하세요.

* Azure SQL 데이터 동기화 에이전트로 Azure SQL Database Edge 인스턴스를 등록합니다. 자세한 내용은 [온-프레미스 SQL Server 데이터베이스 추가](../sql-database/sql-database-get-started-sql-data-sync.md#add-on-prem)를 참조하세요.

## <a name="sync-data-between-an-azure-sql-database-and-sql-database-edge"></a>Azure SQL Database 및 SQL Database Edge 간 데이터 동기화

SQL 데이터 동기화를 사용하여 Azure SQL Database와 SQL Database Edge 인스턴스 간의 동기화를 설정하려면 세 가지 주요 단계가 필요합니다.  

1. Azure Portal을 사용하여 동기화 그룹을 만듭니다. 동기화 그룹을 만들려면 [Azure Portal을 사용하여 동기화 그룹 만들기](../sql-database/sql-database-get-started-sql-data-sync.md#create-sync-group)를 참조하세요. 동일한 *허브* 데이터베이스를 사용하여 서로 다른 SQL Database Edge 인스턴스의 데이터를 Azure의 하나 이상의 SQL 데이터베이스로 동기화하기 위해 여러 동기화 그룹을 만들 수 있습니다.

2. 동기화 그룹에 동기화 구성원을 추가합니다. 동기화 그룹에 구성원을 추가하려면 [SQL 데이터 동기화 그룹에 구성원 추가](../sql-database/sql-database-get-started-sql-data-sync.md#add-sync-members)를 참조하세요.

3. 동기화 그룹을 구성하여 이 동기화의 일부가 될 테이블을 선택합니다. 동기화 그룹을 구성하려면 [동기화 그룹 구성](../sql-database/sql-database-get-started-sql-data-sync.md#add-sync-members)을 참조하세요.

위 단계를 완료하면 Azure SQL Database 및 SQL Database Edge 인스턴스를 포함하는 동기화 그룹이 만들어집니다.

SQL 데이터 동기화에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure SQL 데이터 동기화용 데이터 동기화 에이전트](../sql-database/sql-database-data-sync-agent.md)

* [모범 사례](../sql-database/sql-database-best-practices-data-sync.md) 및 [Azure SQL 데이터 동기화 관련 문제를 해결하는 방법](../sql-database/sql-database-troubleshoot-data-sync.md)

* [Azure Monitor 로그를 사용하여 SQL 데이터 동기화 모니터링](../sql-database/sql-database-sync-monitor-oms.md)

* [Transact-SQL](../sql-database/sql-database-update-sync-schema.md) 또는 [PowerShell](../sql-database/scripts/sql-database-sync-update-schema.md)을 사용하여 동기화 스키마 업데이트

## <a name="next-steps"></a>다음 단계

* [Powershell을 사용하여 Azure SQL Database와 Azure SQL Database Edge 간에 동기화합니다](../sql-database/scripts/sql-database-sync-data-between-azure-onprem.md). 자습서에서 *OnPremiseServer* 데이터베이스 세부 정보를 Azure SQL Database Edge 세부 정보로 바꿉니다.
