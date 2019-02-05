---
title: 빠른 시작 - Azure SQL Database의 단일 데이터베이스 | Microsoft Docs
description: Azure SQL Database의 단일 데이터베이스로 빠르게 시작하는 방법 알아보기
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 72ffdb357b5469b71da7655104add1135bf114fb
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55464819"
---
# <a name="getting-started-with-single-databases-in-azure-sql-database"></a>Azure SQL Database의 단일 데이터베이스로 빠르게 시작하기

Azure SQL Database의 [단일 데이터베이스](sql-database-single-index.yml)는 완전 관리형 PaaS DbaaS(database as a service)이며, 이것은 최신 클라우드 기반 애플리케이션에 이상적인 스토리지 엔진입니다. 이 섹션에서는 SQL Database를 신속하게 구성하고 만드는 방법을 알아봅니다.

## <a name="quickstart-overview"></a>빠른 시작 개요

이 섹션에서는 단일 데이터베이스를 신속하게 시작하는 데 사용할 수 있는 문서에 대한 개요를 알아봅니다. 첫 번째 SQL Database를 만드는 가장 쉬운 방법은 [Azure Portal](sql-database-get-started-portal.md)을 사용하는 것입니다. 여기에서 필요한 매개 변수를 구성할 수 있습니다.
만든 후에는 [방화벽 규칙을 구성하여 데이터베이스를 보호](sql-database-get-started-portal-firewall.md)해야 합니다. 

SQL Server에 Azure로 마이그레이션할 기존 데이터베이스가 있는 경우, [DMA(Data Migration Assistant)](https://www.microsoft.com/download/details.aspx?id=53595)를 설치해야 합니다. DMA는 SQL Server의 데이터베이스를 분석하여 단일 데이터베이스로 마이그레이션을 차단할 수 있는 문제를 찾습니다. 문제가 없으면 데이터베이스를 `.bacpac` 파일로 내보내서 [Azure Portal이나 SqlPackage를 사용하여 가져오면](sql-database-import.md) 됩니다.

이 빠른 시작을 사용하면 Azure 클라우드에서 데이터베이스를 신속하게 구성하고, 만들고, 가져올 수 있습니다.

## <a name="automating-management-operations"></a>관리 작업 자동화

Azure Portal을 통해 단일 데이터베이스를 손쉽게 만들고 수정할 수 있습니다. 아니면 [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md)이나 [Azure CLI](scripts/sql-database-create-and-configure-database-cli.md)를 사용하여 데이터베이스를 만들 수도 있습니다.
[PowerShell](scripts/sql-database-monitor-and-scale-database-powershell.md)이나 [Azure CLI](scripts/sql-database-monitor-and-scale-database-cli.md)를 사용하여 단일 데이터베이스를 업데이트하고 리소스 규모를 조정할 수도 있습니다.

## <a name="migrating-to-a-single-database-with-minimal-downtime"></a>가동 중지 시간을 최소화하면서 단일 데이터베이스 마이그레이션

이 빠른 시작의 문서를 사용하면 `.bacpac`을 사용하여 데이터베이스를 신속하게 만들어서 Azure로 가져올 수 있습니다. 하지만 `.bacpac` 및 `.dacpack` 파일은 다른 버전의 SQL Server 및 Azure SQL Database(단일 데이터베이스, 탄력적 풀 또는 Managed Instance)에서 데이터베이스를 신속하게 이동하거나 DevOps 파이프라인에서 지속적인 통합을 구현하도록 설계되었습니다. 단, 이 방법은 가동 중지 시간을 최소화하면서 프로덕션 데이터베이스 마이그레이션을 수행하도록 설계되지 않았습니다. 원본 데이터베이스를 `.bacpac` 파일로 내보내고 Azure SQL Database로 가져올 때가지 기다려야 하기 때문에 특히 데이터베이스가 클수록 애플리케이션의 가동 중지 시간이 길어질 수 있습니다. 프로덕션 데이터베이스를 이동하는 경우에는, 마이그레이션의 가동 중지 시간을 최소화할 수 있는 더 좋은 마이그레이션 방법이 필요합니다. [Data Migration Service](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-azure-sql?toc=/azure/sql-database/toc.json)는 최소 가동 중지 시간으로 데이터베이스를 마이그레이션할 수 있는 서비스입니다. 이 방식을 사용하면 가동 중지 시간을 최소화하면서 원본에서 대상 데이터베이스로 애플리케이션을 신속하게 전환할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Azure SQL Database에서 지원되는 기능의 개략적인 목록](sql-database-features.md)을 찾습니다. 
* [데이터베이스를 더 안전하게 보호](sql-database-security-tutorial.md)하는 방법을 알아봅니다. 
* [방법 섹션](sql-database-howto-single-database.md)에서 고급 자습서를 찾아봅니다. 
* [PowerShell](sql-database-powershell-samples.md) 및 [Azure CLI](sql-database-cli-samples.md)로 작성된 샘플 스크립트를 찾아봅니다. 
* 데이터베이스를 구성하는 데 사용할 수 있는 [Management API](sql-database-single-databases-manage.md)에 대해 자세히 알아봅니다. 
