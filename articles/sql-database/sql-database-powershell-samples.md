---
title: "SQL Database에 대한 Azure PowerShell 샘플 | Microsoft Docs"
description: "Azure PowerShell 샘플 - Azure SQL Database 서버, 탄력적 풀, 데이터베이스 및 방화벽을 만들고 관리하는 데 유용한 스크립트입니다."
services: sql-database
documentationcenter: sql-database
author: CarlRabeler
manager: jhubbard
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: sql-database
ms.custom: sample
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 03/07/2017
ms.author: janeng
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: abcccabbfc56102c97ef2408fb812755e21b467c
ms.lasthandoff: 03/22/2017

---

# <a name="azure-powershell-samples-for-azure-sql-database"></a>Azure SQL Database에 대한 Azure PowerShell 샘플

다음 표에는 Azure SQL Database의 Azure PowerShell 샘플 스크립트에 대한 링크가 나와 있습니다.

| |  |
|---|---|
|**단일 데이터베이스 및 탄력적 풀 만들기**||
| [단일 데이터베이스 만들기 및 방화벽 규칙 구성](scripts/sql-database-create-and-configure-database-powershell.md) | 단일 Azure SQL Database를 만들고 서버 수준 방화벽 규칙을 구성합니다. |
| [탄력적 풀 만들기 및 풀된 데이터베이스 이동](scripts/sql-database-move-database-between-pools-powershell.md) | 탄력적 풀을 만들고 풀된 데이터베이스를 이동한 후 성능 수준을 변경합니다.|
|**지역에서 복제 및 장애 조치(Failover) 구성**||
| [활성 지역 복제를 사용하여 단일 데이터베이스 구성 및 장애 조치(Failover)](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)| 단일 Azure SQL Database에 대해 활성 지역 복제를 구성하고 보조 복제본으로 장애 조치합니다. |
| [활성 지역 복제를 사용하여 풀된 데이터베이스 구성 및 장애 조치(Failover)](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)| 탄력적 풀에서 Azure SQL Database에 대해 활성 지역 복제를 구성하고 보조 복제본으로 장애 조치합니다. |
|**단일 데이터베이스 및 탄력적 풀 크기 조정**||
| [단일 데이터베이스 크기 조정](scripts/sql-database-monitor-and-scale-database-powershell.md) | Azure SQL Database의 성능 메트릭을 모니터링하고, 더 높은 성능 수준으로 확장하고, 성능 메트릭 중 하나에 경고 규칙을 만듭니다. |
| [탄력적 풀 크기 조정](scripts/sql-database-monitor-and-scale-pool-powershell.md) | 탄력적 풀의 성능 메트릭을 모니터링하고, 더 높은 성능 수준으로 확장하고, 성능 메트릭 중 하나에 경고 규칙을 만듭니다.  |
| **감사 및 위협 감지** |
| [감사 및 위협 감지 구성](scripts/sql-database-auditing-and-threat-detection-powershell.md)| Azure SQL Database에 대한 감사 및 위협 감지 정책을 구성합니다. |
| **데이터베이스 복원, 복사 및 가져오기**||
| [데이터베이스 복원](scripts/sql-database-restore-database-powershell.md)| 지역 중복 백업에서 Azure SQL Database를 복원하고 삭제된 Azure SQL Database를 최신 백업으로 복원합니다. |
| [새 서버에 데이터베이스 복사](scripts/sql-database-copy-database-to-new-server-powershell.md)| 새 Azure SQL Server에 기존 Azure SQL Database의 복사본을 만듭니다. |
| [bacpac 파일에서 데이터베이스 가져오기](scripts/sql-database-import-from-bacpac-powershell.md)| bacpac 파일에서 Azure SQL Server로 데이터베이스를 가져옵니다. |
|||

