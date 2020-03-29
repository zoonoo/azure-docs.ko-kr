---
title: Azure SQL Database에 데이터 이동 - Team Data Science Process
description: 플랫 파일(CSV 또는 TSV 형식)이나 온-프레미스 SQL Server에 저장된 데이터에서 Azure SQL 데이터베이스로 데이터를 이동합니다.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: f9a1424f2afe6c5153e208601b21dff9651880a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76722461"
---
# <a name="move-data-to-an-azure-sql-database-for-azure-machine-learning"></a>Azure Machine Learning을 위해 Azure SQL Database로 데이터 이동

이 문서에서는 플랫 파일(CSV 또는 TSV 형식)에서 온-프레미스 SQL Server에 저장된 데이터에서 Azure SQL Database로 데이터를 이동하는 옵션에 대해 간략하게 설명합니다. 클라우드로 데이터를 이동하는 이러한 작업은 팀 데이터 과학 프로세스의 일부입니다.

Machine Learning을 위해 온-프레미스 SQL Server로 데이터를 이동하기 위한 옵션을 간략히 설명하는 토픽은 [Azure Virtual Machine의 SQL Server로 데이터 이동](move-sql-server-virtual-machine.md)을 참조하세요.

다음 표에서는 Azure SQL Database로 데이터를 이동하는 옵션을 요약합니다.

| <b>소스</b> | <b>대상: Azure SQL Database</b> |
| --- | --- |
| <b>플랫 파일(CSV 또는 TSV 형식)</b> |[대량 삽입 SQL 쿼리](#bulk-insert-sql-query) |
| <b>온-프레미스 SQL 서버</b> |1.[플랫 파일로 내보내기](#export-flat-file)<br> 2. [SQL 데이터베이스 마이그레이션 마법사](#insert-tables-bcp)<br> 3. [데이터베이스 백업 및 복원](#db-migration)<br> 4. [Azure 데이터 팩토리](#adf) |

## <a name="prerequisites"></a><a name="prereqs"></a>사전 요구 사항
여기에 설명된 절차를 위해서는 다음이 필요합니다.

* **Azure 구독**. 구독이 없는 경우 [무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 등록할 수 있습니다.
* **Azure 저장소 계정**. 이 자습서에서는 데이터 저장을 위해 Azure Storage 계정을 사용합니다. Azure Storage 계정이 없는 경우 [스토리지 계정 만들기](../../storage/common/storage-account-create.md) 문서를 참조하세요. 스토리지 계정을 만든 후에는 스토리지 액세스에 사용되는 계정 키를 확보해야 합니다. [저장소 계정 액세스 키 관리를](../../storage/common/storage-account-keys-manage.md)참조하십시오.
* **Azure SQL Database**에 대한 액세스. Azure SQL Database를 설정해야 하는 경우, [Microsoft Azure SQL Database 시작](../../sql-database/sql-database-get-started.md)에서 Azure SQL Database의 새 인스턴스를 프로비전하는 방법에 대한 정보를 제공합니다.
* 로컬로 설치 및 구성된 **Azure PowerShell** . 자세한 내용은 [Azure PowerShell 설치 및 구성법](/powershell/azure/overview)을 참조하세요.

**데이터**: [NYC Taxi 데이터 세트](https://chriswhong.com/open-data/foil_nyc_taxi/)를 사용하여 마이그레이션 프로세스를 시연합니다. NYC Taxi 데이터 세트는 여정 데이터 및 요금에 대한 정보를 포함하며 Azure Blob Storage [NYC Taxi 데이터](https://www.andresmh.com/nyctaxitrips/)에서 제공됩니다. 이러한 파일의 샘플 및 설명은 [NYC Taxi Trips 데이터 세트 설명](sql-walkthrough.md#dataset)에 제공됩니다.

자신의 데이터 세트에 여기에 설명된 절차를 도입하거나 NYC Taxi 데이터 세트를 사용하여 설명된 대로 단계를 따릅니다. NYC Taxi 데이터 세트를 온-프레미스 SQL Server 데이터베이스에 업로드하려면 [SQL Server Database로 대량 데이터 가져오기](sql-walkthrough.md#dbload)에 설명된 절차를 따릅니다. 이러한 지침은 Azure Virtual Machine의 SQL Server에 대한 내용이지만 온-프레미스 SQL Server로 업로드하는 절차는 동일합니다.

## <a name="moving-data-from-a-flat-file-source-to-an-azure-sql-database"></a><a name="file-to-azure-sql-database"></a>플랫 파일 원본에서 Azure SQL 데이터베이스로 데이터 이동
플랫 파일의 데이터(CSV 또는 TSV 서식이 지정)는 대량 삽입 SQL 쿼리를 사용하여 Azure SQL 데이터베이스로 이동할 수 있습니다.

### <a name="bulk-insert-sql-query"></a><a name="bulk-insert-sql-query"></a>대량 삽입 SQL 쿼리
대량 삽입 SQL 쿼리를 사용하는 프로시저의 단계는 Azure VM의 플랫 파일 원본에서 SQL Server로 데이터를 이동하는 방향과 유사합니다. 자세한 내용은 [대량 삽입 SQL 쿼리](move-sql-server-virtual-machine.md#insert-tables-bulkquery)를 참조하세요.

## <a name="moving-data-from-on-premises-sql-server-to-an-azure-sql-database"></a><a name="sql-on-prem-to-sazure-sql-database"></a>온-프레미스 SQL 서버에서 Azure SQL 데이터베이스로 데이터 이동
원본 데이터가 온-프레미스 SQL Server에 저장되는 경우 Azure SQL Database로 데이터를 이동할 수 있는 다양한 가능성이 있습니다.

1. [플랫 파일로 내보내기](#export-flat-file)
2. [SQL 데이터베이스 마이그레이션 마법사](#insert-tables-bcp)
3. [데이터베이스 백업 및 복원](#db-migration)
4. [Azure 데이터 팩터리](#adf)

처음 세 단계는 이러한 동일한 절차를 다루는 [Azure 가상 컴퓨터에서 데이터 이동에서 SQL Server로](move-sql-server-virtual-machine.md) 이동하는 섹션과 유사합니다. 이 항목의 해당 섹션에 대한 링크가 다음 절차에 제공됩니다.

### <a name="export-to-flat-file"></a><a name="export-flat-file"></a>플랫 파일로 내보내기
플랫 파일로 내보내기 단계는 [플랫 파일로 내보내기에서](move-sql-server-virtual-machine.md#export-flat-file)다루는 방향과 유사합니다.

### <a name="sql-database-migration-wizard"></a><a name="insert-tables-bcp"></a>SQL Database 마이그레이션 마법사
SQL 데이터베이스 마이그레이션 마법사를 사용하는 단계는 SQL [데이터베이스 마이그레이션 마법사에서](move-sql-server-virtual-machine.md#sql-migration)다루는 방향과 유사합니다.

### <a name="database-back-up-and-restore"></a><a name="db-migration"></a>데이터베이스 백업 및 복원
데이터베이스 백업 및 복원을 사용하는 단계는 데이터베이스 백업 및 복원에 나열된 [지침과 유사합니다.](move-sql-server-virtual-machine.md#sql-backup)

### <a name="azure-data-factory"></a><a name="adf"></a>Azure 데이터 팩터리
이 항목에서 Azure 데이터 팩터리(ADF)가 있는 Azure SQL 데이터베이스로 데이터를 이동하고, [온-프레미스 SQL 서버에서 Azure 데이터 팩터리를 사용하여 SQL Azure로 데이터를 이동하는](move-sql-azure-adf.md)방법에 대해 알아봅니다. 이 항목에서는 ADF를 사용하여 온-프레미스 SQL Server 데이터베이스에서 Azure Blob 저장소를 통해 Azure SQL 데이터베이스로 데이터를 이동하는 방법을 보여 주었습니다.

하이브리드 온-프레미스 및 클라우드 소스를 사용하여 데이터를 지속적으로 마이그레이션해야 하는 경우 ADF를 사용하는 것이 좋습니다.  또한 ADF는 데이터에 변환이 필요하거나 마이그레이션 중에 새 비즈니스 논리가 필요한 경우에도 도움이 됩니다. ADF에서는 정기적으로 데이터 이동을 관리하는 간단한 JSON 스크립트를 사용하여 작업 예약 및 모니터링이 가능합니다. 또한 복잡한 작업을 지원하는 기타 기능도 포함하고 있습니다.
